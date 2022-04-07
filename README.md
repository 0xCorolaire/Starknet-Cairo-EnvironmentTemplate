# Starknet-Cairo-EnvironmentTemplate
Setup your Cairo Environment for Smart Contract development on Starknet
Follow the steps to initialize your Cairo project environment

----

### 1. **Creating an account contract & Organizing**

⇒ Setup Argent Account

- The easiest way to set one up is currently to use Argent X ([download the chrome extension](https://chrome.google.com/webstore/detail/argent-x-starknet-wallet/dlcobpjiigpikoobohmabehhmhfoodbb/) or [check their repo](https://github.com/argentlabs/argent-x)).
- Deploy Contract to Goeli

![image](https://user-images.githubusercontent.com/30114255/162275586-71580bc6-a48c-4d32-a3bb-6467ddca2d4b.png)

- Get Faucet ETH Token : [https://faucet.goerli.starknet.io/](https://faucet.goerli.starknet.io/)
- Add StarkNet Contract for Henri Tutorial : `0x074002c7df47096f490a1a89b086b8a468f2e7c686e04a024d93b7c59f934f83`

![image](https://user-images.githubusercontent.com/30114255/162275608-d000fd11-d799-43c0-9497-38f795f00204.png)

## 2. Dependencies installation

Req : Python 3.7

```bash
sudo apt install python-dev python3-dev gcc virtualenv python3-virtualenv python3-venv

python3 -m venv ~/cairo_venv
source ~/cairo_venv/bin/activate
pip install --upgrade pip
sudo apt install -y libgmp3-dev
pip3 install cairo-lang
pip3 install cairo-nile
#pip3 install contextvars if using Python 3.6
```

## 3. Test your Environment

create test.cairo :

```
func main():
    [ap] = 1000; ap++
    [ap] = 2000; ap++
    [ap] = [ap - 2] + [ap - 1]; ap++
    ret
end
```

Compile contract and output :

```bash
cairo-compile test.cairo --output test_compiled.json
cairo-run \
  --program=test_compiled.json --print_output \
  --print_info --relocate_prints --tracer
```

## 4. Hello World Starknet Contract

Create a contracts folder, and inside of it, a helloworld.cairo file : 

```
# Declare this file as a StarkNet contract.
%lang starknet

from starkware.cairo.common.cairo_builtins import HashBuiltin

# Define a storage variable.
@storage_var
func balance() -> (res : felt):
end

# Increases the balance by the given amount.
@external
func increase_balance{
        syscall_ptr : felt*, pedersen_ptr : HashBuiltin*,
        range_check_ptr}(amount : felt):
    let (res) = balance.read()
    balance.write(res + amount)
    return ()
end

# Returns the current balance.
@view
func get_balance{
        syscall_ptr : felt*, pedersen_ptr : HashBuiltin*,
        range_check_ptr}() -> (res : felt):
    let (res) = balance.read()
    return (res)
end
```

Then save it and Compile your first contract ! 

```
cd contracts
starknet-compile helloworld.cairo \
    --output helloworldcontract_compiled.json \
    --abi helloworldcontract_abi.json
```

You’ve succed, you have now the abi and compiled file ready to be pushed to network !

Add the network you want to use to the environment variable : 

```bash
export STARKNET_NETWORK=alpha-goerli
```

Deploy your first smart contract !

```bash
starknet deploy --contract helloworldcontract_compiled.json
```

Gj sir ! 

![image](https://user-images.githubusercontent.com/30114255/162275664-dc1b8d19-5b26-4c40-9d55-5e5b460a7766.png)

## 5. More with the cli (it’s just a plus)

```bash
export CONTRACT_ADDRESS="<address of the previous contract>"
```

- You can now interact with the functions of your Smart Contract :

```
starknet invoke \
    --address ${CONTRACT_ADDRESS} \
    --abi helloworldcontract_abi.json \
    --function increase_balance \
    --inputs 1234
```

- Check TX status

```bash
starknet tx_status --hash TXHASH
```

- Check TX results

```bash
starknet get_transaction --hash TXHASH
starknet get_transaction_receipt --hash TXHASH
starknet get_transaction_trace --hash TXHASH# Check emmited events
```

## 6. Deploy and manipulate your smart contract

Exists 3 solutions : the CLI we’ve seen right before,

- **nile** (python) https://github.com/OpenZeppelin/nile → Very easy (best in my opinion)
- **hardhat** (js)
