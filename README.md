# git-subtree-helper
Helper scripts for using git subtree command

In this project are two scripts that ease use of the subtree command. The two scripts are:
1. `subprojects`
1. `merge_from_superproject`

This repo is not actually necessary: you should embed the two scripts into your library project and then operate from there.
In following documentation, 'library repo' refers to your library project not this one directly.

By 'library project' I just mean the sub-project that will be utilised as a component of other repos.

Robert Muil.

## Including your library repo as a sub-project in another repository:

The other repository will be referred to here as the 'superproject': this is a working copy of the project into which you want to include the library repo.

### installing `subprojects` script into your superproject

Copy the 'subprojects' script into your superproject, make it executable, and commit the changes. You can also edit the script in between to customise for project:

    $ cd <superproject>
    $ wget -O subprojects 'https://raw.githubusercontent.com/robertmuil/git-subtree-helper/master/subprojects' && chmod +x subprojects
    $ [optional] vi subprojects...
    $ git add subprojects && git commit -m"add subprojects script to manage library repos"


NB: if the above line doesn't work (if the downloaded file is HTML), then navigate to the `subprojects` script in this repo, log in to github, then download the raw `subprojects` script, and make it executable manually.

### creating sub-project

	$ cd <superproject>
	$ ./subprojects create <library_name> '<git url to library project>'
	
NB: you can use any git url here: it might be convenient to use a local file URL if the library project will be under lots of development because then you can work completely offline.

### bringing changes from upstream

	$ ./subprojects pull

### pushing local changes back upstream

	$ ./subprojects push
	

## Setting up your library repo to use as a sub-project:

The `merge_from_superproject` script aids bringing downstream changes into the library repo. To set it up to use:

1. copy the `merge_from_superproject` script into library repo's working copy, root folder
1. add it to the `.gitignore`

i.e.:

    $ cd <libraryrepo>
    $ wget -O merge_from_superproject 'https://raw.githubusercontent.com/robertmuil/git-subtree-helper/master/merge_from_superproject' && chmod +x merge_from_superproject
    $ echo "merge_from_superproject" >> .gitignore 

NB: don't want to store the script in the repo because it'll be confusing inside the superproject. It's just a helper to use from the library's working directory.

### bringing in changes from superproject

just use the `merge_from_superproject` script once you've 'pushed' the changes from subproj!

### splitting out a library from a folder within existing repo

https://help.github.com/en/articles/splitting-a-subfolder-out-into-a-new-repository


## git subtree

If you don't want to use the scripts I wrote (they're possibly buggy), then
here's how to do it all directly with the subtree command.

The commands are long, but this works very well. You can set up aliases (either
in your shell or with git itself) to make these common tasks more handy).

To inform yourself about the command do:
	`$ git subtree --help`

### creating sub-project
To include this in a project using git subtree, do the following: (replace 'library' with your own library name)

2. Add a remote repository reference (this is optional but recommended):  
	`$ git remote add library_origin <library_git_url>`
3. Import the project as a subtree at `<path>` with:
	`$ git subtree add -P <path> --squash -m "Add library project as subtree" library_origin master`

### bringing changes from upstream
Then, in the future, to bring your local copy up to date, do:  
	`$ git subtree pull -P <path> --squash -m "Updated to latest from library project" library_origin master`

### pushing local changes back upstream
To push local changes back to this (upstream) project:

1. Extract the local changes in the analysis project to a separate branch:  
	`$ git subtree split -P <path> --annotate="(split)" -b library_update`
2. Push that branch up to the upstream repository (as an 'update' branch here - could also push to master but a bit dangerous)  
	`$ git push library_origin library_update:update_from_superproject`
3. In this project, merge the update_from_superproject branch to master:  
	`$ cd <working directory for this subproject>`  
	`$ git checkout master && git merge update_from_superproject`

## Other Mechanisms

If you instead decide to use subtree *merge strategy* (which is different to the git subtree *command*), here is canonical advice:
https://www.kernel.org/pub/software/scm/git/docs/howto/using-merge-subtree.html

Be careful though: I first followed the following, and ended up in a horrible state:
http://git-scm.com/book/en/Git-Tools-Subtree-Merging

It should also actually be possible to incorporate projects as a
'submodule', but I have read several blog entries stating that submodules
are not recommended and can cause major problems.
