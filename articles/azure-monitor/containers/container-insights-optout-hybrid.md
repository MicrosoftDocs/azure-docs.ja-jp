---
title: お使いのハイブリッド Kubernetes クラスターの監視を停止する方法 | Microsoft Docs
description: この記事では、Container insights でお使いのハイブリッド Kubernetes クラスターの監視を停止する方法について説明します。
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: e8708d6b860683cc96a806160ccc7c8e33949ab2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101713696"
---
# <a name="how-to-stop-monitoring-your-hybrid-cluster"></a>お使いのハイブリッド クラスターの監視を停止する方法

Kubernetes クラスターの監視を有効にした後に、その監視が必要なくなったと判断した場合は、Container insights でのクラスターの監視を停止できます。 この記事では、これを次の環境で実現する方法について説明します。

- Azure および Azure Stack 上の AKS エンジン
- OpenShift バージョン 4 以上
- Azure Arc 対応 Kubernetes (プレビュー)

## <a name="how-to-stop-monitoring-using-helm"></a>Helm を使用して停止する方法

次の手順は、次の環境に適用されます。

- Azure および Azure Stack 上の AKS エンジン
- OpenShift バージョン 4 以上

1. まず、helm の次のコマンドを実行し、お使いのクラスターに Container insights の helm チャートがインストールされていることを確認します。

    ```
    helm list
    ```

    出力は次のようになります。

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon-containers-release-1* は、Container insights の helm チャートのリリースです。

2. 次の helm コマンドを実行して、このチャート リリースを削除します。

    `helm delete <releaseName>`

    例:

    `helm delete azmon-containers-release-1`

    これにより、クラスターからこのリリースが削除されます。 これは、`helm list` コマンドを実行して確認できます。

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

設定の変更が完了するまで数分かかります。 Helm では、お使いのリリースの削除後もそれを追跡するので、クラスターの履歴を監査し、`helm rollback` を使用して、リリースの削除を取り消すこともできます。

## <a name="how-to-stop-monitoring-on-arc-enabled-kubernetes"></a>Arc 対応 Kubernetes 上で監視を停止する方法

### <a name="using-powershell"></a>PowerShell の使用

1. 次のコマンドを使用し、監視アドオンを使用してクラスターを構成するスクリプトをローカル フォルダーにダウンロードし、保存します。

    ```powershell
    wget https://aka.ms/disable-monitoring-powershell-script -OutFile disable-monitoring.ps1
    ```

2. Azure Arc 対応 Kubernetes クラスター リソースのリソース ID を表す `subscriptionId`、`resourceGroupName`、`clusterName` の対応する値を設定することによって、`$azureArcClusterResourceId` 変数を構成します。

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. コマンド `kubectl config get-contexts` を実行することによって、クラスターの **kube-context** で `$kubeContext` 変数を構成します。 現在のコンテキストを使用する場合は、この値を `""` に設定します。

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. 次のコマンドを実行して、クラスターの監視を停止します。

    ```powershell
    .\disable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext
    ```

#### <a name="using-service-principal"></a>サービス プリンシパルを使用する
スクリプト *disable-monitoring.ps1* では、対話型デバイス ログインが使用されます。 非対話型のログインを望む場合、既存のサービス プリンシパルを使用するか、「[前提条件](container-insights-enable-arc-enabled-clusters.md#prerequisites)」の説明にある必須のアクセス許可が与えられたサービス プリンシパルを新規作成できます。 サービス プリンシパルを使用するには、$servicePrincipalClientId、$servicePrincipalClientSecret、$tenantId パラメーターを、enable-monitoring.ps1 スクリプトに使用するサービス プリンシパルの値と共に渡す必要があります。

```powershell
$subscriptionId = "<subscription Id of the Azure Arc connected cluster resource>"
$servicePrincipal = New-AzADServicePrincipal -Role Contributor -Scope "/subscriptions/$subscriptionId"

$servicePrincipalClientId =  $servicePrincipal.ApplicationId.ToString()
$servicePrincipalClientSecret = [System.Net.NetworkCredential]::new("", $servicePrincipal.Secret).Password
$tenantId = (Get-AzSubscription -SubscriptionId $subscriptionId).TenantId
```

次に例を示します。

```powershell
\disable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext -servicePrincipalClientId $servicePrincipalClientId -servicePrincipalClientSecret $servicePrincipalClientSecret -tenantId $tenantId
```


### <a name="using-bash"></a>Bash の使用

1. 次のコマンドを使用し、監視アドオンを使用してクラスターを構成するスクリプトをローカル フォルダーにダウンロードし、保存します。

    ```bash
    curl -o disable-monitoring.sh -L https://aka.ms/disable-monitoring-bash-script
    ```

2. Azure Arc 対応 Kubernetes クラスター リソースのリソース ID を表す `subscriptionId`、`resourceGroupName`、`clusterName` の対応する値を設定することによって、`azureArcClusterResourceId` 変数を構成します。

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. コマンド `kubectl config get-contexts` を実行することによって、クラスターの **kube-context** で `kubeContext` 変数を構成します。

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. クラスターの監視を停止するには、デプロイ シナリオに応じてさまざまなコマンドが用意されています。

    現在のコンテキストを使用してクラスターの監視を停止するには、次のコマンドを実行します。

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    コンテキストを指定してクラスターの監視を停止するには、次のコマンドを実行します。

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

#### <a name="using-service-principal"></a>サービス プリンシパルを使用する
Bash スクリプト *disable-monitoring.sh* では、対話型デバイス ログインが使用されます。 非対話型のログインを望む場合、既存のサービス プリンシパルを使用するか、「[前提条件](container-insights-enable-arc-enabled-clusters.md#prerequisites)」の説明にある必須のアクセス許可が与えられたサービス プリンシパルを新規作成できます。 サービス プリンシパルを使用するには、*enable-monitoring.sh* Bash スクリプトに使用するサービス プリンシパルの --client-id、--client-secret、--tenant-id 値を渡す必要があります。

```bash
subscriptionId="<subscription Id of the Azure Arc connected cluster resource>"
servicePrincipal=$(az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${subscriptionId}")
servicePrincipalClientId=$(echo $servicePrincipal | jq -r '.appId')

servicePrincipalClientSecret=$(echo $servicePrincipal | jq -r '.password')
tenantId=$(echo $servicePrincipal | jq -r '.tenant')
```

次に例を示します。

```bash
bash disable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext --client-id $servicePrincipalClientId --client-secret $servicePrincipalClientSecret  --tenant-id $tenantId
```

## <a name="next-steps"></a>次のステップ

クラスターを監視するためだけに作成した Log Analytics ワークスペースが不要になった場合は、それを手動で削除する必要があります。 ワークスペースを削除する方法の詳細については、[Azure Log Analytics ワークスペースの削除](../logs/delete-workspace.md)に関するページを参照してください。