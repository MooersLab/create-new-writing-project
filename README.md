![Version](https://img.shields.io/static/v1?label=create-new-writing-project&message=0.1&color=brightcolor)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)

# Protocol for creating a new writing project

The objective here is to create a project folder in the home directory and populated with template files.
Selected files from this home directory will be put under Version Control using a local and remote repository.
This could be done directly from the project directory. Still, setting up a duplicate subfolder underneath a folder I use to store local copies of all my private GitHub repositories is safer.
This eases starting over if you must copy everything back from the remote repository due to some foul-up with git.

This protocol also sets up a workspace in treemacs inside the Emacs so that you can easily access the files in the folder after switching to the workspace for this project.
Treemacs makes life easier, although it is not essential because there is also dired in Emacs.
I have shared elsewhere a bash function that starts Emacs with a specified workspace given in the terminal so that you do not have to switch to the selected workspace after launching Emacs. 
The first set of steps can be followed without using Emacs.

I have gone through these steps many times since I started creating a bash function to automate the committing of the updated files in a project about a half year ago. 
I should probably write a detailed protocol for my future self if I have an extended period during which I do not create a new writing project.
After a long break in creating new writing projects, I could easily forget these 21 steps.
I have many unfinished writing projects that I need to make progress on.
I anticipate not creating many new writing projects as soon as I catch up.

I enter the push function name, `p` followed by the project number, to commit changes to the files, and then I push the updated files to the remote repository. 
I have the option of supplying a comment, but I often use the default message of `Updated` when I am feeling lazy.
I could figure out how to do these git operations inside Emacs by using Emacs's magit program, but this workflow works for me. 
I like it because I can update the remote repository for the project at hand by issuing a single command.

This protocol supports using a four-digit project number to track each writing project.
This project number is incorporated into the project folder name by starting the name.
If you have autocompletion enabled in your terminal, you enter the project number, and you will get autocompletion of the remaining part of the folder name.
Hitting tab or return is often sufficient to pop you into the project folder without using the `cd` change directory command.
I store the project numbers in an SQLite database along with the project directory name.
I include some fields for describing in detail the title of the manuscript if it is a writing project and other metadata.
I have reserved numbers 0000-0999 for manuscripts and 1000-1999 for grant applications.
I will describe this project management approach elsewhere, but that is enough to get you started.
The first 15 steps below up to creating a treemacs workspace are independent of Emacs and could be used with any other text editor or word processor.

1. Assign the project number by finding a vacant number in your project database.
2. Assign a project directory name and create the project directory in the home directory
3. Navigate to the project directory and run the bash functions `manorg`, `logorg`, and `abiborg` (see the code below). There are analogs for LaTeX if you prefer writing in LaTeX.
4. Create a subdirectory for storing your figures for this project (e.g., figs0574). You should probably keep these under Version Control, too. You want to keep them out of the way by storing them in the subfolder. Their number may grow over time, even though you may only select a subset for the manuscript. Some of the figures you do not use in the manuscript may be useful in slideshows, or at least they can inspire new and better figures. 
5. Set up the push function for the project while also creating a repository subfolder in 6114BlaineMooersLabGitHubRepos, which is where I store all the local repositories for the corresponding repositories on my private GitHub site. Run that alias `epa` to open up the collection of push functions and add a new one.
6. Navigate to the repository folder and run the function commands individually as you edit them.
7. Create a README.md by using the touch command, and then populate the README.md file with the contents inserted by the `insert readme` voice snippet. I run this voice snippet in my online accounts for 750Words (or Write Honey if I have exceeded the daily 5000-word limit of 750words). 
8. Edit the README.md to customize it for the project.
9. Navigate to your private GitHub site and create a new repository using the project folder name. You will get a list of commands to run locally if you want to use the local repository to populate the remote one. This is the fastest way to set up the remote repository. 
10. Back in the local repository folder, run `git init && gac README.md "Init commit." && git branch -M main` to initialize the local repository and to make an initial commit of the README.md file.
11. Note that `gac` is a bash function that combines `git add` and `git commit -m` into a single operation. See its code below.
12. Next, run `git remote add origin https://github.com/??????/???????.git`. Replace the URL with the appropriate one for your private GitHub repository.
13. Now run `git push -u origin main` to push the README.md to the remote repository.
14. Now you must commit the remaining subfolders and files in the local folder and then push these to the remote repository. You do this by sourcing the plain text file that stores the push functions by running the alias `sz`, which sources the `.zshrc` file that, in turn, sources all the files that contain Bash aliases and functions.
15. Run the push function for the project (e.g., p0574) to push the remaining files and folders to the remote repository.
16. Run the command to create a treemacs workspace (C-c C-w a) and paste the project directory name.
17. Now switch to this new project (C-c C-w s). You will be prompted for the directory for this project. It will default to the current directory, which is often incorrect. You can backspace over the current project directory and use autocompletion to insert the desired project directory.
18. Open the new project.s log file and start filling out the project initialization information. This part of the project can take 3 to 5 hours to do well. You can postpone doing this to a later point, but it is best to tackle this as soon as possible before you can try to do any writing in the manuscript. 
19. Record a diary entry for the current date in the daily log section, and note that one of your accomplishments was creating this project.
20. Record your effort on this project in your time-tracking database. Time tracking is a hard habit to adopt, but if you do not measure your time, you cannot manage it well. It isn't easy to manage things that you do not measure. 
21. If you care about your daily word count, turn on `wc-mode` and get the initial word count before completing the project initialization information. This mode will report back the change in word count. Tracking word count in this fashion is useful when you are trying to build up a writing habit. 

After years of building a daily writing habit, I find that tracking the time spent on each project is good enough because there is far more to preparing academic documents than just generating words. Those other tasks have to get done, whereas focusing on word count will lead to optimizing for generating prose, which is often not the limiting factor for me. 

Because I dumped most of my writing into 750 Words are Write Honey during the day and then dumped this into a \LaTeX Book Project (2025words) on Overleaf. I use Overleaf to track my word count for the month. A depression in the monthly word count is frequently correlated with a depression in our spent on various writing tasks due to interruptions caused by teaching, service, and the need to do laboratory work. 

The code for the bash function `gac` is below:

```bash
gac () {
	echo "Function to git add a file and then commit the changes with a message."
	echo "Takes the name of a file and the message in a string."
	echo "Must set up repository in advance of using this function."
	if [ $# -lt 2 ]
	then
		echo "$0: not enough arguments" >&2
		echo "Usage: gca filename 'message about the commit'"
		return 2
	elif [ $# -gt 2 ]
	then
		echo "$0: too many arguments" >&2
		echo "Usage: gct "
		echo "Note absence of file extension .tex"
		return 2
	fi
	git add "$1"
	git commit -m "$2" "$1"
}
```
The code for the project-specific push function is below. 
You can use this as a template to make push functions for new projects

```bash
p0574 () {
	cd ~/6114BlaineMooersLabGitHubRepos/0574ImprovedRNACrystalsByChemMod
	cp /Users/blaine/0574ImprovedRNACrystalsByChemMod/main0574.org .
	cp /Users/blaine/0574ImprovedRNACrystalsByChemMod/log0574.org .
	cp -R /Users/blaine/0574ImprovedRNACrystalsByChemMod/figs/ ./figs/.
	cp -R /Users/blaine/0574ImprovedRNACrystalsByChemMod/abib0574/ ./abib0574/.
	comment="${1:-Updated}"
	gac main0574.org "$comment"
	gac log0574.org "$comment"
	gac ./figs0574/ "$comment"
	gac ./abib0574/ "$comment"
	git push
	echo "Pushed the project 0574 to GitHub."
	cd /Users/blaine/0574ImprovedRNACrystalsByChemMod
	pwd
}
```

The code for manorg is below. 
```bash
manorg () {
	echo "Copy template writing manuscript in org-mode with project number in title."
	if [ $# -lt 1 ]
	then
		echo "$0: not enough arguments" >&2
		echo "Usage1: manorg projectID"
		return 2
	elif [ $# -gt 1 ]
	then
		echo "$0: too many arguments" >&2
		echo "Usage1: manorg projectID"
		return 2
	fi
	projectID="$1"
	echo "Write manuscript to man$1.org file."
	cp ~/6112MooersLabGitHubLabRepos/manuscriptInOrg/main.org main$1.org
	/usr/bin/say 'The manuscript template has been copied.'
}
```

The code for logorg is below. 
```bash
logorg () {
	echo "Copy template writing log in org with project number in title."
	if [ $# -lt 1 ]
	then
		echo "$0: not enough arguments" >&2
		echo "Usage1: logorg projectID"
		return 2
	elif [ $# -gt 1 ]
	then
		echo "$0: too many arguments" >&2
		echo "Usage1: logorg projectID"
		return 2
	fi
	projectID="$1"
	echo "Write writing log to log$1.org file."
	cp ~/6112MooersLabGitHubLabRepos/writingLogTemplateInOrg/writingLogTemplateVer082.org log$1.org
	cp ~/6112MooersLabGitHubLabRepos/writingLogTemplateInOrg/wordcount.txt .
}
```

The code for abiborg is below

```bash
abiborg () {
	echo "Create annotated bibliopgraphy subfolder and populate with required files with project number in title."
	if [ $# -lt 1 ]
	then
		echo "$0: not enough arguments" >&2
		echo "Usage1: abiborg projectIndexNumber"
		return 2
	elif [ $# -gt 1 ]
	then
		echo "$0: too many projectIndexNumber" >&2
		echo "Usage1: abiborg projectIndexNumber"
		return 2
	fi
	projectID="$1"
	mkdir abib$1
	cp ~/6112MooersLabGitHubLabRepos/annotated-bibliography-org/compile.sh ./abib$1/.
	cp ~/6112MooersLabGitHubLabRepos/annotated-bibliography-org/apacannx.bst ./abib$1/.
	cp ~/6112MooersLabGitHubLabRepos/annotated-bibliography-org/ab0519.bib ./abib$1/ab$1.bib
	cp ~/6112MooersLabGitHubLabRepos/annotated-bibliography-org/ab0519.org ./abib$1/ab$1.org
}
```

## Update history

|Version      | Changes                                                                                                                                                                         | Date                 |
|:-----------|:------------------------------------------------------------------------------------------------------------------------------------------|:--------------------|
| Version 0.1 |   Added badges, funding, and update table.  Initial commit.                                                                                                                | 4/12/2025  |

## Sources of funding

- NIH: R01 CA242845
- NIH: R01 AI088011
- NIH: P30 CA225520 (PI: R. Mannel)
- NIH: P20 GM103640 and P30 GM145423 (PI: A. West)



