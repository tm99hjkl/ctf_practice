# Hello PGP
[問題](https://id0-rsa.pub/forum/problem/1/solution/)

## 解答
- 与えられた問題文を`m.asc`に保存する．
- パスワードは1つの単語から成るとのことなので，`/usr/share/dict/words`を使い，以下のコマンドを実行する：
  - `for c in $(cat /usr/share/dict/words); do gpg --batch --passphrase $c -d m.asc 2> /dev/null; if [ $? -eq 0 ]; then break; fi; done`とすれば解が得られる．
  - パスワードを知りたい場合は`break`の前に`echo $c`すればよい．
