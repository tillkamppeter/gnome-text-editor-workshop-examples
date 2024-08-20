# Gnome Text Editor

*[![gnome-text-editor](https://snapcraft.io/gnome-text-editor/badge.svg)](https://snapcraft.io/gnome-text-editor)
[![gnome-text-editor](https://snapcraft.io/gnome-text-editor/trending.svg?name=0)](https://snapcraft.io/gnome-text-editor)*

**Repository to provide workshop examples**

This repository contains 4 examples for creating a Snap package
("snapping") of the GNOME Text Editor. Each example is nothing more
than a single `snapcraft.yaml` file and each example produces a
working Snap of the GNOME Text Editor. The example `snapcraft.yaml` files
are in separate directories. Change into the desired directory and run the command
```
snapcraft pack -v --debug
```

Examples:

- `snap/`: The original `snapcraft.yaml` for the official GNOME Text
  Editor Snap as it is available in the Snap Store. The build process
  downloads the upstream source code and compiles it, it extracts
  version number and package description from the upstream source
  code. Advantages are that one can manipulate/patch the source code
  if needed and one has always the current version of the
  software. The [original
  repository](https://github.com/ubuntu/gnome-text-editor) has Snap
  automation so that it automatically updates itself if a new upstream
  version gets released. So use the original repo whenever possible,
  this `snapcraft.yaml` is more for comparing the different methods.

- `local-deb/`: Download the binary Debian package of
  gnome-text-editor for Ubuntu 24.04 LTS (Noble Numbat) into this
  directory and then run `snapcraft` and the Debian package will get
  converted into a Snap. This is an example how one could snap
  proprietary (closed-source) software which comes in a Debian
  package, to allow installing it in non-Debian-based distros or in
  all-Snap immutable distros, like Ubuntu Core. Note that you can only
  build a Snap for the architecture for which the Debian package is.
  This is mainly intended for system administrators to solve problems
  locally, not for the Snap Store.

- `repo-deb/`: The build process downloads the binary Debian package
  of GNOME Text Editor from the Ubuntu repositories for the
  architecture and the Ubuntu version (base Snap, here `core24`) under
  which the build process is running. So this method could be used for
  publishing a Snap in the Snap Store. Now one wonders why not simply
  add `gnome-text-editor` to the `stage-packages:` instead of having
  `overide-pull:` and `override-build:`. By this way "manually"
  downloading and unpacking the Debian package we do not pull in its
  dependencies. And these we do not need as by using the "gnome"
  snapcraft extension we make the installation of this Snap
  auto-install the gnome-46-2404 content-provider Snap which contains
  all GNOME libraries.

- `stage-packages/`: Here we simply get the Debian package of the
  GNOME Text Editor by adding it to the `stage-packages:` (Debian
  packages we depend on) and do not specify any source or
  `override-pull:` to get hold on the GNOME Text Editor itself. This
  makes a super-simple `snapcraft.yaml`, but requesting a package
  under `stage-packages:` also installs all the dependencies of this
  package, meaning all the GNOME libraries in our case, which are
  already shared between Snaps via the the gnome-46-2404
  content-provider Snap. We could clean up the mess by a part running
  after the `gnome-text-editor:` part, as it is done in the
  [gnome-calculator](https://github.com/ubuntu/gnome-calculator) Snap
  ([`cleanup:` part](https://github.com/ubuntu/gnome-calculator/blob/stable/snapcraft.yaml#L82))
  ...

**Original `README.md` text below**

Below you will find some instructions to help you contribute to this snap. The general workflow will be to submit PRs from your fork onto the "stable" branch. Once the pull request has been *submitted*, there is a GitHub action that will automatically launch a build of the snap that you can use for testing. And then once the PR has been *merged* the GitHub action will launch a build on launchpad that will be automatically uploaded to the snap store, in the candidate channel, for more extensive testing.

## How to contribute to this snap

### Initial setup
Here is the workflow for submitting a change to the stable branch, and getting it published in the snap store in the candidate channel. If this is your first time contributing to this snap, then there are a couple of things you need to do as a one-time setup to get ready to submit your first pull request:

1. [Fork the repository](https://docs.github.com/en/github/getting-started-with-github/fork-a-repo) into your own GitHub namespace.
2. [Clone your fork](https://git-scm.com/book/en/v2/Git-Basics-Getting-a-Git-Repository).
3. In addition to your fork, you need to also add this remote repository. To make things a bit more intuitive, let's rename your fork's remote too (since "origin" is hardly descriptive):

```
git remote rename origin myfork
git remote add ubuntu https://github.com/ubuntu/gnome-text-editor.git
git fetch --all
```

### Submitting changes in a pr

Once you're all setup for contributing, keep in mind that you want the git information to be all up-to-date. So if you haven't "fetched" all changes in a while, start with that:

```
git fetch --all -p
```

Now that your git metadata has been updated you are ready to create a bugfix branch, make your changes, and open a PR.

1. All PRs should go to the stable branch so create your branch as a copy of the stable branch:

```
git checkout -b my-bugfix-branch ubuntu/stable
```
Note that some projects may have a different main branch (for example, many projects still use 'master' instead of 'stable'). If so, just besure to replace the branch name from 'stable' to the correct branch name in the above command.

If you're unsure which branch to submit PRs agains, it is suggested to open the PR against whatever *default* branch gets checked out when you clone the repo. And if another destination for the changes is determined later, you can always update the PR.

2. Make your desired changes and push them to your fork:

```
git push myfork my-bugfix-branch
```

Once this branch has been pushed to your fork, you should update the local branch tracking so it tracks the branch pushed to your fork:

```
git branch -u myfork/my-bugfix-branch
```

3. When you feel they're ready for submitting to the main repository (stable branch), [open up a pull request](https://docs.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests) from your `my-bugfix-branch` to the `ubuntu/stable` branch.
4. Once you've opened the PR, it will automatically trigger the build-test action that will launch a build of the snap. You can watch the progress of the snap build from your PR (Show all checks -> Details). Once the snap build has completed, you can find the built snap (to test with) under "Artifacts".
4. Someone from the team will review the open PR and either merge it or start a discussion with you with additional changes or clarification needed.
5. Once the PR has been merged into the stable branch, then on the next git mirror sync (every 4 hours), launchpad will trigger [a build of the snap that gets published](https://launchpad.net/~desktop-snappers/gnome-text-editor/+snap/gnome-text-editor) to the [snap store](https://snapcraft.io/gnome-text-editor) into the *candidate* channel. After sufficient testing of the snap from the candidate channel, then the reviewer (a Collaborator of the snap in the store) will promote the snap to the stable branch in the snap store.
