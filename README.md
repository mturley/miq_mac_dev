# miq_mac_dev

ManageIQ Dev Environment Setup Script for Mac

## Setup

1. Install Homebrew, if you don't have it already: https://brew.sh/

2. Clone this repo in the directory where you want your manageiq repositories cloned.

   `git clone https://github.com/mturley/miq_mac_dev`

   *Note: `miq_mac_dev/setup` will move up to the directory above miq_mac_dev and clone siblings next to it. Don't clone this repo in the same directory alongside existing manageiq and manageiq-ui-classic repos, use a fresh subdirectory if necessary:*

   `mkdir miq; cd miq; git clone https://github.com/mturley/miq_mac_dev`

3. Run `miq_mac_dev/setup`. Be ready to provide a sudo password, you may be asked more than once.

   `miq_mac_dev/setup` or `miq_mac_dev/setup --with-v2v-plugin`


## Setup Options

`setup` currently has one available option, therefore two possible modes to run this script in:

* Use `setup` with no options to install `manageiq` and `manageiq-ui-classic` from master.

* Use `setup --with-v2v-plugin` to also install priley's v2v plugin, and use the `v2v` branch of `manageiq-ui-classic` instead of master.

## Usage

### Running manageiq with foreman or evm

To run manageiq in the foreground, you can use `miq_mac_dev/foreman`, or run Foreman yourself:

```
cd manageiq
foreman start -p 3000
```

To run manageiq in the background, you cam use, `miq_mac_dev/evm` or run evm yourself:

```
cd manageiq
bundle exec rake evm:start
```

### Running manageiq-ui-classic with webpack-dev-server

You can use `miq_mac_dev/webpack`, or run WDS yourself:

```
cd manageiq-ui-classic
env NODE_ENV=development ./node_modules/.bin/webpack-dev-server --config config/webpack/development.js
```