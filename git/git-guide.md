# Git - A Basic Guide

Hello.

So, you've decided to learn what Git is.  Maybe in your native culture it is part of some obscure coming-of-age ritual; maybe you've been roped into doing something that involves working with Git; maybe you have just heard so much about this strange git or GitHub thing that all the software developers you know say they know how to use, that you just have to know about it for yourself.  Never mind; it doesn't matter.  This is a guide to Git, written for people who know nothing at all about it, or indeed, about version-control systems in general.  It is hopefully simple, straightforward, not too technical, and will not make your brain hurt *too* much.  We start at the start, explain everything from first principles, and by the end of it you won't feel uncomfortable if someone tells you to check out their new branch, or that they have accidentally detached their HEAD.

## So, what is this thing?

Git is a distributed version control system built on top of an immutable content-addressed data store.  Yes, I know that might at present just be a block of noises to you, so let's unpack it to see what those sounds actually mean.

Version control systems are, essentially, systems that store prior versions of computer files so that you can go back and see how they have evolved over time.  They have been around, in one way or another, for the best part of fifty years at the time of writing.  Over that time many different version control systems have been developed, with different markets and capabilities, and taking different approaches to the fundamentals of how a version control system should work.  This document is not a history of or a guide to version control systems in general, so most of this great variety is glossed over as if it is not even there.  This document is unashamedly about Git, and how Git is designed to operate, and in some ways if you are coming to the subject as a complete novice, you have a great advantage in your lack of preconceptions about how you expect a version control system to do things.  Nevertheless, there are some aspects of Git's operation and terminology that do derive from older systems, and might not appear to make much sense on the surface.

Historically, some version control systems have been designed for storing a single user's personal files under version control; others have been based around a client-server model, with a central server holding the entire archive and clients downloading the parts of it they want to know about.  Git is a fully distributed system, where each user has their own copy of the entire archive.

As well as being a distributed system, Git is designed to be non-hierarchical.  No individual copy of an archive is necessarily any more important than any other.  However, because decentralised non-hierarchical networks can be hard work, about 99% of the time Git is actually used in a hierarchical way.

And finally, a "content-addressed data store" sounds like a complicated idea, but really it just means a database where each entry is indexed by its content.  When a new item of data is added, the content of the item is used to generate the name that it will be referred to by.  "Immutable" means that data, once it's in the store, can't be edited.  You can add new data, you can remove old data, but you can't change existing data.  Instead, you have to add a new, edited version of the data.  We'll go a bit more into how all this works later on, and the implications that it leads to.

Git is a relative newcomer to version control, but it has still been around nearly 15 years&mdash;it was created in 2005 by Linus Torvalds, the original creator of the Linux operating system, as he felt very strongly that there was no open source version control software that met all the needs of the Linux kernel development team.  It was specifically written to be fast, flexible, scalable, and to deliberately avoid things that Torvalds saw as flaws in other version control systems; all these things come some way to explaining its current popularity.  It was written from the bottom up, with the data store created first and the version control workflow added on top.

## But what isn't it?

Here's a few things Git is not:

* Git does not give you a permanent and unchanging archive
* Git is not something for keeping data securely
* Git is not a piece of software that needs to be constantly running on your computer
* Git is not one single piece of software
* Git is not ideal for archiving every possible type of data
* Git is not GitHub

Git data is stored in ordinary files on your computer.  It's not encrypted, so the data in it isn't secure.  Git itself really just consists of a few software tools that manipulate those files; but if you were a true masochist, you could write your own tools or even edit the files by hand to some extent.  The data in Git is not necessarily permanent - it is possible to delete data from it.  If you try to delete things willy-nilly it will be obvious that the archive is broken; however, if you use the Git tools properly, it is possible to delete things and have it only be obvious to someone who has an older copy of the archive.

There are many implementations of Git - as well as the standard command-line tools, there are a wide number of versions with graphical user interfaces, and Git tools are built into a lot of programming tools, environments and editors.  Not all of them work in exactly the same way, but most of them are fairly close to each other - they have to be, in order to interoperate.  Git *can* be run as a server, but it doesn't have to be, and very few people need to run it as one.

Git works best with "plain-text-like" files - files that you can open up in a "text editor" like Notepad.  This is because they have a few properties that make them particularly suited to efficient storage by a version control system:

* On a human scale, they are information-dense.  It doesnt take many bytes on disk to store them.  Compare the size of a plain text file with, for example, a photo of the same words on a piece of paper.
* On a computer scale, the reverse is actually true.  They are information-sparse, and compress easily.  A single byte of data can have 256 different values, but in even quite a large plain text file you might see only 50 of those values being used, just because of the size of the alphabet and the frequency of letters in it.
* When you change a text-like file, the size of the changes is usually roughly proportional to the semantic content of the changes.  In simple terms, if you change about 10% of the words in a file, then about 10% of the bytes of data will change.  Going back to our example of a photo of a passage of text: you will find that the file could potentially be completely different.

The upshot of the above is that plain-text-like files are relatively small, easily compressed, and that you can store a group similar files very efficiently by storing the whole of one, together with the instructions for editing it to change it into each of the others.  This makes it practical to store every different version of a file, over time, without using huge amounts of disk space.  If you try to do the same with binary files, such as images, the disk space you use will quickly swell up if the files are changing frequently.

