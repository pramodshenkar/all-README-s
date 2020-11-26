1.He He.. XD
-------------
    create : ng new project-name
    run : ng serve

    go to last commit :
    git reset --hard
    git clean -df

2.Modules
----------------
    structure:
     - every module defined by ng-module ie it act as entry point

        import
        ngModule({
            declartion[];       :mention th all components
            import[];           :mention other imported module
            provider[];         :services we want to add
            bootstrap[];        :which componet we want to load
        });
        export class modulename {} 

3.Decorators :
---------------
    - Decorators provide methadata (extra info) which shows How perticular component have structure and how that component should be instantiated, processed & used at runtime.  
    - class / properties / methods/ parameters MUST follow some structure MUST follow rules to be intiated & processed. So they are decorators

    - start with @
    - types :
        1> class:        ngModule , components  : Simply module & component he class mhnun treate kele jatat. so te class decorater.
        2> propertry:    input , output         : properties for ip & op
        3> method:       HostListener 
        4> parameter:    inject :               : as we created services we have to pass them as parameter to constructor of component

    - Decorators have metadata. 
      eg : component MUST have selector templateURL & styleURL. these three are metadata of decorator @component
           ngModule MUST have declaration, import, provider, bootstrap .

4.component
------------
    - Flow :
        main.ts   = here appmodule is bootstraped
        appmodule = declaration + import + providers + bootrap : here bootrap loads : [AppComponent]
        AppComponent = 
           ______|________________________________________________________
           |                                |                            | 
        1.selector  : app-root         2.templateURL                    3.StyleURL
           |                                |                            |
           |                                |                            |
        index. html <--------------app.component.html<------------app.component.scss
      (include selector as tag)   (insert in place of app-root)  (provide css to templateURL file)

    - Step  to import XYZ module component to ABC Module
        1> import XYZ module in ABC Module
        2> export that component from ABC module
        3> write that component <selector> of component of XYZ in component.html in ABC
        4> Done!!

    - structure :
        @component({selector,templateURL,styleURL});
        export class componentName
        {
            variables;
            methods: these methods used to attach to events of button
        }

5.Directives :
---------------
    - Types:
        1.component
        2.structural : *ngIf, *ngFor, *ngSwitch
        3.attribute : [ngId], [ngStyle]

    b4 going to *ngIf read about template ref var.
    - template ref. variable : variable in template (html file) which we can acess in .ts file.
    - DOM tree contain variaous DOM element such as <html> <body> etc. This variable is just referace to that element
    - it is similar to the attribute of tags. (For remining it we can say attributes are one type of template ref variables thats why we can manioulate dom element (tags) using attribute)
    - Synax of template ref var .
         ~ always in <ng-template> tag. & start with #
    - Eg <ng-template #var1>HELLO</ng-template> 
    - used in if Else .   

     - < *ngIf="isUser; then var1; else var2;">  HERE isUser : bool in .ts 
                                                  var1, var2 : template ref var.

6.Binding:
-----------
    1.interpolation : variable declared in component (ie. export class component { var = 10;}) & access using {{var}}
    2.Propertry Binding : here we assign variable to value of attribute. NOTE : attribute must written in []
        eg1  <input type='text' [placeholer]='var1'>    where var1 = 'Enter Name'.
        eg2  <div [ngStyle] = {color: var2} ></div>     where var2 = 'red'

    3.Attribute Binding :
        - as Propertry Binding realted with properties (which are attribute) then what is attribute binding.
            simple PB only works on attribute proided by html.
            And AB works for custom user defined attributes.
        - how to create custom attribute ?
            by attaching attr. to our attribute name . eg : <h1 [attr.AttributeName]='var1'>Hi</h1> where var1='anyValue'
            here to see this attribute we have to use inspect element.

    4.Event Binding : eg (click)=ShowMessage();
    
    5.Two way Binding :
        - NOTE : first import FormsModule in app.module.ts Else Two Way Binding will not work.
        - to access data from view->component & component->view we use ngModel variables.
          What is ngModel variable? Simple variables but just

