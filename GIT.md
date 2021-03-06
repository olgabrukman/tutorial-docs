[REMOTE BRACNHES](https://git-scm.com/book/en/v2/Git-Branching-Remote-Branches)

Remote branch name: <remote>/<branch>, e.g. origin/master, origin/iss53.
*origin/master* is a remote branch, *master* is a local branch you work on, *origin/master* and *master* are not the same.

Fetch down all the changes on the server that you don’t have yet, it will not modify your working directory at all:
```ssh
git fetch <remote brunch name>
git fetch --all
```

Local branch is not automatically sycnhronyzed with a remote branch it has been copied from.
Pushing local branch to a remote branch:
```ssh
git push <remote> <branch> //means push local <branch> into <remote>/<branch>
``` 
Checking out a local branch from a remote-tracking branch automatically creates 
a *tracking branch*. The branch it tracks is called an *upstream branch*. Tracking branches 
are local branches that have a direct relationship to a remote branch.
```ssh
#creates local <branch> that traces the remote <branch> and switches to the local <branch>
git checkout --track <remote>/<branch> 
```
See what tracking branches you have set up:
```ssh
git branch -vv
```

If you already have a local branch and want to set it to a remote branch you just pulled down, 
or want to change the upstream branch you’re tracking, you can use the *-u* or *--set-upstream-to* 
option to git branch to explicitly set it at any time:
```ssh
#Current branch set up to track remote branch serverfix from origin.
git branch -u origin/serverfix 
```

Fetch down all the changes on the server that you don’t have yet AND modify your working directory:
```ssh
#git fetch +git merge
git pull 
```

Delete remote branch *origin/serverfix*:
```ssh
git push origin --delete serverfix
```

```ssh
#make a directory to be a local git directory
git init
git status
git add [file|dir]
git commit -m "<commit message>"

#compare changes in the working directory against a previously committed version
# By default the command compares the working directory and the HEAD commit.
git diff 
git diff <commit>
git diff<file name>

#create remote repo
git remote add origin [remote repo name]#where origin is a friendy name of a remote repo
git push origin [local branch name we are working on]
git pull origin [local branch name we are working on]

#see changes log
git log
git log --oneline
git show

#git fetch (stores fetched files in directory remotes/<remote-name>/<remote-branch-name>)  + git merge remotes/<remote-name>/<remote-branch-name> master
git pull

#see all remote branches 
git brach -r 

#undo
git checkout . #replace everything in your current directory to to the last comminted version
git reset HEAD . #you've staged files for commit, reset will return them to pre-staging state, reset commit tree
git reset --hard <commit-hash> #allows you to go back to any commit state, combine both git reset and git checkout in a single command
git reset HEAD~1 #remove last commit
git revert HEAD --no-edit #allows you to undo the commits. The command will create a new commit which has the inverse affect of the commit being reverted.
#re-writing history in Git is an anti-pattern. If you have pushed your commits then you should create new commits to undo the changes as other people might have made commits in the meantime.
git revert HEAD...HEAD~2 #revert the commits between HEAD and HEAD~2.

#CONFLICT RESOLVING
git checkout --ours staging.txt or git checkout --theirs staging.txt
git add .
git commit --no-edit #when you wish to use the default commit message.
command git reset --HARD head #If you want to revert in the middle of a merge and try again then use the to go back to your previous state
git pull --no-edit origin master
git log --all --decorate --oneline
git pull --rebase

WORKING USING BRANCHES: MERGE
git checkout -b <new branch name> #create new bracnh and checkout it (git branch <new branch name> <starting branch> + git checkout <new branch name>)
git branch  (-va)#list branches, -a -include remote branches, -v - include head commit messages

#Merge new_brach into master
git checkout master #checkout target branch
git merge new_branch
git push origin master
git branch -d <branch_name>

#WORKING USING BRANCHES: REBASE
git checkout cool-feature
git rebase master
git checkout master
git merge cool-feature


#HANDLING BUGS
git diff HEAD~2 HEAD
git log --oneline #overview of commits
git log -p #display diff per each commit
git log -p -n 2 #show last 2 commits
git log -since="2 weeks ago" -until="1 day ago"
git log --grep="Initial"
git blame list.html #show last person who touched the file
git blame -L 6,8 list.html #show last person  who modified the lines 6,8 in the file

#CHERRY PICKING
We have a branch with several commits. We are interested only in some of the commits, thus we 
don't want to merge the branhc into master. Thus we will do cherry-pick. Switch to master and cherry-pick desired commits:
git cherry-pick <hash-id|ref>

#You can refernce commits using "~#": new_branch~3 
git cherry-pick --abort #abort last cherry-pick

#In case of a conflict:
git cherry-pick new_branch~1
git status
git diff
git checkout --theirs list2.html
git cherry-pick --continue
 
#REWRITING HISTORY
git rebase -interactive #Re-writing the repositories history 
git rebase --interactive --root #opens vim for interactive editing of the git commit tree, change the command for a commit to change, which will open another editing tool to allow the change
git commit --amend #change last commit message

#We want to make 8 commits to be one:
#change last 7 commands to "squash"
git rebase --interactive HEAD~8

#Split commit:
git rebase --interactive HEAD~1
#change command to edit and remove the last commit: 
HEAD^ = HEAD~1= HEAD~~
git reset HEAD^ 

#Do the operations that replace the original commit
git rebase --continue #complete the change 








