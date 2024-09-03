---
title: Exploring History
teaching: 25
exercises: 0
---

::::::::::::::::::::::::::::::::::::::: objectives

- Explain what the HEAD of a repository is and how to use it.
- Identify and use Git commit numbers.
- Compare various versions of tracked files.
- Restore old versions of files.

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- How can I identify old versions of files?
- How do I review my changes?
- How can I recover old versions of files?

::::::::::::::::::::::::::::::::::::::::::::::::::

As we saw in the previous episode, we can refer to commits by their
identifiers.  You can refer to the *most recent commit* of the working
directory by using the identifier `HEAD`.

We've been adding one line at a time to `amr-data-dictionary.txt`, so it's easy to track our
progress by looking, so let's do that using our `HEAD`s.  Before we start,
let's make a change to `amr-data-dictionary.txt`, adding yet another line.

```bash
$ tail amr-data-dictionary.txt
```

Showing only the last few lines:

```output
* sex_male Binary - indicates whether the person from whom the specimen was taken was male or not. 1 (male) 0 (not male)
* region Character - a string indicating the England region of laboratory testing the specimen
* had_surgery_past_yr Binary - indicates whether person from whom sample was taken had undergone surgery in hospital in the past year before specimen taken. 1 (surgery within last year) 0 (No surgery within last year)
* ethnicity Character - indicates self-reported ethnicity group according to Office for National Statistics groupings
* imd Integer - indicates the Index of Multiple Deprivation for residence for person from whom specimen was taken. Range: 1 (least deprived) - 5 (most deprived)
* organism Character - indicates the species name for the organism detected
* coamox Binary - indicates specimen was resistant to Coamoxiclav
* gentam Binary - indicates specimen was resistant to Gentamicin
* ciprof Binary - indicates specimen was resistant to Ciprofloxacin
* name Character - a string giving the name of the person from whom the specimen was taken
```

Now, let's see what we get.

```bash
$ git diff HEAD amr-data-dictionary.txt
```

```output
diff --git a/amr-data-dictionary.txt b/amr-data-dictionary.txt
index c9a8214..d7d742c 100644
--- a/amr-data-dictionary.txt
+++ b/amr-data-dictionary.txt
@@ -15,3 +15,4 @@ These data represent the sort of data that might be obtained from the Second Gen
 * coamox Binary - indicates specimen was resistant to Coamoxiclav
 * gentam Binary - indicates specimen was resistant to Gentamicin
 * ciprof Binary - indicates specimen was resistant to Ciprofloxacin
+* name Character - a string giving the name of the person from whom the specimen was taken
```

which is the same as what you would get if you leave out `HEAD` (try it).  The
real goodness in all this is when you can refer to previous commits.  We do
that by adding `~1`
(where "~" is "tilde", pronounced [**til**\-d*uh*])
to refer to the commit one before `HEAD`.

```bash
$ git diff HEAD~1 amr-data-dictionary.txt
```

If we want to see the differences between older commits we can use `git diff`
again, but with the notation `HEAD~1`, `HEAD~2`, and so on, to refer to them:

```bash
$ git diff HEAD~2 amr-data-dictionary.txt
```

```output
diff --git a/amr-data-dictionary.txt b/amr-data-dictionary.txt
index 55895cc..d7d742c 100644
--- a/amr-data-dictionary.txt
+++ b/amr-data-dictionary.txt
@@ -2,3 +2,17 @@ AMR data
 100,000 rows of 12 variables

 These data represent the sort of data that might be obtained from the Second Generation Surveillance System (SGSS)
+
+* id Integer - A unique identifier for each person
+* dob Character - a string giving the date of birth
+* spec_date Character - a string giving the date a specimen was taken
+* sex_male Binary - indicates whether the person from whom the specimen was taken was male or not. 1 (male) 0 (not male)
+* region Character - a string indicating the England region of laboratory testing the specimen
+* had_surgery_past_yr Binary - indicates whether person from whom sample was taken had undergone surgery in hospital in the past year before specimen taken. 1 (surgery within last year) 0 (No surgery within last year)
+* ethnicity Character - indicates self-reported ethnicity group according to Office for National Statistics groupings
+* imd Integer - indicates the Index of Multiple Deprivation for residence for person from whom specimen was taken. Range: 1 (least deprived) - 5 (most deprived)
+* organism Character - indicates the species name for the organism detected
+* coamox Binary - indicates specimen was resistant to Coamoxiclav
+* gentam Binary - indicates specimen was resistant to Gentamicin
+* ciprof Binary - indicates specimen was resistant to Ciprofloxacin
+* name Character - a string giving the name of the person from whom the specimen was taken
```

We could also use `git show` which shows us what changes we made at an older commit as
well as the commit message, rather than the *differences* between a commit and our
working directory that we see by using `git diff`.

