# Laravel sanctum based API
Sanctum is the laravel token based authentication API.

>$ composer create-project laravel/laravel laravel-sanctum-api

**Note** create new project

>$ php artisan serve

**Note** Start the laravel server

>$ composer require laravel/sanctum

**Note** install the sanctum via composer

>$ php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"

**Note** create sanctum migration and configuration files. Check the laravel documentation for the sanctum installation.

>$ php artisan migrate

**Note** run migration to create tables for token

```php
class Product extends Model {

    // custom table name for the products
    protected $table = 'products';

    // following fields must be provided during products creation
    protected $fillable = ['name', 'slug', 'price'];
    
    // must not return in the response
    protected $hidden = ['password', 'token'];

    // cast or keys mapping before sending in the response
    protected $casts = [
        'date' => "created_at",
        "data" => "body"
    ];
}
```

>$ php artisan make:controller ProductsController --api

**Note** --api will create the API related resources only like (store, update, index, destroy only).

>$ php artisan make:model Product --migration

**Note** --migration will create the migration file as well for the model.

>$ php artisan migration

**Note** run migration files

```php
// auto map all the request methods with the controller methods 
Route::resource('products', ProductController::class);

// get route map with the controller
Route::get('/products/search/{name}', [ProductController::class, 'search']);

// get route
Route::get('/products', [ProductController::class, 'index']);

// post route
Route::post('products', [ProductController::class, 'store']);

// group routes and apply auth middleware on the routes. to protect routes.
Route::group(['middleware' => ['auth:sanctum']], function () {
    Route::resource('products', ProductController::class);
});
```

##### Controller

```php
class AuthCtl extends Controller {

    public function register(Request $req) {

        // validate request
        $fields = $req->validation([
            'name' => 'required|string',
            'email' => 'required|string|unique:users,email', //email must be unique in the email column of the users table.
            'password' => 'required|string|confirmed' // password and password_confirmation fields must be same
        ]);

        // create user 
        $user = User::create([
            'name' => $fields['name'],
            'email' => $fields['email'],
            'password' => bcrypt($fields['password']) // encrypt password
        ]);

        // to create plain token for the user, need to be store in the COOKIE or localStorage on the front-end 
        // and is used to access the protected routes.
        // will be used as a Bearer Token in the API calling
        $token = $user->createToken('myapptoken')->plainTextToken;

        $response = [
            'user' => $user,
            'token' => $token
        ];

        //  response with response code
        return response($response, 200);
    }

    public function login(Request $req) {
        // validate request
        $fields = $req->validation([
            'email' => 'required|string',
            'password' => 'required|string'
        ]);

        $user = User::where('email', $fields['email'])->first();

        //check password
        if(!$user || !Hash::check($field['password'], $user->password)) {
            return response(['not_found'], 401);
        }

        $token = $user->createToken('myapptoken')->plainTextToken;

        $response = [
            'user' => $user,
            'token' => $token
        ];
        return response($response, 200);
    }

    public function logout(Request $req) {
        // delete the token at logout
        auth()->user()->token()->delete();
    }
}
```

##### Resource class
Resource class are like the DTO or custom response used to modify the response before serving.

>$ php artisan make:resource Article

**Note** to create custom DTO or custom api response in Http/Resources/Article.php

```php
// Http/Resources/Article.php

public function toArray($request) {
    return [
        'id' => $this->id,
        'title' => $this->title,
        'date_created' => date(Y-m-s H:i:s, $this->timestamp),
        'body' => $this->body
    ];
}

// following method is used to append/add extra data/meta to the response objects
public function with($request) {
    return [
        'version' => '1.0.0',
        'author_url' => url('https://atif.com')
    ];
}
```