# UAARG Guidebook

<h4 align=center>
  [<a href="https://uaarg.com/guide/">Read it Now</a>]
</h4>

This is an mdbook containing documentation and tutorials for those working with
the UAARG code bases.

The entire book is meant to be self-contained. It should link to or explain all
concepts new members need to be productive on our codebases. Larger concepts
like Python programming or CS theory would be out of scope, but resources
should still be linked where possible.

The book is viewable at [uaarg.com/guide](https://uaarg.com/guide/).

## Working on the Guide

First, of course, clone this repository locally onto your machine.

You will need [`mdbook`](https://rust-lang.github.io/mdBook/index.html)
installed. You can follow the [mdbook installation
guide](https://rust-lang.github.io/mdBook/guide/installation.html) to get
started.

Then, I usually run the `mdbook serve` command from the root of the repository:

```sh 
$ mdbook serve
2023-09-02 16:06:31 [INFO] (mdbook::book): Book building has started
2023-09-02 16:06:31 [INFO] (mdbook::book): Running the html backend
2023-09-02 16:06:31 [INFO] (mdbook::cmd::serve): Serving on: http://localhost:3000
2023-09-02 16:06:31 [INFO] (mdbook::cmd::watch): Listening for changes...
2023-09-02 16:06:31 [INFO] (warp::server): Server::run; addr=[::1]:3000
2023-09-02 16:06:31 [INFO] (warp::server): listening on http://[::1]:3000
```

The book can then be previewed at [localhost:3000](http://localhost:3000).
