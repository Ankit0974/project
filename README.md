pragma solidity ^0.8.0;

contract DynamicPricingToken {
    mapping(address => uint256) public balances;
    uint256 public totalSupply;
    uint256 public basePrice = 1 ether;

    function getPrice(uint256 amount) public view returns (uint256) {
        return basePrice + (amount / 1000) * 0.01 ether;
    }

    function buyTokens() external payable {
        require(msg.value > 0, "No funds sent");
        uint256 amount = msg.value / getPrice(msg.value);
        balances[msg.sender] += amount;
        totalSupply += amount;
    }

    function sellTokens(uint256 amount) external {
        require(balances[msg.sender] >= amount, "Insufficient balance");
        uint256 refund = amount * getPrice(amount);
        balances[msg.sender] -= amount;
        totalSupply -= amount;
        payable(msg.sender).transfer(refund);
    }
}
