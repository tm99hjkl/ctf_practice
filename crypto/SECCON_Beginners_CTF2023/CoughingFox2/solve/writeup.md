# My solutuion of CoughingFox2

## Observation
[main.py](../given_files/main.py)を眺めたり、実行したりすると、大体以下のことが分かる。
* flagの`i`番目と`i+1`番目の文字の和の二乗に`i`が足された値が、暗号文の1文字になっている。
* ↑の処理をflagの左端から実行して出来た`cipher`を`shuffle`した出力が、[`cipher.txt`](../given_files/cipher.txt)に書き込まれている。
* flagは44文字になっている(`cipher`の長さ+ 1)

## Answer
`cipher`が`shuffle`されてしまうと元の並びに戻すことが難しいのではないかと思うかもしれないが、実はこれは可能である。


一例として`cipher`の第一要素である4396を考える。

まず4396という数値が`flag`の`i`番目と`i+1`番目の文字を用いて作られたとすると、

`4396-i == (flag[i] + flag[i+1])**2`

が成り立つ。

これは言い方を変えると、「**`4396-i`の平方根がちょうど整数になるような**イイカンジの`i`を見つければ、それが`shuffle`前の`cipher`におけるインデックスになる」ということである。`i`の候補は0から`len(flag)-1`であるから、そのような`i`の探索方法はブルートフォースでよい。

コードは以下のようになる：
```python
from gmpy2 import *

cipher = [4396, 22819, 47998, 47995, 40007, 9235, 21625, 25006, 4397, 51534, 46680, 44129, 38055, 18513, 24368, 38451, 46240, 20758, 37257, 40830, 25293, 38845, 22503, 44535, 22210, 39632, 38046, 43687, 48413, 47525, 23718, 51567, 23115, 42461, 26272, 28933, 23726, 48845, 21924, 46225, 20488, 27579, 21636]

sorted = [0] * 43
for c in cipher:
    for i in range(len(cipher)):
        # iの値を特定することにより、cipherを整列
        if iroot(c-i,2)[1]: sorted[i] = iroot(c,2)[0]
```

gmpy2はとても便利で、`iroot(c,2)`の返り値の`[1]`要素は、計算した二乗根がちょうど整数なら`True`を返すようになっている。
これをそのまま使えば、簡単に`cipher`を正しい順に(平方根を取った状態で)並びなおすことができる。 (`sorted`に格納している。)

さて、次の疑問としては、求まった平方根の値から`flag`の文字を計算出来るかということだと思う。そしてこれも簡単に求まる。

例えば、`flag`の文字として`ct`まで分かっていて、`f`が不明だとする (本当は`ctf4b{`までわかっているのだが) 。このとき、以下のような状況が成り立つことが分かる：
```
# 右辺は上で求めた sorted から分かる
c + t = 215
t + _ = 218
↓ 式変形 ↓ 
_ = (218 - 215) + c

```
最後の式を少しずつ一般化する：
```
_         = chr(sorted[1]   - sorted[0] + ord(flag[0]))
flag[i+2] = chr(sorted[i+1] - sorted[i] + ord(flag[i]))
```
後はこれを (`index out of range`に気を付けながら)先頭から求めていけばよい：
```python
flag = ['c', 't'] + ['']*42 
for i in range(42):
    flag[i+2] = chr((sorted[i+1] - sorted[i]) + ord(flag[i]))

print("".join(flag))
```

```
ctf4b{hi_b3g1nner!g00d_1uck_4nd_h4ve_fun!!!}
```

