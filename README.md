<p align="center">
  <img width="350px" src="http://dutchx.readthedocs.io/en/latest/_static/DutchX-logo_blue.svg" />
</p>

# DutchX CLI
This project provides a simple script that will allow you to trade on the DutchX using the Command Line Interface (CLI). 
Trading can be done in the following networks:

* Rinkeby (recommended to test)
* Kovan (not recommended)
* Mainnet

> The CLI was initially created for testing purposes and internal use only, hence some commands might work
> differently than expected.
>
> Please [comment any issues](https://github.com/gnosis/dx-cli/issues) with
> the CLI so we can make it better for everyone!

# Documentation
For more information on the underlying protocol, checkout the [DutchX Documentation](http://dutchx.readthedocs.io/en/latest).


# Security and Liability
All the code and explanation is provided WITHOUT ANY WARRANTY; without even the implied warranty
 of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
 An API gathers publicly available data from the Ethereum blockchain on the usage of this Program.
Please note that where you use the DutchX protocol to auction off a token and no one participates on the bid side of the auction within a 24 hour period, the token to be sold will be valued at zero. Therefore, we recommend that you also ensure liquidity for the bid-side.

If you are inexperiences with blockchain operations, we do not recommend to use the CLI. Only you are responsible for your private key.

# Getting ready to trade
**1a Install docker**
* Windows: https://store.docker.com/editions/community/docker-ce-desktop-windows
* Mac OS: https://store.docker.com/editions/community/docker-ce-desktop-mac
* For other platforms or more details: https://docs.docker.com/install/

**1b Alternatively to 1a: Clone the CLI scripts**
> Alternatively, you can download the
> [ZIP file](https://github.com/gnosis/dx-cli/archive/master.zip) instead of
> cloning the git repository.

```bash
# Clone repo
git clone https://github.com/gnosis/dx-cli.git dx-cli
cd dx-cli
```

**2. Create `local.conf` using [local.conf.example](./local.conf.example)**
> This step can be omitted if you plan to use the CLI for read-only operations.

 **IMPORTANT**: Your mnemonic phrase will be required in order to link your wallet to the DutchX. Make sure you don't share
 this information and that you keep the `local.conf` file that you will create offline. If you don't do this, you risk losing your funds! Never commit this to Github. If you don't know how to get your mnemonic phrase, you probably should not continue.

Go to the folder where you downloaded the repository, create a copy of the [local.conf.example](./local.conf.example) file
 and call the new
file `local.conf`.

Edit the file in order to add your own secret mnemonic that will be used to sign
the transactions.

> **NOTE**: The `local.conf` is git ignored, so you can add your wallet config
> here.



**3. Make sure the scripts are executable**
```bash
# Allow the CLI script to be executed
chmod +x dutchx*
```

**4. Network info: Review the list of tokens you want to use**

> **NOTE**: This step can be skipped if you don't intend to list new tokens on the DutchX.

This step is relevant because the DutchX is an open protocol where anyone can list new tokens to trade. 

Each network has the following configuration: 
* [network-rinkeby.conf](./network-rinkeby.conf)
* [network-kovan.conf](./network-kovan.conf)
* [network-mainnet.conf](./network-mainnet.conf)

Check out the complete list of tokens listed on the `DutchX` here:
* **Rinkeby**: 
  * [https://dutchx-rinkeby.d.exchange/api/docs/#!/markets/getMarkets](https://dutchx-rinkeby.d.exchange/api/docs/#!/markets/getMarkets)
* **Mainnet**: 
  * [https://dutchx.d.exchange/api/docs/#!/markets/getMarkets](https://dutchx.d.exchange/api/docs/#!/markets/getMarkets)
* **Kovan**: Unlike `Rinkeby` and `Mainnet`, `Kovan` doesn't have a published API. To check all available markets, you must do so on smart contract level:
  * https://dutchx.readthedocs.io/en/latest/smart-contracts_addresses.html

**5. Try the CLI**

Run the `help` command on the Terminal to get a list of all avaliable commands:
```bash
# Rinkeby
./dutchx-rinkeby -h

# Kovan
./dutchx-kovan -h

# Mainnet
./dutchx-mainnet -h
```

# Start Trading 
## DutchX trading process
Trading on the DutchX requires you to send your tokens to a smart contract, where the trading occurs. 
The whole trading process looks as the following image:

<img src="https://raw.githubusercontent.com/gnosis/dx-docs/master/source/_static/DurtchX_graphi.png" width="600" height="400">



Before we start explaining each step in the image, we will use the balance command to make the process more clear. 
All the commands used in this explanation will be for the Rinkeby Testnet. If you want to trade on Ethereum Mainnet, change `Rinkeby` to `mainnet`


```bash
# Show the balance of the account inputed in the local.config file
./dutchx-rinkeby balances

# You will see the balances your balances displayed in a similar way as below:
  INFO-cli      ACCOUNT: "Your public Ethereum address" +427ms
  INFO-cli      BALANCE: 4.430652555999879 ETH +3ms
  INFO-cli 
  INFO-cli      Balances RDN (0x3615757011112560521536258c1e7325ae3b48ae): +941ms
  INFO-cli              - Balance in DX: 222.972178357604664181 +1ms
  INFO-cli              - Balance of user: 111.617365082280797714 +2ms
  INFO-cli      Balances WETH (0xc778417e063141139fce010982780140aa0cd5ab): +1ms
  INFO-cli
  INFO-cli              - Balance in DX: 11.4032061309442109 +2ms
  INFO-cli              - Balance of user: 0 +0ms
```

As you can see, there are two types of balance for every listed token: 

`Balance of user` indicates the balance in the users wallet. 

`Balance in DX` indicates the balance that has been deposited in the DutchX smart contract and is ready to trade. Lets now 
go through the steps in the image one-by-one. 

#####1. Deposit tokens
 Use the following command to deposit tokens you want to trade: 

```bash
./dutchx-rinkeby deposit 0.35 WETH
```

> **NOTE**: Since ETH is not an ERC-20, the DutchX will automatically call the wrapped Ether smart contract and wrap your Ether.

#####2. Trade on the DutchX

You can now take part in the running auction as a bidder in the current auction, or post a sell order for the coming one. 

Before placing a trade, we recommend you try the following commands:

 ```bash
 # This commands outputs all the available token pairs
./dutchx-rinkeby markets

# This command outputs information about the current auction of a given pair
 ./dutchx-rinkeby state WETH-RDN
```

After picking the pair you want to trade and checking the state of a given auction, you can start trading with the following commands:

```bash
 # This commands will place a bid in the running auction (no need to specify the auction index)
./dutchx-rinkeby buy 5 WETH-RDN

# This command posts a sell order for the specified auction index. 

 ./dutchx-rinkeby sell 5 WETH-RDN 361
```

> **NOTE**: For the sell command, you currently need to specify the index of the upcoming auction in which you want to sell your tokens. To ensure that it is the next auction which start, check the state again, see which one is running and add 1 to the auction index. The 361 noted in the command above is an example only. 

#####3. Claim the tokens from the auction you took part in

In order to see the resulting balance after trading in `Balance in DX`, you must execute the following commands:

```bash
# Claim tokens, which will give you the tokens of the auction you participated in, not mattering if you were selling or bidding
./dutchx-rinkeby claim-tokens WETH-RDN

# Claim tokens after participating as a seller, which will give you the second token in the pair
./dutchx-rinkeby claim-seller WETH-RDN

# Claim tokens after participating as a bidder, which will give you the first tokin in the pair
./dutchx-rinkeby claim-buyer WETH-RDN
```

It is important to remember that sellers can only claim their receiving tokens once the auction has finished. Bidders can claim 
their receiving tokens (once they bid) anytime during the auction and can claim any additional increments in the future. 

We recommend bidders to claim tokens once the auction has ended in order to avoid unnecessary gas costs.

#####4. Withrdaw tokens from the DutchX smart contract 

This is the final step if you would like to have your tokens back in your wallet. It simply sends tokens from your `Balance in DX` to your `Balance of user`

```bash
./dutchx-rinkeby withdraw 0.35 WETH
```

We recommend users that plan on trading often in the DutchX to leave their tokens in the smart contract in order to avoid unnecessary gas costs.

## Useful commands

We have so far covered the process of executing trades on the DutchX, but there are several additional commands that will give important information and facilitate the process.

We will do a rundown on some useful commands, starting by reminding you the command to view all commands :)

```bash
# Rinkeby
./dutchx-rinkeby -h
```
####Price related commands
These set of commands will output one of the most important pieces of information when exchanging tokens - price. 
There are several prices you can call depending on your needs:
```bash
# Price of the ongoing auction
# N/A would indicate an auction didn't start
./dutchx-rinkeby price WETH-RDN

# USD price for a specific token
# It will use the closing price and the Ether oracle
./dutchx-rinkeby usd-price 1 RDN

# Closing prices
# This shows the last N closing prices
./dutchx-rinkeby closing-prices WETH-RDN


# Price of an external exchange (i.e. 'binance', 'huobi', 'kraken', 'bitfinex')
# This is not part of the DX. It's just a reference.
./dutchx-rinkeby market-price WETH-RDN
```
####Auction and trading history commands

It can prove useful to check information of previous auctions and trades in the past. You can display the information
of cleared auctions and filter it by dates using the following commands:

```bash
# Get today's auctions
./dutchx-rinkeby auctions --period today

# Get auctions from the last 7 days
./dutchx-rinkeby auctions --period week

# Get this week's auctions
./dutchx-rinkeby auctions --period week

# Get last week's auctions
./dutchx-rinkeby auctions --period last-week

# Get auctions from a specified date range
./dutchx-rinkeby auctions --from-date=25-05-2018 --to-date=26-05-2018

# Export auctions from a given date range to a .csv file
./dutchx-rinkeby auctions --from-date=25-05-2018 --to-date=26-05-2018 --file=auctions.csv
```

As mentioned previously, you can also get the history of previous trades. Use the following commands:

```bash
# Filter by token
#   It will filter by trades of auctions that contain the given token
./dutchx-rinkeby trades --period today --token RDN

#Filter by wallet
# You can use any of the additional commands and specify a wallet in order to just see these trades
./dutchx-rinkeby trades --period today --token RDN --account=0x45345b00156efe2a859b7e254ab3ae0bb2ebfc0e

# Filter by sell token
#   It will filter by trades of auctions that contain the given token as a sell
#   token
./dutchx-rinkeby trades --period today --sell-token RDN

# Filter by buy token
#   It will filter by trades of auctions that contain the given token as a buy
#   token
./dutchx-rinkeby trades --period today --buy-token RDN

# Filter by auction index
#   It will filter by trades of auctions that contain the given token as a buy
#   token
./dutchx-rinkeby trades --period today --auction-index 24

# Filter by account
#   It will filter by trades of the given account address
./dutchx-rinkeby trades --period today --auction-index 24

# Export the result to a file
./dutchx-rinkeby trades --from-date=25-05-2018 --to-date=26-05-2018 --file=auctions.csv
```



# Feedback, suggestions, collaborations
Please, let us know any typo or error in the project or documentation.

Any idea, proposal or collaboration is welcome.

Consider checking out [DutchX Read the Docs](https://gitter.im/gnosis/DutchX).

Also, you are encouraged to participate in the [Gitter Channel for the DutchX](https://dutchx.readthedocs.io).

# Contributors
- Stefan ([Georgi87](https://github.com/Georgi87))
- Martin ([koeppelmann](https://github.com/koeppelmann))
- Anxo ([anxolin](https://github.com/anxolin))
- Dani ([dasanra](https://github.com/dasanra))
- Dominik ([dteiml](https://github.com/dteiml))
- David ([W3stside](https://github.com/w3stside))
- Dmitry ([Velenir](https://github.com/Velenir))
- Alexander ([josojo](https://github.com/josojo))
