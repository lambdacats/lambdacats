# Lambdacats Administration

We use [GitHub Pages] to serve the [Lambdacats] website. This document describes
how the website was created and how it is updated.

[GitHub Pages]: https://pages.github.com/
[Lambdacats]: https://lambdacats.github.io/

## Background

We use [Zola] to generate the webiste from [Markdown] files containing content
and [Tera] files containing templates for generating HTML.

[Zola]: https://www.getzola.org/
[Markdown]: https://en.wikipedia.org/wiki/Markdown
[Tera]: https://tera.netlify.com/

### Directory Structure

This is a description of the files and directories in the repository.

#### Documentation

* `admin.md`: the current document

* [`readme.md`]: introduces the project and serves as a starting point for those
  who might be interested in contributing

* [`license.md`]: contains the copyright and license for all files in the
  repository

[`readme.md`]: ./readme.md
[`license.md`]: ./license.md

#### Sources

* [`config.toml`]: Zola configuration file in [TOML] format. See the
  [docs][config-docs].

[`config.toml`]: ./config.toml
[TOML]: https://en.wikipedia.org/wiki/TOML
[config-docs]: https://www.getzola.org/documentation/getting-started/configuration/

* [`content/`]: Markdown content files in a directory hierarchy. See the
  [docs][content-docs].

[`content/`]: ./content/
[content-docs]: https://www.getzola.org/documentation/content/overview/

* [`sass/`]: [SASS] files that will be compiled into CSS and put at the top
  level of the output directory

[`sass/`]: ./sass/
[SASS]: https://sass-lang.com/

* [`static/`]: files that will be copied unmodified to the top level of the
  output directory

[`static/`]: ./static/

* [`templates/`]: Tera template files. See the [docs][templates-docs].

[`templates/`]: ./templates/
[templates-docs]: https://www.getzola.org/documentation/templates/overview/

#### Scripts

* [`build`]: builds the website on top of the [repository] and indicates if
  there are changes to deploy

[`build`]: ./build
[repository]: https://github.com/lambdacats/lambdacats.github.io

* [`inspect`]: shows the changes made by `build` to the local website files

[`inspect`]: ./inspect

* [`deploy`]: commits changes made by `build` locally and pushes those commits
  to the remote repository

[`deploy`]: ./deploy

#### Other

The command `zola build` (without flags) outputs generated files into the
`public/` directory, and we use the `tmp/` directory as described below. These
directories have been added to the `.gitignore` file.

## Creating the Initial Website

We use an [Organization Pages site] with a repository at
<https://github.com/lambdacats/lambdacats.github.io> to store the website files
served from <https://lambdacats.github.io/>. This means the files must be found
at the top level of the `master` branch.

[Organization Pages site]: https://help.github.com/en/articles/user-organization-and-project-pages

As mentioned above, Zola uses a `public/` directory to store its generated
files. We might consider using that directory as the local repository for the
website; however `zola build` deletes the directory before populating it.

To support the `zola build` process, we create a local checkout of
`lambdacats.github.io` and move its `.git` directory from and to `public/`. We
use `tmp` to store the `.git` directory temporarily.

First, create the GitHub repository for `lambdacats.github.io`.

Next, build the website:

```
~/lambdacats $ zola build
```

Then, create an initial repository in `public/`, where `zola build` put the
website:

```
~/lambdacats $ git init public
```

Add all of the generated files to the branch, commit, and push:

```
~/lambdacats $ cd public
~/lambdacats/public $ git add .
~/lambdacats/public $ git commit -m Initial
~/lambdacats/public $ git remote add origin git@github.com:lambdacats/lambdacats.github.io.git
~/lambdacats/public $ git push -u origin master
```

Visit <https://lambdacats.github.io/> to see the new website.

## Updating the Website

To alleviate the maintenance burden, there are scripts for building the website,
inspecting local changes, and deploying it to GitHub. While the entire process
could be combined into one script, it is separated into three so that we can
inspect changes locally before deploying them.

These scripts use Bash, can be run from anywhere in the repository, and exit on
the first error.

### Building

To build the website, run `./build`. This script:

1. ensures that we have a local clone of the [repository] in `public/`,

2. runs `zola build`, and

3. handles the management of `.git/` before and after `zola build`.

### Inspecting

To show the local changes made after running `build`, run `./inspect`. This
script calls `git diff` on `public/`.

### Deploying

To deploy changes to the website, run `./deploy`. This script:

1. updates the local repository index to match the current state of the
   directory as a whole (i.e. adds modifications, adds new files, and removes
   deleted files),

2. commits the index, and

3. pushes new commits to the remote repository.

This script exits with an error if `public/` does not exist.
