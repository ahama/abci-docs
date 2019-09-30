
# バケットの暗号化機能

保存する前にサーバ側でオブジェクトを自動的に暗号化する(サーバーサイド暗号化)バケットを作成することができます。


## 暗号化を有効にしたバケットの作成

サーバーサイド暗号化を有効にしたバケットを作るには、awsコマンドではなく、ABCI環境に用意されている create-encrypted-bucket コマンドを実行します。
例えば、'dataset-c0543' というバケットを作る場合は以下のように実行します。

```
[username@es1 ~]$ create-encrypted-bucket --endpoint-url https://s3.abci.ai s3://dataset-c0543
create-encrypted-bucket Success.
```

!!! note
    サーバー側が保持する鍵を用いて、サーバー上でオブジェクトを保存する時に暗号化(読み出す時に復号化)するもので、アクセスキーなど送信リクエスト固有の情報で暗号化するものではありません。

!!! note
    暗号化を有効にしていないバケットを、後から暗号化を有効にすることはできません。


## 暗号化を有効にしたバケットかどうかの判別

サーバーサイド暗号化を有効にしたバケットかどうかを判別するには、オブジェクトのメタデータを確認する必要があるため、空のバケットでは確認できません。空の場合は、なにかオブジェクトを1つ作成してください。

`aws s3api head-object` で確認します。
以下では、dataset-c0543 バケットの cat.jpg というオブジェクトのメタデータを確認しています。 `"ServerSideEncryption": "AES256"` という情報が含まれているため、dataset-c0543 は暗号化を有効にしたバケットです。この情報が含まれていない場合は、暗号化を指定されなかったバケットです。

```
[username@es1 ~]$ aws --endpoint-url https://s3.abci.ai s3api head-object --bucket dataset-c0543 --key cat.jpg
{
    "LastModified": "Tue, 30 Jul 2019 09:34:18 GMT",
    "ContentLength": 1048576,
    "ETag": "\"c951191fe4fa27c0d054a8456c6c20d1\"",
    "ServerSideEncryption": "AES256",
    "Metadata": {}
}
```

<!-- CSE?  -->