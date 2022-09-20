# Ethereum NFT Analysis: quicker Entropy Analysis

In this notebook, I was concerned with the fact that calculating entropy for the NFT collections was taking too much time. 

Hence the **objective** here is to find a way of calculating the same entropies that takes less time.

The actual notebook of the author is [here](https://www.kaggle.com/code/simiotic/ethereum-nft-analysis).

The actual data author is [here](https://www.kaggle.com/datasets/simiotic/ethereum-nfts).

Link to my kaggle notebook is [here](https://www.kaggle.com/code/sbrar0804/ethereum-nft-analysis-quicker-entropy-calc/notebook).


```python
!pip install nfts
```


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
