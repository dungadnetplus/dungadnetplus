---


---

<blockquote>
<p>Written with <a href="https://stackedit.io/">StackEdit</a>.</p>
</blockquote>
<h4 id="tutorial">TUTORIAL</h4>
<h1 id="simple-laravel-crud-with-resource-controllers">Simple Laravel CRUD with Resource Controllers</h1>
<ul>
<li>
<p>By  Chris on Code</p>
<p>Published onSeptember 21, 2020  64.3kviews</p>
</li>
</ul>
<p>While this tutorial has content that we believe is of great benefit to our community, we have not yet tested or edited it to ensure you have an error-free learning experience. It’s on our list, and we’re working on it! You can help us out by using the “report an issue” button at the bottom of the tutorial.</p>
<p>Creating, reading, updating, and deleting resources is used in pretty much every application. Laravel helps make the process easy using resource controllers. Resource Controllers can make life much easier and takes advantage of some cool Laravel routing techniques. Today, we’ll go through the steps necessary to get a fully functioning CRUD application using resource controllers.</p>
<p>For this tutorial, we will go through the process of having an admin panel to create, read, update, and delete (CRUD) a resource. Let’s use  sharks  as our example. We will also make use of  <a href="http://laravel.com/docs/eloquent">Eloquent ORM</a>.</p>
<p>This tutorial will walk us through:</p>
<ul>
<li>Setting up the database and models</li>
<li>Creating the resource controller and its routes</li>
<li>Creating the necessary views</li>
<li>Explaining each method in a resource controller</li>
</ul>
<p>To get started, we will need the  <strong>controller</strong>, the  <strong>routes</strong>, and the  <strong>view</strong>  files.</p>
<p>You can view and clone a  <a href="https://github.com/scotch-io/simple-laravel-crud">repo of all the code covered in this tutorial on GitHub</a></p>
<h2 id="getting-our-database-ready">Getting our Database Ready</h2>
<h3 id="shark-migration">Shark Migration</h3>
<p>We need to set up a quick database so we can do all of our CRUD functionality. In the command line in the root directory of our Laravel application, let’s create a migration.</p>
<pre class=" language-bash"><code class="prism  language-bash">
</code></pre>
<p>Copy</p>
<p>This will create our shark migration in  <code>app/database/migrations</code>. Open up that file and let’s add name, email, and shark_level fields.</p>
<p>app/database/migrations/####<em>##</em>##_######_create_sharks_table.php</p>
<pre class=" language-php"><code class="prism  language-php"><span class="token php language-php"><span class="token delimiter important">&lt;?php</span>

<span class="token keyword">use</span> <span class="token package">IlluminateDatabaseSchemaBlueprint</span><span class="token punctuation">;</span>
<span class="token keyword">use</span> <span class="token package">IlluminateDatabaseMigrationsMigration</span><span class="token punctuation">;</span>

