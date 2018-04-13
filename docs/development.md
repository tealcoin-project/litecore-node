# Setting up Development Environment

## Install Node.js

Install Node.js by your favorite method, or use Node Version Manager by following directions at https://github.com/creationix/nvm

```bash
nvm install v4
```

## Fork and Download Repositories

To develop litecore-tealcoin-node:

```bash
cd ~
git clone git@github.com:<yourusername>/litecore-tealcoin-node.git
git clone git@github.com:<yourusername>/litecore-tealcoin-lib.git
```

To develop tealcoin or to compile from source:

```bash
git clone git@github.com:<yourusername>/tealcoin.git
git fetch origin <branchname>:<branchname>
git checkout <branchname>
```
**Note**: See tealcoin documentation for building tealcoin on your platform.


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
cd litecore-tealcoin-lib
npm install
cd ../litecore-tealcoin-node
npm install
```
**Note**: If you get a message about not being able to download tealcoin distribution, you'll need to compile tealcoind from source, and setup your configuration to use that version.


We now will setup symlinks in `litecore-tealcoin-node` *(repeat this for any other modules you're planning on developing)*:
```bash
cd node_modules
rm -rf litecore-tealcoin-lib
ln -s ~/litecore-tealcoin-lib
rm -rf bitcoind-rpc
ln -s ~/bitcoind-rpc
```

And if you're compiling or developing tealcoin:
```bash
cd ../bin
ln -sf ~/tealcoin/src/tealcoind
```

## Run Tests

If you do not already have mocha installed:
```bash
npm install mocha -g
```

To run all test suites:
```bash
cd litecore-tealcoin-node
npm run regtest
npm run test
```

To run a specific unit test in watch mode:
```bash
mocha -w -R spec test/services/bitcoind.unit.js
```

To run a specific regtest:
```bash
mocha -R spec regtest/bitcoind.js
```

## Running a Development Node

To test running the node, you can setup a configuration that will specify development versions of all of the services:

```bash
cd ~
mkdir devnode
cd devnode
mkdir node_modules
touch litecore-tealcoin-node.json
touch package.json
```

Edit `litecore-tealcoin-node.json` with something similar to:
```json
{
  "network": "livenet",
  "port": 3001,
  "services": [
    "tealcoind",
    "web",
    "insight-api",
    "insight-ui",
    "<additional_service>"
  ],
  "servicesConfig": {
    "tealcoind": {
      "spawn": {
        "datadir": "/home/<youruser>/.tealcoin",
        "exec": "/home/<youruser>/tealcoin/src/tealcoind"
      }
    }
  }
}
```

**Note**: To install services [insight-api](https://github.com/bitpay/insight-api) and [insight-ui](https://github.com/bitpay/insight-ui) you'll need to clone the repositories locally.

Setup symlinks for all of the services and dependencies:

```bash
cd node_modules
ln -s ~/litecore-tealcoin-lib
ln -s ~/litecore-tealcoin-node
ln -s ~/insight-api
ln -s ~/insight-ui
```

Make sure that the `<datadir>/tealcoin.conf` has the necessary settings, for example:
```
server=1
whitelist=127.0.0.1
txindex=1
addressindex=1
timestampindex=1
spentindex=1
zmqpubrawtx=tcp://127.0.0.1:29332
zmqpubhashblock=tcp://127.0.0.1:29332
rpcallowip=127.0.0.1
rpcuser=tealcoin
rpcpassword=local321
```

From within the `devnode` directory with the configuration file, start the node:
```bash
../litecore-tealcoin-node/bin/litecore-tealcoin-node start
```
