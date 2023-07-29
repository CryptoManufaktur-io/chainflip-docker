# chainflip-docker

Chainflip node in docker compose

## Getting started

`./flipd install` can be used to install docker-ce and docker compose, if they aren't already installed

`cp default.env .env` and adjust variables such as the version tags

`cp config/Settings.toml.sample config/Settings.toml` and then `nano config/Settings.toml`. Adjust the `ip_address`, setting it to the public address of the host, and the `ws_node_endpoint` and `http_node_endpoint` URLs for your Ethereum node

## Generate keys

Read this alongside the official [Chainflip docs](https://docs.chainflip.io/perseverance-validator-documentation/validator-setup/keys)

Generate the Ethereum key exactly as described in the official docs and store it in `keys/ethereum_key_file`
 
To generate the signing key, use `docker run --rm chainfliplabs/chainflip-node:0.8.7 chainflip-node key generate`, adjusting the version tag as desired. Set `SECRET_SEED` as described in the official docs, then store it in `keys/signing_key_file` like so: `echo -n "${SECRET_SEED:2}" | tee ./keys/signing_key_file`

Finally to generate the node key, use `docker run --rm -v "$(pwd)"/keys:/etc/chainflip/keys chainfliplabs/chainflip-node:0.8.7 chainflip-node key generate-node-key --file /etc/chainflip/keys/node_key_file`, adjusting the version tag as desired.

Back up the contents of these files, as they cannot be recovered if they are lost.

Set permissions and ownership and clean up after yourself:
- `sudo chown 1000:1000 ./keys/*key*`
- `sudo chmod 600 ./keys/*key*`
- `history -c`

## Sync node

`./flipd up` or `docker compose up -d`. Check logs of the node with `./flipd logs -f node`. The `engine` service will fail until `node` is synced.
