## Laravel 8

routes/channel.php **//is for the broadcasting**
routes/console.php **//is for the artisan custom commands**

> env('key', 'default value'); **// is used to get the env variable in the project**

> @csrf **// following directive must be mentioned in the form otherwise the 419 error will thrown**

> <input type="text" value="{{ old('name') }}" **// helper method is used in the html form with input to persist the value of the input after form submit**

###### **Facade** is like the abstraction or front for the series of functionality

```php
// make user log in
auth()->attempt([
    'email' => $request->email,
    'password' => $request->password,
]);
// OR
auth()->attempt($request->only('email', 'password'));
```

```html
@auth 
**//directive in the blade is used to check if user is logged in**
@endauth
```
```html
@guest
**//directive in the blade is used to check if user is not logged in**
@endguest
```
### Middleware
**Http/Middleware/*** is the directory for the middleware's. **Kernal.php** is the file where all the global middleware run over every request. in the Kernal.php middleware are divided into the groups **web** and **api**. web for the web routes and api are the middlewares for the apis.

```php

// chain route with middleware() method to apply middleware on it.
Route::get("/dashboard", [DashboardController::class, 'index'])
->name('dashboard')
->middleware('auth);
```

```php

// Or to apply the middleware just call it in the constructor of the controller
public function __construct() {
    $this->middleware('auth');
}
```

##### Collection
is the wrapper for the list of items. It is same as the Object or Array. 
* Model methods return collection for records/rows.
* Date are the object of the Carbon library in the laravel

##### Relationships
```php
class Post extends Model {

    // One to One ==> One Post belongsTo One User
    public function user() {
        return $this->belongsTo(User::class);
    }

    // One to Many ==> One Post have many Likes
    public function likes() {
        return $this->hasMany(Like::class);
    }
}
```

#### Dynamic route model binding

```php
/////////////////////////////// ROUTES /////////////////
// Route {post} Model binding. 
Route::post('/posts/{post}', [PostController::class, 'store'])->name('posts');

// Route {post:title} field based Model binding. http://localhost/posts/new-article ==> Post with title "new-article" will be bind
Route::post('/posts/{post:id}', [PostController::class, 'store'])->name('posts');
//////////////////////////////////////////////////////

///////////////////// CONTROLLER /////////////////////
class PostController extends Controller {

    // The Post Model object will auto inject here
    public function store(Post $post, Request $request) {
        dd($post);
    }
}
//////////////////////////////////////////////////////
```

##### Relationship based CRUD

```php
// create new post for the User
$user->posts()->create([
    'title' => 'any title',
    'body' => 'body content
]);
```

##### Blade component

>$ php artisan make:component Post
**Note** will create component for post

```html
<!-- post component with $post props -->
<x-post :post="$post" />
```