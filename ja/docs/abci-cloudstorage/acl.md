
# ACL

## アクセスコントロールリスト（ACL）の利用

ABCIクラウドストレージでは、アクセスコントロールリスト(ACL)を使用してバケットやオブジェクトへのアクセス制御ができます。ACLを使用すれば、他のABCIグループやインターネットへのデータ公開が可能です。ここでは、ACLの利用手順について説明します。

### ACLの概要

ACLでのアクセス制御の設定は、対象となるバケットやオブジェクトに、アクセス元と許可する操作の組み合わせを付与します。指定できるアクセス元と許可する操作は、以下表のように予めシステムで用意されています。

アクセス元の一覧

| アクセス元| 説明|
| :--| :--|
| ABCIグループ| 他のABCIグループからのアクセスを対象とします。対象としたABCIグループに所属する全クラウドストレージアカウントが対象になります。|
| ABCIクラウドストレージ全体| クラウドストレージアカウントをもつ全ての利用者からのアクセスを対象にします。|
| public 公開| インターネットなど不特定多数からのアクセスを許可します。|

許可する操作は、バケットとオブジェクトで異なります。

許可する操作の一覧：バケット

| 許可する操作| 説明|
| :--| :--|
| read| 指定したバケット配下にあるオブジェクトの一覧の取得を許可します。|
| write| 指定したバケット配下で、オブジェクトの作成、消去、変更が可能です。|
| read-acp| 指定したバケットのACLの取得を許可します。|
| write-acp| 指定したバケットのACLの変更を許可します。|
| full-control| 上記全てのACLを許可します。|

許可する操作の一覧：オブジェクト

| 許可する操作| 説明|
| :--| :--|
| read| オブジェクトデータの読み込みを許可します。|
| write| 設定できません。| 
| read-acp| 指定したオブジェクトのACLの取得を許可します。|
| write-acp| 指定したオブジェクトのACLの変更を許可します。|
| full-control| 上記全てのACLを許可します。|

また、インターネットへの公開のようなアクセス元と操作の組み合わせが定型的なACLは、既定ACLが用意されています。既定ACLについては、後の「インターネットへの公開」の章を参照してください。

### オブジェクト共有(ABCIグループ間)

ここでは、異なるABCIグループ間でオブジェクトを共有する方法について紹介します。
共有元として、ABCIグループ aaa00000 のオブジェクトを、ABCIグループ aaa11111 に読み込みを許可します。

| 共有元ABCI グループ| 共有先ABCI グループ|
| :--| :--|
| aaa00000| aaa11111|

共有元オブジェクトの詳細

| バケット名| prefix| オブジェクト名|
| :--| :--| :--|
| test-share| testdir| testmessage|

まず、アクセス元(aaa11111)となるABCIグループの正規IDを入手します。正規IDは、公開する相手に s3 list-bucket コマンド実行結果の"Owner"->"ID"から取得できます。

aaa11111 で実行

```
$ aws s3api list-buckets
{
    "Buckets": [
        {
            "Name": "somebucket",
            "CreationDate": "2019-08-22T11:36:17.523Z"
        }
    ],
    "Owner": {
        "DisplayName": "aaa11111",
正規ID->"ID": "1a2bc03fa4ee5ba678b90cc1a234f5f67f890f1f2341fa56a78901234cc5fad6"
    }
}
```

上記オブジェクトにACLを設定します。読み込み(read)のみの操作は --grant-read を指定し、接続元として id で正規IDを指定します。

```
[username@es1 ~]$aws s3api put-object-acl --grant-read id=1a2bc03fa4ee5ba678b90cc1a234f5f67f890f1f2341fa56a78901234cc5fad6 --bucket test-share --key testdir/testmessage
```

