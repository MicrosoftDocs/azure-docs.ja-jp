---
title: Azure portal から Kubernetes リソースにアクセスする
description: Azure portal から Azure Kubernetes Service (AKS) クラスターを管理するために Kubernetes リソースを操作する方法について説明します。
services: container-service
ms.topic: article
ms.date: 12/16/2020
ms.openlocfilehash: ce5dc74dc3625b2b1fed447c4e6480308267d32a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100578687"
---
# <a name="access-kubernetes-resources-from-the-azure-portal"></a>Azure portal から Kubernetes リソースにアクセスする

Azure portal には、Azure Kubernetes Service (AKS) クラスター内の Kubernetes リソースに簡単にアクセスするための Kubernetes リソース ビューが含まれています。 Azure portal から Kubernetes リソースを表示すると、Azure portal と `kubectl` コマンド ライン ツールの間のコンテキストの切り替えが削減されるため、Kubernetes リソースを表示および編集するためのエクスペリエンスが効率化されます。 リソース ビューアーには現在、デプロイ、ポッド、レプリカ セットなどの複数のリソースの種類が含まれています。

Azure portal の Kubernetes リソース ビューによって、非推奨となった [AKS ダッシュボード アドオン][kubernetes-dashboard]が置き換えられます。

## <a name="prerequisites"></a>前提条件

Azure portal で Kubernetes リソースを表示するには、AKS クラスターが必要です。 任意のクラスターがサポートされていますが、Azure Active Directory (Azure AD) 統合を使用している場合は、そのクラスターで [AKS マネージド Azure AD 統合][aks-managed-aad]を使用する必要があります。 クラスターでレガシ Azure AD を使用している場合は、ポータルまたは [Azure CLI][cli-aad-upgrade] でそのクラスターをアップグレードできます。 [Azure portal を使用][portal-cluster]して、新しい AKS クラスターを作成することもできます。

## <a name="view-kubernetes-resources"></a>Kubernetes リソースを表示する

Kubernetes リソースを表示するには、Azure portal で AKS クラスターに移動します。 左側のナビゲーション ウィンドウは、リソースにアクセスするために使用されます。 リソースには次のものがあります。

- **[名前空間]** には、クラスターの名前空間が表示されます。 名前空間の一覧の上部にあるフィルターを使用すると、名前空間リソースをすばやくフィルター処理して表示できます。
- **[ワークロード]** には、クラスターにデプロイされているデプロイ、ポッド、レプリカ セット、ステートフル セット、デーモン セット、ジョブ、cron ジョブに関する情報が表示されます。 下のスクリーンショットは、サンプルの AKS クラスター内の既定のシステム ポッドを示しています。
- **[サービスとイングレス]** には、クラスターのすべてのサービスとイングレス リソースが表示されます。
- **[ストレージ]** には、Azure ストレージ クラスと永続ボリュームの情報が表示されます。
- **[構成]** には、クラスターの構成マップとシークレットが表示されます。

:::image type="content" source="media/kubernetes-portal/workloads.png" alt-text="Azure portal に表示される Kubernetes ポッドの情報。" lightbox="media/kubernetes-portal/workloads.png":::

### <a name="deploy-an-application"></a>アプリケーションをデプロイする

この例では、サンプルの AKS クラスターを使用して、[AKS クイック スタート][portal-quickstart]から Azure Vote アプリケーションをデプロイします。

1. いずれかのリソース ビュー ([名前空間]、[ワークロード]、[サービスとイングレス]、[ストレージ]、または [構成]) から **[追加]** を選択します。
1. [AKS クイック スタート][portal-quickstart]から Azure Vote アプリケーション用の YAML を貼り付けます。
1. YAML エディターの下部にある **[追加]** を選択してアプリケーションをデプロイします。 

YAML ファイルが追加されると、リソース ビューアーには作成された両方の Kubernetes サービス、つまり、内部サービス (azure-vote-back) と、Azure Vote アプリケーションにアクセスするための外部サービス (azure-vote-front) が表示されます。 外部サービスにはリンクされた外部 IP アドレスが含まれているため、ブラウザーでアプリケーションを簡単に表示できます。

:::image type="content" source="media/kubernetes-portal/portal-services.png" alt-text="Azure portal に表示される Azure Vote アプリケーションの情報。" lightbox="media/kubernetes-portal/portal-services.png":::

### <a name="monitor-deployment-insights"></a>デプロイ分析情報を監視する

[Azure Monitor for containers][enable-monitor] が有効になっている AKS クラスターでは、デプロイ情報やその他の分析情報をすばやく表示できます。 Kubernetes リソース ビューから、ユーザーは個々のデプロイのライブ状態 (CPU やメモリの使用率を含む) を表示できるほか、Azure Monitor に切り替えて、特定のノードやコンテナーに関するより詳細な情報を確認できます。 サンプルの AKS クラスターのデプロイ分析情報の例を次に示します。

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

>[!NOTE]
> Azure Portal の kubernetes リソース ビューは、[マネージド AAD が有効なクラスター](managed-aad.md)または非 AAD が有効なクラスターでのみサポートされています。 マネージド AAD が有効なクラスターを使用している場合、AAD ユーザーまたは ID には、[ユーザーの `kubeconfig`](control-kubeconfig-access.md) をプルする権限に加えて、kubernetes API にアクセスするための、対応するロールまたはロールのバインドが存在している必要があります。

### <a name="enable-resource-view"></a>リソース ビューを有効にする

既存のクラスターでは、Kubernetes リソース ビューを有効にすることが必要な場合があります。 リソース ビューを有効にするには、クラスターのポータルでプロンプトに従います。

> [!TIP]
> [**API サーバーの許可された IP 範囲**](api-server-authorized-ip-ranges.md)に対して AKS 機能を追加し、API サーバーのアクセスをファイアウォールのパブリック エンドポイントのみに制限できます。 このようなクラスターの別のオプションとして、`--api-server-authorized-ip-ranges` を更新して、ローカル クライアント コンピューターや (ポータルが閲覧されている) IP アドレスの範囲へのアクセスを含めることができます。 このアクセスを許可するには、コンピューターのパブリック IPv4 アドレスが必要です。 このアドレスは、下のコマンドを使用するか、インターネット ブラウザーで "what is my IP address" と検索することで確認できます。
```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

## <a name="next-steps"></a>次の手順

この記事では、AKS クラスターの Kubernetes リソースにアクセスする方法について説明しました。 Kubernetes リソース ビューアーを使用してアクセスされるクラスター リソースと YAML ファイルの詳細については、「[デプロイと YAML マニフェスト][deployments]」を参照してください。

<!-- LINKS - internal -->
[kubernetes-dashboard]: kubernetes-dashboard.md
[concepts-identity]: concepts-identity.md
[portal-quickstart]: kubernetes-walkthrough-portal.md#run-the-application
[deployments]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[aks-managed-aad]: managed-aad.md
[cli-aad-upgrade]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
[enable-monitor]: ../azure-monitor/containers/container-insights-enable-existing-clusters.md
[portal-cluster]: kubernetes-walkthrough-portal.md