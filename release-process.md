# Software Release Process

This document will layout the rules for commits, commit history and how to perform a release.

## Commits and Commit History

### Setup

Before starting to work on the release:

1. Begin by creating a new branch from `master` with the following syntax: `release/{{ release_number / version_number }}`, e.g. `git checkout -b release/0.5.0`

**RULE**: Never commit anything to the `master` branch that is not coming from a Pull Request - always use a branch to work in.

**NOTE**: If your ticket is not specific to a project (e.g. bumping a common library or changing CI/CD) it is likely that some other project already has same ticket - please keep ticket names consistent in order to make them easy to look up

**NOTE**: Not all tickets are 5K LoC changes - in the commit history we try to reflect whole project's evolution, hence its important to have tickets like `Bump sbt-bintray to 0.6.0`, which are effectively one-line changes

2. Create a new issue milestone, if one does not exist, for your release, e.g. `Release 0.5.0`

3. Create and assign issues to this milestone for any and all changes that need to happen in this release

**RULE**: Use an impretive case for ticket name (e.g. `Fix a race condition when multiple emitters used` - good, `Race condition bug when multiple emitters used` - bad)

### Commit history

Each change that you make must be related to an issue in the bugtracker.
The idea being that 1 change in the commit history is related to 1 issue and becomes 1 line in the CHANGELOG of the project.

For example if you had an issue to update the README of a project:

```
Issue name: "Update README with new guide links"
Issue number: 12
```

You would make these changes in the branch you have specified above and would then commit this change with the commit message `Update README with new guide links (closes #12)`.

This way of committing the change means that when you merge changes to master the issue is automatically closed for you, you have a very clean commit history and the issue has a reference to the commit for easy searching of the commit that actioned the change.

**RULE**: Always maintain the 1:1:1 relationship between commit log, changelog file and issue tracker.

**NOTE**: Try to have as many details in ticket discussion as possible (but expose any details that can be internal!) - it makes code archeology a lot less painful process.

### Outside contributions

In case the release contains any community pull requests - they need to be treated in a special way.

1. The contributor need to sign a [Contributor License Agreement][cla]
2. We always `cherry-pick` commits to the release branch in order to preserve its initial author
3. A commit title still needs to refer to its issue in a bug tracker (or at least to the PR number). The commit title can be amended by a project maintainer

### Pre-release

Once you have finished all of the issues in your milestone and they have all been committed to the branch in question there are a few housekeeping items left to do:

1. Update the CHANGELOG in the project (find the CHANGELOG file in your local repo, making sure you are on the branch that you use for the actual changes you are making). The format is:

```
Release | Version {{ version|release number }} ({{ date_of_release }})
-------------------------------------------------------------------------------------------
{{ issue_name }} ({{ issue_number }})
```

For a full example:

https://github.com/snowplow/snowplow-scala-tracker/blob/master/CHANGELOG

2. Update any references to the release | version number to the current release, e.g. most common ones:

* Badges in the README
* Hardcoded versions in code

**NOTE**: in many cases, it is useful to have a dedicated `Release process` checklist somewhere in project's wiki, which can contain all steps (including QA, pre-release, release and post-release) that engineer need to go through before releasing as it can be highly individual. 

3. Check that the copyright years are correct; if they are out of date - update them (this would go to a separate ticket - `Extend copyright notice to {{ year }}`
**NOTE**: If you are uncertain about any of these steps look at the commit history for `Prepared for release` commits for reference.

4. Last commit is always `Prepare to release`, containing those small version bumps.

5. After your project is ready for public QA and you need to release a pre-release asset - you bump the asset's version to `rc`/`M` (see [Version conventions][versioning]), commit it, tag and push:

```
git commit -m "Bump to M1"
git tag -a 0.5.0-M1 -m "First milestone"
git push --tags
```

**NOTE**: our deployment processes are triggered via git tags

6. CI/CD should publish the asset

7. If final QA didn't reveal any blockers - the `Bump to Mx` commit should be deleted, otherwise repeat 5th and 6th steps until successful outcome, each time incrementing the pre-release number and squashing fixes into corresponding original tickets

**NOTE**: Most projects also require at least one PR review and sign-off from a fellow engineering team member

### Release

1. Remove all temporary commits (such as `Bump to Mx`). `Prepare to release` commit should be the `HEAD`
2. Merge the release branch to `master`: `git checkout master && git merge release/0.5.0` (we used to use `--no-ff` before, but getting away from this practice)
3. Add a final tag (e.g. `0.5.0`) on top of the `master` branch
4. Push tag-only: `git push --tags`
5. Only after you're sure that CI/CD was successful and asset is published, you can push the `master` branch: `git push origin master`. This will automatically close all tickets in the milestone. By pushing `master` branch last you're making sure that you won't have to force-push to `master` late because of broken CI/CD
6. Push the release branch. It should automatically close the associated PR and mark it as "merged".

### Post-release

1. Close the GH milestone
2. Delete the release branch
3. Add the GH release page with list of all tickets (grouped by sensible categories, e.g. `New features`, `Bug fixes`, `Under the hood` etc)
4. Update all documentation (links should be in `Release process` checklist)
5. Merge the blog post on [website repository][website-repo]
6. [Discourse][discourse] announcement (don't forget to use `New releases` category)
7. Update release page with links to the blog post and discourse announcement
8. Follow the internal notifications process: announcement in `#releases` Slack channel, notify affected teams etc


[versioning]: https://github.com/snowplow-incubator/engineering-resources/blob/master/style-guide.md

[discourse]: http://discourse.snowplowanalytics.com/
[blog-repo]: https://github.com/snowplow/snowplowanalytics.com
[cla]: https://github.com/snowplow/snowplow/wiki/CLA
