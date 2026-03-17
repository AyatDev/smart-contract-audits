# Reentrancy Vulnerability Audit Report

## Severity: Critical
## Status: Fixed

## Description
A reentrancy vulnerability allows an attacker to repeatedly call a function
before the first execution is complete, draining contract funds.

## Vulnerable Code

    function withdraw(uint amount) public {
        require(balances[msg.sender] >= amount);
        (bool success, ) = msg.sender.call{value: amount}("");
        require(success);
        balances[msg.sender] -= amount;
    }

## Attack Scenario
1. Attacker deploys malicious contract
2. Calls withdraw()
3. Fallback function re-enters withdraw() before balance updates
4. Repeats until contract is drained

## Fixed Code

    function withdraw(uint amount) public {
        require(balances[msg.sender] >= amount);
        balances[msg.sender] -= amount;
        (bool success, ) = msg.sender.call{value: amount}("");
        require(success);
    }

## Recommendation
Always follow Checks-Effects-Interactions pattern.
Use ReentrancyGuard from OpenZeppelin.
