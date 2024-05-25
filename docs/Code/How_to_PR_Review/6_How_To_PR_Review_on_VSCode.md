## Creating Pull Request

### Install the official GitHub Extension

The first thing you need to do is to install the official **GitHub Pull Requests and Issues** extension for Visual Studio Code. You can find it in the marketplace by searching for “GitHub” or by clicking [here](https://marketplace.visualstudio.com/items?itemName=GitHub.vscode-pull-request-github).

![GitHub Pull Requests and Issues](https://leonardomontini.dev/_astro/_github-extension.DjZ5atLG_2oTVy2.webp)

_Note_: make sure to not get confused, the extension called “GitHub” is an old one and deprecated. The new one is called “GitHub Pull Requests and Issues”.

As soon as the extension is installed, you’ll see a new icon in the Activity Bar on the left side of Visual Studio Code:

![GitHub Pull Requests and Issues Icon](https://leonardomontini.dev/_astro/_github-extension-icon.BUZfUU0Q_ZW39YN.webp)

Opening it the first time will ask you to login to GitHub, just click on the button and a browser tab will open where you can login to your GitHub account.

### Create a Pull Request

Now that you’re logged in, you can create a Pull Request from Visual Studio Code by clicking the icon on the top bar:

![GitHub Pull Requests and Issues Header](https://leonardomontini.dev/_astro/_extension-header.DdM3shJz_VBgR.webp)

If you’re already on a pushed branch, this is the panel that will open:

![GitHub Pull Requests and Issues Preview](https://leonardomontini.dev/_astro/_pr-preview.Cs4_1wMX_Z1KFuef.webp)

#### Merge Changes From

The first panel you will see allows you to select the origin branch that is the one containing the changes you want to merge. By default, it will select the current branch you’re on.

You can also select the remote in case you have more than one. In the most common open source situation you’ll have your fork and the original repository. In this panel, you might want to select your fork.

#### Into

Similar to the previous panel it allows you to select a remote and a branch. This time it’s the destination. If you’re working on your own project the remote will likely be the same as the one you selected in the previous panel, but if you’re contributing to someone else’s project you’ll likely want to select the original repository.

Branch is usually `main` but make sure to read the project’s contribution guidelines to see if there’s a specific branch you should use.

#### Title

You PR’s title. By default it will use the message of the last commit, but you can change it to whatever you want.

#### Description

You PR’s description. If the project has a template in `.github/PULL_REQUEST_TEMPLATE.md`, you will see this box already filled with the template. Similar to the title, you’re free to change it.

#### Create as draft

The final option before the Create button is a checkbox that allows you to create the PR as a draft. This is useful if you want to create the PR but you’re not ready to merge it yet.

#### Create

Once you’re happy with the options you selected, you can click the Create button and the PR will be created on GitHub!

#### Compare changes

Waaaait a moment, before clicking the Create button, you can also open the Compare changes panel right below.

From this panel you can see all changes that will be included in the Pull Request in the diff format you’re used to see on vscode, so green files are added, red are deleted and yellow are modified.

![Compare panel](https://leonardomontini.dev/_astro/_compare.DLqAXuBf_10Qtdo.webp)

#### Add labels

Aren’t labels also supported? I can’t see them in the screenshot… well, that’s because the button only shows up if you hover the mouse over the top bar.

To be honest I’m not sure this is a feature or a bug, I might open an issue on the extension’s repository to ask about it.

Anyway, if you click that button, the quick pick menu will open on vscode letting you select the labels you want to add to the PR from the list of labels available in the repository.

![Add labels](https://leonardomontini.dev/_astro/_labels.1z_xkG9x_Z2sffM0.webp)

And as I [mention in the video](https://youtu.be/Ma67EbIHi64), this is exactly where I found the bug!

[https://github.com/microsoft/vscode-pull-request-github/pull/4637](https://github.com/microsoft/vscode-pull-request-github/pull/4637)

## Reviewing Pull Request
### Changed files

The first thing you notice when selecting a Pull Request from the sidebar is the changed files.

![Changed files](https://leonardomontini.dev/_astro/_changed-files.C8NzgZGb_2wxCrp.webp)

When clicking on one of them a Diff Editor will open so that you can see the changes made to the file by comparing them to the base branch.

### Pull Request Overview

If you click on “Description” from the sidebar, you’ll see the Pull Request overview:

![Pull Request Overview](https://leonardomontini.dev/_astro/_pr-overview.Ckr5uoyQ_1yjndd.webp)

From here you can see all the relevant information you can also find on the web UI on GitHub. It’s not readonly though, but you can fully interact with it from the editor.

You can for example edit labels, assignees, reviewers, and even add comments.

### Add comments

Speaking of comments, we just saw you can add some to the Pull Request, but you can also add comments to specific lines of code and files!

The UI interaction is the same as on GitHub, you can add a comment by clicking on the line number and then clicking on the “Add comment” button or by dragging the mouse over the lines you want to comment, in case of a multiline comment.

### Checkout the branch

Probably the most interesting feature of the extension is the ability to checkout the branch of the Pull Request directly from the editor. This gives you a lot of advantages, for example you can run and build the code in local so you can test your application.

In case you don’t have a CI/CD pipeline in place (you should though, at least CI), you can also run the tests locally to make sure they pass.

### Check GitHub Actions

Speaking of CI/CD, you can also check the status of the GitHub Actions workflow directly from the editor!

![GitHub Actions](https://leonardomontini.dev/_astro/_ci-status.BQeKFBqL_10LiCa.webp)

### Edit tabs

Before calling it a day, I want to show you another cool feature of the extension: the ability to edit the tabs and organize the filters in the sidebar.

If you hover the mouse on them, you notice a pencil icon. Click on it! It will open your local vscode settings and you can notice this piece of configuration:

```
githubPullRequests.queries": [
    {
      "label": "Waiting For My Review",
      "query": "is:open review-requested:${user}"
    },
    {
      "label": "Assigned To Me",
      "query": "is:open assignee:${user}"
    },
    {
      "label": "Created By Me",
      "query": "is:open author:${user}"
    },
  ],
```

Do you recognize the labels? Those are the default ones defining your tabs when you install the extension! You can change them to whatever you want, for example I added one for PRs where I’ve been mentioned, by adding a new element to the array:

```
{
  "label": "Pull Requests where I've been mentioned",
  "query": "is:open mentions:${user}"
}
```

The syntax is pretty straightforward, on label you put… the label! And on query you can define how PRs will be filtered as you would query them on GitHub.

### Conclusion

That’s it for today!

Last week we learned how to create a Pull Request from Visual Studio Code, and today we saw how to give it a review.