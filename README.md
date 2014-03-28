Use Git for all your SVN projects starting now
=======


Utilize Git with a SVN repository

# GIT SVN Workflows #

## Basic Changeset Process ##

1. **GIT SVN Checkout**

	    $ git svn clone -s https://tools.sapient.com/svn/MGPT

2. **Update local repository**

	    $ git svn fetch

3. **Add file**

	    $ git add [file]

4. **Commit file in GIT**

	    $ git commit -m '[msg]'

5. **Prepare for push / get remote repository changes**

	    $ git svn rebase

6. **Push to remote SVN repository**

	    $ git svn dcommit


## Switching Between Branches ##

1. **Fetch Latest Remote Branches**
	
	    $ git svn fetch

2. **Create Branch**

	    $ git svn branch -m "[msg]" branch_name

3. **Map local branch to remote branch**

	    $ git checkout -b branch_name remotes/branch_name

4. **Switch to New Branch**

	    $ git checkout branch_name

5. **Reset Branch Instance to New Branch**

	    $ git svn rebase

6. **Verify SVN path**

	    $ git svn info

        Path: .  
        URL: https://[repository.url]/branch_name  
        Repository Root: https://[repository.url]  
        Repository UUID: d56a3af8-c092-443f-9987-f536df04eac3  
        Revision: 120
        Node Kind: directory
        Schedule: normal
        Last Changed Author: jlaw
        Last Changed Rev: 119
        Last Changed Date: 2014-03-18 15:32:24 -0700 (Tue, 18 Mar 2014)

## Removing Empty Directories ##

1. ** Create file in root of empty directory **

        $ touch test.md

2. ** Add file to Git history **

        $ git add test.md

3. ** Remove file utilizing Git **

        $ git rm test.md

4. ** Commit file via Git **

        $ git commit -m 'removed file/directory'

5. ** Git - SVN Commit of Directory back to Remote **

        $ git svn dcommit --rmdir


## Shortcuts (aliases) ##

The following should be placed in ~/.gitconfig.

    [alias]
        ci = commit
        co = checkout
        st = status
        br = branch
        lg = "log --pretty=format:'%h - %an, %ar : %s' --stat"
        lgg = "log --graph --decorate --oneline"
        stash-unapply = !git stash show -p | git apply -R
        
        # Show unpushed files
        notpushed = log --branches --not --remotes
        
        
        ###########################
        # git-svn specific commands
        ###########################
        
        si = svn info
        sr = svn rebase
        sf = svn fetch
        sfa = svn fetch --fetch-all
        
        # git-svn version of Pull/Push
        up = !"echo 'Pull'; git svn rebase; echo 'Push'; git svn dcommit --rmdir"
        


        # SVN checkout process: 
        # 1. Checkout {{branch}}
        # 2. Rebase
        # 3. SVN info
        # 4. Status
        #
        # Alias argument:
        # $1: branch name
        #
        # Changing to existing branch trunk
        # COMMAND: git svnco $1
        # EXAMPLE: git svnco trunk
        #
        # NOTE: Branch must have been previously checked out and tracked by using the process set up in the 'snewbr' alias.
        #
        svnco = !"f() { echo 'Checking out'; git co $1; echo 'Rebasing'; git svn rebase; echo 'Show SVN info & git status'; git svn info; git svn status; }; f"
        


        # Creating new branch from currently checked out branch
        #
        # Alias takes two arguments:
        # $1: new branch name
        # $2: commit message
        #
        # Current checked out branch 'ryans-cool-code'
        # COMMAND: git snewbr $1 $2
        # EXAMPLE: git snewbr ryans-cooler-code
        #
        # This creates a new remotes branch called 'ryans-cooler-code' then checks out local and tracks remotes branch.
        #
        snewbr = !"f() { echo 'Fetching remote data'; git sf; git svn branch -m '$2' $1; git co -b $1 remotes/$1; git sr; git ba; }; f"
        



        # Checking out new remote branch created by other author:
        # 1. Fetch
        # 2. Checkout (new branch flag)
        # 3. Rebase
        #
        # Alias argument:
        # $1: branch name
        #
        # Remote branch: remotes/ryans-cool-code
        # COMMAND: git sbr $1
        # EXAMPLE: git sbr ryans-cool-code
        #
        sbr = !"f() { echo 'Fetching SVN data for most current branch list'; git svn fetch --fetch-all; echo 'Checkout and track remote branch $1'; git checkout -b $1 remotes/$1; echo 'Rebase branch after checkout'; git svn rebase; echo 'Display current state of local/remote branches'; git branch -a; }; f"
        


        # Remove branch from repository: local and remote.  Requires 'rr' to return current repo URL.  Process will ask for a commit message in external editor.
        #
        # Alias argument:
        # $1: branch name
        #
        # COMMAND: git rmbranch $1
        # EXAMPLE: git rmbranch jason
        #
        rmbranch = !"f() { svn rm `git rr`/branches/$1; git branch -D $1; git branch -D -r $1; rm -rf .git/svn/refs/remotes/$1; }; f"
        
        # Display Repository Root URL
        rr = !git svn info | grep 'Repository Root' | awk '{print $NF}'



