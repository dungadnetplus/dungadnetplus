


> Written with [StackEdit](https://stackedit.io/).

# Laravel 8 Middleware Example Tutorial

----------

**Share:**

**Published By:** Admin,  **Published On:** Jun 01, 2021,  **Category:** Laravel 8  Laravel

![](https://www.mywebtuts.com/upload/blog/middleware.png)

Hi Guys,

Today, i will explain you how to use Middleware in laravel 8. so you can just follow my step by step and learn laravel custom middleware example tutorial.

Simply laravel middleware filter all the http request in laravel predicated projects. For example when utilizer is do any request that time middleware check utilizer is loggedin or not and redirect accordingly. Any utilizer is not loggedin but he want to access to dashboard or other things in projects that time middleware filter request redirect to utilizer.

In this article we will give a define example of active or inactive users.after we call middleware in routes to restrict logged user to access that routes,Let,s check it.

So let's start to the example and follow to the my all step.

**Step 1 : Create Middleware**

In this first step first of all we will create a middleware in app/http/middleware simple command so let's your open command prompt and paste bellow code.

php artisan make:middleware UserStatus

**Step 2 : Register Middleware**

Next, step we will go to app/http/kernel.php and register your middleware custome name.

**Path : app/Http/kernel.php**
```php
<?php

namespace App\Http;

use Illuminate\Foundation\Http\Kernel as HttpKernel;

class Kernel extends HttpKernel

{

/**

* The application's route middleware.

*

* These middleware may be assigned to groups or used individually.

*

* @var array

*/

protected  $routeMiddleware = [

....

'UserStatus' => \App\Http\Middleware\UserStatus::class,

];

}

?>

```

**Step 3: Implement Your Logic In Middleware**

In this third step we will, go to app/http/middleware and implement your logic here :

**Path : app/Http/Middleware/UserStatus.php**
```php
<?php

namespace App\Http\Middleware;

use Closure;

class UserStatus

{

/**

* Handle an incoming request.

*

* @param \Illuminate\Http\Request $request

* @param \Closure $next

* @return mixed

*/

public  function  handle($request, Closure $next)

{

if  (auth()->user()->status == 'active')  {

return  $next($request);

}

return  response()->json('Your account is inactive');

}

}

?>
```
**Step 4 : Create Route**

In this fourth step to create a route and use custom middleware here in web.php file and use this code.

**Path : routes/web.php**
```php
<?php

use App\Http\Controllers\StudentController;

use App\Http\Middleware\UserStatus;

/*

|--------------------------------------------------------------------------

| Web Routes

|--------------------------------------------------------------------------

|

| Here is where you can register web routes for your application. These

| routes are loaded by the RouteServiceProvider within a group which

| contains the "web" middleware group. Now create something great!

|

*/

Route::middleware([UserStatus::class])->group(function(){

Route::get('home', [StudentController::class,'home']);

});
```
**Step 5 : Add Method In StudentController**

Now we will create one method name language and let’s check :

**Path : app/Http/Controllers/StudentController.php**
```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class StudentController extends Controller

{

public  function  home()

{

dd('You are active');

}

}
```

Now, We have successfully engender custom middleware in laravel 8 based project with simple example.you can check and run the example.

we will open our terminal or command prompt and run the following command:

`php artisan serve`

Now you can open bellow URL on your browser:

`Browser url run : http://localhost:8000/home`

I Hope it Will Help You...
