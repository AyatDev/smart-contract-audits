\# Reentrancy Vulnerability Audit Report



\## Severity: Critical

\## Status: Fixed



\## Description

A reentrancy vulnerability allows an attacker to repeatedly call a function

before the first execution is complete, draining contract funds.



\## Vulnerable Code

```solidity

function withdraw(uint amount) public {

&#x20;   require(balances\[msg.sender] >= amount);

&#x20;   (bool success, ) = msg.sender.call{value: amount}("");

&#x20;   require(success);

&#x20;   balances\[msg.sender] -= amount; // state updated AFTER external call

}

```



\## Attack Scenario

1\. Attacker deploys malicious contract

2\. Calls withdraw()

3\. Fallback function re-enters withdraw() before balance updates

4\. Repeats until contract is drained



\## Fixed Code

```solidity

function withdraw(uint amount) public {

&#x20;   require(balances\[msg.sender] >= amount);

&#x20;   balances\[msg.sender] -= amount; // state updated BEFORE external call

&#x20;   (bool success, ) = msg.sender.call{value: amount}("");

&#x20;   require(success);

}

```



\## Recommendation

Always follow Checks-Effects-Interactions pattern.

Use ReentrancyGuard from OpenZeppelin.

