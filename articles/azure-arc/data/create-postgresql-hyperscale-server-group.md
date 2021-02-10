---
title: Azure Arc で Azure Database for PostgreSQL Hyperscale サーバー グループを作成する
description: Azure Arc で Azure Database for PostgreSQL Hyperscale サーバー グループを作成する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 45bb045e7bad2d5f8a56b71787b3abb5921cb7d5
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98985888"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを作成する

このドキュメントでは、Azure Arc で PostgreSQL Hyperscale サーバー グループを作成する手順について説明します。

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="getting-started"></a>作業の開始
次のトピックについて既によく理解している場合は、この段落をスキップしてください。
作成に進む前に、次のような重要なトピックを参照してください。
- [Azure Arc 対応データ サービスの概要](overview.md)
- [接続モードと要件](connectivity.md)
- [ストレージの構成と Kubernetes ストレージの概念](storage-configuration.md)
- [Kubernetes リソース モデル](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

お客様自身で完全な環境をプロビジョニングせずに使ってみたい場合は、Azure Kubernetes Service (AKS)、AWS Elastic Kubernetes Service (EKS)、Google Cloud Kubernetes Engine (GKE)、または Azure VM 上で [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) をすぐに開始できます。


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

この手順を実装してから、次の手順に進んでください。 既定以外のプロジェクトで PostgreSQL Hyperscale サーバー グループを Red Hat OpenShift にデプロイするには、クラスターに対して次のコマンドを実行して、セキュリティ制約を更新する必要があります。 このコマンドでは、PostgreSQL Hyperscale サーバー グループを実行するサービス アカウントに必要な特権が付与されます。 **_arc-data-scc_** というセキュリティ コンテキスト制約 (SCC) は、Azure Arc データ コントローラーのデプロイ時に追加したものです。

```console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

_**Server-group-name** は、次の手順で作成するサーバー グループの名前です。_
   
OpenShift の SCC の詳細については、[OpenShift のドキュメント](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html)を参照してください。
この時点で、次の手順を実装できます。

## <a name="create-an-azure-database-for-postgresql-hyperscale-server-group"></a>Azure Database for PostgreSQL Hyperscale サーバー グループを作成する

Azure Arc で Azure Database for PostgreSQL Hyperscale サーバー グループを作成するには、次のコマンドを使用します。

```console
azdata arc postgres server create -n <name> --workers <# worker nodes with #>=2> --storage-class-data <storage class name> --storage-class-logs <storage class name> --storage-class-backups <storage class name>

#Example
#azdata arc postgres server create -n postgres01 --workers 2
```

> [!IMPORTANT]
> - バックアップに使用するストレージ クラス ( _--storage-class-backups -scb_) は、データ コントローラーのデータ ストレージ クラスに既定で設定されます (設定されていない場合)。
> - サーバー グループを個別のサーバー グループに復元するには (ポイントインタイム リストアなど)、ReadWriteMany アクセス モードで PVC を使用するようにサーバー グループを構成する必要があります。 この操作は、サーバー グループの作成時に行う必要があります。 作成後にそれを変更することはできません。 詳細については、以下を参照してください。
>    - [バックアップと復元に関するこちらのセクション](https://docs.microsoft.com/azure/azure-arc/data/backup-restore-postgresql-hyperscale#create-a-server-group-that-is-ready-for-backups-and-restores)
>    - [Azure Arc 対応 PostgreSQL Hyperscale の制限に関するこちらのセクション](https://docs.microsoft.com/azure/azure-arc/data/limitations-postgresql-hyperscale)


> [!NOTE]
> - **他のコマンドライン パラメーターも使用できます。`azdata arc postgres server create --help` を実行して、オプションの完全なリストを確認してください。**

> - --volume-size-* パラメーターで使用できる単位は、Kubernetes リソースの数量 (後に SI サフィックス (T、G、M、K、M) または 2 のべき乗の同等物 (Ti、Gi、Mi、Ki) のいずれかが続く整数) です。
> - 名前は、12 文字以下の長さで入力し、DNS 名前付け規則に準拠している必要があります。
> - _postgres_ という標準管理ユーザーのパスワードを入力するように求められます。  create コマンドを実行する前に、`AZDATA_PASSWORD` セッション環境変数を設定すると、対話型プロンプトをスキップできます。
> - 同じターミナル セッションで AZDATA_USERNAME および AZDATA_PASSWORD セッション環境変数を使用してデータ コントローラーをデプロイした場合は、PostgreSQL Hyperscale サーバー グループをデプロイするために AZDATA_PASSWORD の値も使用されます。 別のパスワードを使用する場合は、(1) AZDATA_PASSWORD の値を更新します。または、(2) AZDATA_PASSWORD 環境変数またはその値を削除すると、サーバー グループを作成するときに対話形式でパスワードの入力を求められます。
> - PostgreSQL Hyperscale データベース エンジンの既定の管理者ユーザーの名前は _postgres_ であり、この時点では変更できません。
> - PostgreSQL Hyperscale サーバー グループを作成しても、リソースはすぐに Azure に登録されません。 [リソース インベントリ](upload-metrics-and-logs-to-azure-monitor.md)または[利用状況データ](view-billing-data-in-azure.md)を Azure にアップロードするプロセスの一環として、Azure でリソースが作成され、そのリソースを Azure portal で確認できるようになります。



## <a name="list-your-azure-database-for-postgresql-server-groups-created-in-your-arc-setup"></a>Arc のセットアップ時に作成された Azure Database for PostgreSQL サーバー グループを一覧表示する

Azure Arc で PostgreSQL Hyperscale サーバー グループを表示するには、次のコマンドを使用します。

```console
azdata arc postgres server list
```


```output
Name        State     Workers
----------  --------  ---------
postgres01  Ready     2
```

## <a name="get-the-endpoints-to-connect-to-your-azure-database-for-postgresql-server-groups"></a>Azure Database for PostgreSQL サーバー グループに接続するエンドポイントを取得する

PostgreSQL インスタンスのエンドポイントを表示するには、次のコマンドを実行します。

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

PostgreSQL インスタンスのエンドポイントを使用すると、お気に入りのツールから PostgreSQL Hyperscale サーバー グループに接続できます ([Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)、[pgcli](https://www.pgcli.com/) psql、pgAdmin など)。

Azure VM を使用してテストしている場合は、次の手順に従います。

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

- 複数の PostgreSQL Hyperscale ノードにデータを分散し、Azure Database for PostgreSQL Hyperscale のすべての利点を活用するために、Azure Database for PostgreSQL Hyperscale の概念と使い方に関するガイドを参照してください。 :
    * [ノードとテーブル](../../postgresql/concepts-hyperscale-nodes.md)
    * [アプリケーションの種類の決定](../../postgresql/concepts-hyperscale-app-type.md)
    * [ディストリビューション列の選択](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [テーブル コロケーション](../../postgresql/concepts-hyperscale-colocation.md)
    * [テーブルの配布と変更](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [マルチテナント データベースを設計する](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [リアルタイム分析ダッシュボードを設計する](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \*上記のドキュメントの「**Azure portal にサインインする**」と「**Azure Database for PostgreSQL - Hyperscale (Citus) を作成する**」セクションはスキップしてください。 Azure Arc デプロイの残りの手順を実装します。 これらのセクションは Azure クラウドで PaaS サービスとして提供される Azure Database for PostgreSQL Hyperscale (Citus) に固有のものですが、ドキュメントの他の部分は Azure Arc 対応 PostgreSQL Hyperscale に直接適用できます。

- [Azure Database for PostgreSQL Hyperscale サーバー グループのスケールアウト](scale-out-postgresql-hyperscale-server-group.md)
- [ストレージの構成と Kubernetes ストレージの概念](storage-configuration.md)
- [永続ボリューム要求の拡張](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Kubernetes リソース モデル](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)