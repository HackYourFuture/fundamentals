# Handing in homework

Starting with  the JavaScript2 module we expect you to hand in homework by using the GitHub [Forking Workflow](https://github.com/HackYourFuture/Git/blob/master/Lecture-3.md). This involves the following steps:

## Forking 

1. Go to the [HackYourFuture GitHub page](https://github.com/HackYourFuture) and click on the repository of the current HYF module, e.g. `JavaScript2`.

2. In the upper-right corner, press the [**Fork**] button.

3. On the subsequent dialog box, select your own GitHub account as the place to fork this repository to.

4. After a couple of seconds, you will be taken to your own GitHub account, in the repository just forked.

5. Now, clone this forked repository (_not the original HYF one!_) to your laptop.

## Commit and Push

For each week's homework repeat the following steps.

1. If there are any outstanding changes from the previous week, either commit or discard them.

2. Start afresh by resetting your local repository back to the `master` branch. This ensures that the new homework is not mixed up with the homework of previous weeks. (That previous homework will still be available in their respective branches.) Use this command:

    ```
    git checkout master
    ```

3. Create a new branch for this week's homework (replace `new-branch-name` with the name of your new branch, e.g. 'homework-week1'):

    ```
    git checkout -b new-branch-name
    ```

4. Make changes to your local working directory as required by the homework assignment(s).

5. Commit your changes as and when required. The recommended way to add and commit files is to keep a `.gitignore` file in the root folder of your working directory that lists all the files and directories you want to leave untracked by Git. Now, when you use a `git add .` command, all new, changed and/or deleted files, except those from `.gitignore`, are added to the staging area. 

    ```
    git add .
    git commit -m "your commit message"
    ```

    Make sure that your commit messages are meaningful and accurately describe what the commit is about. Messages such as `my changes` are not okay. If you need to go back in time to review a particular change from the past, the commit messages are the only clues to go by for finding the relevant commit. For an in-depth discussion on how to write good commit messages, see [How to Write a Git Commit Message](https://chris.beams.io/posts/git-commit/).

6. Push your commits to GitHub. You don't need to do this after every individual commit, but pushing often to your own repository is advisable as a safeguard against disk failure on your laptop. The first time you push your new branch you must use the following command (replace `new-branch-name` with the name of your new branch, e.g. 'homework-week1'):

    ```
    git push -u origin new-branch-name
    ```

    Subsequent pushes can simply be done with:

    ```
    git push
    ```

## Create Pull Request

When you have finished your homework and wish to hand it in, do so via a **pull request** (PR).

1. Make sure that you committed and pushed your final changes in the working directory.

2. Go the your GitHub account and select the repository containing your homework.

3. Click the button [**New pull request**].

4. Select the HackYourFuture repository master branch on the left side and your repository and homework branch on the right side.

5. Press the green [**Create pull request**] button to continue.

6. Add a title for your pull request, e.g. 'Homework Week 1'. You can optionally add comments for the pull request reviewer, e.g. known issues with your current homework.

7. Finally, press the green [**Create pull request**] button to finish sending off the pull request.

## Resources

Take a look at [this video](https://www.youtube.com/watch?v=-o0yomUVVpU)
made by Daan, where he explains this process in detail.

Also review the [Git workflow material](https://github.com/HackYourFuture/Git/blob/master/Lecture-3.md)
and use it as a reference.
