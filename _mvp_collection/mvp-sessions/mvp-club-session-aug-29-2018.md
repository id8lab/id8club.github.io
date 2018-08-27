---
layout: default
title: MVP Club session August 29, 2018
permalink: mvpclubsessionaug292018.html
---

### Agenda

- Follow up on Vagrant installation
- Understand git
- Create a static website
- Create a python based website
- Integrate a database


---
### Follow up on Vagrant installation

Vagrant helps you create virtual machines on-the-fly via a set of reusable configuration files. Developers can share their configurations and scripts via GitHub and elsewhere, so that other devs can spin up an identical environment and tooling.

It’s a great tool if you want to spring up servers for testing programs, learn how to use Linux tools, or work in a test environment before applying changes on a production.

To get started install:

1. [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
2. [Vagrant](https://www.vagrantup.com/)

If you are on Windows, you might need to install [PuTTY and PuTTYGen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) to support ssh client and key generation.

### Create Your Projects

Now that you have it all set up, you can start your first Vagrant project by creating a project folder.
You will have to use the command line to run Vagrant commands.

from the command line you can run vagrant. It will output something like this:

```bash
$ vagrant
Usage: vagrant [options] <command> [<args>]

    -v, --version                    Print the version and exit.
    -h, --help                       Print this help.

Common commands:
     box             manages boxes: installation, removal, etc.
     connect         connect to a remotely shared Vagrant environment
     destroy         stops and deletes all traces of the vagrant machine
     global-status   outputs status Vagrant environments for this user
     halt            stops the vagrant machine
     help            shows the help for a subcommand
     init            initializes a new Vagrant environment by creating a Vagrantfile
     list            
     login           log in to HashiCorps Vagrant Cloud
     package         packages a running vagrant environment into a box
     plugin          manages plugins: install, uninstall, update, etc.
     port            displays information about guest port mappings
     powershell      connects to machine via powershell remoting
     provision       provisions the vagrant machine
     push            deploys code in this environment to a configured destination
     rdp             connects to machine via RDP
     reload          restarts vagrant machine, loads new Vagrantfile configuration
     resume          resume a suspended vagrant machine
     share           share your Vagrant environment with anyone in the world
     snapshot        manages snapshots: saving, restoring, etc.
     ssh             connects to machine via SSH
     ssh-config      outputs OpenSSH valid configuration to connect to the machine
     status          outputs status of the vagrant machine
     suspend         suspends the machine
     up              starts and provisions the vagrant environment
     validate        validates the Vagrantfile
     version         prints current and latest Vagrant version

For help on any individual command run `vagrant COMMAND -h`

Additional subcommands are available, but are either more advanced
or not commonly used. To see all subcommands, run the command
`vagrant list-commands`.
```
Now it is time to initialize the box. run:

```bash
$ vagrant init hashicorp/precise64
```
This will initialize the vagrant linux image.
You can use ubuntu/trusty64 instead. See the list of images available [here](https://app.vagrantup.com/boxes/search)

### Starting the instance

to start the instance run:
```bash  
$ vagrant up

$ vagrant ssh
```

The vagrant ssh command will give you ssh access to your vagrant box.

Sometimes, the windows version will need the box add command.

But anyway, if you want to add another box run:

```bash   
$ vagrant box add hashicorp/precise64
```

### Enable port forwarding

Your Vagrantfile will typically look like this:

```bash  
Vagrant.configure("2") do |config|
  config.vm.box = "hashicorp/precise64"
end
```

You can add port forwarding in the Vagrantfile and then restart your instance:

```bash   

Vagrant.configure("2") do |config|
  config.vm.box = "hashicorp/precise64"
  config.vm.network :forwarded_port, guest: 80, host: 4567
end
```
Restart by running vagrant reload

Stop your instance with vagrant halt

for more information about vagrant read [here](https://www.vagrantup.com/intro/getting-started/index.html) and [here](https://www.sitepoint.com/getting-started-vagrant-windows/)

---

### Understand git

Please go to github [training page](https://services.github.com/on-demand/intro-to-github/) to learn the basics about github and git.

A number of [videos are available on github learning path](https://services.github.com/on-demand/resources/learning-path/).

Following is the [github cheatsheet](https://services.github.com/on-demand/downloads/github-git-cheat-sheet/):

<div class="markdown-body d-flex flex-wrap gutter">

<div class="col-md-6">

<h2 id="install-git">Install Git</h2>
<p>GitHub provides desktop clients that include a graphical user interface for the most common repository actions and an automatically updating command line edition of Git for advanced scenarios.</p>

<h3 id="github-desktop">GitHub Desktop</h3>
<p>https://desktop.github.com/</p>

<p>Git distributions for Linux and POSIX systems are available on the official Git SCM website.</p>

<h3 id="git-for-all-platforms">Git for all platforms</h3>
<p>http://git-scm.com</p>

<h2 id="configure-tooling">Configure tooling</h2>
<p>Configure user information for all local repositories</p>

<p><code class="highlighter-rouge">$ git config --global user.name "[name]"</code></p>

<p>Sets the name you want attached to your commit transactions</p>

<p><code class="highlighter-rouge">$ git config --global user.email "[email address]"</code></p>

<p>Sets the email you want attached to your commit transactions</p>

<h2 id="create-repositories">Create repositories</h2>
<p>Start a new repository or obtain one from an existing URL</p>

<p><code class="highlighter-rouge">$ git init [project-name]</code></p>

<p>Creates a new local repository with the specified name</p>

<p><code class="highlighter-rouge">$ git clone [url]</code></p>

<p>Downloads a project and its entire version history</p>


</div>

<div class="col-md-6">

<h2 id="make-changes">Make changes</h2>
<p>Review edits and craft a commit transaction</p>

<p><code class="highlighter-rouge">$ git status</code></p>

<p>Lists all new or modified files to be committed</p>

<p><code class="highlighter-rouge">$ git diff</code></p>

<p>Shows file differences not yet staged</p>

<p><code class="highlighter-rouge">$ git add [file]</code></p>

<p>Snapshots the file in preparation for versioning</p>

<p><code class="highlighter-rouge">$ git diff --staged</code></p>

<p>Shows file differences between staging and the last file version</p>

<p><code class="highlighter-rouge">$ git reset [file]</code></p>

<p>Unstages the file, but preserves its contents</p>

<p><code class="highlighter-rouge">$ git commit -m"[descriptive message]"</code></p>

<p>Records file snapshots permanently in version history</p>

<h2 id="group-changes">Group changes</h2>
<p>Name a series of commits and combine completed efforts</p>

<p><code class="highlighter-rouge">$ git branch</code></p>

<p>Lists all local branches in the current repository</p>

<p><code class="highlighter-rouge">$ git branch [branch-name]</code></p>

<p>Creates a new branch</p>

<p><code class="highlighter-rouge">$ git checkout [branch-name]</code></p>

<p>Switches to the specified branch and updates working directory</p>

<p><code class="highlighter-rouge">$ git merge [branch-name]</code></p>

<p>Combines the specified branch’s history into the current branch</p>

<p><code class="highlighter-rouge">$ git branch -d [branch-name]</code></p>

<p>Deletes the specified branch</p>

</div>
<div class="clearfix"></div>

<div class="col-md-6">

<h2 id="refactor-file-names">Refactor file names</h2>
<p>Relocate and remove versioned files</p>

<p><code class="highlighter-rouge">$ git rm [file]</code></p>

<p>Deletes the file from the working directory and stages the deletion</p>

<p><code class="highlighter-rouge">$ git rm --cached [file]</code></p>

<p>Removes the file from version control but preserves the file locally</p>

<p><code class="highlighter-rouge">$ git mv [file-original] [file-renamed]</code></p>

<p>Changes the file name and prepare it for commit</p>

<h2 id="suppress-tracking">Suppress tracking</h2>
<p>Exclude temporary files and paths</p>

<div class="highlighter-rouge">
<pre class="highlight"><code>*.log
build/
temp-*
</code></pre>
</div>

<p>A text file named <code class="highlighter-rouge">.gitignore</code> suppresses accidental versioning of files and paths matching the specified patterns</p>

<p><code class="highlighter-rouge">$ git ls-files --others --ignored --exclude-standard</code></p>

<p>Lists all ignored files in this project</p>

<h2 id="save-fragments">Save fragments</h2>
<p>Shelve and restore incomplete changes</p>

<p><code class="highlighter-rouge">$ git stash</code></p>

<p>Temporarily stores all modified tracked files</p>

<p><code class="highlighter-rouge">$ git stash pop</code></p>

<p>Restores the most recently stashed files</p>

<p><code class="highlighter-rouge">$ git stash list</code></p>

<p>Lists all stashed changesets</p>

<p><code class="highlighter-rouge">$ git stash drop</code></p>

<p>Discards the most recently stashed changeset</p>

</div>

<div class="col-md-6">

<h2 id="review-history">Review history</h2>
<p>Browse and inspect the evolution of project files</p>

<p><code class="highlighter-rouge">$ git log</code></p>

<p>Lists version history for the current branch</p>

<p><code class="highlighter-rouge">$ git log --follow [file]</code></p>

<p>Lists version history for the file, including renames</p>

<p><code class="highlighter-rouge">$ git diff [first-branch]...[second-branch]</code></p>

<p>Shows content differences between two branches</p>

<p><code class="highlighter-rouge">$ git show [commit]</code></p>

<p>Outputs metadata and content changes of the specified commit</p>

<h2 id="redo-commits">Redo commits</h2>
<p>Erase mistakes and craft replacement history</p>

<p><code class="highlighter-rouge">$ git reset [commit]</code></p>

<p>Undoes all commits after <code class="highlighter-rouge">[commit]</code>, preserving changes locally</p>

<p><code class="highlighter-rouge">$ git reset --hard [commit]</code></p>

<p>Discards all history and changes back to the specified commit</p>

<h2 id="synchronize-changes">Synchronize changes</h2>
<p>Register a remote (URL) and exchange repository history</p>

<p><code class="highlighter-rouge">$ git fetch [remote]</code></p>

<p>Downloads all history from the remote repository</p>

<p><code class="highlighter-rouge">$ git merge [remote]/[branch]</code></p>

<p>Combines the remote branch into the current local branch</p>

<p><code class="highlighter-rouge">$ git push [remote] [branch]</code></p>

<p>Uploads all local branch commits to GitHub</p>

<p><code class="highlighter-rouge">$ git pull</code></p>

<p>Downloads bookmark history and incorporates changes</p>

</div>
</div>

### Graphical userinterface
Sourcetree is a GUI interface to git.
You can download it form [here](https://www.sourcetreeapp.com/)
And follow this [tutorial](https://confluence.atlassian.com/display/BITBUCKET/Tutorial%3A+Learn+SourceTree+with+Bitbucket+Cloud) to learn how to use SourceTree.

---
