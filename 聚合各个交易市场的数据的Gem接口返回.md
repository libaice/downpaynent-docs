NFT 聚合市场接口

Gem 聚合数据

POST getAssets

> https://gem-public-api.herokuapp.com/assets
>
> POST 请求



​	请求体，返回值中包含



slug 的选择

​	boredapeyachtclub

​	mutant-ape-yacht-club

​	azuki

​	clonex

​	meebits

​	cryptopunks



```json
{
   "filters":{
      "slug":"boredapeyachtclub"
   },
   "sort":{
      "currentEthPrice":"desc"
   },
   "fields":{
      "id":1,
      "name":1,
      "address":1,
      "collectionName":1,
      "collectionSymbol":1,
      "externalLink":1,
      "imageUrl":1,
      "smallImageUrl":1,
      "animationUrl":1,
      "standard":1,
      "market":1,
      "pendingTrxs":1,
      "currentBasePrice":1,
      "paymentToken":1,
      "marketUrl":1,
      "marketplace":1,
      "tokenId":1,
      "priceInfo":1,
      "tokenReserves":1,
      "ethReserves":1,
      "sellOrders":1,
      "startingPrice":1,
      "rarityScore":1
   },
   "offset":0,
   "limit":2,
   "markets":[
      
   ],
   "status":[
      "buy_now"
   ]
}
```



* 返回数据中包含此售卖订单的数据
  * 卖单总数量
  * 卖价	priceInfo price
  * seller address 挂单人地址
  * 图片链接
  * 挂单交易市场信息[]



卖单接口返回数据

```json
{
    "hasNext": true,
    "total": 861,
    "data": [
        {
            "_id": "6195e6a29ba05365c3224b01",
            "id": "2674",
            "name": "",
            "address": "0xbc4ca0eda7647a8ab7c2061c2e118a18a936f13d",
            "collectionName": "Bored Ape Yacht Club",
            "collectionSymbol": "BAYC",
            "externalLink": null,
            "imageUrl": "https://lh3.googleusercontent.com/qe4XRR9VdrjcyFNU3B16T5mRl1o9rds5m8TPvjbYUjsHBRcPzSoc9xYRJgHGhcWXHubcVQbNUwarsQdhvF7gvuh4ZBsCv9M0w9iOsQ",
            "smallImageUrl": "https://lh3.googleusercontent.com/qe4XRR9VdrjcyFNU3B16T5mRl1o9rds5m8TPvjbYUjsHBRcPzSoc9xYRJgHGhcWXHubcVQbNUwarsQdhvF7gvuh4ZBsCv9M0w9iOsQ=s250",
            "animationUrl": null,
            "standard": "ERC721",
            "currentBasePrice": 83988000000000000000,
            "duration": null,
            "endingPrice": null,
            "ethReserves": null,
            "market": "x2y2",
            "marketUrl": "https://x2y2.io/eth/0xbc4ca0eda7647a8ab7c2061c2e118a18a936f13d/2674",
            "openseaOrderCreatedAt": null,
            "paymentToken": {
                "address": "0x0000000000000000000000000000000000000000",
                "decimals": "18",
                "symbol": "ETH"
            },
            "startingPrice": null,
            "tokenReserves": null,
            "rarityScore": 3018,
            "slug": "boredapeyachtclub",
            "orderCreatedAt": 1660023486,
            "pendingTrxs": [],
            "marketplace": "x2y2",
            "tokenId": "2674",
            "priceInfo": {
                "price": "83988000000000000000",
                "asset": "0x0000000000000000000000000000000000000000",
                "decimals": 18,
                "startingPrice": null,
                "endingPrice": null,
                "fees": null
            },
            "url": "https://x2y2.io/eth/0xbc4ca0eda7647a8ab7c2061c2e118a18a936f13d/2674",
            "tokenType": "ERC721"
        }
    ]
}
```

