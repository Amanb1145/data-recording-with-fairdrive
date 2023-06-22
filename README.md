# Data Recording with FairOS and FDP Play

An web3 Todos application built using FairOS REST API and FDP Play.

## Local Development Setup

### Prerequisites:

- `node` and `npm` must be installed
- docker must be installed
- the following ports must be free and available for FDP Play to use
    - **1634** and **1635** - for the queen bee node
    - **9545** - for the blockhain
    - **9090** - for fairOS

#### Install `fdp-play` globally
```shell
$ npm install -g @fairdatasociety/fdp-play
```

#### Spin up a local FDP development environment with fairos
```shell
$ fdp-play start --fairos
```

### Setup & Installation:

#### Clone the repo
```shell
$ git clone https://github.com/Amanb1145/data-recording-with-fairdrive.git
```

```shell
$ cd data-recording-with-fairdrive
```

### Install dependencies
```shell
$ npm install
```

### Env file setup

#### Copy `.env.example` to `.env`:

```shell
$ cp .env.example .env
```
#### Generate a Postage Batch Id:

```shell
$ curl -s -XPOST http://localhost:1635/stamps/10000000/18
```

#### Update `VITE_BEE_POSTAGE_BATCH_ID`

Update the `VITE_BEE_POSTAGE_BATCH_ID` in your `.env` file with the "batchId" returned in the previous step.

### Run application
**Requirements:** `fdp-play` must be running already! 

```shell
$ npm run dev
```

The application can be viewed at - http://localhost:5173/ 
