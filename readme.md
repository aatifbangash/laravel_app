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
**FYI:** *'Laravel Blade Snippets'* is the vscode extension for blade snippets and code completion.

##### Basic blade template layout example. 
layout file the base file which will be extended into the view files and view files will yield content into it. The main purpose of the layout is to avoid the content repetition.

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

##### View file example.
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
**app/resource/assets** is the assets directories. Every Laravel project is having the package.json file with dependencies like laravel-mix which used to compile and minify the assets from **app/resource/assets** into **app/public/css, app/public/js** folders which will later be included into the mail layout file to load.
***Note:-*** We must not change the content of the assets in the public folder, rather we will make changes or add new css files in the resource/assets folder and then compile the assets via npm.

###### In order to compile the assets we have to install the npm and run the following commands.
>$ npm install **//in the project directory**

>$ npm run dev **//to re-compile the assets whenever we make changes into the resource/assets**

##### Or
>$ npm run watch **//will constantly watching for the assets changes and re-compile automatically**

