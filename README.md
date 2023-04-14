# squash-merge-demo

Here is the simple example with reusing the branch for hotfix. Non-squash works with no conflicts, squash gives conflict in the exactly similar situation.
The same will happen with feature branch having commits from other feature branch (common situation when you don't want to wait when the changes from the previous PR will be merged).

This example shows that the only difference between squash and normal merge is that the feature branch is disconnected from the merge commit. Link to the feature branch is removed from the merge commit, that's what the "squash" really is. That's why the second squash PR having conflicts while the first one doesn't -- git can't determine the common base and cannot do a 3-point merge.

Also, because of that you cannot see which branches were really merged, and which were left with incomplete work. Quite a contrary, with merge commits you are able to see the exact topology of merges, including the fact that the same branch was merged twice.

![image](https://user-images.githubusercontent.com/469462/231919460-a100f71d-e88e-4639-bc17-5d55c3cc83fb.png)

If you don't want to see the connection from the merge commit to the feature branch, there is no point to delete it with squash merge. Just hide it. That's what I just did, look. Can you see the difference between squash/non-squash now?

![image](https://user-images.githubusercontent.com/469462/231919522-84dbc7f3-fcf0-4037-a678-53da9ad23224.png)

When it really create troubles is when you have manual conflict resolution during the merge. As squash mode deletes the link to the origin in the feature branch, this is not a true merge and git cannot remember how you merged. So, in the situation above when you do the second PR you will have to resolve the same conflicts again.
But when it becomes really screwed up is in the situation when somebody had a long-living branch with a lot of changes & conflicts, made mistake in conflicts resolution, and then squash-merged it. When you investigate what happened, you are interested which version he did use before the merge. And with squash, you don't know, because this is not a true merge commit and the link to the feature branch is deleted.

So, simple cases works, complex cases create troubles, and in fact -- squash doesn't give a thing in return. It's the same as merge commits with the link to the feature branch removed, and the latter gives nothing but troubles.

To be frank, I was being lazy and asked ChatGPT 4 to create the first example which I used in PR. ChatGPT made two mistakes.

1. It forgot that adjacent lines doesn't participate in 3-way merge (that's why the normal merge failed when you tried to reproduce it). You shall put empty lines in between.
2. It lied that 3-way merge is not applied in case of the simple squash merge. In a simple case, it still does. It doesn't only in case if squashed commits participate in merge, as in the example which I created myself (this repo).

As I told you from the beginning, it's not that easy to create such an example. I was surprised when ChatGPT 4 came up with such a simple thing, but trusted the AI being lazy. Won't make the same mistake again.

![image](https://user-images.githubusercontent.com/469462/231919785-8bf78dcb-c31a-49a5-8697-c51a787faa48.png)

Long story short, the only benefit of "squash" is that the history _appears_ linear in simplistic git clients like github front page (but it _doesn't really become_ linear). It's done at the cost of corrupting the dependency graph, increasing the rate of conflicts during complex merges, losing the authorship of the changes when other person merges the PR, and losing the information about the source branches which makes it hard to unscrew errors in manual conflict resolution.

Squash is not really a solution for anything. It's merely an unnecessary castration of the git history. Just hide it if you don't want to see it.

`git log --first-parent --graph` to see the same linear commit history as you would with squash. Just without squash.

`git cherry-pick -m 1 <merge_commit>` to cherry pick merge commit.

`git revert -m 1 <merge_commit>` to revert merge commit.

Et cetera.
