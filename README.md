# bch-dex

[![js-standard-style](https://img.shields.io/badge/code%20style-standard-brightgreen.svg)](http://standardjs.com) [![semantic-release](https://img.shields.io/badge/%20%20%F0%9F%93%A6%F0%9F%9A%80-semantic--release-e10079.svg)](https://github.com/semantic-release/semantic-release) [![Join the chat at https://gitter.im/Permissionless-Software-Foundation/psf-dex-dev](https://badges.gitter.im/Permissionless-Software-Foundation/psf-dex-dev.svg)](https://gitter.im/Permissionless-Software-Foundation/psf-dex-dev?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

This is a prototype web service that monitors the [P2WDB](https://github.com/Permissionless-Software-Foundation/ipfs-p2wdb-service) for trading signals, to trade BCH and SLP tokens. It's inspired by the [SWaP Protocol](https://github.com/vinarmani/swap-protocol/blob/master/swap-protocol-spec.md).

This repository contains the back end code. The user interface is contained in the [bch-dex-ui](https://github.com/Permissionless-Software-Foundation/bch-dex-ui) repository.

**Warning**: This repository is under active development. Things will be constantly changing and breaking.

## Media
- [High-level Overview of bch-dex](https://youtu.be/LVX8CLi4sHw) (Video)
- [Developer walk-through for installing and hacking on bch-dex](https://youtu.be/T5XI43-SWJo) (Video)

## Participate
This is an open source project, and we encourage other JavaScript developers to participate in its creation and maintenance. We have two chat rooms for the community:
- [Telegram Channel](https://t.me/psf_dex_dev)
- [Gitter Channel](https://gitter.im/Permissionless-Software-Foundation/psf-dex-dev)

## Installation
Running the DEX requires composition of these different software packages:
- bch-dex - This repository is the back end software that tracks trade data on the network, generates [Offers and Counter Offers](https://github.com/Permissionless-Software-Foundation/bch-dex/tree/ct-unstable/dev-docs#definitions), and finalizes trades by accepting Counter Offers.
- [bch-dex-ui](https://github.com/Permissionless-Software-Foundation/bch-dex-ui) is a [Gatsby](https://www.gatsbyjs.com/) web app and user interface (UI) for bch-dex.
- [P2WDB](https://github.com/Permissionless-Software-Foundation/ipfs-p2wdb-service) is a censorship-resistant database used to communicate trade data between peers running bch-dex.
- [IPFS](https://ipfs.io/) is a censorship-resistant network for communicating data over the internet.
- [MongoDB](https://www.mongodb.com/) is a database used by both P2WDB and bch-dex to store and manage local data.

The above software is orchestrated using [Docker](https://www.docker.com/) and Docker Compose. The target operating system is Ubuntu 20+, and the target hardware is amd64 (normal desktop PCs) and the arm64 (Raspberry Pi 4). Trying to operate this software on other operating systems or hardware is possible, but not supported.

Instructions for setting up Node.js, Docker, and Docker Compose can be found in [this Gist](https://gist.github.com/christroutner/a39f656850dc022b60f25c9663dd1cdd). Walk-through videos can also be found on the [PSF Videos page](https://psfoundation.cash/video/).

Building the Docker containers and getting the app working involve these steps:
<ol>
<li>Follow the direction in [this Gist](https://gist.github.com/christroutner/a39f656850dc022b60f25c9663dd1cdd) to install Node.js, Docker, and Docker Compose.</li>
<li>Clone the repository with `git clone https://github.com/Permissionless-Software-Foundation/bch-dex` and enter it with `cd bch-dex`.</li>
<li>Install dependencies by running `npm install`</li>
<li>Create a wallet by executing the [create-wallet.js script](./production/scripts/create-wallet.js) with `node create-wallet.js`</li>
<li>Move the `wallet.json` file to the `bch-dex` folder in either the [x86 Docker folder](./production/docker/bch-dex) or the [RPi Docker folder](./production/rpi-docker/bch-dex/) depending on your hardware target.</li>
<li>Change directory to the `docker` or `rpi-docker` folder depending on your hardware target.</li>
<li>Build the Docker containers with `docker-compose build --no-cache`.</li>
<li>Start the Docker containers with `docker-compose up -d`</li>
<li>Wait for the containers to run for about a minute, then bring them down with `docker-compose down`. That will create the needed folders in `production/data`.</li>
<li>Copy the `swarm.key` into the `production/data/go-ipfs/data/` directory. This will allow the IPFS node to connect to the PSF private network.</li>
<li>Bring the containers back up with `docker-compose up -d`.</li>
<li>Wait for the P2WDB to sync and populate bch-dex with trade data. You can monitor it with `docker logs --tail 20 -f p2wdb`.</li>
<li>Open a web browser and navigate the `http://localhost:4500`. You'll be able to see new Offers as they come in and are detected by bch-dex.</li>
<li>To take the other side of the trade, click the `Take` button in the UI.</li>
<li>You can add the 12-word mnemonic from the `wallet.json` file to the the web wallet, which will mirror your wallet in the UI, and allow you to perform basic wallet functions (send and receive BCH and tokens).</li>
</ol>
  
### Applying Software Updates
As this is an active project, software updates will happen frequently. To apply a software update, perform these steps.

<ol>
  <li>Enter the `docker` or `rpi-docker` folder, depending on your hardware target.</li>
<li>Bring down the Docker containers with `docker-compose down`.</li>
<li>Pull in the software updates with `git pull`</li>
<li>Update dependencies with `npm install`</li>
<li>Rebuild the Docker containers with `docker-compose build --no-cache`</li>
<li>Clean up disk space by deleting old Docker images with `./cleanup-images.sh`</li>
<li>Start the Docker containers with `docker-compose up -d`</li>
</ol>
  
Sometimes it may be necessary to delete the databases before applying a software update. This can be done by stopping the Docker containers and deleting the `production/data` directory. When the Docker containers are restarted, they will recreate that directory. The P2WDB will re-sync and bch-dex will be populated with fresh trade data.

## License

[MIT](./LICENSE.md)
