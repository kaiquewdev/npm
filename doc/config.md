npm-config(1) -- Manage the npm configuration file
==================================================

## SYNOPSIS

    npm config set <key> <value> [--global]
    npm config get <key>
    npm config delete <key>
    npm config list
    npm config edit
    npm get <key>
    npm set <key> <value> [--global]

## DESCRIPTION

npm gets its configuration values from 5 sources, in this priority:

* cli:
  The command line flags.  Putting `--foo bar` on the command line sets the
  `foo` configuration parameter to `"bar"`.  A `--` argument tells the cli
  parser to stop reading flags.  A `--flag` parameter that is at the *end* of
  the command will be given the value of `true`.
* env:
  Any environment variables that start with `npm_config_` will be interpreted
  as a configuration parameter.  For example, putting `npm_config_foo=bar` in
  your environment will set the `foo` configuration parameter to `bar`.  Any
  environment configurations that are not given a value will be given the value
  of `true`.  Config values are case-insensitive, so `NPM_CONFIG_FOO=bar` will
  work the same.
* $HOME/.npmrc (or the `userconfig` param, if set above):
  This file is an ini-file formatted list of `key = value` parameters.
* $PREFIX/etc/npmrc (or the `globalconfig` param, if set above):
  This file is an ini-file formatted list of `key = value` parameters
* default configs:
  This is a set of configuration parameters that are internal to npm, and are
  defaults if nothing else is specified.

## Sub-commands

Config supports the following sub-commands:

### set

    npm config set key value

Sets the config key to the value.

If value is omitted, then it sets it to "true".

### get

    npm config get key

Echo the config value to stdout.

### list

    npm config list

Show all the config settings.

### delete

    npm config delete key

Deletes the key from all configuration files.

### edit

    npm config edit

Opens the config file in an editor.  Use the `--global` flag to edit the
global config.

## Shorthands and Other CLI Niceties

The following shorthands are parsed on the command-line:

* `-v`: `--version`
* `-h`, `-?`, `--help`, `-H`: `--usage`
* `-s`, `--silent`: `--loglevel silent`
* `-d`: `--loglevel info`
* `-dd`, `--verbose`: `--loglevel verbose`
* `-ddd`: `--loglevel silly`
* `-g`: `--global`
* `-l`: `--long`
* `-p`, `--porcelain`: `--parseable`
* `-reg`: `--registry`
* `-v`: `--version`
* `-f`: `--force`
* `-l`: `--long`
* `-desc`: `--description`
* `-S`: `--save`
* `-y`: `--yes`
* `-n`: `--yes false`
* `ll` and `la` commands: `ls --long`

If the specified configuration param resolves unambiguously to a known
configuration parameter, then it is expanded to that configuration
parameter.  For example:

    npm ls --par
    # same as:
    npm ls --parseable

If multiple single-character shorthands are strung together, and the
resulting combination is unambiguously not some other configuration
param, then it is expanded to its various component pieces.  For
example:

    npm ls -gpld
    # same as:
    npm ls --global --parseable --long --loglevel info

## Per-Package Config Settings

When running scripts (see `npm help scripts`)
the package.json "config" keys are overwritten in the environment if
there is a config param of `<name>[@<version>]:<key>`.  For example, if
the package.json has this:

    { "name" : "foo"
    , "config" : { "port" : "8080" }
    , "scripts" : { "start" : "node server.js" } }

and the server.js is this:

    http.createServer(...).listen(process.env.npm_package_config_port)

then the user could change the behavior by doing:

    npm config set foo:port 80

## Config Settings

### always-auth

* Default: false
* Type: Boolean

Force npm to always require authentication when accessing the registry,
even for `GET` requests.

### bin-publish

* Default: false
* Type: Boolean

If set to true, then binary packages will be created on publish.

This is the way to opt into the "bindist" behavior described below.

### bindist

* Default: Unstable node versions, `null`, otherwise
  `"<node version>-<platform>-<os release>"`
* Type: String or `null`

Experimental: on stable versions of node, binary distributions will be
created with this tag.  If a user then installs that package, and their
`bindist` tag is found in the list of binary distributions, they will
get that prebuilt version.

Pre-build node packages have their preinstall, install, and postinstall
scripts stripped (since they are run prior to publishing), and do not
have their `build` directories automatically ignored.

It's yet to be seen if this is a good idea.

### browser

* Default: OS X: `"open"`, others: `"google-chrome"`
* Type: String

The browser that is called by the `npm docs` command to open websites.

### cache

* Default: Windows: `~/npm-cache`, Posix: `~/.npm`
* Type: path

The location of npm's cache directory.  See `npm help cache`

### color

* Default: true
* Type: Boolean or `"always"`

If false, never shows colors.  If `"always"` then always shows colors.
If true, then only prints color codes for tty file descriptors.

### depth

* Default: Infinity
* Type: Number

The depth to go when recursing directories for `npm ls` and
`npm cache ls`.

### description

* Default: true
* Type: Boolean

Show the description in `npm search`

### dev

* Default: false
* Type: Boolean

Install `dev-dependencies` along with packages.

Note that `dev-dependencies` are also installed if the `npat` flag is
set.

### editor

* Default: `EDITOR` environment variable if set, or `"vi"`
* Type: path

The command to run for `npm edit` or `npm config edit`.

### force

* Default: false
* Type: Boolean

Makes various commands more forceful.

* lifecycle script failure does not block progress.
* publishing clobbers previously published versions.
* skips cache when requesting from the registry.
* prevents checks against clobbering non-npm files.

### global

* Default: false
* Type: Boolean

Operates in "global" mode, so that packages are installed into the
`prefix` folder instead of the current working directory.  See
`npm help folders` for more on the differences in behavior.

