ECOM:
-----
BASICS
-------
    - l8 Auth added.
    
    1.Migration:
        php artisan make:model ModelName - mcr  
        [Ps:mcr means migarration & resourse controller]
        
        Schema::create('users', function (Blueprint $table) {
            $table->id();
          f $table->unsignedInteger('role_id')->default(1);
            $table->string('email')->unique();
            $table->timestamp('email_verified_at')->nullable();
            $table->string('password');
            $table->rememberToken();
            $table->timestamps();
            $table->softDeletes();

            //[Ps: default 1 role_id indicates that every user created with frontUI is customer. for other type we use DB.]
        });

        Schema::create('profiles', function (Blueprint $table) {
            $table->id();
          f $table->unsignedInteger('user_id');
            $table->string('name')->nullable();
            $table->string('address')->nullable();
            $table->string('phone')->nullable();
            $table->string('slug')->nullable();
            $table->timestamps();
            $table->softDeletes();
        });

        Schema::create('products', function (Blueprint $table) {
            $table->id();
            $table->string('title');
            $table->text('description');
            $table->double('price');
            $table->boolean('discount');
            $table->string('discount_price');
            $table->string('thumbnail');
            $table->text('options');
            $table->timestamps();
            $table->softDeletes();
        });
        Schema::create('orders', function (Blueprint $table) {
            $table->id();
         f  $table->unsignedInteger('user_id');
         f  $table->unsignedInteger('product_id');
            $table->unsignedInteger('qty');
            $table->string('status');
            $table->unsignedInteger('price');
         f  $table->unsignedInteger('payment_id');
            $table->timestamps();
            $table->softDeletes();
        });
        Schema::create('payments', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->text('options');
            $table->timestamps();
            $table->softDeletes();
        });
        Schema::create('roles', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('description');
            $table->timestamps();
            $table->softDeletes();
        });
        Schema::create('categories', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('description');
         f  $table->unsignedInteger('parent_id')->default(0);
            $table->timestamps();
            $table->softDeletes();
        });

    2.Models
    ---------
        - As we use softdelete in all tables.
            do changes as :
                1.use softDelete namspace
                2.create variable for storing deleted date ie $dates = ['deleted_at'];
            Do this for all models.

        - User : As we remove name & add role_id make changes on $fillable.

    3.View
        - We have remove name field from register view
        - replace name by email in to right corner which iis near at logout button.

    4.Controller :
        - As we remove name from user table, Go to register controller.
        remove name from validator & create function.

        - It always comes in mind that role_id should be added here
        But we provide default role_id ie 1 ie indicates customer.

ADMIN SETUP
------------

    5.Views:
        - Here we uses Bootstrap admin template for admin panel.
        so we have to keep seprate files for admin panel

        CSS : 
            add admin.scss in resources/assets/sass
            this file should be compiled & created css file.
            So open webpack.mix.js
                add path as :
                .sass('resources/sass/admin.scss', 'public/css')

            npm run dev
        
        MASTER : create app.blade.php in views/admin folder.

        CODE : create seprate admin folder in views & in it create dashboard.blade.php
                @extends('admin.app')
                @section('css') 
                    <link href="admin.css">
                @endsection
                @section('main-code')
                    <this is what you yeild inside master template>
                @endsection

    6.Controller: 
            - create AdminController
            - add function 
        
                public function dashboard(){
                    return view('admin.dashboard');
                }

    7.Route:
        - create grouping of route.
            add all admin related routes in it.
            at this end we only have dashboard function. so we should have only one dashboard route.

            Route::group(['prefix' => 'admin'], function () 
            {
                Route::get('/dashboard',[App\Http\Controllers\AdminController::class,'dashboard'])->name('admin.dashboard');
            });

SEEDING 
--------
    Here we create 2 roles ie customer & admin
    we also have to make customer as default
    After that we will create 1 customer(normal user) & 1 admin.

    - create seeder : php artisan make:seed AdminUser

        now add whatever we want to create inside run() function.
        
        public function run()
        {
            $role = Role::create([
                'name'=>'customer',
                'description'=>'Customer Role'
            ]);

            $role = Role::create([
                'name'=>'admin',
                'description'=>'Admin Role'
            ]);

            $user = user::create([
                'email'=>'admin@mail.com',
                'password'=>bcrypt('secret'),
                'role_id'=>$role->id,
            ]);

            profile::create([
                'user_id'=>$user->id        //THIS WILL ADD ID OF USER IN PROFIlE TABLE
            ]);

            //[Ps:This will add id (into roles table) of only user which are created using seeding. It dont add id of users which will register using frontendUI.
            To add them we have to write above code in RegisterController. We will see it later after all admin setup ]
        }

        add guarded array in all models
        protected guarded = []       //[Ps: dont use $ for creating array]

        now to activate adminSeeder we have to add some code in DatabaseSeeder.
        ie call call method in run() of DatabaseSeeder & pass AdminSeeder class as the params.
            public function run()
            {
                $this->call([AdminUser::class]);
            }

        php artisan db:seed

        This will create 2 roles ie customer & admin && create 1 admin user.
        Now register normaluser(customer) from frontend UI.


