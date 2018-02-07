# miq_mac_dev

ManageIQ Dev Environment Setup Script for Mac

## Setup

1. Install Homebrew, if you don't have it already: https://brew.sh/

2. Install `rvm` (https://rvm.io/) and then run `rvm install 2.4.2` (Note: the `setup`, `update`, and `update-ui` scripts require `rvm`. If you want to use `rbenv` or something else, you'll just need to change those scripts. The important thing is that you load ruby 2.4.2)

3. Clone this repo in the directory where you want your manageiq repositories cloned:

   ```
   git clone https://github.com/mturley/miq_mac_dev
   ```

   *Note: `miq_mac_dev/setup` will move up to the directory above `miq_mac_dev` and clone siblings next to it. **Don't clone this repo in the same directory alongside existing manageiq and manageiq-ui-classic repos, use a fresh subdirectory if necessary:***

   ```
   mkdir miq; cd miq; git clone https://github.com/mturley/miq_mac_dev
   ```

4. Run `setup`. Be ready to provide a sudo password, you may be asked more than once:
   ```
   miq_mac_dev/setup
   ```
   or:
   ```
   miq_mac_dev/setup --with-v2v-plugin
   ```

## Setup Options

`setup` currently has one available option, therefore two possible modes to run this script in:

* Use `setup` with no options to install `manageiq` and `manageiq-ui-classic` from master.

* Use `setup --with-v2v-plugin` to also install priley's v2v plugin, and use the `v2v` branch of `manageiq-ui-classic` instead of master.

## Usage

### Running manageiq (3 options)

To run manageiq in the foreground, you can either run the rails server directly with `miq_mac_dev/rails`, or run it yourself:

```
cd manageiq
bundle exec rails s
```

or, you can run it with Foreman using `miq_mac_dev/foreman`, or run Foreman yourself:

```
cd manageiq
foreman start -p 3000
```

To run manageiq in the background, you can instead use `miq_mac_dev/evm` or run evm yourself:

```
cd manageiq
bundle exec rake evm:start
```

Tip: Running evm again if it is running already will tell you what pid it is running on.

At this point you should be able to view the rails application at http://localhost:3000/

### Running manageiq-ui-classic with webpack-dev-server

You can use `miq_mac_dev/webpack`, or run WDS yourself:

```
cd manageiq-ui-classic
env NODE_ENV=development ./node_modules/.bin/webpack-dev-server --config config/webpack/development.js
```

Then you can view the hot-reloading webpack-dev-server at http://localhost:8080/