Text-like files that are good for Git (and other version control systems) include:
* Plain text
* Programming code
* Website source code such as HTML, CSS and PHP files
* Other XML data files
* Markdown files, and similar text formats

And files that aren't include:
* Images
* Database files
* Microsoft Office files
* Compiled program files (eg .exe or .dll files)
* ZIP archives

Microsoft Office files are a somewhat annoying case here, because they are actually XML files that have been zipped to save space.  Similarly, PDF files are a grey area: technically PDF is a text-like format, and you could write one by hand if you wanted to; however, nobody ever does, and a lot of PDF files are full of encoded images with all the downsides that entails.

Lastly, I have known quite a few people initially get a bit confused between Git and GitHub.  GitHub is a Git hosting service.  You don't have to use GitHub to use Git.  There are, indeed, other Git hosting services out there such as Bitbucket, GitLab, Azure DevOps, and more.  They offer nice user interfaces, and very useful collaboration tools such as the ability to publish your code or just to share it within a group, and the ability for people to comment on your code or review changes before you finalise them.  They're also, for the 99% of people who want to use Git in a centralised and hierarchical style, a good way to provide that centralised focal point that is considered to be the official version of the archive.  However, you don't *have* to use them.

## Some terminology and some basics

Now you know what Git, in the barest of terms, is, and have a few ideas about what it isn't.  Time to run through some basic concepts and terminology.  Bear in mind that much of the below describes the most common way to use Git.  It's actually much more flexible than this, but 99% of that flexibility is rarely used, and constantly bringing in hedges like "this is stored in that location&mdash;except when it isn't" would not really be very helpful to you right now.

One piece of general terminology that can be confusing is the difference between "folder" and "directory".  In general, frankly, they mean the same thing; originally "directory" was much more common but operating systems tended to switch over to "folder" when they gained graphical interfaces which used icons of manila folders as a visual metaphor.  Because Git started out as a Linux program and generally tends towards using Linux conventions, I'll use the word "directory" below, even though it is more typing.  Similarly, when writing file paths, Linux computers use `/` to separate directory names and Windows computers use `\\`

Another warning about terminology: when I use the word "digit" below I'll often be referring to *hexadecimal*, or base-16 digits; as well as the regular decimal digits they also include the letters a through to f, representing the numbers ten through to fifteen respectively.  Please try not to be too surprised if when I say "a two-digit directory name" you see something like `be` that actually looks like a word.  `be` in base 16 represents 190 in "normal human" base 10 counting, incidentally.

In case you were wondering, I'm using `this font` to refer to things you will see directly on the computer screen, such as names of files or commands for you to type.
If I want to distinguish between things that you type and the computer's response, I'll prefix the things that you type with a `>` symbol, like this:

```
> this is what you type
this is what the computer responds with
```

I said above that there's no single implementation of Git.  There isn't, but below I have given example commands that use the official command-line version, downloadable from <https://git-scm.com/downloads>.  That website also has the official documentation, and [a list of graphical Git tools](https://git-scm.com/downloads/guis), some of them free and some of them expensive.  Git support is also built in to various other programming environments, although some of them (I'm looking at you, Microsoft Visual Studio) alter terminology slightly and blur various workflow steps together a bit.  Some operations are much easier in a graphical tool; I tend to use [Atlassian Sourcetree](https://www.sourcetreeapp.com) although it's naturally tempting to try to produce my own.

And now, let's dive in.

Up to now I've been using the vague word "archive", but in Git jargon the term is *repository*, or *repo* for short.  The repository is a directory which contains a hidden directory called `.git`; the `.git` directory contains the data store itself.  Apart from that, there's nothing special about a repository.  Everything in the repository that isn't in the `.git` directory is called the *working area*.

Git's data store contains a set of snapshots of the contents of the working area at different times.  Each of these snapshots is called a *commit*, and also includes a small amount of metadata, such as who created it and (hopefully) a *log message* explaining what is in it or what has changed.

Each commit can have references to other commits which can be considered its direct predecessors, called *parents*.  Most commits have one parent.  Although a commit contains a full snapshot of every file in the working area, your Git tools can compare a commit with its parent to show you what has changed between the two&mdash;this mimics some other version control systems in which a commit consists of only the changes between two states, not the entire state itself.

Copying the contents of a specific commit from the data store into the working area is called *checking out*.  The simplest possible way to explain the Git workflow is to say that you check out a commit, modify your files, and then *commit* (as a verb).  This will create a new commit (noun) in the data store, whose parent is the commit you checked out.  Your new commit is then considered to be your currently checked-out one.  There is a bit more to it than this, but I will explain that in the next section.

Normally you don't check out a specific commit, though.  You check out a *branch*, which in one sense is just a name for a specific commit, but in another sense is the commit and all its history&mdash;the specific commit a branch refers to is often called "the head of the branch".  If you have a branch checked out, and follow the above process, then when you commit your changes the head of the branch will move along to your new commit, and you will still have the same branch checked out.

Sometimes you will see the current checked-out commit referred to as *HEAD*.  This is because the name of your current checked-out branch or commit is stored in the file `.git/HEAD`.

