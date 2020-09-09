---
title: お使いのハイブリッド Kubernetes クラスターの監視を停止する方法 | Microsoft Docs
description: この記事では、Azure Monitor for containers でお使いのハイブリッド Kubernetes クラスターの監視を停止する方法について説明します。
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 8369c82b83cfbaa7128383c6203aaf584916cae9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091200"
---
# <a name="how-to-stop-monitoring-your-hybrid-cluster"></a>お使いのハイブリッド クラスターの監視を停止する方法

Kubernetes クラスターの監視を有効にした後に、その監視が必要なくなったと判断した場合は、Azure Monitor for containers でのクラスターの監視を停止できます。 この記事では、これを次の環境で実現する方法について説明します。

- Azure および Azure Stack 上の AKS エンジン
- OpenShift バージョン 4 以上
- Azure Arc 対応 Kubernetes (プレビュー)

## <a name="how-to-stop-monitoring-using-helm"></a>Helm を使用して停止する方法

次の手順は、次の環境に適用されます。

- Azure および Azure Stack 上の AKS エンジン
- OpenShift バージョン 4 以上

1. まず、helm の次のコマンドを実行し、お使いのクラスターに Azure Monitor for containers の helm チャートがインストールされていることを確認します。

    ```
    helm list
    ```

    出力は次のようになります。

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon-containers-release-1* は、Azure Monitor for containers の helm チャートのリリースです。

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

## <a name="next-steps"></a>次のステップ

クラスターを監視するためだけに作成した Log Analytics ワークスペースが不要になった場合は、それを手動で削除する必要があります。 ワークスペースを削除する方法の詳細については、[Azure Log Analytics ワークスペースの削除](../platform/delete-workspace.md)に関するページを参照してください。
