## 1. Provide context: Description and images

### Description

When I talk about creating a description, I don’t mean to just link the Jira ticket to the PR and be done with it. This would require the reviewer to switch to Jira and read the description, which might have more information than necessary for code review… and yet give no clue about how the solution is implemented.

You can avoid this by providing a brief description and answering three questions:

- **What?** What is the task that is accomplished by this pull request?
- **How?** How is it implemented (an overview of your solution)?
- **Why?** If applicable (e.g., if several valid approaches exist), why did you choose this approach, or what are the impediments of the other approaches?

Thanks to this description, the reviewer will know what to look for in the code and why it was written the way it was. This enables them to provide more relevant comments.

You can ensure this by adding a pull request template in GitHub.

### Images and videos

Evaluating the code while knowing what the actual result looks like is much easier than trying to imagine it. If your task is related to changes in the UI, adding an image of the end result (or a video, if it’s a flow) will greatly benefit the reviewer’s comprehension of the code. Or, if your task involves complicated logic, you can attach a sequence diagram that explains the algorithm.

## 2. Keep it small

The bigger the pull request, the poorer the code review will be. Code review is a tough process mentally — you need to read code, figure out what it’s doing, understand how it’s doing it, and look for potential problems. The more lines of code you need to keep in mind, the higher the chance that you’ll overlook something. Although there isn’t really a “golden” number of lines, I’d recommend sticking to under 500. If there are more, split them into multiple pull requests.

## 3. Keep it clean

No, I’m not talking about the architecture here. During development, we use all kinds of tricks: generating print logs, hard-coding specific values, leaving `TODO`s, and so on. To save some time and keep the reviewer’s focus on the feature at hand, make it a habit to ensure your pull request is clean before assigning reviewers. What do I mean by keeping it clean?

1. Find and remove all of the debug logs and unused or commented-out code.
2. Decide what to do with the `TODO`:
    1. Implement it.
    2. If you can’t implement it right away, create a task or leave at least some kind of estimation on when it will be done.
    3. Delete it if it’s either done or not relevant anymore.
3. Regarding hardcoded strings (albeit not all of the possible hardcoded values), I’ve recently stumbled upon a [tip from Vandad Nahavandipoor](https://github.com/vandadnp/flutter-tips-and-tricks#hardcoded-strings-in-flutter) about suffixing all of your hardcoded strings with an emoji (with the help of an extension function, for example).

> Pro tip: Most of those issues can be resolved with static code analysis tools, although some of them require custom rules implementation, depending on how you want to handle them.

## 4. Resolve all of the merge conflicts

This may seem like a minor thing because the feature is done, the code is final, and you can leave merge conflicts for later… But that isn’t always true. Your branch may have diverged a lot from the base branch, or some changes might clash with your code in a way that makes it impossible to merge without refactoring. And this will either require another review or get merged with possibly overlooked issues.

## 5. Exclude code-gen files

Code-generated files, such as JSON models or unit test mocks, don’t need to be reviewed. So seeing them explicitly in a diff doesn’t add any benefit and is just annoying. Different Git clients have different approaches to excluding such files from review. For example, in GitHub, you can [add the patterns to `.gitattributes`](https://twitter.com/mkobuolys/status/1489610640335515648?s=20&t=Z1vYY62HbLDYeBIHLO-Tig), and they will be collapsed in the final diff.

![https://pbs.twimg.com/media/FKwn0QDXEAUi_4q?format=jpg&amp;name=large](https://blog.codemagic.io/FKwn0QDXEAUi_4q_18250863249198792760_hudda3d95600c53ca7a0bcece824b999b0_0_1280x1800_fit_q90_linear.jpg)

## 6. Static code analysis

It’s a good practice to have static code analysis enabled.

> Static analysis allows you to find problems before executing a single line of code. It’s a powerful tool used to prevent bugs and ensure that code conforms to style guidelines.

Before assigning reviewers, make sure that your code doesn’t violate any lint rules. You can make sure of this with CI: Block the possibility of merging until there are no lint warnings.

## 7. Unit tests

Unit tests help identify problems and catch bugs in the early stages of development before anyone else even sees the code, let alone tests it. Additionally, they ensure that code behavior stays the same in case of code changes. As a bonus, they also cause you to write cleaner and more decoupled code, contributing to the overall code quality.

You can set up CI to run unit tests on every pull request and block merging in case anything fails. And you should fix any failing tests before marking the pull request as ready for review.

> Pro tip: Enable code coverage reports with tools like [Codecov](https://about.codecov.io/).

## 8. Other markers, such as milestones and labels

Using markers such as milestones and labels is more of a nice-to-have tip, and depending on the size of the project, it can be omitted. But in larger projects, it can help to organize pull requests. For example, if there are a lot of pull requests to review, they can be filtered by milestone so that the PRs for the upcoming release are reviewed sooner. And labels can provide more context by indicating bugs, features, enhancements, etc.

## 9. Add GitHub PR template

1. In the root folder of our project, we need to add a file called `pull_request_template.md`.
2. Fill it in (commit, push, and merge into `main`).

```markdown
## Description

1. What type of code this is (e.g., bug fix, new feature)
2. What it does and how it does it
3. Why it is done that way (if required)
4. Nice to have: images/videos (if it involves the UI) 

## Checklist

- [ ] Has unit tests
- [ ] Coverage is at least 90%
- [ ] Has documentation 
- [ ] Has release milestone
```

The next time we open a pull request, it will already be prefilled for us:

![Screenshot 2022-02-13 at 23.21.45.png#center](https://blog.codemagic.io/uploads/2022/04/Screenshot_2022-02-13_at_23.21.45.png#center)

## **Bonus tip**

Review your own code before assigning other reviewers. Yes, you have just written this code and still remember everything. But giving it a fresh look, especially in the GUI of the Git client, can help you find some things that you may have previously overlooked.