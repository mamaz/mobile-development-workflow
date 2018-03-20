# Proposal for Ideal Mobile Development Workflow Based on GitFlow
**We need to have workflow that make stable builds, on development, maintenance, and when production issue occurs.**

**Goals:** 
- **Make a stable production ready build every sprints**
- **Production ready deployment for hotfixes**

Stable does not mean bug free.
Stable means worry less, no critical or high priority bugs, no obvious bugs.

## Development Phase is based on Scrum
2 weeks sprints
1 week or less **stabilization phase**
2 days PVT + Security Testing (need subject matters on this)

## Maintenance Mode is based on Kanban.
This is for handling non-critical bug fixes, non high impact stories.
1 week or according to team’s agreement 
2 days PVT + Security (need subject matters on this)

## Production Issue
This phase is for business impacting issues, that needs to be done immediately, by adding patches.
According to SLA if any, or ASAP.

## Rules
* **We create builds for consumers (QA or Pen Testers), when we want to release.**
* **QA tests** on **release branch** on **stabilization phase**.
* Developers should make sure it is done according to stories and already test them before doing code review.
* Approver **MUST** checkout branch on PR and **see by their own eyes, things is proper** before click the approve button.
* No builds comes from develop branch for other consumers, develop build is only for developer. CI will not create build for develop branch. Developers should be able to make builds locally.
* Make builds from release branch (stabilization) daily.
* Only hot fix branch can directly make builds.
* Release branch is a branching from develop, release branch contains commits that wants to be released and wants to be stabilized.
* On release branch, several release candidate can be tagged. Ex: v.1.0.1-RC1.
* Once QA team declares the build is stable, release is merged to master and tagged, it is also merged back to develop.
* Tagging format is like this: v.1.15.0-7001 (v.<version><buildNumber>).

## Detailed Guidelines
![](proposal_based_gitflow/Screen%20Shot%202018-03-20%20at%208.36.05%20PM.png)

### Development Mode
Development mode is whenever we build a new features with full sprint cycles on it.

**feature/***
**bug/***
Developer create this branch on their local machine, will be merged and pushed to develop branch after code review is approved.
This branch should be removed from git after it is merged to develop

**develop**
Main branch for all developers’ work, this is still not stable, not yet checked by QA. All commits merged to this branch is tested by developers to be **working, not breaking, linted, passed unit tests, and code reviewed** by lead developer.
Additionally, developer can add a flag to features merged on this branch, when the features are not gonna be released yet, because of let’s say, a business need. 
Develop branch should only visible to developers only. No builds from this branch goes to outside world.

**release**
This branch is for preparation to release a feature. Whenever the team decides to have a release, developer lead merges develop branch on a specific commit hash to release branch. 
Then all bug fixes and stabilization is merged to this branch.
The build is automatically done **daily**, whenever it has changes.
QA get build from this branch only. 
The QA process is done in stabilization phase.
After the build is declared **stable**, then developer lead merge this to master and develop branch.

**master**
This branch contains stable build. It is tagged by version and build number. The tag functions should make developers easy to pinpoint which commit to build in case there’s a rollback.
PVT + Security 

### Maintenance Mode
This is for handling non-critical bug fixes, non high impact stories.
If there are any big features or improvement, then it’s better to use development mode.
**Bug branch** will be merged to **release branch**, then to master and develop

**bug/***
the same as above, the difference is only it is for bug fixing.

**release**
the same as above

**develop**
the same as above.

**master**
the same as above.

### Hotfix Mode
Hotfix mode is for whenever there is production issue that needs to be fixed as soon as possible. The build is already up and running on production.
Pushing changes to `hotfix/*` branch will automatically make builds for **UAT** and **Production** environment.

**hotfix/***
Hotfix branch is for fixing the bug happened in production, this is branched directly from master on specific tag.
After linting, unit testing, and code reviewing is done. 
Hotfix is merged to master branch.
This branch, whenever pushed, should automatically run lint, unit tests, and creating build.
Then it should be reviewed by lead developer before it’s merged to master and develop

**develop**
the same as above

**master**
the same as above

## Roles
### Developer
- Design and develop awesome features.
- Code Reviews.

### Lead Developer
- To lead the development team
- Design high level architecture within the scope of the team
- With the overall teams decides tools and infrastructure including CI/CD pipelines
- Account management (Jira, Bitbucket, etc)
- Code Reviews
- Merging release to develop and master
- Maintain discipline: Jira status, etc

### QA
- Stable build assurance

### QE
- Design and writes test cases to be automated

### Security Analyst
- Pentest

### Others
- Do PVT

## Pros and Cons/Challenges
### Pros:
- Stabilized each releases
- Clear visibility of code and rollbacks
- Clear phase between development and testing
- Clear guidelines

### Challenges:
- need discipline to follow the guidelines
- need CI/CD that fulfill needs
- versioning needs to be automated

### Notes
If the full Automated UI Test is stable, then the Development mode and Maintenance mode will need a revision.

## Tools:
1. CircleCI for CI runner
2. Fastlane for builder
3. HockeyApp, for adhoc or internal distribution
4. Artifactory for collecting IPAs or APKs 
5. Bugsnag for error reporting
6. Slack for team notifications
7. Bitbucket or GitHub for Source Control

## CI/CD Pipelines
![](proposal_based_gitflow/Screen%20Shot%202018-03-20%20at%208.50.14%20PM.png)

## Contributions
mamaz @mamaz

