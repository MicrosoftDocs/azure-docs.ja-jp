---
title: Container insights エージェントを管理する方法 | Microsoft Docs
description: この記事では、Container insights によって使用されるコンテナー化された Log Analytics エージェントで、最も一般的なメンテナンス タスクを管理する方法について説明します。
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: 2a0c32ef797a953eca794e16fe0ace5e967f339f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101713798"
---
# <a name="how-to-manage-the-container-insights-agent"></a>Container insights エージェントを管理する方法

Container insights では、コンテナー化されたバージョンの Linux 用 Log Analytics エージェントが使用されます。 初期のデプロイ後は、ライフ サイクル中に実行する必要のあるルーチンまたは省略可能なタスクが存在します。 この記事では、エージェントを手動でアップグレードし、特定のコンテナーから環境変数のコレクションを無効にする方法について詳しく説明します。 

## <a name="how-to-upgrade-the-container-insights-agent"></a>Container insights エージェントをアップグレードする方法

Container insights では、コンテナー化されたバージョンの Linux 用 Log Analytics エージェントが使用されます。 エージェントの新しいバージョンがリリースされると、Azure Kubernetes Service (AKS) および Azure Red Hat OpenShift バージョン 3.x でホストされているマネージド Kubernetes クラスター上のエージェントが自動的にアップグレードされます。 [hybrid Kubernetes クラスター](container-insights-hybrid-setup.md)と Azure Red Hat OpenShift バージョン 4.x のエージェントは管理されていないため、エージェントを手動でアップグレードする必要があります。

AKS または Azure Red Hat OpenShift バージョン 3.x でホストされているクラスターのエージェントのアップグレードに失敗している場合、この記事では、エージェントを手動でアップグレードするプロセスについても説明します。 リリースされたバージョンを確認するには、[エージェントのリリースのお知らせ](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)を参照してください。

### <a name="upgrade-agent-on-aks-cluster"></a>AKS クラスター上のエージェントのアップグレード

