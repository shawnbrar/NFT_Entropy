# Ethereum NFT Analysis: quicker Entropy Analysis

In this notebook, I was concerned with the fact that calculating entropy for the NFT collections was taking too much time. 

Hence the **objective** here is to find a way of calculating the same entropies that takes less time.

The actual notebook of the author is [here](https://www.kaggle.com/code/simiotic/ethereum-nft-analysis).

The actual data author is [here](https://www.kaggle.com/datasets/simiotic/ethereum-nfts).

Link to my kaggle notebook is [here](https://www.kaggle.com/code/sbrar0804/ethereum-nft-analysis-quicker-entropy-calc/notebook).


```python
!pip install nfts
```

    Collecting nfts
      Downloading nfts-0.0.2-py3-none-any.whl (16 kB)
    Requirement already satisfied: requests in /opt/conda/lib/python3.7/site-packages (from nfts) (2.25.1)
    Requirement already satisfied: pandas in /opt/conda/lib/python3.7/site-packages (from nfts) (1.3.2)
    Requirement already satisfied: tqdm in /opt/conda/lib/python3.7/site-packages (from nfts) (4.62.1)
    Collecting web3
      Downloading web3-5.30.0-py3-none-any.whl (501 kB)
    [K     |████████████████████████████████| 501 kB 2.1 MB/s 
    [?25hCollecting moonstreamdb
      Downloading moonstreamdb-0.3.2-py3-none-any.whl (7.4 kB)
    Collecting humbug
      Downloading humbug-0.2.7-py3-none-any.whl (11 kB)
    Collecting psycopg2-binary
      Downloading psycopg2_binary-2.9.3-cp37-cp37m-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (3.0 MB)
    [K     |████████████████████████████████| 3.0 MB 59.2 MB/s 
    [?25hRequirement already satisfied: alembic in /opt/conda/lib/python3.7/site-packages (from moonstreamdb->nfts) (1.7.3)
    Requirement already satisfied: sqlalchemy in /opt/conda/lib/python3.7/site-packages (from moonstreamdb->nfts) (1.4.22)
    Requirement already satisfied: importlib-metadata in /opt/conda/lib/python3.7/site-packages (from alembic->moonstreamdb->nfts) (3.4.0)
    Requirement already satisfied: Mako in /opt/conda/lib/python3.7/site-packages (from alembic->moonstreamdb->nfts) (1.1.5)
    Requirement already satisfied: importlib-resources in /opt/conda/lib/python3.7/site-packages (from alembic->moonstreamdb->nfts) (5.2.2)
    Requirement already satisfied: greenlet!=0.4.17 in /opt/conda/lib/python3.7/site-packages (from sqlalchemy->moonstreamdb->nfts) (1.1.1)
    Requirement already satisfied: zipp>=0.5 in /opt/conda/lib/python3.7/site-packages (from importlib-metadata->alembic->moonstreamdb->nfts) (3.5.0)
    Requirement already satisfied: typing-extensions>=3.6.4 in /opt/conda/lib/python3.7/site-packages (from importlib-metadata->alembic->moonstreamdb->nfts) (3.7.4.3)
    Requirement already satisfied: MarkupSafe>=0.9.2 in /opt/conda/lib/python3.7/site-packages (from Mako->alembic->moonstreamdb->nfts) (2.0.1)
    Requirement already satisfied: numpy>=1.17.3 in /opt/conda/lib/python3.7/site-packages (from pandas->nfts) (1.19.5)
    Requirement already satisfied: python-dateutil>=2.7.3 in /opt/conda/lib/python3.7/site-packages (from pandas->nfts) (2.8.0)
    Requirement already satisfied: pytz>=2017.3 in /opt/conda/lib/python3.7/site-packages (from pandas->nfts) (2021.1)
    Requirement already satisfied: six>=1.5 in /opt/conda/lib/python3.7/site-packages (from python-dateutil>=2.7.3->pandas->nfts) (1.15.0)
    Requirement already satisfied: idna<3,>=2.5 in /opt/conda/lib/python3.7/site-packages (from requests->nfts) (2.10)
    Requirement already satisfied: chardet<5,>=3.0.2 in /opt/conda/lib/python3.7/site-packages (from requests->nfts) (4.0.0)
    Requirement already satisfied: urllib3<1.27,>=1.21.1 in /opt/conda/lib/python3.7/site-packages (from requests->nfts) (1.26.6)
    Requirement already satisfied: certifi>=2017.4.17 in /opt/conda/lib/python3.7/site-packages (from requests->nfts) (2021.5.30)
    Collecting eth-account<0.6.0,>=0.5.7
      Downloading eth_account-0.5.9-py3-none-any.whl (101 kB)
    [K     |████████████████████████████████| 101 kB 7.4 MB/s 
    [?25hRequirement already satisfied: protobuf<4,>=3.10.0 in /opt/conda/lib/python3.7/site-packages (from web3->nfts) (3.18.0)
    Requirement already satisfied: aiohttp<4,>=3.7.4.post0 in /opt/conda/lib/python3.7/site-packages (from web3->nfts) (3.7.4.post0)
    Collecting hexbytes<1.0.0,>=0.1.0
      Downloading hexbytes-0.3.0-py3-none-any.whl (6.4 kB)
    Requirement already satisfied: jsonschema<5,>=3.2.0 in /opt/conda/lib/python3.7/site-packages (from web3->nfts) (3.2.0)
    Collecting eth-rlp<0.3
      Downloading eth_rlp-0.2.1-py3-none-any.whl (5.0 kB)
    Collecting eth-hash[pycryptodome]<1.0.0,>=0.2.0
      Downloading eth_hash-0.5.0-py3-none-any.whl (8.9 kB)
    Collecting eth-typing<3.0.0,>=2.0.0
      Downloading eth_typing-2.3.0-py3-none-any.whl (6.2 kB)
    Collecting eth-utils<2.0.0,>=1.9.5
      Downloading eth_utils-1.10.0-py3-none-any.whl (24 kB)
    Collecting ipfshttpclient==0.8.0a2
      Downloading ipfshttpclient-0.8.0a2-py3-none-any.whl (82 kB)
    [K     |████████████████████████████████| 82 kB 598 kB/s 
    [?25hCollecting lru-dict<2.0.0,>=1.1.6
      Downloading lru_dict-1.1.8-cp37-cp37m-manylinux_2_5_x86_64.manylinux1_x86_64.manylinux_2_17_x86_64.manylinux2014_x86_64.whl (26 kB)
    Collecting websockets<10,>=9.1
      Downloading websockets-9.1-cp37-cp37m-manylinux2010_x86_64.whl (103 kB)
    [K     |████████████████████████████████| 103 kB 67.8 MB/s 
    [?25hCollecting eth-abi<3.0.0,>=2.0.0b6
      Downloading eth_abi-2.2.0-py3-none-any.whl (28 kB)
    Collecting multiaddr>=0.0.7
      Downloading multiaddr-0.0.9-py2.py3-none-any.whl (16 kB)
    Requirement already satisfied: yarl<2.0,>=1.0 in /opt/conda/lib/python3.7/site-packages (from aiohttp<4,>=3.7.4.post0->web3->nfts) (1.6.3)
    Requirement already satisfied: async-timeout<4.0,>=3.0 in /opt/conda/lib/python3.7/site-packages (from aiohttp<4,>=3.7.4.post0->web3->nfts) (3.0.1)
    Requirement already satisfied: attrs>=17.3.0 in /opt/conda/lib/python3.7/site-packages (from aiohttp<4,>=3.7.4.post0->web3->nfts) (21.2.0)
    Requirement already satisfied: multidict<7.0,>=4.5 in /opt/conda/lib/python3.7/site-packages (from aiohttp<4,>=3.7.4.post0->web3->nfts) (5.1.0)
    Collecting parsimonious<0.9.0,>=0.8.0
      Downloading parsimonious-0.8.1.tar.gz (45 kB)
    [K     |████████████████████████████████| 45 kB 2.2 MB/s 
    [?25hCollecting eth-keyfile<0.6.0,>=0.5.0
      Downloading eth_keyfile-0.5.1-py3-none-any.whl (8.3 kB)
    Collecting eth-keys<0.4.0,>=0.3.4
      Downloading eth_keys-0.3.4-py3-none-any.whl (21 kB)
    Collecting bitarray<3,>=1.2.1
      Downloading bitarray-2.6.0-cp37-cp37m-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (235 kB)
    [K     |████████████████████████████████| 235 kB 66.2 MB/s 
    [?25hCollecting rlp<3,>=1.0.0
      Downloading rlp-2.0.1-py2.py3-none-any.whl (20 kB)
    Collecting pycryptodome<4,>=3.6.6
      Downloading pycryptodome-3.15.0-cp35-abi3-manylinux2010_x86_64.whl (2.3 MB)
    [K     |████████████████████████████████| 2.3 MB 43.9 MB/s 
    [?25hRequirement already satisfied: cytoolz<1.0.0,>=0.9.0 in /opt/conda/lib/python3.7/site-packages (from eth-keyfile<0.6.0,>=0.5.0->eth-account<0.6.0,>=0.5.7->web3->nfts) (0.11.0)
    Requirement already satisfied: toolz>=0.8.0 in /opt/conda/lib/python3.7/site-packages (from cytoolz<1.0.0,>=0.9.0->eth-keyfile<0.6.0,>=0.5.0->eth-account<0.6.0,>=0.5.7->web3->nfts) (0.11.1)
    Collecting eth-utils<2.0.0,>=1.9.5
      Downloading eth_utils-1.9.5-py3-none-any.whl (23 kB)
    Requirement already satisfied: setuptools in /opt/conda/lib/python3.7/site-packages (from jsonschema<5,>=3.2.0->web3->nfts) (57.4.0)
    Requirement already satisfied: pyrsistent>=0.14.0 in /opt/conda/lib/python3.7/site-packages (from jsonschema<5,>=3.2.0->web3->nfts) (0.17.3)
    Collecting netaddr
      Downloading netaddr-0.8.0-py2.py3-none-any.whl (1.9 MB)
    [K     |████████████████████████████████| 1.9 MB 54.0 MB/s 
    [?25hCollecting varint
      Downloading varint-1.0.2.tar.gz (1.9 kB)
    Requirement already satisfied: base58 in /opt/conda/lib/python3.7/site-packages (from multiaddr>=0.0.7->ipfshttpclient==0.8.0a2->web3->nfts) (2.1.0)
    Building wheels for collected packages: parsimonious, varint
      Building wheel for parsimonious (setup.py) ... [?25l- \ done
    [?25h  Created wheel for parsimonious: filename=parsimonious-0.8.1-py3-none-any.whl size=42724 sha256=0d866b4c874844eb33a5655ea990ef7caabd8dc063b61e006a79a5d156c10284
      Stored in directory: /root/.cache/pip/wheels/88/5d/ba/f27d8af07306b65ee44f9d3f9cadea1db749a421a6db8a99bf
      Building wheel for varint (setup.py) ... [?25l- \ done
    [?25h  Created wheel for varint: filename=varint-1.0.2-py3-none-any.whl size=1979 sha256=b96a14c80c3832875469ec215acaaf0f2a11aa6d94ec38f1c92faf11b895f264
      Stored in directory: /root/.cache/pip/wheels/69/21/07/09f1c6a7d9b59377aa6d98da6efdd670f7ca40aabd93d02704
    Successfully built parsimonious varint
    Installing collected packages: eth-typing, eth-hash, eth-utils, varint, rlp, pycryptodome, parsimonious, netaddr, hexbytes, eth-keys, multiaddr, eth-rlp, eth-keyfile, eth-abi, bitarray, websockets, psycopg2-binary, lru-dict, ipfshttpclient, eth-account, web3, moonstreamdb, humbug, nfts
    Successfully installed bitarray-2.6.0 eth-abi-2.2.0 eth-account-0.5.9 eth-hash-0.5.0 eth-keyfile-0.5.1 eth-keys-0.3.4 eth-rlp-0.2.1 eth-typing-2.3.0 eth-utils-1.9.5 hexbytes-0.3.0 humbug-0.2.7 ipfshttpclient-0.8.0a2 lru-dict-1.1.8 moonstreamdb-0.3.2 multiaddr-0.0.9 netaddr-0.8.0 nfts-0.0.2 parsimonious-0.8.1 psycopg2-binary-2.9.3 pycryptodome-3.15.0 rlp-2.0.1 varint-1.0.2 web3-5.30.0 websockets-9.1
    [33mWARNING: Running pip as the 'root' user can result in broken permissions and conflicting behaviour with the system package manager. It is recommended to use a virtual environment instead: https://pip.pypa.io/warnings/venv[0m



```python
import os
import sqlite3

import matplotlib.pyplot as plt
import nfts.dataset
import numpy as np
import pandas as pd
from scipy.optimize import curve_fit
from scipy.special import zeta
```


```python
os.listdir("/kaggle/input/ethereum-nfts")
DATASET_PATH = "/kaggle/input/ethereum-nfts/nfts.sqlite"
ds = nfts.dataset.FromSQLite(DATASET_PATH)
```


```python
current_owners_df = ds.load_dataframe("current_owners")
current_owners_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>nft_address</th>
      <th>token_id</th>
      <th>owner</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0x00000000000b7F8E8E8Ad148f9d53303Bfe20796</td>
      <td>0</td>
      <td>0xb776cAb26B9e6Be821842DC0cc0e8217489a4581</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0x00000000000b7F8E8E8Ad148f9d53303Bfe20796</td>
      <td>1</td>
      <td>0x8A73024B39A4477a5Dc43fD6360e446851AD1D28</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0x00000000000b7F8E8E8Ad148f9d53303Bfe20796</td>
      <td>10</td>
      <td>0x5e5C817E9264B46cBBB980198684Ad9d14f3e0B4</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0x00000000000b7F8E8E8Ad148f9d53303Bfe20796</td>
      <td>11</td>
      <td>0x8376f63c13b99D3eedfA51ddd77Ff375279B3Ba0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0x00000000000b7F8E8E8Ad148f9d53303Bfe20796</td>
      <td>12</td>
      <td>0xb5e34552F32BA9226C987769BF6555a538510BA8</td>
    </tr>
  </tbody>
</table>
</div>



### The shapes of NFT collections

NFTs are released in collections, with a single contract accounting for multiple tokens.

Are there differences between ownership distributions of NFTs like the [Ethereum Name Service (ENS)](https://ens.domains/), which have utility beyond their artistic value, and those that do not currently have such use cases?

One way we can answer this question is to see how much information each NFT collection gives us about individual owners of tokens in that collection. We will do this by treating each collection as a probability distribution over owners of tokens from that collection. If the collection $C$ consists of $n$ tokens and an address $A$ owns $m$ of those tokens, we will assign that address a probability of $p_A = m/n$ in the collection's associated probability distribution. Then we will calculate the entropy:

$$H(C) = - \sum_{A} p_A \log(p_A).$$
Here, the sum is over all addresses $A$ that own at least one token from $C$.

$H(C)$ simultaneously contains information about:
1. How many tokens were issued as part of the collection $C$.
2. How evenly the tokens in $C$ are distributed over the addresses $A$ which own those tokens.



```python
contract_owners_df = current_owners_df.groupby(["nft_address", "owner"], as_index=False).size().rename(columns={"size": "num_tokens"})
contract_owners_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>nft_address</th>
      <th>owner</th>
      <th>num_tokens</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0x00000000000b7F8E8E8Ad148f9d53303Bfe20796</td>
      <td>0x429a635eD4DaF9529C07d5406D466B349EC34361</td>
      <td>3</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0x00000000000b7F8E8E8Ad148f9d53303Bfe20796</td>
      <td>0x5e5C817E9264B46cBBB980198684Ad9d14f3e0B4</td>
      <td>5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0x00000000000b7F8E8E8Ad148f9d53303Bfe20796</td>
      <td>0x8376f63c13b99D3eedfA51ddd77Ff375279B3Ba0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0x00000000000b7F8E8E8Ad148f9d53303Bfe20796</td>
      <td>0x83D7Da9E572C5ad14caAe36771022C43AF084dbF</td>
      <td>5</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0x00000000000b7F8E8E8Ad148f9d53303Bfe20796</td>
      <td>0x8A73024B39A4477a5Dc43fD6360e446851AD1D28</td>
      <td>5</td>
    </tr>
  </tbody>
</table>
</div>




```python
contract_owners_groups = contract_owners_df.groupby(["nft_address"])
entropies = {}
```

## ZOMGLINGS way of calculating entropy


```python
%%timeit
for contract_address, owners_group in contract_owners_groups:
    total_supply = owners_group["num_tokens"].sum()
    owners_group["p"] = owners_group["num_tokens"]/total_supply
    owners_group["log(p)"] = np.log2(owners_group["p"])
    owners_group["-plog(p)"] = (-1) * owners_group["p"] * owners_group["log(p)"]
    entropy = owners_group["-plog(p)"].sum()
    entropies[contract_address] = entropy
```

    23.8 s ± 63.4 ms per loop (mean ± std. dev. of 7 runs, 1 loop each)


## My way of calculating entropies


```python
# function to calulate entropy of a group
def group_ops(group):
    total_supply = group["num_tokens"].sum()
    p = group["num_tokens"]/total_supply
    log_p = np.log2(p)
    plog_p = -p*log_p
    return plog_p.sum()
```


```python
%%timeit
entropies2 = contract_owners_groups.apply(group_ops)
```

    5.94 s ± 75.5 ms per loop (mean ± std. dev. of 7 runs, 1 loop each)


For some reason running this does not assigns the entropies to the variable. I don't know why

Now to find out if these two variables have the same values or not


```python
entropies2 = contract_owners_groups.apply(group_ops)
```


```python
(entropies2 == pd.Series(entropies)).all()
```




    True



Hence, they do have the same entropies. 
