# ABCIクラウドストレージ

ABCIでは、Amazon Simple Storage Service (Amazon S3) 互換インターフェースを備えたオブジェクトストレージサービスを提供しています。以降、このサービスをABCIクラウドストレージと呼びます。

ABCIクラウドストレージには、ABCIで提供する他のストレージサービスにない次の特徴があります。

- Amazon S3 互換インタフェースを提供:  

    ABCI利用者は、Amazon S3と互換性のあるインタフェースを用いて、本ストレージ内で一意であるバケットの下にオブジェクトとしてデータを格納します。AWS Command Line Interface（AWS CLI）や s3cmd のような S3 互換クライアントを使用できます。boto を使ってクライアントツールを作ることもできます（ただし、一部サポートされていないAPIがあります）。AWS CLI の使用例は後述します。

- 計算ノード、ABCI外部の両方からアクセス可能:

    計算ノードやインタラクティブノードからのみならず、ABCI外部から直接アクセスできます。これにより、ジョブで使用するデータを外部からABCIに転送する手段として使えます。

- 暗号化機能を提供:

    クライアントとABCIクラウドストレージとの間のデータ転送を暗号化できます。また、ABCIクラウドストレージ内で暗号化してデータを保存しておくこともできます。

利用を開始するには、各ABCIグループの利用管理者からの申請が必要です。[ABCI利用ポータル](https://portal.abci.ai/user/)から申請を行ってください。利用管理者でない場合は、所属グループの利用管理者にお問い合わせください。ABCI利用ポータルの操作方法に関しては、[こちら](https://docs.abci.ai/portal/ja/)をご参照ください。

ABCIクラウドストレージでは、ABCIグループが所有するバケットに入っているデータ量に応じて、1日ごとにABCIポイントが差し引かれます。なお、データ転送量やAPIの呼び出し回数による課金は行っていません。[show_cs_usage](02.md#check-cloud-storage-usage) を使ってクラウドラウドストレージに保存したデータ量を確認できます。ABCIクラウドストレージのABCIポイントの計算式は以下の通りです。

<div class="codehilite"><pre>
ABCI point = ABCI Cloud Storage の使用量
           &times; ABCI Cloud Storage の単価
</pre></div>

ABCIクラウドストレージの単価は、[料金表](https://abci.ai/ja/how_to_use/tariffs.html) を確認してください。

| ページ名 | 概要 |
|:--|:--|
| [アカウントとアクセスキー](abci-cloudstorage/cs-account.md) | ABCIクラウドストレージで用いるアカウントと認証用のアクセスキーについて説明しています。 |
| [使い方](abci-cloudstorage/usage.md) | アップロードやダウンロードなどの基本操作について説明しています。 |
| [データの暗号化](abci-cloudstorage/encryption.md) | 暗号化の機能について説明しています。 |
| [アクセス制御（1）](abci-cloudstorage/acl.md) | ACLによるアクセス制御の方法を説明しています。グループ間でのデータ共有の設定などが可能です。|
| [アクセス制御（2）](abci-cloudstorage/policy.md) | アカウント毎にアクセス制御を行う方法を説明しています。ACLでは実現できないアクセス制御も可能ですが、本制御は管理者用クラウドストレージアカウントでのみ実行可能です。|