AKS クラスター上のエージェントをアップグレードするプロセスは、2 つの単純な手順で構成されます。 最初の手順は、Azure CLI を使用して Container insights の監視を無効にすることです。 [監視の無効化](container-insights-optout.md?#azure-cli)に関する記事の手順に従ってください。 Azure CLI を使用して、ソリューションとワークスペースに格納されている対応するデータに影響を与えることなく、クラスター内のノードからエージェントを削除できます。 

>[!NOTE]
>このメンテナンス アクティビティを実行している間、クラスター内のノードによる収集されたデータの転送は行われず、エージェントを削除して新しいバージョンをインストールするまでの間、パフォーマンス ビューにデータは表示されません。 
>

新しいバージョンのエージェントをインストールするには、[Azure CLI を使用して監視を有効にする](container-insights-enable-new-cluster.md#enable-using-azure-cli)に関する記事の手順に従って、このプロセスを完了します。  

監視を再び有効にした後、クラスターの更新された正常性メトリックが表示されるまで、約 15 分かかる場合があります。 エージェントが正常にアップグレードされたことを確認するには、次のいずれかを実行します。

* コマンド `kubectl get pod <omsagent-pod-name> -n kube-system -o=jsonpath='{.spec.containers[0].image}'` を実行します。 返された状態の、出力の *Containers* セクションにある omsagent の **Image** の下の値をメモします。
* **[ノード]** タブでクラスター ノードを選択し、右側の **[プロパティ]** ペインにある **[エージェント イメージ タグ]** の下の値をメモします。

表示されるエージェントのバージョンは、[[Release history]\(リリース履歴\)](https://github.com/microsoft/docker-provider/tree/ci_feature_prod) ページに表示されている最新バージョンと一致している必要があります。

### <a name="upgrade-agent-on-hybrid-kubernetes-cluster"></a>ハイブリッド Kubernetes クラスター上でエージェントをアップグレードする

次の手順を実行して、以下の場所で実行されている Kubernetes クラスター上のエージェントをアップグレードします。

* AKS エンジンを使用して、Azure でホストされた自己管理の Kubernetes クラスター。
* AKS エンジンを使用して、Azure Stack またはオンプレミスでホストされた自己管理の Kubernetes クラスター。
* Red Hat OpenShift バージョン 4.x。

Log Analytics ワークスペースが商用 Azure にある場合には、次のコマンドを実行します。

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
```

Log Analytics ワークスペースが Azure China 21Vianet にある場合には、次のコマンドを実行します。

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

Log Analytics ワークスペースが Azure US Government にある場合には、次のコマンドを実行します。

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

### <a name="upgrade-agent-on-azure-red-hat-openshift-v4"></a>Azure Red Hat OpenShift v4 上のエージェントのアップグレード

次の手順を実行して、Azure Red Hat OpenShift バージョン 4.x で実行されている Kubernetes クラスター上でエージェントをアップグレードします。 

>[!NOTE]
>Azure Red Hat OpenShift バージョン 4.x は、Azure 商用クラウドでの実行のみをサポートしています。
>

```console
curl -o upgrade-monitoring.sh -L https://aka.ms/upgrade-monitoring-bash-script
export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
bash upgrade-monitoring.sh --resource-id $ azureAroV4ClusterResourceId
```

このコマンドでサービス プリンシパルを使用する方法の詳細については、「[Azure Arc 対応 Kubernetes クラスターの監視を有効にする](container-insights-enable-arc-enabled-clusters.md#enable-using-bash-script)」の **サービス プリンシパルの使用** に関する箇所を参照してください。

### <a name="upgrade-agent-on-azure-arc-enabled-kubernetes"></a>Azure Arc 対応 Kubernetes 上のエージェントのアップグレード

次のコマンドを実行して、Azure Arc 対応 Kubernetes クラスター上のエージェントをアップグレードします。

```console
curl -o upgrade-monitoring.sh -L https://aka.ms/upgrade-monitoring-bash-script
export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
bash upgrade-monitoring.sh --resource-id $azureArcClusterResourceId
```

このコマンドでサービス プリンシパルを使用する方法の詳細については、「[Azure Arc 対応 Kubernetes クラスターの監視を有効にする](container-insights-enable-arc-enabled-clusters.md#enable-using-bash-script)」の **サービス プリンシパルの使用** に関する箇所を参照してください。


## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>コンテナーの環境変数コレクションを無効にする方法

Container insights は、ポッドで実行されているコンテナーから環境変数を収集し、 **[コンテナー]** ビューで選択したコンテナーのプロパティ ペインに表示します。 この動作を制御するには、Kubernetes クラスターのデプロイ中、またはデプロイ後に環境変数 *AZMON_COLLECT_ENV* を設定して、特定のコンテナーのコレクションを無効にします。 この機能は、エージェント バージョン ciprod11292018 以降で使用できます。  

新規または既存のコンテナーの環境変数のコレクションを無効にするには、Kubernetes デプロイ yaml 構成ファイルで変数 **AZMON_COLLECT_ENV** を値 **False** に設定します。 

```yaml
- name: AZMON_COLLECT_ENV  
  value: "False"  
```

`kubectl apply -f  <path to yaml file>` コマンドを実行して、Azure Red Hat OpenShift 以外の Kubernetes クラスターに変更を適用します。 ConfigMap を編集し、この変更を Azure Red Hat OpenShift クラスターに適用するには、次のコマンドを実行します。

```bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

これにより、既定のテキスト エディターが開きます。 変数を設定したら、ファイルをエディターに保存します。

構成の変更が有効になったことを確認するには、Container insights の **[コンテナー]** ビューでコンテナーを選択し、プロパティ パネルで **[環境変数]** を展開します。  このセクションには、先ほど作成した変数 - **AZMON_COLLECT_ENV = FALSE** のみが表示されます。 その他のすべてのコンテナーでは、[環境変数] セクションには、検出されたすべての環境変数がリストされます。

環境変数の検出を再度有効にするには、先ほどと同じプロセスを適用し、値を **False** から **True** に変更して、`kubectl` コマンドを再実行してコンテナーを更新します。  

```yaml
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>次のステップ

エージェントのアップグレード中に問題が発生した場合は、[トラブルシューティング ガイド](container-insights-troubleshoot.md)を参照してください。
