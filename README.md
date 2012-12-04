# Ombushop git Workflow
Git is a powerful tool. Git is your friend. In OmbuShop we like friends.

There are plenty of ways to use git to manage a project's source code, and OmbuShop's way is pretty simple.

## Background
### The ```master``` branch
We work by making changes to this branch, and then issuing the ```git commit``` command to record those changes, one after another, then, we ```git push``` them to Github to share them with the team, for backup purposes, to deploy the code to the server and our users. So far so good.

**Figure 1**

	           git commit
	               |   
	[local env]    C1<--C2<--C3<--CN <= master
	                    |
	                git commit

### Doing some work

Now lets assume this: it's monday, we're all getting ready to start another epic week of work, and Ernesto and Leandro start working. After a couple hours, this is what Github, Ernesto and Leandro's repositories all look like, before any ```git push``` command is run:

**Figure 2**

	 [Github] C1<--C2<--C3<--C4 <= master
	[Ernesto] C1<--C2<--C3<--C4<--C5<--C6 <= master
	[Leandro] C1<--C2<--C3<--C4<--C7<--C8 <= master


Or to simplify it:

**Figure 3**

	                 /<--C5<--C6 <= Ernesto/master
	C1<--C2<--C3<--C4 <= Github/master
	                 \<--C7<--C8 <= Leandro/master

Note: This means that commits 1 thu 4 are present everywhere, commits 5 and 6 only in Ernesto's laptop's master branch, and commits 7 and 8 in Leandro's master branch. Also, the arrows point back in time, meaning C1 was the first commit, followed by C2 and so on. Captain Obvious out.

### Time to push: Fast Forward

The wole point of this tutorial is to teach a way to keep each local repository "fast-forward" with the copy in Github, and vice versa.

Fast Forward is something git does when, between two branches, the first new commit in one of the branches follows the last commit in the other branch. Awful complicated to write, but if you take a look at Figure 3, it's pretty obvious: ```Ernesto/master``` and ```Leandro/master``` are both **fast forward** with ```github/master```. Don't panic, let it soak.