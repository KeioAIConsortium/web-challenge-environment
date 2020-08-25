# Web で簡単プログラミングチャレンジの実行環境
[Web で簡単プログラミングチャレンジ](https://contest.aic.keio.ac.jp/) に参加していただき、ありがとうございます！

提出していただいたコードは、Docker による隔離環境で実行され自動的に採点されます。
このリポジトリには、隔離環境を作成するための Docker の設定ファイル (Dockerfile) が含まれています。
 (実際の自動採点システムでは、このリポジトリに含まれる Dockerfile に少し変更を加えています。）
以下の手順に従えば、実際の採点環境とほとんど同じ環境でコードが実行できます。
ぜひデバッグに役立ててください。

# 使い方
[例題](https://contest.aic.keio.ac.jp/contest/2/question/detail/13) をもとに説明します。

あらかじめ、プログラム `predict.py` を作成しておきます

```
$ cat predict.py
'''
A, B及びansはint型
'''
def pred(A, B):
    ## ここにansを求める処理を追記
    ans = A + B
    return ans


# デバッグ用に pred を呼び出します
print(pred(1, 1))
print(pred(-5, 0))
```

次のコマンドを実行して、隔離環境でコードを実行します

```
$ docker run -v $PWD:/root -w /root kumassy/foo-bar-images:cuda10.0-python3.7 python3 predict.py
```

`-v $PWD:/root`: 作業ディレクトリ（ここでは predict.py があるディレクトリ）を、コンテナ内の `/root` にコピーします。  
`-w /root`: コンテナ内の作業ディレクトリを `/root` に設定します。  
`kumassy/foo-bar-images:cuda10.0-python3.7`: 実行環境を設定します。  
`python3 predict.py`: コンテナ内で実行するコマンドを設定します。

# Docker イメージリスト
以下の 3 つの Docker イメージが利用できます

- kumassy/foo-bar-images:cuda10.0-python3.7
    - GPU ライブラリと Python 実行環境が含まれます
    - 現在、すべての問題はこちらをベースとした環境で実行されます
- kumassy/foo-bar-images:cuda10.0
    - GPU ライブラリのみが含まれます
- kumassy/foo-bar-images:python3.7
    - Python 実行環境のみが含まれます

# 補足情報
## Docker とは？
Docker は *コンテナ* と呼ばれる軽量な仮想環境を作成・実行できるツールです。

例えば、こちらの記事が参考になるでしょう: 

> Docker入門（第一回）～Dockerとは何か、何が良いのか～ | さくらのナレッジ  
> https://knowledge.sakura.ad.jp/13265/

## インストール方法
公式サイトの手順に従ってください

> Get Docker | Docker Documentation  
> https://docs.docker.com/get-docker/
 

# 貢献する
Issue や Pull Request をお待ちしています。

また、一緒に働ける方を随時募集しています。
現在募集中の業務については、こちらをご覧ください: 

> https://aic.keio.ac.jp/forStudents/manage

# ライセンス