7.Pipes 
--------
    - eg {var1 | uppercase}.
    -custom pipe we can create custom pipe by ng g pipe pipeName. Then pipe-name.ts will generate.
      How to add custom defination to pipe?  -> pass the value & parameter to transform method. MUST write returntype string & return required string.
      here value : variable on which we apply pipe. 
      here parameter : other parameter

      eg : class pipe ...
            {
                transform (value:string, param1:string,param2:string) : string 
                {
                    return value+''+param1+''+param2;
                }
            }

    -How to apply it?
    {{ var1 | pipename:param1:param2}}  where param1, param2 defined in component.ts file.

8.routing :
-----------
    type: pathLocation & hashLocation.
    
    1.Path: dont need to add anything direct write path
    2.Hash : In module.ts there are 4> ie   declaration,import, proider,bootstarp.
                Here in provider is array. Here we pass following object

                {use:LocactionStaturgy, useClass: hashLocation}
        where : here we have to read route from UrL.but how? Locaction services will read it.
                But how enable Locaction services? use : {use:LocactionStaturgy} & then in it define useClass: hash/pathLocation

    what is base href?
    check index.html there it present as <base href='/'> means point to root folder.
    simple : string in base href get concanated with http://localhost:4200/ & create new root level URL.

    Why it ? angular application is SPA single page application. we just changes content of that single page.
    '/' in base href says that show this page when user enter URL http://localhost:4200/  that lastwla slash we define in base href
    suppose if we want to our base URL is like http://localhost:4200/user/    then we will use <base href='/user/'.

    note : we cant create url like http://localhost:4200user. there must / between 4200 & user. so base href value always start with /

    How to do? use command. ng build --base-href="user". (PS:I tried it mannualy it works but dont know then why commnad) 

9.Routing Module
-----------------
    - we can declare path in anywhere in component. but for simplicity we add one special module where all paths are store called Routing Module.
    - have same structure as Modules but one thing added : an array of object with name Routes = [];

    - each object should have {path,component/redirectedto}
    - when to use pathMatch?
    - to create link we have to use value of 'path'.

    - As we create route, How Show component associated with that route on our webpage?? 
        To show any component's html code on our app.component.html we use <router-outlet>. its just  a tag. we can use it multiple time.
        Synax :  <router-outlet></router-outlet>   : content of component renders inside this tag

    Note : if you create Routing module using command then MUST import routing module in app.module

10.Paramterized Routes:
-----------------------
just use : in path Eg. {path:'user/:id'} & in browser write localhost:4200/user/201



11.What if user enter anything which not is route?
---------------------------------------------------
    then we write {path:'**'} =>Known as wildcard Routes

12.How to group Multiple Route?
--------------------------------
    - Suppose components are user-id, product-name, product-value,user-profile.
    - all mixed up so we create logical grouping as
        user                                product
        {                                   {
            user-id,                            product-name
            user-profile                        product-value
        }                                   }
    -this is called as ++++ CHILD ROUTING ++++
    -syntax { path: 'product'
              children: [arry of object of path]
            }
    - PS : it must not have component.
           simply procuct dont show anything /product/product-value/ will show ProductValue component.
           if we want to show anything define {path:'' redirectedto } in childs.


13.Lazy loading:
----------------
    - initailly. All modules loaded.
       But if want load order module only if login module load then this called lazy loading.
    - How to acieve??
        1. doNt import modules directly.
        2. import modules as path with syntax 
            { path: 'user', loadChildren: () => import('./user/user.module').then(m => m.UserModule)},
        3. Wait. Here we just import module then how to call component?
            by setting path in user-routing.module.ts
            
            how?? --> here we alredy creat path as user for this module. so create blank path as '' & set component to this ''.
            so it will open that component on URL localhost:4200/user 

            suppose we have multiple component then ???-->  with path '' add child:[] insted of component.


