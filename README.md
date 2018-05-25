# git-subtree-helper
Helper scripts for using git subtree command

In this project are two scripts that ease use of the subtree command. The two scripts are:
1. `subprojects`
1. `merge_from_superproject`

This repo is not actually necessary: you should embed the two scripts into your library project and then operate from there.
In following documentation 'this repo' refers to your library repo not this one directly.

Robert Muil.

## How to include your library repository as a sub-project in another repository:

The other repository will be referred to here as the 'superproject'.

### installing `subprojects` script into your superproject

Simply copy the 'subprojects' script into your superproject (a working copy of the project into which you want to include this repository), and make it executable:

	$ cd <superproject>
	$ wget -O subprojects 'https://github.com/robertmuil/git-subtree-helper/browse/subprojects?raw' && chmod +x subprojects
NB: if the above line doesn't work (if the downloaded file is HTML), then point your browser to the address, log in to github, then download the raw subprojects script, and make it executable manually.

### creating sub-project

	$ cd <superproject>
	$ ./subprojects create <library_name> '<git url to library project>'

### bringing changes from upstream

	$ ./subprojects pull

### pushing local changes back upstream

	$ ./subprojects push

## git subtree

If you don't want to use the script's I wrote (they're possibly buggy), then
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

If you instead decide to use subtree merging, here is canonical advice:
https://www.kernel.org/pub/software/scm/git/docs/howto/using-merge-subtree.html

I first followed the following, and ended up in a horrible state:
http://git-scm.com/book/en/Git-Tools-Subtree-Merging

It should also actually be possible to incorporate projects as a
'submodule', but I have read several blog entries stating that submodules
are not recommended and can cause major problems.