設定の結果を確認します。デフォルトでは、Grantsの要素に、aaa11111 の要素が Permission "READ" で追加されています。
```
[username@es1 ~]$ aws s3api get-object-acl --bucket test-share --key testdir/testmessage
{
    "Owner": {
        "DisplayName": "acct-gxx00000",
        "ID": "f12d0fa66ea4df5418c0c6234fd5eb3a9f4409bf50b5a58983a30be8f9a42bda"
    },
    "Grants": [
        {
            "Grantee": {
                "DisplayName": "aaa11111@abci.local",
                "ID": "1a2bc03fa4ee5ba678b90cc1a234f5f67f890f1f2341fa56a78901234cc5fad6",
                "Type": "CanonicalUser"
            },
            "Permission": "READ"
        }
    ]
}
```

設定を戻すときは、以下のように ACL を private に設定すれば、初期値が設定されます。
```
[username@es1 ~]$ aws s3api put-object-acl --acl private --bucket test-share --key testdir/testmessage
```


### インターネットへの公開

ABCIクラウドストレージは、ACL を設定することでバケットやオブジェクトをインターネットに公開することが可能です。
インターネットに公開する既定ACL は２つが用意されており、バケットやオブジェクトに適用すると以下表の通り公開されます。

| 既定ACL| バケット| オブジェクト|
| :--| :--| :--|
| public-read| 指定したバケット配下のオブジェクト一覧が公開されます。| 指定したオブジェクトが公開されます。オブジェクトの変更はクラウドストレージ領域内の適切なポリシーを有したクラウドストレージアカウントのみが可能です。|
| public-read-write| インターネット上の全てのユーザが、指定したバケット配下の読み書きおよび ACL の変更が可能になります。| インターネット上の全てのユーザがACLを適用したオブジェクトの読み書きおよび ACL の変更が可能になります|

!!! caution
    public-read-write はセキュリティの観点から利用を推奨しません。リスクをご検討のうえご利用ください。

また、デフォルトの既定 ACLは private が設定されています。公開を停止する場合は、private を設定してください。

#### バケットの公開 (public-read)

既定ACL public-read をバケットに適用することで、そのバケット配下のオブジェクトの一覧が公開されます。ここでは、以下のバケットを公開する例で説明します。

| 適用ACL| 公開バケット|
| :--| :--|
| public-read| test-pub|

バケット公開 (public-read) のACL設定は put-bucket-acl で設定します。設定の確認は、get-bucket-acl でおこないます。この場合は、public を示すURI "http://acs.amazonaws.com/groups/global/AllUsers" の Permission に READ が付与された Grantee が追加されます。

```
[username@es1 ~]$ aws --endpoint-url https://s3.abci.ai s3api put-bucket-acl --acl public-read --bucket test-pub
[username@es1 ~]$ aws --endpoint-url https://s3.abci.ai s3api get-bucket-acl --bucket test-pub
{
    "Owner": {
        "DisplayName": "gxx00000",
        "ID": "f12d0fa66ea4df5418c0c6234fd5eb3a9f4409bf50b5a58983a30be8f9a42bda"
    },
    "Grants": [
        {
            "Grantee": {
                "DisplayName": "gxx00000",
                "ID": "f12d0fa66ea4df5418c0c6234fd5eb3a9f4409bf50b5a58983a30be8f9a42bda",
                "Type": "CanonicalUser"
            },
            "Permission": "FULL_CONTROL"
        },
        {
            "Grantee": {
                "Type": "Group",
                "URI": "http://acs.amazonaws.com/groups/global/AllUsers"
            },
            "Permission": "READ"
        }
    ]
}
```

確認はインターネットブラウザで https://s3.abci.ai/test-pub (https://test-pub.s3.abci.ai) にアクセスすることで可能です。Firefoxの場合は、オブジェクトのリストを含むXMLが表示されます。

公開を停止し、初期値に戻す手順は以下のとおりです。
追加された Grantee がなくなり、ABCIグループ名がの Permission が "FULL_CONTROL" になっていることを確認して下さい。

