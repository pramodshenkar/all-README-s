Laravel 8  
-----------

IMP commands:
create:
laravel new proj1

add ui package:
composer require laravel/ui --dev

add bootstrap with/without Auth
php artisan ui bootstrap --auth OR
php artisan ui bootstrap

install dependancies by auth & bootstrap.
npm install && npm run dev

---------
1. Route
--------
Methods :
    i. Route::view('url','view-name');
    2. Route::get('url',function(){
          return view('view name');
        });
    3. Route::get('url',[controller-name::class,'function-name']);


    - with Params:
        Route::get('user/{id}',function($id){
            echo "Hi".$id;
        })
    
    - with optinal Params:
        Route::get('user/{id?}',function($id = null){
            echo 'Hello'.id
        })

    - Now If we want user MUST not type number as prameter.
        ie  localhost:8000/user/prmd1998  --> Inalid
            localhost:8000/user/prmd      --> Valid Only.

        then use where condition. like
         Route::get('user/{name}',function($name){
                echo 'Hello'.id
            })->where('name','[a-zA-Z]+');            //or [0-9]+ for id

    - Suppose we have too many routes with id & name
      its not efficient to apply where on each route.
      we need only once declaration then
      --> 
        goto App/Providers/RouteServicesProvider.php
        in boot() function at last we have to add our condition as
        
        Route::pattern('name','[a-zA-Z]+')
        Route::pattern('id','[0-9]+')

        Now for all routes only alfabetic name & digit id will be allowed;

    - Http verbs : get, post 

    - we can use same route for get & post.
      How?--> use match('[http-verbs]','url','function')

      eg : http:match(['get','post'],'user',function(){});
      to check this use postman API.

    - for many http verbs use route with any
      eg : any('/user',function(){});

    NOTE : we can create route in web.php as well as api.php
            for route in api.php we use api keyword.
            eg : localhost:8000/api/home/10.


Controller :
------------
    - cmd : php artisan make:controller HomeController
    - Route syntax for calling metod in controller.

        Route::get('home',[HomeController::class,'index']);

    - How to pass params in controller.
        Route::get('user/{id}'[HomeController::class,'index']);

        class HomeController
        {
            function index($id)
            {
                echo '$id';
            }
        }


Resource controller : 
---------------------
    - controller with index create show update delete method by default.
    - cmd : php artisam make:controller --resource postController
    - route for resource controller : 
            Route::resource('post',PostsController::class);
    - to see all route created by above line : php artisan route:list


View
-----
    - call view from method of controller
        ie return view('home');

    - send variable from controller->view
        ie return view('home'['varNameOnView'=>'value'])

        on template :  {{ $varNameOnView }}

        same as we can pass multiple variable

    - send multiple var on template.
        Use compact(var1,var2,var3) method. It will create array of passed variables.

         ie $name = 'pramod'
            $mailid = 'shenkar'

            return view('home',compact('name','mailid'))




5. Blade template layout :
   ------------------------
    we can include 1 page into another page.
    eg we have to show contact page in about us page. 
    syntax: 
        include('contact')    <--write this in aboutus.blade.html

    we can create fix layout for website. For this we have to use master template.
    How to create it.

    add new folder 'layouts' in view folder. inside it create master.blade.php 
    we will define our layout in this master file.

    in master.blade.php  -->> yeild('title')  :   it will act as defination it will take param title. this will provided in other blade file
    in others.blade.php  -->> section('title','HomePage') : it will act as calling. & replace master's variable to HomePage.
                         -->> section('content') HELLO @endsection : view inside section will get loaded in place of yeild('content').

    Wait. Before using blade in other template we have to extends out master.blade.php
    using extends('layouts.master')   where layout = folder & master = blade file.

    master.blade.php
    <html>
        <head>
            <title>@yield('title')</title>
        </head>
        <body>
            @yield('content')
        </body>
    </html>

    home.blade.php
    @extends('layouts.master')
    @section('title','Home')          
    @section('section')
        <h3>WELCOME to Home</h3>
    @endsection

