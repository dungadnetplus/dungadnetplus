


> Written with [StackEdit](https://stackedit.io/).

#### TUTORIAL

# Simple Laravel CRUD with Resource Controllers

-   By  Chris on Code
    
    Published onSeptember 21, 2020  64.3kviews

While this tutorial has content that we believe is of great benefit to our community, we have not yet tested or edited it to ensure you have an error-free learning experience. It's on our list, and we're working on it! You can help us out by using the "report an issue" button at the bottom of the tutorial.

Creating, reading, updating, and deleting resources is used in pretty much every application. Laravel helps make the process easy using resource controllers. Resource Controllers can make life much easier and takes advantage of some cool Laravel routing techniques. Today, we’ll go through the steps necessary to get a fully functioning CRUD application using resource controllers.

For this tutorial, we will go through the process of having an admin panel to create, read, update, and delete (CRUD) a resource. Let’s use  sharks  as our example. We will also make use of  [Eloquent ORM](http://laravel.com/docs/eloquent).

This tutorial will walk us through:

-   Setting up the database and models
-   Creating the resource controller and its routes
-   Creating the necessary views
-   Explaining each method in a resource controller

To get started, we will need the  **controller**, the  **routes**, and the  **view**  files.

You can view and clone a  [repo of all the code covered in this tutorial on GitHub](https://github.com/scotch-io/simple-laravel-crud)  

## Getting our Database Ready

### Shark Migration

We need to set up a quick database so we can do all of our CRUD functionality. In the command line in the root directory of our Laravel application, let’s create a migration.

```bash

```

Copy

This will create our shark migration in  `app/database/migrations`. Open up that file and let’s add name, email, and shark_level fields.

app/database/migrations/####_##_##_######_create_sharks_table.php

```php
<?php

use IlluminateDatabaseSchemaBlueprint;
use IlluminateDatabaseMigrationsMigration;

class CreatesharksTable extends Migration {

    /**
        * Run the migrations.
        *
        * @return void
        */
    public function up()
    {
        Schema::create('sharks', function(Blueprint $table)
        {
            $table->increments('id');

            $table->string('name', 255);
            $table->string('email', 255);
            $table->integer('shark_level');

            $table->timestamps();
        });
    }

    /**
        * Reverse the migrations.
        *
        * @return void
        */
    public function down()
    {
        Schema::drop('sharks');
    }

}
```

Copy

Now from the command line again, let’s run this migration. Make sure your  **database settings**  are good in  `app/config/database.php`  and then run:

`php artisan migrate`  Our database now has a sharks table to house all of the sharks we CRUD (create, read, update, and delete). Read more about migrations at the  [Laravel docs](http://laravel.com/docs/migrations#creating-migrations).

## Eloquent Model for the sharks

Now that we have our database, let’s create a simple Eloquent model so that we can access the sharks in our database easily. You can read about  [Eloquent ORM](http://laravel.com/docs/eloquent)  and see how you can use it in your own applications.

In the  `app/models`  folder, let’s create a shark.php model.

app/models/shark.php

```php
<?php

    class shark extends Eloquent
    {

    }
```

Copy

That’s it! Eloquent can handle the rest. By default, this model will link to our  `sharks`  table and we can access it later in our controllers.

## Creating the Controller

From the official Laravel docs, on  [resource controllers](http://laravel.com/docs/controllers#resource-controllers), you can generate a resource controller using the artisan tool.

Let’s go ahead and do that. This is the easy part. From the command line in the root directory of your Laravel project, type:

`php artisan make:controller sharkController --resource`  This will create our resource controller with all the methods we need.

app/controllers/sharkController.php

```php
<?php

class sharkController extends BaseController {

    /**
        * Display a listing of the resource.
        *
        * @return Response
        */
    public function index()
    {
        //
    }

    /**
        * Show the form for creating a new resource.
        *
        * @return Response
        */
    public function create()
    {
        //
    }

    /**
        * Store a newly created resource in storage.
        *
        * @return Response
        */
    public function store()
    {
        //
    }

    /**
        * Display the specified resource.
        *
        * @param  int  $id
        * @return Response
        */
    public function show($id)
    {
        //
    }

    /**
        * Show the form for editing the specified resource.
        *
        * @param  int  $id
        * @return Response
        */
    public function edit($id)
    {
        //
    }

    /**
        * Update the specified resource in storage.
        *
        * @param  int  $id
        * @return Response
        */
    public function update($id)
    {
        //
    }

    /**
        * Remove the specified resource from storage.
        *
        * @param  int  $id
        * @return Response
        */
    public function destroy($id)
    {
        //
    }

}
```

Copy

## Setting Up the Routes

Now that we have generated our controller, let’s make sure our application has the routes necessary to use it. This is the other easy part (they actually might all be easy parts). In your  **routes.php**  file, add this line:

app/routes.php

```php
<?php

    Route::resource('sharks', 'sharkController');
```

Copy

This will automatically assign many actions to that resource controller. Now if you, go to your browser and view your application at  `example.com/sharks`, it will correspond to the proper method in your sharkController.

### Actions Handled By the Controller

HTTP Verb

Path (URL)

Action (Method)

Route Name

GET

/sharks

index

sharks.index

GET

/sharks/create

create

sharks.create

POST

/sharks

store

sharks.store

GET

/sharks/{id}

show

sharks.show

GET

/sharks/{id}/edit

edit

sharks.edit

PUT/PATCH

/sharks/{id}

update

sharks.update

DELETE

/sharks/{id}

destroy

sharks.destroy

**Tip:**  From the command line, you can run  `php artisan routes`  to see all the routes associated with your application.

## The Views

Since only four of our routes are GET routes, we only need four views. In our  `app/views`  folder, let’s make those views now.

```
app
└───views
    └───sharks
        │    index.blade.php
        │    create.blade.php
        │    show.blade.php
        │    edit.blade.php

```

## Making It All Work Together

Now we have our  **migrations, database, and models**, our  **controller and routes**, and our  **views**. Let’s make all these things work together to build our application. We are going to go through the methods created in the resource controller one by one and make it all work.

### Showing All Resources sharks.index

Description

URL

Controller Function

View File

Default page for showing all the sharks.

GET example.com/sharks

index()

app/views/sharks/index.blade.php

#### Controller Function index()

In this function, we will get all the sharks and pass them to the view.

app/controllers/sharkController.php

```php
<?php

...

    /**
        * Display a listing of the resource.
        *
        * @return Response
        */
    public function index()
    {
        // get all the sharks
        $sharks = shark::all();

        // load the view and pass the sharks
        return View::make('sharks.index')
            ->with('sharks', $sharks);
    }
...
```

Copy

#### The View app/views/sharks/index.blade.php

Now let’s create our view to loop over the sharks and display them in a table. We like using  [Twitter Bootstrap](http://getbootstrap.com/)  for our sites, so the table will use those classes.

app/views/sharks/index.blade.php

```php
<!DOCTYPE html>
<html>
<head>
    <title>Shark App</title>
    <link rel="stylesheet" href="//netdna.bootstrapcdn.com/bootstrap/3.0.0/css/bootstrap.min.css">
</head>
<body>
<div class="container">

<nav class="navbar navbar-inverse">
    <div class="navbar-header">
        <a class="navbar-brand" href="{{ URL::to('sharks') }}">shark Alert</a>
    </div>
    <ul class="nav navbar-nav">
        <li><a href="{{ URL::to('sharks') }}">View All sharks</a></li>
        <li><a href="{{ URL::to('sharks/create') }}">Create a shark</a>
    </ul>
</nav>

<h1>All the sharks</h1>

<!-- will be used to show any messages -->
@if (Session::has('message'))
    <div class="alert alert-info">{{ Session::get('message') }}</div>
@endif

<table class="table table-striped table-bordered">
    <thead>
        <tr>
            <td>ID</td>
            <td>Name</td>
            <td>Email</td>
            <td>shark Level</td>
            <td>Actions</td>
        </tr>
    </thead>
    <tbody>
    @foreach($sharks as $key => $value)
        <tr>
            <td>{{ $value->id }}</td>
            <td>{{ $value->name }}</td>
            <td>{{ $value->email }}</td>
            <td>{{ $value->shark_level }}</td>

            <!-- we will also add show, edit, and delete buttons -->
            <td>

                <!-- delete the shark (uses the destroy method DESTROY /sharks/{id} -->
                <!-- we will add this later since its a little more complicated than the other two buttons -->

                <!-- show the shark (uses the show method found at GET /sharks/{id} -->
                <a class="btn btn-small btn-success" href="{{ URL::to('sharks/' . $value->id) }}">Show this shark</a>

                <!-- edit this shark (uses the edit method found at GET /sharks/{id}/edit -->
                <a class="btn btn-small btn-info" href="{{ URL::to('sharks/' . $value->id . '/edit') }}">Edit this shark</a>

            </td>
        </tr>
    @endforeach
    </tbody>
</table>

</div>
</body>
</html>
```

Copy

We can now show all of our sharks on a page. There won’t be any that show up currently since we haven’t created any or seeded our database with sharks. Let’s move on to the form to create a shark.

[![index-blade](https://cask.scotch.io/2013/10/index-blade.png)](https://cask.scotch.io/2013/10/index-blade.png)

### Creating a New Resource sharks.create

Description

URL

Controller Function

View File

Show the form to create a new shark.

GET example.com/sharks/create

create()

app/views/sharks/create.blade.php

#### Controller Function create()

In this function, we will show the form for creating a new shark. This form will be processed by the  `store()`  method.

app/controllers/sharkController.php

```php
<?php
...
    /**
        * Show the form for creating a new resource.
        *
        * @return Response
        */
    public function create()
    {
        // load the create form (app/views/sharks/create.blade.php)
        return View::make('sharks.create');
    }
...
```

Copy

#### The View app/views/sharks/create.blade.php

app/views/sharks/create.blade.php

```php
<!DOCTYPE html>
<html>
<head>
    <title>Shark App</title>
    <link rel="stylesheet" href="//netdna.bootstrapcdn.com/bootstrap/3.0.0/css/bootstrap.min.css">
</head>
<body>
<div class="container">

<nav class="navbar navbar-inverse">
    <div class="navbar-header">
        <a class="navbar-brand" href="{{ URL::to('sharks') }}">shark Alert</a>
    </div>
    <ul class="nav navbar-nav">
        <li><a href="{{ URL::to('sharks') }}">View All sharks</a></li>
        <li><a href="{{ URL::to('sharks/create') }}">Create a shark</a>
    </ul>
</nav>

<h1>Create a shark</h1>

<!-- if there are creation errors, they will show here -->
{{ HTML::ul($errors->all()) }}

{{ Form::open(array('url' => 'sharks')) }}

    <div class="form-group">
        {{ Form::label('name', 'Name') }}
        {{ Form::text('name', Input::old('name'), array('class' => 'form-control')) }}
    </div>

    <div class="form-group">
        {{ Form::label('email', 'Email') }}
        {{ Form::email('email', Input::old('email'), array('class' => 'form-control')) }}
    </div>

    <div class="form-group">
        {{ Form::label('shark_level', 'shark Level') }}
        {{ Form::select('shark_level', array('0' => 'Select a Level', '1' => 'Sees Sunlight', '2' => 'Foosball Fanatic', '3' => 'Basement Dweller'), Input::old('shark_level'), array('class' => 'form-control')) }}
    </div>

    {{ Form::submit('Create the shark!', array('class' => 'btn btn-primary')) }}

{{ Form::close() }}

</div>
</body>
</html>
```

Copy

We will add the errors section above to show validation errors when we try to  `store()`  the resource.  
**Tip:**  When using  `{{ Form::open() }}`, Laravel will automatically create a hidden input field with a token to protect from cross-site request forgeries. Read more at the  [Laravel docs](http://laravel.com/docs/html#csrf-protection).  

We now have the form, but we need to have it do something when it the submit button gets pressed. We set this form’s  `action`  to be a POST to  **example.com/sharks**. The resource controller will handle this and automatically route the request to the  `store()`  method. Let’s handle that now.

### Storing a Resource store()

Description

URL

Controller Function

View File

Process the create form submit and save the shark to the database.

POST example.com/sharks

store()

NONE

As you can see from the form action and the URL, you don’t have to pass anything extra into the URL to store a shark. Since this form is sent using the POST method, the form inputs will be the data used to store the resource.

To process the form, we’ll want to  **validate the inputs**,  **send back error messages if they exist**,  **authenticate against the database**, and  **store the resource if all is good**. Let’s dive in.

#### Controller Function store()

app/controllers/sharkController.php

```bash
<?php
...
    /**
        * Store a newly created resource in storage.
        *
        * @return Response
        */
    public function store()
    {
        // validate
        // read more on validation at http://laravel.com/docs/validation
        $rules = array(
            'name'       => 'required',
            'email'      => 'required|email',
            'shark_level' => 'required|numeric'
        );
        $validator = Validator::make(Input::all(), $rules);

        // process the login
        if ($validator->fails()) {
            return Redirect::to('sharks/create')
                ->withErrors($validator)
                ->withInput(Input::except('password'));
        } else {
            // store
            $shark = new shark;
            $shark->name       = Input::get('name');
            $shark->email      = Input::get('email');
            $shark->shark_level = Input::get('shark_level');
            $shark->save();

            // redirect
            Session::flash('message', 'Successfully created shark!');
            return Redirect::to('sharks');
        }
    }
...
```

Copy

If there are errors processing the form, we will redirect them back to the create form with those errors. We will add them in so the user can understand what went wrong. They will show up in the errors section we setup earlier.

Now you should be able to create a shark and have them show up on the main page! Navigate to  `example.com/sharks`  and there they are. All that’s left is  **showing a single shark**,  **updating**, and  **deleting**.

[![created](https://cask.scotch.io/2013/10/created.png)](https://cask.scotch.io/2013/10/created.png)

### Showing a Resource show()

Description

URL

Controller Function

View File

Show one of the sharks.

GET example.com/sharks/{id}

show()

app/views/sharks/show.blade.php

#### Controller Function show()

app/controllers/sharkController.php

```php
<?php

...

    /**
        * Display the specified resource.
        *
        * @param  int  $id
        * @return Response
        */
    public function show($id)
    {
        // get the shark
        $shark = shark::find($id);

        // show the view and pass the shark to it
        return View::make('sharks.show')
            ->with('shark', $shark);
    }

...
```

Copy

#### The View app/views/sharks/show.blade.php

app/views/sharks/show.blade.php

```php
<!DOCTYPE html>
<html>
<head>
    <title>Shark App</title>
    <link rel="stylesheet" href="//netdna.bootstrapcdn.com/bootstrap/3.0.0/css/bootstrap.min.css">
</head>
<body>
<div class="container">

<nav class="navbar navbar-inverse">
    <div class="navbar-header">
        <a class="navbar-brand" href="{{ URL::to('sharks') }}">shark Alert</a>
    </div>
    <ul class="nav navbar-nav">
        <li><a href="{{ URL::to('sharks') }}">View All sharks</a></li>
        <li><a href="{{ URL::to('sharks/create') }}">Create a shark</a>
    </ul>
</nav>

<h1>Showing {{ $shark->name }}</h1>

    <div class="jumbotron text-center">
        <h2>{{ $shark->name }}</h2>
        <p>
            <strong>Email:</strong> {{ $shark->email }}<br>
            <strong>Level:</strong> {{ $shark->shark_level }}
        </p>
    </div>

</div>
</body>
</html>
```

Copy

[![show](https://cask.scotch.io/2013/10/show.png)](https://cask.scotch.io/2013/10/show.png)

### Editing a Resource edit()

Description

URL

Controller Function

View File

Pull a shark from the database and allow editing.

GET example.com/sharks/{id}/edit

edit()

app/views/sharks/edit.blade.php

To edit a shark, we need to pull them from the database, show the creation form, but populate it with the selected shark’s info. To make life easier, we will use  [form model binding](http://laravel.com/docs/html#form-model-binding). This allows us to pull info from a model and bind it to the input fields in a form. Just makes it easier to populate our edit form and you can imagine that when these forms start getting rather large this will make life much easier.

#### Controller Function edit()

app/controllers/sharkController.php

```php
<?php

...

    /**
        * Show the form for editing the specified resource.
        *
        * @param  int  $id
        * @return Response
        */
    public function edit($id)
    {
        // get the shark
        $shark = shark::find($id);

        // show the edit form and pass the shark
        return View::make('sharks.edit')
            ->with('shark', $shark);
    }
...
```

Copy

#### The View app/views/sharks/edit.blade.php

app/views/sharks/edit.blade.php

```php
<!DOCTYPE html>
<html>
<head>
    <title>Shark App</title>
    <link rel="stylesheet" href="//netdna.bootstrapcdn.com/bootstrap/3.0.0/css/bootstrap.min.css">
</head>
<body>
<div class="container">

<nav class="navbar navbar-inverse">
    <div class="navbar-header">
        <a class="navbar-brand" href="{{ URL::to('sharks') }}">shark Alert</a>
    </div>
    <ul class="nav navbar-nav">
        <li><a href="{{ URL::to('sharks') }}">View All sharks</a></li>
        <li><a href="{{ URL::to('sharks/create') }}">Create a shark</a>
    </ul>
</nav>

<h1>Edit {{ $shark->name }}</h1>

<!-- if there are creation errors, they will show here -->
{{ HTML::ul($errors->all()) }}

{{ Form::model($shark, array('route' => array('sharks.update', $shark->id), 'method' => 'PUT')) }}

    <div class="form-group">
        {{ Form::label('name', 'Name') }}
        {{ Form::text('name', null, array('class' => 'form-control')) }}
    </div>

    <div class="form-group">
        {{ Form::label('email', 'Email') }}
        {{ Form::email('email', null, array('class' => 'form-control')) }}
    </div>

    <div class="form-group">
        {{ Form::label('shark_level', 'shark Level') }}
        {{ Form::select('shark_level', array('0' => 'Select a Level', '1' => 'Sees Sunlight', '2' => 'Foosball Fanatic', '3' => 'Basement Dweller'), null, array('class' => 'form-control')) }}
    </div>

    {{ Form::submit('Edit the shark!', array('class' => 'btn btn-primary')) }}

{{ Form::close() }}

</div>
</body>
</html>
```

Copy

Note that we have to pass a method of  `PUT`  so that Laravel knows how to route to the controller correctly.

### Updating a Resource update()

Description

URL

Controller Function

View File

Process the create form submit and save the shark to the database.

PUT example.com/sharks

update()

NONE

This controller method will process the edit form. It is very similar to  `store()`. We will  **validate**,  **update**, and  **redirect**.

#### Controller Function update()

app/controllers/sharkController.php

```php
<?php

...

    /**
        * Update the specified resource in storage.
        *
        * @param  int  $id
        * @return Response
        */
    public function update($id)
    {
        // validate
        // read more on validation at http://laravel.com/docs/validation
        $rules = array(
            'name'       => 'required',
            'email'      => 'required|email',
            'shark_level' => 'required|numeric'
        );
        $validator = Validator::make(Input::all(), $rules);

        // process the login
        if ($validator->fails()) {
            return Redirect::to('sharks/' . $id . '/edit')
                ->withErrors($validator)
                ->withInput(Input::except('password'));
        } else {
            // store
            $shark = shark::find($id);
            $shark->name       = Input::get('name');
            $shark->email      = Input::get('email');
            $shark->shark_level = Input::get('shark_level');
            $shark->save();

            // redirect
            Session::flash('message', 'Successfully updated shark!');
            return Redirect::to('sharks');
        }
    }
...
```

Copy

### Deleting a Resource destroy()

Description

URL

Controller Function

View File

Process the create form submit and save the shark to the database.

DELETE example.com/sharks/{id}

destroy()

NONE

The workflow for this is that a user would go to view all the sharks, see a delete button, click it to delete. Since we never created a delete button in our  `app/views/sharks/index.blade.php`, we will create that now. We will also add a notification section to show a success message.

We have to send the request to our application using the  **DELETE**  HTTP verb, so we will create a form to do that since a button won’t do.

**Alert:**  The DELETE HTTP verb is used when accessing the  `sharks.destroy`  route. Since you can’t just create a button or form with the  **method**  DELETE, we will have to spoof it by creating a hidden input field in our delete form.  

#### The View app/views/sharks/index.blade.php

app/views/sharks/index.blade.php

```php
...

    @foreach($sharks as $key => $value)
        <tr>
            <td>{{ $value->id }}</td>
            <td>{{ $value->name }}</td>
            <td>{{ $value->email }}</td>
            <td>{{ $value->shark_level }}</td>

            <!-- we will also add show, edit, and delete buttons -->
            <td>

                <!-- delete the shark (uses the destroy method DESTROY /sharks/{id} -->
                <!-- we will add this later since its a little more complicated than the other two buttons -->
                {{ Form::open(array('url' => 'sharks/' . $value->id, 'class' => 'pull-right')) }}
                    {{ Form::hidden('_method', 'DELETE') }}
                    {{ Form::submit('Delete this shark', array('class' => 'btn btn-warning')) }}
                {{ Form::close() }}

                <!-- show the shark (uses the show method found at GET /sharks/{id} -->
                <a class="btn btn-small btn-success" href="{{ URL::to('sharks/' . $value->id) }}">Show this shark</a>

                <!-- edit this shark (uses the edit method found at GET /sharks/{id}/edit -->
                <a class="btn btn-small btn-info" href="{{ URL::to('sharks/' . $value->id . '/edit') }}">Edit this shark</a>

            </td>
        </tr>
    @endforeach
    ...
```

Copy

Now when we click that form submit button, Laravel will use the sharks.destroy route and we can process that in our controller.

#### Controller Function destroy()

app/controllers/sharkController.php

```php
<?php

...

    /**
        * Remove the specified resource from storage.
        *
        * @param  int  $id
        * @return Response
        */
    public function destroy($id)
    {
        // delete
        $shark = shark::find($id);
        $shark->delete();

        // redirect
        Session::flash('message', 'Successfully deleted the shark!');
        return Redirect::to('sharks');
    }
...
```

Copy

## Conclusion

That’s everything! Hopefully we covered enough so that you can understand how resource controllers can be used in all sorts of scenarios. Just create the controller, create the single line in the routes file, and you have the foundation for doing CRUD.

As always, let us know if you have any questions or comments. We’ll be expanding more on Laravel in the coming articles so if there’s anything specific, throw it in the comments or email us.

**Further Reading**: For more Laravel, check out our  [Simple Laravel Series](https://www.digitalocean.com/series/simple-laravel).
