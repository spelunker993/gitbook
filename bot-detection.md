# Bot detection

Every response will include the `botProbability` field that you can use to identify bot traffic.  
Bot probability value is a value between 0 and 1 which tells you the likelihood of current visitor being a programmed headless agent. Visitors with bot probability greater than 0.9 should generally be treated as bots.

FingerprintJS Pro currently can identify these bot types:

1. Selenium combinations \(FF, Chrome, IE etc\).
2. PhantomJS.
3. Chrome Headless.
4. Search engine bots.
5. Crawl engines/bots.

