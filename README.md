# Proposal for Ideal Mobile Development Workflow Based on GitFlow
**We need to have workflow that make stable builds, on development, maintenance, and when production issue occurs.**

**Goals:** 
- **Make a stable production ready build every sprints**
- **High and critical bugs are known as early as possible**
- **Make hotfixes / patches to be production ready fast**

Stable does not mean bug free.

Stable means worry less, no critical or high priority bugs, no obvious bugs.

There are several development phase for this proposal, each one has specific characteristics. The flows are:

## Development Phase
Development phase is a phase for building new features or starting new product from scratch.
- Based on Scrum
- 2 weeks sprints
- 1 week or less **stabilization phase**
- 2 days PVT + Security Testing (need subject matters on this)

## Maintenance Mode
This is for handling non-critical bug fixes and stabilization of an existing application.
- Based on Kanban
- 1 week or according to team’s agreement 
- 2 days PVT + Security (need subject matters on this)

## Production Issue
This phase is for business impacting issues, that needs to be done immediately, by adding patches.
The timeline is according to SLA if any, or ASAP.

## Detailed Guidelines
![](README/Screen%20Shot%202018-03-20%20at%208.36.05%20PM.png)

### Development Mode

Development phase is a phase for building new features or starting new product from scratch.

Branches:

