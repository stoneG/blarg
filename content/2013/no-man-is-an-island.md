Title: No man is an island: Collaboration with git
Date: 2013-03-12 20:55
Author: Sitong Peng
Tags: git, version control, crash course, rebase, github, heroku, bitbucket
Slug: no-man-is-an-island
Summary: Branching, merging, rebasing

I'm currently in the middle of a Git crash course at work. Though to be honest, it's not really a course. It's more just me crashing. Luckily `git` is forgiving while I'm expanding my terminology past `add`, `commit`, `push`, `status`, `diff`, and `clone`. In this post, I'll summarize what I'm learning in my own words, but you should read the amazingly clear [Pro Git](http://git-scm.com/book) text if you have the time.

Version control for the masses
------------------------------
When you're just a lone soul contributing to your own project, `git` is basically just a backup system. You're really only keeping snapshots of your project progress and not really worrying about concurrent user editing. But add other contributors into the equation and suddenly you'll need to be in control of all the moving parts of your codebase, or you may have a mess of things.

Growing a forest from a single tree
-----------------------------------
Every `git` repository holds a wealth of information about a project. Since each repository houses an entire project and it is dead simple to pull down repository clones from websites like [Github](https://github.com) and [BitBucket](https://bitbucket.org/), it is easy to see how a popular project might spread its roots over many machines. This can lead to a lot of chaos though, and teams that are working together on projects will need to work on atomizing their changes. Individual commits do fulfill this fairly well on the small scale, but you also need to organize your commits into groups. A good way of conducting this separation is through `git`'s branching model.
```git
git branch [new_branch]
```
This command creates a new branch separate from your current development branch. Branching is useful to organize paths of development, as you're literally splitting off a new path. For instance, you might want to make a new branch for every bug-fix/feature request of your website or for both server and client sides of your new game. If not given a parameter, `git branch` will simply list your outstanding branches and show you which branch you're currently working in.

To actually move branches, you'll want to run this command:
```git
git checkout [existing_branch]
```
Then you can either `git branch` or `git status` to see where you are.

Finding your way back home
--------------------------
Great, so you've fixed three bugs and added two features, including pillow armor for your game's main character. How do you get all of that jazz back onto your main development or master branch?

Generally there's two ways about this.
```git
git merge [existing_side_branch]
```
While you're in a branch (such as the main/master branch) and you want to merge in a side branch, you'll generally be safe with this command. If your current branch never moved forward, the merge will just `fast forward` your current branch to the position of the side branch. If your current branch has progressed separately from the side branch, the merge will recursively find the two branches' common ancestor, then perform a three-way merge. You'll end up with an extra commit that merges your two branches. This extra commit will have two parents instead of one (one for each branch that it was merged from).

You can avoid having two parent commits and have a more straightforward development path with the second method of merging.
```git
git rebase [existing_main_branch]
```
You'll start in your side branch. The `rebase` command starts with the divergent main branch and tries to sequentially stack the each side branch's commits on top. If there are no merge conflicts, you'll end up with a straight development path.

Knowing where your keys are
---------------------------
The true beauty of `git` isn't realized until you spread out repositories onto different machines and servers. Remote locations are tracked in the `remote` variable.
```git
git remote
```
This lists all saved remote locations. Your list may contain `origin` (Github/Bitbucket), `heroku` (Heroku), or the name for your own private server.

Anyways, that's essentially the foundation you need for version control. Stay tuned to a later post for more advanced `git` control.
