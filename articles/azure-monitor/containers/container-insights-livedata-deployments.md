---
title: Container insights の Deployments (プレビュー) の表示 | Microsoft Docs
description: この記事では、Container insights で kubectl を使用しない、Kubernetes Deployments のリアル タイム表示について説明します。
ms.topic: conceptual
ms.date: 10/15/2019
ms.custom: references_regions
ms.openlocfilehash: c6eaac209234f7352395502c6761312cf258108f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101713917"
---
# <a name="how-to-view-deployments-preview-in-real-time"></a>リアルタイムで Deployments (プレビュー) を表示する方法

Container insights の Deployments (プレビュー) の表示機能では、`kubeclt get deployments` と `kubectl describe deployment {your deployment}` コマンドを公開することにより、Kubernetes Deployment オブジェクトへの直接アクセスがリアルタイムでエミュレートされます。

>[!NOTE]
>[プライベート クラスター](https://azure.microsoft.com/updates/aks-private-cluster/)として有効にされた AKS クラスターは、この機能でサポートされていません。 この機能では、ブラウザーからプロキシ サーバーを介した Kubernetes API への直接アクセスを利用します。 このプロキシから Kubernetes API をブロックするようにネットワーク セキュリティを有効にすると、このトラフィックはブロックされます。

詳細については、[Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) に関する Kubernetes のドキュメントを参照してください。

## <a name="how-it-works"></a>しくみ

Live Data (プレビュー) 機能は、Kubernetes API に直接アクセスします。認証モデルの詳細については、[こちら](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)を参照してください。

Deployments (プレビュー) 機能では、デプロイ エンドポイント `/apis/apps/v1/deployments` に対して 1 回 (更新可能) の読み込みが実行されます。 これにより、特定のデプロイを選択し、その特定のデプロイの詳細情報をデプロイ エンドポイント `/apis/apps/v1/namespaces/${nameSpace}/deployments/${deploymentName}` に対して読み込むことができます。

ページの左上にある **[更新]** を選択すると、デプロイの一覧が更新されます。 これにより、`kubectl` コマンドの再実行がシミュレートされます。

>[!IMPORTANT]
>この機能の操作中、データが永続的に保存されることはありません。 セッション中にキャプチャされたすべての情報は、ブラウザーを閉じるかブラウザーから移動すると削除されます。

>[!NOTE]
>コンソールからの Live Data (プレビュー) データを Azure ダッシュボードにピン留めすることはできません。

## <a name="deployments-describe"></a>Deployments の説明

デプロイの詳細説明を表示するには (`kubectl describe deployment` に相当する)、次の手順を行います。

1. Azure portal で、AKS クラスター リソース グループを参照し、AKS リソースを選択します。

2. AKS クラスター ダッシュボードで、左側の **[監視]** の下で、 **[Insights]** を選択します

3. **[Deployments (プレビュー)]** タブを選択します。

    ![Azure portal での Deployments ビュー](./media/container-insights-livedata-deployments/deployment-view.png)

このビューには、稼働中のすべてのデプロイの一覧に加えて、名前空間およびその他の詳細情報が表示され、コマンド `kubectl get deployments –all-namespaces` の実行がエミュレートされます。 列のいずれかを選択することで、結果を並べ替えることができます。

![Deployments プロパティ ウィンドウの詳細](./media/container-insights-livedata-deployments/deployment-properties-pane-details.png)

一覧からデプロイを選択すると、ページの右側にプロパティ ウィンドウが自動的に表示されます。 そこには、選択したデプロイに関連する情報が表示されます。この情報は、コマンド `kubectl describe deployment {deploymentName}` を実行した場合に表示されるものです。 説明情報にいくつかの詳細が不足していることに気づいたのではないでしょうか。 特に **[テンプレート]** がありません。 **[未加工]** タブを選択すると、解析されていない詳細説明に移動できます。

![Deployments プロパティ ウィンドウの未加工の詳細](./media/container-insights-livedata-deployments/deployment-properties-pane-raw.png)

デプロイに関する詳細のレビュー中は、コンテナーのログとイベントをリアルタイムで確認できます。 **[Show live console]\(ライブ コンソールの表示\)** を選択すると、Live Data (プレビュー) コンソール ウィンドウがデプロイ データ グリッドの下に表示されます。そこでは、連続したストリームでライブ ログ データを表示できます。 フェッチ状態インジケーターのウィンドウの一番右に緑色のチェック マークが表示される場合は、データを取得できること、およびコンソールへのストリーミングが開始されることを意味します。

名前空間またはクラスター レベルのイベントでフィルター処理することもできます。 コンソールにデータをリアルタイムで表示する方法について詳しくは、[Container insights を使用したライブ データ (プレビュー) の表示](container-insights-livedata-overview.md)に関するページを参照してください。

![コンソールでの Deployments ビューのライブ データ](./media/container-insights-livedata-deployments/deployments-console-view-events.png)

## <a name="next-steps"></a>次のステップ

- Azure Monitor を使用して、AKS クラスターの他の側面を監視する方法を引き続き学習するには、[Azure Kubernetes Service の正常性の表示](container-insights-analyze.md)に関するページをご覧ください。

- アラートの作成、視覚化の作成、またはクラスターの詳細な分析を行うために、定義済みのクエリや例を確認するには、[ログ クエリの例](container-insights-log-search.md#search-logs-to-analyze-data)を参照してください。
