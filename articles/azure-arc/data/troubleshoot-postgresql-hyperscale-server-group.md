---
title: PostgreSQL Hyperscale サーバー グループをトラブルシューティングする
description: Jupyter Notebook を使用した PostgreSQL Hyperscale サーバー グループをトラブルシューティングする
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: caaab07200a8631935a2b5d5368a0c16ea9a60c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92320225"
---
# <a name="troubleshooting-postgresql-hyperscale-server-groups"></a>PostgreSQL Hyperscale サーバー グループのトラブルシューティング
この記事では、サーバー グループのトラブルシューティングに使用できるいくつかの手法について説明します。 この記事に加えて、[Kibana](monitor-grafana-kibana.md) を使用してログを検索する方法や、[Grafana](monitor-grafana-kibana.md) を使用してサーバー グループに関するメトリックを視覚化する方法についても必要になる場合があります。 

## <a name="getting-more-details-about-the-execution-of-an-azdata-command"></a>azdata コマンドの実行に関する詳細情報の取得
実行する azdata コマンドにパラメーター **--debug** を追加できます。 このようにすると、そのコマンドの実行に関する追加情報がコンソールに表示されます。 そのコマンドの動作を理解するのに役立つ詳細情報を取得するのに便利です。
たとえば、次のように実行できます
```console
azdata arc postgres server create -n postgres01 -w 2 --debug
```

or
```console
azdata arc postgres server edit -n postgres01 --extension SomeExtensionName --debug
```

また、任意の azdata コマンドで --help パラメーターを使用して、特定のコマンドのヘルプやパラメーターの一覧を表示することもできます。 次に例を示します。
```console
azdata arc postgres server create --help
```


## <a name="collecting-logs-of-the-data-controller-and-your-server-groups"></a>データ コントローラーとサーバー グループのログの収集
[Azure Arc 対応データ サービスのログの取得](troubleshooting-get-logs.md)に関する記事を参照してください



## <a name="interactive-troubleshooting-with-jupyter-notebooks-in-azure-data-studio"></a>Azure Data Studio での Jupyter ノートブックによる対話型トラブルシューティング
ノートブックでは、マークダウン コンテンツを追加して、実行内容や実行方法を説明することで、プロシージャをドキュメント化できます。 また、プロシージャを自動化する実行可能コードを提供することもできます。  このパターンは、標準的な運用手順からトラブルシューティング ガイドまで、あらゆるものに役立ちます。

たとえば、Azure Data Studio を使用していくつかの問題が発生している可能性のある PostgreSQL Hyperscale サーバー グループのトラブルシューティングしてみましょう。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

### <a name="install-tools"></a>ツールをインストールする

Azure Data Studio でノートブックを実行するために使用しているクライアント コンピューターに、Azure Data Studio、`kubectl`、および [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] をインストールします。 これを行うには、[クライアント ツールのインストール](install-client-tools.md)に関する手順に従ってください。

### <a name="update-the-path-environment-variable"></a>PATH 環境変数を更新する

これらのツールをこのクライアント コンピューターの任意の場所から呼び出すことができることを確認します。 たとえば、Windows クライアント コンピューターで PATH システム環境変数を更新し、kubectl をインストールしたフォルダーを追加します。

### <a name="sign-in-with-azure-data-cli-azdata"></a>[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] でサインインする

Azure Data Studio を起動する前に、このクライアント コンピューターから Arc データ コントローラーにサインインします。 これを行うには、次のようにコマンドを実行します。

```console
azdata login --endpoint https://<IP address>:<port>
```

`<IP address>` を Kubernetes クラスターの IP アドレス、`<port>` を Kubernetes がリッスンしているポートに置換します。 ユーザー名とパスワードの入力を求められます。 詳しい情報を表示するには、_ を実行します。

```console
azdata login --help
```

### <a name="log-into-your-kubernetes-cluster-with-kubectl"></a>kubectl を使用して Kubernetes クラスターにログインする

これを行うには、[この](https://blog.christianposta.com/kubernetes/logging-into-a-kubernetes-cluster-with-kubectl/)ブログ記事に記載されているコマンド例を使用することができます。
次のようなコマンドを実行します。

```console
kubectl config view
kubectl config set-credentials kubeuser/my_kubeuser --username=<your Arc Data Controller Admin user name> --password=<password>
kubectl config set-cluster my_kubeuser --server=https://<IP address>:<port>
kubectl config set-context default/my_kubeuser/ArcDataControllerAdmin --user=ArcDataControllerAdmin/my_kubeuser --namespace=arc --cluster=my_kubeuser
kubectl config use-context default/my_kubeuser/ArcDataControllerAdmin
```

#### <a name="the-troubleshooting-notebook"></a>トラブルシューティング ノートブック

Azure Data Studio を起動して、トラブルシューティング ノートブックを開きます。 

[033-manage-Postgres-with-AzureDataStudio.md](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md) で説明されている手順を実装して次の操作を行います。

1. Arc データ コントローラーに接続します。
2. Postgres インスタンスを右クリックして、 **[管理]** を選択します。
3. **[問題の診断と解決]** を選択します。
4. **[トラブルシューティング] リンク** を選択します。

:::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook.jpg" alt-text="Azure Data Studio - PostgreSQL トラブルシューティング ノートブックを開く":::

**TSG100 - Azure Arc 対応の PostgreSQL Hyperscale トラブルシューティング ノートブック** が開きます。:::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook2.jpg" alt-text="Azure Data Studio - PostgreSQL のトラブルシューティング ノートブックを使用する":::

#### <a name="run-the-scripts"></a>スクリプトの実行
上部にある [すべて実行] ボタンをクリックしてノートブックを一度にすべて実行するか、各コード セルを 1 つずつステップ実行して実行することができます。

潜在的な問題について、コード セルの実行の出力を確認します。

一般的な問題を認識する方法とその解決方法について、ノートブックに詳細を追加する予定です。

## <a name="next-step"></a>次の手順
- [Azure Arc 対応データ サービスのログを取得](troubleshooting-get-logs.md)する方法を参照してください。
- [Kibana を使用してログを検索する](monitor-grafana-kibana.md)方法を参照してください。
- [Grafana を使用して監視する](monitor-grafana-kibana.md)方法を参照してください。
- 独自のノートブックを作成する