MIDDDLEWARE :


    Now we have to check user is customer or Admin 
    Then
        1. Admin dashboard only accessible to user who have role 'Admin'
        2. show relevent Dashboard

    To check this
        we have to create realtaion between user & role table.
        for this we use ELOQUENT RELATIONSHIP.

        in user model :
            public function role(){
                return $this->belongsTo('App\Models\Role');
            }

        in role model:
            public function users(){
                return $this->hasMany('App\Models\User');
            }
        
        now relation is established.
        Now using this relation we can check is user admin or not.
        We will check it in middleware

        For thos create middleware - AdminAuthenticated & register it in kernel as 'admin' name.
        add checking code as 

                if(Auth::user()->role->name == 'customer'){
                    return redirect('home')
                }
                return $next($request);

        Now apply this middleware & auth middleware in constructor of adminController so before calling any method of this controller it will check middleware.

        we will pass array of auth & admin
        public function __construct()
        {
            $this->middleware(['auth','admin']);
        }

        NOW ONLY admin CAN ACCESS DASHBOARD ROUTE.
        but still we will redirected on user dashbord although we logged in as admin,

        So open loginController. there is $redirectedTo variable with default home value.

        as we have to appy if else & change value of redirectedTo var
        But we cannot write our own function. we just have to override functions by 'Authenticated' trait. 

        we will override Authenticate() method. check for role admin as follows:
            if($user->role->name == 'admin'){
                $this->redirectTo = '/admin/dashboard';
            }

        NOW WE WILL GET ADMINPANEL ON ADMIN LOGIN
        DONE.

REMAINING ASPECT
-----------------
    when user will register then its id should automatically get added in profile table
    for this 
        open RegisterController fetch user which is registering & create new row with that id in profile table

            protected function create(array $data)
            {
                $user = User::create([
                    'email' => $data['email'],
                    'password' => Hash::make($data['password']),
                ]);

                if($user){
                    Profile::create([
                        'user_id'=>$user->id,
                    ]);
                }
            }

    Another 1 Aspect is that.. 
      - for normal user,if we are already logged in & still try to access login page     it will redirect to nomal-user-dashboard.
      - BUt in case of Admin user, if we are already logged in & still try to access login page it will still redirect us to normal-user-dashboard. 
      we have to make changes in RedirectedIfAuthenticated midddleware as follows

          public function handle(Request $request, Closure $next, ...$guards)
            {
                $guards = empty($guards) ? [null] : $guards;

                foreach ($guards as $guard) {
                    if (Auth::guard($guard)->check()) {
                        if(Auth::user()->role->name == 'admin'){
                            return redirect('/admin');
                        }
                        else{
                            return redirect(RouteServiceProvider::HOME);
                        }
                    }
                }
                return $next($request);
            }

        DONE!!!.
        NOTE: we can still access admin's normal dashboard using route home.


PRODUCT MODULE
---------------------
    - setup diff url for #
    - create resourse route for productController as it is resourceCOntroller. & give its route to product in dashboard.
    - in index() call view
    - create folder - products in admin folder & create index.blade.php
    - initially we just write 'PRODUCT LIST' in that view ie hardcode

    - as we click on product link on dashboard still that link dont change to blue & still dashboard link is blue.
    This is due to :
        <a class="nav-link active" href="#">
    
    - we have to add conditions on this 'active' bootstap class
    - we have to replce this as :
        
        <a class="nav-link @if(request()->url('/admin/products')==route('admin.products.index')){{'active'}} @endif" href="{{ route('admin.products.index') }}">

    - here we compare our requested required URL with route assoicated with that link.


Category Module:
----------------
    All things same as Product Module

    UPDATE :
        we forgat to apply admin middleware for produt & category. so anyone can see admin panel using url app.test/admin/produts.
        To avoid this appply middleware on route instead of constructor.
        - so remove middleware form constructor of adminController
          & as follows in web.php


Prouduct Category CRUD
-----------------------
NEW RELATIONS & MIGRATIONS :

    - As product-categry have many-many relation.
        ie multiple products ie phones which comes under multiple category ie electronics, phones.
        So as *-* relation should Have 3 tables
        ie Products Categories && CategoryProducts (mapping table). 
        lets create migration for mapping table.
            php artisan make:migratation create_category_products_table


        Schema::create('category_products', function (Blueprint $table) {
            $table->id();
            $table->bigInteger('category_id')->unsigned();
            $table->bigInteger('product_id')->unsigned();
            $table->timestamps();
        });

        Models be like :            
        public function products(){
            return $this->belongsToMany(Product::class);
        }
        
        public function categories(){
            return $this->belongsTo(Category::class);
        }


    - As Category to Category *-* relations.
        So we need mapping table with id for both table.
        here both table are category table so distinguish key we will create 
            parent_id & category_id
        For this we have create migration : create_category_parent_table

        Schema::create('category_parent', function (Blueprint $table) {
            $table->id();
            $table->string('parent_id');
            $table->string('category_id');
            $table->timestamps();
        });

        Initially we added a column parent_id in categories migration. but will remove it
        
        Is we need to establish a relationship between both table we will use function in Model
        here only one model so we define only one function.
        but as params to belongsToMany() we will pass :
            2nd Table       ->   Category_table
            mapping table   ->   category_parent
            foregin key     ->   category_id
            key on which foregin key applied : parent_id
    
        public function Children(){
            return $this->belongsToMany(Category::class,'category_parent','category_id','parent_id');
        }

CRUD FORM:
    - To select multiple categories we will use select2 npm package. 
        just check it on internet you will get why we uses it here.
    - Video 17:19 minutes
