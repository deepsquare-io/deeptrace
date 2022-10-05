<h1 align="center">Deeptrace</h1>
<p align="center">Blockchain Explorer for inspecting and analyzing avalanche subnet EVM Chains.</p>
<div align="center">

[![Blockscout](https://github.com/blockscout/blockscout/workflows/Blockscout/badge.svg?branch=master)](https://github.com/blockscout/blockscout/actions)
[![](https://dcbadge.vercel.app/api/server/blockscout?style=flat)](https://discord.gg/blockscout)

</div>

This is a fork of Blockscout. To make this work with Avalanche subnet read the Avalanche subnet section. 

BlockScout provides a comprehensive, easy-to-use interface for users to view, confirm, and inspect transactions on EVM (Ethereum Virtual Machine) blockchains. This includes the POA Network, Gnosis Chain, Ethereum Classic and other **Ethereum testnets, private networks and sidechains**.

See our [project documentation](https://docs.blockscout.com/) for detailed information and setup instructions.

For questions, comments and feature requests see the [discussions section](https://github.com/blockscout/blockscout/discussions).

## About BlockScout

BlockScout is an Elixir application that allows users to search transactions, view accounts and balances, and verify smart contracts on the Ethereum network including all forks and sidechains.

Currently available full-featured block explorers (Etherscan, Etherchain, Blockchair) are closed systems which are not independently verifiable.  As Ethereum sidechains continue to proliferate in both private and public settings, transparent, open-source tools are needed to analyze and validate transactions.

## Avalanche subnet 

### Test blockscout on local avalanche subnet deployment

1. Create a subnet `avalanche subnet create Deepji` 
2. Deploy the subnet `avalanche subnet deploy Deepji`. Get the rpc endpoints, e.g. `http://0.0.0.0:9650/ext/bc/UABeZkZb1rvutmWVquC2HqCSFoma9C5B4Zhb9ZBayuzj9MBng/rpc` and keep the chain id `UABeZkZb1rvutmWVquC2HqCSFoma9C5B4Zhb9ZBayuzj9MBng` we will need it later. 
3. Stop the local network `avalanche network stop` 
4. Now that we know our chain id, let's create a specific config on `node5` for the subnet, this node will play the role of an archive node. To do so edit the `network.json` file in the `~/.avalanche-cli/snapshots/anr-snapshot-default-xxxxxxxxxx/network.json` folder to add a new entry in the `"chainConfigFiles"` section by copying the default one of the `C` chain and adding the following flags to the `eth-apis` array: `"internal-public-tx-pool", "debug-tracer"`
5. Restart the network again with `avalanche network start` 
6. Check that one of the node has the correct config written in the chain-config-dir because there is now a new folder in the `chainConfigs` folder of the updated node: `~/.avalanche-cli/runs/restart_yyyyyyyy_xxxxxx/network-runner-root-data_yyyyyyyy_xxxxxx/node5/chainConfigs/UABeZkZb1rvutmWVquC2HqCSFoma9C5B4Zhb9ZBayuzj9MBng/config.json`
7. Check that `debug_traceTransaction` is enabled:  
```
export BLOCKCHAIN_ID=YOUR_BLOCKCHAIN_ID
curl http://0.0.0.0:9658/ext/bc/${BLOCKCHAIN_ID}/rpc \
  -X POST \     
  -H "Content-Type: application/json" \
  --data '{"method":"debug_traceTransaction","params":["0x9e63085271890a141297039b3b711913699f1ee4db1acb667ad7ce304772036b"],"id":1,"jsonrpc":"2.0"}'
{"jsonrpc":"2.0","id":1,"error":{"code":-32601,"message":"the method debug_traceTransaction does not exist/is not available"}}
```

If this work we can now start blockscout 


1. Go in the docker-compose folder
2. Update the `envs/common-blockscout.env` with 
```
ETHEREUM_JSONRPC_HTTP_URL=http://172.17.0.1:9658/ext/bc/UABeZkZb1rvutmWVquC2HqCSFoma9C5B4Zhb9ZBayuzj9MBng/rpc
ETHEREUM_JSONRPC_TRACE_URL=http://172.17.0.1:9658/ext/bc/UABeZkZb1rvutmWVquC2HqCSFoma9C5B4Zhb9ZBayuzj9MBng/rpc
```
3. Run `docker-compose  -f docker-compose-no-build-avalanchego.yml up -d`
4. Open `http://localhost:4000`




> note1 : debug_traceTransaction call with JavaScript-based tracer returns "tracer not found" error : https://github.com/ava-labs/avalanchego/issues/1121

> note2 : in order to run properly Blockscout needs to have the `INDEXER_DISABLE_PENDING_TRANSACTIONS_FETCHER=false` in the blockscout config : https://github.com/blockscout/blockscout/issues/5729

> note3 : to adapt the look and feel of blockscout refer to [this link](https://docs.blockscout.com/for-developers/configuration-options/branding-configs)

## Supported Projects

BlockScout supports a number of projects. Hosted instances include POA Network, Gnosis Chain, Ethereum Classic, Sokol & Kovan testnets, and other EVM chains.

- [List of hosted mainnets, testnets, and additional chains using BlockScout](https://docs.blockscout.com/for-projects/supported-projects)
- [Hosted instance versions](https://docs.blockscout.com/about/use-cases/hosted-blockscout)

## Getting Started

See the [project documentation](https://docs.blockscout.com/) for instructions:

- [Requirements](https://docs.blockscout.com/for-developers/information-and-settings/requirements)
- [Ansible deployment](https://docs.blockscout.com/for-developers/ansible-deployment)
- [Manual deployment](https://docs.blockscout.com/for-developers/manual-deployment)
- [ENV variables](https://docs.blockscout.com/for-developers/information-and-settings/env-variables)
- [Configuration options](https://docs.blockscout.com/for-developers/configuration-options)

## Acknowledgements

We would like to thank the [EthPrize foundation](http://ethprize.io/) for their funding support.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for contribution and pull request protocol. We expect contributors to follow our [code of conduct](CODE_OF_CONDUCT.md) when submitting code or comments.

## License

[![License: GPL v3.0](https://img.shields.io/badge/License-GPL%20v3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)

This project is licensed under the GNU General Public License v3.0. See the [LICENSE](LICENSE) file for details.
