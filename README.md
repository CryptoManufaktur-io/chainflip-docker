# chainflip-docker

Chainflip node in docker compose

## Getting started

`./flipd install` can be used to install docker-ce and docker compose, if they aren't already installed

`cp default.env .env` and adjust variables such as the version tags, `HOST_IP` and `ETH_RPC` as well as `ETH_WS` URLs.

## Generate keys

Read this alongside the official [Chainflip docs](https://docs.chainflip.io/perseverance-validator-documentation/validator-setup/keys)

Generate the Ethereum key exactly as described in the official docs and store it in `keys/ethereum_key_file`
 
To generate the signing key, use `docker run --rm chainfliplabs/chainflip-node:latest chainflip-node key generate`,
adjusting the version tag as desired. Set `SECRET_SEED` as described in the official docs, then store it in
`keys/signing_key_file` like so: `echo -n "${SECRET_SEED:2}" | tee ./keys/signing_key_file`

Finally to generate the node key, use `docker run --rm -v "$(pwd)"/keys:/etc/chainflip/keys
chainfliplabs/chainflip-node:latest chainflip-node key generate-node-key --file /etc/chainflip/keys/node_key_file`,
adjusting the version tag as desired.

Back up the contents of these files, as they cannot be recovered if they are lost.

Set permissions and ownership and clean up after yourself:
- `sudo chown 1000:1000 ./keys/*key*`
- `sudo chmod 600 ./keys/*key*`
- `history -c`

## Sync node

`./flipd up` or `docker compose up -d`. Check logs of the node with `./flipd logs -f node`. The `engine` service will
print "wait" messages until `node` is synced.

## Funding and bidding

Read this alongside the official [Chainflip docs](https://docs.chainflip.io/perseverance-validator-documentation/funding/funding)

Make sure `node` is synced before proceeding.

Stake tFLIP to the node exactly as described in the official docs.

Restart the engine: `./flipd restart engine`

Register your account for the validator role: `docker compose run --rm cli register-account-role Validator`

As long as you saw a successful tx, next rotate: `docker compose run --rm cli rotate`

Check logs of node and engine, make sure you're synced and everything is working well, check status is green in the web
app, then start bidding: `docker compose run --rm cli start-bidding`

Optionally, set a vanity name: `docker compose run --rm cli vanity-name <my-discord-username>`

## Get metrics

You can either add `:grafana-cloud.yml` to `COMPOSE_FILE` in `.env`, and send metrics to Grafana Cloud or, indeed,
your own Mimir or Thanos. Adjust `prometheus/custom-prom.yml` with the desired remote write.

Or you can run [central-proxy-docker](https://github.com/CryptoManufaktur-io/central-proxy-docker) in a directory
such as `traefik`, configure it for remote write with its `prometheus.yml`, and also `promtail.yml` as desired,
and have the chainflip metrics be scraped centrally. Use `ext-network.yml` to connect the chainflip stack to the
central traefik/prometheus/promtail stack.

## Updating chainflip

If you are using a specific version tag, instead of latest, `nano .env` and set the desired new version tag.

`./flipd update` to pull the new version.

`./flipd up` to start using it

This is chainflip-docker v1.1.1
