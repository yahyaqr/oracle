Address and resolve all comments before merging. This way, you won’t leave anyone guessing what was implemented and what wasn’t. And by addressing, I don’t mean that you have to implement every single comment, but at least leave a reply or react with an emoji to indicate that it was handled or won’t be handled. Then the reviewer can resolve the conversation. GitHub even has a setting you can enable that blocks merging unless all conversations have been resolved.

## Miscellaneous tips

Below, you can find a very small list of other things that you can do to improve code review and quality.

1. When writing code, try to read it. If you have to compute a lot as you read, then consider refactoring it. Computing code on the go means keeping a lot of things in memory, and human working memory is very limited. This means that the more computations you need to perform, the higher the chance you’ll miss something from the bigger picture, making the code review less effective. As a reviewer, if I find it hard to keep in memory everything that is going on, I ask to refactor the code. (Some examples of code that needs refactoring are complex boolean checks or long method bodies.)
    
2. Have a checklist of things to review and test before opening a pull request. For a mobile app, this can include configurations such as portrait and landscape, minimum and maximum API levels of the OS, flavors, and themes. I have a post about [my own checklist](https://medium.com/chili-labs/is-your-feature-ready-for-qa-checklist-113167f0ab00), which is very old but still relevant.
    
3. GitHub has a feature called draft pull requests. If you want an early review, perhaps on a complicated feature, but the code isn’t ready for review yet, you can mark the pull request as a draft. I think that this is a neater solution than prefixing PRs with titles such as `WIP`.
    
4. Integrate other tools for controlling code quality. For Flutter devs, these might be [Dart Code Metrics](https://dartcodemetrics.dev/), [Spec from Invertase](https://github.com/invertase/spec), or various tools from [Very Good Ventures](https://github.com/VeryGoodOpenSource).