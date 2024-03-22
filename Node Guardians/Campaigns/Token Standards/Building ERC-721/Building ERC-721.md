- ERC-721 token are a lot like ERC-20 token. They are ruled by a smart contract that handle the ownership of every toke,, transfer of them, minting....
- But the main difference is that ERC-721 are non-Fongible which means that each token is unique. This particular feature makes it particulary suitable to representation of unique art such as artistique nfts. But also as proof of identity.
- Just like the ERC-20 there is a standard that allow dapp to interact with any kind of ERC721 token.

## Implementing an ERC-721 token
#### ERC-721 Metadata
- The ERC-721 metadata standard is an optional standard. It allows entity to interogate the contract for its name and basic details of each nft. In addition of this metadata you can found the next view functions:
	- `name() -> string`
	- `symbol() -> string`
	- `tokenUri(uint256 _tokenId) -> string`
- Each nfts in the collection has a unique id. It is convinient, though not required to have metadata associated to each nft. These metadata can be stored off-chain reducing gas-cost and allowing references to external ressources such as images.
	- When you store the data on-chain it is called `inscription'.
- The `tokenUri`function return a string such as an url or file path which point to the metadata of the token associated to his id. If the id doesn't exist, then the function must reverse.
- The standard suggest that the metadata are formatted as a json.
```
{ 
	"name": "Sheepshead Amulet", 
	"description": "Amulet crafted from a sheep's head. Useful for protection against fish amulets.", 
	"spell": "BAAABAAABAAA", 
	"image": ... 
}
```

#### Token Ownership
- Each NFT in a collection is identified by a unique ID and owned by a single address at any given time. The ERC-721 standard defines two functions regarding ownership:

![[FunctionOwnerOfBalanceOfERC721.png]]

#### Minting
- For any ERC-721 token, there must be a way to mint token. Althought the way itself is not defined by the ERC-721 standard.
- The standard also not defined how the IDs are to be given.

#### ERC-165 Standard
- This standard allow other parties to check the contract support given interface.
- A function is had to verify the supported interface. This function compare the provided bytes to the interface ids of ERC-165, ERC-721, and ERC-721 Metadata - returning true only if `interfaceId` matches any of these interface IDs.
```solidity
function supportsInterface(bytes4 interfaceId) external pure returns (bool) {
    return (interfaceId == type(IERC165).interfaceId ||
        interfaceId == type(IERC721).interfaceId ||
        interfaceId == type(IERC721Metadata).interfaceId);
}
```

#### View function
- One main difference between the ERC-20 standard is that `balanceOf`and `ownerOf` must revert if supplie with the 0 address or an invalid tokenId. Therefore a private or internal variable must be set to store information like *`users balance and ownership`*, plus separate view function that can revert. Then unlike ERC-20 contract we must define the variable `balance`and `owner` then the view funcitons.
```solidity
	mapping(address => uint) balance;
	mapping(uint => address) owner;

	function balanceOf(address _owner) external view returns (uint256) {
	    require(_owner != address(0), "Invalid address!");
	    return balance[_owner];
	}

	function ownerOf(uint256 _tokenId) external view returns (address) {
	    require(_tokenId < nextId, "Invalid token!");
	    return owner[_tokenId];
	}
```
## Transfer
-  In ERC-721 token, each token can have up to one approved address at any given time. This means that if an nft is approved for transfer to a new address, the previous approval is revoke.
- Unlike ERC-20 token, ERC-721 token qre generally send one by one. Plus, the funciton `safeTransfer`ensure that the receiver if it is a contract can correctly handle that type of token avoiding that the token get lock in an incompatible contract.
- To do so, we implement the function `onERC721Received`. When called, this function must return a specifi bytes4 vallue (called a selector) to indicate if it is a compatible receiver.
```
// This evaluates to 0x150b7a02
bytes4(keccak256("onERC721Received(address,address,uint256,bytes)"))
```

```solidity
    function onERC721Received(address operator, address from, uint256 tokenId, bytes calldata data) external override returns (bytes4) {
        require(msg.sender == address(amulet));
		//logical to check things.
        return 0x150b7a02; // equal bytes4(Keccack... above)
    }
```

- `safeTransferFrom` comes in two overloaded variants: one with a parameter called `data`, and one without. Both call `onERC721Received`. The difference is one forwards any application-specific data to the receiver contract via the `data` parameter, while the other passes an empty byte-string.