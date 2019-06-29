# Setting up Development Environment

## Install Node.js

Install Node.js by your favorite method, or use Node Version Manager by following directions at https://github.com/creationix/nvm

```bash
nvm install v4
```

## Fork and Download Repositories

To develop kienscoincore-node:

```bash
cd ~
git clone git@github.com:<yourusername>/kienscoincore-node.git
git clone git@github.com:<yourusername>/kienscoin-lib.git
```

To develop kienscoin or to compile from source:

```bash
git clone git@github.com:<yourusername>/kienscoin.git
git fetch origin <branchname>:<branchname>
git checkout <branchname>
```
**Note**: See kienscoin documentation for building kienscoin on your platform.


## Install Development Dependencies

For Ubuntu:
```bash
sudo apt-get install libzmq3-dev
sudo apt-get install build-essential
```
**Note**: Make sure that libzmq-dev is not installed, it should be removed when installing libzmq3-dev.


For Mac OS X:
```bash
brew install zeromq
```

## Install and Symlink

```bash
cd bitcore-lib
npm install
cd ../bitcore-node
npm install
```
**Note**: If you get a message about not being able to download kienscoin distribution, you'll need to compile kienscoind from source, and setup your configuration to use that version.


We now will setup symlinks in `kienscoincore-node` *(repeat this for any other modules you're planning on developing)*:
```bash
cd node_modules
rm -rf kienscoin-lib
ln -s ~/kienscoin-lib
rm -rf kienscoind-rpc
ln -s ~/kienscoind-rpc
```

And if you're compiling or developing kienscoin:
```bash
cd ../bin
ln -sf ~/kienscoin/src/kienscoind
```

## Run Tests

If you do not already have mocha installed:
```bash
npm install mocha -g
```

To run all test suites:
```bash
cd kienscoincore-node
npm run regtest
npm run test
```

To run a specific unit test in watch mode:
```bash
mocha -w -R spec test/services/kienscoind.unit.js
```

To run a specific regtest:
```bash
mocha -R spec regtest/kienscoind.js
```

## Running a Development Node

To test running the node, you can setup a configuration that will specify development versions of all of the services:

```bash
cd ~
mkdir devnode
cd devnode
mkdir node_modules
touch kienscoincore-node.json
touch package.json
```

Edit `kienscoincore-node.json` with something similar to:
```json
{
  "network": "livenet",
  "port": 3001,
  "services": [
    "kienscoind",
    "web",
    "kienscoin-api",
    "insight-ui",
    "<additional_service>"
  ],
  "servicesConfig": {
    "kienscoind": {
      "spawn": {
        "datadir": "/home/<youruser>/.kienscoin",
        "exec": "/home/<youruser>/kienscoin/src/kienscoind"
      }
    }
  }
}
```

**Note**: To install services [kienscoin-api](https://github.com/HTMLCOIN/kienscoin-api) and [kienscoin-explorer](https://github.com/HTMLCOIN/kienscoin-explorer) you'll need to clone the repositories locally.

Setup symlinks for all of the services and dependencies:

```bash
cd node_modules
ln -s ~/kienscoin-lib
ln -s ~/kienscoincore-node
ln -s ~/kienscoin-api
ln -s ~/kienscoin-explorer
```

Make sure that the `<datadir>/kienscoin.conf` has the necessary settings, for example:
```
server=1
whitelist=127.0.0.1
txindex=1
addressindex=1
timestampindex=1
spentindex=1
zmqpubrawtx=tcp://127.0.0.1:28332
zmqpubhashblock=tcp://127.0.0.1:28332
rpcallowip=127.0.0.1
rpcuser=user
rpcpassword=password
rpcport=18332
reindex=1
gen=0
addrindex=1
logevents=1
```

From within the `devnode` directory with the configuration file, start the node:
```bash
../kienscoincore-node/bin/kienscoincore-node start
```