```
[username@es1 ~]$ aws --endpoint-url https://s3.abci.ai s3api put-bucket-acl --acl private --bucket test-pub
[username@es1 ~]$ aws --endpoint-url https://s3.abci.ai s3api get-bucket-acl --bucket test-pub
{
    "Owner": {
        "DisplayName": "gxx00000",
        "ID": "f12d0fa66ea4df5418c0c6234fd5eb3a9f4409bf50b5a58983a30be8f9a42bda"
    },
    "Grants": [
        {
            "Grantee": {
                "DisplayName": "gxx00000",
                "ID": "f12d0fa66ea4df5418c0c6234fd5eb3a9f4409bf50b5a58983a30be8f9a42bda",
                "Type": "CanonicalUser"
            },
            "Permission": "FULL_CONTROL"
        }
    ]
}
```


#### オブジェクトの公開 (public-read)

既定ACL public-read をオブジェクトに適用することで、そのオブジェクトを公開することができます。ここでは、以下のオブジェクトを公開する例で説明します。

| 適用ACL| バケット| prefix| 公開オブジェクト|
| :--| :--| :--| :--|
| public-read| test-pub2|testdir/|message-pub|

オブジェクト公開 (public-read) のACL設定は put-object-acl で設定します。また、get-object-acl で設定状況を確認できます。この場合は、public を示すURI "http://acs.amazonaws.com/groups/global/AllUsers" の Permission に READ が付与された Grantee が追加されます。

```
[username@es1 ~]$ aws --endpoint-url https://s3.abci.ai s3api put-object-acl --bucket test-pub2 --acl public-read --key testdir/message-pub
[username@es1 ~]$ aws --endpoint-url https://s3.abci.ai s3api get-object-acl--bucket test-pub2 --key testdir/message-pub
{
    "Owner": {
        "DisplayName": "gxx00000",
        "ID": "f12d0fa66ea4df5418c0c6234fd5eb3a9f4409bf50b5a58983a30be8f9a42bda" 
    },
    "Grants": [
        {
            "Grantee": {
                "DisplayName": "gxx00000",
                "ID": "f12d0fa66ea4df5418c0c6234fd5eb3a9f4409bf50b5a58983a30be8f9a42bda",
                "Type": "CanonicalUser" 
            },
            "Permission": "FULL_CONTROL" 
        },
        {
            "Grantee": {
                "Type": "Group",
                "URI": "http://acs.amazonaws.com/groups/global/AllUsers" 
            },
            "Permission": "READ" 
        }
    ]
}
```

確認はインターネットブラウザで https://s3.abci.ai/test-pub2/testdir/message-pub (https://test-pub2.test-pub.s3.abci.ai/testdir/message-pub) にアクセスすることで可能です。Firefoxの場合は、オブジェクトのデータが表示されます。

公開を停止し、初期値に戻す手順は以下のとおりです。
追加された Grantee がなくなり、ABCIグループ名がの Permission が "FULL_CONTROL" になっていることを確認して下さい。

```
[username@es1 ~]$ aws --endpoint-url http://s3.abci.local s3api put-object-acl --acl private --bucket  test-pub2 --key testdir/testmessage
[username@es1 ~]$ aws --endpoint-url http://s3.abci.local s3api get-object-acl --bucket test-pub2 --key testdir/testmessage
{
    "Owner": {
        "DisplayName": "gxx00000",
        "ID": "f12d0fa66ea4df5418c0c6234fd5eb3a9f4409bf50b5a58983a30be8f9a42bda"
    },
    "Grants": [
        {
            "Grantee": {
                "DisplayName": "gxx00000",
                "ID": "f12d0fa66ea4df5418c0c6234fd5eb3a9f4409bf50b5a58983a30be8f9a42bda",
                "Type": "CanonicalUser"
            },
            "Permission": "FULL_CONTROL"
        }
    ]
}
```
