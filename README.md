# Local packages for Nix

## Contents

* vault: " Generates safe passwords so you never need to remember them
" from https://github.com/jcoglan/vault

## Installation

I don't want to maintain a local fork of the nixpkgs repo with my
personal packages, at least unless and until I think it's worth trying
to get them merged.

Instead I use `packageOverrides`.  `packageOverrides` is (as far as I
can work out) a function that should accept a set of packages and
return another set of packages: nixpkgs calls it with the set of all
available packages and merges whatever it returns onto the same set.

There is a `config.nix` file in this repo.  If you already have a
`$HOME/.nixpkgs/config.nix` then merge the contents of this one with
that one.  If not, symlink - or even, check this repo out as
$HOME/.nixpkgs (I don't have enough experience with Nix to say if this
is a good idea or not)


## Implementation notes

### Vault

I started by running 

```
`nix-build '<nixpkgs>' -A npm2nix`/bin/npm2nix package.json node-packages.generated.nix
```

but the generated default.nix seems to need significant manual editing:

* first it gave me `Assertion failed: (!s.empty()), function showId, file src/libexpr/nixexpr.cc, line 33.` which seemed to be related to the definition of `tarball`.  I replaced this with `fetchFromGitHub`

* the `deps` definition in the gnerated expression produced errors about unknown attributes, so I replaced that line with

```
deps = (filter (v: nixType v == "derivation") (attrValues nodePackages))
```

