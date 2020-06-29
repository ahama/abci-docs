
# データセットの公開

ABCIでは、ABCIクラウドストレージで公開されている[データセットをリストするページ](https://datasets.abci.ai/)を用意しています。 ここでは、ABCIクラウドストレージ上でデータセットを公開し、そのリストに追加するまでの手順を説明します。

## データのアップロードと公開設定 {#upload-and-publish-data}


## データセットのメタデータの記述 {#write-metadata}

[こちら](https://datasets.abci.ai/dataset.yml)のYAMLファイルをダウンロードし、[サンプルのページ](https://datasets.abci.ai/registration/)を参考に、各項目を記入してください。その後、ABCIクラウドストレージのバケットにアップロードし、公開設定を行ったら、URLを <abci-application-ml@aist.go.jp> までお知らせください。

```
[username@es1 ~]$ aws 
```

## 簡易WEBサイトの生成 (任意) {#make-simple-site}

データセットを紹介するプロジェクトのページをお持ちでない場合、

```
[username@es1 ~]$ aws --endpoint-url https://s3.abci.ai s3 cp s3://admin/generate-simple-page generate-simple-page
[username@es1 ~]$ chmod 755 generate-simple-page
[username@es1 ~]$ ./generate-simple-page dataset.yaml > dataset.html
```