By convention, repositories start out with one branch, called `master`.  It's possible to have a repository that only ever has a `master` branch.  More commonly, people create and destroy branches on a fairly frequent basis.  It's fairly normal to create new branches to work on specific pieces of work.  Git was specifically designed for branches to be "cheap": in other words, creating them is quick and isn't resource-hungry.

Git's concept of branches is (in my opinion) one of the areas that differs the most from other common version control systems, in which a branch is really just a fancy type of directory.  Git's branches are much more powerful than that, but can be somewhat harder to understand, so I'll go into that in more detail later.  Before that, I want to explain a bit more about how the workflow works in practise.

## Typical workflow operations

### The creation of the universe out of the infinite blackness of nothing

Obviously, every Git repository has to start somewhere.  This isn't actually something you need to do very often, though, so therefore it isn't something you really need to know, but it is so straightforward and simple that we may as well begin at the beginning.  If you are using Git by typing commands into a command line, you do it by running the following inside the directory you want to turn into a repository:

```
git init
```

This will create the `.git` directory, and a bunch of other things inside it: the `HEAD` file already mentioned, and various other things like a `refs/` directory and an `objects/` directory.  These various things together make up a minimal empty Git data store.

### Taking up the baton from elsewhere

It is much more common to copy a repository from elsewhere than to create one from scratch on your own computer; even if you are creating a new repository yourself, if you are going to host your repository on a service such as GitHub then the normal way round is to create it there first.  The process of copying a repository onto your computer so you can work on it there is called *cloning*, and on the command-line is done like this:

```
git clone [repository address]
```

Note that&mdash;in one of Git's many inconsistencies&mdash;running this command creates the repository as a new directory below your current one, whereas `git init` creates it inside your current directory.  In order for `git clone` to work in the same way as `git init`, you need to add "` .`" (space, dot) at the end of the command.

Cloning a repository like this will download the whole thing from the server, then check out the `master` branch into the working area.  However, it does a little bit more than this.  A Git repository can also contain links to other copies of the same repository, called *remotes*, and cloning sets up a remote called `origin` which is a link back to the source of your clone.  This is important, when you are working in a centralised and hierarchical way, when it comes to sending your changes back to the central server.  The link to the origin repository also includes a list of all the branches on the remote repository, and what commits each branch currently points to.

I said earlier that Git was designed to be completely decentralised and non-hierarchical, but that most people do still use it in a hierarchical way.  Because of this, most Git repositories end up having just one remote, `origin`.  However if you are working in a genuinely decentralised mesh of people, hard to manage as they are, you can have as many remote links as you like.  Having multiple remotes can also be a useful way to keep a hosted repository in sync between different hosting services, should you ever need to do that.

### Making a change

Assuming you are working with a remotely-hosted copy but you have your own local clone of it already, the full set of steps to carry out to make a change is as follows:

* Fetch
* Check out
* Pull remote changes
* Make your edits
* Stage your changed files
* Commit your changes
* Push your changes to the remote server

Yes, it seems like a lot, but it's a relatively smooth process.  Let's run through the steps in a bit more detail.  Bear in mind before we start that the following instructions are making a few instructions, to whit:

* Your local repository is a clone of a remote origin repository.
* You're going to make the changes in a branch that already exists on the remote repository.
* That branch also already exists in your local repository, too.

#### Fetch

This step gets any data from the origin repository that you don't have locally.

```
git fetch
```

That's all it does really.  It updates your repository's data about what branches are in the origin repository, and what their status is, but doesn't make any changes to your own repository itself.

#### Check out and pull

Next, it's a good idea to make sure that the branch you have checked out is the branch you thought you had checked out.

```
git checkout [branch-name]
```

Note that if your working area has some uncommitted changes, the behaviour of this can be a bit unexpected.  If checking out the other branch would overwrite those changes, Git will refuse to do the checkout.  If it wouldn't overwrite them&mdash;if there are files in your working area that aren't in the data store at all, for example&mdash;they will stay there.  This can get a bit confusing, so it is often best to make sure your working area is relatively clear before you switch branches.

Even though `git fetch` makes sure your local repository has all of the same data as the remote one, it doesn't make any changes to the branches in your local repository.  Because of that, you might now need to *pull*.

```
git pull
```

This will, if necessary, update your local branch so that it is in line with the matching remote branch.  If it wasn't necessary, nothing will happen, but equally no harm is done.

#### Make your edits and add your changes to the data store

I'll assume you don't need me to tell you how to edit your files, or change whatever it is you want to change.  When you've done it, though, you need to add the new version of your file(s) to the data store.  This is called the *Staging* step, but the command to do it is `git add`.

```
git add [filename]
git add -A
```

The first form processes only the named file (or files).  The second form processes all changes; if you want to do a "dry run" to see what it would do, use the command `git add -An`.  Confusingly, even though the command is `git add`, it's not just for adding new files to the data store.  It's also needed for adding new versions of existing but modified files.  Even more mind-melting, you can also use `git add` after you've deleted a file, to "add" the record of its deletion (although there is another less confusing way to delete files that I'll mention later).

Because `git add` applies to the version of the file currently saved on disk, you have to run it after the last edit to the file and before you commit your changes.  It doesn't matter if you run it multiple times (you will store extra versions of the file in the data store, but they will eventually get cleaned up again) but if you don't run `git add` immediately before committing your changes, you might not commit the version you expect.

