<div style="text-align:center">
  <img
  src="https://raw.githubusercontent.com/multiversx/mx-chain-sovereign-go/master/multiversx-logo.svg"
  alt="MultiversX">
</div>
<br>

<br>

[![](https://img.shields.io/badge/made%20by-MultiversX-blue.svg?style=flat-square)](http://multiversx.com/)
[![](https://img.shields.io/badge/project-MultiversX%20Mainnet-blue.svg?style=flat-square)](http://multiversx.com/)

# mx-chain config for sovereign

MultiversX sovereign configuration files used in conjunction with mx-chain-sovereign-go project.
Below are some example on how to run it on MVX mainnet. Please change them according to your specific chain.
For more info how to connect to the another chain, similar to how a node connects to the MVX main chain,
please check [here](https://docs.multiversx.com/validators/nodes-scripts/config-scripts/)

## run an sovereign MultiversX observer/validator with docker

### build docker image

```docker image build . -t chain-sovereign-local -f ./docker/Dockerfile```

### run node with docker

```
CONFIG_FOLDER=path/to/folder/with/pem/file
docker run --mount type=bind,source=${CONFIG_FOLDER}/,destination=/data chain-sovereign-local --validator-key-pem-file="/data/validatorKey.pem" --log-level *:DEBUG