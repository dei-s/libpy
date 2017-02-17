# PyWaves
PyWaves is an object-oriented Python interface to the Waves blockchain platform.

## Getting Started

You can install PyWaves using:

    pip install pywaves

## Documentaion

The library utilizes classes to represent various Waves data structures:

* pywaves.Address
* pywaves.Asset
* pywaves.AssetPair
	
####Code Example
```python
import pywaves as pw

myAddress = pw.Address(privateKey='CtMQWJZqfc7PRzSWiMKaGmWFm4q2VN5fMcYyKDBPDx6S')
otherAddress = pw.Address('3PNTcNiUzppQXDL9RZrK3BcftbujiFqrAfM')
myAddress.sendWaves(otherAddress, 10000000)
myToken = myAddress.issueAsset('Token1', 'My Token', 1000, 0)
while not myToken.issued():
	pass
myAddress.sendAsset(otherAddress, myToken, 50)

```

###Address Class
__pywaves.Address(address, publicKey, privateKey, seed)__ _Creates a new Address object_

####attributes:
- _address_
- _publicKey_
- _privateKey_
- _seed_

####methods:

`balance(assetId='', confirmations=0)` returns balance of Waves or other assets

`assets()` returns a list of assets owned by the address

`issueAsset(name, description, quantity, decimals=0, reissuable=False, txfee=100000000)` issue a new asset

`reissueAsset(Asset, quantity, reissuable=False, txfee=100000000)` reissue an asset

`burnAsset(Asset, quantity, txfee=100000000)` burn the specified quantity of an asset

`sendWaves(recipient, amount, attachment='', txfee=100000)` send specified amount of Waves to recipient

`sendAsset(recipient, asset, amount, attachment='', txfee=100000)` send specified amount of an asset to recipient

`buy(assetPair, price, amount)` post a buy order

`sell(assetPair, price, amount)` post a sell order


###Asset Class
__pywaves.Asset(assetId)__ _Creates a new Asset object_

####attributes:
- _status_: 	either 'PENDING' or 'ISSUED'
- _assetId_	
- _issuer_
- _name_
- _description_	
- _quantity_
- _decimals_ = 0
- _reissuable = False_

####methods:
`issued()` returns True if the asset is issued


###AssetPair Class
__pywaves.AssetPair(asset1, asset2)__ _Creates a new AssetPair object with 2 Asset objects_

####attributes:
- _asset1_
- _asset2_


##Other functions
`pywaves.setMatcher(host, port, publicKey, fee)` set the Matcher's parameters

`pywaves.height()` returns blockchain height

`pywaves.lastblock()` returns last block

`pywaves.block(n)` returns block at specified height

`pywaves.tx(id)` returns transaction details


## More Examples

####Playing with addresses:

```python
import pywaves as pw

# generate a new address
myAddress = pw.Address()  

# set an address with a public key
myAddress = pw.Address('3P6WfA4qYtkgwVAsWiiB6yaea2X8zyXncJh')

# get an existing address from seed
myAddress = pw.Address(seed='seven wrist bargain hope pattern banner plastic maple student chaos grit next space visa answer')

# get an existing address from privateKey
myAddress = pw.Address(privateKey='CtMQWJZqfc7PRzSWiMKaGmWFm4q2VN5fMcYyKDBPDx6S')
```

####Balances:
```python
import pywaves as pw

myAddress = pw.Address('3P6WfA4qYtkgwVAsWiiB6yaea2X8zyXncJh')

# get Waves balance
print("Your balance is %18d" % myAddress.balance())

# get Waves balance after 20 confirmations 
print("Your balance is %18d" % myAddress.balance(confirmations = 20))

# get an asset balance
print("Your asset balance is %18d" % myAddress.balance('DHgwrRvVyqJsepd32YbBqUeDH4GJ1N984X8QoekjgH8J'))
```

####Waves and asset transfers:
```python
import pywaves as pw

myAddress = pw.Address(privateKey='CtMQWJZqfc7PRzSWiMKaGmWFm4q2VN5fMcYyKDBPDx6S')

# send Waves to another address
myAddress.sendWaves(recipient = pw.Address('3PNTcNiUzppQXDL9RZrK3BcftbujiFqrAfM'),
					amount = 100000000)

# send asset to another address
myToken = pw.Asset('4ZzED8WJXsvuo2MEm2BmZ87Azw8Sx7TVC6ufSUA5LyTV')
myAddress.sendAsset(recipient = pw.Address('3PNTcNiUzppQXDL9RZrK3BcftbujiFqrAfM'),
					asset = myToken,
					amount = 1000)
```

####Issuing an asset:
```python
import pywaves as pw

myToken = myAddress.issueToken(name = "MyToken",
					 		   description = "This is my first token",
					 		   quantity = 1000000,
					 		   decimals = 2)
```

