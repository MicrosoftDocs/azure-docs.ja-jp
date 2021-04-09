---
title: Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを作成する
description: Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを作成する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: 046f9d80c034e1ac1f2e7ffe144b4f389861b043
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101687942"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを作成する

このドキュメントでは、Azure Arc で PostgreSQL Hyperscale サーバー グループを作成する手順について説明します。

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="getting-started"></a>作業の開始
下記のトピックについて既によく理解している場合は、この段落をスキップしてください。
作成に進む前に、次のような重要なトピックを参照してください。
- [Azure Arc 対応データ サービスの概要](overview.md)
- [接続モードと要件](connectivity.md)
- [ストレージの構成と Kubernetes ストレージの概念](storage-configuration.md)
- [Kubernetes リソース モデル](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

お客様自身で完全な環境をプロビジョニングせずに試してみることを希望する場合は、Azure Kubernetes Service (AKS)、AWS Elastic Kubernetes Service (EKS)、Google Cloud Kubernetes Engine (GKE)、または Azure VM 上で [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) をすぐに開始できます。


## <a name="login-to-the-azure-arc-data-controller"></a>Azure Arc データ コントローラーにログインする

インスタンスを作成するには、まず Azure Arc データ コントローラーにログインする必要があります。 データ コントローラーに既にログインしている場合は、この手順をスキップできます。

```console
azdata login
```

その後、ユーザー名、パスワード、システム名前空間を入力するように求められます。  

> スクリプトを使用してデータ コントローラーを作成した場合は、名前空間を **arc** にする必要があります

```console
Namespace: arc
Username: arcadmin
Password:
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>OpenShift ユーザー専用の暫定的および一時的な手順
この手順を実装してから、次の手順に進んでください。 既定以外のプロジェクトで PostgreSQL Hyperscale サーバー グループを Red Hat OpenShift にデプロイするには、クラスターに対して次のコマンドを実行して、セキュリティ制約を更新する必要があります。 このコマンドでは、PostgreSQL Hyperscale サーバー グループを実行するサービス アカウントに必要な特権が付与されます。 arc-data-scc というセキュリティ コンテキスト制約 (SCC) は、Azure Arc データ コントローラーのデプロイ時に追加したものです。

```Console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

**Server-group-name は、次の手順で作成するサーバー グループの名前です。**

OpenShift の SCC の詳細については、[OpenShift のドキュメント](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html)を参照してください。 この時点で、次の手順を実装できます。


## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを作成する

Arc データ コントローラーで Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを作成するには、いくつかのパラメーターを渡す `azdata arc postgres server create` コマンドを使用します。

作成時に設定できるすべてのパラメーターの詳細については、コマンドの出力を確認してください。
```console
azdata arc postgres server create --help
```

検討する必要がある主なパラメーターは以下のとおりです。
- デプロイしようとしている **サーバー グループの名前**。 `--name` または `-n` の後に名前を指定します。この名前は 11 文字以下にする必要があります。

- デプロイしようとしている **PostgreSQL エンジンのバージョン**: 既定ではバージョン 12 です。 バージョン 12 をデプロイするには、このパラメーターを省略するか、パラメーターとして `--engine-version 12` または `-ev 12` のいずれかを渡します。 バージョン 11 をデプロイするには、`--engine-version 11` または `-ev 11` を指定します。

- スケールアウトによるパフォーマンスの向上を目的としてデプロイしようとしている **ワーカー ノードの数**。 ここに進む前に、[Postgres Hyperscale の概念](concepts-distributed-postgres-hyperscale.md)に関するページを参照してください。 デプロイするワーカー ノードの数を指定するには、パラメーター `--workers` または `-w` を使用し、その後に 2 以上の整数を指定します。 たとえば、2 つのワーカー ノードを含むサーバー グループをデプロイする場合は、`--workers 2` または `-w 2` を指定します。 これで 3 つのポッドが作成されます。1 つはコーディネーター ノード/インスタンス用、2 つはワーカー ノード/インスタンス用です (各ワーカーに 1 つ)。

- サーバー グループで使おうとしている **ストレージ クラス**。 ストレージ クラスはデプロイ後に変更することはできないため、サーバー グループをデプロイする時点で設定することが重要です。 もしデプロイ後にストレージ クラスを変更するとすれば、データの抽出、サーバー グループの削除、新しいサーバー グループの作成、データのインポートが必要になります。 データ、ログ、バックアップ用に使用するストレージ クラスを指定することもできます。 既定では、ストレージ クラスを指定しない場合、データ コントローラーのストレージ クラスが使用されます。
    - データ用のストレージ クラスを設定するには、パラメーター `--storage-class-data` または `-scd` を指定し、その後にストレージ クラスの名前を指定します。
    - ログ用のストレージ クラスを設定するには、パラメーター `--storage-class-logs` または `-scl` を指定し、その後にストレージ クラスの名前を指定します。
    - バックアップ用のストレージ クラスを設定するには: Azure Arc 対応 PostgreSQL Hyperscale のこのプレビューでは、実行しようとしているバックアップ/復元操作の種類に応じて、ストレージ クラスを設定する方法が 2 つあります。 Microsoft は、このエクスペリエンスの簡素化に取り組んでいます。 ストレージ クラスまたはボリューム要求マウントのいずれかを指定します。 ボリューム要求マウントは、(同じ名前空間内の) 既存の永続的ボリューム要求と、コロンで区切られたボリュームの種類 (およびボリュームの種類に応じたオプションのメタデータ) のペアです。 永続的ボリュームは、PostgreSQL サーバー グループの各ポッドにマウントされます。
        - 実行する予定があるのはデータベースの完全復元だけである場合は、パラメーター `--storage-class-backups` または `-scb` を設定し、その後にストレージ クラスの名前を指定します。
        - データベースの完全復元とポイントインタイム リストアの両方を行う予定の場合は、パラメーター `--volume-claim-mounts` または `-vcm` を設定し、その後にボリューム要求の名前とボリュームの種類を指定します。

作成コマンドを実行すると、既定の `postgres` 管理ユーザーのパスワードを入力するよう求められ ます。 そのユーザーの名前は、このプレビューでは変更できません。 作成コマンドを実行する前に `AZDATA_PASSWORD` セッション環境変数を設定すると、対話型プロンプトをスキップすることができます。

### <a name="examples"></a>例

**データ コントローラーと同じストレージ クラスを使用する 2 つのワーカー ノードが含まれる、postgres01 という名前の Postgres バージョン 12 のサーバー グループをデプロイするには、次のコマンドを実行します。**
```console
azdata arc postgres server create -n postgres01 --workers 2
```

**データとログのためのデータ コントローラーと同じストレージ クラスを使用する 2 つのワーカー ノードが含まれる、postgres01 という名前の Postgres バージョン 12 のサーバーグループをデプロイするには、以下の手順を実行します。**

 この例は、お使いのサーバー グループが Azure Kubernetes Service (AKS) クラスターでホストされていることを前提としています。 この例では、ストレージ クラス名として azurefile-premium を使用します。 次の例を実際の環境に合わせて調整してもかまいません。 この構成には、**accessModes として ReadWriteMany が必要** であることに注意してください。  

まず、以下のバックアップ PVC の説明を含む YAML ファイルを作成し、たとえば CreateBackupPVC.yml という名前を付けます。
```console
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: backup-pvc
  namespace: arc
spec:
  accessModes:
    - ReadWriteMany
  volumeMode: Filesystem
  resources:
    requests:
      storage: 100Gi
  storageClassName: azurefile-premium
```

次に、YAML ファイルに格納されている定義を使用して PVC を作成します。

```console
kubectl create -f e:\CreateBackupPVC.yml -n arc
``` 

次に、サーバー グループを作成します。

```console
azdata arc postgres server create -n postgres01 --workers 2 -vcm backup-pvc:backup
```

> [!IMPORTANT]
> - [バックアップ/復元に関連する現在の制限事項](limitations-postgresql-hyperscale.md#backup-and-restore)をお読みください。


> [!NOTE]  
> - 同じターミナル セッションで `AZDATA_USERNAME` および `AZDATA_PASSWORD` セッション環境変数を使用してデータ コントローラーをデプロイした場合は、PostgreSQL Hyperscale サーバー グループをデプロイするために `AZDATA_PASSWORD` の値も使用されます。 別のパスワードを使用する場合は、(1) `AZDATA_PASSWORD` の値を更新するか、(2) `AZDATA_PASSWORD` 環境変数を削除するか、(3) その値を削除すると、サーバー グループを作成するときに対話形式でパスワードの入力を求められます。
> - PostgreSQL Hyperscale サーバー グループを作成しても、リソースはすぐに Azure に登録されません。 [リソース インベントリ](upload-metrics-and-logs-to-azure-monitor.md)または[利用状況データ](view-billing-data-in-azure.md)を Azure にアップロードするプロセスの一環として、Azure でリソースが作成され、そのリソースを Azure portal で確認できるようになります。



## <a name="list-the-postgresql-hyperscale-server-groups-deployed-in-your-arc-data-controller"></a>Arc データ コントローラーにデプロイされている PostgreSQL Hyperscale サーバー グループを一覧表示する

Arc データ コントローラーにデプロイされている PostgreSQL Hyperscale サーバー グループを一覧表示するには、次のコマンドを実行します。

```console
azdata arc postgres server list
```


```output
Name        State     Workers
----------  --------  ---------
postgres01  Ready     2
```

## <a name="get-the-endpoints-to-connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Azure Arc 対応 PostgreSQL Hyperscale サーバー グループに接続するためのエンドポイントを取得する

PostgreSQL サーバー グループのエンドポイントを表示するには、次のコマンドを実行します。

```console
azdata arc postgres endpoint list -n <server group name>
```
次に例を示します。
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

PostgreSQL インスタンスのエンドポイントを使用すると、[Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)、[pgcli](https://www.pgcli.com/) psql、pgAdmin などのお気に入りのツールから、PostgreSQL Hyperscale サーバー グループに接続できます。そうするときには、分散テーブルを作成済みの場合はクエリが適切なワーカー ノード/インスタンスにルーティングされるよう処理するコーディネーター ノード/インスタンスに接続します。 詳細については、[Azure Arc 対応 PostgreSQL Hyperscale の概念](concepts-distributed-postgres-hyperscale.md)に関するページを参照してください。

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Azure 仮想マシンのデプロイに関する特別な注意事項

Azure 仮想マシンを使用している場合は、エンドポイントの IP アドレスに _パブリック_ IP アドレスが表示されません。 パブリック IP アドレスを検索するには、次のコマンドを使用します。

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

その後、パブリック IP アドレスとポートを組み合わせて接続できます。

また、ネットワーク セキュリティ ゲートウェイ (NSG) を使用して PostgreSQL Hyperscale サーバー グループのポートを公開する必要がある場合もあります。 NSG 経由のトラフィックを許可するには、次のコマンドを使用して実行できる規則を追加する必要があります。

規則を設定するには、NSG の名前を把握しておく必要があります。 次のコマンドを使用して、NSG を特定します。

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

NSG の名前を取得したら、次のコマンドを使用してファイアウォール規則を追加できます。 この例の値は、ポート 30655 に対する NSG 規則を作成し、**任意の** ソース IP アドレスからの接続を許可します。  これは、セキュリティ上、ベスト プラクティスとはいえません。  クライアントの IP アドレスや、チームまたは組織の IP アドレスが含まれる IP アドレス範囲に固有の -source-address-prefixes 値を指定すると、適切にロック ダウンできます。

以下の --destination-port-ranges パラメーターの値は、上記の 'azdata arc postgres server list' コマンドで取得されたポート番号に置き換えてください。

```azurecli
az network nsg rule create -n db_port --destination-port-ranges 30655 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="connect-with-azure-data-studio"></a>Azure Data Studio で接続する

Azure Data Studio を開き、上記の外部エンドポイントの IP アドレスとポート番号、およびインスタンスの作成時に指定したパスワードを使用してインスタンスに接続します。  PostgreSQL が *[接続の種類]* ドロップダウンに表示されない場合は、[拡張] タブで PostgreSQL を検索して、PostgreSQL 拡張機能をインストールできます。

> [!NOTE]
> 接続パネルの [詳細] ボタンをクリックして、ポート番号を入力する必要があります。

Azure VM を使用している場合は、次のコマンドを使用してアクセスできる _パブリック_ IP アドレスが必要であることを忘れないでください。

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

## <a name="connect-with-psql"></a>psql を使用して接続する

PostgreSQL Hyperscale サーバー グループにアクセスするには、前の手順で取得した PostgreSQL Hyperscale サーバー グループの外部エンドポイントを渡します。

この時点で、psql のいずれかに接続できます。

```console
psql postgresql://postgres:<EnterYourPassword>@10.0.0.4:30655
```

## <a name="next-steps"></a>次のステップ

- 複数の PostgreSQL Hyperscale ノードにわたってデータを分散させ、パフォーマンス向上の可能性があるという利点を得るため、Azure Database for PostgreSQL Hyperscale の概念と使い方に関するガイドを参照してください。
    * [ノードとテーブル](../../postgresql/concepts-hyperscale-nodes.md)
    * [アプリケーションの種類の決定](../../postgresql/concepts-hyperscale-app-type.md)
    * [ディストリビューション列の選択](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [テーブル コロケーション](../../postgresql/concepts-hyperscale-colocation.md)
    * [テーブルの配布と変更](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [マルチテナント データベースを設計する](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [リアルタイム分析ダッシュボードを設計する](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \*上記のドキュメントの「**Azure portal にサインインする**」と「**Azure Database for PostgreSQL - Hyperscale (Citus) を作成する**」セクションはスキップしてください。 Azure Arc デプロイの残りの手順を実装します。 これらのセクションは Azure クラウドで PaaS サービスとして提供される Azure Database for PostgreSQL Hyperscale (Citus) に固有のものですが、ドキュメントの他の部分は Azure Arc 対応 PostgreSQL Hyperscale に直接適用できます。

- [Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのスケールアウト](scale-out-postgresql-hyperscale-server-group.md)
- [ストレージの構成と Kubernetes ストレージの概念](storage-configuration.md)
- [永続ボリューム要求の拡張](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Kubernetes リソース モデル](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
