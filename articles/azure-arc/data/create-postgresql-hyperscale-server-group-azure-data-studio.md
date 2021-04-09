---
title: Azure Data Studio を使用して Azure Arc 対応 PostgreSQL Hyperscale を作成する
description: Azure Data Studio を使用して Azure Arc 対応 PostgreSQL Hyperscale を作成する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e2007d8f0c558d35c0507b6e12bce6d6777fad52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92310896"
---
# <a name="create-azure-arc-enabled-postgresql-hyperscale-using-azure-data-studio"></a>Azure Data Studio を使用して Azure Arc 対応 PostgreSQL Hyperscale を作成する

このドキュメントでは、Azure Data Studio を使用して Azure Arc 対応 PostgreSQL Hyperscale サーバー グループをプロビジョニングする手順について説明します。

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connect-to-the-azure-arc-data-controller"></a>Azure Arc データ コントローラーに接続する

Azure Arc データ コントローラーにまだログインしていない場合は、インスタンスを作成する前にログインします。

```console
azdata login
```

コントローラーにログインするには、データ コントローラーが作成されている名前空間、ユーザー名、パスワードを入力するように求められます。

> 名前空間を確認する必要がある場合は、```kubectl get pods -A``` を実行して、クラスター上のすべての名前空間の一覧を取得できます。

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>OpenShift ユーザー専用の暫定的および一時的な手順

この手順を実装してから、次の手順に進んでください。 既定以外のプロジェクトで PostgreSQL Hyperscale サーバー グループを Red Hat OpenShift にデプロイするには、クラスターに対して次のコマンドを実行して、セキュリティ制約を更新する必要があります。 このコマンドでは、PostgreSQL Hyperscale サーバー グループを実行するサービス アカウントに必要な特権が付与されます。 **_arc-data-scc_** というセキュリティ コンテキスト制約 (SCC) は、Azure Arc データ コントローラーのデプロイ時に追加したものです。

```console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

_**Server-group-name** は、次の手順でデプロイするサーバー グループの名前です。_
   
OpenShift の SCC の詳細については、[OpenShift のドキュメント](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html)を参照してください。
この時点で、次の手順を実装できます。

## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを作成する

1. Azure Data Studio を起動する
1. [接続] タブで、左上にある 3 つのドットをクリックし、[新しい展開] を選択します
1. デプロイ オプションから、 **[PostgreSQL Hyperscale サーバー グループ - Azure Arc]** を選択します
    >[!NOTE]
    > [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] が現在インストールされていない場合は、ここでインストールするように求められることがあります。
1. プライバシーとライセンス条項に同意し、下部にある **[選択]** をクリックします
1. [Deploy PostgreSQL Hyperscale server group - Azure Arc]\(PostgreSQL Hyperscale サーバー グループをデプロイする - Azure Arc\) ブレードで、次の情報を入力します。
   - サーバー グループの名前を入力します
   - サーバー グループの _postgres_ 管理者ユーザーのパスワードを入力して確認します
   - データに適したストレージ クラスを選択します
   - ログに適したストレージ クラスを選択します
   - バックアップに適したストレージ クラスを選択します
   - プロビジョニングするワーカー ノードの数を選択します
1. **[デプロイ]** ボタンをクリックします

これにより、データ コントローラーで Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの作成が開始されます。

作成が正常に完了するまでに数分かかる場合があります。

## <a name="next-steps"></a>次のステップ
- [Azure Data Studio を使用してサーバー グループを管理する](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md)
- [サーバー グループを監視する](monitor-grafana-kibana.md)
- 複数の PostgreSQL Hyperscale ノードにデータを分散し、Azure Database for Postgres Hyperscale のすべての利点を活用するために、Azure Database for PostgreSQL Hyperscale の概念と使い方に関するガイドを参照してください。 :
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
- [Kubernetes リソース モデル](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