**feature/***

**bug/***

Developer create this branch on their local machine, it will be merged and pushed to develop branch after code review is approved.
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
This is for handling non-critical bug fixes and stabilization of an existing application. If there are big features or improvements, then it’s better to use development mode.

**bug/**

**Bug branch** will be merged to **release branch**, then to master and develop

Have the same **develop**, **release**, and **master** as the Development Mode.

### Hotfix Mode
Hotfix mode is for whenever there is production issue that needs to be fixed as soon as possible. The build is already up and running on production.
Pushing changes to **hotfix/*** branch will automatically make builds for **UAT** and **Production** environment.

**hotfix/**

Hotfix branch is for fixing the bug happened in production, this is branched directly from master on specific tag. After linting, unit testing, and code reviewing is done. Hotfix is merged to master branch. 

This branch, whenever pushed, should automatically run lint, unit tests, and creating build.
Then it should be reviewed by lead developer before it’s merged to master and develop

Have the same **develop** and **master** as the Development Mode.

## Rules
* **We create builds for internal consumers (QA , Internal Users, or Penetration Testers), when we want to release.**
* **Customers get production stable release only.**
* **QA tests** on **release branch** on **stabilization phase**.
* Developers should make sure their work is done according to stories and already test them before doing code review.
* “Real” Unit Tested :)
* Approver **MUST** checkout branch on PR and **see by their own eyes, that things is proper** before clicking the approve button.
* **No builds** comes from **develop branch** for **internal consumers**, develop build is only for developer. 
* CI will not create build for develop branch. Developers should be able to make builds locally.
* Make builds from release branch daily on stabilization phase.
* **Only hotfix branch can directly make builds**.
* Release branch is a branching from develop, release branch contains commits that wants to be stabilized and released, a release candidate.
* On release branch, several release candidate can be tagged. Ex: v.1.0.1-7004.
* Once QA team declares the build is stable, release is merged to master and tagged, it is also merged back to develop.
* Tagging format is like this: **v.1.15.0-7001 (v.< version >< buildNumber >)**.

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
- If the test case is done, it will be integrated with UI testing in build phase in CI/CD

### Security Team
- Penetration Testers

### Others
- Do PVT

## Pros and Cons/Challenges
### Pros:
- Stabilized each releases
- Clear visibility of codes and rollbacks if needed
- Clear phase between development and testing
- Clear guidelines

### Challenges
- need discipline to follow the guidelines
- need CI/CD that fulfill needs

### Notes
If the full Automated UI Test is stable, then the Development mode and Maintenance mode will need a revision. The builds develop by any developers can be merged to **master** right away.

## Tools
1. CircleCI for CI runner
2. Fastlane for builder
3. HockeyApp, for adhoc or internal distributions
4. Artifactory for collecting IPAs or APKs 
5. Bugsnag for error reporting
6. Slack for team notifications
7. Bitbucket or GitHub for Source Control

## CI/CD Pipelines
To actually make a build we need to make sure it is simple and easy to create one, so we just need one simple step to create a build: **pushing a branch**.  CircleCI will automatically create a VM for us so that we can run our workflow.
The branch that we need to configure to create build automatically is **release**, **hotfix**, and **master**.

Generally, the pipeline is like this:

![](README/Screen%20Shot%202018-03-21%20at%203.56.44%20PM.png)

### Checkout

We start the CI/CD pipeline by checking out the code a.k.a pull the code.

### Build Creation

In this step, we start installing dependencies to creating the build. This step consists of Install Runner Dependencies and Creating Builds (IPA or APK). 

### Build Distribution

We distribute the build to QA and Internal Users and also post the symbolication file to error tracking service, we use Bugsnag for this. Build distribution consists of:

- Uploading Symbolication to Bugsnag
- Distributing Builds: SIT, UAT, Production AdHoc (for Internal Users) to HockeyApp and Production AppStore / PlayStore to Artifactory
- Sending Notifications

### Build Testing

QA process happens in this step. QA can test their builds after receiving notifications, it can be from email sent by Distribution service , like HockeyApp and from Slack.

### Delivery Flows

![](README/Screen%20Shot%202018-03-21%20at%206.37.47%20PM.png)

![](README/Screen%20Shot%202018-03-21%20at%207.05.59%20PM.png)

![](README/Screen%20Shot%202018-03-21%20at%207.06.22%20PM.png)

**Notes**

- On Native build Unit Tests is run on `Fastlane`.
- On React Native, Running Unit Tests is run after Installing Runner Dependencies as `config.yml`’s run.
- Both are executed on **Create Build** step.

### Delivery Implementation
![](README/Screen%20Shot%202018-03-22%20at%201.18.35%20PM.png)

Since we use CircleCI, it is configured in `config.yml` file in `.circleci` folder on your iOS or Android project. **Checkout** and **Install Runner Dependencies** are written as a **run** step on `config.yml` while the implementation details of **Build Creation** and **Build Distribution** steps is written in `Fastfile` . 

In other words:

- Checkout, Install Runner Dependencies, `fastlane` command line execution  —> CircleCI’s config.yml’s `run`
- Build Creation and Distribution details —> Fastlane’s `Fastfile`
- Execution is all done in CircleCI’s VM

Here’s the example of the CircleCI configuration files:

**config.yml**

```yaml
version: 2
jobs:
  adhoc:
    # Specify the Xcode version to use
    macos:
      xcode: "9.2.0"

    environment:
      LC_ALL: en_US.UTF-8
      LANG: en_US.UTF-8
    shell: /bin/bash --login -eo pipefail
    steps:
      - checkout

      # Fetch cocoapods spec from CircleCI repo
      # to speed up spec download
      - run:
          name: Fetch CocoaPods Specs
          command: |
            curl https://cocoapods-specs.circleci.com/fetch-cocoapods-repo-from-s3.sh | bash -s c

      - run:
          name: Set Ruby Version
          command: echo "ruby-2.3" > ~/.ruby-version

      - run:
          name: Install gems using bundler
          command: bundle install

      - run:
          name: Make adhoc build for SIT
          command: bundle exec fastlane adhoc --env sit

      - run:
          name: Make adhoc build for UAT
          command: bundle exec fastlane adhoc --env uat

      - run:
          name: Make adhoc build for PROD
          command: bundle exec fastlane adhoc --env prod

      # Store logs
      - store_artifacts:
          path: ~/Library/Logs/scan
          destination: scan-logs
```

and here’s the example of the **Fastfile**  `adhoc` lane:

```ruby
desc "Make adhoc build, for SIT, UAT, PROD using AdHoc profile"
  lane :adhoc do
    PLIST_PATH = "#{ENV["PROJECT_NAME"]}/Info.plist"
    build_version = get_info_plist_value(path: PLIST_PATH, key: "CFBundleShortVersionString")
    build_number  = get_info_plist_value(path: PLIST_PATH, key: "CFBundleVersion")

    ipa_name = "#{ENV["APP_NAME"]}-#{build_version}(#{build_number})-#{ENV["SCHEME"]}"

    build_ios_app(
      scheme: ENV["SCHEME"],
      export_method: "ad-hoc",
      clean: true,
      output_directory: ENV["BUILD_DIRECTORY"],
      output_name: ipa_name,
      xcargs: "PROVISIONING_PROFILE_SPECIFIER='#{ENV["PROV_PROFILE_NAME"]}'"
    )
	
	  Dir.chdir("../..") do
      # upload sourcemaps to Bugsnag
      Helper.backticks("./bundle_sourcemaps_ios_release.sh")
    end

    hockey(
      api_token: ENV["HOCKEY_TOKEN"],
      ipa: "#{ENV["BUILD_DIRECTORY"]}/#{ipa_name}.ipa",
      notes: "Some notes",
      notify: "2", # notify all testers
      status: "2", 
      notes_type: "0"
    )
  end
```

## Contributions
mamaz @mamaz

## Further Development
- If the automated UI test is working, then the **true** Continuous Delivery will be real. Developer will only branch from master and merge back to it, it should automatically create a production ready build.
- App Store / Play Store flow can be automated to create snapshots or upload automatically if we want.

## License
MIT License

Copyright (c) 2018 mamaz

