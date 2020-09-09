---
title: プレビュー - Kubernetes 用の Azure Policy について学習する
description: Azure Policy で Rego および Open Policy Agent を使用して、Azure 内またはオンプレミスで Kubernetes を実行しているクラスターを管理する方法について説明します。 これはプレビュー機能です。
ms.date: 08/07/2020
ms.topic: conceptual
ms.openlocfilehash: e9da5caf13994e1c198345958feec43867c0b5f5
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509877"
---
# <a name="understand-azure-policy-for-kubernetes-clusters-preview"></a>Kubernetes 用の Azure Policy について理解する (プレビュー)

Azure Policy によって [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) のための "_アドミッション コントローラー Webhook_" である [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3 が拡張され、一貫性した一元的な方法でクラスターに対して大規模な実施と保護が適用されます。 Azure Policy を使用すると、Kubernetes クラスターのコンプライアンスの状態を 1 か所から管理し、レポートすることができます。 アドオンにより、次の機能が設定されます。

- Azure Policy サービスを使用してクラスターへのポリシー割り当てをチェックします。
- ポリシーを定義を[制約テンプレート](https://github.com/open-policy-agent/gatekeeper#constraint-templates)および[制約](https://github.com/open-policy-agent/gatekeeper#constraints)カスタム リソースとしてクラスターにデプロイします。
- 監査およびコンプライアンスの詳細を Azure Policy サービスに報告します。

Kubernetes 用の Azure Policy では、次のクラスター環境がサポートされています。

- [Azure Kubernetes Service (AKS)](../../../aks/intro-kubernetes.md)
- [Azure Arc 対応 Kubernetes](../../../azure-arc/kubernetes/overview.md)
- [AKS エンジン](https://github.com/Azure/aks-engine/blob/master/docs/README.md)

> [!IMPORTANT]
> Kubernetes 用の Azure Policy はプレビュー中であり、Linux ノード プールと組み込みのポリシー定義のみをサポートします。 組み込みのポリシー定義は、**Kubernetes** カテゴリ内にあります。 **EnforceOPAConstraint** および **EnforceRegoPolicy** 効果を持つ限定プレビュー ポリシー定義と、関連する **Kubernetes Service** カテゴリは "_非推奨_" になっています。 代わりに、リソース プロバイダー モード `Microsoft.Kubernetes.Data` で "_audit_" および "_deny_" 効果を使用します。

## <a name="overview"></a>概要

Kubernetes クラスターで Azure Policy を有効にして使用するには、次の操作を実行します。

1. Kubernetes クラスターを構成し、アドオンをインストールする:
   - [Azure Kubernetes Service (AKS)](#install-azure-policy-add-on-for-aks)
   - [Azure Arc 対応 Kubernetes](#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)
   - [AKS エンジン](#install-azure-policy-add-on-for-aks-engine)

   > [!NOTE]
   > インストールの一般的な問題については、[Azure Policy アドオンのトラブルシューティング](../troubleshoot/general.md#add-on-installation-errors)に関するページを参照してください。

1. [Kubernetes 用の Azure Policy 言語を理解する](#policy-language)

1. [組み込みの定義を Kubernetes クラスターに割り当てる](#assign-a-built-in-policy-definition)

1. [検証を待機する](#policy-evaluation)

## <a name="install-azure-policy-add-on-for-aks"></a>AKS 用の Azure Policy アドオンをインストールする

Azure Policy アドオンをインストールするか、このサービスの機能のいずれかを有効にする前に、お客様のサブスクリプションで **Microsoft.ContainerService** および **Microsoft.PolicyInsights** リソース プロバイダーを有効にする必要があります。

1. Azure CLI バージョン 2.0.62 以降がインストールされて構成されている必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

1. リソース プロバイダーとプレビュー機能を登録します。

   - Azure portal:

     1. **Microsoft.ContainerService** と **Microsoft.PolicyInsights** リソース プロバイダーに登録します。 手順については、「[リソース プロバイダーと種類](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)」を参照してください。

     1. Azure portal 上で **[すべてのサービス]** をクリックし、 **[ポリシー]** を検索して選択し、Azure Policy サービスを起動します。

        :::image type="content" source="../media/policy-for-kubernetes/search-policy.png" alt-text="[すべてのサービス] で [ポリシー] を検索する" border="false":::

     1. Azure Policy ページの左側にある **[プレビューに参加する]** を選択します。

        :::image type="content" source="../media/policy-for-kubernetes/join-aks-preview.png" alt-text="AKS プレビューのポリシーに参加する" border="false":::

     1. プレビューに追加するサブスクリプションの行を選択します。

     1. サブスクリプションの一覧の上部にある **[オプトイン]** ボタンを選択します。

   - Azure CLI:

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Kubernetes Service provider
     az provider register --namespace Microsoft.ContainerService

     # Provider register: Register the Azure Policy provider
     az provider register --namespace Microsoft.PolicyInsights

     # Feature register: enables installing the add-on
     az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove

     # Use the following to confirm the feature has registered
     az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].   {Name:name,State:properties.state}"

     # Once the above shows 'Registered' run the following to propagate the update
     az provider register -n Microsoft.ContainerService
     ```

1. 限定プレビュー ポリシー定義がインストールされていた場合は、AKS クラスターの **[ポリシー (プレビュー)]** ページで **[無効]** ボタンを使用して、アドオンを削除します。

1. AKS クラスターは、バージョン _1.14_ 以降である必要があります。 AKS クラスターのバージョンを検証するには、次のスクリプトを使用します。

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

上記の前提条件ステップが完了したら、管理する AKS クラスターに Azure Policy アドオンをインストールします。

- Azure portal

  1. Azure portal で **[すべてのサービス]** をクリックし、 **[Kubernetes サービス]** を検索して選択することによって AKS サービスを起動します。

  1. お使いのいずれかの AKS クラスターを選択します。

  1. [Kubernetes サービス] ページの左側の **[ポリシー (プレビュー)]** を選択します。

     :::image type="content" source="../media/policy-for-kubernetes/policies-preview-from-aks-cluster.png" alt-text="AKS クラスターからのポリシー定義" border="false":::

  1. メイン ページで、 **[アドオンを有効にする]** ボタンを選択します。

     :::image type="content" source="../media/policy-for-kubernetes/enable-policy-add-on.png" alt-text="AKS 用の Azure Policy アドオンを有効にする":::

     <a name="migrate-from-v1"></a>
     > [!NOTE]
     > **[アドオンを有効にする]** ボタンが淡色表示されている場合、サブスクリプションはまだプレビューに追加されていません。 **[アドオンを無効にする]** ボタンが有効になっていて、移行警告 v2 メッセージが表示された場合は、v1 アドオンがインストールされており、v2 ポリシー定義を割り当てる前に削除する必要があります。 _非推奨_の v1 アドオンは、2020 年 8 月 24 日以降、v2 アドオンに自動的に置き換えられます。 その後、新しい v2 バージョンのポリシー定義を割り当てる必要があります。 アップグレードするには、次の手順を実行します。
     >
     > 1. AKS クラスターの **[ポリシー (プレビュー)]** ページにアクセスして、"現在のクラスターでは Azure Policy アドオン v1 が使用されています..." というメッセージが表示されることを確認することによって、AKS クラスターに v1 アドオンがインストールされていることを検証します。
     > 1. [アドオンを削除します](#remove-the-add-on-from-aks)。
     > 1. **[アドオンを有効にする]** ボタンを選択して、アドオンの v2 バージョンをインストールします。
     > 1. [v1 組み込みポリシー定義の v2 バージョンを割り当てます](#assign-a-built-in-policy-definition)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

アドオンが正常にインストールされていることと、_azure-policy_ および _gatekeeper_ ポッドが実行されていることを確認するには、次のコマンドを実行します。

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

最後に、`<rg>` をリソース グループ名に置き換え、`<cluster-name>` を AKS クラスターの名前に置き換えて Azure CLI コマンド `az aks show -g <rg> -n <cluster-name>` を実行することにより、最新のアドオンがインストールされていることを確認します。 結果は次の出力のようになり、**config.version** は `v2` になります。

```output
"addonProfiles": {
    "azurepolicy": {
        "config": {
            "version": "v2"
        },
        "enabled": true,
        "identity": null
    },
}
```

## <a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes"></a>Azure Arc 対応 Kubernetes 用の Azure Policy アドオンのインストール

Azure Policy アドオンをインストールするか、このサービスの機能のいずれかを有効にする前に、お客様のサブスクリプションで **Microsoft.PolicyInsights** リソース プロバイダーを有効にし、クラスター サービス プリンシパルのロール割り当てを作成する必要があります。

1. Azure CLI バージョン 2.0.62 以降がインストールされて構成されている必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

1. リソース プロバイダーを有効にするには、[リソース プロバイダーと種類](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)に関する記事の手順を実行するか、Azure CLI または Azure PowerShell コマンドを実行します。

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. Kubernetes クラスターは、バージョン _1.14_ 以降である必要があります。

1. [Helm 3](https://v3.helm.sh/docs/intro/install/) をインストールします。

1. Kubernetes クラスターが Azure Arc に対して有効になります。詳細については、[Azure Arc への Kubernetes クラスターのオンボード](../../../azure-arc/kubernetes/connect-cluster.md)に関する記事を参照してください。

1. Azure Arc 対応 Kubernetes クラスターの完全修飾 Azure リソース ID を用意します。

1. アドオンのポートを開きます。 Azure Policy アドオンでは、これらのドメインとポートを使用して、ポリシー定義と割り当てがフェッチされ、クラスターのコンプライアンスが Azure Policy に報告されます。

   |Domain |Port |
   |---|---|
   |`gov-prod-policy-data.trafficmanager.net` |`443` |
   |`raw.githubusercontent.com` |`443` |
   |`login.windows.net` |`443` |
   |`dc.services.visualstudio.com` |`443` |

1. 'Policy Insights データ ライター (プレビュー)' ロールの割り当てを Azure Arc 対応 Kubernetes クラスターに割り当てます。 `<subscriptionId>` をサブスクリプション ID に、`<rg>` を Azure Arc 対応 Kubernetes クラスターのリソース グループに、`<clusterName>` を Azure Arc 対応 Kubernetes クラスターの名前に置き換えます。 インストール ステップのために、_appId_、_password_、および _tenant_ の戻り値を記録しておきます。

   - Azure CLI

     ```azurecli-interactive
     az ad sp create-for-rbac --role "Policy Insights Data Writer (Preview)" --scopes "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     $sp = New-AzADServicePrincipal -Role "Policy Insights Data Writer (Preview)" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"

     @{ appId=$sp.ApplicationId;password=[System.Runtime.InteropServices.Marshal]::PtrToStringAuto([System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret));tenant=(Get-AzContext).Tenant.Id } | ConvertTo-Json
     ```

   上記のコマンドの出力例:

   ```json
   {
       "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
       "password": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
       "tenant": "cccccccc-cccc-cccc-cccc-cccccccccccc"
   }
   ```

上記の前提条件ステップが完了したら、Azure Arc 対応 Kubernetes クラスターに Azure Policy アドオンをインストールします。

1. Azure Policy アドオン リポジトリを Helm に追加します。

   ```bash
   helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
   ```

1. Helm Chart を使用して Azure Policy アドオンをインストールします。

   ```bash
   # In below command, replace the following values with those gathered above.
   #    <AzureArcClusterResourceId> with your Azure Arc enabled Kubernetes cluster resource Id. For example: /subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>
   #    <ServicePrincipalAppId> with app Id of the service principal created during prerequisites.
   #    <ServicePrincipalPassword> with password of the service principal created during prerequisites.
   #    <ServicePrincipalTenantId> with tenant of the service principal created during prerequisites.
   helm install azure-policy-addon azure-policy/azure-policy-addon-arc-clusters \
       --set azurepolicy.env.resourceid=<AzureArcClusterResourceId> \
       --set azurepolicy.env.clientid=<ServicePrincipalAppId> \
       --set azurepolicy.env.clientsecret=<ServicePrincipalPassword> \
       --set azurepolicy.env.tenantid=<ServicePrincipalTenantId>
   ```

   Helm Chart によってインストールされるアドオンの詳細については、GitHub 上の [Azure Policy アドオンの Helm Chart 定義](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-arc-clusters)に関する記事を参照してください。

アドオンが正常にインストールされていることと、_azure-policy_ および _gatekeeper_ ポッドが実行されていることを確認するには、次のコマンドを実行します。

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="install-azure-policy-add-on-for-aks-engine"></a>AKS エンジン用の Azure Policy アドオンをインストールする

Azure Policy アドオンをインストールするか、このサービスの機能のいずれかを有効にする前に、お客様のサブスクリプションで **Microsoft.PolicyInsights** リソース プロバイダーを有効にし、クラスター サービス プリンシパルのロール割り当てを作成する必要があります。

1. Azure CLI バージョン 2.0.62 以降がインストールされて構成されている必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

1. リソース プロバイダーを有効にするには、[リソース プロバイダーと種類](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)に関する記事の手順を実行するか、Azure CLI または Azure PowerShell コマンドを実行します。

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. クラスター サービス プリンシパルのロールの割り当てを作成します。

   - クラスター サービス プリンシパル アプリ ID がわからない場合は、次のコマンドを使用して確認します。

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system

     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Azure CLI を使用して、'Policy Insights Data Writer (Preview)' ロールの割り当てをクラスター サービス プリンシパル アプリ ID (前の手順の値 _aadClientID_) に割り当てます。 `<subscriptionId>` をお使いのサブスクリプション ID に、`<aks engine cluster resource group>` を AKS エンジンのセルフマネージド Kubernetes クラスターが属するリソース グループに置き換えます。

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

上記の前提条件ステップが完了したら、Azure Policy アドオンをインストールします。 インストールは、AKS エンジンの作成時または更新サイクル時に行うか、既存のクラスターに対して独立した操作として行うことができます。

- 作成または更新サイクル時にインストールする

  新しいセルフマネージド クラスターの作成時、または既存のクラスターに対する更新として Azure Policy アドオンを有効にするには、AKS エンジン用の [addons](https://github.com/Azure/aks-engine/tree/master/examples/addons/azure-policy) プロパティ クラスター定義を含めます。

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true
  }]
  ```

  詳細については、外部ガイドの [AKS エンジン クラスター定義](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md)を参照してください。

- Helm Chart を含む既存のクラスターにインストールする

  次の手順でクラスターを準備し、アドオンをインストールします。

  1. [Helm 3](https://v3.helm.sh/docs/intro/install/) をインストールします。

  1. Azure Policy リポジトリを Helm に追加します。

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     詳細については、[Helm Chart のクイックスタート ガイド](https://helm.sh/docs/using_helm/#quickstart-guide)のページを参照してください。

  1. Helm Chart を使用してアドオンをインストールします。 `<subscriptionId>` をお使いのサブスクリプション ID に、`<aks engine cluster resource group>` を AKS エンジンのセルフマネージド Kubernetes クラスターが属するリソース グループに置き換えます。

     ```bash
     helm install azure-policy-addon azure-policy/azure-policy-addon-aks-engine --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Helm Chart によってインストールされるアドオンの詳細については、GitHub 上の [Azure Policy アドオンの Helm Chart 定義](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-aks-engine)に関する記事を参照してください。

     > [!NOTE]
     > Azure Policy アドオンとリソース グループ ID の関係があるため、Azure Policy は各リソース グループに対して 1 つの AKS エンジン クラスターのみをサポートします。

アドオンが正常にインストールされていることと、_azure-policy_ および _gatekeeper_ ポッドが実行されていることを確認するには、次のコマンドを実行します。

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="policy-language"></a>ポリシーの言語

Kubernetes を管理するための Azure Policy 言語構造は、既存のポリシー定義のものに従います。 `Microsoft.Kubernetes.Data` の[リソース プロバイダー モード](./definition-structure.md#resource-provider-modes)では、Kubernetes クラスターを管理するために、"[audit](./effects.md#audit)" および "[deny](./effects.md#deny)" 効果が使用されます。 "_audit_" および "_deny_" によって、[OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint) および Gatekeeper v3 の操作に固有の **details** プロパティが提供される必要があります。

Azure Policy からはアドオンに対して、ポリシー定義の _details.constraintTemplate_ および _details.constraint_ プロパティの一部として [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) の URI が渡されます。 Rego は、Kubernetes クラスターへの要求を検証するために OPA および Gatekeeper がサポートする言語です。 Azure Policy は、Kubernetes 管理のための既存の標準をサポートすることによって、既存の規則を再利用し、これらを Azure Policy とペアにすることで、統合されたクラウド コンプライアンス レポート体験を実現します。 詳しくは、「[Rego とは](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego)」を参照してください。

## <a name="assign-a-built-in-policy-definition"></a>組み込みポリシー定義の割り当て

ポリシー定義を Kubernetes クラスターに割り当てるには、適切なロールベースのアクセス制御 (RBAC) ポリシー割り当て操作が割り当てられている必要があります。 Azure 組み込みのロール **リソース ポリシー共同作成者**と**所有者**には、これらの操作があります。 詳細については、「[Azure Policy における RBAC アクセス許可](../overview.md#rbac-permissions-in-azure-policy)」を参照してください。

次の手順に従って、Azure portal を使用してクラスターを管理する組み込みのポリシー定義を確認します。

1. Azure portal で Azure Policy サービスを開始します。 左側のウィンドウで **[すべてのサービス]** を選択し、 **[ポリシー]** を検索して選択します。

1. Azure Policy ページの左側のウィンドウで、 **[定義]** を選択します。

1. カテゴリ ドロップダウン リスト ボックスから、 **[すべて選択]** を使用してフィルターをクリアし、 **[Kubernetes]** を選択します。

1. ポリシー定義を選択し、 **[割り当てる]** ボタンを選択します。

1. **[スコープ]** を、ポリシーの割り当てを適用する Kubernetes クラスターの管理グループ、サブスクリプション、またはリソース グループに設定します。

   > [!NOTE]
   > Kubernetes 用の Azure Policy 定義を割り当てるとき、 **[スコープ]** にクラスター リソースを含める必要があります。 AKS エンジン クラスターの場合、 **[スコープ]** はクラスターのリソース グループである必要があります。

1. ポリシーの割り当てに、簡単に識別するために使用できる **[名前]** と **[説明]** を設定します。

1. [[ポリシーの適用]](./assignment-structure.md#enforcement-mode) を以下のいずれかの値に設定します。

   - **[有効]** - クラスターでポリシーを適用します。 違反がある Kubernetes の受付要求は拒否されます。

   - **[無効]** - クラスターでポリシーを適用しません。 違反がある Kubernetes の受付要求は拒否されません。 コンプライアンス評価の結果は引き続き利用できます。 新しいポリシー定義を実行中のクラスターにロールアウトする場合、 _[無効]_ オプションを使用すると、違反のある受付要求が拒否されないため、ポリシー定義のテストに役立ちます。

1. **[次へ]** を選択します。

1. **パラメーターの値**を設定する

   - ポリシーの評価から Kubernetes 名前空間を除外するには、パラメーター **[名前空間の除外]** で名前空間の一覧を指定します。 _kube-system_、_gatekeeper-system_、、_azure-arc_ は除外することをお勧めします。

1. **[Review + create]\(レビュー + 作成\)** を選択します。

あるいは、「[ポリシーの割り当て - ポータル](../assign-policy-portal.md)」クイックスタートを使用して、Kubernetes ポリシーを見つけて割り当てます。 サンプルの 'audit vms' ではなく、Kubernetes ポリシー定義を検索します。

> [!IMPORTANT]
> 組み込みのポリシー定義は、カテゴリ **Kubernetes** の Kubernetes クラスターに使用できます。 組み込みポリシー定義の一覧については、[Kubernetes のサンプル](../samples/built-in-policies.md#kubernetes)に関する記事をご覧ください。

## <a name="policy-evaluation"></a>ポリシーの評価

アドオンは 15 分おきに、ポリシーの割り当ての変更について Azure Policy サービスでチェックインします。
この更新サイクル中に、アドオンによって変更が確認されます。 これらの変更によって、制約テンプレートと制約の作成、更新、または削除がトリガーされます。

Kubernetes クラスターでは、名前空間に次のラベルのいずれかが含まれている場合、違反のある受付要求は拒否されません。 コンプライアンス評価の結果は引き続き利用できます。

- `control-plane`
- `admission.policy.azure.com/ignore`

> [!NOTE]
> クラスター管理者は、Azure Policy アドオンによってインストールされた制約テンプレートと制約リソースを作成および更新するアクセス許可を持っている場合がありますが、手動更新は上書きされるため、これらのシナリオはサポートされていません。 Gatekeeper は、アドオンをインストールして Azure Policy ポリシー定義を割り当てる前に存在していたポリシーの評価を続けます。

アドオンでは、15 分ごとにクラスターのフル スキャンが呼び出されます。 アドオンを使うと、フル スキャンの詳細情報と、クラスターに試行された変更についての Gatekeeper によるすべてのリアルタイム評価が収集された後、すべての Azure Policy 割り当てと同様に、[[ポリシー準拠状況の詳細]](../how-to/get-compliance-data.md) に含めるために、結果が Azure Policy に報告されます。 アクティブなポリシー割り当ての結果のみが監査サイクル中に返されます。 監査結果は、失敗した制約の状態フィールドに[違反](https://github.com/open-policy-agent/gatekeeper#audit)と示されることもあります。

> [!NOTE]
> Kubernetes クラスター用の Azure Policy 内の各コンプライアンス レポートには、過去 45 分間のすべての違反が含まれます。 タイムスタンプは、違反が発生した時刻を示します。

## <a name="logging"></a>ログ記録

_azure-policy_ と _gatekeeper_ のポッドはどちらも、Kubernetes のコントローラーおよびコンテナーとして、Kubernetes クラスター内にログを保持します。 このログは Kubernetes クラスターの **[Insights]** ページに公開されます。
詳細については、「[Azure Monitor for containers を使用して Kubernetes クラスターのパフォーマンスを監視する](../../../azure-monitor/insights/container-insights-analyze.md)」を参照してください。

アドオンのログを表示するには、`kubectl` を使用します。

```bash
# Get the azure-policy pod name installed in kube-system namespace
kubectl logs <azure-policy pod name> -n kube-system

# Get the gatekeeper pod name installed in gatekeeper-system namespace
kubectl logs <gatekeeper pod name> -n gatekeeper-system
```

詳細については、Gatekeeper ドキュメントの [Gatekeeper のデバッグ](https://github.com/open-policy-agent/gatekeeper#debugging)に関する記事を参照してください。

## <a name="remove-the-add-on"></a>アドオンを削除する

### <a name="remove-the-add-on-from-aks"></a>AKS からアドオンを削除する

AKS クラスターから Azure Policy アドオンを削除するには、Azure portal または Azure CLI のいずれかを使用します。

- Azure portal

  1. Azure portal で **[すべてのサービス]** をクリックし、 **[Kubernetes サービス]** を検索して選択することによって AKS サービスを起動します。

  1. Azure Policy アドオンを無効にする AKS クラスターを選択します。

  1. [Kubernetes サービス] ページの左側の **[ポリシー (プレビュー)]** を選択します。

     :::image type="content" source="../media/policy-for-kubernetes/policies-preview-from-aks-cluster.png" alt-text="AKS クラスターからのポリシー定義" border="false":::

  1. メイン ページで、 **[アドオンを無効にする]** ボタンを選択します。

     :::image type="content" source="../media/policy-for-kubernetes/disable-policy-add-on.png" alt-text="AKS 用の Azure Policy アドオンを無効にする" border="false":::

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="remove-the-add-on-from-azure-arc-enabled-kubernetes"></a>Azure Arc 対応 Kubernetes からアドオンを削除する

Azure Policy アドオンと Gatekeeper を Azure Arc 対応 Kubernetes クラスターから削除するには、次の Helm コマンドを実行します。

```bash
helm uninstall azure-policy-addon
```

### <a name="remove-the-add-on-from-aks-engine"></a>AKS エンジンからアドオンを削除する

AKS エンジン クラスターから Azure Policy アドオンと Gatekeeper を削除するには、アドオンのインストール方法に合わせたメソッドを使用します。

- AKS エンジンのクラスター定義で **addons** プロパティを設定してインストールした場合:

  _azure-policy_ の **addons** プロパティを false に変更した後で、クラスター定義を AKS エンジンに再デプロイします。


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

  詳細については、[AKS エンジン - Azure Policy アドオンの無効化](https://github.com/Azure/aks-engine/blob/master/examples/addons/azure-policy/README.md#disable-azure-policy-add-on)に関するページを参照してください。

- Helm Chart を使用してインストールした場合は、次の Helm コマンドを実行します。

  ```bash
  helm uninstall azure-policy-addon
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Azure Policy アドオンによって収集される診断データ

Kubernetes の Azure Policy アドオンを使うと、制限されたクラスター診断データが収集されます。 この診断データは、ソフトウェアとパフォーマンスに関連する重要な技術データです。 これは、次の方法で使用されます。

- Azure Policy アドオンを最新の状態に維持する
- Azure Policy アドオンをセキュリティで保護し、信頼性が高く、パフォーマンスに優れた状態に維持する
- アドオンの使用の集計分析を通じて Azure Policy アドオンを改善する

アドオンによって収集された情報は、個人データではありません。 現在、次の詳細情報が収集されています。

- Azure Policy アドオン エージェントのバージョン
- クラスターの種類
- クラスターのリージョン
- クラスターのリソース グループ
- クラスターのリソース ID
- クラスターのサブスクリプション ID
- クラスターの OS (例:Linux)
- クラスターの市区町村 (例:シアトル)
- クラスターの州または都道府県 (例:ワシントン)
- クラスターの国または地域 (例:米国)
- ポリシー評価でのエージェントのインストール中に Azure Policy アドオンによって発生した例外/エラー
- Azure Policy アドオンによってインストールされていない Gatekeeper ポリシー定義の数

## <a name="next-steps"></a>次のステップ

- [Azure Policy のサンプル](../samples/index.md)を確認します。
- [ポリシー定義の構造](definition-structure.md)を確認します。
- 「[Policy の効果について](effects.md)」を確認します。
- [プログラムによってポリシーを作成する](../how-to/programmatically-create.md)方法を理解します。
- [コンプライアンス データを取得する](../how-to/get-compliance-data.md)方法を学習します。
- [準拠していないリソースを修復する](../how-to/remediate-resources.md)方法を学習します。
- 「[Azure 管理グループのリソースを整理する](../../management-groups/overview.md)」で、管理グループとは何かを確認します。