

## [First Steps with Cargo](https://doc.rust-lang.org/cargo/getting-started/first-steps.html)
This section provides a quick sense for the `cargo` command line tool. We demonstrate its ability to generate a new package for us, its ability to compile the crate within the package, and its ability to run the resulting program.Cargo defaults to `--bin` to make a binary program. To make a library, we would pass `--lib`, instead.
Let’s check out what Cargo has generated for us.

```
$ cargo new hello_world
$ cd hello_world

├── Cargo.toml
└── src
    └── main.rs

1 directory, 2 files
```

This is all we need to get started. First, let’s check out `Cargo.toml`.This is called a manifest, and it contains all of the metadata that Cargo needs to compile your package.
`Cargo.toml` is about describing your dependencies in a broad sense, and is written by you.
`Cargo.lock` contains exact information about your dependencies. It is maintained by Cargo and should not be manually edited.
```
[package]
name = "hello_world"
version = "0.1.0"
edition = "2021"

[dependencies]
```

```
$ cargo run
```

## [Dependencies](https://doc.rust-lang.org/cargo/guide/dependencies.html)

[crates.io](https://crates.io/) is the Rust community's central package registry that serves as a location to discover and download packages. `cargo` is configured to use it by default to find requested packages.
To depend on a library hosted on crates.io, add it to your `Cargo.toml`.If your `Cargo.toml` doesn't already have a `[dependencies]` section, add that, then list the crate name and version that you would like to use. This example adds a dependency of the `time` crate. Re-run `cargo build`, and Cargo will fetch the new dependencies and all of their dependencies, compile them all, and update the `Cargo.lock`.
```
[dependencies]
time = "0.1.12"
regex = "0.1.41"
```
Our `Cargo.lock` contains the exact information about which revision of all of these dependencies we used.
Now, if `regex` gets updated, we will still build with the same revision until we choose to `cargo update`.
You can now use the regex library in `main.rs`.
```
use regex::Regex;

fn main() {
    let re = Regex::new(r"^\d{4}-\d{2}-\d{2}$").unwrap();
    println!("Did our date match? {}", re.is_match("2014-01-01"));
}
```

