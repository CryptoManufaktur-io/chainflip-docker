# Chainflip Docker

Chainflip node in Docker Compose

## Getting started

`./flipd install` can be used to install docker-ce and Docker Compose, if they aren't already installed

`cp default.env .env` and adjust variables such as the version tags, `HOST_IP` and `ETH_RPC` as well as `ETH_WS` URLs,
as well as the `ARB_RPC` and `ARB_WS` variables. For `SOL_RPC`, use an RPC provider.

## Generate keys

Read this alongside the official [Chainflip docs](https://docs.chainflip.io/perseverance/validator-setup/keys)

Make sure the `keys` directory is owned by uid `1000`. This will already be the default if you have
just one user in Debian/Ubuntu: `sudo chown 1000:1000 ./keys`

Generate the keys with `./flipd cmd run --rm cli generate-keys --path /etc/chainflip/keys`.

**Make sure to back up your Seed Phrase and make a note of the public keys and account ID. You will need the Seed
Phrase if you ever need to restore your node or recover your funds if you lose access to the node. DO NOT LOSE THIS.**

Back up the `./keys` directory, as another failsafe alongside the mnemonic.

Take special note of the Validator Account ID beginning with cF. This is the ID that you will need to add funds and
track your node.

> NEVER REVEAL YOUR PRIVATE KEYS TO ANYONE.

## Sync node

Start the Chainflip node with `./flipd up`. Check logs of the node with `./flipd logs -f node`. The `engine` service
will print "wait" messages until `node` is synced.

## Funding and bidding

Read this alongside the official [Chainflip docs](https://docs.chainflip.io/perseverance/funding/funding-and-bidding).

Make sure `node` is synced before proceeding.

Stake tFLIP to the node exactly as described in the official docs.

Restart the engine: `./flipd restart engine`

Register your account for the validator role: `./flipd cmd run --rm cli register-account-role Validator`

As long as you saw a successful tx, next rotate: `./flipd cmd run --rm cli rotate`

Check logs of node and engine, make sure you're synced and everything is working well, check status is green in the web
app, then start bidding: `./flipd cmd run --rm cli start-bidding`

Optionally, set a vanity name: `./flipd cmd run --rm cli vanity-name <my-discord-username>`

## Get metrics

You can either add `:grafana-cloud.yml` to `COMPOSE_FILE` in `.env`, and send metrics to Grafana Cloud or, indeed,
your own Mimir or Thanos. Adjust `prometheus/custom-prom.yml` with the desired remote write.

Or you can run [central-proxy-docker](https://github.com/CryptoManufaktur-io/central-proxy-docker) in a directory
such as `traefik`, configure it for remote write with its `prometheus.yml`, and also `promtail.yml` as desired,
and have the chainflip metrics be scraped centrally. Use `ext-network.yml` to connect the chainflip stack to the
central traefik/prometheus/promtail stack.

## Updating Chainflip

If you are using specific version tags for node and engine, instead of latest, `nano .env` and set the desired new
version tags.

`./flipd update` to pull in the new versions.

`./flipd up` to restart node and engine on the new versions.

## Restoring from backup

After you restore the `keys` directory from backup and sync the node and engine again, run
`./flipd cmd run --rm cli rotate`. This is necessary after restore.

This is Chainflip Docker v4.1.0
