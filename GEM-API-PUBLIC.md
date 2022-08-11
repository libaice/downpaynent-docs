# API Docs

## FAQs

1. **What is the rate limit?**

    Gem public API has a rate limit of about **5 requests per second**. Any requests regarding upgrading the rate limit depends on how much volume you bring to Gem. We do not take any payments for increasing the API rate limit. **Gem public API is a public good. Any plans to make it public will be announced publicly, in case we decide to do so.**

2. **Can I request new API features/endpoints?**

    You can suggest any features/endpoints, but as we have a small team, we are quite conservative when it comes to entertaining random feature requests (it's all about prioritizing things that impact more users).

3. **Are there any additional resources?**

    You can find a Postman collection [here](https://www.getpostman.com/collections/a7d29f6133ddd7bd5bc8).


A general request to all API users is to figure out small development related issues yourself. **If something can be figured out by searching on Google or learning using some tutorials, please do that.** As we have a small team of people, we will not be able to entertain all your queries and requests.


## Collections

Following is the Collections Schema

```js
{
    name: String,
    symbol: String,
    standard: String,
    description: String,
    address: String,
    createdDate: String,
    externalUrl: String,
    imageUrl: String,
    totalSupply: Number,
    isVerified: Boolean,
    sevenDayVolume: Number,
    oneDayVolume: Number,
    lastOpenSeaSaleCreatedId: Number,
    lastOpenSeaTransferId: Number,
    lastOpenSeaCancelledId: Number,
    lastRaribleAssetUpdateId: String,
    lastNumberOfUpdates: Number,
    chainId: String,
    stats: Object,
    traits: Array,
    indexingStatus: String,
    indexingError: Object,
    discordUrl: String,
    mediumUsername: String,
    telegramUrl: String,
    twitterUsername: String,
    instagramUsername: String,
    wikiUrl: String,
}
```

### Fetching any number of collection(s) and related data with sorting, filters, and pagination

- URL: https://gem-public-api.herokuapp.com/collections
- HTTP verb: POST 

**NOTE:** You can pass the following fields in the body of the POST request:
- `filters`: Filters that are directly passed to the mongodb (note: `searchText` filter is pre-processed before passing as db query). If empty, all collections in the database will be a match. Some [examples](https://mongoosejs.com/docs/queries.html)
- `sort`: Sorting parameters passed directly to mongodb. If empty, it will sort by `sevenDayVolume` by default. Some [examples](https://mongoosejs.com/docs/queries.html)
- `fields`: Fields that you want from the collection schema, passed in `select` function of mongodb query. If empty, it will return all possible fields. Some [examples](https://mongoosejs.com/docs/queries.html) 
- `limit`: Number of results you want. If empty, it defaults to `20`.  Some [examples](https://mongoosejs.com/docs/queries.html)
- `offset`: Number of results you want to skip from start. If empty, it defaults to `0`. Some [examples](https://mongoosejs.com/docs/queries.html)


#### 1. Pull a list of collections
First, we need to show the user a list of collections. There are various ways you can sort the list. A simple way is to sort by seven day volume. You can also set a `limit` which is by default set to 20 if no limit is passes. Also, you can specify what fields you want in the response.

**Request Body**
```js
{
    "sort": { "sevenDayVolume": 1 },
    "limit": 1,
    // { "field name": 1 (include field) or -1 (ignore field) }
    // ideally ONLY request the fields you want to reduce bandwidth usage and response time
    "fields": {
        "name": 1,
        "symbol": 1,
        "standard": 1,
        "description": 1,
        "address": 1,
        "createdDate": 1,
        "externalUrl": 1,
        "imageUrl": 1,
        "totalSupply": 1,
        "sevenDayVolume": 1,
        "oneDayVolume": 1,
        "stats": 1,
        "traits": 1,
        "indexingStatus": 1, 
        "discordUrl": 1, 
        "instagramUsername": 1, 
        "isVerified": 1, 
        "lastNumberOfUpdates": 1, 
        "lastOpenSeaCancelledId": 1, 
        "lastOpenSeaSaleCreatedId": 1, 
        "lastOpenSeaTransferId": 1, 
        "lastRaribleAssetUpdateId": 1, 
        "mediumUsername": 1, 
        "telegramUrl": 1, 
        "twitterUsername": 1, 
        "updatedAt": 1, 
        "wikiUrl": 1 
    }
}
```

**NOTE:** The `"indexingStatus"` field shows the status of the data indexing which includes all asset data and a full list of traits (which is saved in the collections schema). There are a few possible values of `"indexingStatus"`:
- `"lambda-indexing"`: We set this state when we want the AWS lambda function to index the collection.
- `"indexing"`: This is the state stating that the collection is being indexed as of now, so you should wait for a few minutes until it changed to "success".
- `"success"`: This state indicates that the collection and its assets have been indexed and are ready for use.
- `"failed"`: This state indicates that the indexing process has failed somehow and this collection will be indexed later, hence not ready for use.
- `"realm-event-indexing"`: This state is set when MongoDB Realm is updating the order and price info for the collection assets.
- `"realm-event-indexing-failed"`: This state is set when MongoDB Realm has experienced an error while updating the order and price info for the collection assets.

#### 2. Search with text
Once the user has a list of collections, he/she can try to search for a specific collection by text (which can be matched with multiple fields within the collection schema).

Here is a simple example showing how to search for `"bored"` (case-insensitive) on the field `name` within the collection schema.

It's a good practice to set limit and fields with each request as it reduces the database search time (search stops in case the db finds limit number of matched collections) and reduces the data that needs to be recieved from API request.

We also added a search on address field which will be used in next scenario.

**Request Body**
```js
{
    "filters": { "searchText": "bored" },
    "limit": 1,
    // { "field name": 1 (include field) or -1 (ignore field) }
    // ideally ONLY request the fields you want to reduce bandwidth usage and response time
    "fields": {
        "name": 1,
        "symbol": 1,
        "standard": 1,
        "description": 1,
        "address": 1,
        "createdDate": 1,
        "externalUrl": 1,
        "imageUrl": 1,
        "totalSupply": 1,
        "sevenDayVolume": 1,
        "oneDayVolume": 1,
        "stats": 1,
        "traits": 1,
        "indexingStatus": 1, 
        "discordUrl": 1, 
        "instagramUsername": 1, 
        "isVerified": 1, 
        "lastNumberOfUpdates": 1, 
        "lastOpenSeaCancelledId": 1, 
        "lastOpenSeaSaleCreatedId": 1, 
        "lastOpenSeaTransferId": 1, 
        "lastRaribleAssetUpdateId": 1, 
        "mediumUsername": 1, 
        "telegramUrl": 1, 
        "twitterUsername": 1, 
        "updatedAt": 1, 
        "wikiUrl": 1 
    }
}
```

#### 3. Search with address

In case the user is not able to find any collection matching for a give search text, the user can try searching for a collection using an address.

**Request Body**
```js
{
    "filters": { "address": "0xbc4ca0eda7647a8ab7c2061c2e118a18a936f13d" },
    "limit": 1,
    // { "field name": 1 (include field) or -1 (ignore field) }
    // ideally ONLY request the fields you want to reduce bandwidth usage and response time
    "fields": {
        "name": 1,
        "symbol": 1,
        "standard": 1,
        "description": 1,
        "address": 1,
        "createdDate": 1,
        "externalUrl": 1,
        "imageUrl": 1,
        "totalSupply": 1,
        "sevenDayVolume": 1,
        "oneDayVolume": 1,
        "stats": 1,
        "traits": 1,
        "indexingStatus": 1, 
        "discordUrl": 1, 
        "instagramUsername": 1, 
        "isVerified": 1, 
        "lastNumberOfUpdates": 1, 
        "lastOpenSeaCancelledId": 1, 
        "lastOpenSeaSaleCreatedId": 1, 
        "lastOpenSeaTransferId": 1, 
        "lastRaribleAssetUpdateId": 1, 
        "mediumUsername": 1, 
        "telegramUrl": 1, 
        "twitterUsername": 1, 
        "updatedAt": 1, 
        "wikiUrl": 1 
    }
}
```

**NOTE:** Once an address is searched for and you get a response, the backend starts to indexing the collection, hence the `"indexingStatus"` will probably be set to "indexing". This process takes a few minutes depending on the size of the collection.

#### 4. In case the user scrolls for more collections
In this case we just increase the `offset` by some desired amount.

**Request Body**
```js
{
    "limit": 10,
    "offset": 10,
    // { "field name": 1 (include field) or -1 (ignore field) }
    // ideally ONLY request the fields you want to reduce bandwidth usage and response time
    "fields": {
        "name": 1,
        "symbol": 1,
        "standard": 1,
        "description": 1,
        "address": 1,
        "createdDate": 1,
        "externalUrl": 1,
        "imageUrl": 1,
        "totalSupply": 1,
        "sevenDayVolume": 1,
        "oneDayVolume": 1,
        "stats": 1,
        "traits": 1,
        "indexingStatus": 1, 
        "discordUrl": 1, 
        "instagramUsername": 1, 
        "isVerified": 1, 
        "lastNumberOfUpdates": 1, 
        "lastOpenSeaCancelledId": 1, 
        "lastOpenSeaSaleCreatedId": 1, 
        "lastOpenSeaTransferId": 1, 
        "lastRaribleAssetUpdateId": 1, 
        "mediumUsername": 1, 
        "telegramUrl": 1, 
        "twitterUsername": 1, 
        "updatedAt": 1, 
        "wikiUrl": 1 
    }
}
```

## Assets

Following is the Assets Schema

```js
{
    id: String,
    name: String,
    address: String,
    description: String,
    collectionName: String,
    collectionSymbol: String,
    externalLink: String,
    imageUrl: String,
    smallImageUrl: String,
    animationUrl: String,
    tokenMetadata: String,
    standard: String,
    decimals: Number,
    traits: Array,
    creator: Object,
    owner: Object,
    market: String,
    orderCreator: String,
    openseaOrderCreatedAt: String,
    currentBasePrice: Number,
    currentEthPrice: Number,
    currentUsdPrice: Number,
    ethReserves: String,
    tokenReserves: String,
    startingPrice: Number,
    endingPrice: Number,
    duration: Number,
    paymentToken: Object,
    quantity: Number,
    topBid: Number,
    sellOrders: Object,
    lastSale: Object,
    marketUrl: String,
}
```

### Fetching any number of asset(s) and related data

- URL: https://gem-public-api.herokuapp.com/assets
- HTTP verb: POST 

**NOTE:** You can pass the following fields in the body of the POST request:
- `filters`: Filters are used to filter assets using multiple parameters. If empty, all assets in the database will be a match. 
- `sort`: Sorting parameters passed directly to mongodb. If empty, no sorting preference is added. 
- `fields`: Fields that you want from the collection schema, passed in `select` function of mongodb query. If empty, it will return a (minimum) fixed set of fields necessary for the interface.
- `limit`: Number of results you want. If empty, the value defaults to `20`.
- `offset`: Number of results you want to skip from start. If empty, the value defaults to `0`.
- `markets`: Markets from where the asset data should be pulled. If empty, all markets will be used.
- `status`: Checks the order status of the asset. `buy_now` means that the asset is on a fixed-price (or dutch) sale. `all` means return all assets, irrespective of the order status.

Here is an example request body:

```js
{
    "filters": {
        "address": "0xbc4ca0eda7647a8ab7c2061c2e118a18a936f13d",    // address of the collection
        "traits": { 
            // "trait type": ["desired trait 1", "desired trait 2"]
            "Hat": ["Bayc Hat Black", "Fez"],
            "Eyes": ["Bored", "Bloodshot"] 
        },
        "searchText": "123",    // matched against name, id, description of assets
        "price": {
            "symbol": "ETH",    // can be "ETH" or "USD"
            // in case of "ETH" the "low" and "high" need to be scaled by 10**18
            // in case of "ETH" the "low" and "high" need to be scaled by 1
            "low": 100000000000000000000,   
            "high": 200000000000000000000 
        }
    },
    // { "field name": "asc" or "desc"}, you can sort by multiple fields
    "sort": { "currentEthPrice": "asc" },
    // { "field name": 1 (include field) or -1 (ignore field) }
    // ideally ONLY request the fields you want to reduce bandwidth usage and response time
    "fields": {
        "id": 1,
        "name": 1,
        "address": 1,
        "description": 1,
        "collectionName": 1,
        "collectionSymbol": 1,
        "externalLink": 1,
        "imageUrl": 1,
        "smallImageUrl": 1,
        "animationUrl": 1,
        "tokenMetadata": 1,
        "standard": 1,
        "decimals": 1,
        "traits": 1,
        "creator": 1,
        "owner": 1,
        "market": 1,
        "currentBasePrice": 1,
        "currentEthPrice": 1,
        "currentUsdPrice": 1,
        "paymentToken": 1,
        "marketUrl": 1,
        "marketplace": 1,
        "tokenId": 1,
        "priceInfo": 1,
        "url": 1
    },
    "limit": 10,    // specifies the (max) number of responses (default: 20)
    "offset": 0,    // specifies the number of results to skip from start
    "status": ["buy_now"],  // values can be "all" or "buy_now" (default: "buy_now")
    "markets": ["opensea", "rarible", "nftx", "nft20"]  // specify the market orders you want. If left blank, it will give all market orders.
}
```

**NOTE:** As we maintain an orderbook cache for all the markets, usually the order data is updated with 1 minute of accuracy. 

#### 1. Pull a list of assets
Once a user selects a collection we need to pull an initial list of assets. An example request body is shown below.

**Request Body**
```js
{
    "filters": {
        "address": "0xbc4ca0eda7647a8ab7c2061c2e118a18a936f13d"
    },
    // { "field name": "asc" or "desc"}, you can sort by multiple fields
    "sort": { "currentEthPrice": "asc" },
    // { "field name": 1 (include field) or -1 (ignore field) }
    "fields": {
        "address" : 1,
        "name" : 1,
        "id" : 1,
        "imageUrl" : 1,
        "currentPrice" : 1,
        "currentEthPrice" : 1,
        "paymentToken" : 1
    },
    "limit": 10,
    "offset": 0,
    "markets": ["opensea", "rarible", "nftx", "nft20"]
}
```

#### 2. Search for an asset
The user might want to search for a specific asset by typing something in the search bar. The search text will the matched against name, id, description of assets.

**Request Body**
```js
{
    "filters": {
        "address": "0xbc4ca0eda7647a8ab7c2061c2e118a18a936f13d", 
        "searchText": "123",    // matched against name, id, description of assets
    },
    // { "field name": "asc" or "desc"}, you can sort by multiple fields
    "sort": { "currentEthPrice": "asc" },
    // { "field name": 1 (include field) or -1 (ignore field) }
    "fields": {
        "address" : 1,
        "name" : 1,
        "id" : 1,
        "imageUrl" : 1,
        "currentPrice" : 1,
        "currentEthPrice" : 1,
        "paymentToken" : 1
    },
    "limit": 10,
    "offset": 0,
    "markets": ["opensea", "rarible", "nftx", "nft20"]
}
```

#### 3. Apply filters and search for assets
In case the user wants the filter the results.

**Request Body**
```js
{
    "filters": {
        "address": "0xbc4ca0eda7647a8ab7c2061c2e118a18a936f13d", 
        "traits": { 
            // "trait type": ["desired trait 1", "desired trait 2"]
            "Hat": ["Bayc Hat Black", "Fez"],
            "Eyes": ["Bored", "Bloodshot"] 
        },
        "price": {
            "symbol": "ETH",    // can be "ETH" or "USD"
            // in case of "ETH" the "low" and "high" need to be scaled by 10**18
            // in case of "ETH" the "low" and "high" need to be scaled by 1
            "low": 100000000000000000000,   
            "high": 200000000000000000000 
        }
    },
    // { "field name": "asc" or "desc"}, you can sort by multiple fields
    "sort": { "currentEthPrice": "asc" },
    // { "field name": 1 (include field) or -1 (ignore field) }
    "fields": {
        "address" : 1,
        "name" : 1,
        "id" : 1,
        "imageUrl" : 1,
        "currentPrice" : 1,
        "currentEthPrice" : 1,
        "paymentToken" : 1
    },
    "limit": 10,
    "offset": 0,
    "markets": ["opensea", "rarible", "nftx", "nft20"]
}
```

#### 4. In case the user scrolls the list of assets
In this case, we just increase the offset by some desired amount.

**Request Body**
```js
{
    "filters": {
        "address": "0xbc4ca0eda7647a8ab7c2061c2e118a18a936f13d", 
        "traits": { 
            // "trait type": ["desired trait 1", "desired trait 2"]
            "Hat": ["Bayc Hat Black", "Fez"],
            "Eyes": ["Bored", "Bloodshot"] 
        },
        "price": {
            "symbol": "ETH",    // can be "ETH" or "USD"
            // in case of "ETH" the "low" and "high" need to be scaled by 10**18
            // in case of "ETH" the "low" and "high" need to be scaled by 1
            "low": 100000000000000000000,   
            "high": 200000000000000000000 
        }
    },
    // { "field name": "asc" or "desc"}, you can sort by multiple fields
    "sort": { "currentEthPrice": "asc" },
    // { "field name": 1 (include field) or -1 (ignore field) }
    "fields": {
        "address" : 1,
        "name" : 1,
        "id" : 1,
        "imageUrl" : 1,
        "currentPrice" : 1,
        "currentEthPrice" : 1,
        "paymentToken" : 1
    },
    "limit": 10,
    "offset": 10,
    "markets": ["opensea", "rarible", "nftx", "nft20"]
}
```

**NOTE:** In case the user scrolls very slowly, it's possible that the new set of assets that we pull might contain some duplicate assets (as the price of the assets from first set of assets could increase and hence appear once again). In such cases, we should just discard the previous occurence of the asset from the list.


## Routes

This endpoint can be used for getting quotes and corresponding transaction calldata (to execute the quotes) for buying or selling any NFT(s). It supports batch buying and selling so that your users can buy/sell multiple NFTs in a single transaction ALL on your website without going to any external marketplace website.

- URL: https://gem-public-api.herokuapp.com/route
- HTTP verb: POST 

#### 1. Buying a single NFT
This shows how to get quote for buying a single NFT in a single trx.

**Request Body**
```js
{
    "sender": "0x6d68807e973e14e9c511d58a398e681a38b755b8",     // address that should recieve the NFT 
    "balanceToken": "0xeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee",   // token used to pay for the NFT. Can be ETH or any ERC20
    "sell": [],
    "buy": [
        {
            "tokenType": "ERC721",  // token standard
            "address": "0xf75140376d246d8b1e5b8a48e3f00772468b3c0c",    // token address
            "tokenId": "7085",   // token id
            "amount": 1,    // amount of tokens to be bought
        }
    ]
}
```

**Response Body**

```js
{
    "transaction": "0x186b100c...00",   // trx calldata to execute
    "amountToBalance": {    // ignore
        "type": "BigNumber",
        "hex": "0x8ac7230489e80000"
    },
    "value": {              // ignore
        "type": "BigNumber",
        "hex": "0x8ac7230489e80000"
    },
    "route": [  // route showing input assets and output assets
        {
            "action": "buy",
            "marketplace": "opensea",
            "assetIn": {
                "amount": "10000000000000000000",
                "asset": "0x0000000000000000000000000000000000000000",
                "decimals": 18
            },
            "assetOut": {
                "amount": 1,
                "asset": "0xf75140376d246d8b1e5b8a48e3f00772468b3c0c",
                "decimals": 0
            }
        }
    ],
    "unavailable": []
}
```

#### 2. Buying multiple NFTs
This shows how to get quote for buying a multiple NFTs in a single trx.

**Request Body**
```js
{
    "sender": "0x6d68807e973e14e9c511d58a398e681a38b755b8",
    "balanceToken": "0xeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee",
    "sell": [],
    "buy": [
        {
            "tokenType": "ERC721",
            "address": "0xf75140376d246d8b1e5b8a48e3f00772468b3c0c",
            "tokenId": "7085",
            "amount": 1
        },
        {
            "tokenType": "ERC721",
            "address": "0xe5545fa0636a82c0b37c7db62e2104e69a11d062",
            "tokenId": "642",
            "amount": 1
        }
    ]
}
```

**Response Body**

```js
{
    "transaction": "0x186b100c...00",
    "amountToBalance": {
        "type": "BigNumber",
        "hex": "0x98a7d9b8314c0000"
    },
    "value": {
        "type": "BigNumber",
        "hex": "0x98a7d9b8314c0000"
    },
    "route": [
        {
            "action": "buy",
            "marketplace": "opensea",
            "assetIn": {
                "amount": "10000000000000000000",
                "asset": "0x0000000000000000000000000000000000000000",
                "decimals": 18
            },
            "assetOut": {
                "amount": 1,
                "asset": "0xf75140376d246d8b1e5b8a48e3f00772468b3c0c",
                "decimals": 0
            }
        },
        {
            "action": "buy",
            "marketplace": "opensea",
            "assetIn": {
                "amount": "1000000000000000000",
                "asset": "0x0000000000000000000000000000000000000000",
                "decimals": 18
            },
            "assetOut": {
                "amount": 1,
                "asset": "0xe5545fa0636a82c0b37c7db62e2104e69a11d062",
                "decimals": 0
            }
        }
    ],
    "unavailable": [],
    "contractAddress": "0x83C8F28c26bF6aaca652Df1DbBE0e1b56F8baBa2",
    "usingBackUpOrders": false
}
```

Below are some details about fields that are relevant when using Gem for a buy transaction.

- `transaction`: This part represents the calldata (the `data` part) that needs to be passed when constructing a transaction using libraries like web3.js, ethers.js or any other equivalent library for any language.

- `value`: The value of ETH that you need to pass with the transaction.

- `route`: Shows routing details of each step of the transaction.

- `unavailable`: In case any of the requested items does not have any sell orders on any markets supported by the public gem API, those items will be populated in the unavailable array.

- `contractAddress`: This the contract address to which you need to send the call to.