<span class="token keyword">class</span> <span class="token class-name">CreatesharksTable</span> <span class="token keyword">extends</span> <span class="token class-name">Migration</span> <span class="token punctuation">{</span>

    <span class="token comment">/**
        * Run the migrations.
        *
        * @return void
        */</span>
    <span class="token keyword">public</span> <span class="token keyword">function</span> <span class="token function">up</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
    <span class="token punctuation">{</span>
        Schema<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">create</span><span class="token punctuation">(</span><span class="token string">'sharks'</span><span class="token punctuation">,</span> <span class="token keyword">function</span><span class="token punctuation">(</span>Blueprint <span class="token variable">$table</span><span class="token punctuation">)</span>
        <span class="token punctuation">{</span>
            <span class="token variable">$table</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token function">increments</span><span class="token punctuation">(</span><span class="token string">'id'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

            <span class="token variable">$table</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token function">string</span><span class="token punctuation">(</span><span class="token string">'name'</span><span class="token punctuation">,</span> <span class="token number">255</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token variable">$table</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token function">string</span><span class="token punctuation">(</span><span class="token string">'email'</span><span class="token punctuation">,</span> <span class="token number">255</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token variable">$table</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token function">integer</span><span class="token punctuation">(</span><span class="token string">'shark_level'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

            <span class="token variable">$table</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token function">timestamps</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token comment">/**
        * Reverse the migrations.
        *
        * @return void
        */</span>
    <span class="token keyword">public</span> <span class="token keyword">function</span> <span class="token function">down</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
    <span class="token punctuation">{</span>
        Schema<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">drop</span><span class="token punctuation">(</span><span class="token string">'sharks'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span>
</span></code></pre>
<p>Copy</p>
<p>Now from the command line again, let’s run this migration. Make sure your  <strong>database settings</strong>  are good in  <code>app/config/database.php</code>  and then run:</p>
<p><code>php artisan migrate</code>  Our database now has a sharks table to house all of the sharks we CRUD (create, read, update, and delete). Read more about migrations at the  <a href="http://laravel.com/docs/migrations#creating-migrations">Laravel docs</a>.</p>
<h2 id="eloquent-model-for-the-sharks">Eloquent Model for the sharks</h2>
<p>Now that we have our database, let’s create a simple Eloquent model so that we can access the sharks in our database easily. You can read about  <a href="http://laravel.com/docs/eloquent">Eloquent ORM</a>  and see how you can use it in your own applications.</p>
<p>In the  <code>app/models</code>  folder, let’s create a shark.php model.</p>
<p>app/models/shark.php</p>
<pre class=" language-php"><code class="prism  language-php"><span class="token php language-php"><span class="token delimiter important">&lt;?php</span>

    <span class="token keyword">class</span> <span class="token class-name">shark</span> <span class="token keyword">extends</span> <span class="token class-name">Eloquent</span>
    <span class="token punctuation">{</span>

    <span class="token punctuation">}</span>
</span></code></pre>
<p>Copy</p>
<p>That’s it! Eloquent can handle the rest. By default, this model will link to our  <code>sharks</code>  table and we can access it later in our controllers.</p>
<h2 id="creating-the-controller">Creating the Controller</h2>
<p>From the official Laravel docs, on  <a href="http://laravel.com/docs/controllers#resource-controllers">resource controllers</a>, you can generate a resource controller using the artisan tool.</p>
<p>Let’s go ahead and do that. This is the easy part. From the command line in the root directory of your Laravel project, type:</p>
<p><code>php artisan make:controller sharkController --resource</code>  This will create our resource controller with all the methods we need.</p>
<p>app/controllers/sharkController.php</p>
<pre class=" language-php"><code class="prism  language-php"><span class="token php language-php"><span class="token delimiter important">&lt;?php</span>

<span class="token keyword">class</span> <span class="token class-name">sharkController</span> <span class="token keyword">extends</span> <span class="token class-name">BaseController</span> <span class="token punctuation">{</span>

    <span class="token comment">/**
        * Display a listing of the resource.
        *
        * @return Response
        */</span>
    <span class="token keyword">public</span> <span class="token keyword">function</span> <span class="token function">index</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
    <span class="token punctuation">{</span>
        <span class="token comment">//</span>
    <span class="token punctuation">}</span>

    <span class="token comment">/**
        * Show the form for creating a new resource.
        *
        * @return Response
        */</span>
    <span class="token keyword">public</span> <span class="token keyword">function</span> <span class="token function">create</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
    <span class="token punctuation">{</span>
        <span class="token comment">//</span>
    <span class="token punctuation">}</span>

    <span class="token comment">/**
        * Store a newly created resource in storage.
        *
        * @return Response
        */</span>
    <span class="token keyword">public</span> <span class="token keyword">function</span> <span class="token function">store</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
    <span class="token punctuation">{</span>
        <span class="token comment">//</span>
    <span class="token punctuation">}</span>

    <span class="token comment">/**
        * Display the specified resource.
        *
        * @param  int  $id
        * @return Response
        */</span>
    <span class="token keyword">public</span> <span class="token keyword">function</span> <span class="token function">show</span><span class="token punctuation">(</span><span class="token variable">$id</span><span class="token punctuation">)</span>
    <span class="token punctuation">{</span>
        <span class="token comment">//</span>
    <span class="token punctuation">}</span>

    <span class="token comment">/**
        * Show the form for editing the specified resource.
        *
        * @param  int  $id
        * @return Response
        */</span>
    <span class="token keyword">public</span> <span class="token keyword">function</span> <span class="token function">edit</span><span class="token punctuation">(</span><span class="token variable">$id</span><span class="token punctuation">)</span>
    <span class="token punctuation">{</span>
        <span class="token comment">//</span>
    <span class="token punctuation">}</span>

    <span class="token comment">/**
        * Update the specified resource in storage.
        *
        * @param  int  $id
        * @return Response
        */</span>
    <span class="token keyword">public</span> <span class="token keyword">function</span> <span class="token function">update</span><span class="token punctuation">(</span><span class="token variable">$id</span><span class="token punctuation">)</span>
    <span class="token punctuation">{</span>
        <span class="token comment">//</span>
    <span class="token punctuation">}</span>

    <span class="token comment">/**
        * Remove the specified resource from storage.
        *
        * @param  int  $id
        * @return Response
        */</span>
    <span class="token keyword">public</span> <span class="token keyword">function</span> <span class="token function">destroy</span><span class="token punctuation">(</span><span class="token variable">$id</span><span class="token punctuation">)</span>
    <span class="token punctuation">{</span>
        <span class="token comment">//</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span>
</span></code></pre>
<p>Copy</p>
<h2 id="setting-up-the-routes">Setting Up the Routes</h2>
<p>Now that we have generated our controller, let’s make sure our application has the routes necessary to use it. This is the other easy part (they actually might all be easy parts). In your  <strong>routes.php</strong>  file, add this line:</p>
<p>app/routes.php</p>
<pre class=" language-php"><code class="prism  language-php"><span class="token php language-php"><span class="token delimiter important">&lt;?php</span>

    Route<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">resource</span><span class="token punctuation">(</span><span class="token string">'sharks'</span><span class="token punctuation">,</span> <span class="token string">'sharkController'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</span></code></pre>
<p>Copy</p>
<p>This will automatically assign many actions to that resource controller. Now if you, go to your browser and view your application at  <code>example.com/sharks</code>, it will correspond to the proper method in your sharkController.</p>
<h3 id="actions-handled-by-the-controller">Actions Handled By the Controller</h3>
<p>HTTP Verb</p>
<p>Path (URL)</p>
<p>Action (Method)</p>
<p>Route Name</p>
<p>GET</p>
<p>/sharks</p>
<p>index</p>
<p>sharks.index</p>
<p>GET</p>
<p>/sharks/create</p>
<p>create</p>
<p>sharks.create</p>
<p>POST</p>
<p>/sharks</p>
<p>store</p>
<p>sharks.store</p>
<p>GET</p>
<p>/sharks/{id}</p>
<p>show</p>
<p>sharks.show</p>
<p>GET</p>
<p>/sharks/{id}/edit</p>
<p>edit</p>
<p>sharks.edit</p>
<p>PUT/PATCH</p>
<p>/sharks/{id}</p>
<p>update</p>
<p>sharks.update</p>
<p>DELETE</p>
<p>/sharks/{id}</p>
<p>destroy</p>
<p>sharks.destroy</p>
<p><strong>Tip:</strong>  From the command line, you can run  <code>php artisan routes</code>  to see all the routes associated with your application.</p>
<h2 id="the-views">The Views</h2>
<p>Since only four of our routes are GET routes, we only need four views. In our  <code>app/views</code>  folder, let’s make those views now.</p>
<pre><code>app
└───views
    └───sharks
        │    index.blade.php
        │    create.blade.php
        │    show.blade.php
        │    edit.blade.php

</code></pre>
<h2 id="making-it-all-work-together">Making It All Work Together</h2>
<p>Now we have our  <strong>migrations, database, and models</strong>, our  <strong>controller and routes</strong>, and our  <strong>views</strong>. Let’s make all these things work together to build our application. We are going to go through the methods created in the resource controller one by one and make it all work.</p>
<h3 id="showing-all-resources-sharks.index">Showing All Resources sharks.index</h3>
<p>Description</p>
<p>URL</p>
<p>Controller Function</p>
<p>View File</p>
<p>Default page for showing all the sharks.</p>
<p>GET <a href="http://example.com/sharks">example.com/sharks</a></p>
<p>index()</p>
<p>app/views/sharks/index.blade.php</p>
<h4 id="controller-function-index">Controller Function index()</h4>
<p>In this function, we will get all the sharks and pass them to the view.</p>
<p>app/controllers/sharkController.php</p>
<pre class=" language-php"><code class="prism  language-php"><span class="token php language-php"><span class="token delimiter important">&lt;?php</span>

<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>

    <span class="token comment">/**
        * Display a listing of the resource.
        *
        * @return Response
        */</span>
    <span class="token keyword">public</span> <span class="token keyword">function</span> <span class="token function">index</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
    <span class="token punctuation">{</span>
        <span class="token comment">// get all the sharks</span>
        <span class="token variable">$sharks</span> <span class="token operator">=</span> shark<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">all</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token comment">// load the view and pass the sharks</span>
        <span class="token keyword">return</span> View<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">make</span><span class="token punctuation">(</span><span class="token string">'sharks.index'</span><span class="token punctuation">)</span>
            <span class="token operator">-</span><span class="token operator">&gt;</span><span class="token function">with</span><span class="token punctuation">(</span><span class="token string">'sharks'</span><span class="token punctuation">,</span> <span class="token variable">$sharks</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
</span></code></pre>
<p>Copy</p>
<h4 id="the-view-appviewssharksindex.blade.php">The View app/views/sharks/index.blade.php</h4>
<p>Now let’s create our view to loop over the sharks and display them in a table. We like using  <a href="http://getbootstrap.com/">Twitter Bootstrap</a>  for our sites, so the table will use those classes.</p>
<p>app/views/sharks/index.blade.php</p>
<pre class=" language-php"><code class="prism  language-php"><span class="token operator">&lt;</span><span class="token operator">!</span><span class="token constant">DOCTYPE</span> html<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span>html<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span>head<span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span>title<span class="token operator">&gt;</span>Shark App<span class="token operator">&lt;</span><span class="token operator">/</span>title<span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span>link rel<span class="token operator">=</span><span class="token string">"stylesheet"</span> href<span class="token operator">=</span><span class="token string">"//netdna.bootstrapcdn.com/bootstrap/3.0.0/css/bootstrap.min.css"</span><span class="token operator">&gt;</span>
<span class="token operator">&lt;</span><span class="token operator">/</span>head<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span>body<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span>div <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"container"</span><span class="token operator">&gt;</span>

<span class="token operator">&lt;</span>nav <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"navbar navbar-inverse"</span><span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span>div <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"navbar-header"</span><span class="token operator">&gt;</span>
        <span class="token operator">&lt;</span>a <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"navbar-brand"</span> href<span class="token operator">=</span><span class="token string">"{{ URL::to('sharks') }}"</span><span class="token operator">&gt;</span>shark Alert<span class="token operator">&lt;</span><span class="token operator">/</span>a<span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span><span class="token operator">/</span>div<span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span>ul <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"nav navbar-nav"</span><span class="token operator">&gt;</span>
        <span class="token operator">&lt;</span>li<span class="token operator">&gt;</span><span class="token operator">&lt;</span>a href<span class="token operator">=</span><span class="token string">"{{ URL::to('sharks') }}"</span><span class="token operator">&gt;</span>View All sharks<span class="token operator">&lt;</span><span class="token operator">/</span>a<span class="token operator">&gt;</span><span class="token operator">&lt;</span><span class="token operator">/</span>li<span class="token operator">&gt;</span>
        <span class="token operator">&lt;</span>li<span class="token operator">&gt;</span><span class="token operator">&lt;</span>a href<span class="token operator">=</span><span class="token string">"{{ URL::to('sharks/create') }}"</span><span class="token operator">&gt;</span>Create a shark<span class="token operator">&lt;</span><span class="token operator">/</span>a<span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span><span class="token operator">/</span>ul<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span><span class="token operator">/</span>nav<span class="token operator">&gt;</span>

<span class="token operator">&lt;</span>h1<span class="token operator">&gt;</span>All the sharks<span class="token operator">&lt;</span><span class="token operator">/</span>h1<span class="token operator">&gt;</span>

<span class="token operator">&lt;</span><span class="token operator">!</span><span class="token operator">--</span> will be used to show any messages <span class="token operator">--</span><span class="token operator">&gt;</span>
@<span class="token keyword">if</span> <span class="token punctuation">(</span>Session<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">has</span><span class="token punctuation">(</span><span class="token string">'message'</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
    <span class="token operator">&lt;</span>div <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"alert alert-info"</span><span class="token operator">&gt;</span><span class="token punctuation">{</span><span class="token punctuation">{</span> Session<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token string">'message'</span><span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">}</span><span class="token operator">&lt;</span><span class="token operator">/</span>div<span class="token operator">&gt;</span>
@<span class="token keyword">endif</span>

<span class="token operator">&lt;</span>table <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"table table-striped table-bordered"</span><span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span>thead<span class="token operator">&gt;</span>
        <span class="token operator">&lt;</span>tr<span class="token operator">&gt;</span>
            <span class="token operator">&lt;</span>td<span class="token operator">&gt;</span><span class="token constant">ID</span><span class="token operator">&lt;</span><span class="token operator">/</span>td<span class="token operator">&gt;</span>
            <span class="token operator">&lt;</span>td<span class="token operator">&gt;</span>Name<span class="token operator">&lt;</span><span class="token operator">/</span>td<span class="token operator">&gt;</span>
            <span class="token operator">&lt;</span>td<span class="token operator">&gt;</span>Email<span class="token operator">&lt;</span><span class="token operator">/</span>td<span class="token operator">&gt;</span>
            <span class="token operator">&lt;</span>td<span class="token operator">&gt;</span>shark Level<span class="token operator">&lt;</span><span class="token operator">/</span>td<span class="token operator">&gt;</span>
            <span class="token operator">&lt;</span>td<span class="token operator">&gt;</span>Actions<span class="token operator">&lt;</span><span class="token operator">/</span>td<span class="token operator">&gt;</span>
        <span class="token operator">&lt;</span><span class="token operator">/</span>tr<span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span><span class="token operator">/</span>thead<span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span>tbody<span class="token operator">&gt;</span>
    @<span class="token keyword">foreach</span><span class="token punctuation">(</span><span class="token variable">$sharks</span> <span class="token keyword">as</span> <span class="token variable">$key</span> <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token variable">$value</span><span class="token punctuation">)</span>
        <span class="token operator">&lt;</span>tr<span class="token operator">&gt;</span>
            <span class="token operator">&lt;</span>td<span class="token operator">&gt;</span><span class="token punctuation">{</span><span class="token punctuation">{</span> <span class="token variable">$value</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token property">id</span> <span class="token punctuation">}</span><span class="token punctuation">}</span><span class="token operator">&lt;</span><span class="token operator">/</span>td<span class="token operator">&gt;</span>
            <span class="token operator">&lt;</span>td<span class="token operator">&gt;</span><span class="token punctuation">{</span><span class="token punctuation">{</span> <span class="token variable">$value</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token property">name</span> <span class="token punctuation">}</span><span class="token punctuation">}</span><span class="token operator">&lt;</span><span class="token operator">/</span>td<span class="token operator">&gt;</span>
            <span class="token operator">&lt;</span>td<span class="token operator">&gt;</span><span class="token punctuation">{</span><span class="token punctuation">{</span> <span class="token variable">$value</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token property">email</span> <span class="token punctuation">}</span><span class="token punctuation">}</span><span class="token operator">&lt;</span><span class="token operator">/</span>td<span class="token operator">&gt;</span>
            <span class="token operator">&lt;</span>td<span class="token operator">&gt;</span><span class="token punctuation">{</span><span class="token punctuation">{</span> <span class="token variable">$value</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token property">shark_level</span> <span class="token punctuation">}</span><span class="token punctuation">}</span><span class="token operator">&lt;</span><span class="token operator">/</span>td<span class="token operator">&gt;</span>

            <span class="token operator">&lt;</span><span class="token operator">!</span><span class="token operator">--</span> we will also add show<span class="token punctuation">,</span> edit<span class="token punctuation">,</span> <span class="token keyword">and</span> delete buttons <span class="token operator">--</span><span class="token operator">&gt;</span>
            <span class="token operator">&lt;</span>td<span class="token operator">&gt;</span>

                <span class="token operator">&lt;</span><span class="token operator">!</span><span class="token operator">--</span> delete the <span class="token function">shark</span> <span class="token punctuation">(</span>uses the destroy method <span class="token constant">DESTROY</span> <span class="token operator">/</span>sharks<span class="token operator">/</span><span class="token punctuation">{</span>id<span class="token punctuation">}</span> <span class="token operator">--</span><span class="token operator">&gt;</span>
                <span class="token operator">&lt;</span><span class="token operator">!</span><span class="token operator">--</span> we will add this later since its a little more complicated than the other two buttons <span class="token operator">--</span><span class="token operator">&gt;</span>

                <span class="token operator">&lt;</span><span class="token operator">!</span><span class="token operator">--</span> show the <span class="token function">shark</span> <span class="token punctuation">(</span>uses the show method found at <span class="token constant">GET</span> <span class="token operator">/</span>sharks<span class="token operator">/</span><span class="token punctuation">{</span>id<span class="token punctuation">}</span> <span class="token operator">--</span><span class="token operator">&gt;</span>
                <span class="token operator">&lt;</span>a <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"btn btn-small btn-success"</span> href<span class="token operator">=</span><span class="token string">"{{ URL::to('sharks/' . $value-&gt;id) }}"</span><span class="token operator">&gt;</span>Show this shark<span class="token operator">&lt;</span><span class="token operator">/</span>a<span class="token operator">&gt;</span>

                <span class="token operator">&lt;</span><span class="token operator">!</span><span class="token operator">--</span> edit this <span class="token function">shark</span> <span class="token punctuation">(</span>uses the edit method found at <span class="token constant">GET</span> <span class="token operator">/</span>sharks<span class="token operator">/</span><span class="token punctuation">{</span>id<span class="token punctuation">}</span><span class="token operator">/</span>edit <span class="token operator">--</span><span class="token operator">&gt;</span>
                <span class="token operator">&lt;</span>a <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"btn btn-small btn-info"</span> href<span class="token operator">=</span><span class="token string">"{{ URL::to('sharks/' . $value-&gt;id . '/edit') }}"</span><span class="token operator">&gt;</span>Edit this shark<span class="token operator">&lt;</span><span class="token operator">/</span>a<span class="token operator">&gt;</span>

            <span class="token operator">&lt;</span><span class="token operator">/</span>td<span class="token operator">&gt;</span>
        <span class="token operator">&lt;</span><span class="token operator">/</span>tr<span class="token operator">&gt;</span>
    @<span class="token keyword">endforeach</span>
    <span class="token operator">&lt;</span><span class="token operator">/</span>tbody<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span><span class="token operator">/</span>table<span class="token operator">&gt;</span>

<span class="token operator">&lt;</span><span class="token operator">/</span>div<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span><span class="token operator">/</span>body<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span><span class="token operator">/</span>html<span class="token operator">&gt;</span>
</code></pre>
<p>Copy</p>
<p>We can now show all of our sharks on a page. There won’t be any that show up currently since we haven’t created any or seeded our database with sharks. Let’s move on to the form to create a shark.</p>
<p><a href="https://cask.scotch.io/2013/10/index-blade.png"><img src="https://cask.scotch.io/2013/10/index-blade.png" alt="index-blade"></a></p>
<h3 id="creating-a-new-resource-sharks.create">Creating a New Resource sharks.create</h3>
<p>Description</p>
<p>URL</p>
<p>Controller Function</p>
<p>View File</p>
<p>Show the form to create a new shark.</p>
<p>GET <a href="http://example.com/sharks/create">example.com/sharks/create</a></p>
<p>create()</p>
<p>app/views/sharks/create.blade.php</p>
<h4 id="controller-function-create">Controller Function create()</h4>
<p>In this function, we will show the form for creating a new shark. This form will be processed by the  <code>store()</code>  method.</p>
<p>app/controllers/sharkController.php</p>
<pre class=" language-php"><code class="prism  language-php"><span class="token php language-php"><span class="token delimiter important">&lt;?php</span>
<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
    <span class="token comment">/**
        * Show the form for creating a new resource.
        *
        * @return Response
        */</span>
    <span class="token keyword">public</span> <span class="token keyword">function</span> <span class="token function">create</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
    <span class="token punctuation">{</span>
        <span class="token comment">// load the create form (app/views/sharks/create.blade.php)</span>
        <span class="token keyword">return</span> View<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">make</span><span class="token punctuation">(</span><span class="token string">'sharks.create'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
</span></code></pre>
<p>Copy</p>
<h4 id="the-view-appviewssharkscreate.blade.php">The View app/views/sharks/create.blade.php</h4>
<p>app/views/sharks/create.blade.php</p>
<pre class=" language-php"><code class="prism  language-php"><span class="token operator">&lt;</span><span class="token operator">!</span><span class="token constant">DOCTYPE</span> html<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span>html<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span>head<span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span>title<span class="token operator">&gt;</span>Shark App<span class="token operator">&lt;</span><span class="token operator">/</span>title<span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span>link rel<span class="token operator">=</span><span class="token string">"stylesheet"</span> href<span class="token operator">=</span><span class="token string">"//netdna.bootstrapcdn.com/bootstrap/3.0.0/css/bootstrap.min.css"</span><span class="token operator">&gt;</span>
<span class="token operator">&lt;</span><span class="token operator">/</span>head<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span>body<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span>div <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"container"</span><span class="token operator">&gt;</span>

<span class="token operator">&lt;</span>nav <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"navbar navbar-inverse"</span><span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span>div <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"navbar-header"</span><span class="token operator">&gt;</span>
        <span class="token operator">&lt;</span>a <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"navbar-brand"</span> href<span class="token operator">=</span><span class="token string">"{{ URL::to('sharks') }}"</span><span class="token operator">&gt;</span>shark Alert<span class="token operator">&lt;</span><span class="token operator">/</span>a<span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span><span class="token operator">/</span>div<span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span>ul <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"nav navbar-nav"</span><span class="token operator">&gt;</span>
        <span class="token operator">&lt;</span>li<span class="token operator">&gt;</span><span class="token operator">&lt;</span>a href<span class="token operator">=</span><span class="token string">"{{ URL::to('sharks') }}"</span><span class="token operator">&gt;</span>View All sharks<span class="token operator">&lt;</span><span class="token operator">/</span>a<span class="token operator">&gt;</span><span class="token operator">&lt;</span><span class="token operator">/</span>li<span class="token operator">&gt;</span>
        <span class="token operator">&lt;</span>li<span class="token operator">&gt;</span><span class="token operator">&lt;</span>a href<span class="token operator">=</span><span class="token string">"{{ URL::to('sharks/create') }}"</span><span class="token operator">&gt;</span>Create a shark<span class="token operator">&lt;</span><span class="token operator">/</span>a<span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span><span class="token operator">/</span>ul<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span><span class="token operator">/</span>nav<span class="token operator">&gt;</span>

<span class="token operator">&lt;</span>h1<span class="token operator">&gt;</span>Create a shark<span class="token operator">&lt;</span><span class="token operator">/</span>h1<span class="token operator">&gt;</span>

<span class="token operator">&lt;</span><span class="token operator">!</span><span class="token operator">--</span> <span class="token keyword">if</span> there are creation errors<span class="token punctuation">,</span> they will show here <span class="token operator">--</span><span class="token operator">&gt;</span>
<span class="token punctuation">{</span><span class="token punctuation">{</span> <span class="token constant">HTML</span><span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">ul</span><span class="token punctuation">(</span><span class="token variable">$errors</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token function">all</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">}</span>

<span class="token punctuation">{</span><span class="token punctuation">{</span> Form<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">open</span><span class="token punctuation">(</span><span class="token keyword">array</span><span class="token punctuation">(</span><span class="token string">'url'</span> <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'sharks'</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">}</span>

    <span class="token operator">&lt;</span>div <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"form-group"</span><span class="token operator">&gt;</span>
        <span class="token punctuation">{</span><span class="token punctuation">{</span> Form<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">label</span><span class="token punctuation">(</span><span class="token string">'name'</span><span class="token punctuation">,</span> <span class="token string">'Name'</span><span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">}</span>
        <span class="token punctuation">{</span><span class="token punctuation">{</span> Form<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">text</span><span class="token punctuation">(</span><span class="token string">'name'</span><span class="token punctuation">,</span> Input<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">old</span><span class="token punctuation">(</span><span class="token string">'name'</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token keyword">array</span><span class="token punctuation">(</span><span class="token string">'class'</span> <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'form-control'</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">}</span>
    <span class="token operator">&lt;</span><span class="token operator">/</span>div<span class="token operator">&gt;</span>

    <span class="token operator">&lt;</span>div <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"form-group"</span><span class="token operator">&gt;</span>
        <span class="token punctuation">{</span><span class="token punctuation">{</span> Form<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">label</span><span class="token punctuation">(</span><span class="token string">'email'</span><span class="token punctuation">,</span> <span class="token string">'Email'</span><span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">}</span>
        <span class="token punctuation">{</span><span class="token punctuation">{</span> Form<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">email</span><span class="token punctuation">(</span><span class="token string">'email'</span><span class="token punctuation">,</span> Input<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">old</span><span class="token punctuation">(</span><span class="token string">'email'</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token keyword">array</span><span class="token punctuation">(</span><span class="token string">'class'</span> <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'form-control'</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">}</span>
    <span class="token operator">&lt;</span><span class="token operator">/</span>div<span class="token operator">&gt;</span>

    <span class="token operator">&lt;</span>div <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"form-group"</span><span class="token operator">&gt;</span>
        <span class="token punctuation">{</span><span class="token punctuation">{</span> Form<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">label</span><span class="token punctuation">(</span><span class="token string">'shark_level'</span><span class="token punctuation">,</span> <span class="token string">'shark Level'</span><span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">}</span>
        <span class="token punctuation">{</span><span class="token punctuation">{</span> Form<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">select</span><span class="token punctuation">(</span><span class="token string">'shark_level'</span><span class="token punctuation">,</span> <span class="token keyword">array</span><span class="token punctuation">(</span><span class="token string">'0'</span> <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'Select a Level'</span><span class="token punctuation">,</span> <span class="token string">'1'</span> <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'Sees Sunlight'</span><span class="token punctuation">,</span> <span class="token string">'2'</span> <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'Foosball Fanatic'</span><span class="token punctuation">,</span> <span class="token string">'3'</span> <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'Basement Dweller'</span><span class="token punctuation">)</span><span class="token punctuation">,</span> Input<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">old</span><span class="token punctuation">(</span><span class="token string">'shark_level'</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token keyword">array</span><span class="token punctuation">(</span><span class="token string">'class'</span> <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'form-control'</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">}</span>
    <span class="token operator">&lt;</span><span class="token operator">/</span>div<span class="token operator">&gt;</span>

    <span class="token punctuation">{</span><span class="token punctuation">{</span> Form<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">submit</span><span class="token punctuation">(</span><span class="token string">'Create the shark!'</span><span class="token punctuation">,</span> <span class="token keyword">array</span><span class="token punctuation">(</span><span class="token string">'class'</span> <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'btn btn-primary'</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">}</span>

<span class="token punctuation">{</span><span class="token punctuation">{</span> Form<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">close</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">}</span>

<span class="token operator">&lt;</span><span class="token operator">/</span>div<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span><span class="token operator">/</span>body<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span><span class="token operator">/</span>html<span class="token operator">&gt;</span>
</code></pre>
<p>Copy</p>
<p>We will add the errors section above to show validation errors when we try to  <code>store()</code>  the resource.<br>
<strong>Tip:</strong>  When using  <code>{{ Form::open() }}</code>, Laravel will automatically create a hidden input field with a token to protect from cross-site request forgeries. Read more at the  <a href="http://laravel.com/docs/html#csrf-protection">Laravel docs</a>.</p>
<p>We now have the form, but we need to have it do something when it the submit button gets pressed. We set this form’s  <code>action</code>  to be a POST to  <strong><a href="http://example.com/sharks">example.com/sharks</a></strong>. The resource controller will handle this and automatically route the request to the  <code>store()</code>  method. Let’s handle that now.</p>
<h3 id="storing-a-resource-store">Storing a Resource store()</h3>
<p>Description</p>
<p>URL</p>
<p>Controller Function</p>
<p>View File</p>
<p>Process the create form submit and save the shark to the database.</p>
<p>POST <a href="http://example.com/sharks">example.com/sharks</a></p>
<p>store()</p>
<p>NONE</p>
<p>As you can see from the form action and the URL, you don’t have to pass anything extra into the URL to store a shark. Since this form is sent using the POST method, the form inputs will be the data used to store the resource.</p>
<p>To process the form, we’ll want to  <strong>validate the inputs</strong>,  <strong>send back error messages if they exist</strong>,  <strong>authenticate against the database</strong>, and  <strong>store the resource if all is good</strong>. Let’s dive in.</p>
<h4 id="controller-function-store">Controller Function store()</h4>
<p>app/controllers/sharkController.php</p>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token operator">&lt;</span>?php
<span class="token punctuation">..</span>.
    /**
        * Store a newly created resource <span class="token keyword">in</span> storage.
        *
        * @return Response
        */
    public <span class="token keyword">function</span> store<span class="token punctuation">(</span><span class="token punctuation">)</span>
    <span class="token punctuation">{</span>
        // validate
        // <span class="token function">read</span> <span class="token function">more</span> on validation at http://laravel.com/docs/validation
        <span class="token variable">$rules</span> <span class="token operator">=</span> array<span class="token punctuation">(</span>
            <span class="token string">'name'</span>       <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'required'</span>,
            <span class="token string">'email'</span>      <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'required|email'</span>,
            <span class="token string">'shark_level'</span> <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'required|numeric'</span>
        <span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token variable">$validator</span> <span class="token operator">=</span> Validator::make<span class="token punctuation">(</span>Input::all<span class="token punctuation">(</span><span class="token punctuation">)</span>, <span class="token variable">$rules</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        // process the login
        <span class="token keyword">if</span> <span class="token punctuation">(</span><span class="token variable">$validator</span>-<span class="token operator">&gt;</span>fails<span class="token punctuation">(</span><span class="token punctuation">))</span> <span class="token punctuation">{</span>
            <span class="token keyword">return</span> Redirect::to<span class="token punctuation">(</span><span class="token string">'sharks/create'</span><span class="token punctuation">)</span>
                -<span class="token operator">&gt;</span>withErrors<span class="token punctuation">(</span><span class="token variable">$validator</span><span class="token punctuation">)</span>
                -<span class="token operator">&gt;</span>withInput<span class="token punctuation">(</span>Input::except<span class="token punctuation">(</span><span class="token string">'password'</span><span class="token punctuation">))</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span> <span class="token keyword">else</span> <span class="token punctuation">{</span>
            // store
            <span class="token variable">$shark</span> <span class="token operator">=</span> new shark<span class="token punctuation">;</span>
            <span class="token variable">$shark</span>-<span class="token operator">&gt;</span>name       <span class="token operator">=</span> Input::get<span class="token punctuation">(</span><span class="token string">'name'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token variable">$shark</span>-<span class="token operator">&gt;</span>email      <span class="token operator">=</span> Input::get<span class="token punctuation">(</span><span class="token string">'email'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token variable">$shark</span>-<span class="token operator">&gt;</span>shark_level <span class="token operator">=</span> Input::get<span class="token punctuation">(</span><span class="token string">'shark_level'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token variable">$shark</span>-<span class="token operator">&gt;</span>save<span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

            // redirect
            Session::flash<span class="token punctuation">(</span><span class="token string">'message'</span>, <span class="token string">'Successfully created shark!'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token keyword">return</span> Redirect::to<span class="token punctuation">(</span><span class="token string">'sharks'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">..</span>.
</code></pre>
<p>Copy</p>
<p>If there are errors processing the form, we will redirect them back to the create form with those errors. We will add them in so the user can understand what went wrong. They will show up in the errors section we setup earlier.</p>
<p>Now you should be able to create a shark and have them show up on the main page! Navigate to  <code>example.com/sharks</code>  and there they are. All that’s left is  <strong>showing a single shark</strong>,  <strong>updating</strong>, and  <strong>deleting</strong>.</p>
<p><a href="https://cask.scotch.io/2013/10/created.png"><img src="https://cask.scotch.io/2013/10/created.png" alt="created"></a></p>
<h3 id="showing-a-resource-show">Showing a Resource show()</h3>
<p>Description</p>
<p>URL</p>
<p>Controller Function</p>
<p>View File</p>
<p>Show one of the sharks.</p>
<p>GET <a href="http://example.com/sharks/%7Bid%7D">example.com/sharks/{id}</a></p>
<p>show()</p>
<p>app/views/sharks/show.blade.php</p>
<h4 id="controller-function-show">Controller Function show()</h4>
<p>app/controllers/sharkController.php</p>
<pre class=" language-php"><code class="prism  language-php"><span class="token php language-php"><span class="token delimiter important">&lt;?php</span>

<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>

    <span class="token comment">/**
        * Display the specified resource.
        *
        * @param  int  $id
        * @return Response
        */</span>
    <span class="token keyword">public</span> <span class="token keyword">function</span> <span class="token function">show</span><span class="token punctuation">(</span><span class="token variable">$id</span><span class="token punctuation">)</span>
    <span class="token punctuation">{</span>
        <span class="token comment">// get the shark</span>
        <span class="token variable">$shark</span> <span class="token operator">=</span> shark<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">find</span><span class="token punctuation">(</span><span class="token variable">$id</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token comment">// show the view and pass the shark to it</span>
        <span class="token keyword">return</span> View<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">make</span><span class="token punctuation">(</span><span class="token string">'sharks.show'</span><span class="token punctuation">)</span>
            <span class="token operator">-</span><span class="token operator">&gt;</span><span class="token function">with</span><span class="token punctuation">(</span><span class="token string">'shark'</span><span class="token punctuation">,</span> <span class="token variable">$shark</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
</span></code></pre>
<p>Copy</p>
<h4 id="the-view-appviewssharksshow.blade.php">The View app/views/sharks/show.blade.php</h4>
<p>app/views/sharks/show.blade.php</p>
<pre class=" language-php"><code class="prism  language-php"><span class="token operator">&lt;</span><span class="token operator">!</span><span class="token constant">DOCTYPE</span> html<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span>html<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span>head<span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span>title<span class="token operator">&gt;</span>Shark App<span class="token operator">&lt;</span><span class="token operator">/</span>title<span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span>link rel<span class="token operator">=</span><span class="token string">"stylesheet"</span> href<span class="token operator">=</span><span class="token string">"//netdna.bootstrapcdn.com/bootstrap/3.0.0/css/bootstrap.min.css"</span><span class="token operator">&gt;</span>
<span class="token operator">&lt;</span><span class="token operator">/</span>head<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span>body<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span>div <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"container"</span><span class="token operator">&gt;</span>

<span class="token operator">&lt;</span>nav <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"navbar navbar-inverse"</span><span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span>div <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"navbar-header"</span><span class="token operator">&gt;</span>
        <span class="token operator">&lt;</span>a <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"navbar-brand"</span> href<span class="token operator">=</span><span class="token string">"{{ URL::to('sharks') }}"</span><span class="token operator">&gt;</span>shark Alert<span class="token operator">&lt;</span><span class="token operator">/</span>a<span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span><span class="token operator">/</span>div<span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span>ul <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"nav navbar-nav"</span><span class="token operator">&gt;</span>
        <span class="token operator">&lt;</span>li<span class="token operator">&gt;</span><span class="token operator">&lt;</span>a href<span class="token operator">=</span><span class="token string">"{{ URL::to('sharks') }}"</span><span class="token operator">&gt;</span>View All sharks<span class="token operator">&lt;</span><span class="token operator">/</span>a<span class="token operator">&gt;</span><span class="token operator">&lt;</span><span class="token operator">/</span>li<span class="token operator">&gt;</span>
        <span class="token operator">&lt;</span>li<span class="token operator">&gt;</span><span class="token operator">&lt;</span>a href<span class="token operator">=</span><span class="token string">"{{ URL::to('sharks/create') }}"</span><span class="token operator">&gt;</span>Create a shark<span class="token operator">&lt;</span><span class="token operator">/</span>a<span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span><span class="token operator">/</span>ul<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span><span class="token operator">/</span>nav<span class="token operator">&gt;</span>

<span class="token operator">&lt;</span>h1<span class="token operator">&gt;</span>Showing <span class="token punctuation">{</span><span class="token punctuation">{</span> <span class="token variable">$shark</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token property">name</span> <span class="token punctuation">}</span><span class="token punctuation">}</span><span class="token operator">&lt;</span><span class="token operator">/</span>h1<span class="token operator">&gt;</span>

    <span class="token operator">&lt;</span>div <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"jumbotron text-center"</span><span class="token operator">&gt;</span>
        <span class="token operator">&lt;</span>h2<span class="token operator">&gt;</span><span class="token punctuation">{</span><span class="token punctuation">{</span> <span class="token variable">$shark</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token property">name</span> <span class="token punctuation">}</span><span class="token punctuation">}</span><span class="token operator">&lt;</span><span class="token operator">/</span>h2<span class="token operator">&gt;</span>
        <span class="token operator">&lt;</span>p<span class="token operator">&gt;</span>
            <span class="token operator">&lt;</span>strong<span class="token operator">&gt;</span>Email<span class="token punctuation">:</span><span class="token operator">&lt;</span><span class="token operator">/</span>strong<span class="token operator">&gt;</span> <span class="token punctuation">{</span><span class="token punctuation">{</span> <span class="token variable">$shark</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token property">email</span> <span class="token punctuation">}</span><span class="token punctuation">}</span><span class="token operator">&lt;</span>br<span class="token operator">&gt;</span>
            <span class="token operator">&lt;</span>strong<span class="token operator">&gt;</span>Level<span class="token punctuation">:</span><span class="token operator">&lt;</span><span class="token operator">/</span>strong<span class="token operator">&gt;</span> <span class="token punctuation">{</span><span class="token punctuation">{</span> <span class="token variable">$shark</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token property">shark_level</span> <span class="token punctuation">}</span><span class="token punctuation">}</span>
        <span class="token operator">&lt;</span><span class="token operator">/</span>p<span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span><span class="token operator">/</span>div<span class="token operator">&gt;</span>

<span class="token operator">&lt;</span><span class="token operator">/</span>div<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span><span class="token operator">/</span>body<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span><span class="token operator">/</span>html<span class="token operator">&gt;</span>
</code></pre>
<p>Copy</p>
<p><a href="https://cask.scotch.io/2013/10/show.png"><img src="https://cask.scotch.io/2013/10/show.png" alt="show"></a></p>
<h3 id="editing-a-resource-edit">Editing a Resource edit()</h3>
<p>Description</p>
<p>URL</p>
<p>Controller Function</p>
<p>View File</p>
<p>Pull a shark from the database and allow editing.</p>
<p>GET <a href="http://example.com/sharks/%7Bid%7D/edit">example.com/sharks/{id}/edit</a></p>
<p>edit()</p>
<p>app/views/sharks/edit.blade.php</p>
<p>To edit a shark, we need to pull them from the database, show the creation form, but populate it with the selected shark’s info. To make life easier, we will use  <a href="http://laravel.com/docs/html#form-model-binding">form model binding</a>. This allows us to pull info from a model and bind it to the input fields in a form. Just makes it easier to populate our edit form and you can imagine that when these forms start getting rather large this will make life much easier.</p>
<h4 id="controller-function-edit">Controller Function edit()</h4>
<p>app/controllers/sharkController.php</p>
<pre class=" language-php"><code class="prism  language-php"><span class="token php language-php"><span class="token delimiter important">&lt;?php</span>

<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>

    <span class="token comment">/**
        * Show the form for editing the specified resource.
        *
        * @param  int  $id
        * @return Response
        */</span>
    <span class="token keyword">public</span> <span class="token keyword">function</span> <span class="token function">edit</span><span class="token punctuation">(</span><span class="token variable">$id</span><span class="token punctuation">)</span>
    <span class="token punctuation">{</span>
        <span class="token comment">// get the shark</span>
        <span class="token variable">$shark</span> <span class="token operator">=</span> shark<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">find</span><span class="token punctuation">(</span><span class="token variable">$id</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token comment">// show the edit form and pass the shark</span>
        <span class="token keyword">return</span> View<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">make</span><span class="token punctuation">(</span><span class="token string">'sharks.edit'</span><span class="token punctuation">)</span>
            <span class="token operator">-</span><span class="token operator">&gt;</span><span class="token function">with</span><span class="token punctuation">(</span><span class="token string">'shark'</span><span class="token punctuation">,</span> <span class="token variable">$shark</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
</span></code></pre>
<p>Copy</p>
<h4 id="the-view-appviewssharksedit.blade.php">The View app/views/sharks/edit.blade.php</h4>
<p>app/views/sharks/edit.blade.php</p>
<pre class=" language-php"><code class="prism  language-php"><span class="token operator">&lt;</span><span class="token operator">!</span><span class="token constant">DOCTYPE</span> html<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span>html<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span>head<span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span>title<span class="token operator">&gt;</span>Shark App<span class="token operator">&lt;</span><span class="token operator">/</span>title<span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span>link rel<span class="token operator">=</span><span class="token string">"stylesheet"</span> href<span class="token operator">=</span><span class="token string">"//netdna.bootstrapcdn.com/bootstrap/3.0.0/css/bootstrap.min.css"</span><span class="token operator">&gt;</span>
<span class="token operator">&lt;</span><span class="token operator">/</span>head<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span>body<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span>div <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"container"</span><span class="token operator">&gt;</span>

<span class="token operator">&lt;</span>nav <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"navbar navbar-inverse"</span><span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span>div <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"navbar-header"</span><span class="token operator">&gt;</span>
        <span class="token operator">&lt;</span>a <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"navbar-brand"</span> href<span class="token operator">=</span><span class="token string">"{{ URL::to('sharks') }}"</span><span class="token operator">&gt;</span>shark Alert<span class="token operator">&lt;</span><span class="token operator">/</span>a<span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span><span class="token operator">/</span>div<span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span>ul <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"nav navbar-nav"</span><span class="token operator">&gt;</span>
        <span class="token operator">&lt;</span>li<span class="token operator">&gt;</span><span class="token operator">&lt;</span>a href<span class="token operator">=</span><span class="token string">"{{ URL::to('sharks') }}"</span><span class="token operator">&gt;</span>View All sharks<span class="token operator">&lt;</span><span class="token operator">/</span>a<span class="token operator">&gt;</span><span class="token operator">&lt;</span><span class="token operator">/</span>li<span class="token operator">&gt;</span>
        <span class="token operator">&lt;</span>li<span class="token operator">&gt;</span><span class="token operator">&lt;</span>a href<span class="token operator">=</span><span class="token string">"{{ URL::to('sharks/create') }}"</span><span class="token operator">&gt;</span>Create a shark<span class="token operator">&lt;</span><span class="token operator">/</span>a<span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span><span class="token operator">/</span>ul<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span><span class="token operator">/</span>nav<span class="token operator">&gt;</span>

<span class="token operator">&lt;</span>h1<span class="token operator">&gt;</span>Edit <span class="token punctuation">{</span><span class="token punctuation">{</span> <span class="token variable">$shark</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token property">name</span> <span class="token punctuation">}</span><span class="token punctuation">}</span><span class="token operator">&lt;</span><span class="token operator">/</span>h1<span class="token operator">&gt;</span>

<span class="token operator">&lt;</span><span class="token operator">!</span><span class="token operator">--</span> <span class="token keyword">if</span> there are creation errors<span class="token punctuation">,</span> they will show here <span class="token operator">--</span><span class="token operator">&gt;</span>
<span class="token punctuation">{</span><span class="token punctuation">{</span> <span class="token constant">HTML</span><span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">ul</span><span class="token punctuation">(</span><span class="token variable">$errors</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token function">all</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">}</span>

<span class="token punctuation">{</span><span class="token punctuation">{</span> Form<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">model</span><span class="token punctuation">(</span><span class="token variable">$shark</span><span class="token punctuation">,</span> <span class="token keyword">array</span><span class="token punctuation">(</span><span class="token string">'route'</span> <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token keyword">array</span><span class="token punctuation">(</span><span class="token string">'sharks.update'</span><span class="token punctuation">,</span> <span class="token variable">$shark</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token property">id</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token string">'method'</span> <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'PUT'</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">}</span>

    <span class="token operator">&lt;</span>div <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"form-group"</span><span class="token operator">&gt;</span>
        <span class="token punctuation">{</span><span class="token punctuation">{</span> Form<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">label</span><span class="token punctuation">(</span><span class="token string">'name'</span><span class="token punctuation">,</span> <span class="token string">'Name'</span><span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">}</span>
        <span class="token punctuation">{</span><span class="token punctuation">{</span> Form<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">text</span><span class="token punctuation">(</span><span class="token string">'name'</span><span class="token punctuation">,</span> <span class="token keyword">null</span><span class="token punctuation">,</span> <span class="token keyword">array</span><span class="token punctuation">(</span><span class="token string">'class'</span> <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'form-control'</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">}</span>
    <span class="token operator">&lt;</span><span class="token operator">/</span>div<span class="token operator">&gt;</span>

    <span class="token operator">&lt;</span>div <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"form-group"</span><span class="token operator">&gt;</span>
        <span class="token punctuation">{</span><span class="token punctuation">{</span> Form<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">label</span><span class="token punctuation">(</span><span class="token string">'email'</span><span class="token punctuation">,</span> <span class="token string">'Email'</span><span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">}</span>
        <span class="token punctuation">{</span><span class="token punctuation">{</span> Form<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">email</span><span class="token punctuation">(</span><span class="token string">'email'</span><span class="token punctuation">,</span> <span class="token keyword">null</span><span class="token punctuation">,</span> <span class="token keyword">array</span><span class="token punctuation">(</span><span class="token string">'class'</span> <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'form-control'</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">}</span>
    <span class="token operator">&lt;</span><span class="token operator">/</span>div<span class="token operator">&gt;</span>

    <span class="token operator">&lt;</span>div <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"form-group"</span><span class="token operator">&gt;</span>
        <span class="token punctuation">{</span><span class="token punctuation">{</span> Form<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">label</span><span class="token punctuation">(</span><span class="token string">'shark_level'</span><span class="token punctuation">,</span> <span class="token string">'shark Level'</span><span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">}</span>
        <span class="token punctuation">{</span><span class="token punctuation">{</span> Form<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">select</span><span class="token punctuation">(</span><span class="token string">'shark_level'</span><span class="token punctuation">,</span> <span class="token keyword">array</span><span class="token punctuation">(</span><span class="token string">'0'</span> <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'Select a Level'</span><span class="token punctuation">,</span> <span class="token string">'1'</span> <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'Sees Sunlight'</span><span class="token punctuation">,</span> <span class="token string">'2'</span> <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'Foosball Fanatic'</span><span class="token punctuation">,</span> <span class="token string">'3'</span> <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'Basement Dweller'</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token keyword">null</span><span class="token punctuation">,</span> <span class="token keyword">array</span><span class="token punctuation">(</span><span class="token string">'class'</span> <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'form-control'</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">}</span>
    <span class="token operator">&lt;</span><span class="token operator">/</span>div<span class="token operator">&gt;</span>

    <span class="token punctuation">{</span><span class="token punctuation">{</span> Form<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">submit</span><span class="token punctuation">(</span><span class="token string">'Edit the shark!'</span><span class="token punctuation">,</span> <span class="token keyword">array</span><span class="token punctuation">(</span><span class="token string">'class'</span> <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'btn btn-primary'</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">}</span>

<span class="token punctuation">{</span><span class="token punctuation">{</span> Form<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">close</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">}</span>

<span class="token operator">&lt;</span><span class="token operator">/</span>div<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span><span class="token operator">/</span>body<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span><span class="token operator">/</span>html<span class="token operator">&gt;</span>
</code></pre>
<p>Copy</p>
<p>Note that we have to pass a method of  <code>PUT</code>  so that Laravel knows how to route to the controller correctly.</p>
<h3 id="updating-a-resource-update">Updating a Resource update()</h3>
<p>Description</p>
<p>URL</p>
<p>Controller Function</p>
<p>View File</p>
<p>Process the create form submit and save the shark to the database.</p>
<p>PUT <a href="http://example.com/sharks">example.com/sharks</a></p>
<p>update()</p>
<p>NONE</p>
<p>This controller method will process the edit form. It is very similar to  <code>store()</code>. We will  <strong>validate</strong>,  <strong>update</strong>, and  <strong>redirect</strong>.</p>
<h4 id="controller-function-update">Controller Function update()</h4>
<p>app/controllers/sharkController.php</p>
<pre class=" language-php"><code class="prism  language-php"><span class="token php language-php"><span class="token delimiter important">&lt;?php</span>

<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>

    <span class="token comment">/**
        * Update the specified resource in storage.
        *
        * @param  int  $id
        * @return Response
        */</span>
    <span class="token keyword">public</span> <span class="token keyword">function</span> <span class="token function">update</span><span class="token punctuation">(</span><span class="token variable">$id</span><span class="token punctuation">)</span>
    <span class="token punctuation">{</span>
        <span class="token comment">// validate</span>
        <span class="token comment">// read more on validation at http://laravel.com/docs/validation</span>
        <span class="token variable">$rules</span> <span class="token operator">=</span> <span class="token keyword">array</span><span class="token punctuation">(</span>
            <span class="token string">'name'</span>       <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'required'</span><span class="token punctuation">,</span>
            <span class="token string">'email'</span>      <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'required|email'</span><span class="token punctuation">,</span>
            <span class="token string">'shark_level'</span> <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'required|numeric'</span>
        <span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token variable">$validator</span> <span class="token operator">=</span> Validator<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">make</span><span class="token punctuation">(</span>Input<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">all</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token variable">$rules</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token comment">// process the login</span>
        <span class="token keyword">if</span> <span class="token punctuation">(</span><span class="token variable">$validator</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token function">fails</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">return</span> Redirect<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">to</span><span class="token punctuation">(</span><span class="token string">'sharks/'</span> <span class="token punctuation">.</span> <span class="token variable">$id</span> <span class="token punctuation">.</span> <span class="token string">'/edit'</span><span class="token punctuation">)</span>
                <span class="token operator">-</span><span class="token operator">&gt;</span><span class="token function">withErrors</span><span class="token punctuation">(</span><span class="token variable">$validator</span><span class="token punctuation">)</span>
                <span class="token operator">-</span><span class="token operator">&gt;</span><span class="token function">withInput</span><span class="token punctuation">(</span>Input<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">except</span><span class="token punctuation">(</span><span class="token string">'password'</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span> <span class="token keyword">else</span> <span class="token punctuation">{</span>
            <span class="token comment">// store</span>
            <span class="token variable">$shark</span> <span class="token operator">=</span> shark<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">find</span><span class="token punctuation">(</span><span class="token variable">$id</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token variable">$shark</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token property">name</span>       <span class="token operator">=</span> Input<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token string">'name'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token variable">$shark</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token property">email</span>      <span class="token operator">=</span> Input<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token string">'email'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token variable">$shark</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token property">shark_level</span> <span class="token operator">=</span> Input<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token string">'shark_level'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token variable">$shark</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token function">save</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

            <span class="token comment">// redirect</span>
            Session<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">flash</span><span class="token punctuation">(</span><span class="token string">'message'</span><span class="token punctuation">,</span> <span class="token string">'Successfully updated shark!'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token keyword">return</span> Redirect<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">to</span><span class="token punctuation">(</span><span class="token string">'sharks'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
</span></code></pre>
<p>Copy</p>
<h3 id="deleting-a-resource-destroy">Deleting a Resource destroy()</h3>
<p>Description</p>
<p>URL</p>
<p>Controller Function</p>
<p>View File</p>
<p>Process the create form submit and save the shark to the database.</p>
<p>DELETE <a href="http://example.com/sharks/%7Bid%7D">example.com/sharks/{id}</a></p>
<p>destroy()</p>
<p>NONE</p>
<p>The workflow for this is that a user would go to view all the sharks, see a delete button, click it to delete. Since we never created a delete button in our  <code>app/views/sharks/index.blade.php</code>, we will create that now. We will also add a notification section to show a success message.</p>
<p>We have to send the request to our application using the  <strong>DELETE</strong>  HTTP verb, so we will create a form to do that since a button won’t do.</p>
<p><strong>Alert:</strong>  The DELETE HTTP verb is used when accessing the  <code>sharks.destroy</code>  route. Since you can’t just create a button or form with the  <strong>method</strong>  DELETE, we will have to spoof it by creating a hidden input field in our delete form.</p>
<h4 id="the-view-appviewssharksindex.blade.php-1">The View app/views/sharks/index.blade.php</h4>
<p>app/views/sharks/index.blade.php</p>
<pre class=" language-php"><code class="prism  language-php"><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>

    @<span class="token keyword">foreach</span><span class="token punctuation">(</span><span class="token variable">$sharks</span> <span class="token keyword">as</span> <span class="token variable">$key</span> <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token variable">$value</span><span class="token punctuation">)</span>
        <span class="token operator">&lt;</span>tr<span class="token operator">&gt;</span>
            <span class="token operator">&lt;</span>td<span class="token operator">&gt;</span><span class="token punctuation">{</span><span class="token punctuation">{</span> <span class="token variable">$value</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token property">id</span> <span class="token punctuation">}</span><span class="token punctuation">}</span><span class="token operator">&lt;</span><span class="token operator">/</span>td<span class="token operator">&gt;</span>
            <span class="token operator">&lt;</span>td<span class="token operator">&gt;</span><span class="token punctuation">{</span><span class="token punctuation">{</span> <span class="token variable">$value</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token property">name</span> <span class="token punctuation">}</span><span class="token punctuation">}</span><span class="token operator">&lt;</span><span class="token operator">/</span>td<span class="token operator">&gt;</span>
            <span class="token operator">&lt;</span>td<span class="token operator">&gt;</span><span class="token punctuation">{</span><span class="token punctuation">{</span> <span class="token variable">$value</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token property">email</span> <span class="token punctuation">}</span><span class="token punctuation">}</span><span class="token operator">&lt;</span><span class="token operator">/</span>td<span class="token operator">&gt;</span>
            <span class="token operator">&lt;</span>td<span class="token operator">&gt;</span><span class="token punctuation">{</span><span class="token punctuation">{</span> <span class="token variable">$value</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token property">shark_level</span> <span class="token punctuation">}</span><span class="token punctuation">}</span><span class="token operator">&lt;</span><span class="token operator">/</span>td<span class="token operator">&gt;</span>

            <span class="token operator">&lt;</span><span class="token operator">!</span><span class="token operator">--</span> we will also add show<span class="token punctuation">,</span> edit<span class="token punctuation">,</span> <span class="token keyword">and</span> delete buttons <span class="token operator">--</span><span class="token operator">&gt;</span>
            <span class="token operator">&lt;</span>td<span class="token operator">&gt;</span>

                <span class="token operator">&lt;</span><span class="token operator">!</span><span class="token operator">--</span> delete the <span class="token function">shark</span> <span class="token punctuation">(</span>uses the destroy method <span class="token constant">DESTROY</span> <span class="token operator">/</span>sharks<span class="token operator">/</span><span class="token punctuation">{</span>id<span class="token punctuation">}</span> <span class="token operator">--</span><span class="token operator">&gt;</span>
                <span class="token operator">&lt;</span><span class="token operator">!</span><span class="token operator">--</span> we will add this later since its a little more complicated than the other two buttons <span class="token operator">--</span><span class="token operator">&gt;</span>
                <span class="token punctuation">{</span><span class="token punctuation">{</span> Form<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">open</span><span class="token punctuation">(</span><span class="token keyword">array</span><span class="token punctuation">(</span><span class="token string">'url'</span> <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'sharks/'</span> <span class="token punctuation">.</span> <span class="token variable">$value</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token property">id</span><span class="token punctuation">,</span> <span class="token string">'class'</span> <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'pull-right'</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">}</span>
                    <span class="token punctuation">{</span><span class="token punctuation">{</span> Form<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">hidden</span><span class="token punctuation">(</span><span class="token string">'_method'</span><span class="token punctuation">,</span> <span class="token string">'DELETE'</span><span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">}</span>
                    <span class="token punctuation">{</span><span class="token punctuation">{</span> Form<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">submit</span><span class="token punctuation">(</span><span class="token string">'Delete this shark'</span><span class="token punctuation">,</span> <span class="token keyword">array</span><span class="token punctuation">(</span><span class="token string">'class'</span> <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'btn btn-warning'</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">}</span>
                <span class="token punctuation">{</span><span class="token punctuation">{</span> Form<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">close</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">}</span>

                <span class="token operator">&lt;</span><span class="token operator">!</span><span class="token operator">--</span> show the <span class="token function">shark</span> <span class="token punctuation">(</span>uses the show method found at <span class="token constant">GET</span> <span class="token operator">/</span>sharks<span class="token operator">/</span><span class="token punctuation">{</span>id<span class="token punctuation">}</span> <span class="token operator">--</span><span class="token operator">&gt;</span>
                <span class="token operator">&lt;</span>a <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"btn btn-small btn-success"</span> href<span class="token operator">=</span><span class="token string">"{{ URL::to('sharks/' . $value-&gt;id) }}"</span><span class="token operator">&gt;</span>Show this shark<span class="token operator">&lt;</span><span class="token operator">/</span>a<span class="token operator">&gt;</span>

                <span class="token operator">&lt;</span><span class="token operator">!</span><span class="token operator">--</span> edit this <span class="token function">shark</span> <span class="token punctuation">(</span>uses the edit method found at <span class="token constant">GET</span> <span class="token operator">/</span>sharks<span class="token operator">/</span><span class="token punctuation">{</span>id<span class="token punctuation">}</span><span class="token operator">/</span>edit <span class="token operator">--</span><span class="token operator">&gt;</span>
                <span class="token operator">&lt;</span>a <span class="token keyword">class</span><span class="token operator">=</span><span class="token string">"btn btn-small btn-info"</span> href<span class="token operator">=</span><span class="token string">"{{ URL::to('sharks/' . $value-&gt;id . '/edit') }}"</span><span class="token operator">&gt;</span>Edit this shark<span class="token operator">&lt;</span><span class="token operator">/</span>a<span class="token operator">&gt;</span>

            <span class="token operator">&lt;</span><span class="token operator">/</span>td<span class="token operator">&gt;</span>
        <span class="token operator">&lt;</span><span class="token operator">/</span>tr<span class="token operator">&gt;</span>
    @<span class="token keyword">endforeach</span>
    <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
</code></pre>
<p>Copy</p>
<p>Now when we click that form submit button, Laravel will use the sharks.destroy route and we can process that in our controller.</p>
<h4 id="controller-function-destroy">Controller Function destroy()</h4>
<p>app/controllers/sharkController.php</p>
<pre class=" language-php"><code class="prism  language-php"><span class="token php language-php"><span class="token delimiter important">&lt;?php</span>

<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>

    <span class="token comment">/**
        * Remove the specified resource from storage.
        *
        * @param  int  $id
        * @return Response
        */</span>
    <span class="token keyword">public</span> <span class="token keyword">function</span> <span class="token function">destroy</span><span class="token punctuation">(</span><span class="token variable">$id</span><span class="token punctuation">)</span>
    <span class="token punctuation">{</span>
        <span class="token comment">// delete</span>
        <span class="token variable">$shark</span> <span class="token operator">=</span> shark<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">find</span><span class="token punctuation">(</span><span class="token variable">$id</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token variable">$shark</span><span class="token operator">-</span><span class="token operator">&gt;</span><span class="token function">delete</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token comment">// redirect</span>
        Session<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">flash</span><span class="token punctuation">(</span><span class="token string">'message'</span><span class="token punctuation">,</span> <span class="token string">'Successfully deleted the shark!'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">return</span> Redirect<span class="token punctuation">:</span><span class="token punctuation">:</span><span class="token function">to</span><span class="token punctuation">(</span><span class="token string">'sharks'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
</span></code></pre>
<p>Copy</p>
<h2 id="conclusion">Conclusion</h2>
<p>That’s everything! Hopefully we covered enough so that you can understand how resource controllers can be used in all sorts of scenarios. Just create the controller, create the single line in the routes file, and you have the foundation for doing CRUD.</p>
<p>As always, let us know if you have any questions or comments. We’ll be expanding more on Laravel in the coming articles so if there’s anything specific, throw it in the comments or email us.</p>
<p><strong>Further Reading</strong>: For more Laravel, check out our  <a href="https://www.digitalocean.com/series/simple-laravel">Simple Laravel Series</a>.</p>

