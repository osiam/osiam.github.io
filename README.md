# osiam.github.io

OSIAM's website powered by [GitHub Pages](https://pages.github.com/) and [Jekyll](http://jekyllrb.com/).

## Development

Clone this repo and install the dependencies via `bundler`:

```
git clone git@github.com:osiam/osiam.github.io.git
cd osiam.github.io
bundle install
```

Then you can start a local web server to preview your changes:

```
bundle exec jekyll serve
```

To additionally render drafts, add `--drafts` to the command:

```
bundle exec jekyll serve --drafts
```

You can now visit a preview of the website under `http://0.0.0.0:4000/`.

Refer to the documentation of [GitHub Pages](https://help.github.com/articles/using-jekyll-with-pages/)
and [Jekyll](http://jekyllrb.com/docs/home/) for more details.
