Sandbox is a shell script that allows  developers to create and configure an Algorand node using [docker](https://docs.docker.com/v17.09/engine/installation/).

```shell
sandbox commands:
  up [mainnet||testnet||betanet] [-s||--skip-snapshot]
          -> spin up the sandbox environment, uses testnet by default.
             Optionally provide -s to skip initializing with a snapshot.
  down    -> tear down the sandbox environment
  restart -> restart the sandbox
  enter   -> enter the sandbox container
  clean   -> stops and deletes containers and data directory
  test    -> runs some tests to make sure everything is working correctly

algorand commands:
  logs        -> stream algorand logs with the carpenter utility
  status      -> get node status
  goal (args) -> run goal command like 'goal node status'

tutorials:
  introduction -> learn how to get Algos on testnet and create a transaction
```