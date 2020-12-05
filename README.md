# Textwrap

[![](https://github.com/mgeisler/textwrap/workflows/build/badge.svg)][build-status]
[![](https://codecov.io/gh/mgeisler/textwrap/branch/master/graph/badge.svg)][codecov]
[![](https://img.shields.io/crates/v/textwrap.svg)][crates-io]
[![](https://docs.rs/textwrap/badge.svg)][api-docs]

Textwrap is a library for word wrapping text. You can use it to format
strings for display in commandline applications. The crate name and
interface is inspired by the [Python textwrap module][py-textwrap].

## Usage

To use `textwrap`, add this to your `Cargo.toml` file:
```toml
[dependencies]
textwrap = "0.13"
```

This gives you the text wrapping without of the optional Cargo
features listed next.

### `hyphenation`

If you would like to have automatic language-sensitive hyphenation,
enable the `hyphenation` feature:

```toml
[dependencies]
textwrap = { version = "0.13", features = ["hyphenation"] }
```

This gives you hyphenation support for US English. Please see the
[`hyphenation` example] for an executable demo. Read the Getting
Started section below to see how to load the hyphenation patterns for
other languages.

### `terminal_size`

To conveniently wrap text at the current terminal width, enable the
`terminal_size` feature:

```toml
[dependencies]
textwrap = { version = "0.13", features = ["terminal_size"] }
```

Please see the [`termwidth` example] for how to use this feature.

## Documentation

**[API documentation][api-docs]**

## Getting Started

Word wrapping is easy using the `fill` function:

```rust
fn main() {
    let text = "textwrap: an efficient and powerful library for wrapping text.";
    println!("{}", textwrap::fill(text, 28));
}
```

The output is wrapped within 28 columns:

```
textwrap: an efficient
and powerful library for
wrapping text.
```

Sharp-eyed readers will notice that the first line is 22 columns wide.
So why is the word “and” put in the second line when there is space
for it in the first line?

The explanation is that textwrap does not just wrap text one line at a
time. Instead, it uses an optimal-fit algorithm which looks ahead and
chooses line breaks which minimize the gaps left at ends of lines.

Without look ahead, the first line would be longer and the text would
look like this:

```
textwrap: an efficient and
powerful library for
wrapping text.
```

The second line is now shorter and the text is more ragged. The kind
of wrapping can be configured via `Option::wrap_algorithm`.

If you enable the `hyphenation` Cargo feature, you get support for
automatic hyphenation for [about 70 languages][patterns] via
high-quality TeX hyphenation patterns.

Your program must load the hyphenation pattern and configure
`Options::splitter` to use it:

```rust
use hyphenation::{Language, Load, Standard};
use textwrap::Options;

fn main() {
    let hyphenator = Standard::from_embedded(Language::EnglishUS).unwrap();
    let options = Options::new(28).splitter(hyphenator);
    let text = "textwrap: an efficient and powerful library for wrapping text.";
    println!("{}", fill(text, &options);
}
```

The output now looks like this:
```
textwrap: an efficient and
powerful library for wrap-
ping text.
```

The US-English hyphenation patterns are embedded when you enable the
`hyphenation` feature. They are licensed under a [permissive
license][en-us license] and take up about 88 KB in your binary. If you
need hyphenation for other languages, you need to download a
[precompiled `.bincode` file][bincode] and load it yourself. Please
see the [`hyphenation` documentation] for details.

## Wrapping Strings at Compile Time

If your strings are known at compile time, please take a look at the
procedural macros from the [`textwrap-macros` crate].


## Examples

The library comes with [a
collection](https://github.com/mgeisler/textwrap/tree/master/examples)
of small example programs that shows various features. You’re invited
to clone the repository and try them out for yourself!

Of special note is the `interactive` example. This is a demo program
which demonstrates most of the available features: you can enter text
and adjust the width at which it is wrapped interactively. You can
also adjust the `Options` used to see the effect of different
`WordSplitter`s and wrap algorithms.

Run the demo with

```sh
$ cargo run --example interactive
```

The demo needs a Linux terminal to function.

## Release History

Please see the [CHANGELOG file] for details on the changes made in
each release.

## License

Textwrap can be distributed according to the [MIT license][mit].
Contributions will be accepted under the same license.

[crates-io]: https://crates.io/crates/textwrap
[build-status]: https://github.com/mgeisler/textwrap/actions?query=workflow%3Abuild+branch%3Amaster
[codecov]: https://codecov.io/gh/mgeisler/textwrap
[py-textwrap]: https://docs.python.org/library/textwrap
[`textwrap-macros` crate]: https://crates.io/crates/textwrap-macros
[`hyphenation` example]: https://github.com/mgeisler/textwrap/blob/master/examples/hyphenation.rs
[`termwidth` example]: https://github.com/mgeisler/textwrap/blob/master/examples/termwidth.rs
[patterns]: https://github.com/tapeinosyne/hyphenation/tree/master/patterns-tex
[en-us license]: https://github.com/hyphenation/tex-hyphen/blob/master/hyph-utf8/tex/generic/hyph-utf8/patterns/tex/hyph-en-us.tex
[bincode]: https://github.com/tapeinosyne/hyphenation/tree/master/dictionaries
[`hyphenation` documentation]: http://docs.rs/hyphenation
[api-docs]: https://docs.rs/textwrap/
[CHANGELOG file]: https://github.com/mgeisler/textwrap/blob/master/CHANGELOG.md
[mit]: LICENSE
