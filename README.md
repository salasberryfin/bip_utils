# BIP utility library
[![PyPI version](https://badge.fury.io/py/bip-utils.svg)](https://badge.fury.io/py/bip-utils)
[![Build Status](https://travis-ci.com/ebellocchia/bip_utils.svg?branch=master)](https://travis-ci.com/ebellocchia/bip_utils)
[![codecov](https://codecov.io/gh/ebellocchia/bip_utils/branch/master/graph/badge.svg)](https://codecov.io/gh/ebellocchia/bip_utils)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://raw.githubusercontent.com/ebellocchia/bip_utils/master/LICENSE)

## Introduction

This package contains an implementation of some BIP (Bitcoin Improvement Proposal) specifications, allowing to:
- Generate a mnemonic string from a random entropy
- Generate a secure seed from the mnemonic string
- Use the seed to generate the master key of the wallet and derive the children keys, including address encoding

The implemented BIP specifications are the following:
- [BIP-0039](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki) for mnemonic and seed generation
- [BIP-0032](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki) for master key generation (from the secure seed) and children keys derivation
- [BIP-0044](https://github.com/bitcoin/bips/blob/master/bip-0044.mediawiki), [BIP-0049](https://github.com/bitcoin/bips/blob/master/bip-0049.mediawiki) and [BIP-0084](https://github.com/bitcoin/bips/blob/master/bip-0084.mediawiki) for the hierarchy of deterministic wallets, based on BIP-0032 specification
- [SLIP-0010](https://github.com/satoshilabs/slips/blob/master/slip-0010.md) for BIP-0032 derivation with ed25519 and nist256p1 curves

In addition to this, the package allows to:
- Parse BIP-0032 derivation paths
- Generate addresses for all the supported coins
- Encode/Decode [WIF](https://en.bitcoin.it/wiki/Wallet_import_format)
- Encode/Decode [base58](https://en.bitcoin.it/wiki/Base58Check_encoding#Background)
- Encode/Decode [ss58](https://polkadot.js.org/docs/keyring/start/ss58/)
- Encode/Decode [segwit bech32](https://github.com/bitcoin/bips/blob/master/bip-0173.mediawiki)
- Encode/Decode Bitcoin Cash bech32

Dependencies:
- [crcmod](https://pypi.org/project/crcmod/) for CRC computation
- [pycryptodome](https://pypi.org/project/pycryptodome/) for keccak256 and SHA512/256
- [ecdsa](https://pypi.org/project/ecdsa/) for nist256p1 and secp256k1 curves
- [pynacl](https://pypi.org/project/PyNaCl/) for ed25519 curve

The package currently supports the following coins (I try to add new ones from time to time):
- Algorand
- Avalanche (all the 3 chains)
- Band Protocol
- Binance Chain
- Binance Smart Chain
- Bitcoin (and related test net)
- Bitcoin Cash (and related test net)
- BitcoinSV (and related test net)
- Cosmos
- Dash (and related test net)
- Dogecoin (and related test net)
- Elrond
- Ethereum
- Ethereum Classic
- Fantom Opera
- Harmony One (Ethereum and Cosmos addresses)
- Huobi Heco Chain
- IRIS Network
- Kava
- Kusama (based on ed25519 SLIP-0010, like TrustWallet or Ledger, it won't generate the same addresses of Polkadot-JS)
- Litecoin (and related test net)
- OKEx Chain (Ethereum and Cosmos addresses)
- NEO
- Ontology
- Polkadot (based on ed25519 SLIP-0010, like TrustWallet or Ledger, it won't generate the same addresses of Polkadot-JS)
- Polygon
- Ripple
- Solana
- Stellar
- Terra
- Tezos
- Theta Network
- Tron
- VeChain
- Zcash (and related test net)
- Zilliqa

Clearly, for those coins that support Smart Contrats (e.g. Ethereum, Tron, ...), the generated addresses are valid for all the related tokens.

## Install the package

The package requires Python 3, it is not compatible with Python 2.\
To install it:
- Using *setuptools*:

        python setup.py install

- Using *pip*:

        pip install bip_utils

To run the tests:

- Without code coverage

        python -m unittest discover

- With code coverage and report:

        pip install coverage
        coverage run -m unittest discover
        coverage report

## BIP-0039 library

### Mnemonic generation

A mnemonic string can be generated by specifying the words number (in this case a random entropy will be used) or directly the entropy bytes.\
Supported words number:

|Words number|Enum|
|---|---|
|12|*Bip39WordsNum.WORDS_NUM_12*|
|15|*Bip39WordsNum.WORDS_NUM_15*|
|18|*Bip39WordsNum.WORDS_NUM_18*|
|21|*Bip39WordsNum.WORDS_NUM_21*|
|24|*Bip39WordsNum.WORDS_NUM_24*|

Supported entropy bits:

|Entropy bits|Enum|
|---|---|
|128|*Bip39EntropyBitLen.BIT_LEN_128*|
|160|*Bip39EntropyBitLen.BIT_LEN_160*|
|192|*Bip39EntropyBitLen.BIT_LEN_192*|
|224|*Bip39EntropyBitLen.BIT_LEN_224*|
|256|*Bip39EntropyBitLen.BIT_LEN_256*|

Supported languages (if not specified, the default is English):

|Language|Enum|
|---|---|
|English|*Bip39Languages.ENGLISH*|
|Italian|*Bip39Languages.ITALIAN*|
|French|*Bip39Languages.FRENCH*|
|Spanish|*Bip39Languages.SPANISH*|
|Portuguese|*Bip39Languages.PORTUGUESE*|
|Czech|*Bip39Languages.CZECH*|
|Chinese (simplified)|*Bip39Languages.CHINESE_SIMPLIFIED*|
|Chinese (traditional)|*Bip39Languages.CHINESE_TRADITIONAL*|
|Korean|*Bip39Languages.KOREAN*|

**Code example**

    import binascii
    from bip_utils import Bip39EntropyGenerator, Bip39MnemonicGenerator, Bip39WordsNum, Bip39Languages

    # Generate a random mnemonic string of 12 words with default language (English)
    mnemonic = Bip39MnemonicGenerator().FromWordsNumber(Bip39WordsNum.WORDS_NUM_12)

    # Generate a random mnemonic string of 15 words by specifying the language
    mnemonic = Bip39MnemonicGenerator(Bip39Languages.ITALIAN).FromWordsNumber(Bip39WordsNum.WORDS_NUM_15)

    # Generate the mnemonic string from entropy bytes:
    entropy_bytes_hex = b"00000000000000000000000000000000"
    mnemonic = Bip39MnemonicGenerator().FromEntropy(binascii.unhexlify(entropy_bytes_hex))
    mnemonic = Bip39MnemonicGenerator(Bip39Languages.FRENCH).FromEntropy(binascii.unhexlify(entropy_bytes_hex))

    # Generate mnemonic from random 192-bit entropy
    entropy_bytes = Bip39EntropyGenerator(Bip39EntropyBitLen.BIT_LEN_192).Generate()
    mnemonic = Bip39MnemonicGenerator().FromEntropy(entropy_bytes)

### Mnemonic validation

A mnemonic string can be validated by verifying its checksum.
It is also possible to get back the entropy bytes from a mnemonic.\
When validating, the language can be either specified or automatically detected.
Automatic detection takes more time, so if you know the mnemonic language in advance it'll be better to specify it at construction.

**Code example**

    from bip_utils import Bip39ChecksumError, Bip39Languages, Bip39MnemonicValidator

    # Get back the original entropy from a mnemonic string, specifying the language
    entropy_bytes = Bip39MnemonicValidator(mnemonic, Bip39Languages.SPANISH).GetEntropy()
    # Like before with automatic language detection
    entropy_bytes = Bip39MnemonicValidator(mnemonic).GetEntropy()
    # Get if a mnemonic string is valid, return bool
    is_valid = Bip39MnemonicValidator(mnemonic).IsValid()
    # Validate a mnemonic string, raise exceptions
    try:
        Bip39MnemonicValidator(mnemonic).Validate()
        # Valid...
    except Bip39ChecksumError:
        # Invalid checksum...
    except ValueError:
        # Invalid length or language...

### Seed generation

A secure 64-byte seed is generated from a mnemonic and can be protected by a passphrase.\
This seed can be used to construct a Bip class.\
Also in this case, the language can be specified or automatically detected.

**Code example**

    from bip_utils import Bip39SeedGenerator

    # Generate with automatic language detection and passphrase (empty)
    seed_bytes = Bip39SeedGenerator(mnemonic).Generate()
    # Generate with automatic language detection and custom passphrase
    seed_bytes = Bip39SeedGenerator(mnemonic).Generate("my_passphrase")
    # Generate specifying the language
    seed_bytes = Bip39SeedGenerator(mnemonic, Bip39Languages.CZECH).Generate()

### Substrate seed generation

Polkadot introduced a variant for generating seed, which computes the seed directly from the mnemonic entropy instead of the mnemonic string.\
Reference: [substrate-bip39](https://github.com/paritytech/substrate-bip39)\
For this purpose, the class *Bip39SubstrateSeedGenerator* can be used, which has the same usage of *Bip39SeedGenerator*.

    from bip_utils import Bip39SubstrateSeedGenerator

    # Generate with automatic language detection and passphrase (empty)
    seed_bytes = Bip39SubstrateSeedGenerator(mnemonic).Generate()
    # Generate with automatic language detection and custom passphrase
    seed_bytes = Bip39SubstrateSeedGenerator(mnemonic).Generate("my_passphrase")
    # Generate specifying the language
    seed_bytes = Bip39SubstrateSeedGenerator(mnemonic, Bip39Languages.CZECH).Generate()

Please note that this is not used by all wallets supporting Polkadot. For example, TrustWallet or Ledger still use the standard BIP39 seed generation for Polkadot.

## BIP-0032 library

The BIP-0032 library is wrapped inside the BIP-0044, BIP-0049 and BIP-0084 libraries, so there is no need to use it alone unless you need to derive some non-standard paths.\
The library currently supports the following elliptic curves for key derivation:
- Ed25519 (based on SLIP-0010): *Bip32Ed25519Slip* class
- Nist256p1 (based on SLIP-0010): *Bip32Nist256p1* class
- Secp256k1: *Bip32Secp256k1* class

They all inherit from the generic *Bip32Base* class, which can be extended to implement new elliptic curves derivation.\
The specific curve depends on the specific coin and it's automatically selected if you use the *Bip44* library.

### Construction from a seed

The class can be constructed from a seed. The seed can be specified manually or generated by *Bip39SeedGenerator*.\
The constructed class is the master path, so printing the private key will result in printing the master key.

**Code example**

    import binascii
    from bip_utils import Bip32Secp256k1

    # Seed bytes
    seed_bytes = binascii.unhexlify(b"5eb00bbddcf069084889a8ab9155568165f5c453ccb85e70811aaed6f6da5fc19a5ac40b389cd370d086206dec8aa6c43daea6690f20ad3d8d48b2d2ce9e38e4")
    # Construct from seed. In case it's a test net, pass True as second parameter. Derivation path returned: m
    bip32_ctx = Bip32Secp256k1.FromSeed(seed_bytes)
    # Print master key in extended format
    print(bip32_ctx.PrivateKey().ToExtended())

In addition to a seed, it's also possible to specify a derivation path.

**Code example**

    # Derivation path returned: m/0'/1'/2
    bip32_ctx = Bip32Secp256k1.FromSeedAndPath(seed_bytes, "m/0'/1'/2")
    # Print private key for derivation path m/0'/1'/2 in extended format
    print(bip32_ctx.PrivateKey().ToExtended())

### Construction from an extended key

Alternatively, the class can be constructed directly from an extended key.\
The returned object will be at the same depth of the specified key.

**Code example**

    from bip_utils import Bip32Secp256k1

    # Private extended key from derivation path m/0'/1 (depth 2)
    key_str = "xprv9wTYmMFdV23N2TdNG573QoEsfRrWKQgWeibmLntzniatZvR9BmLnvSxqu53Kw1UmYPxLgboyZQaXwTCg8MSY3H2EU4pWcQDnRnrVA1xe8fs"
    # Construct from key (return object has depth 2)
    bip32_ctx = Bip32Secp256k1.FromExtendedKey(key_str)
    # Print keys
    print(bip32_ctx.PrivateKey().ToExtended())
    print(bip32_ctx.PublicKey().ToExtended())

    # Public extended key from derivation path m/0'/1 (depth 2)
    key_str = "xpub6ASuArnXKPbfEwhqN6e3mwBcDTgzisQN1wXN9BJcM47sSikHjJf3UFHKkNAWbWMiGj7Wf5uMash7SyYq527Hqck2AxYysAA7xmALppuCkwQ"
    # Construct from key (return object has depth 2)
    bip32_ctx = Bip32Secp256k1.FromExtendedKey(key_str)
    # Print key
    print(bip32_ctx.PublicKey().ToExtended())
    # Getting private key from a public-only object triggers a Bip32KeyError exception

### Construction from a private key bytes

Finally, the class can be constructed directly from a private key bytes. The key will be considered a master key since there is no way to recover the key derivation data from the key bytes.\
Therefore, the returned object will have a depth equal to zero, a zero chain code and parent fingerprint.

**Code example**

    import binascii
    from bip_utils import Bip32Secp256k1

    # Private key bytes
    priv_key_bytes = b"e8f32e723decf4051aefac8e2c93c9c5b214313817cdb01a1494b917c8436b35"
    # Construct from private key bytes
    bip32_ctx = Bip32Secp256k1.FromPrivateKey(binascii.unhexlify(priv_key_bytes))
    # Print keys and data
    print(bip32_ctx.PrivateKey().Raw().TóBytes())
    print(bip32_ctx.PublicKey().RawCompressed().ToBytes())
    print(bip32_ctx.Depth())
    print(bip32_ctx.ChainCode())
    print(bip32_ctx.ParentFingerPrint().ToBytes())

### Keys derivation

Each time a key is derived, a new instance of the class is returned. This allows to chain the methods call or save a specific key pair for future derivation.\
The *Bip32Utils.HardenIndex* method can be used to make an index hardened.

**Code example**

    import binascii
    from bip_utils import Bip32Secp256k1, Bip32Utils

    # Seed bytes
    seed_bytes = binascii.unhexlify(b"5eb00bbddcf069084889a8ab9155568165f5c453ccb85e70811aaed6f6da5fc19a5ac40b389cd370d086206dec8aa6c43daea6690f20ad3d8d48b2d2ce9e38e4")
    # Path: m
    bip32_ctx = Bip32Secp256k1.FromSeed(seed_bytes)
    # Derivation path: m/0'/1'/2/3
    bip32_ctx = bip32_ctx.ChildKey(Bip32Utils.HardenIndex(0)) \
                         .ChildKey(Bip32Utils.HardenIndex(1)) \
                         .ChildKey(2)                         \
                         .ChildKey(3)
    # Print keys in extended format
    print(bip32_ctx.PrivateKey().ToExtended())
    print(bip32_ctx.PublicKey().ToExtended())

    # Print keys bytes
    print(bip32_ctx.PrivateKey().Raw().ToBytes())
    print(bytes(bip32_ctx.PrivateKey().Raw()))
    print(bip32_ctx.PublicKey().RawCompressed().ToBytes())
    print(bytes(bip32_ctx.PublicKey().RawCompressed()))
    print(bip32_ctx.PublicKey().RawUncompressed().ToBytes())
    print(bytes(bip32_ctx.PublicKey().RawUncompressed()))

    # Print keys in hex format
    print(bip32_ctx.PrivateKey().Raw().ToHex())
    print(str(bip32_ctx.PrivateKey().Raw()))
    print(bip32_ctx.PublicKey().RawCompressed().ToHex())
    print(str(bip32_ctx.PublicKey().RawCompressed()))
    print(bip32_ctx.PublicKey().RawUncompressed().ToHex())
    print(str(bip32_ctx.PublicKey().RawUncompressed()))

    # Print other BIP32 data
    print(bip32_ctx.Index().IsHardened())
    print(bip32_ctx.Index().ToInt())
    print(int(bip32_ctx.Index()))

    print(bip32_ctx.Depth())
    print(bip32_ctx.ChainCode())

    print(bip32_ctx.FingerPrint().IsMasterKey())
    print(bip32_ctx.FingerPrint().ToBytes())
    print(bytes(bip32_ctx.FingerPrint()))

    print(bip32_ctx.ParentFingerPrint().IsMasterKey())
    print(bip32_ctx.ParentFingerPrint().ToBytes())
    print(bytes(bip32_ctx.ParentFingerPrint()))

    # Alternative: use DerivePath method
    bip32_ctx = Bip32.FromSeed(seed_bytes)
    bip32_ctx = bip32_ctx.DerivePath("0'/1'/2/3")

    # DerivePath derives from the current depth, so it can be split
    bip32_ctx = Bip32.FromSeed(seed_bytes)
    bip32_ctx = bip32_ctx.DerivePath("0'/1'")   # Derivation path: m/0'/1'
    bip32_ctx = bip32_ctx.DerivePath("2/3")     # Derivation path: m/0'/1'/2/3

The *Bip32Ed25519Slip* and *Bip32Nist256p1*  classes work exactly in the same way.\
However, the *Bip32Ed25519Slip* class has some differences:
- Not-hardened private key derivation is not supported
- Public key derivation is not supported

For example:

    import binascii
    from bip_utils import Bip32Ed25519Slip

    # Seed bytes
    seed_bytes = binascii.unhexlify(b"5eb00bbddcf069084889a8ab9155568165f5c453ccb85e70811aaed6f6da5fc19a5ac40b389cd370d086206dec8aa6c43daea6690f20ad3d8d48b2d2ce9e38e4")
    # Only hardened private key derivation, it's ok
    bip32_ctx = Bip32Ed25519Slip.FromSeedAndPath(seed_bytes, "m/0'/1'")
    # Not-hardened private key derivation, Bip32KeyError is raised
    bip32_ctx = Bip32Ed25519Slip.FromSeedAndPath(seed_bytes, "m/0/1")
    # Public derivation, Bip32KeyError is raised
    bip32_ctx = bip32_ctx.ConvertToPublic()
    bip32_ctx.ChildKey(0)

### Parse path

The Bip32 module allows also to parse derivation paths by returning the list of indexes in the path.\
In case of error, an empty list is returned.

**Code example**

    from bip_utils import Bip32PathParser

    # Parse path
    path = Bip32PathParser.Parse("0'/1'/2")
    # 'p' can be used as an alternative character instead of '
    path = Bip32PathParser.Parse("0p/1p/2")
    # "m" can be added at the beginning
    path = Bip32PathParser.Parse("m/0'/1'/2")
    # Get if valid
    print(path.IsValid())
    # Get length
    print(path.Length())
    # Convert to string
    print(path.ToStr())
    print(str(path))
    # Print elements info and value
    for elem in path:
        print(elem.IsHardened())
        print(elem.ToInt())
        print(int(elem))
    # Get as list of integers
    path_list = path.ToList()
    for elem in path_list:
        print(elem)

## Bip-0044, BIP-0049, BIP-0084 libraries

These libraries derive all from the same base class, so they are used exactly in the same way.\
Therefore, the following code examples can be used with the Bip44, Bip49 or Bip84 class.\
These classes automatically use the correct elliptic curve for key derivation depending on the coin.

### Construction from a seed

A Bip class can be constructed from a seed, like Bip32. The seed can be specified manually or generated by *Bip39SeedGenerator*.

**Code example**

    import binascii
    from bip_utils import Bip44, Bip44Coins

    # Seed bytes
    seed_bytes = binascii.unhexlify(b"5eb00bbddcf069084889a8ab9155568165f5c453ccb85e70811aaed6f6da5fc19a5ac40b389cd370d086206dec8aa6c43daea6690f20ad3d8d48b2d2ce9e38e4")
    # Derivation path returned: m
    # In case it's a test net, pass True as second parameter
    bip44_ctx = Bip44.FromSeed(seed_bytes, Bip44Coins.BITCOIN)

### Construction from an extended key

Alternatively, a Bip class can be constructed directly from an extended key.\
The returned Bip object will be at the same depth of the specified key.

**Code example**

    from bip_utils import Bip44, Bip44Coins

    # Private extended key
    key_str = "xprv9s21ZrQH143K3QTDL4LXw2F7HEK3wJUD2nW2nRk4stbPy6cq3jPPqjiChkVvvNKmPGJxWUtg6LnF5kejMRNNU3TGtRBeJgk33yuGBxrMPHi"
    # Construct from extended key
    bip44_ctx = Bip44.FromExtendedKey(key_str, Bip44Coins.BITCOIN)

### Construction from a private key bytes

Finally, a Bip class can be constructed directly from a private key bytes. Like BIP32, the key will be considered a master key since there is no way to recover the key derivation data from the key bytes.\
Therefore, the returned object will have a depth equal to zero, a zero chain code and parent fingerprint.

**Code example**

    import binascii
    from bip_utils import Bip44, Bip44Coins

    # Private key bytes
    priv_key_bytes = b"e8f32e723decf4051aefac8e2c93c9c5b214313817cdb01a1494b917c8436b35"
    # Construct from private key bytes
    bip44_ctx = Bip44.FromPrivateKey(binascii.unhexlify(priv_key_bytes), Bip44Coins.BITCOIN)

### Keys derivation

Like Bip32, each time a key is derived a new instance of the Bip class is returned.\
The keys must be derived with the levels specified by BIP-0044:

    m / purpose' / coin_type' / account' / change / address_index

using the correspondent methods. If keys are derived in the wrong level, a *RuntimeError* will be raised.\
The private and public extended keys can be printed at any level.

**NOTE**: In case not-hardened private derivation is not supported (e.g. in ed25519 SLIP-0010), all indexes are hardened:

    m / purpose' / coin_type' / account' / change' / address_index'

Currently supported coins enumerative:

|Coin|Main net enum|Test net enum|
|---|---|---|
|Algorand|*Bip44Coins.ALGORAND*|-|
|Avalanche C-Chain|*Bip44Coins.AVAX_C_CHAIN*|-|
|Avalanche P-Chain|*Bip44Coins.AVAX_P_CHAIN*|-|
|Avalanche X-Chain|*Bip44Coins.AVAX_X_CHAIN*|-|
|Band Protocol|*Bip44Coins.BAND_PROTOCOL*|-|
|Binance Chain|*Bip44Coins.BINANCE_CHAIN*|-|
|Binance Smart Chain|*Bip44Coins.BINANCE_SMART_CHAIN*|-|
|Bitcoin|*Bip44Coins.BITCOIN*|*Bip44Coins.BITCOIN_TESTNET*|
|Bitcoin Cash|*Bip44Coins.BITCOIN_CASH*|*Bip44Coins.BITCOIN_CASH_TESTNET*|
|BitcoinSV|*Bip44Coins.BITCOIN_SV*|*Bip44Coins.BITCOIN_SV_TESTNET*|
|Cosmos|*Bip44Coins.COSMOS*|-|
|Dash|*Bip44Coins.DASH*|*Bip44Coins.DASH_TESTNET*|
|Dogecoin|*Bip44Coins.DOGECOIN*|*Bip44Coins.DOGECOIN_TESTNET*|
|Elrond|*Bip44Coins.ELROND*|-|
|Ethereum|*Bip44Coins.ETHEREUM*|-|
|Ethereum Classic|*Bip44Coins.ETHEREUM_CLASSIC*|-|
|Fantom Opera|*Bip44Coins.FANTOM_OPERA*|-|
|Harmony One (Cosmos address)|*Bip44Coins.HARMONY_ONE_ATOM*|-|
|Harmony One (Ethereum address)|*Bip44Coins.HARMONY_ONE_ETH*|-|
|Harmony One (Metamask address)|*Bip44Coins.HARMONY_ONE_METAMASK*|-|
|Huobi Chain|*Bip44Coins.HUOBI_CHAIN*|-|
|IRIS Network|*Bip44Coins.IRIS_NET*|-|
|Kava|*Bip44Coins.KAVA*|-|
|Kusama (ed25519 SLIP-0010)|*Bip44Coins.KUSAMA_ED25519_SLIP*|-|
|Litecoin|*Bip44Coins.LITECOIN*|*Bip44Coins.LITECOIN_TESTNET*|
|OKEx Chain (Cosmos address)|*Bip44Coins.OKEX_CHAIN_ATOM*|-|
|OKEx Chain (Ethereum address)|*Bip44Coins.OKEX_CHAIN_ETH*|-|
|OKEx Chain (Old Cosmos address before mainnet upgrade)|*Bip44Coins.OKEX_CHAIN_ATOM_OLD*|-|
|NEO|*Bip44Coins.NEO*|-|
|Ontology|*Bip44Coins.ONTOLOGY*|-|
|Polkadot (ed25519 SLIP-0010)|*Bip44Coins.POLKADOT_ED25519_SLIP*|-|
|Polygon|*Bip44Coins.POLYGON*|-|
|Ripple|*Bip44Coins.RIPPLE*|-|
|Solana|*Bip44Coins.SOLANA*|-|
|Stellar|*Bip44Coins.STELLAR*|-|
|Terra|*Bip44Coins.TERRA*|-|
|Tezos|*Bip44Coins.TEZOS*|-|
|Theta Network|*Bip44Coins.THETA*|-|
|Tron|*Bip44Coins.TRON*|-|
|VeChain|*Bip44Coins.VECHAIN*|-|
|Zcash|*Bip44Coins.ZCASH*|*Bip44Coins.ZCASH_TESTNET*|
|Zilliqa|*Bip44Coins.ZILLIQA*|-|

The library can be easily extended with other coins anyway.

**NOTES**

- *Bip44Coins.HARMONY_ONE_ETH* generates the address using the Harmony One coin index (i.e. *1023*).
This is the behavior of the official Harmony One wallet and the Ethereum address that you get in the Harmony One explorer.\
  However, if you just add the Harmony One network in Metamask, Metamask will use the Ethereum coin index (i.e. *60*) thus resulting in a different address.
Therefore, if you need to generate the Harmony One address for Metamask, use *Bip44Coins.HARMONY_ONE_METAMASK*.
- *Bip44Coins.OKEX_CHAIN_ETH* and *Bip44Coins.OKEX_CHAIN_ATOM* generate the address using the Ethereum coin index (i.e. *60*).
These formats are the ones used by the OKEx wallet. *Bip44Coins.OKEX_CHAIN_ETH* is compatible with Metamask.\
*Bip44Coins.OKEX_CHAIN_ATOM_OLD* generates the address using the OKEx Chain coin index (i.e. *996*).
  This address format was used before the mainnet upgrade (some wallets still use it, e.g. Cosmostation).

**Code example**

    import binascii
    from bip_utils import Bip44, Bip44Coins, Bip44Changes

    # Seed bytes
    seed_bytes = binascii.unhexlify(b"5eb00bbddcf069084889a8ab9155568165f5c453ccb85e70811aaed6f6da5fc19a5ac40b389cd370d086206dec8aa6c43daea6690f20ad3d8d48b2d2ce9e38e4")
    # Create from seed
    bip44_mst = Bip44.FromSeed(seed_bytes, Bip44Coins.BITCOIN)

    # Print master key in extended format
    print(bip44_mst.PrivateKey().ToExtended())
    # Print master key in hex format
    print(bip44_mst.PrivateKey().Raw().ToHex())

    # Print public key in extended format (default: Bip44PubKeyTypes.EXT_KEY)
    print(bip44_mst.PublicKey())
    # Print public key in raw uncompressed format
    print(bip44_mst.PublicKey().RawUncompressed().ToHex())
    # Print public key in raw compressed format
    print(bip44_mst.PublicKey().RawCompressed().ToHex())

    # Print the master key in WIF
    print(bip44_mst.IsMasterLevel())
    print(bip44_mst.PrivateKey().ToWif())

    # Derive account 0 for Bitcoin: m/44'/0'/0'
    bip44_acc = bip44_mst.Purpose() \
                         .Coin()    \
                         .Account(0)
    # Print keys in extended format
    print(bip44_acc.IsAccountLevel())
    print(bip44_acc.PrivateKey().ToExtended())
    print(bip44_acc.PublicKey().ToExtended())
    # Address of account level
    print(bip44_acc.PublicKey().ToAddress())

    # Derive the external chain: m/44'/0'/0'/0
    bip44_change = bip44_acc.Change(Bip44Changes.CHAIN_EXT)
    # Print again keys in extended format
    print(bip44_change.IsChangeLevel())
    print(bip44_change.PrivateKey().ToExtended())
    print(bip44_change.PublicKey().ToExtended())
    # Address of change level
    print(bip44_change.PublicKey().ToAddress())

    # Derive the first 20 addresses of the external chain: m/44'/0'/0'/0/i
    for i in range(20):
        bip44_addr = bip44_change.AddressIndex(i)
        # Print extended keys and address
        print(bip44_addr.PrivateKey().ToExtended())
        print(bip44_addr.PublicKey().ToExtended())
        print(bip44_addr.PublicKey().ToAddress())

In the example above, Bip44 can be substituted with Bip49 or Bip84 without changing the code.

### Default derivation paths

Most of the coins (especially the ones using the secp256k1 curve) use the complete BIP-0044 path to derive the address private key:

    m / purpose' / coin_type' / account' / change / address_index

However, this doesn't apply all coins. For example, Solana uses the following path to derive the address private key: m/44'/501'/0'\
This can be derived manually, for example:

    # Derive m/44'/501'/0'
    bip44_mst = Bip44.FromSeed(seed_bytes, Bip44Coins.SOLANA)
    bip44_acc = bip44_mst.Purpose().Coin().Account(0)
    # Default address generated by the wallet (e.g. TrustWallet): m/44'/501'/0'
    print(bip44_acc.PublicKey().ToAddress())

However, in order to avoid remembering the default path for each coin, the *DeriveDefaultPath* method can be used to automatically derive the default path:

    # Automatically derive m/44'/501'/0'
    bip44_def = Bip44.FromSeed(seed_bytes, Bip44Coins.SOLANA).DeriveDefaultPath()
    # Same as before
    print(bip44_def.PublicKey().ToAddress())

    # Automatically derive m/44'/3'/0'/0/0
    bip44_def = Bip44.FromSeed(seed_bytes, Bip44Coins.DOGECOIN).DeriveDefaultPath()
    # Same as before
    print(bip44_def.PublicKey().ToAddress())

## Addresses generation

These libraries are used internally by the other libraries, but they are available also for external use.

**Code example**

    import binascii
    from bip_utils import (
      P2PKHAddr, P2SHAddr, P2WPKHAddr, BchP2PKHAddr, BchP2SHAddr, AtomAddr, AvaxPChainAddr, AvaxXChainAddr,
      EgldAddr, EthAddr, NeoAddr, OkexAddr, OneAddr, SolAddr, TrxAddr, XlmAddr, XrpAddr, XtzAddr,
      Ed25519PublicKey, Nist256p1PublicKey, Secp256k1PublicKey
    )

    #
    # Coins that require a secp256k1 curve
    #

    # You can use public key bytes or a public key object
    pub_key = binascii.unhexlify(b"022f469a1b5498da2bc2f1e978d1e4af2ce21dd10ae5de64e4081e062f6fc6dca2")
    pub_key = Secp256k1PublicKey(binascii.unhexlify(b"022f469a1b5498da2bc2f1e978d1e4af2ce21dd10ae5de64e4081e062f6fc6dca2"))

    # P2PKH address (the default uses Bitcoin network address version, you can pass a different one as second parameter)
    addr = P2PKHAddr.EncodeKey(pub_key)
    # P2SH address (the default uses Bitcoin network address version, you can pass a different one as second parameter)
    addr = P2SHAddr.EncodeKey(pub_key)
    # P2WPKH address (the default uses Bitcoin network address version, you can pass a different one as second parameter)
    addr = P2WPKHAddr.EncodeKey(pub_key)

    # P2PKH address in Bitcoin Cash format
    addr = BchP2PKHAddr.EncodeKey(pub_key, "bitcoincash", b"\x00")
    # P2SH address in Bitcoin Cash format
    addr = BchP2SHAddr.EncodeKey(pub_key, "bitcoincash", b"\x00")

    # Ethereum address
    addr = EthAddr.EncodeKey(pub_key)
    # Tron address
    addr = TrxAddr.EncodeKey(pub_key)
    # AVAX address
    addr = AvaxPChainAddr.EncodeKey(pub_key)
    addr = AvaxXChainAddr.EncodeKey(pub_key)
    # Atom addresses
    addr = AtomAddr.EncodeKey(pub_key, "cosmos")
    addr = AtomAddr.EncodeKey(pub_key, "band")
    addr = AtomAddr.EncodeKey(pub_key, "kava")
    # OKEx Chain address
    addr = OkexAddr.EncodeKey(pub_key)
    # Harmony One address
    addr = OneAddr.EncodeKey(pub_key)
    # Ripple address
    addr = XrpAddr.EncodeKey(pub_key)
    # Zilliqa address
    addr = ZilAddr.EncodeKey(pub_key)

    #
    # Coins that require a ed25519 curve
    #

    # You can use public key bytes or a public key object
    pub_key = binascii.unhexlify(b"00dff41688eadfb8574c8fbfeb8707e07ecf571e96e929c395cc506839cc3ef832")
    pub_key = Ed25519PublicKey(binascii.unhexlify(b"00dff41688eadfb8574c8fbfeb8707e07ecf571e96e929c395cc506839cc3ef832"))

    # Algorand address
    addr = AlgoAddr.EncodeKey(pub_key)
    # Elrond address
    addr = EgldAddr.EncodeKey(pub_key)

    # Solana address
    addr = SolAddr.EncodeKey(pub_key)
    # Stellar address
    addr = XlmAddr.EncodeKey(pub_key)
    # Substrate address
    addr = SubstrateEd25519Addr.EncodeKey(pub_key, b"\x00")
    # Tezos address
    addr = XtzAddr.EncodeKey(pub_key)

    #
    # Coins that require a nist256p1 curve
    #

    # You can use public key bytes or a public key object
    pub_key = binascii.unhexlify(b"038ea003d38b3f2043e681f06f56b3864d28d73b4f243aee90ed04a28dbc058c5b")
    pub_key = Nist256p1PublicKey(binascii.unhexlify(b"038ea003d38b3f2043e681f06f56b3864d28d73b4f243aee90ed04a28dbc058c5b"))

    # NEO address
    addr = NeoAddr.EncodeKey(pub_key)

## WIF

This library is used internally by the other libraries, but it's available also for external use.

**Code example**

    import binascii
    from bip_utils import Secp256k1PublicKey, WifDecoder, WifEncoder

    # You can use private key bytes or a private key object
    priv_key = binascii.unhexlify(b'1837c1be8e2995ec11cda2b066151be2cfb48adf9e47b151d46adab3a21cdf67')
    priv_key = Secp256k1PublicKey(binascii.unhexlify(b'1837c1be8e2995ec11cda2b066151be2cfb48adf9e47b151d46adab3a21cdf67'))

    # Encode
    enc = WifEncoder.Encode(priv_key)
    # Decode
    dec = WifDecoder.Decode(enc)

## Base58

This library is used internally by the other libraries, but it's available also for external use.\
It supports both normal encode/decode and check_encode/check_decode with Bitcoin and Ripple alphabets (if not specified, the Bitcoin one will be used by default).

**Code example**

    import binascii
    from bip_utils import Base58Decoder, Base58Encoder, Base58Alphabets

    data_bytes = binascii.unhexlify(b"636363")

    # Normal encode
    enc     = Base58Encoder.Encode(data_bytes)
    # Check encode
    chk_enc = Base58Encoder.CheckEncode(data_bytes)

    # Normal decode
    dec     = Base58Decoder.Decode(enc)
    # Check decode, RuntimeError is raised if checksum verification fails
    chk_dec = Base58Decoder.CheckDecode(chk_enc)

    # Same as before with Ripple alphabet
    enc     = Base58Encoder.Encode(data_bytes, Base58Alphabets.RIPPLE)
    chk_enc = Base58Encoder.CheckEncode(data_bytes, Base58Alphabets.RIPPLE)
    dec     = Base58Decoder.Decode(enc, Base58Alphabets.RIPPLE)
    chk_dec = Base58Decoder.CheckDecode(chk_enc, Base58Alphabets.RIPPLE)

## Bech32

This library is used internally by the other libraries, but it's available also for external use.

**Code example**

    import binascii
    from bip_utils import (
        Bech32Decoder, Bech32Encoder BchBech32Encoder, BchBech32Decoder, SegwitBech32Decoder, SegwitBech32Encoder
    )

    data_bytes = binascii.unhexlify(b'9c90f934ea51fa0f6504177043e0908da6929983')

    # Encode with bech32
    enc = Bech32Encoder.Encode("cosmos", data_bytes)
    # Decode with bech32
    dec = Bech32Decoder.Decode("cosmos", enc)

    # Encode with segwit bech32
    enc = SegwitBech32Encoder.Encode("bc", 0, data_bytes)
    # Decode with segwit bech32
    wit_ver, wit_prog = SegwitBech32Decoder.Decode("bc", enc)

    # Encode with BCH bech32
    enc = BchBech32Encoder.Encode("bitcoincash", b"\x00", data_bytes)
    # Decode with BCH bech32
    net_ver, dec = BchBech32Decoder.Decode("bitcoincash", enc)

## SS58

This library is used internally by the other libraries, but it's available also for external use.\
It allows encoding/deconding in SS58 format (1-byte version, 2-byte checksum).

**Code example**

    import binascii
    from bip_utils import SS58Decoder, SS58Encoder

    data_bytes = binascii.unhexlify(b"e92b4b43a62fa66293f315486d66a67076e860e2aad76acb8e54f9bb7c925cd9")

    # Encode
    enc = SS58Encoder.Encode(data_bytes, b"\x00")
    # Decode
    version, dec = SS58Decoder.Decode(enc)

## Complete code example

Example from mnemonic generation to wallet addresses.

    from bip_utils import Bip39MnemonicGenerator, Bip39SeedGenerator, Bip44, Bip44Coins, Bip44Changes

    # Generate random mnemonic
    mnemonic = Bip39MnemonicGenerator.FromWordsNumber(12)
    print("Mnemonic string: %s" % mnemonic)
    # Generate seed from mnemonic
    seed_bytes = Bip39SeedGenerator(mnemonic).Generate()

    # Generate BIP44 master keys
    bip_obj_mst = Bip44.FromSeed(seed_bytes, Bip44Coins.BITCOIN)
    # Print master key
    print("Master key (bytes): %s" % bip_obj_mst.PrivateKey().Raw().ToHex())
    print("Master key (extended): %s" % bip_obj_mst.PrivateKey().ToExtended())
    print("Master key (WIF): %s" % bip_obj_mst.PrivateKey().ToWif())

    # Generate BIP44 account keys: m/44'/0'/0'
    bip_obj_acc = bip_obj_mst.Purpose().Coin().Account(0)
    # Generate BIP44 chain keys: m/44'/0'/0'/0
    bip_obj_chain = bip_obj_acc.Change(Bip44Changes.CHAIN_EXT)

    # Generate the address pool (first 20 addresses): m/44'/0'/0'/0/i
    for i in range(20):
        bip_obj_addr = bip_obj_chain.AddressIndex(i)
        print("%d. Address public key (extended): %s" % (i, bip_obj_addr.PublicKey().ToExtended()))
        print("%d. Address private key (extended): %s" % (i, bip_obj_addr.PrivateKey().ToExtended()))
        print("%d. Address: %s" % (i, bip_obj_addr.PublicKey().ToAddress()))

# Donations

If you'd like to donate something:
- BTC: bc1qxr3camglhmrcl5uhs2m5hmaxmrxf47krs3fzpm
- ETH: 0xd059eA7259367512fFC7269B9beD4A45f13bb40b

Thank you very much in advance for your support.

# License

This software is available under the MIT license.
