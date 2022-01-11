# juno_poc
Proof Of Concept for deploying, instantiating, executing and querying smart contracts on the Juno blockchain.
We are launching on the latest testnet of Juno Moneta (moneta-alpha v2.0.0-alpha.3)

## Smart Contract
We use a very basic smart contract, the CW-template offered by Cosmwasm (1.0.0-beta) that allows for storing and reading count from a state.

```cargo generate --git https://github.com/CosmWasm/cw-template.git --name juno_stkd_poc ``` (https://github.com/InterWasm/cw-template)

`cd juno_stake_poc` 
`cargo clean`
`cargo build`
`cargo test`

To produce an efficient stripped wasm file to upload to Juno    
```
docker run --rm -v "$(pwd)":/code \
  --mount type=volume,source="$(basename "$(pwd)")_cache",target=/code/target \
  --mount type=volume,source=registry_cache,target=/usr/local/cargo/registry \
  cosmwasm/rust-optimizer:0.12.4
  ```

`junod tx wasm store juno_stkd_poc.wasm --from juno_test --gas-prices 0.025ujunox --gas-adjustment 1.4 -b block --gas auto`
In my case, I stored the contract with code-id: 22.

### Instantiation
We can instantiate a smart contract on testnet using Junod (juno build) using
```
junod tx wasm instantiate 22 \
'{"count": 32}' \
--label "juno_testing" --from juno_test --gas-prices 0.025ujunox --gas-adjustment 1.4 -b block --gas auto
```
This sets the initial count of the smart contract at 32
We get a testnet smart contract address: `juno13wxll57yp7l6ansarjtgqg79gnv83wacl5pfsypj82femvrcszrq32wv3t`

Now we can execute and query messages

### Execute Messages
```
junod tx wasm execute "juno13wxll57yp7l6ansarjtgqg79gnv83wacl5pfsypj82femvrcszrq32wv3t" \
'{"increment": {}}' \
 --from juno_test --gas-prices 0.025ujunox --gas-adjustment 1.4 -b block --gas auto
 ```
This increments the count in the state of the smart contract by 1
 ### Query Messages 
 To query the count in the state of the smart contract we can query from the terminal
 junod query wasm contract-state smart juno13wxll57yp7l6ansarjtgqg79gnv83wacl5pfsypj82femvrcszrq32wv3t '{"get_count":{}}'

 we get count: 33