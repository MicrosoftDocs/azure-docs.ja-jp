---
title: 新しい Azure Kubernetes Service (AKS) クラスターを監視する | Microsoft Docs
description: Container insights のサブスクリプションによって、新しい Azure Kubernetes Service (AKS) クラスターの監視を有効にする方法について説明します。
ms.topic: conceptual
ms.date: 04/25/2019
ms.custom: devx-track-terraform, devx-track-azurecli
ms.openlocfilehash: 9b6c4f8a05b8e7a350ebd5afd677e8bb2ee6e9b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101717572"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>新しい Azure Kubernetes Service (AKS) クラスターの監視を有効にする

この記事では、サブスクリプションへのデプロイを準備している、[Azure Kubernetes Service](../../aks/index.yml) でホストされているマネージド Kubernetes クラスターを監視するように Container insights を設定する方法について説明します。

サポートされている次のいずれかの方法を使用して、AKS クラスターの監視を有効にすることができます。

* Azure CLI
* Terraform

## <a name="enable-using-azure-cli"></a>Azure CLI を使用して有効にする

Azure CLI で作成した新しい AKS クラスターの監視を有効にするには、クイック スタート記事の「[AKS クラスターの作成](../../aks/kubernetes-walkthrough.md#create-aks-cluster)」セクションの手順に従ってください。  

>[!NOTE]
>Azure CLI を使用する場合は、まず、ローカルに CLI をインストールして使用する必要があります。 Azure CLI バージョン 2.0.74 以降を実行している必要があります。 ご利用のバージョンを識別するには、`az --version` を実行します。 Azure CLI をインストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 aks-preview CLI 拡張機能バージョン 0.4.12 以降をインストールしている場合は、プレビュー拡張機能を有効にするために行ったすべての変更を削除します。これは、AKS プレビュー機能が Azure US Government クラウドでは利用できないので、既定の Azure CLI 動作をオーバーライドできるためです。

## <a name="enable-using-terraform"></a>Terraform を使用して有効にする

[Terraform を使用して新しい AKS クラスターをデプロイする場合](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks)、既存のものを指定することを選択しない場合には、プロファイルに必要な引数を指定して [Log Analytics ワークスペースを作成します](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html)。 

>[!NOTE]
>Terraform を使用することを選択する場合、Terraform Azure RM プロバイダー バージョン 1.17.0 以降が実行されている必要があります。

Container insights をワークスペースに追加するには、[azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) を参照し、[**addon_profile**](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) を含め、**oms_agent** を指定してプロファイルを完成させます。 

監視を有効にし、すべての構成タスクが正常に完了すると、次の 2 つの方法のいずれかを使用して、クラスターのパフォーマンスを監視することができます。

* 左側のウィンドウで **[正常性]** を選択し、AKS クラスターで直接監視します。
* 選択したクラスターの AKS クラスター ページで、 **[Monitor Container insights]\(コンテナーの分析情報の監視\)** タイルを選択します。 Azure Monitor の左側のウィンドウにある **[正常性]** を選択します。 

  ![AKS で Container insights を選択するためのオプション](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

監視を有効にした後、クラスターの正常性メトリックが表示されるまで、約 15 分かかる場合があります。 

## <a name="verify-agent-and-solution-deployment"></a>エージェントとソリューションのデプロイを確認する
バージョン *06072018* 以降のエージェントでは、エージェントとソリューションの両方が正常にデプロイされていることを確認することができます。 これより前のバージョンのエージェントでは、エージェントのデプロイしか確認できません。

### <a name="agent-version-06072018-or-later"></a>エージェント バージョン 06072018 以降
エージェントが正常にデプロイされていることを確認するには、次のコマンドを実行します。 

```
kubectl get ds omsagent --namespace=kube-system
```

出力は次のようになり、適切にデプロイされたことが示されます。

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

ソリューションのデプロイを確認するには、次のコマンドを実行します。

```
kubectl get deployment omsagent-rs -n=kube-system
```

出力は次のようになり、適切にデプロイされたことが示されます。

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>06072018 より前のバージョンのエージェント

*06072018* より前にリリースされたバージョンの Log Analytics エージェントが適切にデプロイされていることを確認するには、次のコマンドを実行します。  

```
kubectl get ds omsagent --namespace=kube-system
```

出力は次のようになり、適切にデプロイされたことが示されます。  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>CLI で構成を表示する
ソリューションが有効かどうか、Log Analytics ワークスペースのリソース ID、クラスターに関するサマリー詳細などの詳細を取得するには、`aks show` コマンドを使用します。  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

数分してコマンドが完了すると、ソリューションに関する情報が JSON 形式で表示されます。  コマンドの結果では監視アドオン プロファイルが表示され、次の出力例のようになります。

```
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="next-steps"></a>次のステップ

* ソリューションのオンボードを試みた際に問題が発生した場合は、[トラブルシューティング ガイド](container-insights-troubleshoot.md)を確認してください。

* AKS クラスターと実行中のワークロードの正常性とリソース使用率を収集するための監視を有効にしたうえで、Container insights を[使用する方法](container-insights-analyze.md)について学習します。