* packages are installed into the `prefix/node_modules` folder, instead of the
  current working directory.
* bin files are linked to `prefix/bin`
* man pages are linked to `prefix/share/man`

### globalconfig

* Default: {prefix}/etc/npmrc
* Type: path

The config file to read for global config options.

### globalignorefile

* Default: {prefix}/etc/npmignore
* Type: path

The config file to read for global ignore patterns to apply to all users
and all projects.

If not found, but there is a "gitignore" file in the
same directory, then that will be used instead.

### group

* Default: GID of the current process
* Type: String or Number

The group to use when running package scripts in global mode as the root
user.

### gzipbin

* Default: "gzip"
* Type: path

The gzip binary

### ignore

* Default: ""
* Type: string

A white-space separated list of glob patterns of files to always exclude
from packages when building tarballs.

### init.version 

* Default: "0.0.0"
* Type: semver

The value `npm init` should use by default for the package version.

### init.author.name

* Default: "0.0.0"
* Type: String

The value `npm init` should use by default for the package author's name.

### init.author.email

* Default: ""
* Type: String

The value `npm init` should use by default for the package author's email.

### init.author.url

* Default: ""
* Type: String

The value `npm init` should use by default for the package author's homepage.

### link

* Default: false
* Type: Boolean

If true, then local installs will link if there is a suitable globally
installed package.

Note that this means that local installs can cause things to be
installed into the global space at the same time.  The link is only done
if one of the two conditions are met:

* The package is not already installed globally, or
* the globally installed version is identical to the version that is
  being installed locally.

### logfd

* Default: stderr file descriptor
* Type: Number or Stream

The location to write log output.

### loglevel

* Default: "warn"
* Type: String
* Values: "silent", "win", "error", "warn", "info", "verbose", "silly"

What level of logs to report.  On failure, *all* logs are written to
`npm-debug.log` in the current working directory.

### long

* Default: false
* Type: Boolean

Show extended information in `npm ls`

### node-version

* Default: process.version
* Type: semver or false

The node version to use when checking package's "engines" hash.

### npat

* Default: false
* Type: Boolean

Run tests on installation and report results to the
`npaturl`.

### npaturl

* Default: Not yet implemented
* Type: url

The url to report npat test results.

### onload-script

* Default: false
* Type: path

A node module to `require()` when npm loads.  Useful for programmatic
usage.

### outfd

* Default: standard output file descriptor
* Type: Number or Stream

Where to write "normal" output.  This has no effect on log output.

### parseable

* Default: false
* Type: Boolean

Output parseable results from commands that write to
standard output.

### prefix

* Default: node's process.installPrefix
* Type: path

The location to install global items.  If set on the command line, then
it forces non-global commands to run in the specified folder.

### production

* Default: false
* Type: Boolean

Set to true to run in "production" mode.

1. devDependencies are not installed at the topmost level when running
   local `npm install` without any arguments.
2. Set the NODE_ENV="production" for lifecycle scripts.

### proxy

* Default: `HTTP_PROXY` or `http_proxy` environment variable, or null
* Type: url

A proxy to use for outgoing http requests.

### rebuild-bundle

* Default: true
* Type: Boolean

Rebuild bundled dependencies after installation.

### registry

* Default: https://registry.npmjs.org/
* Type: url

The base URL of the npm package registry.

### rollback

* Default: true
* Type: Boolean

Remove failed installs.

### save

* Default: false
* Type: Boolean

Save installed packages to a package.json file as dependencies.

Only works if there is already a package.json file present.

### searchopts

* Default: ""
* Type: String

Space-separated options that are always passed to search.

### searchexclude

* Default: ""
* Type: String

Space-separated options that limit the results from search.

### shell

* Default: SHELL environment variable, or "bash"
* Type: path

The shell to run for the `npm explore` command.

### tag

* Default: latest
* Type: String

If you ask npm to install a package and don't tell it a specific version, then
it will install the specified tag.

Also the tag that is added to the package@version specified by the `npm
tag` command, if no explicit tag is given.

### tar

* Default: TAR environment variable, or "tar"
* Type: path

The tar executable

### tmp

* Default: TMPDIR environment variable, or "/tmp"
* Type: path

Where to store temporary files and folders.  All temp files are deleted
on success, but left behind on failure for forensic purposes.

### unicode

* Default: true
* Type: Boolean

When set to true, npm uses unicode characters in the tree output.  When
false, it uses ascii characters to draw trees.

### unsafe-perm

* Default: false if running as root, true otherwise
* Type: Boolean

Set to true to suppress the UID/GID switching when running package
scripts.  If set explicitly to false, then installing as a non-root user
will fail.

### usage

* Default: false
* Type: Boolean

Set to show short usage output (like the -H output)
instead of complete help when doing `npm help`.

### user

* Default: "nobody"
* Type: String or Number

The UID to set to when running package scripts as root.

### username

* Default: null
* Type: String

The username on the npm registry.  Set with `npm adduser`

### userconfig

* Default: ~/.npmrc
* Type: path

The location of user-level configuration settings.

### userignorefile

* Default: ~/.npmignore
* Type: path

The location of a user-level ignore file to apply to all packages.

If not found, but there is a .gitignore file in the same directory, then
that will be used instead.

### version

* Default: false
* Type: boolean

If true, output the npm version and exit successfully.

Only relevant when specified explicitly on the command line.

### viewer

* Default: "man"
* Type: path

The program to use to view help content.

### yes

* Default: null
* Type: Boolean or null

If set to `null`, then prompt the user for responses in some
circumstances.

If set to `true`, then answer "yes" to any prompt.  If set to `false`
then answer "no" to any prompt.