If you think this step all sounds a bit confusing, don't worry, it probably is.  A good user interface tool can help a lot here.  Some tools, such as the Git functionality built into Microsoft text editors like Visual Studio or Visual Studio Code, effectively hide the staging step from the user entirely, doing it in the background before committing.  Others, like Atlassian Sourcetree, still make it an explicit step, but show clearly on-screen which files have been changed but have not been staged, and which have been staged and will be committed.

#### Commit your staged changes

The command for committing is, you may have guessed, `git commit`.

```
git commit -m "Explain what I have changed"
```

The part in quotes is the *log message*.  You should really always include a log message with every commit, because not doing so is Naughty with a capital N.  Unfortunately, because Git knows this, if you use `git commit` but don't specify `-m` followed by a message, the first thing it will do is open up a text editor for you to type your message in.  It probably won't be a nice friendly easy-to-use editor, though, it will probably be Vim.  Now, I would never say a word against Vim, but that is largely because its fans are as fanatical as a football supporter or a railway enthusiast, and some of them are bigger than me and know where I live.  Only a truly devoted Vim fanatic, though, would ever claim it is easy to use&mdash;or easy to get out of if you open it by accident.  It's also not actually easy to type anything in it if you open it by accident, either.  If you ever do, to get out of it, you have to type `:wq`, or possibly `:q`, or maybe even `:!q`.

I love Vim really.  Honest.  Please, you can put the baseball bat down now.

If you want to avoid any risk of accidentally opening Vim, of course, the best way is to use some sort of fancier Git interface like Sourcetree, where you can just type your log message into a box and then push a button.

Incidentally, if you want to reduce your typing, then if you type `git commit -a` it's *almost* the same as doing:

```
git add -A
git commit
```

Except that it's not quite the same.  Did I mention the Git command line has many inconsistencies?  Firstly, `git add -A` stages changes to *all* files, whether new, modified or deleted.  `git commit -a` only stages modified and deleted files; it doesn't touch new ones.  Secondly, because you didn't add `-m`...you've just started Vim.  Next time, you will remember to do `git commit -a -m "Please, not the fingers, I admit Vim has lots of advantages"`.

Again, if you want to be more confident about exactly what you are committing, use a tool with a graphical user interfae, not the command line.

#### Push your changes back to the remote server

Now you have committed your changes, your branch is updated, but that has only happened on your own computer.  For other people to see your work, you need to copy the changes in your local repository up to the origin repository.  This is called a *push*.

```
git push
```

This will update the branch in the origin repository to match the branch on your local machine, together with uploading all of the data that you have added.  Now, your work is up for all your collaborators to see, and the cycle is complete.

### But wasn't it even more complicated than that?  Where do branches come into it?

Yes, I did say earlier that branching is very common in Git, and most people will start a new branch for every new piece of work they do.  In that case the workflow is usually a little bit different - a bit like the following.

* Fetch and check out the branch you want to branch from
* Create your new branch
* Make changes, stage your new files, commit your changes
* Push your new branch to the remote server

Most of it is, you can see, fairly similar to the previous.  You start off in a very similar way to before...

```
git fetch
git checkout master
git pull
```

But then you use `checkout` again, this time with a `-b` switch to create a new branch.

```
git checkout -b bugs/my-amazing-bugfix
```

If you use `/` in a branch name most Git tools will interpret that as a tree structure, as if it were a path to a file, but that's purely convention.  Nevertheless it's very useful to be able to group branches by purpose this way, and there are some popular workflows which impose specific prefixes like `release/` or `feature/` for those specific types of work.  If you use a ticketing system that gives each bug its own reference number, a lot of people will use that reference number in the branch name, so that it becomes something like `bugs/issue-1337-fix-some-typos`.  Some Git hosting services will automatically spot if a branch name contains a ticket reference, so their interface can then show links from code to ticket and vice-versa, or can automatically change the ticket status as commits are made to the branch.

The `git checkout -b` command creates the new branch and checks it out as a single step, so you are then ready to make your changes, stage your changed files, and create your commit as before.  However, the command for uploading the branch to the remote server is slightly different.  A naked `git push` only works if the branch already exists in the origin repository.  You need to upload the commit, create the branch on the server and link the local branch to the new server branch.  The command for doing all of this is:

```
git push --set-upstream origin [branchname]
```

As above, a good Git user interface tool will hide all of this complexity from you somewhat, although some do distinguish between a push (the simple sort we described earlier) and a *publish* (the push-with-extra-bits shown here).

"So how do I get the changes in my branch back into the master branch?" you're probably asking.  Well, to be frank, I suspect you're probably asking just how long this guide is, and whether it's time for a cup of tea yet.  However, in the hope that you have wondered if branches can be combined together rather than just split apart, I'll explain an answer to what happens next.

Now, branches can be *merged*.  A while ago, I mentioned that commits can have parents, and most commits have one parent.  You might have wondered at the time if it is possible for commits to have multiple parents&mdash;and it is!  A commit with two or more parents is called a *merge commit*, because it merges two branches together.  The vast majority of merge commits have two parents.  Theoretically, though, a commit can have any number of parents; a commit with three or more parents is called an *octopus merge* and is rather rare in the wild.  Of course, a commit also can have no parents at all; the first commit in a repository always does, for example, for obvious reasons.  A commit with no parents is called a *root*, and although you can have multiple roots in one repository, this is probably even rarer than an octopus merge.

