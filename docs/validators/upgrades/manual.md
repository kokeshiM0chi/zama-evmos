
<!--
order: 3
-->

# Manual Upgrades

## Upgrade Genesis File

:::warning
If the new version you are upgrading to has breaking changes, you will have to [export](#export-state) the state  and [restart](#restart-node) your node.

If it is **not** breaking (eg. from `v0.1.x` to `v0.1.<x+1>`), you can skip to [Restart](#restart-node) after installing the new version.
:::

To upgrade the genesis file, you can either fetch it from a trusted source or export it locally using the `evmosd export` command.

## Fetch from a Trusted Source

If you are joining an existing testnet, you can fetch the genesis from the appropriate testnet source/repository where the genesis file is hosted.

Save the new genesis as `new_genesis.json`. Then, replace the old `genesis.json` with `new_genesis.json`.

```bash
cd $HOME/.evmosd/config
cp -f genesis.json new_genesis.json
mv new_genesis.json genesis.json
```

## Export State

Evmos can dump the entire application state to a JSON file. This, besides upgrades, can be
useful for manual analysis of the state at a given height.

Export state with:

```bash
evmosd export > new_genesis.json
```

You can also export state from a particular height (at the end of processing the block of that height):

```bash
evmosd export --height [height] > new_genesis.json
```

If you plan to start a new network for 0 height (i.e genesis) from the exported state, export with the `--for-zero-height` flag:

```bash
evmosd export --height [height] --for-zero-height > new_genesis.json
```

Then, replace the old `genesis.json` with `new_genesis.json`.

```bash
cp -f genesis.json new_genesis.json
mv new_genesis.json genesis.json
```

At this point, you might want to run a script to update the exported genesis into a genesis state that is compatible with your new version.

You can use the `migrate` command to migrate from a given version to the next one (eg: `v0.X.X` to `v1.X.X`):

```bash
evmosd migrate [target-version] [/path/to/genesis.json] --chain-id=<new_chain_id> --genesis-time=<yyyy-mm-ddThh:mm:ssZ>
```

## Restart Node

To restart your node once the new genesis has been updated, use the `start` command:

```bash
evmosd start
```

## Updating the `evmosd` binary

These instructions are for full nodes that have ran on previous versions of and would like to upgrade to the latest testnet.

First, stop your instance of `evmosd`. Next, upgrade the software:

```bash
cd evmos
git fetch --all && git checkout <new_version>
make install
```

::: tip
If you have issues at this step, please check that you have the latest stable version of GO installed.
:::

You will need to ensure that the version installed matches the one needed for th testnet. Check the Evmos [release page](https://github.com/tharsis/evmos/releases) for details on each release.

Verify that everything is OK. If you get something like the following, you've successfully installed Evmos on your system.

```bash
$ evmosd version --long

name: evmos
server_name: evmosd
version: 0.4.0
commit: 070b668f2cbbf52548c46e96b236e09884483dd4
build_tags: netgo,ledger
go: go version go1.17 darwin/amd64
...
```

If the software version does not match, then please check your $PATH to ensure the correct evmosd is running.