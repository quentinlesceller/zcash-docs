# Zcash 1.0 "Sprout" Guide

Welcome! This guide is intended to get you running on the official Zcash network. Zcash currently has some limitations: it only officially supports Linux, requires 64-bit, and in some situations requires heavy memory and CPU consumption to create transactions.

Please let us know if you run into snags. We plan to make it less memory/CPU intensive and support more architectures and operating systems in the future.

## Upgrading?

If you were playing with our alpha/beta/rc testnets, ensure that your `~/.zcash/zcash.conf` does not contain `testnet=1` or `addnode=betatestnet.z.cash`. If you're on a Debian-based distribution, you can follow the [Debian instructions](https://github.com/zcash/zcash/wiki/Debian-binary-packages) to install zcash on your system. Otherwise, you can update your local snapshot of our code:

```
git fetch origin
git checkout v1.0.3
./zcutil/fetch-params.sh
./zcutil/build.sh -j$(nproc)
```

Also make sure that your ``~/.zcash`` directory contains only ``zcash.conf`` to start with.

## A quick note about terminology

Zcash supports two different kinds of addresses, a _z-addr_ (which begins with a **z**) is an address that uses zero-knowledge proofs and other cryptography to protect user privacy. There are also _t-addrs_ (which begin with a **t**) that are similar to Bitcoin's addresses.

## Requirements

Currently, you will need:

* Linux (easiest with a Debian-based distribution)
* 64-bit
* 4GB of free memory

The interfaces are a commandline client (`zcash-cli`) and a Remote Procedure Call (RPC) interface, which is documented here:

https://github.com/zcash/zcash/blob/v1.0.3/doc/payment-api.md

## Security

Before installing, upgrading, or running zcash, ensure you have checked
for any security issues. Please See our Security page:

https://z.cash/support/security.html

## Get started

### Debian-based operating systems

Follow the instructions here: https://github.com/zcash/zcash/wiki/Debian-binary-packages

### Compile it yourself

On Ubuntu/Debian-based systems:

```bash
$ sudo apt-get install \
      build-essential pkg-config libc6-dev m4 g++-multilib \
      autoconf libtool ncurses-dev unzip git python \
      zlib1g-dev wget bsdmainutils automake
```

On Fedora-based systems:

```bash
$ sudo dnf install \
      git pkgconfig automake autoconf ncurses-devel python wget \
      gtest-devel gcc gcc-c++ libtool patch
```

Fetch our repository with git and run `fetch-params.sh` like so:

```bash
$ git clone https://github.com/zcash/zcash.git
$ cd zcash/
$ git checkout v1.0.3
$ ./zcutil/fetch-params.sh
```

