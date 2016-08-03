# Development Process

We develop and deploy on a 2-3 week schedule. Before a deploy, a new release is
tagged and cut, deployment ticket filed, and QA verified. On occasion a release
will get a full suite of load-tests as well.

Some of the ancillary Push projects have no set release schedule and are
released as needed.

## Product Planning

Product-level feature planning is done using OKR's, similar to
[Google's OKR process][OKR]. Finalized OKR's per quarter result in feature
cards being created in Aha, where they're then prioritized for work to begin. A
single OKR may result in multiple feature cards depending on scope, each card
is tagged for its corresponding OKR #.

* [Aha Feature Cards](https://mozilla.aha.io/products/PUSHSVC/feature_cards)

## Code Development

Each Aha card being actively developed has a milestone created in the main
[Mozilla Push-Service Repository][MPSM], which is then propagated out to all
Push-related GitHub repositories.

### Milestones & Waffleboard

Issues are created in their appropriate repositories and associated with a
milestone. Ownership and triage of issues is done via a single Waffleboard that
aggregates issue tracking across all Push-related repos.

* [Push Waffleboard](https://waffle.io/mozilla-services/push-service)

All issues must have a milestone attached before assignment.

### Labels

Issues are assigned a priority based on the release the issue is targeted at.

* P1 - Next release
* P2 - Release after next
* P3 - No immediate release targeted
* P5 - No work planned yet

P2 issues are promoted to P1 after a release, while P3's are triaged to
determine if they should become P2's at the first standup after a release.

The priority tags are applied as Github labels. Other labels will also be
applied to categorize the issue appropriately.

Issues being worked on are moved to the **In Progress** column and assigned to
the person working on the issue.

### Standups

A short stand-up meeting is held every Monday at 10:20 AM Pacific time. This
meeting covers bug triage and a summary of work completed in the past week and
planned work for the next week.

### Github Workflow

(Based heavily on Servo's Workflow)

You will need to [fork](https://help.github.com/articles/fork-a-repo/) the 
appropriate repository in order to be able to publish your changes. Push Team
members may create a branch in the repo which should reference the issue being
worked on. In these instructions `<mozilla-services>` is the name of the remote
pointing to the remote at the appropriate repo and `<fork>` is the remote
pointing at your fork of the repository. 

1. Fetch the latest code and create a local branch:

    `$ git fetch <mozilla-services>`

    `$ git checkout -b <local_branch> <mozilla-services>/master`
 
    Name the branch appropriately for the fix/feat/docs commit you're working on.
    Ideally a Pull Request will have a single commit to address the issue.
    <br /><br />

2. Code/hack/do stuff then commit:

    `$ git commit -a `

    Make sure to phrase your commit message appropriately per the Contributing
    documentation for the repository. Most Push-related repositories use an
    [Angular-based commit style](https://github.com/mozilla-services/autopush/blob/master/CONTRIBUTING.md).
  
    Before committing and pushing code, remember to run the appropriate tests
    locally to avoid having our CI systems reject it. PR's that don't pass the
    automated tests won't be reviewed until they pass.
    <br /><br />

3. Push local branch to your cloned repository: 

    `$ git push --set-upstream <fork> <local_branch> `
 
     (`git push -u <fork> <local_branch>[:remote_name]` should work if you want to
     publish your changes to a branch with a different name than `[local_branch]`.)
     <br /><br />

4. Create a [PR in GitHub](https://help.github.com/articles/using-pull-requests/). 

    If you know who should code review this PR, you can write `r? @username`
    in the text of the PR and they will automatically be assigned to it.
    If not, don't worry: a reviewer will be randomly selected and notified.
    <br /><br />

5. Wait for reviewers' feedback - if something needs to be fixed, either amend
   the existing commits if the changes are minor, or fix it in a new commit on 
   the same branch, optionally using `--fixup`:

    `$ git commit --fixup=<sha1_of_relevant_commit_on_branch>`

    Alternatively, add the following to your `.gitconfig` and simply use `git fixup`:

    ```
    [alias]
    	fixup = !sh -c 'git commit -m \"fixup! $(git log -1 --format='\\''%s'\\'' $@ | sed \"s/fixup\\! //\")\"' -
    	ri = rebase --interactive --autosquash
    ```
    <br /><br />

6. Use `git push` to update the Pull Request. Repeat steps 5-6 until the review
   is accepted. If existing commits were amended, a force push will be necessary
   (see step 8).
   <br /><br />

7. When you know there is a substantive change on master that affects your
   patch, update `<mozilla>` and rebase your local branch to make sure your
   patch still applies correctly: 

    `$ git fetch <mozilla-services>`

    `$ git rebase <mozilla-services>/master`

    You may have to fix merge conflicts on the way.
    <br /><br />

8. Force-push your changes: 

    `$ git push -f <fork> <local_branch>`
    <br /><br />

9. Once your patch is accepted and based on a recent master, squash the commits
   together for a cleaner history (if requested):

    `$ git rebase -i --autosquash <mozilla-services>/master`
    <br /><br />

10. Force push the squashed commits to github (see step 8).
    <br /><br />

11. When the reviewer thinks the code is ready, they will leave a comment
    saying "r+", meaning "review granted."  Then our bot will
    automatically test and merge your PR.  Congratulations!


[MPSM]: https://github.com/mozilla-services/push-service/milestones
[OKR]: https://library.gv.com/how-google-sets-goals-okrs-a1f69b0b72c7#.4540y9hzl