---
title: Azure portal から Kubernetes リソースにアクセスする (プレビュー)
description: Azure portal から Azure Kubernetes Service (AKS) クラスターを管理するために Kubernetes リソースを操作する方法について説明します。
services: container-service
author: laurenhughes
ms.topic: article
ms.date: 08/11/2020
ms.author: lahugh
ms.openlocfilehash: 4a0acf284475f3c9119f3b9d012debad656b1faa
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661352"
---
# <a name="access-kubernetes-resources-from-the-azure-portal-preview"></a>Azure portal から Kubernetes リソースにアクセスする (プレビュー)

Azure portal には、Azure Kubernetes Service (AKS) クラスター内の Kubernetes リソースに簡単にアクセスするための Kubernetes リソース ビューアー (プレビュー) が含まれています。 Azure portal から Kubernetes リソースを表示すると、Azure portal と `kubectl` コマンド ライン ツールの間のコンテキストの切り替えが削減されるため、Kubernetes リソースを表示および編集するためのエクスペリエンスが効率化されます。 リソース ビューアーには現在、デプロイ、ポッド、レプリカ セットなどの複数のリソースの種類が含まれています。

Azure portal の Kubernetes リソース ビューによって、非推奨として設定されている [AKS ダッシュボード アドオン][kubernetes-dashboard]が置き換えられます。

>[!NOTE]
>この機能は現在のところ、[プライベート Azure Kubernetes Service クラスター](https://docs.microsoft.com/azure/aks/private-clusters)ではサポートされていません。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>前提条件

Azure portal で Kubernetes リソースを表示するには、AKS クラスターが必要です。 任意のクラスターがサポートされていますが、Azure Active Directory (Azure AD) 統合を使用している場合は、そのクラスターで [AKS マネージド Azure AD 統合][aks-managed-aad]を使用する必要があります。 クラスターでレガシ Azure AD を使用している場合は、ポータルまたは [Azure CLI][cli-aad-upgrade] でそのクラスターをアップグレードできます。

## <a name="view-kubernetes-resources"></a>Kubernetes リソースを表示する

Kubernetes リソースを表示するには、Azure portal で AKS クラスターに移動します。 左側のナビゲーション ウィンドウは、リソースにアクセスするために使用されます。 リソースには次のものがあります。

- **[名前空間]** には、クラスターの名前空間が表示されます。 名前空間の一覧の上部にあるフィルターを使用すると、名前空間リソースをすばやくフィルター処理して表示できます。
- **[ワークロード]** には、クラスターにデプロイされているデプロイ、ポッド、レプリカ セット、デーモン セットに関する情報が表示されます。 下のスクリーンショットは、サンプルの AKS クラスター内の既定のシステム ポッドを示しています。
- **[サービスとイングレス]** には、クラスターのすべてのサービスとイングレス リソースが表示されます。

:::image type="content" source="media/kubernetes-portal/workloads.png" alt-text="Azure portal に表示される Kubernetes ポッドの情報。" lightbox="media/kubernetes-portal/workloads.png":::

### <a name="deploy-an-application"></a>アプリケーションをデプロイする

この例では、サンプルの AKS クラスターを使用して、[AKS クイック スタート][portal-quickstart]から Azure Vote アプリケーションをデプロイします。

1. いずれかのリソース ビュー ([名前空間]、[ワークロード]、または [サービスとイングレス]) から **[追加]** を選択します。
1. [AKS クイック スタート][portal-quickstart]から Azure Vote アプリケーション用の YAML を貼り付けます。
1. YAML エディターの下部にある **[追加]** を選択してアプリケーションをデプロイします。 

YAML ファイルが追加されると、リソース ビューアーには作成された両方の Kubernetes サービス、つまり、内部サービス (azure-vote-back) と、Azure Vote アプリケーションにアクセスするための外部サービス (azure-vote-front) が表示されます。 外部サービスにはリンクされた外部 IP アドレスが含まれているため、ブラウザーでアプリケーションを簡単に表示できます。

:::image type="content" source="media/kubernetes-portal/portal-services.png" alt-text="Azure portal に表示される Azure Vote アプリケーションの情報。" lightbox="media/kubernetes-portal/portal-services.png":::

### <a name="monitor-deployment-insights"></a>デプロイ分析情報を監視する

[Azure Monitor for containers][enable-monitor] が有効になっている AKS クラスターでは、デプロイ分析情報をすばやく表示できます。 Kubernetes リソース ビューから、ユーザーは個々のデプロイのライブ状態 (CPU やメモリの使用率を含む) を表示できるほか、Azure Monitor に切り替えてより詳細な情報を確認できます。 サンプルの AKS クラスターのデプロイ分析情報の例を次に示します。

:::image type="content" source="media/kubernetes-portal/deployment-insights.png" alt-text="Azure portal に表示されるデプロイ分析情報。" lightbox="media/kubernetes-portal/deployment-insights.png":::

## <a name="edit-yaml"></a>YAML を編集する

Kubernetes リソース ビューには YAML エディターも含まれています。 組み込みの YAML エディターを使用すると、ポータル内からサービスやデプロイを更新または作成し、直ちに変更を適用できます。

:::image type="content" source="media/kubernetes-portal/service-editor.png" alt-text="Azure portal に表示される Kubernetes サービスの YAML エディター。":::

YAML を編集した後、 **[確認と保存]** を選択し、変更を確認して、再び保存することによって変更を適用します。

>[!WARNING]
> 運用環境の変更を UI または CLI 経由で直接実行することはお勧めできません。[継続的インテグレーション (CI) と継続的デプロイ (CD) のベスト プラクティス](kubernetes-action.md)を利用する必要があります。 Azure Portal の Kubernetes 管理機能や YAML エディターは、開発およびテストの設定での新しいデプロイの学習やフライティングのために構築されています。

## <a name="troubleshooting"></a>トラブルシューティング

このセクションでは、一般的な問題とトラブルシューティング手順について説明します。

### <a name="unauthorized-access"></a>未承認のアクセス

Kubernetes リソースにアクセスするには、AKS クラスター、Kubernetes API、Kubernetes オブジェクトへのアクセス権を持っている必要があります。 クラスター管理者か、または AKS クラスターにアクセスするための適切なアクセス許可を持つユーザーであることを確認してください。 クラスターのセキュリティの詳細については、[AKS でのアクセスと ID オプション][concepts-identity]に関するページを参照してください。

### <a name="enable-resource-view"></a>リソース ビューを有効にする

既存のクラスターでは、Kubernetes リソース ビューを有効にすることが必要な場合があります。 リソース ビューを有効にするには、クラスターのポータルでプロンプトに従います。

:::image type="content" source="media/kubernetes-portal/enable-resource-view.png" alt-text="Kubernetes リソース ビューを有効にするための Azure portal のメッセージ。" lightbox="media/kubernetes-portal/enable-resource-view.png":::

## <a name="next-steps"></a>次の手順

この記事では、AKS クラスターの Kubernetes リソースにアクセスする方法について説明しました。 Kubernetes リソース ビューアーを使用してアクセスされるクラスター リソースと YAML ファイルの詳細については、「[デプロイと YAML マニフェスト][deployments]」を参照してください。

<!-- LINKS - internal -->
[kubernetes-dashboard]: kubernetes-dashboard.md
[concepts-identity]: concepts-identity.md
[portal-quickstart]: kubernetes-walkthrough-portal.md#run-the-application
[deployments]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[aks-managed-aad]: managed-aad.md
[cli-aad-upgrade]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
[enable-monitor]: ../azure-monitor/insights/container-insights-enable-existing-clusters.md