5.Blade Component : 
-------------------
2 approch :
    1. class based Component    
        cmd: php artisan make:component ComponentName
        
        it will create 2 files.
        i.  App/view/ComponentName.php     //class file
            this class will contain function render() which return component's view
        
        ii. App/resources/view/component/ComponentName.blade.php
            this is view file.

        we have to add this component in our view use tag <x-ComponentName />

    - send data to component:
      we just have to pass variable & value in call of component
      eg <x-ComponentName var1 = "Pramod" : var2 = "Shenkar" />   This value must be in "" not in ''.

      Note : here we can assign variable instead of value to parameter.
      eg
      <div>
        @php
          $surname = "shenkar";
        @endphp
        <x-ComponentName, var1 = "Pramod" : var2 = "{{ $surname }}" />
      </div>

      This is only passing name;
      we have to catch thsesvariables from constructor of ComponentName.php class in local variables & then we can aceess it on ComponentName.blade.php.

      public $name;
      public $surname;
      constructor($var1,$var2)       //this var1 name must be same as var1 name passed in <x-ComponentName,var1='pramod'>
      {
          $name = $var1;
          $surname = $var2;
      }

      we can access this $name variable on template by {{ $name }} {{ $surname }}


migration : 
-------------
        - create migration using : php artisan make:migration migration_name_here
        - this will create one migration file in folder app\database\migration.
        - this class will have 2 function 
            ie up() & down()
               up : create table.
               down : drop table.
        - run migration using : php artisan migrate

    Commands :
        create : php artisan make:migration create_tablename_table
         
          NOTE : migration name MUST be in format create_tablename_table 
                 eg create_users_table.
                 if name is not in this format it will create empty up() function
                 if name in in this format then it will create up() function with users table.
                 eg :
                        public function up()
                        {
                            Schema::create('employees', function (Blueprint $table) {
                                $table->id();
                                $table->timestamps();
                            });
                        }

                 we can add multiple fields as : $table->string('column-name')
                 eg :
                        $table->string('username')
                        $table->string('password')


           run : php artisan migrate
         reset : php artisan migrate:reset      (delete all table)
      rollback : php artisan migrate:rollback   (delete all table created in last migration)
                 php artisan migrate:rollback --step 3  (delete last 3 migration table)

       refresh : php artisan migrate refresh (delete tables by all migration and again recreate it)
  
  only one mgn : php artisan migrate --path=/path/migration_name.php
            eg : php artisan migrate --path=/database/migrations/2020_10_01_062313_create_sample1_table.php



6.Httpclient
------------
    - these are get post put delete method.
    - before doing this operations we need one API with records.
        we will create fake json-api using command : json-server --watch db.json

    - Now supose link to that API is ie ApiURL : http://localhost:3000/users.

    - We will write function in controller & call these methods in it. 

    - GET :
        As get method returns data we need variable to store it. 
        we have to pass ApiURL as parameter.
        we can use GET by two ways ie getAllRecords or getOneRecord

        getAllRecords()
        {
            $responce = HTTP::get('http://localhost:3000/users');
            return $responce->json();
        }

        to get specific record we need id to pass in url
        eg http://localhost:8000/get/3;

        so we create route as: Route::get('/get/{id}',[RecordsControlle::class,'getOneRecord']);

        getOneRecord($id)
        {
            $responce = HTTP::get('http://localhost:3000/users/'.$id);
            return $responce->json();
        }

    - POST : 
        takes 2 params ie ApiURL & array of data which have to post.
        here we need variable to create that array.

        AddRecord()
        {
            $record = ["id"="4","name"="Pramod"];
            HTTP::post('http://localhost:3000/user',$record);
            return getAllData();  //to see record added or not.
        }

    - PUT :
        take id from user from route & pass ApiURL with id.
        2nd param is updation in record.

        function updateRecord($id)
        {
            $record = ["id"=>"4","name"=>"Tushar"];

            Http::put('localhost:3000/users/'.$id,$record);
            return $this->getRecords();
        }

    - Delete :
        takes only 1 param ie ApiURL with id which haveto delete.

        function deleteRecord($id)
        {
            Http::delete('localhost:3000/users/'.$id);
            return $this->getRecords();
        }

