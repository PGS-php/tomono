# Multi-To-Mono repository

Merge multiple repositories into one big monorepository. Migrates every branch in
every subrepo to the eponymous branch in the monorepo, with all files
(including in the history) rewritten to live under a subdirectory.

**This version of tomono.sh uses previous approach that overrides commits hashes and moves files to subdirectory using `filter-branch`.**

Features:

* Keep history but override commit hashes of all repositories.

Requirements:
* git version 2.9+.

## Usage

Prepare a list of repositories to merge in a file. The format is
`<repository_url><space><new_name>`. If you try and use a slash in
`<new_name>` it will fail because it uses this as a `git remote`. If
you need to have a slash, i.e. some folder depth, pass a third
parameter, the format will then be:
`<repository_url><space><new_name><space><folder_name>`

Here is an example `repos.txt` where the services are directly in the
root of the repository and the libraries are in a `/lib` subfolder:

```
git@github.com:mycompany/service-one.git one
git@github.com:mycompany/service-two.git two
git@github.com:mycompany/library-three.git three lib/three
git@github.com:mycompany/library-four.git four lib/four
```

Now pipe the file to the tomono.sh script. Assuming you've downloaded this
program to your home directory, for example, you can do:

```sh
$ cat repos.txt | ~/tomono/tomono.sh
```

This will create a new repository called `core`, in your current directory.

If you already have a repository called `core` and wish to import more into it,
pass the `--continue` flag. Make sure you don't have any outstanding changes!

To change the name of the monorepo directory, set an envvar before any other
operations:

```sh
$ export MONOREPO_NAME=my_directory
$ ...
```

### Tags and namespacing

Note that all tags are checkout as release branches: e.g. if your remote `one` has tags `v1.0.0` and `v1.1.0`, your new monorepo will have branches `release/v1.0.0` and `release/v1.1.0` with appropriate version of the codebase. If `two` remote has the same tags then all tag versions would be merge into `release/${tag}` branch e.g. `release/v1.0.0` with `one` and `two` subdirs. 

 **Please keep in mind that after migration step the script is walking through the all release branches and create a new tags**

### Server migration

Let's assume you have the following directory structure on the server:

```
/one/
/one/.git
/two/
/two/.git
/tree/
/tree/.git
```

Everything what you have to do is:
```
git clone ${monoRepoUrl} monorepo

mv monorepo/.git .
rm -rf ./one/.git
rm -rf ./two/.git
rm -rf ./tree/.git
rm -rf ./monorepo

git checkout v1.3.0
```

Result:

```
/.git
/one/
/two/
/tree/
```

### Examples

```
cd example
cat repos.txt | ../tomono.sh
```

The final monorepo will be available in `./example/core`.

### Enjoy!