+++
date = "2018-08-04T23:59:18-07:00"
draft = false
title = "Using git submodules"
+++

Suppose you have a repository called *mainrepo* and another repository called *subrepo* that you want to use inside *mainrepo*. You can just clone *subrepo* inside *mainrepo* and use it.
However, if subrepo gets updated frequently, then you will have to clone it everytime and replace it inside *mainrepo*. If you want to view the history of commits made to *subrepo* , then you need to go to the remote repository where you cloned it from and see it there. And if you make any changes to the *subrepo*, then those changes will be part of your *mainrepo* and not your *subrepo*. You can see how this can get difficult to maintain. For cases like these, using submodule makes it easier to manage.

### Adding a submodule
After cloning the *subrepo*, we can add it as a submodule within our *mainrepo*.

```
git submodule add https://github.com/jiteshvm/subrepo subrepo
```

Its important to know what changes were made to our git repo because removing a submodule is not as straightforward as adding a submodule.

Lets first do a git status in the mainrepo

```
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	new file:   .gitmodules
	new file:   subrepo
```

The .gitmodules file contains an entry for each submodule added to this repository :

```
[submodule "subrepo"]
	path = subrepo
	url = https://github.com/jiteshvm/subrepo
	branch = master
```
There's also a new entry called *subrepo*. If you check the directory, you wont find a file with that name. This is because when a submodule is added, git doesnt track the contents of the submodule directory from mainrepo. Instead, it treats it as a file which is used to track the commit to which this submodule currenly points at. You can see this by either doing a diff or checking the contents of .git/modules/subrepo/FETCH_HEAD file.

```
$ git diff --cached subrepo
diff --git a/subrepo b/subrepo
new file mode 160000
index 0000000..ca70dc9
--- /dev/null
+++ b/subrepo
@@ -0,0 +1 @@
+Subproject commit ca70dc947a4611af400cf4cbb70d464c7302fda0
```

It also created a directory called subrepo inside .git/modules/ and an entry is added in .git/config file :

```
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
	ignorecase = true
	precomposeunicode = true
[submodule "subrepo"]
	url = /Users/jitesh/gitsubmodules/subrepo
	active = true
```

### Removing a submodule
There is no straightforward way to remove a submodule from the mainrepo. To completely remove a submodule, the .gitmodules file, the .git/config file and the modules directory all need to be updated. The following 3 commands does that :

```
$ git submodule deinit -f subrepo/
Cleared directory 'subrepo'
Submodule 'subrepo' (/Users/jitesh/gitsubmodules/subrepo) unregistered for path 'subrepo'

$ git rm -f subrepo/
rm 'subrepo'

$ rm -rf .git/modules/subrepo/

```

### References

<https://git-scm.com/book/en/v2/Git-Tools-Submodules>