Fluent String :
----------------
     Fluent strings are just processed string by doing operation on it.

     for this we have to import namespace : use Illuminate\Support\Str;

     following are operations :

         $str = "Hello. I am Prmod.";

         $var1 = Str::of($str)->upper();

         $var1 = Str::of($str)->lower();

         $var1 = Str::of($str)->after('Hello');

         $var1 = Str::of($str)->replace('Hello','Hi');

         $var1 = Str::of($str)->append('And I am Engineer.');

         $var1 = Str::of($str)->trim('H');     //it trims only single letter

         $var1 = Str::of($str)->substr(13,3);  //its trims 3 letters from 13th place.

         $var1 = Str::of($str)->slug('@');

         $var1 = Str::of($str)->upper();


         Outputs :

            Hello. I am Prmod.

            upper() : HELLO. I AM PRMOD.

            lower() : hello. i am prmod.

            after('Hello') : . I am Prmod.

            replace('Hello','Hi') : Hi. I am Prmod.

            append('And I am Engineer.') : Hello. I am Prmod.And I am Engineer.

            trim('H') : ello. I am Prmod.

            substring(13,3) : rmo

            slug('@') : hello@i@am@prmod

            upper() : HELLO. I AM PRMOD.


Stub Customization :
--------------------
    when we create anythhing ie controller, Component etc. its structure is predefind.
    & we have to code within it as our requirement.

    But supoose we need 1000 controller. which having method Showdata() 
    then is not efficient to everytime add Showdata() after creating controller.
    So we need something which directly create controller which already have Showdata() function.

    for this we uses stub Customization.
    steps :
        1.create stub using command : php artisan stub:publish
        Now stub folder get created which having multiple components.
        
        2.Suppose we wants that controller get created must have Showdata() method.
        then open controller.plain.stub
        add method it its.

        3.All Done. Now create controller & check it. It will already have Showdata method. 


Forms :
----------

    Steps : 
        1. Create a form & route for it.

        2. Create post route.
            - use this route in form for submit
                eg : <form action='post-route' method='post'></form>
            - call function from controller when this route get calling

            if it shows page expires then use @csrf inside form.

        when form is submitted then post request is send as well as controller method get called.
        We can check this request in controller function by passing request object as parameter to function.


Middleware
-----------
    - We can filter http request by checking user info.
        ie Is user signed-in, Is user 18+ etc.
        And on this filtering we decide access for our web pages.
        This all done using middleware.

        Normally we can do it without middleware.
        but for this we have to write checking code on each page.
        To avoid this we uses middleware.

    - command : php artisan make:middleware checkAge   //checkAge is middleware name.
        it will create checkAge.php in App/http/middleware
        in which class checkAge contain handle() function.
        This function have Request $request as parameter.

        we can check age using this request variable
        eg

        handle(Request $request)
        {
            if($request->age && $request->age<18)
            {
                return redirect('login');
            }
            return $next($request);
        }  

    Now middleware is created.
    but we can't use it until we register middleware in kernel.php .

    - Go to kernel.php in middleware.
      there are 3 arrays :
       1. $middleware.
       2. $middlewareGroups
       3. $routeMiddleware

       we have to add path of our middleware in 1st array ie $middleware.
       \App\Http\Middleware\checkAge::class,

    Thats All.!!Middleware is Applied on all webpages.

    - 3 Types of middleware: 
       as we saw 3 arrays in kernel.php these are types of middleware.
        i.  Global   : Applied to all webpages
        ii. Grouped  : Applied to some webpages as per our need.
        iii.Individual : Apply only on single webpage.

        1.Global middleware : As we seen

        2.Grouped middleware : 
            i.  Goto $middlewareGroups array.
            ii. Create a new group & give it name. 
            iii.Add middleware path in it.

            eg : 
            $middlewareGroups = [
                'web'=>[...], //predefind
                'api'=>[...], //predefind
                'ageMiddlewareGroup'=>[
                    \App\Http\Middleware\checkAge::class,
                ]
            ]    

            Now middleware is registerd.
            Now Suppose we want to apply this middleware only on views user home & about.
            then create group as follows & pass corrospoding routes in its function.

            eg 
                Route::group();
                it takes 2 params :
                    1. array of Key:value pair 
                       ie what we want to apply on group & array of its value. 
                       ie ['middleware' => ['ageMiddleware']]
                    2. function which having routes of group.

                Route::group(['middleware'=>['ageMiddlewareGroup']],function(){
                    Route::view('user','user'),
                    Route::view('home','home'),
                    Route::view('about','about'),
                })

        Now Our checkAge middleware get applied on these 3 webpages only.

    3.Route Middleware : 
         - Applied only on sigle route : 
         - How to register in kernel?
           -->> 'ageMiddleware' => \App\Http\Middleware\checkAge::class,

         - How to apply on Route?
           -->> Route::post(...)->middleware('ageMiddleware');


