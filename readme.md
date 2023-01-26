### Laravel docs/cheatsheet

Laravel is Open Source and most popular PHP framework. It aims to make dev work pleasing. Laravel use MVC pattern. The current repository is having the Laravel Blog Application.

### What does laravel do?
* Routing
* Security layer
* Models & DB migrations
* View/Templates
* Authentication
* Sessions
* Compile Assets
* Storage & File management
* Error Handling
* Unit and Integration testing
* Email & config
* Cache Handling
* and many more...

### Artisan CLI
It is the CLI tool for the Laravel. It can do the following things.
* Create controllers and models
* Create database migrations and running migrations
* Create providers, events, jobs, form requests, etc
* It can show routes
* Session commands
* Run Tinker (laravel repl)
* Create custom artisan commands 
* and many more...

### Example of Artisan commands
* php artisan list **//it show all the avialable commands**
* php artisan help migrate **// shows help regarding migrate or any command**
* php artisan make:controller ControllerName **// create controller, controller should be plural**
* php artisan make:model ModelName -m **//create model along with migration, model should be singular**
* php artisan make:migration add_todos_to_db -table=todos **//create migration with custom table**
* php artisan migrate **//run migration**
* php artisan tinker **//run tinker repl**
* and many more...
* [Artisan official reference](https://laravel.com/docs/5.4/artisan)

### Eloquent ORM
It is an ORM, used to interact with database. We can use raw SQL queries as well if needed.
[Eloquent ORM reference](https://laravel.com/docs/5.4/eloquent)

```php
User App\Todo; //include todo Model
$todo = new Todo(); //create object of Model
$todo->title = "any title"; //set title for the todos
$todo->save(); //save into database using ORM
```

### Blade Template Engine
It is the template engine use by the laravel. It's simple and powerful. We can also use raw php in the blade template files. We can create custom components as well in the blade template. Template inheritance/partial is also very easy with the blade template. It has control structures like if, else, loops, etc.
[Laravel Blade template reference](https://laravel.com/docs/5.4/blade)

### Laravel installation
If your application is running inside the Docker then first install the PHP Cli in the host operating system to interact with the composer and laravel artisan from host operating system.
>$ sudo apt install php7.4-cli

In order to install laravel first install the composer.
##### create new laravel project via composer
>$ composer create-project laravel/laravel app **//to install the latest version**

>$ composer create-project --prefer-dist laravel/laravel blog "5.4.*" **//to install the specific version**

The laravel app can be run the following command.
>$ php artisan serve

OR
Install apache2 and open the laravel project URL/public/ in the browser. Or create the vhost and add domain to /etc/hosts file. 
> https://laravel-project-url/public/

```txt
<VirtualHost *:80>
    ServerName laravel-app.com
    DocumentRoot /var/www/html/laravel_app/app/public
    
    # add the following otherwise the URL rewriting will not work.
    <Directory "/var/www/html/laravel_app/app/public">
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>

#Add following domain to /etc/hosts file
127.0.0.1 laravel-app.com
```
### Basic Routing
**app/routes** is the directory where we can define the web or api or channels routes.

Following are the laravel routes methods.

```php
Route::get('path', callback);
Route::post('path', callback);
Route::put('path', callback);
Route::delete('path', callback);
Route::resource('path', ControllerName); // it will auto map all the Request methods with resource methods of the controller
```

### Views/Templates
**app/resources** is the directory containing the blade view. default extension for the blade template is **file.blade.php** 

Following is used to load the view in the routes from callback functions or controller methods.

```php
Route::get("/about", function(){
    return view('file');
    // return view('dir/file');
    // return view('dir.file'); //via . syntax
});
```

##### Dynamic route or route params

```php
Route::get("/user{id}/{name}", function($id){
    return $id . ' ' . $name;
});

// http://laravel-app/user/10/atif
```

### Controller
We have to use the controller with the routes to load view. **app/Http/Controller** is the controllers directory.

>$ php artisan make:controller PagesController **//Controller must be plural** 

```php
// to load the index method of the PagesController
Route::get("/", 'PagesController@index');
```

### .env file
this is the file where we set the application secrets.

Following is used in the blade templates to load the env/config from the .env file into blade template.

```html
<span>{{config('app.name', 'default value')}}</span>
```

### Blade templating and assets compiling
**app/public/css, app/public/js** are the folders for the compiled assets. Following will be included or will be loaded in the layout file.

**app/resources/** is the folder for the layouts and views.

**FYI:** *'Laravel Blade Snippets'* is the must-have vscode extension for blade snippets and code completion.

##### Basic blade template layout example. 
layout file is the file which will be extended into the view files and view files will yield content into it. The main purpose of the layout is to avoid the content repetition.

###### layout file example.
```html
<!-- FILENAME: layouts/default.blade.php -->

<!doctype html>
<html>
<head>
    <!-- assets() function is used to load the assets from app/public/css directory -->
    <link rel="stylesheet" type="text/css" href="{{assets('css/app.css')}}">

    <!-- config method is used to load the env into the view files -->
   <title>{{config('app.name', 'default value')}}</title>
</head>
<body>
<div class="container">
   <header class="row">
    <!-- following directive is used to include the partials like includes/header.blade.php -->
       @include('includes.header')
   </header>
   <div id="main" class="row">
    <!-- @yield is used to include the content from the view files. -->
           @yield('content')
   </div>
   <footer class="row">
       <span>copyright: 2023</span>
   </footer>
</div>
</body>
</html>
```

###### View file example.
```html
<!-- FILENAME: main.blade.php -->

<!-- @extends is used to extend the layout file (layouts/default.blade.php) -->
@extends('layouts.default')

<!-- Following section will be @yield in the mail layout file -->
@section('content')
   <p>following is the content of the page.</p>
@endsection
```
#### Passing values to the view/template.
```php
Route::get("/about", function(){
    $title = "Laravel application"; // can be single value or array.
    // return view('file', compact("title")); // Or the following
    return view('file')->with('title', $title);
});
```

#### Rendering the passed values in the view/template.
```html
<h1>{{$title}}</h1>
<strong>OR</strong>
<h1><?php echo $title; ?></h1>
```

#### Blade template condition and loop.
```html
@section('content')
   @if(count($data) > 0)
        @foreach($data as $value)
            <p>{{$value}}</p>
        @endforeach
   @endif
@endsection
```
---
#### Assets compilation.
**app/resource/assets** is the assets directories. Every Laravel project is having the package.json file with dependencies like laravel-mix which used to compile and minify the assets from **app/resource/assets** into **app/public/css, app/public/js** folders which will later be included in the main layout file.

***Note:-*** We must not change the content of the assets in the public folder, rather we will make changes or add new css files in the resource/assets folder and then compile the assets via npm.

###### In order to compile the assets we have to install the npm and run the following commands.
>$ npm install **//in the project directory**

>$ npm run dev **//to re-compile the assets whenever we make changes into the resource/assets**

##### Or
>$ npm run watch **//will constantly watching for the assets changes and re-compile automatically**
---
##### Laravel life cycle
* All the request in the laravel will first land on the the public/index.php
* public/index.php execute composer autoload and load all the classes from composer vendor
* After the composer autoload all the service providers will register and after the registration th boot method of the service providers will execute.
* Request will go through the global middleware's to intercept the request
* Request will be handled to routes to controller to model and view.
* Response will be generated and will be given to middleware to filter the response.
* finally the response will be serve to the user via public/index.php file.

###### Controller
Controller is used to get request from route and data from database via model and load view or json as a response to user.
>$ php artisan make:controller PostsController

**Note** it is the standard that the controller name should be plural.

```php
class PostsController extends Controller {
    public function index() {
        $posts = Post::all(); // get all post via Model. it will return the collection
        $posts = Post::orderBy('title', 'desc')->get();
        $posts = Post::where('title', 'my post')->get(); // where clause
        $posts = Post::orderBy('title', 'desc')->take(1)->get(); // get single record

        $post = Post::find($id); // get single record
        $post->title = "change title";
        $post->save(); // update the record
        $post->delete(); //delete the record

        $post->update($request->all()); //update the record with new data from $request

        $posts = Post::orderBy('title', 'desc')->paginate(5); // get 5 records per page with pagination

        // helper method to return the logged in user object
        auth()->user()
        return view('posts.index')->with('posts', $posts); // load the view file from posts/index.blade.php
    }
}
```

```html
<!-- To display the pagination links -->
{{$posts->links()}}
```

###### Controller Resource Methods
**index()** => list of records
**show()** => single record
**create()** => html create form
**store()** => html create form submit
**edit()** => html edit form
**update()** => html edit form submit
**destroy()** => deletion
>$ php artisan make:controller PostsController --resource

**Note**: following will create the controller along with the resource methods in it.

>$ php artisan route:list

**Note** show all routes along with it's name and method/resource

###### Model (Eloquent ORM)
Laravel model is the class which is used to get the data from the database via Eloquent ORM.
>$ php artisan make:model Post -m

**Note** it is the standard that the model name should be singular. **-m** mean also create the migration file for the model.

```php
//NOTE:- 
//Model is the base class, it already having all the methods which are useful for the interaction with the database.
//If the Model name is Post then the table table will be posts in the database.

class Post extends Model {
    protected $table = "table_custom_name"; // to give custom table name for the model.
    protected $primaryKey = "custom_field_for_pk"; // to give custom pk field for the table.
    public $timestamps = true; // to set created_at and updated_at fields for the table.
}
```
###### DB Library
DB library can be used to write the raw queries. It should be used in case of complex queries.


```php
class PostsController extends Controller {
    public function index() {
        $posts = DB::select("SELECT * from posts"); // get all posts via db library and pass to view with with() method
        return view("posts")->with('posts', $posts);
    }
}
```

###### Migrations
Migrations are the schema files. Which is used to create the tables of the project. 

```php
class CreatePostsTable extends Migration {
    public function up() {
        // create table schema or alter the table schema
    }

    public function down() {
        // drop the table of drop the table field
    }
}
```
>$ php artisan migrate 

**Note** following command will execute the up() method of the migrations.

>$ php artisan migrate:rollback

**Note** following method will execute the down() method of the migrations. migrations are being tracked in the `migration` table in the database.

###### Tinker
>$ php artisan tinker

**Note** tinker is php Repl used to interact with laravel application via terminal.

```bash
App\Post::count(); //count all posts
$post = new App\Post();
$post->title = 'my article';
$post->body = 'content of the post';
$post->save(); //save the post via tinker in the table

quit
```
###### Controller level validation
it is used to validate the request params and body in the resource methods.

```php
public function store(Request $req) {
    $this->validate($req), [
        'title' => 'required',
        'body' => 'required',
        'email' => 'required|unique',
    ];

    // to get input value from request
    $name = $req->input("name");

    // redirect with flash message.
    return redirect("/post")->with("success", "completed");
}
```

```html
<!-- $errors will be populated if the validation failed. -->
@if(count($errors) > 0)
    @foreach($errors->all() as $err)
        <li>{{$err}}</li>
    @endforeach
@endif

<!-- To access the session value or flash message value. -->
@if(session("success"))
        <li>session("success")</li>
@endif
```

>$ composer require unisharp/laravel-ckeditor

**Note** to install package via composer. Alway add package service provider in the config/app.php file

##### Enable authentication in the Laravel
>$ php artisan make:auth

**Note** it will enable the auth controller, model, migrations, middleware's, layouts with views for registration, login, forget password and logout.

##### Useful blade helpers
```html
<!-- url() helper method is used for url based navigation -->
<a href="{{ url('/') }}"> visit </a>

<!-- url() helper method is used for route name based navigation -->
<a href="{{ route('route_name') }}"> visit </a>

@if(Auth::guest())
    <!-- if user is not logged in -->
@endif

@if(Auth::user())
    <!-- if user is logged in -->
    Auth::user()->name <!-- return logged in user object -->
@endif

<!-- for csrf token in the form -->
{{ csrf_field() }}
<!-- OR -->
@csrf
```

##### Migrations
Migration file name must be descriptive.

>$ php artisan make:migration create_user_table

**Note** naming convention for creating the migration for the table

>$ php artisan make:migration add_user_id_to_posts

**Note** naming convention for creating the migration for adding column to table

##### Relationships

```php
class Post extends Model {

    // Post belongs to User
    // One to One (One post --> One user)
    public function user() {
        return $this->belongsTo('App\User');
    }
}


class User extens Model {

    // User has many Posts
    // One to Many (One user --> Many Posts)
    public function posts() {
        return $this->hasMany('App\Post');
    }
}

$user->posts // get all posts of the user in controller or view
$post->user->name // get user of the post in the controller or view

public function __construct() {

    // middleware enabled by the auth
    // middleware are the function execute before route to process request object and is used to authenticate and authorize the user.
    // middleware applied in the constructor of the class will apply only to the class in which it is called.

    $this->middleware('auth'); // blocked request if user is logged in
    $this->middleware('guest'); // if user is not logged in

    // apply the middleware on all the resources methods except index and show.
    $this->middleware('auth', ['except' => ['index', 'show']]);
}
```

##### Laravel file uploading

```php
if($request->hasFile('myImage')) {
    // get filename with extension
    $filenameWithExtension = $request->file('myImage')->getClientOriginalImage();

    // get file extension
    $extension = $request->file('myImage')->getClientOriginalExtension();

    // upload image to resource/storage/public/images directory
    $path = $request->file('myImage')->storeAs('public/images', 'newFileName.png');

    // delete the via Storage facade
    Storage::delete('public/images/newFileName.png');
}
```

>$ php artisan storage:link

**Note** to create the symbolic link from resource/storage to public/storage. public/storage is the directory expose to the web.

##### Seeder and Factory
Factory is used to seed fake data into the table.

>$ php artisan make:seeder ArticleTableSeeder

**Note** make seeder for the table

```php
// ArticleTableSeeder.php
public function run() {

    // using factory create 30 records via Article Model
    factory(App\Article::class, 30)->create();
}
```

>$ php artisan make:factory ArticleFactory

**Note** to create factory

```php
$factory->define(App\Article::class, function(Faker $faker) {
    return [
        'title' => $faker->text(50),
        'body' => $faker->text(50)
    ];
});
```

>$ php artisan db:seed

**Note** run seeder to insert fake data in the table.

-----