Merging your branches together when your work in one is completed is all well and good if you are a solo developer, a lone wolf, a coding hermit fighting a single-handed battle against the forces of chaos who doesn't use any sort of Git hosting service.  However, most of the hosting services offer something a bit more powerful called a *pull request*.  This has the following advantages:

* It lets you easily see what changes are included in your merge
* It lets your collaborators see them too, and review and comment on them
* You can ask your collaborators for formal approval before the changes are merged in.  Or, for that matter, they can reject them.
* It gives you a long-term archive of "significant merges"
* You can use it alongside permissions to control contributions to your code

On the last point, it's not that unusual to have a hosted repository set up in such a way that **nobody at all** is allowed to write commits directly onto the `master` branch.  Instead, the permissions in hosted repository service can often be configured so that `master` can only be changed by merging a pull request.  Additional limits can include things like "pull requests must have N reviewers sign them off before merging", or "your code must pass checks to make sure it is suitably stylish before the pull request can be merged".  That last one is not actually a joke.

I've held off discussion of merging for a bit because it is a complicated and messy topic, because of the things that can happen when things don't merge together as cleanly as you would hope.  There's also *rebasing*, which is where you stick your fingers in your ears and pretend that your branch never existed to begin with.  Some people swear by rebasing, others swear at it, but I generally think that it is a bad idea in nearly all circumstances.  This is partly a philosophical point about rewriting the past, partly due to the difficulty involved in sticking your fingers in other people's ears as well as your own.  I'll explain why in a bit more detail later.  Before that, it might be an idea for me to dive more deeply into exactly what is happening and churning away in your `.git` directory when all of these different commands are done.

## The intestines, giblets, and other squishy bits

Earlier on, I introduced you to the dense and hard-to-chew lump of text: "an immutable content-addressed data store".  Now that you know a bit more about how Git can be used on the surface, I'm going to dive in a little deeper and explain what that sentence actually means.

### Who could object to that?

Most of the data Git uses is stored as *objects*.  A commit is an object.  The content of a file is also an object.  Unlike a file, though, you can't set the name of an object.  Its name is a *hash*, a long number that is generated by applying a specific mathematical function to the content of the object.  The function used by Git is *SHA-1*, a hash function originally created by the NSA for use as a cryptographic standard, and it produces a hash that is always 40 digits long when written down in base 16.  For Git's purposes, you don't need to worry about SHA-1's origins or its cryptographic properties, although thousands of mathematical papers have been written on the topic.  All you need to know is that firstly the same content will always produce the same 40-digit output; and secondly, one of the function's designed-in features is called the *avalanche effect*.  This means that only a small change to the contents of the object results in a large change in the hash.

For example, an empty file, stored in Git, is always stored as an object named `e69de29bb2d1d6434b8b29ae775ad8c2e48c5391`.  A file containing just the word "cakes" and nothing else is stored with the name `548bbf86c3567db83ced0f0cf82ba6c2340c0650`, but if it is changed by one letter to the word "makes", the name becomes `af898e16b5c09cb9817eec0c914cf04ae03288cf`.  In other words, a change of just one letter in a text file has changed 35 of the 40 digits in the hash.  That's the avalanche effect in operation.

You might wonder what happens if two files don't have the same content but do turn out to have exactly the same hash.  This is technically termed a *collision*, and theoretically, it is possible.  However, because of the length of these numbers, it's very very unlikely.  The number of different possible hash values is approaching one and a half million.  Million.  Million, million, million, million.  Million.  That's quite a lot, so the chance of this happening is very very low.  If it ever does, Git will only ever use the first object it encounters with the given hash, so you would need to change the second object ever so slightly to change the hash and avoid the collision.  If you ever do come across this as an issue, you should probably go out and buy a lottery ticket too.  Because SHA-1 was designed to be used in cryptography, it was designed to be very difficult to construct two files which have the same hash.  After the function algorithm was published, it took ten years of trying for someone to work out a potentially practical way to create a collision, and another twelve years after that for someone to publish an actual example.  Although this terrified cryptography experts, it doesn't really matter for Git.  Incidentally, any Git commands that expect you to enter a hash will accept the first seven digits if they are unique within the repository, and if not, as many as are needed to remove the ambiguity.  I have occasionally seen collisions at the seven-digit level in repositories with a few hundred thousand objects.

Reading the paragraph before last, you might have deduced that when a file is stored as a Git object, its filename isn't included.  That's correct.  The objects that contain file contents are called *blob objects*, and all that's inside them is the file content itself, plus a small header that contains the file's length (and the word "blob").  The names of the files, on the other hand, are stored in *tree objects*.  These are like a list of the files in a directory: they contain a list of filenames, a flag to say whether each one is a blob or another tree, and the hash of the object.  There's also another flag which is mostly used to say whether a blob could be run as a program or not, which you don't really need to worry about.