Database : 
-----------

    - Config: 
        - open .env file & put db info.
            ie. DB_PORT=3306
                DB_DATABASE=ics_proj6
                DB_USERNAME=root
                DB_PASSWORD=root123
    
    - Now we can do DB operations by 2 ways
        i.  QueryBuilder (DB)
        ii. ORM 

    - Note : If it says Access denied to DB 
        then try following solutions :
        1. check config in .env
           check db is created or not.

        2. clear cache using command :
            php artisan config:cache
            php artisan config:clear
        
        3. If not working by above command then use following :
            php artisan route:cache
            php artisan route:clear
            php artisan config:cache
            php artisan config:clear
            php artisan optimize


    1.QueryBuilder : 
   ----------------
        - namespace : Illuminate\Support\Facades\DB;

        - syntax : DB::table('tbl-name')->method();

        - operations as follows : 
            
            DB::table('users')->insert([
                'name' => 'Pramod',
                'age' => '22';
            ]);
            OR we can insert data from form which is posted by Request $req
            eg :

            index(Request $req)
            {
                DB::table('users')->insert([
                    'name' => $req->name,
                    'age' => $req->age;
                ]);
            }

            return DB::table('users')->get();
            OR we can store this data by get() in variable & pass that variable on view.

            we can use where, cout , min , max;
                DB::table('users')
            ->where('name','pramod')
            ->get();
            
            DB::table('users')
            ->where('name','Akash')
            ->update([
                    'name' => $req->name,
                    'age' => $req->age;
                ]);

            DB::table('users')
            ->where('name','Pramod')
            ->delete();


    2. ORM :
------------
    - Same DB operation can done using ORM.
    - But instead of directly using database by DB we will use model.
    - we will create model. This model represnt a table. 
      we will use this model in controller & then do DB operations.

      command : php artisan make:model user  & create table 
                php artisan make:model user -m  this will create automatically by migration. 

      suppose 'user' is our model.
      then add its namespace in controller as: use App\Http\Models\user;

    - operations:

     1. Insert : 
        we have to create instance of model. 
        then assign variables to it
        then save data using save() method.
        eg  $admin = new admin;
            $admin->name = $req->name;
            $admin->save(); 

      2. Delete : 
        fetch record using id in variable & then perform delete on that variable.
        eg  $var = admin::find(2);
            $var->delete();

      3. Update
        fetch record using id in variable & then assign value variable to variable and save using save() method.
        eg  $var = admin::find(2);
            $var->name = 'Pramod';
            $var->save()

      4.Show()
        fetch records using all() method.    

        Note : IF error as 'column not found' 
        then open model ie admin.php & add there : public $timestamp = false

        Eg : 

        function index(Request $req)
        {
            #INSERT
            $admin = new admin;
            $admin->name = $req->name;
            $admin->save(); 

            #DELETE
            $admin = admin::find(3);
            $admin->delete();

            #UPDATE
            $admin = admin::find(1);
            $admin->name = $req->name;
            $admin->save();

            #SHOW
            return admin::all();
        }


Maintanance mode: 
-----------------
      start Maintanance mode : php artisan down
       end maintainance mode : php artisan up
    bypass maintainance mode : php artisan down --secret="abc123"
    Now we can access website if we use url http://localhost:8000/abc123

    Thats All!!!


Session :
---------
    - session used tracking like login user.
    - when we create logout button then we add route of logout function to it.
      this logout function contain clear session code.

      eg : 
        function login(Request $req)
        {
            $req->session()->put('name',$req->name);
            return view('dashboard');
        }

        function logout()
        {
            if(session()->has('user'))
            {
                session()->pull('name',null);
            }
            return redirect('login');
        } 

        we can get this name variable on view using {{session('name')}}

