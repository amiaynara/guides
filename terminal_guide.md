This will sound RANDOM, but believe me it is not!

Making an IP static on a LAN
-_---------------------------
    vim /etc/dhcpcd.conf
    
    interface wlan0
    static ip_address=192.168.0.104             # the static ip you want
    static routers=192.168.0.1                  # gateway ip
    static domain_name_servers=8.8.8.8 8.8.4.4  # you could give any
    reboot

Display all devices on network
----------------------------------
    sudo apt install arp 
    ping 192.168.0.255      # replace last section of gateway(.1) with .255
    arp -a


basic commands : believe me you will need them and also believe me you will forget them
--------------------------------------------------------

    pwd
    cd ~home_directory/
    wget url_for_.sh_file
    bash the_above_file.sh
    conda install jupyter
    conda init bash  # when powerline-shell: command not found while activating env
    pipx install powerline-shell # or may be sudo apt does the job as well
    jupyter notebook

vim Plugins
-----------
I am using tpope's "pathogen". 
    mkdir -p ~/.vim/autoload ~/.vim/bundle && \
    curl -LSso ~/.vim/autoload/pathogen.vim https://tpo.pe/pathogen.vim
    execute pathogen#infect()           # add this particular line to .vimrc , in my case to /.vim/vimrc
That is it ! now start installing plugins... git clone

Using Vundle
-------------
Better. 
requires more setup steps but pays in the long run.
    git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim
    set nocompatible              " be iMproved, required
    filetype off                  " required

    " set the runtime path to include Vundle and initialize
    set rtp+=~/.vim/bundle/Vundle.vim
    call vundle#begin()
    " alternatively, pass a path where Vundle should install plugins
    "call vundle#begin('~/some/path/here')
    Plugin 'VundleVim/Vundle.vim'
    "" other plugins go in here as well <-----------
    call vundle#end() 
in the command line of .vimrc
    :source  %
    :BundleInstall "" changes will be in effect immediately
Don't forget to do BundleInstall after ever Plugin addition.
Warnings: Do not stage the whole .vim => add a .gitignore with 'bundle/' in it. 


Wifi Control
--------------
nmcli is a command line tool controlling NeworkManager. 
        
        nmcli c             : list of saved connections
        nmcli d wifi list   : list of available wifi hotspots
        sudo iwlist <WifiInterface> scanning

        ifconfig -a         : to see the list of interfaces

creating & saving aliases
--------------------------
# temporary aliases
>> alias activate='source ../../bin/activate'

# permanent
alias activate='source ../../bin/activate'  # in ./bashrc


git-flow
------------------
https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow
    # starting a git flow
    git flow init

    # creating a feature branch
    git flow feature start feature_branch_name      # without using git-flow
                                                    # git checkout develop
                                                    # git checkout -b feature_branch
    # git flow feature finish feature_branch        # git checkout develop && git merge feature_branch

    # Once develop has acquired enough features for a release (or a predetermined release date is approaching), you fork a release branch off of develop.Creating this branch starts the next release cycle, so no new features can be added after this point—only bug fixes, documentation generation, and other release-oriented tasks should go in this branch. Once it's ready to ship, the release branch gets merged into master and tagged with a version number. In addition, it should be merged back into develop, which may have progressed since the release was initiated. In addition, it should be merged back into develop, which may have progressed since the release was initiated.
    
    git flow release start 0.1.0
    git flow hotfix start hotfix_branch     # hotfixes are done by forking the master
    git flow hotfix finish hotfix_branch    # similar to release branch, hotfix gets mergedd to both 'master' & 'develop'


    git flow release finish '0.1.0'

Publishing your python package
------------------------------

    create  a main folder say PACKAGE <--- is the main folder 
    create an 'src' folder inside
    create a setup.py as well. 
        - Add content to your setup.py..... 
                from setuptool import setup
                setup(
                name="helloworld",
                ...
                )
    Put your module.py(your codes) in the source folder. 
    we are ready with the bare minimum 

    test it by-
        $ python setup.py bdist_wheel       => creates a .whl file inside a new directory called dist, other things are 
        also created
    from gitignore.io get a gitignore for python code. 
    for further testing 
        $ pip install -e .      # install on your local system.
        Every time you change your setup.py file run this command to test if things are working properly.
    ----- wait before uploading to pypi -------

    create a git init
    add LICENSE.txt         ----------> choosealicense.com
    add classifiers to the setup() in setup.py      --> info about programming language. 
    Choice of formatting documentation. [ReStructured Text] or [Markdown] README.md is simpler and less powerful.
    Add more info to the setup.py as well. like long_description() 
    Add dependencies in setup() of setup.py as install_requires=["pandas~=1.2"...] <--- a list
    Add extra_require={dev=['lxml~=0.46']}
    Write tests---> You can use pytest. instead using REPL every time.  
    Add more info meta-data, like url github and so on. 
    to test
        tar tzf dist/.......tar.gz      # but LICENSE.txt and test_module.py will be missing 
        # therefore 
        $ pip install check-manifest
        $ check-manifest --create       # suggests you some lines to add just that to the file
        $ git add MANIFEST.in       
    $ python setup.py bdist_wheel sdist
    $ ls dist/
        # will give the wheel file and the tar file. 
    Pushing to PyPi
    use 'twine' -> separates build step from upload step and avoids complications
    $ pip install twine
    $ twine upload dist/*
        # we can upload here after giving the password and the username
    Now for production , [technically we are still not there ]
    use 'tox.ini' to test for various other python environments [3.6, 3.7]    
    $ pip install tox 
    
    you can write documentation and test as much as you can. 

    However this talk suggests to not use all the above steps. 
    Instead use 'cookiecutter' project that will create the whole set of files without much of this 
    hassle
        $ pip install cookiecutter
        $ cookiecutter gh:ionelmc/cookiecutter-pylibrary
            ... lots of questions ...
            ... done ! ...
            ... Tweak required ...
        other projects are :
        Poetry 
        Flit
        Hatch
---------------------------------------------
https://youtu.be/-WDV0-OB4fE
---------------------------------------------

# updating your package
do the changes-----
Now change the release version in setup.py from 0.0.5 to 0.0.6 for example 
now you create the build again 
    $ python setup.py bdist_wheel sdist         # it will create two more files in the dist/
    $ git add . && git commit -m 'Releasing second version'
    $ twine upload --skip-existing dist/*
    # 

## AWS CLI

```
$ pipenv shell
$ pipenv install awscli
$ aws configure
$ aws s3 ls     // should list the buckets present
$ aws s3 cp /source/file/location s3://name_of_s3_bucket/or/path/to/subfolder
$ aws s3 cp /source/file/location -       // Alternative to 'cat' command 
$ aws s3 sync /source/file/location s3://name_of_s3_bucket/or/path/to/subfolder       // much faster using parallel
$ aws s3 rm s3://path/to/required/file/folder --recursive                             // recursive in case of folder

```
**NOTE:** S3 does not have folder structure, it just presents the data on the UI as folder structure
