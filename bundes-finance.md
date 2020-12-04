# Bundles Finance

- Date: 04.12.202 | 15.00
- Web-app: https://bundles.finance/

### Bundles contract

- Deployed at [0x619b26D4a3df18C5e0EC37a70F1359774972959F](https://etherscan.io/address/0x619b26D4a3df18C5e0EC37a70F1359774972959F#code)
- Owner set to [0x486745875beea5f53a92852beabcfd6d400613f0](https://etherscan.io/address/0x486745875beea5f53a92852beabcfd6d400613f0) (an EOA)

#### Drain

There is a `drain` method in the contract, which only the owner can call, and sends all BUND tokens (~$2.3K) to the owner account
```solidity
    function drain() public returns(bool,uint256){
        require(msg.sender == owner,'Not Owner');
        uint256 amount = bundle_address.balanceOf(address(this));
        bundle_address.transfer(msg.sender,amount);
        return(true,amount);
    }
```
