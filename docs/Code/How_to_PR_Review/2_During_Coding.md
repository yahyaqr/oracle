## 1. Branch naming strategy

So you have opened your task manager, selected a ticket, and moved it to “Progress”. What’s next?

Create a branch in which you will be working. And call that branch… what?

The branch name is your first opportunity to give your task context. In case your reviewer decides to check the code out locally, they will need to find that branch among many others.

There are several good approaches you can choose from, depending on how you manage work.

### Approach 1

If you’re using a task management tool that gives your tasks a prefix or number (e.g., Jira), then you can use that prefix too — especially to set up some hooks, such as to move the ticket to “Code review” when a PR is opened or “To test” when the PR is merged. For example, if the task name is `ABC-57: Add list with purchases`, then a good name for a branch would be `ABC-57_purchases_list`.

But why not just `ABC-57`?

Well, let’s compare these two options.

|**List of branches A:**|**List of branches B:**|
|---|---|
|`ABC-57`|`ABC-57_purchases_list`|
|`ABC-64`|`ABC-64_fix_cart_npe`|
|`ABC-73`|`ABC-73_migrate_2.8`|

In which of these lists will you find the required branch faster? Using a naming convention that includes a description also helps when switching your own branches regularly, as you don’t need to remember or look up the ticket number.

### Approach 2

Use the same `prefix` + `description` approach, but instead of prefixing the name with the ticket number, prefix it with what it is: `feature`, `bug_fix`, `refactor`, etc. It’s best to use this approach only if there are no ticket names available. Here are a couple of examples:

`feature_purchases_list` or `bug_fix_cart_npe`

### Approach 3

Any other branch naming strategy is fine, as long as it is consistent and has a logical structure. Randomly naming branches can eventually lead to chaos. Not only is it harder to navigate between them but also more difficult to maintain. Which branches are stale? Can you delete them? Is there unmerged work? Is it still relevant?

## 2. Commit history

One code review strategy you can use is per-commit review — but only if the commit history makes it possible. [Here](https://cbea.ms/git-commit/) is a really great and detailed article about how to correctly name your commits.

In short, here are some guidelines:

1. Keep commit messages concise. It’s best to keep them around 50 characters, and 72 is the hard limit. Anything more than 72 is wrapped in GitHub.
2. Use an imperative approach for your commit messages. Phrase it as what will happen when you apply this commit, e.g., `Update headline text color` or `Remove unused imports`.
3. This naming strategy is tightly coupled with an even more important notion for managing history: atomic commits. You can’t give a concise name to a commit unless it introduces a single logical change, i.e., it is atomic.
4. If you have ticket names, as mentioned in the branch naming strategies, prefix every commit with the name of the ticket. This can make it easier to navigate the commit history by task and allow integrations with project management tools. For example, you can integrate Jira with GitHub so that the ticket descriptions also keep track of the commit history.

By just reading the commit history of the pull request, the reviewer can already gain some understanding of what they will be reviewing, even before seeing a single line of code.

If you want to go a step further, I’ve recently encountered an interesting approach to commits. The context of this approach is broader than just commit naming. You can read all about it [here](https://hackernoon.com/the-meticulous-approach-to-coding-8g9353n).

But the general idea is to write a commit name _before_ you start coding. This will help you stay focused on one step at a time, provide more clarity on what you need to do, and implicitly make your commits atomic. I think this is a very interesting take and plan to try it myself.