```bash
$ git show HEAD~2 amr-data-dictionary.txt
```

```output
commit 0f988204ddcf33c060ecb849d640b3bd7aec71cc
Author: John Doe <john.doe@unknown.com>
Date:   Wed Aug 14 14:54:11 2024 +0100

    Start data dictionary

diff --git a/amr-data-dictionary.txt b/amr-data-dictionary.txt
new file mode 100644
index 0000000..55895cc
--- /dev/null
+++ b/amr-data-dictionary.txt
@@ -0,0 +1,4 @@
+AMR data
+100,000 rows of 12 variables
+
+These data represent the sort of data that might be obtained from the Second Generation Surveillance System (SGSS)
```

In this way,
we can build up a chain of commits.
The most recent end of the chain is referred to as `HEAD`;
we can refer to previous commits using the `~` notation,
so `HEAD~1`
means "the previous commit",
while `HEAD~123` goes back 123 commits from where we are now.

We can also refer to commits using
those long strings of digits and letters
that both `git log` and `git show` display.
These are unique IDs for the changes,
and "unique" really does mean unique:
every change to any set of files on any computer
has a unique 40-character identifier.
Our first commit was given the ID
`0f988204ddcf33c060ecb849d640b3bd7aec71cc`,
so let's try this:

```bash
$ git diff 0f988204ddcf33c060ecb849d640b3bd7aec71cc amr-data-dictionary.txt
```

```output
diff --git a/amr-data-dictionary.txt b/amr-data-dictionary.txt
index 55895cc..d7d742c 100644
--- a/amr-data-dictionary.txt
+++ b/amr-data-dictionary.txt
@@ -2,3 +2,17 @@ AMR data
 100,000 rows of 12 variables

 These data represent the sort of data that might be obtained from the Second Generation Surveillance System (SGSS)
+
+* id Integer - A unique identifier for each person
+* dob Character - a string giving the date of birth
+* spec_date Character - a string giving the date a specimen was taken
+* sex_male Binary - indicates whether the person from whom the specimen was taken was male or not. 1 (male) 0 (not male)
+* region Character - a string indicating the England region of laboratory testing the specimen
+* had_surgery_past_yr Binary - indicates whether person from whom sample was taken had undergone surgery in hospital in the past year before specimen taken. 1 (surgery within last year) 0 (No surgery within last year)
+* ethnicity Character - indicates self-reported ethnicity group according to Office for National Statistics groupings
+* imd Integer - indicates the Index of Multiple Deprivation for residence for person from whom specimen was taken. Range: 1 (least deprived) - 5 (most deprived)
+* organism Character - indicates the species name for the organism detected
+* coamox Binary - indicates specimen was resistant to Coamoxiclav
+* gentam Binary - indicates specimen was resistant to Gentamicin
+* ciprof Binary - indicates specimen was resistant to Ciprofloxacin
+* name Character - a string giving the name of the person from whom the specimen was taken
```

That's the right answer,
but typing out random 40-character strings is annoying,
so Git lets us use just the first few characters (typically seven for normal size projects):

```bash
$ git diff 0f98820 amr-data-dictionary.txt
```

All right! So
we can save changes to files and see what we've changed. Now, how
can we restore older versions of things?
Let's suppose we change our mind about the last update to
`amr-data-dictionary.txt` (we realise the `name` variable is not actually part of the data).

`git status` now tells us that the file has been changed,
but those changes haven't been staged:

```bash
$ git status
```

```output
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   amr-data-dictionary.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

We can put things back the way they were
by using `git restore`:

```bash
$ git restore amr-data-dictionary.txt
$ tail amr-data-dictionary.txt
```

```output
* spec_date Character - a string giving the date a specimen was taken
* sex_male Binary - indicates whether the person from whom the specimen was taken was male or not. 1 (male) 0 (not male)
* region Character - a string indicating the England region of laboratory testing the specimen
* had_surgery_past_yr Binary - indicates whether person from whom sample was taken had undergone surgery in hospital in the past year before specimen taken. 1 (surgery within last year) 0 (No surgery within last year)
* ethnicity Character - indicates self-reported ethnicity group according to Office for National Statistics groupings
* imd Integer - indicates the Index of Multiple Deprivation for residence for person from whom specimen was taken. Range: 1 (least deprived) - 5 (most deprived)
* organism Character - indicates the species name for the organism detected
* coamox Binary - indicates specimen was resistant to Coamoxiclav
* gentam Binary - indicates specimen was resistant to Gentamicin
* ciprof Binary - indicates specimen was resistant to Ciprofloxacin
```

As you might guess from its name,
`git restore` restores an old version of a file.
By default,
it recovers the version of the file recorded in `HEAD`,
which is the last saved commit.
If we want to go back even further,
we can use a commit identifier instead, using `-s` option:

```bash
$ git restore -s 0f98820 amr-data-dictionary.txt
```

```bash
$ cat amr-data-dictionary.txt
```

```output
AMR data
100,000 rows of 12 variables

