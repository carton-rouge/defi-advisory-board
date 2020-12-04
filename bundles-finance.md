# Bundles Finance

- Date: 04.12.2020 | 15.00 (CET)
- Web-app: https://bundles.finance/

### BUND token

- 9,970 BUND locked for 1 year (until 25.10.2021) [see tx](https://etherscan.io/tx/0x1c98a1c8cae3e53d4cdadef643c8aae67563421b7026ff262036ad8cb40e6ea8)
- Owner has 9,515 BUND (~9.5% ; ~$60K) [see here](https://etherscan.io/token/0x8D3E855f3f55109D473735aB76F753218400fe96?a=0xc86780efee90943da056b031464f9358133f8d1f)
- 1,998 BUND locked in [0x44ef4b1dc8f566350f3cCD5C83c21743151fb98D](https://etherscan.io/address/0x44ef4b1dc8f566350f3ccd5c83c21743151fb98d) ; this is an **unverfied** contract (on Etherscan), looks like a staking reward contract.

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

#### Bets

After a user placed a bet (`function PlaceBet(uint256 index,uint256 _prices,uint256 _percent,uint256 _bundleId,uint256 _amount) public returns(bool)`),
only the `owner` (an EOA) can updated the result of the bet, choosing all the parameters (whether it's positive, the reward rate, etc.)

```solidity
function updatebal(address _user,uint256 _bundleId,uint256 _reward,bool _isPositive) public returns(bool){
    require(msg.sender == owner,'Not Owner');
    require(_reward <= 12000000,'Invalid Reward Percent');
    User storage us = user[_user];
    require(us.active == true,'Invalid User');
    UserBets storage u = bets[_user][_bundleId];
    require(u.claimed == false,'Already Claimed');
    uint256 a = SafeMath.mul(u.totalbet,_reward);
    uint256 b = SafeMath.div(a,10**8);
    if(_isPositive == true){
        uint256 c = SafeMath.add(u.totalbet,b);
        u.claimed = true;
        us.freebal = SafeMath.add(c,us.freebal);
        us.balance = SafeMath.sub(us.balance,u.totalbet);
    }
    else{
        uint256 c = SafeMath.sub(u.totalbet,b);
        u.claimed = true;
        us.freebal = SafeMath.add(c,us.freebal);
        us.balance = SafeMath.sub(us.balance,u.totalbet);
    }
    return true;
}
```
