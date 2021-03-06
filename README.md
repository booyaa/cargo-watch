# $ cargo watch

[![Crate release version](https://img.shields.io/crates/v/cargo-watch.svg?style=flat-square)](https://crates.io/crates/cargo-watch)
[![Crate license: CC0 1.0](https://img.shields.io/crates/l/cargo-watch.svg?style=flat-square)](https://creativecommons.org/publicdomain/zero/1.0/)
![Crate download count](https://img.shields.io/crates/d/cargo-watch.svg?style=flat-square)

[![Build status (Travis)](https://img.shields.io/travis/passcod/cargo-watch.svg?style=flat-square)](https://travis-ci.org/passcod/cargo-watch)
[![Code of Conduct](https://img.shields.io/badge/CoC-WeAllBehave-123456.svg?style=flat-square)](./CODE_OF_CONDUCT.md)

Cargo Watch watches over your project's source for changes, and runs Cargo
commands when they occur.

If you've used [nodemon], [gulp], [guard], [watchman], or similar others,
it will probably feel familiar.

[nodemon]: http://nodemon.io/
[gulp]: http://gulpjs.com/
[guard]: http://guardgem.org/
[watchman]: https://facebook.github.io/watchman/

## Install

```
$ cargo install cargo-watch
```

To upgrade:

```
$ cargo install --force cargo-watch
```

Or clone and build with `$ cargo build` then place in your $PATH.

## Usage

By default, it runs `check` (which is available [since Rust 1.16][st-check]).
You can easily override this, though:

```
$ cargo watch [-x command]...
```

[st-check]: https://blog.rust-lang.org/2017/03/16/Rust-1.16.html

A few examples:

```
# Run tests only
$ cargo watch -x test

# Run check then tests
$ cargo watch -x check -x test

# Run run with arguments
$ cargo watch -x 'run -- --some-arg'

# Run an arbitrary command
$ cargo watch -s 'echo Hello world'
```

There's a lot more you can do! Here's a copy of the help:

```
USAGE:
    cargo watch [FLAGS] [OPTIONS]

FLAGS:
    -c, --clear             Clear the screen before each run
    -h, --help              Display this message
        --ignore-nothing    Ignore nothing, not even target/
        --no-gitignore      Don’t use .gitignore files
        --poll              Force use of polling for file changes
        --postpone          Postpone first run until a file changes
    -q, --quiet             Suppress output from cargo-watch itself
    -V, --version           Display version information

OPTIONS:
    -x, --exec <cmd>...
            Cargo command(s) to execute on changes [default: check]
    -s, --shell <cmd>...
            Shell command(s) to execute on changes
    -d, --delay <delay>
            File updates debounce delay in seconds [default: 1]
    -i, --ignore <pattern>...
            Ignore a glob/gitignore-style pattern
    -w, --watch <watch>...
            Watch specific file(s) or folder(s) [default: .]

Cargo commands (-x) are always executed before shell commands (-s).

By default, your entire project is watched, except for the target/
folder, and your .gitignore files are used to filter paths.
```

### Prettier compiler output

Cargo watch pairs well with [dybuk], the compiler output prettifier:

```
$ cargo watch |& dybuk
```

[dybuk]: https://github.com/Ticki/dybuk

## Contributing

The cargo-watch team enthusiastically welcomes contributions and project
participation! There's a bunch of things you can do if you want to contribute!
The [Contributor Guide](./CONTRIBUTING.md) has all the information you need for
everything from reporting bugs to contributing entire new features. Please
don't hesitate to jump in if you'd like to, or even ask us questions if
something isn't clear. <sub>{[attribution](https://github.com/zkat/pacote#contributing)}</sub>

## Troubleshooting

### If running cargo watch errors with "Found argument 'build' which wasn't expected" (or similar)

You're probably using **version 4** (or higher) but using the **version 3** (or
lower) style of arguments. The interface changed! Refer to the sections above
for new usage guidelines, or to the help message:

```
$ cargo watch --help
```

### I want to run cargo-watch directly, without going through cargo

You can! But you'll have to specify the `watch` subcommand as the first
argument, like so:

```
$ /path/to/cargo-watch watch -x build
```

### If file updates seems to never trigger

Try using `--poll` to force the polling fallback.

If that still doesn't work, and you're using an editor that does "safe saving",
like IntelliJ / PyCharm, you may have to disable "safe saving" as that may
prevent file notifications from being generated properly.

### Linux: If it fails to watch some deep directories but not others

You may have hit [the inotify watch limit][inotify-limit]. You can either
increase the limit (instructions are on the previous link and at [this Guard
wiki page][inotify-guard]), or you can stop whatever it is that's consuming so
many inotify watches.

[inotify-limit]: http://blog.sorah.jp/2012/01/24/inotify-limitation
[inotify-guard]: https://github.com/guard/listen/wiki/Increasing-the-amount-of-inotify-watchers

### If you want to only recompile one Cargo workspace

Cargo workspaces [are not natively supported yet][i-52].

However, as you can run "arbitrary commands" with the `-s` option, you can
write workspace-aware commands manually.

[i-52]: https://github.com/passcod/cargo-watch/issues/52

### If it runs repeatedly without touching anything

That can happen when watching files that are modified by the command you're
running.

If you're only running compiles or checks (i.e. any command that only affects
the target/ folder) and you're using `-w`, you might be confusing the
target-folder-ignorer. Check your options and paths.

### Something not covered above / I have a feature request

Please [open an issue][watch-issues], or look through the existing ones. You
may also want to look through [issues for the Notify library][notify-issues]
this tool depends on.

If you want more verbose output, try running with the
`RUST_LOG=cargo_watch=info` environment variable. You can switch `info` to
`debug` or even `trace` (caution: very busy output!) for even more messages.

Note that `-q` will hide some `error`-level messages, even with `RUST_LOG`.

[notify-issues]: https://github.com/passcod/notify/issues
[watch-issues]: https://github.com/passcod/cargo-watch/issues

## About

Created by [Félix Saparelli][passcod] and [awesome contributors][contributors].

[contributors]: https://github.com/passcod/cargo-watch/network/members
[passcod]: https://passcod.name
