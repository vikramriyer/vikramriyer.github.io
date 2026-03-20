# Run Instructions

## Prerequisites

- **Ruby** (3.x recommended; the macOS system Ruby 2.6 is too old)
- **Bundler**

### Install Ruby via Homebrew (if needed)

```sh
brew install ruby
```

Add Homebrew Ruby to your PATH (add to ~/.zshrc to persist):

```sh
export PATH="$(brew --prefix ruby)/bin:$PATH"
```

## Setup

Install gem dependencies locally:

```sh
bundle config set --local path 'vendor/bundle'
bundle install
```

## Run Locally

```sh
bundle exec jekyll serve
```

The site will be available at [http://localhost:4000](http://localhost:4000).

### Live reload (auto-refresh on changes)

```sh
bundle exec jekyll serve --livereload
```

## Build Only (no server)

```sh
bundle exec jekyll build
```

Output goes to the `_site/` directory.
