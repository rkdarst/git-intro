exercises: 30
  - How can we find out when exactly a line of code was changed?
  - How can we navigate past versions of the code?
  - Quickly find a line of code, find out why it was introduced and when.
  - "`git log/grep/annotate/show/bisect` is a powerful combination when doing archaeology in a project."

Please make sure that you do not clone repositories inside an already tracked folder:

If you are inside an existing Git repository, step out of it.
You need to find a different location since we will clone a new repository.
If you see this message, this is good in this case:
```
fatal: not a git repository (or any of the parent directories): .git
---
## Our toolbox for history inspection
First the instructor demonstrates four commands on a real life example
repository [networkx](https://github.com/networkx/networkx) (mentioned in the amazing site [The
Programming Historian](https://programminghistorian.org/)).
Later we will practice these in groups in archaeology exercise.
### `git grep` to search through the repository
With `git grep` you can find all lines in a repository which contain some string or regular expression.
This is useful to find out where in the code some variable is used or some error message printed:
$ git grep sometext
In the [networkx](https://github.com/networkx/networkx) repository you can try:
$ git grep -i fixme
### `git log -S` to search through the history of changes
While `git grep` searches the **current state** of the repository,
it is possible to search also through all changes for "sometext":
$ git log -S sometext
In the [networkx](https://github.com/networkx/networkx) repository you can try:
$ git log -S test_weakly_connected_component
### `git show` to inspect commits
We have seen this one before already. Using `git show` we can inspect an individual commit if
we know its hash:
$ git show somehash
For instance:
$ git show 759d589bdfa61aff99e0535938f14f67b01c83f7
```
### `git annotate` to annotate code with commit metadata
Try it out on a file - with `git annotate` you can see line by line who and **when** the line was modified
last. It also prints the precise hash of the last change which modified each line. Incredibly useful
for reproducibility.
$ git annotate somefile
Example:
```
$ git annotate networkx/convert_matrix.py
```
If you annotate in a terminal and the file is longer than the screen, Git by default uses the program `less` to
scroll the output.
Use `/sometext` `<ENTER>` to find "sometext" and you can cycle through the results with `n` (next) and `N` (last).
You can also use page up/down to scroll. You can quit with `q`.
### `git checkout -b` to inspect code in the past
We can create branches pointing to a commit in the past.
This is the recommended mechanism to inspect old code:
$ git checkout -b branchname somehash
Example:
  # create branch called "older-code" from hash 347e6292419b
$ git checkout -b older-code 347e6292419bd0e4bff077fe971f983932d7a0e9
  # now you can navigate and inspect the code as it was back then
  # ...

  # after we are done we can switch back to "master"
$ git checkout master

  # if we like we can delete the "older-code" branch
$ git branch -d older-code
```
> Let us explore the value of these commands in an exercise.
>
> **In-person workshops**: We recommend that you
> do this exercise in groups of two and discuss with your neighbors.
>
> **Video workshops**: We will group you in breakout rooms of ~4 persons where you
> can work and discuss together. A helper or instructor will pop in to help out.
> Please write down questions in the collaborative notes.
> After 15 minutes we will bring you back into the main room and discuss.
>
> Exercise steps:
> 1. Clone this repository:
>    [https://github.com/tidyverse/rvest](https://github.com/tidyverse/rvest).
>    Then step into the new directory:
>    ```
>    $ git clone --branch v0.3.5 https://github.com/tidyverse/rvest.git
>    $ cd rvest
>    ```
> 2. Find the code line which contains `'No links matched that expression'`
> 3. Find out when this line was last modified. Find the actual commit which modified that line.
> 4. Inspect that commit with `git show`.
> 5. Create a branch pointing to the past when that commit was created to be able to browse and use the code as it was back then.
> 6. How would can you bring the code to the commit precisely before that line was last modified?
{: .challenge}
## Finding out when something broke with `git bisect`

> ## How would you solve this?
>
> Before we go on first discuss how you would solve this problem: You know that it worked
> 500 commits ago but it does not work now.
>
> - How would you find the commit which changed it?
> - Why could it be useful to know the commit that changed it?
>
> **Video workshops**: Write down ideas on how you would solve it in the collaborative
> note and we will discuss various approaches.
{: .discussion}

---

We will probably arrive at a solution which is similar to `git bisect`:

> - Clone or fork this repository and use `git bisect` to find the commit which
>   broke the computation
>   ([solution - spoiler alert!](https://github.com/coderefinery/git-bisect-exercise#solutions-spoiler-alert)).
> - Once you have found the offending commit, also practice navigating to the last good commit.
> - Bonus exercise:
>   Write a script that checks for a correct result and use `git bisect run` to
>   find the offending commit automatically
>   ([solution - spoiler alert!](https://github.com/coderefinery/git-bisect-exercise#solutions-spoiler-alert)).
>
> **Video workshops**: We will group you in breakout rooms of ~4 persons where you
> can work and discuss together. A helper or instructor will pop in to help out.
> Please write down questions in the collaborative notes.
> After 15-20 minutes we will bring you back into the main room and discuss.
>
> #### Hints
> Finding the first commit:
> How to navigate to the parent of a commit with hash `somehash`:
>
> ```shell
>   # create branch pointing to the parent of somehash
> $ git checkout -b branchname somehash~1
>
>   # instead of a tilde you can also use this
> $ git checkout -b branchname somehash^
> ```
{: .challenge}