This will fetch our Sprout proving and verifying keys (the final ones created in the [Parameter Generation Ceremony](https://github.com/zcash/mpc)), and place them into `~/.zcash-params/`. These keys are just under 911MB in size, so it may take some time to download them.

The message printed by ``git checkout`` about a "detached head" is normal and does not indicate a problem.

#### Build

Ensure you have successfully installed all system package dependencies as described above. Then run the build, e.g.:

```bash
$ ./zcutil/build.sh -j$(nproc)
```

This should compile our dependencies and build `zcashd`. (Note: if you don't have `nproc`, then substitute the number of your processors.)

#### Testing

The tests take a while to run and may require up to 8GB of RAM. If you would rather get started right away, you can skip to the next section. If you want to run the tests to make sure Zcash is working, run:

```bash
$ ./qa/zcash/full-test-suite.sh
```

You can also run the RPC tests, which take much longer:

```bash
$ ./qa/pull-tester/rpc-tests.sh
```

The tests need a lot of memory to run successfully. An out-of-memory error will usually cause a FAIL or ERROR outcome with "std::bad_alloc" somewhere in the output.

## Configuration

Create the `~/.zcash` directory and place a configuration file at `~/.zcash/zcash.conf` using the following commands:

```bash
mkdir -p ~/.zcash
echo "addnode=mainnet.z.cash" >~/.zcash/zcash.conf
echo "rpcuser=username" >>~/.zcash/zcash.conf
echo "rpcpassword=`head -c 32 /dev/urandom | base64`" >>~/.zcash/zcash.conf
```

Note that this will overwrite any `zcash.conf` settings you may have added from testnet. You can retain a `zcash.conf` from testnet, but make sure that the `testnet=1` and `addnode=betatestnet.z.cash` settings are removed; use `addnode=mainnet.z.cash` instead. We strongly recommend that you use a random password to avoid [potential security issues with access to the RPC interface](https://github.com/zcash/zcash/blob/master/doc/security-warnings.md#rpc-interface).

### Enabling CPU mining:

If you want to enable CPU mining, run these commands:

```bash
$ echo 'gen=1' >> ~/.zcash/zcash.conf
$ echo "genproclimit=$(nproc)" >> ~/.zcash/zcash.conf
```

The default miner is not efficient, but has been well reviewed. To use a much more efficient but unreviewed solver, you can run this command:

```bash
$ echo 'equihashsolver=tromp' >> ~/.zcash/zcash.conf
```

Note, you probably want to read the [[Mining-Guide]] to learn more mining details.

## Running Zcash:

Now, run zcashd!

```bash
$ ./src/zcashd
```

To run it in the background (without the node metrics screen that is normally displayed) use ``./src/zcashd --daemon``.

You should be able to use the RPC after it finishes loading. Here's a quick way to test:

```bash
$ ./src/zcash-cli getinfo
```

**NOTE**: If you are familiar with bitcoind's RPC interface, you can use many of those calls to send ZEC between `t-addr` addresses. We do not support the 'Accounts' feature (which has also been deprecated in ``bitcoind``) — only the empty string ``""`` can be used as an account name.

**NOTE**: The main network node at mainnet.z.cash is also accessible via Tor hidden service at zcmaintvsivr7pcn.onion.

To see the peers you are connected to:

```bash
$ ./src/zcash-cli getpeerinfo
```

## Using Zcash

First, you want to obtain Zcash. You can purchase them from an exchange, from other users, or sell goods and services for them! Exactly how to obtain Zcash (safely) is not in scope for this document, but you should be careful. Avoid scams!

### Generating a t-addr

Let's generate a t-addr first.

```bash
$ ./src/zcash-cli getnewaddress
tb4oHp2v54vfmdgQ3v3SNuQga8JKHTNi2a1
```

### Receiving Zcash with a z-addr

Now let's generate a z-addr.

```bash
$ ./src/zcash-cli z_getnewaddress
ztbqWB8VDjVER7uLKb4oHp2v54v2a1jKd9o4FY7mdgQ3gDfG8MiZLvdQga8JK3t58yjXGjQHzMzkGUxSguSs6ZzqpgTNiZG
```

This creates a private address and stores its key in your local wallet file. Give this address to the sender!

A z-addr is pretty large, so it's easy to make mistakes with them. Let's put it in an environment variable to avoid mistakes:

```bash
$ ZADDR='ztbqWB8VDjVER7uLKb4oHp2v54v2a1jKd9o4FY7mdgQ3gDfG8MiZLvdQga8JK3t58yjXGjQHzMzkGUxSguSs6ZzqpgTNiZG'
```

To get a list of all addresses in your wallet for which you have a spending key, run this command:

```bash
$ ./src/zcash-cli z_listaddresses
```

You should see something like:
```json
[
    "zta6qngiR3U7HxYopyTWkaDLwYBd83D5MT7Jb9gpgTzPLMZytzRbtdPP1Syv4RvRgHeoZrJWSask3DyfwXG9DGPMWMvX7aC",
    "ztbqWB8VDjVER7uLKb4oHp2v54v2a1jKd9o4FY7mdgQ3gDfG8MiZLvdQga8JK3t58yjXGjQHzMzkGUxSguSs6ZzqpgTNiZG"
]
```

Great! Now, send your z-addr to the sender. You should eventually see their transaction when checking:

```bash
$ ./src/zcash-cli z_listreceivedbyaddress "$ZADDR"
```
```json
[
    {
        "txid" : "af1665b317abe538148114a45322f28151925501c081949cc7a5207ef21cb750",
        "amount" : 1.23,
        "memo" : "48656c6c6f20ceb2210000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"
    }
]
```

### Sending coins with your z-addr

If someone gives you their z-addr...

```bash
$ FRIEND='ztcDe8krwEt1ozWmGZhBDWrcUfmK3Ue5D5z1f6u2EZLLCjQq7mBRkaAPb45FUH4Tca91rF4R1vf983ukR71kHyXeED4quGV'
```

You can send 0.8 ZEC by doing...

```bash
$ ./src/zcash-cli z_sendmany "$ZADDR" "[{\"amount\": 0.8, \"address\": \"$FRIEND\"}]"
```

After waiting about a minute, you can check to see if the operation has finished and produced a result:
```bash
$ ./src/zcash-cli z_getoperationresult
```
```json
[
    {
        "id" : "opid-4eafcaf3-b028-40e0-9c29-137da5612f63",
        "status" : "success",
        "creation_time" : 1473439760,
        "result" : {
            "txid" : "3b85cab48629713cc0caae99a49557d7b906c52a4ade97b944f57b81d9b0852d"
        },
        "execution_secs" : 51.64785629
    }
]
```


## Known Security Issues

Each release contains a `./doc/security-warnings.md` document describing
security issues known to affect that release. You can find the most
recent version of this document here:

https://github.com/zcash/zcash/blob/master/doc/security-warnings.md

Please also see our security page for recent notifications and other
resources:

https://z.cash/support/security.html
