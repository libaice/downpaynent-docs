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