14. Template Driven form :   <import FormsModule in app.module.ts>
--------------------------
uses 1 things: 
    - ngform & ngModel. : 
    - for what ??--> 
        group the form element. ie elements having ngModel variable are binding together and this group assign to ngform variable.
       
    - Syntax NOTE : 
        For ngForm :  normally we assign variable as ngform = form-name. 
                        but here syntax are opposite. ie  form-name = ngform.
                        Addition to this syntax ngform name must be start with # ie #form-name='ngForm'.
        For ngModel : syntax is  just add ngModel ie <input type='text' ngModel>
                      wait... How it will know diffence in variaous textboxes?  using diffennt name of textboxes.
                      So name MUST be in textbox if ngModel is declared.
                      eg : <input type='text' name='username' ngModel>

                      REMEMBER : In template form only ngModel. but in two way binding [('ngModel')]

    - This is all about declaration... But wait what about sending data to component.
        for this with this ngForm we use event ngSubmit in form tag. & pass this ngform variable as parameter
        eg : <form #StudentForm='ngForm' (ngSubmit)=methodName(StudentForm)></form>

    - here suppose can custom validation message using ngIf if value in textbox is invalid.
      wait how will diffennt custom validation message get added with perticular textbox.
      here we uses ngModel variable. this is same declaration as ng form ie. #var1 = ngModel.
    - Previously we just use ngModel keyward to group textboxes for form.
      But here we use ngModel variable to check input is valid or not. & these two are diffennt things.