These data represent the sort of data that might be obtained from the Second Generation Surveillance System (SGSS)
```

```bash
$ git status
```

```output
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   amr-data-dictionary.txt

no changes added to commit (use "git add" and/or "git commit -a")

```
Notice that the changes are currently in the staging area. 
Again, we can put things back the way they were by using `git restore`:

```bash
$ git restore amr-data-dictionary.txt
$ cat amr-data-dictionary.txt
```

```output
AMR data
100,000 rows of 12 variables

These data represent the sort of data that might be obtained from the Second Generation Surveillance System (SGSS)

* id Integer - A unique identifier for each person
* dob Character - a string giving the date of birth
* spec_date Character - a string giving the date a specimen was taken
* sex_male Binary - indicates whether the person from whom the specimen was taken was male or not. 1 (male) 0 (not male)
* region Character - a string indicating the England region of laboratory testing the specimen
* had_surgery_past_yr Binary - indicates whether person from whom sample was taken had undergone surgery in hospital in the past year before specimen taken. 1 (surgery within last year) 0 (No surgery within last year)
* ethnicity Character - indicates self-reported ethnicity group according to Office for National Statistics groupings
* imd Integer - indicates the Index of Multiple Deprivation for residence for person from whom specimen was taken. Range: 1 (least deprived) - 5 (most deprived)
* organism Character - indicates the species name for the organism detected
* coamox Binary - indicates specimen was resistant to Coamoxiclav
* gentam Binary - indicates specimen was resistant to Gentamicin
* ciprof Binary - indicates specimen was resistant to Ciprofloxacin
```


It's important to remember that
we must use the commit number that identifies the state of the repository
*before* the change we're trying to undo.
A common mistake is to use the number of
the commit in which we made the change we're trying to discard.
In the example below, we want to retrieve the state from before the most
recent commit (`HEAD~1`), which is commit `f22b25e`. We use the `.` to mean all files:

![](fig/git-restore.svg){alt='A diagram showing how git restore can be used to restore the previous version of two files'}

So, to put it all together,
here's how Git works in cartoon form:

![https://figshare.com/articles/How_Git_works_a_cartoon/1328266](fig/git_staging.svg){alt='A diagram showing the entire git workflow: local changes are staged using git add, applied to the local repository using git commit, and can be restored from the repository using git checkout'}


The fact that files can be reverted one by one
tends to change the way people organize their work.
If everything is in one large document,
it's hard (but not impossible) to undo changes to the introduction
without also undoing changes made later to the conclusion.
If the introduction and conclusion are stored in separate files,
on the other hand,
moving backward and forward in time becomes much easier.

:::::::::::::::::::::::::::::::::::::::  challenge

## Recovering Older Versions of a File

Jennifer has made changes to the Python script that she has been working on for weeks, and the
modifications she made this morning "broke" the script and it no longer runs. She has spent
\~ 1hr trying to fix it, with no luck...

Luckily, she has been keeping track of her project's versions using Git! Which commands below will
let her recover the last committed version of her Python script called
`data_cruncher.py`?

1. `$ git restore`

2. `$ git restore data_cruncher.py`

3. `$ git restore -s HEAD~1 data_cruncher.py`

4. `$ git restore -s <unique ID of last commit> data_cruncher.py`

5. Both 2 and 4

:::::::::::::::  solution

## Solution

The answer is (5)-Both 2 and 4.

The `restore` command restores files from the repository, overwriting the files in your working
directory. Answers 2 and 4 both restore the *latest* version *in the repository* of the file
`data_cruncher.py`. Answer 2 uses `HEAD` to indicate the *latest*, whereas answer 4 uses the
unique ID of the last commit, which is what `HEAD` means.

Answer 3 gets the version of `data_cruncher.py` from the commit *before* `HEAD`, which is NOT
what we wanted.

Answer 1 results in an error. You need to specify a file to restore. If you want to restore all files
you should use `git restore .`



:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::  challenge

## Reverting a Commit

Jennifer is collaborating with colleagues on her Python script.  She
realizes her last commit to the project's repository contained an error, and
wants to undo it.  Jennifer wants to undo correctly so everyone in the project's
repository gets the correct change. The command `git revert [erroneous commit ID]` will create a
new commit that reverses the erroneous commit.

The command `git revert` is
different from `git restore -s [commit ID] .` because `git restore` returns the
files not yet committed within the local repository to a previous state, whereas `git revert`
reverses changes committed to the local and project repositories.

Below are the right steps and explanations for Jennifer to use `git revert`,
what is the missing command?

1. `________ # Look at the git history of the project to find the commit ID`

