---
title: "Handle conflicts"
description: "How to handle git conflicts using command line"
authors: [stijnvanhoey]
date: 2017-10-18
categories: ["version control"]
tags: ["git", "version control"]
---

## Fix merge conflict with a pull request

You have made some changes to a feature branch. Make a pull request on the server. The standard case of automatic merge is not possible. Push your latest changes from the feature branch to the server.

Locally on your computer:

```
git fetch origin
```

Rebase your feature branch with your master

```
git rebase origin/master
```

Git will now state that there are merge conflicts. These will look like this:

``` 
<<<<< HEAD:<some git nonsense>
        This part is from a version of this file
=====
	This is from another version of a file
>>>>> blahdeblahdeblah:<some more git nonsense>
```

The <<<<<, ===== and >>>>> markers show which lines were changed simultaneously. In order to remove the conflict, choose which line you want to keep (first or second), remove the other line and the markers, and finally commit the result.

Add your files that you fixed.

```
git add <fixed files>
```

Continue with your rebase

```
git rebase --continue
```

If more troubles occur, fix them, add them, and do a ```git rebase --continue```

Force push your branch to the server. (force because you changed the commit)

```
git push -f origin branchname
```

On the server, you can now automatically close your Pull Request.
