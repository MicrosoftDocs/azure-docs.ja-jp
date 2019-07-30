---
title: 仮想マシンのコンテンツを監査する方法の概要
description: Azure Policy が Rego および Open Policy Agent を使用して、Azure Kubernetes Service 上でクラスターを管理する方法について説明します。
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: fdb392533e28df1d50e90c842d0117385afb254b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454606"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Azure Kubernetes Service に対する Azure Policy の理解

Azure Policy は [Azure Kubernetes Service](../../../aks/intro-kubernetes.md) (AKS) と統合することにより、一元的な一貫性のある方法でクラスターを大規模に適用して保護できます。
[GateKeeper](https://github.com/open-policy-agent/gatekeeper) ([Open Policy Agent](https://www.openpolicyagent.org/) (OPA) のための_アドミッション コントローラー Webhook_) の使用を拡張することにより、Azure Policy では Azure リソースおよび AKS クラスターのコンプライアンスの状態を 1 つの場所から管理および報告することが可能になります。

> [!NOTE]
> AKS 用の Azure Policy は限定プレビューで、組み込みのポリシー定義のみをサポートします。

## <a name="overview"></a>概要

AKS クラスターで AKS 用の Azure Policy を有効にして使用するには、次の操作を実行します。

- [プレビュー機能のためのオプトイン](#opt-in-for-preview)
- [Azure Policy アドオンをインストールする](#installation-steps)
- [AKS のポリシー定義を割り当てる](#built-in-policies)
- [検証を待機する](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>プレビューのためのオプトイン

Azure Policy アドオンをインストールするか、いずれかのサービス機能を有効にする前に、サブスクリプションは **Microsoft.ContainerService** リソース プロバイダーと **Microsoft.PolicyInsights** リソース プロバイダーを有効にする必要があり、その後プレビューへの参加が承認されるようにする必要があります。 プレビューに参加するには、Azure portal または Azure CLI のいずれかで次の手順を実行します。

- Azure portal:

  1. **Microsoft.ContainerService** と **Microsoft.PolicyInsights** リソース プロバイダーに登録します。 手順については、「[リソース プロバイダーと種類](../../../azure-resource-manager/resource-manager-supported-services.md#azure-portal)」を参照してください。

  1. Azure portal 上で **[すべてのサービス]** をクリックし、 **[ポリシー]** を検索して選択し、Azure Policy サービスを起動します。

     ![[すべてのサービス] で [ポリシー] を検索する](../media/rego-for-aks/search-policy.png)

  1. Azure Policy ページの左側にある **[プレビューに参加する]** を選択します。

     ![AKS プレビューのポリシーに参加する](../media/rego-for-aks/join-aks-preview.png)

  1. プレビューに追加するサブスクリプションの行を選択します。

  1. サブスクリプションの一覧の上部にある **[オプトイン]** ボタンを選択します。

- Azure CLI:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Services provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove

  # Feature register: enables the add-on to call the Azure Policy resource provider
  az feature register --namespace Microsoft.PolicyInsights --name AKS-DataplaneAutoApprove
  ```

## <a name="azure-policy-add-on"></a>Azure Policy アドオン

Kubernetes 用の _Azure Policy アドオン_は、Azure Policy サービスを GateKeeper アドミッション コントローラーに接続します。 _azure-policy_ 名前空間にインストールされるこのアドオンは、次の機能を実行します。

- Azure Policy で AKS クラスターへの割り当てをチェックします
- _rego_ ポリシー定義などのポリシー詳細をダウンロードし、**configmaps** としてキャッシュします
- AKS クラスターでフル スキャンのコンプライアンス チェックを実行します
- 監査およびコンプライアンスの詳細を Azure Policy に報告します

### <a name="installing-the-add-on"></a>アドオンのインストール

#### <a name="prerequisites"></a>前提条件

AKS クラスターにアドオンをインストールする前に、プレビュー拡張機能をインストールする必要があります。 この手順は Azure CLI で実行されます。

1. Azure CLI バージョン 2.0.62 以降がインストールされて構成されている必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

1. AKS クラスターは、バージョン _1.10_ 以上である必要があります。 AKS クラスターのバージョンを検証するには、次のスクリプトを使用します。

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. AKS 用の Azure CLI プレビュー拡張機能 `aks-preview` のバージョン _0.4.0_ をインストールします。

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > 以前に _aks-preview_ 拡張機能をインストール済みの場合は、`az extension update --name aks-preview` コマンドを使用して、すべての更新プログラムをインストールします。

#### <a name="installation-steps"></a>インストール手順

前提条件が完了したら、管理する AKS クラスターに Azure Policy アドオンをインストールします。

- Azure ポータル

  1. Azure portal で **[すべてのサービス]** をクリックし、 **[Kubernetes サービス]** を検索して選択することによって AKS サービスを起動します。

  1. お使いのいずれかの AKS クラスターを選択します。

  1. [Kubernetes サービス] ページの左側の **[ポリシー (プレビュー)]** を選択します。

     ![AKS クラスターからのポリシー](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. メイン ページで、 **[アドオンを有効にする]** ボタンを選択します。

     ![AKS 用の Azure Policy アドオンを有効にする](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > **[アドオンを有効にする]** ボタンが淡色表示されている場合、サブスクリプションはまだプレビューに追加されていません。 必要な手順については「[プレビューのためのオプトイン](#opt-in-for-preview)」を参照してください。

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>検証および報告の頻度

アドオンは5 分おきに、ポリシーの割り当ての変更について Azure Policy でチェックインします。 この更新サイクル中に、アドオンは _azure-policy_ 名前空間のすべての _configmaps_ を削除し、GateKeeper が使用する _configmaps_ を再作成します。

> [!NOTE]
> _クラスター管理者_は _azure-policy_ 名前空間に対する権限を持つ場合がありますが、名前空間を変更することは推奨されず、サポートもされません。 手動で行ったすべての変更は、更新サイクル中に失われます。

アドオンは 5 分ごとにクラスターのフル スキャンを呼び出します。 アドオンは、フル スキャンの詳細情報と、クラスターに試行された変更についての GateKeeper によるすべてのリアルタイム評価を収集した後、すべての Azure Policy 割り当てと同様に、[[ポリシー準拠状況の詳細]](../how-to/get-compliance-data.md) に含めるために結果を Azure Policy に報告します。 アクティブなポリシー割り当ての結果のみが監査サイクル中に返されます。

## <a name="policy-language"></a>ポリシーの言語

AKS を管理するための Azure Policy 言語構造は、既存のポリシーのものに従います。 _EnforceRegoPolicy_ 効果が AKS クラスターを管理するために使用され、OPA および GateKeeper の操作に固有の _details_ プロパティが使用されます。 詳細と例については、[EnforceRegoPolicy](effects.md#enforceregopolicy) 効果を参照してください。

ポリシー定義の _details.policy_ プロパティの一部として、Azure Policy は rego ポリシーの URI をアドオンに渡します。 Rego は、Kubernetes クラスターへの要求を検証または変更するために OPA および GateKeeper がサポートする言語です。 Azure Policy は、Kubernetes 管理のための既存の標準をサポートすることによって、既存の規則を再利用し、これらを Azure Policy とペアにすることで、統合されたクラウド コンプライアンス レポート体験を実現します。 詳しくは、「[Rego とは](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego)」を参照してください。

## <a name="built-in-policies"></a>組み込みのポリシー

Azure portal を使用して AKS を管理するための組み込みのポリシーを検索するには、次の手順に従います。

1. Azure portal で Azure Policy サービスを開始します。 左側のウィンドウで **[すべてのサービス]** を選択し、 **[ポリシー]** を検索して選択します。

1. Azure Policy ページの左側のウィンドウで、 **[定義]** を選択します。

1. カテゴリ ドロップダウン リスト ボックスから、 **[すべて選択]** を使用してフィルターをクリアし、 **[Kubernetes サービス]** を選択します。

1. ポリシー定義を選択し、 **[割り当てる]** ボタンを選択します。

> [!NOTE]
> AKS 用の Azure Policy 定義を割り当てるとき、 **[スコープ]** に AKS クラスター リソースを含める必要があります。

あるいは、「[ポリシーの割り当て - ポータル](../assign-policy-portal.md)」クイックスタートを使用して、AKS ポリシーを見つけて割り当てます。 サンプルの 'audit vms' ではなく、Kubernetes ポリシー定義を検索します。

## <a name="logging"></a>ログの記録

### <a name="azure-policy-add-on-logs"></a>Azure Policy アドオン ログ

Azure Policy アドオンは、Kubernetes のコント ローラー/コンテナーとして、AKS クラスター内にログを保持します。 このログは AKS クラスターの **[Insights]** ページに公開されます。 詳しくは、「[コンテナーの Azure Monitor を使用して AKS クラスターのパフォーマンスを把握する](../../../azure-monitor/insights/container-insights-analyze.md)」をご覧ください。

### <a name="gatekeeper-logs"></a>GateKeeper ログ

新規リソース要求についての GateKeeper ログを有効にするには、「[AKS での Kubernetes マスター ノード ログの有効化とレビュー](../../../aks/view-master-logs.md)」の手順に従います。
新規リソース要求に関して拒否されたイベントを表示するためのクエリの例を以下に示します。

```kusto
| where Category == "kube-audit"
| where log_s contains "admission webhook"
| limit 100
```

GateKeeper コンテナーからログを表示するには、「[AKS での Kubernetes マスター ノード ログの有効化とレビュー](../../../aks/view-master-logs.md)」の手順に従い、 **[診断設定]** ウィンドウで _kube-apiserver_ オプションを確認します。

## <a name="remove-the-add-on"></a>アドオンを削除する

AKS クラスターから Azure Policy アドオンを削除するには、Azure portal または Azure CLI のいずれかを使用します。

- Azure ポータル

  1. Azure portal で **[すべてのサービス]** をクリックし、 **[Kubernetes サービス]** を検索して選択することによって AKS サービスを起動します。

  1. Azure Policy アドオンを無効にする AKS クラスターを選択します。

  1. [Kubernetes サービス] ページの左側の **[ポリシー (プレビュー)]** を選択します。

     ![AKS クラスターからのポリシー](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. メイン ページで、 **[アドオンを無効にする]** ボタンを選択します。

     ![AKS 用の Azure Policy アドオンを無効にする](../media/rego-for-aks/disable-policy-add-on.png)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

## <a name="next-steps"></a>次の手順

- [Azure Policy のサンプル](../samples/index.md)を確認します。
- [ポリシー定義の構造](definition-structure.md)を確認します。
- 「[Policy の効果について](effects.md)」を確認します。
- [プログラムによってポリシーを作成する](../how-to/programmatically-create.md)方法を理解します。
- [コンプライアンス データを取得する](../how-to/getting-compliance-data.md)方法を学習します。
- [準拠していないリソースを修復する](../how-to/remediate-resources.md)方法を学習します。
- 「[Azure 管理グループのリソースを整理する](../../management-groups/index.md)」で、管理グループとは何かを確認します。