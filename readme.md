Elbaite public API
==================

Base url:    
```
  api.elbaite.io/public
```

Headers:    
```
  “Content-Type”: “application/x-www-form-urlencoded”
  Accept: “application/json”
  Authorization: “Bearer: %API_TOKEN%”    
 ```
 
Numbers format: `1234.56`     

API access
==============

Contact us at help@elbaite.com.au in order to request your API key.    
You will need to provide your personal details and verify your identity.    
There is a limit of 30 requests per minute.    
    
In order to list Ethereum or ERC20 tokens, you must login to your account at www.elbaite.io and add a wallet address. This is not necessary for Bitcoin.    

How to operate
==============

1. Get the ID for the token/coin you want to list
```
  [GET] /tokens
    Response:    
    {
      res: [
        {
          token_symbol: "ETH",
          token_name: "Ethereum",
          token_id: "0x0"
        },
        {
          token_symbol: "MKR",
          token_name: "Maker",
          token_id: "0x9f8f72aa9304c8b593d555f12ef6589cc3a579a2"
        },
        ...
      ]
    }
```

2. Post the offer parameters
```
 [POST] /create_offer
   params: 
   {
     wallet: %WALLET_ADDRESS%, #Optional for Bitcoin
     token_id: %TOKEN_ID%, #Received from [GET] /tokens 
     price_per_unit: %PRICE_PER_UNIT%,
     amount: %AMOUNT%
   }

   example request: 
   {
     wallet: "0xC0D3De126F9c073E81540C062Ad0256ECBA63325",
     token_id: "0x0",
     price_per_unit: 253.62198,
     amount: 10
   }

   success response: 
   {
     status: "success",
     offer_id: %OFFER_ID%
   }
```

3. Monitor your sales. When a user makes a purchase in one of your listings and we confirm the payment, the transaction will be available to be retrieved
```
[GET] /transactions
   Response: 
   {
     res: [
       {
         token_symbol: "ETH",
         token_name: "Ethereum",
         purchase_id: 12,
         buyer_wallet: "0xC0D3De126F9c073E81540C062Ad0256ECBA63325",
         amount: 0.2
       },
       {
         token_symbol: "ETH",
         token_name: "Ethereum",
         purchase_id: 19,
         buyer_wallet: "0xC0D3De126F9c073E81540C062Ad0256ECBA63325",
         amount: 1.5
       },
       ...
     ]
   }
```

4. Once you sent the cryptocurrency to the buyer and the transaction has been confirmed, submit the tx_hash to Elbaite to finalise the sale
```
[POST] /verify_tx_hash
     params: 
     {
       purchase_id: %PURCHASE_ID%, #Received from /transactions
       tx_hash: %TX_HASH%
     }

     example request: 
     {
       purchase_id: 19,
       tx_hash: "0xbb820199a09a8ee2bfe8c7e79d3355da2ae7711ca09783f24adbb33816ba5ac8"
     }

     success response: 
     {
       user: 2,
       status: "confirmed",
       purchase: 19,
       info: {
           value: 1.5,
           to: "0xc0d3de126f9c073e81540c062ad03256ecba63325",
           timestamp: 1557190705,
           success: true,
           logs: [],
           input: "0x",
           hash: "0xbb820199a09a8ee2bfe8c7e79d3355da2ae7711ca09783f24adbb33816ba5ac8",
           gasUsed: 21000,
           gasLimit: 21000,
           from: "0xaaeea29e4efd3ca279205b1af5580562b3b88571",
           confirmations: 22,
           blockNumber: 7710641
       }
     }
```

Aditional Endpoints
===================

- List all your offers
```
  [GET] /offers
  Response:
    {
      res:
        [
          {
            token_name: "Bitcoin",
            token_id: "bitcoin",
            terminated: false,
            price_per_unit: 123.456789,
            offer_id: 90,
            offer_amount: 10,
            is_deleted: false,
            created: "2019-06-07T00:06:27.796876"
          },
          ...
        ]         
    }
```

- Edit the price of your listing
```
 [POST] /edit_offer
   params: 
   {
     offer_id: %OFFER_ID%, #Received from /create_offer
     price_per_unit: %PRICE_PER_UNIT%
   }

   example request: 
   {
     offer_id: 18, 
     price_per_unit: 416.34978
   }

   success response: 
   {
     status: "success"
   }
```

- Delete your listing
```
 [POST] /delete_offer
   params: 
   {
     offer_id: %OFFER_ID% #Received from /create_offer
   }

   example request: 
   {
     offer_id: 18
   }

   success response: 
   {
     status: "deleted"
   }
 ```