Flash session :
----------------
we need one session variable as var1= "logged out successfully".
& it should be get clear when we refresh page.
for this we useses flash session.

        - we set this variable in logout function as : session()->flash('isLoggedOut','Logged out Successfully.');
        - we will show this variable on loginpage when we logout as {{session('isLoggedOut)}} 
          but this will only show for time when we logged out.
          after that once we refresh it should be get clear.


Seeding & Faker: 
----------------
        - insert dummy data in DB
        - how ?? create a seeder using command: php artisan make:seeder seederName
          It will generate seederName.php file in app\database\seeders.

          now user either QueryBuilder or ORM to insert data in run() function of seederName.php class
          ie $user = new userClass();
             $user->name = 'Pramod';
             $user->age = '22';
             $user->save();

         Run : php artisan db:seed --class=SeederName
         All done about seeding.

         Now we can insert fake data using faker
         for this add namespace : use Faker\Factory as faker.

         now inside run function create one faker as :
         $faker = faker::create();

         now we can create fake data using it as 
          ie $user = new userClass();
             $user->name = $faker->name;
             $user->age = '$faker->randomNumberNotNull;
             $user->save();

        PS : here name & randomNumberNotNull create random name & number and these are part of faker.

        If we want multiple record at one time put this in forloop.

        Thats All!!!



Pagination:
-----------

        instead of tableName::all() use tableName::paginate(5)
        & to create arrows on view use var1->links();
        eg 
        controller : 
            function index()
            {
                $data = admin::paginate(5);
                return view('dashboard',['admins'=>$data]);
            }

        View :
             <div class="row">
                @foreach($admins as $admin)
                    {{ $admin['id'] }}  {{ $admin['name']}} <br>
                @endforeach
            </div><br><br>
            <div class="row">
                {{$admins->links()}}
            </div>

        Note : if arrows are so bigger then add following code below above code

        <style>
            .w-5{
                display: none;
            }
        </style>


ORM Relations :
----------------
    suppose we have 2 tables 
    ie products & reviews
    They can have relationnships
     i One to many ie one product many review





------------------------Laravel API----------------------------------------------------------

Resource controller: 
--------------------
    - Note : Here we create table using migration while making model as 
      command : php artisan make:model modelName -m

    1.One to many  :
     eg : 
        one : posts
       many : comments  

       Schema::create('posts', function (Blueprint $table) {
            $table->id();
            $table->string('title');
            $table->text('body');
            $table->timestamps();
        });

         Schema::create('comments', function (Blueprint $table) {
            $table->id();
            $table->string('comment');
            $table->foreignId('post_id')->references('id')->on('posts')->onDelete('cascade');
            $table->timestamps();
        });

        Now define relationnships in models,
            - Post model should cotain function which have #hasMany relation as : 
              return $this->hasMany(Comment::class);

            - commn model should contain functiion which have #belongsTo relation as :
            return $this->belongsTo(Post::class);

        Now create functins in controller to add post & comment.
            - addPost is simple eg of ORM : 
                 ie $post  = new Post();
                    $post->title = "1st Post";
                    $post->body = "Description";
                    $post->save();

            - addComment is a bit tricky example.
                here comment adding is simple eg of ORM.
                    $comment = new Comment();
                    $comment->comment = "1st comment on 1st post";

                but addig post_id is bit tricky.
                here we first fetch post from id as : 
                $post = Post::find($id);

                remember we have model post which have 1 variable ie $table = posts 
                & 1 function where we defined hasMany relation ie function comments(){ return $this->hasMany(Comment::class)}

                we have to call this function using post object. ie     $post->comments()
                we have to done save operation ie.                      $post->comments()->save()

                pass the comment variable in which comment is saved ie  $post->comments()->save($comment);
    
                So Final function becomes 

                addPost($id)
                {
                    $post = Post::find($id);
                    $comment = new Comment();
                    $comment->comment = 'This is comment';
                    $post->comments()->save($comment);
                }

                we have to use same commnets function to fetch comments of perticular post as: 
                    ie return $comments = Post::find($id)->comments;
    }


