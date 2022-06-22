# Setup VM swap ram to digital ocean

https://www.digitalocean.com/community/tutorials/how-to-add-swap-space-on-ubuntu-20-04
# Online servers

| IP address | Hosts services | Digitalocean location | ssh from Rpi | Layer1 name | Services |
| -----------|----------------|-----------------------|--------------|-------------|----------|
| 139.198.14.205 | | 北京3区 | ssh testnet-alice |alice|layer1|
| 139.198.4.185 | | 北京3区| ssh testnet-bob |bob|layer1|
| 139.198.127.249 | | 广东2区| ssh testnet-charlie |charlie|layer1|
| 139.198.27.87 | | 北京3区| ssh testnet-dave |dave|layer1|
|--------------|---|------|------------------|-----|------|
| 198.199.82.222 |   | NY1 center | ssh wallet |wallet|layer1|
| 157.245.244.227 | Indexer | NY1 center| ssh nyindexer |||
| 64.227.105.212 |  | SF3 center| ssh alice |alice|layer1|
| 164.90.159.26 |  | SF3 center| ssh bob |bob|layer1|
| 159.89.158.202 |  |SF2 center| ssh charlie |charlie|layer1|
| 159.65.102.36 |  | SF2 center| ssh dave |dave|layer1|
| 159.65.102.135 | | SF2 center| ssh eve |eve|layer1|
| 159.65.102.153 |  | SF2 center| ssh ferdie |ferdie|layer1|
| 159.65.106.210 |   | SF2 center | ssh george |george|layer1|

# Internal testing servers

| IP address | Hosts services | Digitalocean location | ssh from Rpi | Layer1 name | Services |
| -----------|----------------|-----------------------|--------------|-------------|----------|
| 134.209.69.224 | Test node 1 | SF2 center | ssh epoch |alice|layer1|
| 143.244.154.7 | Test node 2 | NY1 center | ssh raft |bob|layer1|
| 165.227.9.107 | Test indexer | SF2 center| ssh indexer |||

# Internal testing miners

| IP address | Hosts services | Digitalocean location | ssh from Rpi | Layer1 name | Services |
| -----------|----------------|-----------------------|--------------|-------------|----------|
| float use rpi to ssh | miners setup own machine | SF2 center | ssh test1 ||test1|
| float use rpi to ssh | miner setup own machine | SF2 center | ssh test2 ||test2|
| float use rpi to ssh | miner setuo own machine  | SF2 center| ssh test3 ||test3|

# Website

| IP address | Hosts services | Digitalocean location | ssh from Rpi | Layer1 name | Services |
| -----------|----------------|-----------------------|--------------|-------------|----------|
| 64.227.49.206 | Web server teaproject.org | SF2 center| ssh web |||


# Query all transfer to NPC
```
query {
    logs (
        orderBy: AT_BLOCK_ASC
        filter: {
        name: {in: ["Transfer"]}
            to: {in: ["5D2od84fg3GScGR139Li56raDWNQQhzgYbV7QsEJKS4KfTGv"]}
        }
      ) {
            totalCount
            nodes {
                name
                type
                args
                atBlock
                from
                to
                auctionId
                cmlId
                price
                amount,
                tappId,
                json,
                }
            }
        }
```

