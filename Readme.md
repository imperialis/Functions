
# Introduction
This write-up examines one of Solidity's low-level functions, the staticCall function, within the context of the TransparentUpgradeableProxy smart contract. A real-world use case is employed to demonstrate its purpose, usage, and i
**Protocol Name:** Aurus Token  
**Category:** RWA Tokenization  
**Smart Contract:** TransparentUpgradeableProxy  

## Function Analysis

**Function Name:** getProxyImplementation  
**Block Explorer Link:** https://etherscan.io/address/0xe4a6f23fb9e00fca037aa0ea0a6954de0a6c53bf#code  
**Function Code:**  
```solidity
function getProxyImplementation(TransparentUpgradeableProxy proxy) public view virtual returns (address) {
    // We need to manually run the static call since the getter cannot be flagged as view
    // bytes4(keccak256("implementation()")) == 0x5c60da1b
    (bool success, bytes memory returndata) = address(proxy).staticcall(hex"5c60da1b");
    require(success);
    return abi.decode(returndata, (address));
}
```
**Used Encoding/Decoding or Call Method:** staticCall

## Explanation

### Purpose
The `getProxyImplementation` function is used to retrieve the address of the current implementation contract of the proxy. This is necessary to ensure that one can interact with the present implementation without making any alterations to the current state of the data therein.

### Detailed Usage
The function uses the low-level `staticCall` method to get information from another contract without modifying the state of the calling contract. It follows the steps below:
1. **Encoding the Call:** The function uses the `keccak256` hash of the string "implementation()" to generate a function selector (`0x5c60da1b`), which is then used in the static call.
2. **Making the Static Call:** The `staticCall` method is called on the proxy contract address with the encoded function selector.
3. **Handling the Response:** The call returns a success flag and the raw returned data (`returndata`). The `abi.decode` function decodes the returned data into an address type.

This method is used because `staticCall` ensures that the call does not change the state of the target contract. This is important for view functions that should not have any state changes.

### Impact
The `staticCall` function has a significant impact on the smart contract by enabling it to query the `TransparentUpgradeableProxy` contract without making any changes to its state and without incurring gas fees. This is important for several reasons:
- **Cost Efficiency:** The function can be called multiple times without concerns about gas costs, as static calls do not consume gas for state changes.
- **Functionality:** Enhances the contract's ability to interact with other contracts in a read-only manner, which is essential for obtaining information about the current implementation of the proxy.

The `staticCall` function contributes to the smart contract’s functionality within the protocol by allowing secure and efficient queries, promoting cost-effective operations, and ensuring seamless interaction with the `TransparentUpgradeableProxy` contract.
```
