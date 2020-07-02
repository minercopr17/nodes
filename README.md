# Feature

* Fully self-sufficient
* Minimal memory and disk usage (in the 10s of megabytes vs. gigabytes)
* Hot-swap with live node daemons, no restart required
* Select any IP address, no static IP required
* Auto-load settings from a coinconf.json 
* Optionally auto-load bootstrap hashes and peers from Iquidus explorers (more APIs coming soon)
* Epoch timestamp support for high-availability deterministic pings
* Use your existing masternode.conf if you don't want deterministic pings
* Runs on windows, linux, mac, arm, and more.

# Quick start

Download a binary release from below. See if there's a coin configuration for the coin you're wishing to use. If not, you'll need to locate the proper settings. There are notes below on where to look or feel free to ask on discord, reddit, or btct. If there is a coin conf for your coin then switching over to phantoms is easy:

```
./phantom -coin_conf="/path/to/coin.conf" -masternode_conf="/path/to/masternode.conf"
```

That's it. You do not need to restart your masternodes, you don't need to change IP addresses, etc. Once the phantom daemon is running, you can disable your masternode daemons, cancel most of VPS subscriptions, and enjoy the savings. You'll know the phantoms are working when you see the active time refresh (can take up to 20 minutes). If that active time doesn't update, restart your daemons and check the settings.

# Setup 

The setup is simple: copy your masternode.conf, modify it slightly, launch the phantom executable.

## Masternode.txt setup

Copy your masternode.conf to the same folder as the phantom executable. Rename it to masternode.txt. Remove any comment lines from the top of the file (i.e. delete any line starting with #). At the end of each line add a epoch time ( https://www.unixtimestamp.com ). The epoch timestamp is utilized to allow you to run multiple phantom node setups in a deterministic manner, creating a highly-available configuration.

**Example**

`masternode.conf`
```
# Masternode config file
# Format: alias IP:port masternodeprivkey collateral_output_txid collateral_output_index
mn1 45.50.22.125:17817 73HaYBVUCYjEMeeH1Y4sBGLALQZE1Yc1K64xiqgX37tGBDQL8Xg 2bcd3c84c84f87eaa86e4e56834c92927a07f9e18718810b92e0d0324456a67c 1
```

becomes:

`masternode.txt`
```
mn1 45.50.22.125:17817 73HaYBVUCYjEMeeH1Y4sBGLALQZE1Yc1K64xiqgX37tGBDQL8Xg 2bcd3c84c84f87eaa86e4e56834c92927a07f9e18718810b92e0d0324456a67c 1 1555847365
```

comments removed, epoch timestamp added to the end.

## Run the phantom executable

```
./phantom -magicbytes="E4D2411C" -port=1929 -protocol_number=70209 -magic_message="ProtonCoin Signed Message:" -bootstrap_ips="51.15.236.48:1929" -bootstrap_url="http://explorer.anodoscrypto.com:3001" -max_connections=10
```

## PIVX based coins

If you are launching a new node, not performing a hotswap, due to the way PIVX coins relay information, a special start-up flag is required ```-broadcast_listen```. You must start the phantom daemon, let it gather up a few peers, and then press start from your wallet.

## Coin configurations

Check the /config folder

There is a coinconf generator included that can auto-generate settings for most masternode coins. Check the `tools/coinconf` directory.

## Available Flags

```
  -bootstrap_hash string
    	Hash to bootstrap the pings with ( top - 12 )
  -bootstrap_ips string
    	IP addresses to bootstrap the network (i.e. "1.1.1.1:1234,2.2.2.2:1234")
  -bootstrap_url string
    	Explorer to bootstrap from.
  -broadcast_listen
    	If set to true, the phantom will listen for new broadcasts and cache them for 4 hours.
  -coin_conf string
    	Name of the file to load the coin information from.
  -daemon_version string
    	The string to use for the sentinel version number (i.e. 1.20.0)
  -magic_message string
    	the signing message
  -magic_message_newline
    	add a new line to the magic message (default true)
  -magicbytes string
    	a hex string for the magic bytes
  -masternode_conf string
    	Name of the file to load the masternode information from. (default "masternode.txt")
  -max_connections uint
    	the number of peers to maintain (default 10)
  -port uint
    	the default port number
  -protocol_number uint
    	the protocol number to connect and ping with
  -sentinel_version string
    	The string to use for the sentinel version number (i.e. 1.20.0)
  -user_agent string
    	The user agent string to connect to remote peers with. (default "@_breakcrypto phantom")
```

## Building (using Docker)

```
docker run --rm -it -v "$PWD":/go/src/phantom -w /go/src/phantom golang:1.12.4 ./build.sh 
```