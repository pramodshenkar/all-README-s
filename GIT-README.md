Initialize git repo:
  git init

check status:
  git status

add to repo:
  git add . or git add --a

End repo:
  rm -rf .git


Git Ignore :
-------------
  create file .gitignore using command touch .gitignore
  How to ignore diff files :
    - simple file as Photo.jpeg
    - all files with extension .log
    - all folder with name dir
    - only 'files' folder inside 'folder' Dont ignore 'files' folder at root level
      ie only ignore /folder/files
         dont ignore /files

    -->> .gitignore
    photo.jpeg
    *.log
    docs/
    /files/


  Git staging area & working dir:
  --------------------------------
    when we use add. after modification then file status get changed from
    working -> to -> staged (staging area)

    if we check status it will say modified.
    either in red color or green color.
      red means in working not staging yet.
      green means working is completed now staged.

    suppose
          1.we modify 1st .txt & check status : modified (red)
          2.       then add . to staging area : modified (green)
          3.        now again do modification : modified (green) &&
                                                modified (red)
              [This is because we staged file but again do changes.]

          4.Now suppose we want to see differences in file after staging (1.) && modification after staging (3.)
            then we will use command : git diff
            it will show us lines changed after modification done after staging.

          5.suppose we want to see diff in file at commit & modification done at commit
            then we will use command : git diff --staged
            it will show diff in commit & staging after commit.


Direct commit:
--------------
    - Till date we see that we first have to stage files using add .
      & then only these staged files get stored on commit.
    - But if we want to do commit without staging then we can do it
      with command : git commit -a -m 'commit-name'

    - Note : this commit only add tracked && modified files to commit.
      It cant add untracked files


Rename & delete file in git:
-----------------------------
  commands :
    1.rename : git mv 'prev-name' 'changed-name'
    2.delete : git rm 'file-name'

    As we can do rename/delete manually then why git?
    if we rename 2nd.txt to 3rd.txt manually
    then git thinks we deleted 2nd.txt & create new file 3rd.txt until staging is done.
    Once we staged it then it show status as renaming instead of deleted 2nd.txt & created 3rd.txt

    So avoid manually doing rename/delete and then staging
    we directly use rm/mv command. these command automatically doing staging.


Git tracking .gitignore file:
-----------------------------
    - Suppose we tracking one file ie 2nd.txt from previously.
      But now we add it to gitignore and do commit.
      & then do changes to it (2nd.txt);

    - will git track it????
      -->> yes still git tracking it. because of its cache.

    - To stop this tracking we have to clear its cache
      command :
          git rm --cached 'file-which-we-want-to-stop-tracking'


Rename commits:
Setting alias to command:
-----------------

Unstage file:
    git restore --staged 'filname'

Go back to last staging area from modified:
    git checkout -- filename.txt
    PS: it do nothing when you already in staging area.

Go back to last commit:
    git checkout -f

Remote Repository :
------------------
  - create repo at gitlab/github
  - follow commands given at that repo.

Branching :
-----------
    - Create branch using : git checkout -b 'branch-name'
    - Now we have 2 branches ie MASTER & 'branch-name'
    - to swich between them use : git checkout branch-name OR git checkout MASTER

Scene :
      1.create simple webapp. do commit as webApp 1.0
      2.create new branch as navbar
        create simple navbar. do commit navbar 1.0
      3.switch to master. do changes and commit as webApp 2.0
      4.switch to navbar branch. do changes in navbar & commit as navbar 2.0
      5.Now merge navbar branch in master.
      We will get final webApp Now do commit as 'webApp Final'
      Done :

      commands :
        git init
        git add .
        git commit -m "webapp-1.0"
        git checkout -b "navbar"
        git add .
        git commit -m "navbar-1.0"
        git checkout master
        git add .
        git commit -m "webapp-2.0"
        git checkout navbar
        git add .
        git commit -m "navbar 2.0"
        git checkout master
        git merge navbar
        git add .
        git commit -m "webapp-4.0 Final"


Solve conflicts:
-----------------
    - It will automatically shows options on vscode. choose it properly.
    - now stage it using add .
    - now commit it.
    Thats all!!!

Branch Management:
------------------
    - To show all branches :
          git branch
    
    - To show all branches with their last commit.
          git branch -v

    - To show which branches are merged & Not.
          merged     : git branch --merged
          not merged : git branch --no-merged

    - To delete branch.
          if we want delete merged branch   : git branch -d branch1
          if we want delete unmerged branch : git branch -D branch1