15.Reactive Form :   <import ReactiveForms in app.module.ts>
-----------------
    - in template driven form we focus on html file. we create form using ngForm on html & then pass it to us using submit event.
    - in Reactive form we focus on .ts file. We create Model using FormGroup & FormBuilder. & then we bind this model to our simple Html form.
    - Why it not TDF? --> coz Reactive fom support Two way data binding.

    How to create Model for ReactiveForms?
    using : 1>FormGroup   : it will create a form  
            2>FormBuilder : used to delevop structure of form created bby formgroup with diffennt controls with initial value & validation.
            3>FormControl : associated with controls ie textboxes. as any value changes in textbox it will reflact to FormControl Object in ts. file
            NOTE : to access data of form we MUST us formname.controls syntax

    Syntax : 
    1. FormGroup: create form as loginform : FormGroup;
    2. FormBuilder : we create the structure of form.
            suppose form will have 2 control 
            then 
              1.inject FormBuilder in constructor. ie constructor(private formBuilder: FormBuilder)
              2.create form using group method & assign it to login Form as this.loginform = formBuilder.group({})
              3.pass object of controls/textboxes in that group method. as group({username : ////, password:////})
              4.here in this object we create new control using syntax user: new FormControl();

        So OverAll code be like :

        loginform = FormGroup;
        user = '';
        pass = '';

        constructor(private formBuilder : FormBuilder)
        {
            this.loginform = formBuilder.group(
                {
                    username : new FormControl(),
                    password : new FormControl()
                }
            );
        }

        Done...This is just declaration.
        Now write OnSubmit method for asign this data from contols ie username & password to our variables declared in ts ie user & pass above

    Now HTML Synaxces :
        - In ReactiveForms, As in model we create form using FormGroup so we will use same sytax in html.
          eg : <form [formgroup] = 'loginform' (ngSubmit) = OnSubmit('loginform') ></form>

        - instead of ngModel & name as like TDF we directly uses FormControlName. But value of this Must be same as declaration of control in object in formbuilder 
          here we deaclare controls in formBuilder with name username & password
          so we uses same name :
          eg : <input type='textbox' formControlName ='username'>


15.validation in Reactive Form:     <import validators from angularform in component.ts of reactive form>
    
    - here we apply validation which are written while creating controls in object passing to formBuilder.group().
    
    - until this we create form controls/textboxes as username = new formControl();
      But for validation we will create controls as   username = [];
        here this arry [] contain 1st element as value passed in textbox binding with FormControlName.
        so we kept it as '',
        second elements of array is validation-type OR if multiple validation then array of validation.
        There validation elemetent syntax is : validators.validation-type 

        eg : username : ['',[validators.required,validators.maxlength(10)]]  

    - Now How to show custom message for validation :
        As in TDF we check ngIf & !txtbox-ngModel-name.touched.valid.
        here we use        ngIf & formName.get('formControlName').haserror('type-of-validation');
            Note: we can also use .comtrols['formControlName'] instead of .get().
        
        eg  : <small *ngfor="loginform.controls['username'].haserror(required)">Username required</small>

        if we want to use touched then use it with get() as . get('username').touched


16.GetValue() & reset() :
-------------------------
    simple functions 
    syntax : formName.get('formControlName'); we can attach others to it.
             Eg :  formName.get('formControlName').touched;
                   formName.get('formControlName').hasError('required');

    syntax : formName.reset();
    Note : if we create seprate function for reset then use this with it eg: this.formName.reset()
    PS : if want to reset field to default values then pass object of those values to reset() as parameter.

17.SetValue() & Patchvalue();
-----------------------------
    Both are useto set default value.
    Both take object of values as parameter.
    diffence :  Setvalue() takes object Who MUST have all FormControlName values. in PatchVaue() we can skipp any value.

    syntax : formName.SetValue({name:'pramod'})  PS:use this. as javascript rule.

18.valueChanges() & statusChanges():
-------------------------------------
    - as we dont get data until user submit form. But suppose if we want to get data everytime user changes input without submitting then ??
    -> then we use these function. they track every key of user press while typing input.
    - what is statusChanges()??--> as valueChanges() gives value of user entered  then statusChanges gives status of that value ie valid or not.
    
    - Thats it??
    - no.  these two are properties of classes : formGroup, formArray & formControl.
    - they return observables. So to get data we have to subscribe them.

    - formControl + vc : return value of that control whenever any changed in control.
    - formArray + vc :  return value of array whenever any changed in control elements of array.
    - formGroup + vc :  return value of all control althogh any control value changes.

    for status similar logic is applied

    Syntax :
        - this.                                                             as js sytx we use                 
        - this.loginform                                                    which form                        
        - this.loginform.get('username')                                    suppose we want to get username we will use get method. this will only give username on submit
        - this.loginform.get('username').valueChanges                       to track every change without OnSubmit we use valuechanges. But it will only return observable.
        - this.loginform.get('username').valueChanges.subscribe()           as we cant use obbservale only. we need to subscribe it.
        - this.loginform.get('username').valueChanges.subscribe(user => {}) store this username in variable user.
        - this.loginform.get('username').valueChanges.subscribe(user => { console.log(user)})  track uservalue using console.log().

    this for formControl. For formGroup just dont write get() method.


19.formArray:   <import formArray>
---------------
    - formArray combines formControl & create array of it.
    - syntax: const arr = new formArray();
    - we pass the array of controls as parameter.
        - here we create new control using new FormControl('control-value',validators.validation-type());

    eg :  const arr = new formArray([
            new FormControl('pramod',validators.minLength(3)),
            new FormControl('Shenkar',validators.minLength(3))
    ]);

    - to check these values & status we can use console.log(arr.valuees+''+arr.status)

    - NOTE :  this will have to create on initialization so we write these all code in onInit() method. Or eiher write in constructor like a normal formControl.
    - we can use reset(), Setvalue(), Patchvalue() with this formArray.


20. Nested formArray():
-----------------------
    - As we have formGroup() we have many formcontrols in it as we (coder) want.
    - But suppose we are creating form for flipkart-Cart. here we dont know how many elements will be added to cart by user untill user adding elements.
      So we just cant decide how many formControl should be write in one form.
      So here we uses nested form concepts.

    - Synax :
        initailly consider this is any formControl. So this is under scope of formGroup;
        2nd thing : we have to give formArrayName like formGroup & under this we will write ngfor loop to get every element of array

        eg:
            <form [FormGroup] = 'StudentForm'>

                <input>email</input>
                <input>Class</input>

                <div formArrayName = 'contact'>
                    <div *ngFor="let item of StudentForm.controls.contact.controls; index as i ">
                        <input [formControlName]=i>
                    </div>
                </div>
            </form>

            NOTE : when we use [] with anything it will assign the value to that component
                    Eg. [FormGroup] = will assign object 
                       [FormControlName]=i will asign value of i to formControlName.

                   when we directly use name without [] then it will only assign string.
                    Eg. formArrayName = 'contact' it will assign string 'contact'
                        [formArrayName]= 'contact'  will assign value present in variable contact.  

        NOTE : In ts file we must declare StudentForm as StudentForm:any; 
                if we declare it as StudentForm:FormGroup; it show error for controls

        In .ts file decalre array as any other formControl 
            
            StudentForm:FormGroup;

            constructor(private formbuilder: FormBuilder) {
                this.loginform = formbuilder.group(
                {
                    email : ['Student-name'],
                    class : ['BE'],

                    subjects: new FormArray([
                        new FormControl('math'),
                        new FormControl('hindi'),
                        new FormControl('IT')
                    ])
                }
            );
            }

            NOTE: here we can use formBuilder.array() method insted of formArray(). Both will create same array.

    - How to write function that will create new inputbox by clicking on button as user needed
        simple Funda :  
            - Here we use for loop for print elemets of array without considering how many element. 
            - So we must have add formcontrol to array whenever user click on button.
            - Now how to add elements to array???
              --> using push() method & pass element as parameter.
              So syntax become push(new formControl());

            - Wait. As our formGroup can have multiple FormArray How to get know which formArray?
              --> using get(element-name) method

              Eg: this.loginform.get('subjects').push(new formControl());

              Note .get(subjects) is same as .controls.subjects


21.Add row which having multiple textboxes
-------------------------------------------
    - In Previous note we added only one textbox as need which is quite simple.
    - But suppose if our product have 3 column ie id, product,type. & we have to generate entrire row as user press addtocart button.

    - This is quite similar to previous one just changes as follows.
        1.in .ts file :
            Previously we pass "new formControl()" as parameter to push method. eg push(new FormControl());
            Here : we have to pass function which will return multiple formControl.
                eg: push(CreateRow());  
                    &&
                    CreateRow()
                    {
                        return this.formBuilder.group()
                        {
                            id : new formControl(),
                            product : new formControl(),
                            type : new formControl()
                        }
                    }
            
            This will about Push method.
            What is about structure of for -->
                Previously we create formGroup with formBuilder & we use formArray to create array. 
                    this.form-name = new formBuilder.group(
                        {
                            user : new formControl(),

                            items : new formArray([
                                product-name : new formControl()   //by default one textbox for procuct other will added in this array by push operation.
                            ])
                        }
                    )
                Now we will create array using formBuilder
                    this.form-name = new formBuilder.group(
                        {
                            user : new formControl(),

                            items : new FormArray([this.CreateRow()]);  //by default row will be added due to this CreateRow() call Here CreateRow method returns object having {id,prodcut-name,type}
                        }
                    )
                This is all about changes in .ts file

    - Changes in .html 
        Previously we have structure :
            <form [formGroup]='form-name'>
                
                <input>email</input>
                <input>Class</input>
                
                <div formArrayName='items'>
                    <div *ngfor="let item of form-name.controls.items.controls; index as i">
                        <input [formControlName] = "i">
                    </div>
                </div>

            </form>

            Now its definitely sure that we will use multiple input textboxes in One iteration of for loop.
            Here Previously we use index which assign to each textbox to distiguish them.
            But Here we will have group of multiple textboxes so we will assign index to whole group.

            Now How to create this group--> using FormGroupName.

                <div FormArrayName = 'items'>

                    <div *ngfor="let item of form-name.controls.items.controls; index as i", [FormArrayName]="i">
                        <input formControlName='id'>
                        <input formControlName='product-name'>
                        <input formControlName='type'>
                    </div>
                    
                </div>

            All done. This is all about show our array in HTML.
            Note: we can use FormGroupName insted of FormArray name.

    Now Lets Add button to add row. & delete row. 
    - Add :this.form-name.get('items').push(Addrow());
    - Remove : Now we will user removeAt() method & pass index as parameter. 


22. services : 
    - Suppose our diffennt components (& modules also) contain same functionality.
    - Then its not good to write code again & again in every component.
    - So we must have to share that functionality.
    - Ok. But how --> using the Services.
    - Ok. But how service & component work together as they have diffennt structure.
      --> Using dependancy injection. We have to inject our service in constructor of component at RUNTIME.

    - Contact (module)
        |- Componets
        |    * create
        |    * edit
        |    * delete
        |
        |- Services
            * processing-data
            * make Http requests 
            * clean-data

    In above module All 3 components ie create edit & delete. anyone can fetch any service. All 3 services are avalliable for all components.

    - Use : Eg Services are used to make Http requests & get response from API.

    - services will variables as well as methods just like class.

    - steps : 
        1. generate using cli : ng g service service-name eg ng g service contact This will generate service with classname ContactService
        2. write method in service  
            eg 
                getContact()
                {
                     #All functionality like http calls should here    
                }
        
        3. import service in component     [PS : here keyward service refers to service classname.]
            eg import { ContactService } from '../contact.service';

        4. Ok. Now inject service in constructor of component.
            eg constructor(priave contactService : ContactService)

        5. All done. Now you can call method in service using that serviceVariable.
            eg Here we calling in onInit() method.

                 onInit()
                 {
                     contactService.getContact()
                 }

            NOTE : here instead of calling serviceMethod in our component method,
                   we can directly call it on click event of button.
                   eg <button (click)="contactService.getContact()" >Click Me</button>

                   But for this : injection is Must public ie constructor(public contactService : ContactService )


23. observable
---------------
    - observables keep track of data that is send to & send by our webAPI
    - But we cant get data until we subscribe observable.
    - eg: Some Youtube channel provide lots of video. But we dont get it until we subscribe that channel.

    - Ok. Wait.. How to get observable-->
    - http methods ( ie get post returns a observable.) To subscribe this observable we have to subscribe that method.
    - Ok. Here we will write our own method in services *with returntype observable* and call http method in it. So indirectly we have to subscribe thsese methods in out component.

    NOTE : until injection of HttpClient we in constructor of service we cannot use it. so done it first. Also import Observable

    - Syntax: 
     - import HttpClientModule in app.module.ts
     - inject HttpClient in service. & then inject that service in component.
     - write method in service with returntype observable & call http method & return data returned by http methods. subscribe this method in component.

     1. Syntax for DI & userdefined method in service:

        constructor(private httpClient : HttpClient){}

        getDataInService() : Observable<any> 
        {
           //we have to call get method here. For get method plz see next topic
        }

    2. Syntax for DI & userdefined method in component:

        constructor(private contactService : ContactService )
        var: any;
        getDataInComponent()
        {
            this.contactService.getDataInService().subscribe(temp=> this.var=temp) 
            //here we get data from observable in temp variable & store this our any variable var to show on template  
        }

    Greate!!All done.


24.HttpClient :  
    - What is HttpClient --> It is a CLASS who perform HttpRequest.
    - suppose : if db store on @server of flipkart then How our flipkart website on clinet side get & post data on it
      --> this data sending & recieving done via HttpRequest & HttpResponce. This is known as Http protocol.

      - HttpClient can perform following Http requests : 
         get() post() put() delete() head() jsonp() options() patch()

      steps: 
       1. import HttpClientModule in  app.module.ts
       2. import HttpClient in our service.ts  & inject it in its constructor.& then inject service in component
       3. call & return http method in serviceMethod . & subscribe that serviceMethod in component.
       :) 

       Syntax : 
       1.for serviceMethod :
       getData() : Observable<any>
       {
           ApiURL = 'localhost:3000/items';

           return get(ApiURL)                           Http methods takes ApiURL as parameter.
           return get<any>(ApiURL)                      Observable also have returntype. So specify returntype of observable of get method.
           return httpClient.get(ApiURL)                We can't directly call get method. We MUST have to call it with DI-variable.
           return this.httpClient.get<any>(ApiUrl);     this keyword is MUST according to js syntax
       }

       Note: suppose we want specific record with id=2 then we have to pass url as localhost:3000/items/2 as parameter
        eg : get(ApiURL+'/'+id);


        2.for componentMethod
        var1:any
        getData()
        {
            this.contactService.serviceMethod().subscribe(temp => this.var1.temp);
        }

        Note: suppose we want specific record with id=2 then we have to it via serviceMethod(2) parameter.

        3.for Html
        <div *ngFor="let item of var1" >
          {{item.id}} {{item.name}} : {{item.role}}
        </div>

        Note: suppose we want specific record with id=2 then we have to show using if loop.
        eg
            <div *ngIf="var1 as item" >
                {{item.id}} {{item.name}} : {{item.role}}
            </div>

       Done. Thats great.


25. observable Async pipe : 
    - To subscribe observable we have 2 options:
        1. by subscribe() method : we already seen
        2. by async pipe : as follows

    - In subscribe we use any variable to store data return by observable.
      But in this method we will directly store this observable in variable instead of subscribing.
      So we need variable of type observable. 
        eg :    var1:Observable(any);
    
    - Now our var1 is observable. We will subscribe this var1 in our .html file.
    - How--> using async pipe.

    - as observable contain multiple data object we will use ngfor loop.
        eg :    <div *ngFor = "let item of var1" | async >
                    {{item.id}} {{item.name}}
                </div> 

    - This is done for all data.
    - Suppose we have to see one record only on the basis of id passed in url
    - Then How-->
        Synax changes: 
            in serviceMethod :
            - pass url with id in get() 
              eg: 
                    getData(id:number): Observable<any>
                    {
                         return this.httpClientget(this.ApiURL+'/'+id);
                    }

            in componentMethod
            - pass id to serviceMethod as parameter
              eg :
                    getData()
                    {
                        this.var1 = this.contactService.getData(201)
                    }
            in template file
            - use ngIf instead of for
              eg :
                    <div *ngIf = "var1 | async as item; else loading">
                        {{item.id}} {{item.name}}
                    </div>
                    <ng-template #loading>
                        Wait. We are checking data one by one.
                    </ng-template>

        Now : As we know one method return object & another returns observable.
        But suppose : we need just string or we need id*100 number from webAPI then
        then How to convert that object/observable in a string/int 
        For this transformation we uses .map() method./ 


26. Post()
-----------
    - method for post data on service.
    - response of post is observable.
    - Syntax :
        we passes following parameters
        1. ApiURL
        2. body : Data which we have to post.
        3. options (optinal)
            i.   Header  : Pass header info. (for what : sothat server can identify data easily)
            ii.  observe : Defines whether we want Complete response / body / or events.
            iii. params  : Pass url of parameters

        Syntax :
            in service create a function which accept new record & pass it in .post() with ApiURL.
            This function returns a observable.In component we have to subscribe this observable.
            eg :    addItem(item: any)
                    {
                        return this.httpClient.post(ApiURL,item)
                    }

                    //here we can pass 3rd parameter option which is option we explain it in next lesson.

        OverAll Coding :
        here to write full code to accept data & post it on API we uses 2 concept.
        1. Form : to accept data
        2. service which having this additem() method.

            steps :
            create reactive form using formgroup to accept new data.
            inject formbuilder & group the form
            create form in template using formGroup & formControlName

            now data will be get in out form variable in .ts
            now subscribe addItem() method of service & pass the formVariable.value as parameter.
            NOTE: here we have to pass .value infront of formVariable to get value.

            In addiction we can call ShowItem() method in subscribe to see all records with updated one. However this is optional.
            Here we dont need any variable to get data as post method dont fetch any value.

            code :
            newItem : FormGroup;

            constructor(private formBuilder: FormBuilder, private itemService: ItemService)

            onNgInit()
            {
                this.newItem = this.formBuilder.group({
                    id : ['',Validators.required],
                    product  : ['',Validators.required],
                    type : ['',Validators.required]
                });
            }

            onSubmit()
            {
                this.itemService.addItem().subscribe(temp=>{}); //just Any httpClient method syntax.
                this.itemService.addItem(this.newItemForm.value).subscribe(temp=>{this.showItems()});
            }

            In template:
            
            NEW ITEM

            <form [formGroup]="newItem" (ngSubmit)="onSubmit()" >
            <input formControlName="id" placeholder="id">
            <input formControlName="product" placeholder="product">
            <input formControlName="type" placeholder="type">
            <button type="submit">Send</button>
            </form>

            ALL ITEMS

            <div *ngFor="let item of records  | async" >
                {{item.id}}  {{item.product}}  {{item.type}}
            </div>

27. put : 
    similar to push method.
    we here create one edit button on template with each record.

    logic : 
    1. whenerver we click update button id should be passed to updateRecord() function
        subtask: i. show data in textbox
                ii. update data on Api.

    1. fethch record by id & show it in textbox.
    2. edit record on Api by subscribing serviceMethod.

    we will create 2 seprate method & call it in our updateRecord(i) method wich takes id as parameter on this id basis we fetch specific record.

    updateRecord(id:number){
        this.getRecordById(id);
        this.editRecord();
    }

    here we will just call serviceMethod so we get data.
    But how to show that data in textboxes.
    --> we can assign that data to our controls using setvalue.
    
    getRecordById(id)
    {
        this.itemService.getRecordById().subscribe()            commen syntax.
        this.itemService.getRecordById(id).subscribe()          we have to pass id of required data in serviceMethod.
        this.itemService.getRecordById(id).subscribe(temp=>{})  we will get data in temp.
        this.itemService.getRecordById(id).subscribe(
            temp=>{
                this.newItem.controls.id.setvalue(temp.id);
                this.newItem.controls.product.setvalue(temp.product);
                this.newItem.controls.type.setvalue(temp.type);    
            }                                                   In this way we will assign data we got in temp to controls of our form newItemForm.
        )
    }

    Now we have to edit it on Api path.
    All data in our form. we just have to send it using serviceMethod.

     editRecord()
     {
         this.itemService.updateRecord().subscribe();                       commen syntax
         this.itemService.updateRecord(this.newItem.value).subscribe();     pass formGroup variable's value as parameter to service method.
         this.itemService.updateRecord(this.newItem.value).subscribe(
             temp=>{ 
                        this.var1 = this.itemService.getData() 
            })

       we can call getData() in subscribe to assign updated records  to our var1 which will be shown on template however this is optional    
     }


     All done??? not yet..
     as we use same submit button to insert records & update records which bind with onSubmit method.
     How our onSubmit method will distiguishes in it.

     we will create one variable ie. updateId which is initially null eg updateId = null;
     if updateId = null then it will call addRecord() method.
     else call updateRecord() method.

27. Delete : 
------------
 easiest method. : 
    create serviceMethod which take id as parameter & call delete in it.
    on template create button along each record which will call deleteItem(id) method by pasing id;
    deleteItem method will subscribe serviceMethod & pass id as number;


     Now when updateId changed??
     --> when user click on update button getRecordById(id) method will call to fetch & insert data in control
         So in this getRecordById(id) method we will set updateId = id where id is parameter of getRecordById method.
