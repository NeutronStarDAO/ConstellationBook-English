## The Exchange Rate Canister (XRC)

The Exchange Rate Canister (XRC) is a powerful tool that can provide critical exchange rate information to various applications and businesses.



### Who are you, XRC?

If you're hearing about XRC for the first time, you may be a little confused. You might ask: "What exactly is this XRC thing?" Good question.

In short, XRC is a Canister running on the uzr34 subnet that mainly serves to provide exchange rate information to requesting Canisters. It's like a 24/7 exchange rate consultation advisor - whether you need rates for BTC/ICP, ICP/USD, or USD/EUR, it's there to serve you. And you can specify a timestamp to get the rate at that point in time. If no timestamp is provided, it will give you the current rate.

XRC acts as an on-chain exchange rate oracle, which is very useful for DeFi apps but can also add value to any application that needs exchange rate data.

The NNS Canister responsible for minting Cycles will use XRC to get the latest ICP/XDR rate, critical for converting ICP to Cycles.



### How can I use you, XRC?

"This XRC sounds really cool, but how do I actually use it?" Using XRC is very straightforward.

First, you need to know XRC's ID, which is uf6dk-hyaaa-aaaaq-qaaaq-cai. Then you can send it a request containing a base asset, a quote asset, and an optional timestamp.

```
Copy codetype GetExchangeRateRequest = record {
   base_asset: Asset;
   quote_asset: Asset;
   timestamp: opt nat64;
};
```

After sending your request, XRC will reply with a result - either a successful exchange rate or an error message.

```
Copy codetype GetExchangeRateResult = variant {
   Ok: ExchangeRate; 
   Err: ExchangeRateError;
};
```

Note your request must contain sufficient Cycles, otherwise XRC will return an error. In fact, the cost per call depends on the asset types requested and XRC's internal rate cache state.



### How do you work, XRC?

Now you may be curious how XRC actually works under the hood. After all, knowing how something operates allows you to better leverage it, right?

Upon receiving your request, XRC springs into action. It queries all supported exchanges to get the rate for your requested crypto assets against USDT. It then calculates a rate and caches it for next time.

If you request a crypto/crypto base-quote pair, XRC derives the B/Q rate from the queried B/USDT and Q/USDT rates.

In addition to querying exchanges, XRC also automatically retrieves FX rates from data providers, and polls rates for multiple stablecoins to infer a USD/USDT rate.

If successful, the response contains metadata in addition to the rate, which helps you determine the credibility of the returned rate.