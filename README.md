![GQ71wcXXEAEO73H](https://github.com/0xmoei/autonity-validator/assets/90371338/48065208-50f6-4e16-ad15-bf3650dd972e)
# Autonity Incentivized Validator (Phase/Game R6)
> * Round 6 is open for everyone!
> 
> * Start Time : June 26th 2024 00:00:00 UTC
> 
> * Duration : 6 weeks
>
> * Game 6 [Link](https://game.autonity.org/round-6/)
> 
> * 120 winners for Validator, [more info](https://game.autonity.org/round-6/validator-sdp/)
> 
> * 50 winners for Public RPC Node, [more info](https://game.autonity.org/round-6/node-open-door/)
>
> There are more tasks for this round E.g. "Community Incentives" ( Like + Retweet), Check them all [here](https://game.autonity.org/round-6/)
>
> ![Screenshot_5](https://github.com/0xmoei/autonity-validator/assets/90371338/d01d5766-1562-440a-9355-371e3b0817c1)

#

## We’ll cover in this tutorial:
* Validator Node Task
* RPC Node Task
> I prefer Validator but If you want to register both of them, you need 2 seprate VPS servers

## Useful links
* Autonity [Discord](https://discord.gg/autonity)
* Validators [Stakeflow Dashboard](https://stakeflow.io/autonity-piccadilly/validators)
* Game6 [Leaderboard](https://game.autonity.org/statistics/leaderboard.html)

# Validator Installation

## 1- Install Dependecies
### 1.1- Install libraries
```console
sudo apt update && sudo apt upgrade -y

apt install curl iptables build-essential git wget jq make gcc nano tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip chrony libleveldb-dev liblz4-tool -y
```
### 1.2- Install Golang
```console
# remove old versions
sudo apt-get remove golang-go
sudo apt-get remove --auto-remove golang-go
cd $HOME && rm -rf go

# install
VERSION="1.21.6"
ARCH="amd64"
curl -O -L "https://golang.org/dl/go${VERSION}.linux-${ARCH}.tar.gz"
wget -L "https://golang.org/dl/go${VERSION}.linux-${ARCH}.tar.gz"
wget -L "https://golang.org/dl/go${VERSION}.linux-${ARCH}.tar.gz"
curl -sL https://golang.org/dl/ | grep -A 5 -w "go${VERSION}.linux-${ARCH}.tar.gz"
tar -xf "go${VERSION}.linux-${ARCH}.tar.gz"
sudo chown -R root:root ./go
sudo mv -v go /usr/local
export GOPATH=$HOME/go
export PATH=$PATH:/usr/local/go/bin:$GOPATH/bin
source ~/.bash_profile
go version
```

### 1.3- Install ethkey (to pull-out private key from seed files)
```console
git clone https://github.com/autonity/autonity.git autonity1
cd autonity1
make all
mv build/bin/ethkey /usr/local/bin

ethkey --version
```
> Important:

> #Only if you have problems installing aut or other binaries, use these below commands or pass it.

```console
export CGO_CFLAGS="-O -D__BLST_PORTABLE__" 
export CGO_CFLAGS_ALLOW="-O -D__BLST_PORTABLE__"
make clean
```

## 2. Install Autonity Utility (Aut)
### 2.1 install Python3
```console
cd
sudo apt install python3-pip && sudo apt install python3.10-venv && sudo pip install pipx
```

### 2.2 Install Aut
```console
pipx install --force git+https://github.com/autonity/aut
mv /root/.local/bin/aut /usr/local/bin/aut

aut --version
# aut, version 0.4.0
```

### 2.2 Edit .autrc file
```console
tee <<EOF >/dev/null $HOME/.autrc
[aut]
rpc_endpoint= ws://127.0.0.1:8546
EOF
```

## 3. Install Autonity RPC Node
> Recommended Specifications
![Screenshot_1](https://github.com/0xmoei/autonity-validator/assets/90371338/5e6735de-cbdc-4c1d-b176-a4a440340c6d)

### 3.1 Open ports
```console
sudo ufw allow 8545 && sudo ufw allow 8546 && sudo ufw allow 6060 && sudo ufw allow 30303
```

### 3.2 Install Autonity
```console
cd
git clone https://github.com/autonity/autonity && cd autonity
git checkout tags/v0.14.0 -b v0.14.0
make autonity
mv $HOME/autonity/build/bin/autonity /usr/local/bin/

autonity version
# Version: 0.14.0
```

### 3.3 Create Keys
```console
cd
mkdir -p $HOME/autonity-chaindata/autonity
```
> We need to create nodekey, tresure.key and oracle.key
>
> nodekey is the main key of the node, from which the validator address and enode are formed
>
> tresure.key will be our treasury. Using this key we will register task forms, receive faucet, delegate to the validator and we will receive rewards for this key
>
> oracle.key will be used as the cryptographic identifier of the Oracle server and will be used to sign price report transactions sent to Oracle Contract on-chain
>
> Before we Deploy Oracle Node, we need to Fund our oracle.key with at least 0.1 $ATN

### 3.4 Generate nodekey
```console
autonity genAutonityKeys $HOME/autonity-chaindata/autonity/nodekey --writeaddress

# Node address: 0xac16454f6D7B5725F4221f86D935963C87Dae7F5
# Node public key:0x1988dee846c92a7a95b5ddc62a808f6a34eb13aa567bcc9caf6ef7a5
# Consensus public key:0x87f764f47664b635290ffaa567bcc9caf6ef7a51116cafc7c0
```

### 3.5 Create a separate directory for tresure.key and oracle.key
```console
mkdir -p $HOME/.autonity/keystore
```

### 3.6 Create an oracle wallet and save the created file
```console
aut account new -k $HOME/.autonity/keystore/oracle.key
```

### 3.7 Create a tresure wallet and save the created file
```console
aut account new -k $HOME/.autonity/keystore/tresure.key
```

> If you are an existing user of Game R5 you need to import your last round tresurekey which was registered in the R4 form
>
> Make a `wallet.priv` file in your root directory and paste your previous wallet `privatekey` in it
```console
aut account import-private-key ./wallet.priv -k $HOME/.autonity/keystore/tresure.key
```
> IMPORTANT: save your key files, addresses and your passphrase in a safe place

### 3.8 Add tresurekey to .autrc file
```console
nano $HOME/.autrc
```
add this in the next line `keyfile=/root/.autonity/keystore/tresure.key`

`Ctrl+X+Y` `Enter`

### 3.9 Create your Node service file
```console
tee  << EOF > /dev/null /etc/systemd/system/autonity.service
[Unit]
Description=autonity node
After=network.target
[Service]
User= $USER
Type=simple
ExecStart= $( which autonity ) --datadir $HOME/autonity-chaindata --syncmode full --piccadilly --http --http.addr 0.0.0.0 --http.api aut,eth,net,txpool,web3,admin --http.vhosts * --ws --ws.addr 127.0.0.1 --ws.api aut,eth,net,txpool,web3,admin --autonitykeys $HOME/autonity-chaindata/autonity/nodekey --nat extip:$( wget -qO- eth0.me )
Restart=on-failure
LimitNOFILE=65535
--bootnodes enode://48a10db920251436ee1d7989db6cbab734157d5bd3ec9d534021e4903fdab51407ba4fd936bd6af1d188e3f464374c437accefa40f0312eac9bc9ae6fc0a2782@34.105.239.129:30303
[Install]
WantedBy=multi-user.target
EOF
```

### 3.10 Run your Autonity RPC Node
```console
systemctl daemon-reload
systemctl enable autonity
sudo systemctl restart autonity && journalctl -u autonity -f  -o  cat
```
![Screenshot_2](https://github.com/0xmoei/autonity-validator/assets/90371338/07171264-1ef3-4220-afaa-113266420d7d)
To exit: `Ctrl+C`

## 4- FORM 1 - REGISTRATION
> IMPORTANT - register only if you did not participate in round 4 (R4)
>
> Now is the time to register your participation in the test network to receive test tokens. To do this, go to the website and fill out the data - https://game.autonity.org/getting-started/register.html
>
> In the autonity address field , insert the tresure address

In the Signature hash field , insert the hash of the signed transaction after the following command:
```console
aut account sign-message "I have read and agree to comply with the Piccadilly Circus Games Competition Terms and Conditions published on IPFS with CID QmVghJVoWkFPtMBUcCiqs7Utydgkfe19wkLunhS5t57yEu"
```

> After registration, you will receive a confirmation email and tokens should be credited to your balance. You can check your balance in Explorer or with the command:

```console
aut account info
```

## 5- Install Oracle
> Recommended Specifications
> 
![Screenshot_3](https://github.com/0xmoei/autonity-validator/assets/90371338/214c05a4-24f4-41e8-97d8-83ae40e1b432)

### 5.1 Install
```console
cd
git clone https://github.com/autonity/autonity-oracle && cd autonity-oracle
git fetch --all 
git checkout v0.1.9 
make autoracle
mv build/bin/autoracle /usr/local/bin

autoracle version
# v0.1.9
```

### 5.2 Edit Plugin-conf.yml
```console
nano $HOME/autonity-oracle/build/bin/plugins-conf.yml
```
* You now need to remove # from bottom lines, Register in the data-feed websites and add your custom API to the lines
![Screenshot_4](https://github.com/0xmoei/autonity-validator/assets/90371338/ec69e87e-1d20-47e8-96be-96ac3aaf75da)

### Websites:
* https://currencyfreaks.com
* https://openexchangerates.org
* https://currencylayer.com (this one needs card number & address, you can skip it and leave it with #)
* https://www.exchangerate-api.com

### 5.3 Fund oracle.key
Add oracle.key privatekey to metamask
```console
ethkey inspect --private /root/.autonity/keystore/oracle.key
```

Add tresure.key privatekey to metamask
```console
ethkey inspect --private /root/.autonity/keystore/tresure.key
```

### Add Piccadilly Network to Metamask
> ChainID: 65100003
> 
> Network Name:Autonity Piccadilly (Barada) Testnet
>
> New RPC URL: https://rpc1.piccadilly.autonity.org/
>
> Symbol: ATN - for transactions
>
> Symbol: NTN - for delegation to validator
>
> Explorer: https://piccadilly.autonity.org/

> If you have problem adding official rpc to your metamask, you can add your own node rpc to metamask
>
> Your node rpc is: `http://YourIP:8545`

Send 0.1 ATN from tresure.key to oracle.key

### 5.4 Create service Oracle Node service file
change `PASSWORD` in -key.password argument and run the command
```console
tee <<EOF >/dev/null /etc/systemd/system/antoracle.service
[Unit]
Description=Autonity Oracle Server
After=syslog.target network.target
[Service]
Type=simple
ExecStart=$(which autoracle) -key.file="/root/.autonity/keystore/oracle.key" -plugin.dir="/root/autonity-oracle/build/bin/plugins/" -plugin.conf="/root/autonity-oracle/build/bin/plugins-conf.yml" -key.password="PASSWORD" -ws="ws://127.0.0.1:8546"
Restart=on-failure
RestartSec=5
[Install]
WantedBy=multi-user.target
EOF
```

### 5.4 Run Oracle
```console
systemctl daemon-reload
systemctl enable antoracle
systemctl restart antoracle && journalctl -u antoracle -f  -o  cat
```
![Screenshot_5](https://github.com/0xmoei/autonity-validator/assets/90371338/e61ca9dd-46a0-4b5a-ae18-198b6272e627)
To exit: `Ctrl+C`

## 6. Register Validator
### 6.1 Create proof of node ownership
Replace `privatekey_oracle` and `tresure_account_address` with your values
```console
autonity genOwnershipProof --autonitykeys $HOME/autonity-chaindata/autonity/nodekey --oraclekeyhex privatekey_oracle tresure_account_address
```
* Save Signature Hash

### 6.2 Get your enode
```console
aut node info
```
* For example my enode is: `enode://8f8656b34583911dcd9cdbff4b1dertyb472f7c48ea2456456beb1044930c83yrtbyrt59@22.125.458.10:30303`

### 6.3 Get your Validator address
Replace <enode>
```console
aut validator compute-address <enode>
```

### 6.4 Get the consensus public key
```console
ethkey autinspect $HOME/autonity-chaindata/autonity/nodekey
```

### 6.5 Send Validator Registration Transaction
Replace `ENODE` `ORACLE_ADDRESS` `CONSENSUS_KEY` `PROOF`

Proof: Node ownership Signature Hash ( step: 6.1 )
```console
aut validator register ENODE ORACLE_ADDRESS CONSENSUS_KEY PROOF | aut tx sign - | aut tx send -
```

### 6.6 Find your validator address in the validators list
```console
aut validator list
```

### 6.7 Check validator info
Replace VALIDATOR_ADDRESS
```console
aut validator info --validator VALIDATOR_ADDRESS
```

## 7. Bond NTN stake to your Validator
Replace VALIDATOR_ADDRESS
```console
aut validator bond --validator VALIDATOR_ADDRESS 1 | aut tx sign - | aut tx send -
```

## 8. Form 2 - Validator Registeration
> Now is the time to register your validator. To do this, go to the website and fill out the data - https://stake-delegation.pages.dev/#join
>
> On the node, you need to sign a message with your validator nodekey instead of your tresurekey (we signed with tresurekey in form 1)

### 8.1 Get private key of nodekey
our default signing key is tresure key, we need to sign a message specifically with our nodekey so we need to create a nodekey2 file which contains our nodekey privatekey
```console
head -c 64 $HOME/autonity-chaindata/autonity/nodekey
```

### 8.2 Enter your Private key in the file
```console
nano $HOME/nodekey2
```
`Ctrl+X+Y` `Enter`

### 8.3 Add nodekey file to our account lists
```console
aut account import-private-key $HOME/nodekey2
mv $HOME/.autonity/keystore/UTC* $HOME/.autonity/keystore/nodekey.key
```

### 8.4 Sign ”Validator Onboarded” message with our nodekey
```console
aut account sign-message "Application for the stake delegation program" -k $HOME/.autonity/keystore/nodekey.key
```
As soon as the validator is selected to the committee, we should see similar logs on the node
![Screenshot_6](https://github.com/0xmoei/autonity-validator/assets/90371338/c1e890b5-aa6b-4451-9268-1cc8f1ab1bc8)

## ***CAUTIOS***
* Now we have our validator registered on the network with 1 NTN stake bonded to it but if we want to get Testnet Points, we need to be in committee
* There’s a limited room in committee and it depends on the NTN staked on your validator. So you need more NTN to be in `top100` in stakeflow
* Check committee leaderboard [here](https://stakeflow.io/autonity-piccadilly/validators)

## 9. Buy NTN in CAX
> CAX is a centralized automated exchange. If you have already registered in the Form 1, then your CAX account is automatically funded with 1 MILLION USD . It's linked to your registered member account so you can easily move assets between internal and external networks
>
> The exchange uses API keys for authentication, so you must create an API key for your account before you start trading

* You must be early or tricky to buy more NTN in lower prices in CAX

### 9.1 Install httpie
```console
sudo apt install snapd

sudo rm -rf /snap/httpie
sudo rm -rf /var/snap/httpie

export PATH=$PATH:~/.local/bin

pip install httpie==3.0.0

# your httpie version must be exactly 3.0.0
httpie --version
```

### 9.2 Get API KEY
```console
MESSAGE=$(jq -nc --arg nonce "$(date +%s%N)" '$ARGS.named')

aut account sign-message $MESSAGE message.sig -k $HOME/.autonity/keystore/tresure.key > message.sig

echo -n $MESSAGE | https --verify=no https://cax.piccadilly.autonity.org/api/apikeys api-sig:@message.sig
```
![image](https://github.com/0xmoei/autonity-validator/assets/90371338/27db53d9-90e2-431d-ac55-99f01cef6fd4)

### 9.3 Get USDC on your CAX offchain account

1- All registered participants received 1,000,000 USDC on `Polygon Amoy testnet` Network in their registered tresure wallet

2- Bridge USDC from Polygon Amoy to Picaddilly [here](https://usdc-frontend-autonity.vercel.app/)

3- Get Polygon Amoy faucet [here](https://faucets.chain.link/polygon-amoy) or any site [here](https://www.google.com/search?q=polygon+amoy+faucet&oq=polygon+amoy+faucet&gs_lcrp=EgZjaHJvbWUqBggAEEUYOzIGCAAQRRg7MgYIARBFGDwyBggCEC4YQNIBCDI1MjNqMGoxqAIAsAIA&sourceid=chrome&ie=UTF-8)

4- You can add USDC token address in your metamask on picadilly testnet network to see your USDC amount
`0x3a60C03a86eEAe30501ce1af04a6C04Cf0188700`

5- Transfer USDC from Picadilly to CAX ( from Onchain to Offchain)
```console
# Just Replace AMOUNT
aut token transfer --token 0x3a60C03a86eEAe30501ce1af04a6C04Cf0188700 0x11F62c273dD23dbe4D1713C5629fc35713Aa5a94 AMOUNT | aut tx sign - | aut tx send -
```

### 10- CAX Commands
```console
# Replace API_KEY with your api in step: 9.2
KEY=API_KEY
```
```console
# View off-chain CAX balance
https --verify=no GET https://cax.piccadilly.autonity.org/api/balances/ API-Key:$KEY
```
```console
# View orderbooks
https --verify=no GET https://cax.piccadilly.autonity.org/api/orderbooks/ API-Key:$KEY
```
```console
# Getting current price of NTN-USD
https --verify=no GET https://cax.piccadilly.autonity.org/api/orderbooks/NTN-USDC/quote API-Key:$KEY
```
```console
# Getting current price of ATN-USD
https --verify=no GET https://cax.piccadilly.autonity.org/api/orderbooks/ATN-USDC/quote API-Key:$KEY
```
```console
# Buy NTN with limit-order (Replace x with the price of your favor and y with the amount if NTN you want to bid or ask)
https --verify=no POST https://cax.piccadilly.autonity.org/api/orders/ API-Key:$KEY pair=NTN-USDC side=bid price=x amount=y
```
```console
# view order status (you need the "order_id" number, which you will receive in response to the previous request)
https --verify=no GET https://cax.piccadilly.autonity.org/api/orders/order_id API-Key:$KEY
```
```console
# Cancel open order
https --verify=no DELETE https://cax.piccadilly.autonity.org/api/orders/313469 API-Key:$KEY
```
```console
# Send X amount of NTN from off-chain CAX to tresurekey wallet
https --verify=no POST https://cax.piccadilly.autonity.org/api/withdraws/ API-Key:$KEY symbol=NTN amount=X
```

### NTN Balance in your tresure wallet
```console
aut account info
```

## 10. You can now bond NTN to your validator again with step 7 command, but increase the amount from 1

#

** Thanks lesnik13utsa & zuka_116 for their detailed guide **

#

********Follow [0xMoei](https://twitter.com/0xMoei) For more info********
