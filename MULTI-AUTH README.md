1. install laravel Ui using - composer require laravel ui
2. check all cmd -            php artisan
3. install auth -             php artisan ui:auth 
4. add bootstrap -            php artisan ui bootstrap (no : )
5. Above commands will do changes in package.json and to update & install those changes use cmd - npm install && npm run dev (This will create css & js folder with app.js & app.css)

UI & AUTH ADDED. 15.01 min
1. migrate tables - php artisan migrate
2. make model+migration+controller php artisan make:model Admin -mc
3. copy user migration field to admin migration & migrate : php artisan migrate
3. Now keep in mind that dont add admin using frontend ui. 
    Add it using db seeder. 
    so open DatabaseSeeder.php run() function. There is syntax of factory. 
    So we have to create factory.
    use command - php artisan make:factory Admin
    add hardcoded records in defination() function.
    do seeding in DatabadeSeeder.php add - \App\Models\Admin::factory()->create(); in run()
   Now seed it using - php artisan db:seed 
4. Use tinker to see data - php artisan tinker
    tinker will get start. use - Admin::find(1)
    it will show data.
    Exit using - exit 

ADMIN TABLE MODEL CONTROLLER COMPLETED

Let use same concept as user for admin.
if admin not valid then redirect it to admin.login page
SO we need 1 middleware 1 view folder & login page in it.

ROUTE:  
    Create Route for admin login->[view,post] and dashboard. see all routes- php artisan:list
   
VIEW:
    Create view in view/admin for login and home by copying. 
    - As we dont register user we need to remove register link. 
    - So we need 2 diff layouts. 
    - Hence copy app.blade.php from layout in itself rename it to admin.blade.php
        - remove register link
        - change route of login link to admin.login 

MODEL:
    - as our all admin field same as user. both models will be same. so just copy paste & change name.

MIDDDLEWARE -
    As we created AdminAuthenticate middleware copy authenticate() function of authenticate middleware and override its code for per our admin guard

    protected function authenticate($request, array $guards)
    {
            if ($this->auth->guard('admin')->check()) {
                return $this->auth->shouldUse('admin');
            }

        $this->unauthenticated($request,['admin']);
    }

CONTROLLER:
    Add method login() for logining in in AdminController

    public function login(Request $request){
        $credentials = $request->only('email','password');
        if(Auth::guard('admin')->attempt($credentials,$request->remember)){
            return redirect()->route('admin.home');
        }
        return redirect()->route('admin.login')->with('status','Failed to process login');
    }


1. In auth.php - create guard, provider & password for admin
2. Middleware : copy authenticate middleware as AdminAuthenticate name. Just change classname and route('login') to route('admin.login')
    Register this middleware in kernel.php as 'admin.auth'
    Apply it on routes using route group

DONE!!!!


    
AVOID ADMIN LOGIN PAGE IF ALREADY LOGGED IN 
for this :
    copy redirectIfAuthenticated middleware & confige its route as 
               return redirect()->route('admin.home');
    register it in kernel.php as admin.guest name
    then apply this middleware to route as admin.guest
NOW YOU WILL ALWAYS REDIRECTED TO DASHBOARD IF YOU LOGGED IN AND TRY TO 
ACEESS LOGIN PAGE.



LOGOUT:
    create admin.logout in web.php && add this route to admin.blade.php for logout button. 

    generally when we logged out session get destroyed. 
    ie if we logged out admin only then user also getlogged out due to session destroyed.
    - To solve this problem we should logged ut specific guard only.

    As we use seprate 2 controllers
    ie LoginController - user
       AdminController - admin

    both should we use AuthenticatesUsers.
    AuthentucatesUser haves all functions ie login logout defined. Our LoginController Just uses Those fuctions directly.

    Now instead of using them directly we have to overroide these functions in LoginController and AdminController. Here we working only on logout function so just consider logout only.[PS:We can override login() to].

    as here login function calls guard() we need to overrride auth with passing web & admin as a parameter.

    protected function guard()
    {
        return Auth::guard('admin');//for loginController replace 'admin' by 'web'
    }

    Now our logout() which we copy pasted (overridden) from AuthenticaresUser will call this guard and get admin/web as parameter automatically. So we dont need to do any change in logout().

    THIS WILL PERFECTLY DO OUR SEPRATE LOGOUT FUNCTIONALITY.

    in this addition override loggedOut() function and return redirect route as for admin-> admin.login & user->login. Sothat after logged out we open login page.

DONE!!!.
