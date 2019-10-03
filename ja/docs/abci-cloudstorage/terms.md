
# 用語

## クラウドストレージアカウント

ABCI利用者に作られるABCIクラウドストレージ用のアカウントです。各ABCIグループごとに発行されます。例えば所属しているグループのうち 2つのグループで利用可能となった場合には、aaa00000aa.1 と aaa00000aa.2 といった 2つのクラウドストレージアカウントを持つことになります。1つのグループに複数クラウドストレージアカウントを持つことができ、例えば開発中のアプリケーション用に aaa00000aa.3 を追加で持つことができます(名前の指定はできません)。1グループあたり1人最大10個までで、2つのグループに所属していればそれぞれ10個、合計20個のクラウドストレージアカウントを持つことになります(グループ内で10個という意味ではなく、グループ内に4人いれば、そのグループ内には最大40個存在します)。

ABCIグループの利用責任者または利用管理者には、利用者用とは別に、管理者用のクラウドストレージアカウントが1つ発行されます。こちらは 1グループあたり1人1つです(グループの管理者が4人がいれば、グループ内には4つ存在し、ある利用者が 2グループにおいて管理者であるならば、それぞれ1つずつ計2つ持ちます)。

!!! note
    利用ポータルから申請できるようになると(現在準備中)、管理者用を発行するかしないか取捨選択できるようになります

### 管理者用とそうではないクラウドストレージアカウントの違い

管理者用のクラウドストレージアカウントは、データのアップロードやダウンロードなど、利用者用のクラウドストレージアカウントが行えることの他に、ポリシーの設定を行える権限を持っています。ポリシーとは、バケットやオブジェクトに設定するACLとは別に、クラウドストレージアカウントに対してアクセス許可を設定するためのものです。デフォルトで設定されている権限のままで使う場合には変更する必要はなく、必ず設定を行わなければいけないものではありません。

## アクセスキー

ABCIから提供されるアクセスキー ID とシークレットアクセスキーのペアを指します。ABCIクラウドストレージへのアクセスに使います。シークレットアクセスキーは、第三者に見せたり第三者から見えるところに置いてはいけません。1つのクラウドストレージアカウントが持つことのできるアクセスキーの数は、2つまでです。

<!--
## サブグループ

グループ内に作るグループです。アクセスポリシーの適用などに使います。
-->