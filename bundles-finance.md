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
