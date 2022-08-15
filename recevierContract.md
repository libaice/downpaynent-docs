

1. 用户部署 reciverContract,

   ```
    function registerProxy() external returns (address);



```json
{
      "inputs": [],
      "name": "registerProxy",
      "outputs": [
        {
          "internalType": "address",
          "name": "",
          "type": "address"
        }
      ],
      "stateMutability": "nonpayable",
      "type": "function"
    }
```



2. 查询用户部署的合约 / 是否部署过

   ```
    function proxies(address owner) external view returns (address);
   ```

   

传入用户地址, 如果返回0x0000 地址， 没有部署过