2. Copy the ID (the first few characters of the ID, e.g. 0b1d055).

3. `git revert [commit ID]`

4. Type in the new commit message.

5. Save and close

:::::::::::::::  solution

## Solution

The command `git log` lists project history with commit IDs.

The command `git show HEAD` shows changes made at the latest commit, and lists
the commit ID; however, Jennifer should double-check it is the correct commit, and no one
else has committed changes to the repository.



:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::  challenge

## Understanding Workflow and History

What is the output of the last command in

```bash
$ cd planets
$ echo "Venus is beautiful and full of love" > venus.txt
$ git add venus.txt
$ echo "Venus is too hot to be suitable as a base" >> venus.txt
$ git commit -m "Comment on Venus as an unsuitable base"
$ git restore venus.txt
$ cat venus.txt #this will print the contents of venus.txt to the screen
```

1. ```output
  Venus is too hot to be suitable as a base
  ```
2. ```output
  Venus is beautiful and full of love
  ```
3. ```output
  Venus is beautiful and full of love
  Venus is too hot to be suitable as a base
  ```
4. ```output
  Error because you have changed venus.txt without committing the changes
  ```

:::::::::::::::  solution

## Solution

The answer is 2.

The command `git add venus.txt` places the current version of `venus.txt` into the staging area.
The changes to the file from the second `echo` command are only applied to the working copy,
not the version in the staging area.

So, when `git commit -m "Comment on Venus as an unsuitable base"` is executed,
the version of `venus.txt` committed to the repository is the one from the staging area and
has only one line.

At this time, the working copy still has the second line (and
`git status` will show that the file is modified). However, `git restore venus.txt`
replaces the working copy with the most recently committed version of `venus.txt`.

So, `cat venus.txt` will output

```output
Venus is beautiful and full of love.
```

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::  challenge

## Checking Understanding of `git diff`

Consider this command: `git diff HEAD~9 amr-data-dictionary.txt`. What do you predict this command
will do if you execute it? What happens when you do execute it? Why?

Try another command, `git diff [ID] amr-data-dictionary.txt`, where [ID] is replaced with
the unique identifier for your most recent commit. What do you think will happen,
and what does happen?


::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::  challenge

## Getting Rid of Staged Changes

`git restore` can be used to restore a previous commit when unstaged changes have
been made, but will it also work for changes that have been staged but not committed?
Make a change to `amr-data-dictionary.txt`, add that change using `git add`,
then use `git restore` to see if you can remove your change.

:::::::::::::::  solution

## Solution

After adding a change, `git restore` can not be used directly.
Let's look at the output of `git status`:

```output
On branch main
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   amr-data-dictionary.txt

```

Note that if you don't have the same output
you may either have forgotten to change the file,
or you have added it *and* committed it.

Using the command `git restore amr-data-dictionary.txt` now does not give an error,
but it does not restore the file either.
Git helpfully tells us that we need to use `git restore --staged` first
to unstage the file:

```bash
$ git restore --staged amr-data-dictionary.txt
```


Now, `git status` gives us:

```bash
$ git status
```

```output
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git git restore <file>..." to discard changes in working directory)

        modified:   amr-data-dictionary.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

This means we can now use `git restore` to restore the file
to the previous commit:

```bash
$ git restore amr-data-dictionary.txt
$ git status
```

```output
On branch main
nothing to commit, working tree clean
```

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::  challenge

## Explore and Summarize Histories

Exploring history is an important part of Git, and often it is a challenge to find
the right commit ID, especially if the commit is from several months ago.

Imagine the `data-dictionary` project has more than 50 files.
You would like to find a commit that modifies some specific text in `amr-data-dictionary.txt`.
When you type `git log`, a very long list appeared.
How can you narrow down the search?

Recall that the `git diff` command allows us to explore one specific file,
e.g., `git diff amr-data-dictionary.txt`. We can apply a similar idea here.

```bash
$ git log amr-data-dictionary.txt
```

Unfortunately some of these commit messages are very ambiguous, e.g., `update files`.
How can you search through these files?

Both `git diff` and `git log` are very useful and they summarize a different part of the history
for you.
Is it possible to combine both? Let's try the following:

```bash
$ git log --patch amr-data-dictionary.txt
```

You should get a long list of output, and you should be able to see both commit messages and
the difference between each commit.

Question: What does the following command do?

```bash
$ git log --patch HEAD~9 *.txt
```

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: keypoints

- `git diff` displays differences between commits.
- `git restore` recovers old versions of files.

::::::::::::::::::::::::::::::::::::::::::::::::::
