ORM :

Migration with Foreign Key.
    Must conatin
    - BigInteger('table_id')->unsigned()  
    - PS : As our Id() is alwyas +. ie unsigned. So this key must be unsigned else error 
    - foreign('table_id')->referances('columnNameInTable2')->on->('table2Name')->onDelete('cascade')


1-1 :
------
    - eg 1 User 1 Phone.
    
    - migrations
        User  : (default)
        Phone : id, number, user_id(f)

    - models 
        User : has function Phone() which returns hasOne() instance.
        Phone : has function USer() which returns belongsTo() function instance.

        fuction phone(){
            return $this->hasOne(Phone::class);
        }

        function user(){
            return $this->belongsTo(User::class);
        }

        //PS: These functions will we call in controller to while saving data
        //ie. $user->phone()->save($phone)

    - php artisan migrate
    
    - INSERT :
        create HomeController.
        create insert() method.

        This method will use Both model. User & Phone.
        now create user table instance with all filed ie name email password.
            $user->name = "Admin";

        create phone table instance & add all field ie phone pin etc
            $pin->code = "223213";

        Now call save mathod for $user && phone() class of user. & pass $phone as parameter. 
            $user->save();                  //this will save data in user table
            $user->phone()->save($phone);   //this will save data in phone table.

        ALL DONE FOR INSERT.

    - SHOW :
        we can fetch phone number based on user.id
        it fetch data as id -> user.id == user_id ->phone.id ->phone.number
        
        show($id){
            $phone = User::find($id)->phone->number;
            return $phone;
        }

    DONE.


1-*
------
    - 1 Post * comment
    - migration: 
        Post :      id title body
        Comment :   id comment post_id(f).

    - Models:
        Post : contain Comment function which returns hasMany() function.
        Comment : contain Post function which returns belongsTo() function.

        public function Comment(){
            return $this->hasMany(Comment::class);
        }
        
        public function Post(){
            return $this->belongsTo(Post::class);
        }

    - INSERT : 
        in 1-1 we create one 1 function & direct add phone number when user is added. there we can't add phone number again & again so we dont create diff function to add user & phone.

        but here we can add n comments for 1 post.
        if we do all in 1 function whenever we try to add comment, post will be added.
        so we use to diff funtions.

        we can add post as normal
        but to add comment for specific post we need to fetch post using id & then while adding comment.

        addPost(){
            title,body,save()
        }

        addComment($id){
            
            $comment = new Comment();
            $comment->comment = "GOOD";
            
            $post = Post::find($id);
            $post->Comment()->save($comment);

            return "Comment added"
        }

    - Show
        we can fetch all comment by passing id of the post
        as follows :

        public function getCommentsById($id){
            $comment = Post::find($id)->comment;
            return $comment;
        }

*-* :
-----
    eg mullti User & multi role.
    - here we use 3 tables 
        1. User     : store user data
        2. Roles    : store role data
        3. RoleUser : store user-role mapping.

    - migrations: 
        User : default
        Role : id, name
        RoleUser : id, user_id(f), role_id(f)

    - models 
        User : have function Role().
        Role : have function User()
        RoleUser : Nothing :) other than $table variable

        Till now in 1-1 & 1-* 2 functions are used ie hasOne()/hasMany() && belongsTo().
        But here only 1 function is used ie belongsToMany()
        Both Role() User() function returns belongsToMany()
            
        params for belongsMany('main-tables ie User/Role','mapping table ie RoleUser')

            public function Role/User(){
                return $this->belongsToMany(Role/User::class,'role_users');
            }

    - INSERT : 
        create 1 controller ie HomeController.
        create 2 functions ie addUser & addRole.

        adding role isnt big deal
        adding user isnt big deal also.
        But to store data in mapping table is tricky.
        as As we can pass array of records as parameter.
        we will create array of roleid 
            ie $roleids = [2,3]         ie ( 2=developer 3=analyst in role table )  
        
        Now call Role() function we defined with attach() method instead of our save()
        & pass $roleids as params
         ie $user->Role()->attach($roleids);

            public function addRole(){
                $role = new Role();
                $role->name = "Admin";
                $role->save();

                $role = new Role();
                $role->name = "Developer";
                $role->save();

                $role = new Role();
                $role->name = "Analyst";
                $role->save();

                return "Roles created";
            }

            public function addUser(){
                $user = new User();
                $user->name = "Pramod";
                $user->email = "pramod@mail.com";
                $user->password = "pramod@mail.com";
                $user->save();

                $roleids = [2,3];
                $user->Role()->attach($roleids);


                $user = new User();
                $user->name = "Avinash";
                $user->email = "avinash@mail.com";
                $user->password = "avinash@mail.com";
                $user->save();

                $roleids = [1,3];
                $user->Role()->attach($roleids);


                $user = new User();
                $user->name = "Ekaj";
                $user->email = "ekaj@mail.com";
                $user->password = "ekaj@mail.com";
                $user->save();

                $roleids = [1,2];
                $user->Role()->attach($roleids);

                return "Record Added successfully";
            }

        - SHOW :
            we can fetch off roles of user.
            as well as
            All user which played perticular role.

                public function getRolesOfUser($id){
                    return User::find($id)->role;
                }

                public function getUsersOfThatRole($id){
                    return Role::find($id)->user;
                }

        DONE !!!.