####Mass payment:
```python
import pywaves as pw

recipients = ['3PBbp6bg2YEnHfdJtYM7jzzXYQeb7sx5oFg',
			  '3P4A27aCd3skNja46pcgrLYEnK36TkSzgUp',
			  '3P81U3ujotNUwZMWALdcJQLzBVbrAuUQMfs',
			  '3PGcKEMwQcEbmeL8Jhe9nZQRBNCNdcHCoZP',
			  '3PKjtzZ4FhKrJUikbQ1hRk5xbwVKDyTyvkn']

myAddress = pw.Address(privateKey = "CtMQWJZqfc7PRzSWiMKaGmWFm4q2VN5fMcYyKDBPDx6S")

for address in recipients:
	myAddress.sendWaves(address, 1000000)
```

####Playing with Waves Matcher node (DEX):
```python	
import pywaves as pw

# specify the Matcher node to use
pw.setMatcher(host = '127.0.0.1',
			  port = 6886,
			  publicKey = 'FXaH5ZQW4dSdjgPTRwAXmJycLFWjaxYyZqiwVJ7cp64w',
			  fee = 1000000)

# post a buy order
BTC = pw.Asset('4ZzED8WJXsvuo2MEm2BmZ87Azw8Sx7TVC6ufSUA5LyTV')
USD = pw.Asset('6wuo2hTaDyPQVceETj1fc5p4WoMVCGMYNASN8ym4BGiL')
BTC_USD = pw.AssetPair(BTC, USD)
myAddress.buy(assetPair = BTC_USD, price = 950.75, amount = 15)

# post a sell order
WCT = pw.Asset('6wuo2hTaDyPQVceETj1fc5p4WoMVCGMYNASN8ym4BGiL')
Incent = pw.Asset('FLbGXzrpqkvucZqsHDcNxePTkh2ChmEi4GdBfDRRJVof')
WCT_Incent = pw.AssetPair(WCT, Incent)
myAddress.sell(assetPair = WCT_Incent, price = 2.50, amount = 100)
```

### Using PyWaves in a Python shell

#### Check an address balance:
```
>>> import pywaves as pw
>>> pw.Address('3P31zvGdh6ai6JK6zZ18TjYzJsa1B83YPoj')
address = 3P31zvGdh6ai6JK6zZ18TjYzJsa1B83YPoj
publicKey = 
privateKey = 
seed = 
balances:
  Waves = 1186077288304570
  BDMRyZsmDZpgKhdM7fUTknKcUbVVkDpMcqEj31PUzjMy (Tokes) = 43570656915
  RRBqh2XxcwAdLYEdSickM589Vb4RCemBCPH5mJaWhU9 (Ripto Bux) = 4938300000000
  4rmhfoscYcjz1imNDvtz45doouvrQqDpbX7xdfLB4guF (incentCoffee) = 7
  Ftim86CXM6hANxArJXZs2Fq7XLs3nJvgBzzEwQWwQn6N (Waves) = 2117290600000000
  E4ip4jzTc4PCvebYn1818T4LNoYBVL3Y4Y4dMPatGwa9 (BitCoin) = 500000000000
  FLbGXzrpqkvucZqsHDcNxePTkh2ChmEi4GdBfDRRJVof (Incent) = 12302659925430
  GQr2fpkfmWjMaZCbqMxefbiwgvpcNgYdev7xpuX6xqcE (KISS) = 1000
  DxG3PLganyNzajHGzvWLjc4P3T2CpkBGxY4J9eJAAUPw (UltraCoin) = 200000000000000
  4eWBPyY4XNPsFLoQK3iuVUfamqKLDu5o6zQCYyp9d8Ae (LIKE) = 1000
>>> 
```

#### Generate a new address:
```
>>> import pywaves as pw
>>> pw.Address()
address = 3P6WfA4qYtkgwVAsWiiB6yaea2X8zyXncJh
publicKey = EYNuSmW4Adtcc6AMCZyxkiHMPmF2BZ2XxvjpBip3UFZL
privateKey = CtMQWJZqfc7PRzSWiMKaGmWFm4q2VN5fMcYyKDBPDx6S
seed = seven wrist bargain hope pattern banner plastic maple student chaos grit next space visa answer
balances:
  Waves = 0
>>> 
```

#### Check an asset:
```
>>> import pywaves as pw
>>> pw.Asset('DHgwrRvVyqJsepd32YbBqUeDH4GJ1N984X8QoekjgH8J')
status = ISSUED
assetId = DHgwrRvVyqJsepd32YbBqUeDH4GJ1N984X8QoekjgH8J
issuer = 3PPKF2pH4KMYgsDixjrhnWrPycVHr1Ye37V
name = WavesCommunity
description = Waves community token.
quantity = 1000000000
decimals = 2
reissuable = False
```

## Connecting to a different node or chain

PyWaves supports both mainnet and testnet chains. By default, PyWaves connects to the mainnet RPC server at https://nodes.wavesnodes.com. It's possible to specify a different server and chain with the setNode() function

```python
import pywaves as pw

# connects to a local testnet node
pw.setNode(node = 'http://127.0.0.1:6869', chain = 'testnet')

# connects to a local mainnet node
pw.setNode(node = 'http://127.0.0.1:6869', chain = 'mainnet')

```


## License
Code released under the [MIT License](https://github.com/PyWaves/pywaves/LICENSE).
