### 用户自己领取

1. 调用 userFLashCLiamRegisteryV3 createReceiver() 方法 部署用户的空投receiver 合约,

2. 部署后的receiver 可以通过后端接口查询到

3. 获取到一种NFT 的bNFT 地址, bNFT的合约地址, 和用的当前持有此BNFT 的数量

4. 调用bNFT的flashLoan   `function flashLoan( address receiverAddress, uint256[] calldata nftTokenIds, bytes calldata params )`
   * 使用abi.encode 来构造参数params encode 的格式
       ```typescript    
      const receiverEncodedData = ethers.utils.defaultAbiCoder.encode(
         ["uint256[]", "address[]", "uint256[]", "address", "bytes"],
         [
           [1, 2, 3],
           [mockAirdropERC20Address, mockAirdropERC721Address, mockAirdropERC1155Address],
           [0, 0, nftTokenId],
           mockAirdropContract.address,
           applyAirdropEncodedData,
         ]
       );```

  * 参数uint256[]  airdrop Token Types [1 -> erc20 , 2 ->  ERC721 with Enumerate, 3-> ERC1155, 4 -> ERC721 without Enumerate  ]
  * 参数address[] 具体的token address
  * uint256[] 只有 erc1155(3) 和 ERC721 without Enumerate(4)类型的NFT 需要指定自己的tokenId,
  * address 空投合约地址
  * bytes 调用空投合约的方法+ 参数进行encode 





----



### 项目分发放空投

AirDropDistribution 
[项目方发放空投到AirDrop合约中](https://etherscan.io/token/0x1e52f7a450b08b1b249a4f4f54518fc5278c2285?a=0xd46c8648f2ac4ce1a1aace620460fbd24f640853)， 此时某种NFT的所有id 对应的owner 进行快照记录

此时管理员将记录上传到链上存储，用于用户claim时候的校验

```solidity
 function configureNftUserTokenIds(
    uint256 airdropId,
    address[] calldata nftUsers,
    uint256[] calldata nftTokenIds
  ) public onlyOwner
```



此时用户调用 claimERC1155 便可以从AirDropDistribution 获取自己的空投的token

```
   function claimERC721(uint256 airdropId) public 
 function claimERC1155(uint256 airdropId) public  
```





---



### 空投快照

在某个时间进行快照，此时池中的nft的owner属于bNFT 的合约地址。

此时需要claimOwner 来执行此BNFT 合约的 `function executeAirdrop(address airdropContract, bytes calldata airdropParams)` 进行空投的领取，

之后再将 claimAdmin 再将对应类型的airdrop进行transfer

```solidity
 function claimERC20Airdrop(
    address token,
    address to,
    uint256 amount
  ) external override nonReentrant onlyClaimAdmin
  
   function claimERC721Airdrop(
    address token,
    address to,
    uint256[] calldata ids
  ) external override nonReentrant onlyClaimAdmin 
  
function claimERC1155Airdrop(
    address token,
    address to,
    uint256[] calldata ids,
    uint256[] calldata amounts,
    bytes calldata data
  ) external override nonReentrant onlyClaimAdmin {
  
  
```

