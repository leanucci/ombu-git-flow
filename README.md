# Ombushop git Workflow
Git is a powerful tool. Git is your friend. In OmbuShop we like friends.

There are plenty of ways to use git to manage a project's source code, and OmbuShop's way is pretty simple.

## **TL;DR**
Don't use ```git pull```. It messes with our pretty commit tree.

### Before recording new commits:

```git fetch```

```git merge origin/master --ff-only```

#### If the merge is not possible due to non fast forward local branch:

```git rebase origin/master```

### Before pushing your commits to github:

```git fetch```

```git rebase origin/master```

The rationale for this is:

## Background
### The ```master``` branch
We work by making changes to this branch, and then issuing the ```git commit``` command to record those changes, one after another, then, we ```git push``` them to Github to share them with the team, for backup purposes, to deploy the code to the server and our users. So far so good.

**Figure 1**

	           git commit
	               |   
	[local env]    C1<--C2<--C3<--CN <= master
	                    |
	                git commit

### After one day of work...

Now lets assume this: it's monday, we're all getting ready to start another epic week of work, and Ernesto and Leandro start working. After a couple hours, this is what Github, Ernesto and Leandro's repositories all look like, before any ```git push``` command is run:

**Figure 2**

	 [origin] C1<--C2<--C3<--C4 <= master
	[Ernesto] C1<--C2<--C3<--C4<--C5<--C6 <= master
	[Leandro] C1<--C2<--C3<--C4<--C7<--C8 <= master


Or to simplify it:

**Figure 3**

	                 /<--C5<--C6 <= Ernesto/master
	C1<--C2<--C3<--C4 <= origin/master
	                 \<--C7<--C8 <= Leandro/master

Note: This means that commits 1 thu 4 are present everywhere, commits 5 and 6 only in Ernesto's laptop's master branch, and commits 7 and 8 in Leandro's master branch. Also, the arrows point back in time, meaning C1 was the first commit, followed by C2 and so on. Captain Obvious out.

### Time to push: Fast Forward

The wole point of this tutorial is to teach a way to keep each local repository "fast-forward" with the copy in Github, and vice versa.

A ```branch A``` is **Fast Forward** with another ```branch B``` when the first new commit in ```branch A``` follows the last commit in ```branch B```. 

Awful complicated to read. If you take a look at Figure 3, it's pretty obvious: ```Ernesto/master``` and ```Leandro/master``` are both **fast forward** with ```origin/master```. Don't panic, let it soak.

## Making branches fast-forwardable

When getting updates from origin, instead of ```git pull``` use  ```git fetch``` followed by ```git merge origin/master --ff-only```. This ensures that your local ```master``` branch will be updated only with fast forward merges.

If the merge command fails, it means that your local repository and the one in github have diverged in commits. Since the reference is ```origin/master```, those changes take precedence over your local changes.

## Rebase
Let's go back to our example: Nestor and Leandro had commits:

**Figure 3**

	                 /<--C5<--C6 <= Ernesto/master
	C1<--C2<--C3<--C4 <= origin/master
	                 \<--C7<--C8 <= Leandro/master

After Ernesto is done, he pushes, leaving us with

**Figure 4**

	C1<--C2<--C3<--C4<--C5<--C6 <= Ernesto/master AND Github/master
                   \<--C7<--C8 <= Leandro/master

What happened? According to what we said before, ```Ernesto/master``` branch was fast-forward with ```origin/master```, so when he pushed, his commits were added seamlessly to the commit stream.

But now Leandro is done and wants to push, and this is what he gets:

<code>
To git@github.com:etagwerker/ombu_store.git

 ! [rejected]        master -> master (non-fast-forward)

error: failed to push some refs to 'git@github.com:etagwerker/ombu_store.git'

To prevent you from losing history, non-fast-forward updates were rejected

Merge the remote changes (e.g. 'git pull') before pushing again.  See the 'Note about fast-forwards' section of 'git push --help' for details.
</code>

<img src="http://cdn.memegenerator.net/instances/400x/31279473.jpg" alt="Y U NO Fast-Forward?">

This happens because ```C7``` can only be fast forward if the last commit in github is ```C4```, but now the las commit is ```C6```.

To our aid comes ```git rebase```.By issuing ```git fetch``` we download the remote changes Ernesto pushed, without merging them into any branch. After that, we tell git to rebase those changes into our branch, so that our commits get added on top of the latest master, making the repositories look like this: 

**Figure 5**

	C1<--C2<--C3<--C4<--C5<--C6<--C7<--C8 <= Leandro/master
                             \ <= Ernesto/master AND Github/master

As we can see, now ```leandro/master``` looks like if ```C7``` had been committed on top of ```C6``` instead of ```C4```. ```Leandro/master``` is now fast-forward!!

This helps prevent ugly things like this: 

**Figure 6**

	C1<--C2<--C3<--C4<--C5<--C6<--C9 <= Ernesto/master AND Github/master
                   \<--C7<--C8<--/ <= Leandro/master

```C9``` ends up being a "merge commit", which uglifies the commit history.