There are two more types of object inside Git.  One is *tag objects*.  We haven't mentioned tags yet, although they are very useful indeed, so I won't talk about them too much here.  The final one is *commit objects*, which unsurprisingly store commits.  Each commit object contains the log message, the names of the parent commits, the name and email address of the person who created it, and the name of the tree object which contains the top-level working area directory for this particular version.

Like blob objects, the names of all the other object types are hashes of their content.  Not the content of the various blob objects they eventually point to, but all the various items of metadata they contain themselves.  Nevertheless, the fact that the object names are all directly derived from their content is what gives the Git data store its property of immutabiity.  You can't change the content of a blob: because of the way its name is defined, a blob with different content has to have a different name, making it a different creature.  You can't change the blobs in a tree, because the tree's name is derived from its content, which includes the names of all the blobs it contains (as well as their filenames on disk too).  This bubbles up all the way to the top: you can't change the tree a commit points to, or who its parents are, because doing so would change the commit's name too.  In other words, the name of a commit is derived from all of the data contained within it, and you can't change any of that data without turning it into a different commit, or breaking your repository.  It also bubbles forward into the future: if you change the name of a commit you also have to change its child commits, and therefore their children, and indeed all subsequent commits in the repository that are derived from the one you have changed.

All of the objects, whatever their type, are saved somewhere in the `.git/objects/` directory, which makes remarkable sense.  There are ways for Git to store an object, however.  Newly-birthed objects are put into the *loose object store*.  If you look at the `.git/objects/` directory of a typical repository, you'll see it contains a lot of directories with two-digit names.  Inside each directory there may be data files that each have thirty-eight-digit names.  These are the loose objects, one object per file, with the first two digits of the object's name being the directory name and the rest the filename.  Some operating systems have a history of poor performance when it comes to reading directories containing a large number of files, so Git has adopted this pigeonholing system to split the loose object files up and hopefully work around this problem.  You can't look inside the objects easily, because each file is compressed, but the `git cat-file -p` command will display the content for you.

```
git cat-file -p 548bbf86c3567db83ced0f0cf82ba6c2340c0650
cakes
```

Even with objects divided up among different directories the loose object store can get a bit unwieldy with thousands of entries, and transferring objects one by one over the network can be a bit of a pain too.  Because of this, Git has a second object-storing mechanism, in which objects are bundled into *packs* or *packfiles*.  If your repository contains packs (a small one might not), they will by convention be inside the `.git/objects/pack/` directory, with each consisting of a pair of files: `pack-[name].pack` and `pack-[name].idx`.  Like objects, the name of a pack is a SHA-1 hash of its content, so the `[name]` parts of those filenames will each be a 40-digit number.

The `.pack` file contains the pack itself: object data, some objects stored exactly the same as the file of an individual loose object, but some stored using a compression mechanism called *delta compression*.  The `.idx` file contains the index, which is vital for getting each object unpacked.  It consists of a list of objects, ordered by name, together with its position in the `.pack` file, its length, plus if the object is stored using delta compression, the name of another object, the next in the *delta chain*.

For a version control system, where each file might only change a small amount from version to version, delta compression can be a really efficient way to save space.  A *delta*, in this context, is the difference between two objects.  When Git is storing objects into a pack, it will (in its head) sort them all by size, and then as it processes each one, compare it to the most similarly-sized objects.  It's looking to see if, instead of storing the full versions of all objects, it would be more efficient to store some objects as deltas, the changes needed to transform one object into another.  Instead of storing both `cakes` and `makes`, for example, store `cakes` and `take the last object and change the first letter to "m"`.

That is a slightly silly example because&mdash;written out in regular English, at any rate&mdash;it clearly doesn't save you any space.  With real files, where only a small percentage of a file potentially changes from version to version, it can have a great effect.  Moreover, it doesn't just look at pairs of closely-related objects (which is how some other version control systems implement delta compression).  The packing program doesn't even know how closely related any given pair of objects is.  It goes purely on their content, and the length of the list of instructions needed to transform object A into object B.  And of course it might also be efficient to store object C by comparing it to object B.  In this way, chains of objects are built up and stored in the index.  When Git unpacks an object stored by delta compression, it will see the delta chain reference in the index and follow the links in the chain until it finds an object that is stored complete.  It will then work back through the chain, applying the stored transformation for each object, until getting back to where it started with the object it was trying to unpack to begin with.

Git doesn't necessarily know where an object is, by the way.  If it needs an object, it goes looking for it first in the loose object store, then in the various pack indexes that it has to hand.  There isn't any master index, though, that says where all of the objects are.  Because of that, having too many packs can also get unwieldy, and if Git decides that a repository does contain too many, it will repack them into just one.

### So what sort of object is a branch?

We've seen that files (or versions of files), directories and commits are all objects, all stored side by side in the same object stores.  So what about branches?  Are they objects too?

No, branches are a different type of thing altogether.  A branch is a type of thing called a *reference*.  We've already mentioned one reference earlier: the file `.git/HEAD`, which is a small text file containing the name of the currently checked-out branch or commit.  All references, though, are of the same format: the directory `.git/refs/heads/` contains reference files for branches, each one containing the name of the commit which currently marks the tip of the branch.  The file's name is the branch name.

