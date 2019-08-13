# Lambdacats Administration

I use [GitHub Pages] to serve the [Lambdacats] website. This document describes
how the website was created and how it is updated.

[GitHub Pages]: https://pages.github.com/
[Lambdacats]: https://lambdacats.github.io/

## Background

I use [Zola] to generate the webiste from [Markdown] files containing content
and [Tera] files containing templates for generating HTML.

[Zola]: https://www.getzola.org/
[Markdown]: https://en.wikipedia.org/wiki/Markdown
[Tera]: https://tera.netlify.com/

### Directory Structure

This is a description of the files and directories in the repository.

#### Documentation

* `license.md`: contains the copyright and license for all files in the
  repository

* `readme.md`: introduces the project and serves as a starting point for those
  who might be interested in contributing

* `build.md`: the current document

#### Sources

* `config.toml`: Zola configuration file in [TOML] format. See the [docs].

[TOML]: https://en.wikipedia.org/wiki/TOML
[docs]: https://www.getzola.org/documentation/getting-started/configuration/

* `content/`: Markdown content files in a directory hierarchy. See the [docs].

[docs]: https://www.getzola.org/documentation/content/overview/

* `sass/`: [SASS] files that will be compiled into CSS and put at the top level
  of the output directory

[SASS]: https://sass-lang.com/

* `static/`: files that will be copied unmodified to the top level of the output
  directory

* `templates/`: Tera template files. See the [docs].

[docs]: https://www.getzola.org/documentation/templates/overview/

#### Scripts

* `build`: builds the website on top of the [repository] and indicates if there
  are changes to deploy

[repository]: https://github.com/lambdacats/lambdacats.github.io

* `deploy`: commits changes made by `build` locally and pushes those commits to
  the remote repository

#### Other

The command `zola build` (without flags) outputs generated files into a
`public/` directory, and we use the `tmp/` directory as described below. So,
we have added these to the `.gitignore` file.

## Creating the Initial Website

We use a so-call [Organization Pages site] with a repository at
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

Then, create an initial repository in `public/`:

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

To alleviate the maintenance burden, there are scripts for building the website
and deploying it to GitHub. While the entire process could be combined into one
script, it is separated into two so that we can inspect changes locally before
deploying them.

These scripts use Bash, can be run from anywhere in the repository, and exit on
the first error.

### Building

To build the website, run `./build`. This script:

1. ensures that we have a local clone of the [repository],

[repository]: https://github.com/lambdacats/lambdacats.github.io

2. runs `zola build`, and

3. handles the management of `.git/` before and after `zola build`.

### Deploying

To deploy changes to the website, run `./deploy`. This script:

1. updates the local repository index to match the current state of the
   directory as a whole (i.e. adds modifications, adds new files, and removes
   deleted files),

2. commits the index, and

3. pushes new commits to the remote repository.

This script exits with an error if `public/` does not exist.
