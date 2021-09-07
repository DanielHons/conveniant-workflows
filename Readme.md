# CI-Workflow-Set
## Basics
The "main" branch is considered to be master. It does not matter how the repositories main branch in git is configured, release versions will be determined by the diff to master, and after a successful release it will merged into master.

## Wokflows
### Stage branch for release
Trigger: manual

When you want to stage the changes from some branch for the next release, run this pipeline on that branch. This will
- [x] Calculate what the SemVer of a release containing that branch would look like (e.g. v3.10.3 -> v3.11.0) based on the commit messages. See [conventional commits](https://www.conventionalcommits.org/en/v1.0.0/)
- [x] Create a release branch containing the upcoming tag, like release/v3.11.0 from that source branch
- [x] If such a release branch already exists, the source branch will be merged onto it (this will trigger a build of the release candidate


### Build Pre-Release
Trigger: Push to release/v*

Any push to a release branch will create a release candidate and tag the commit with a tag matching the branch name like: release/v3.11.0 --> v3.11.0-rc.0
where the last digit "0" will be replaced with an incrementing number.

---

💡 This workflow can be extended to build and push a docker image and/or deploy it to some staging environment

---

### Release
Trigger: manual

Accepting a release candidate is done by applying this workflow to a branch, with HEAD pointing to the accepted release candidate tag.
So if the source branch (on which you run this workflow) is named `release/v3.11.0` with HEAD pointing to `v3.11.0-rc.8`, then it will
- [x] create a release with tag `v3.11.0` from the same commit as `v3.11.0-rc.8`
- [x] Provide a changelog for the release based on the commits between the latest non-rc-tags
- [ ] (Add as you wish) ReTag the docker image 3.11.0-rc.8 -> 3.11.0
- [x] Merge the release branch into master
- [x] Create a PR from master to develop if any files have been changed


# Use cases

### "Usual" development

- create a feature branch for each feature
- squash them into develop, having a [conventional commit message](https://www.conventionalcommits.org/en/v1.0.0/)
- when ready for pre-release, run `Stage branch for release` on `develop`
- repeat as ofter as you like
- you can add commits to the generated release branch as well, without touching develop  
- For accepting a pending release, run `Release` on the release branch created from pre-release


### Hotfixes
- create a hotfix branch from develop
- run `Stage branch for release` on your hotfix branch
- For accepting a pending hotfix, run `Release` on the release branch created from pre-release