Incidentally, if `HEAD` contains the name of a commit, rather than a branch, then that is when you are in the rather florid-sounding *detached HEAD mode*.  We'll discuss the implications of that later, but suffice to say it may prove fatal for any commits you make whilst in that state.

If you poked around inside `.git/refs` you might have seen a few other things in there.  If your repository was cloned from elsewhere originally there will be `.git/refs/remotes/origin`, with details of the branches that exist in the origin repository, or at any rate, those that existed before the time of the last `git fetch`.  If anything in your repository has been tagged, then you might see `.git/refs/tags/` with tag references in it.  Tags, confusingly, can have their own objects too, but they always have a reference, and a tag which consists only of a reference to a commit is called a *lightweight tag*.  I'll go into what tags actually do a bit later on, but all you really need to know is that a tag is a reference that doesn't change after it has been created.  A branch is also a reference, but as you learned earlier, it changes when you make a new commit.

The above paragraph describes the references' equivalent of the loose object store.  There's also an equivalent of a pack: the file `.git/packed-refs`.  This file can store any number of references in a single place, each on a separate line&mdash;well, tags sometimes take up two lines.

When a branch moves from one commit to another, Git does log this happening, so the history of the branch can be traced later.  Each branch has its own *reflog* (that's ref-log, not re-flog), stored under the `.git/logs/` directory.  HEAD has a reflog too, and although you can view the files directly if you want, you can also see them in a slightly friendlier abbreviated way with the command `git reflog show [branch]`.

## Bringing it all back together

Before the above digression into the inner workings of a Git repository, I somewhat skimmed over the concept of *merging*: the process of taking two divergent branches and bringing them back together into a single harmonious whole.  It is not something we can ignore forever, though.  Merging is a big topic, with a lot of settings you can tweak to get slightly different results, but I'm going to try to stay at the high level and avoid a long list of semi-hypothetical situations.

Merging is, in essence, just what the name implies: the concept of merging some (usaually two) branches together.  There are three cases we have to consider, in increasing order of complexity:

1. Since the branches have diverged, only one has changed (and nothing from elsewhere has been merged in).
2. Since the branches have diverged, both have changed, but in different places.
3. Since the branches have diverged, they have changed in the same places.

You don't yourself need to know which case is which: Git will try to work out what does and does not apply.

Incidentally, before doing any sort of merging, you should make sure that your working area is in a clean and safe state.  Don't have any uncommitted changes lying about.  If you have changes you want to keep but don't want them in your current branch, make a new branch to pop them into temporarily.  If you try to do a merge, but you have uncommitted changes and the merge turns out to be a Type 3 (in my unofficial list above), you might get into a situation where you lose your uncommitted changes even if you try to cancel your sticky merge midway through.  So, it's best to be careful.

In case 1: imagine we had a `parent` branch, and we created a `child` branch by branching from it.  The `child` branch might have all sorts of commits on it, further branches, merges even, but the following conditions still apply:

* The `parent` branch has not had any commits since `child` branched from it.
* Every commit on the `child` branch is either also on the `parent` branch, or is a descendant of the commit at the tip of the `parent` branch.

We want to update the `parent` branch to contain all of the `child` commits.  To do this, we need to check `parent` out then merge `child` into it.  Git will work out for itself that this is the simplest type of merge, and will do what it calls a *fast-forward*.

```
> git checkout parent
Switched to branch 'parent'
> git merge child
Updating d8c80de..9d13b11
Fast-forward
[summary list of changes]
```

I've omitted here the section that shows you exactly what files in your working area have been changed by the merging.  The point here is that Git has worked out that only the other branch has had changes, so it can merge those changes into the current branch by moving the branch reference to the same commit as the tip of the other branch.  No new commits have been created.

If we were to try to do this the other way around, nothing would happen at all.  Git can see that every commit in `parent` is already part of `child`'s back-story, so it will tell you that nothing needs to be done:

```
> git checkout child
Switched to branch 'child'
> git merge parent
Already up to date.
```

If there have been changes in both branches, then the `git merge` command will realise that something more complex is needed.  First, it will follow the branches backwards to find the nearest common ancestor commit of both branch tip commits.  When it has found that point, it will review the changes that have occurred on both branches, between the common ancestor and the branch tips.  For text-like files it will review the changes line-by-line; for other file types, it will compare entire files.

Hopefully, there will be no *conflicts*.  Either there will be no non-text-like files that have changed on both sides, or there will be no text-like files that have changed in the same places in the file on both sides.  If that's the case, and there are no conflicts, we have a Case 2 merge.  If you're doing it on the command line, the output will look something like this:

```
> git checkout parent
Switched to branch 'parent'
> git merge child
Merge made by the 'recursive' strategy.
[summary list of changes]
```

There are other merge strategies; do not worry about them until you reach the level of God Tier Git User.

In this case, the merge command will have created a new commit, with two parents and an auto-generated log message.  If you're using a graphical tool that shows you a commit graph, it should be obvious that two chains of commits have been merged into one.

Unlike a fast-forward merge, this one could have been done in either direction.  We checked out `parent` and merged `child` into it, but as there are changes on both branches we could have done it the other way and merged `parent` into `child` instead.  The merge commit will only be on the branch we have currently checked out, though: if you do what we did and merge `child` into `parent`, then the tip of `parent` will advance to the merge commit, but the tip of `child` will remain where it was, on one of the parents of the merge commit.  All of the commits on the `child` branch are now also considered to be part of the `parent` branch, even though a commit graph still shows you the two separate streams of commits side by side.  What we do with `child` after this will depend on our workflow.  It might be that it has served its purpose and can be deleted (you can do that with `git branch -d child`).  We might want to develop it further, but not merge the changes in from `parent`; in that situation, we don't need to do anything at all.  We might want to keep `child`, but merge in both directions.  In that case, the second merge is just a fast-forward.  Let's redo that merge from the start:

```
> git checkout parent
Switched to branch 'parent'
> git merge child
Merge made by the 'recursive' strategy.
[summary list of changes]
> git checkout child
Switched to branch 'child'
> git merge parent
Updating c61b69b..58fdcf2
Fast-forward
[summary list of changes]
```

Now, the two branch tips are both pointing at the merge commit created by the first merge, the one in this example with name beginning `58fdcf2`.

You might be thinking: "what if the two branches don't have a common ancestor at all?"  In most repositories, there's just one root node, so there will always be a common ancestor *somewhere* in the history.  In the strange and peculiar case that you have multiple root nodes, then if there's no common ancestor, the branches just won't merge.  Git will give you an error message: `refusing to merge unrelated histories`.  You can get around this error with a bit of low-level hacking, but I'm not going to go into the details here on the grounds that this is extremely unlikely ever to actually happen to you.  Unless, of course, you deliberately provoke it.

Above, we skipped over the possibility of what might happen if there are conflicting changes in the two branches.  That is, if a file has been edited in the same place, or the same non-text-like file has been changed at all.  There are other slightly rarer conflicts, too, such as if a file has been deleted in one branch but changed in the other.  If something like this has happened, Git will spot it, and we will be in Case 3.  For a text-like file, it will modify the file so that it contains both versions of the text, flagged with *conflict markers* either side.  If you are using the command-line version of Git, you are expected to go into the conflicting files, edit them until they contain the correct combined version of the code, and then stage and commit the files to complete the merge.

That all is a little bit complex and dry, so it might make more sense if I give you a concrete example.  Imagine our repository has a file called `cakes.txt`, and it contains one line of text:

```
cakes
```

In the parent branch I'm going to edit the file so that it says `stakes`; in the child branch I edit it so that it says `drakes`.  Then, see what happens when we try to merge the child into the parent:

```
> git checkout parent
Switched to branch 'parent'
> git merge child
Auto-merging cakes.txt
CONFLICT (content): Merge conflict in cakes.txt
Automatic merge failed; fix conflicts and then commit the result.
```

If we then open `cakes.txt` we see it now has more than just one line inside it:

```
<<<<<<< HEAD
stakes
=======
drakes
>>>>>>> child
```

The lines with all the various symbols like `<<<` and so on are the conflict markers.  Here, it's trying to tell us that the HEAD branch contains `stakes`, the `child` branch contains `drakes`, and that we need to sort the problem out.

Our job, now, is to edit the file and produce some sort of combined version.  We don't have to accept one or the other; we just have to edit it into the shape we want it, and then continue with the merge.  When we're done, we tell Git that we want to continue.

```
> git add -A
> git merge --continue
[parent 924c217] Merge branch 'child' into parent
```

And now we have our merge commit created, just like before.

The annoying thing about conflict markers is that, for good reason, they break your file.  Nearly any sort of programming file will be broken by a conflict marker.  If you forget to resolve one, therefore, your files will probably be broken in a very annoying and obvious way.  Resolving conflicts is one of the ways in which the more advanced Git tools really do give you a big advantage compared to using the command line.  The best tool I have found for it is Microsoft Visual Studio, assuming you have a big enough monitor; it splits your screen into three parts.  At the top are the versions in each of the two branches, and below them is a "final output" pane.  You can select entire blocks on either side by clicking; for each conflict you can select either, both, or neither version, and you can also edit the "final output" section directly if you need to edit lines more closely.  Microsoft Visual Studio Code also makes a good attempt at putting an interface on top of the conflict markers themselves, but is almost too clever for its own good.  When I used it to create this guide, and entered the example conflict shown as a demonstration, it thought it meant that this file had a conflict itself and that I needed to fix it.

Earlier on, we discussed that one of the best ways to merge branches together, because of the review capabilities it gives you, is by a pull request in a hosting service.  All a pull request essentially does, at the end of the day, is a merge just like these; the benefit comes from the ability to review the changes easily beforehand.  What you can't generally do, though, is merge a pull request when the merge would conflict.  In general, the hosting services that offer pull requests just won't let you push the button when that happens.  To sort this out, the best thing to do is to merge the other way.  If you have a pull request open for merging `child` into `parent`, but your hosting service says it can't be done, the way to fix it is, on your local machine, merge `parent` into `child`.  This may well have conflicts, and you can resolve them.  Push the `child` branch up to the origin repository in the hosting service, including the merged changes from the `parent` branch, and your problematic pull request will be healthy again.  All hosting services will behave this way if there is a conflict; GitHub has additional settings on the repository, so you can tell it to make pull requests unmergable if there are any changes at all in the `parent` branch that are not in the `child` branch, even if the changes are not conflicting.