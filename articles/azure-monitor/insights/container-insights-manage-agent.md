---
title: コンテナーに対する Azure Monitor エージェントを管理する方法 | Microsoft Docs
description: この記事では、コンテナーに対する Azure Monitor によって使用されるコンテナー化された Log Analytics エージェントで、最も一般的なメンテナンス タスクを管理する方法について説明します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/06/2018
ms.author: magoedte
ms.openlocfilehash: c8a5c839d6d662f9d330099f89c97eb83c4fe516
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2019
ms.locfileid: "58577086"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>コンテナーに対する Azure Monitor エージェントを管理する方法
コンテナーに対する Azure Monitor では、コンテナー化されたバージョンの Linux 用 Log Analytics エージェント が使用されます。 初期のデプロイ後は、ライフ サイクル中に実行する必要のあるルーチンまたは省略可能なタスクが存在します。 この記事では、エージェントを手動でアップグレードし、特定のコンテナーから環境変数のコレクションを無効にする方法について詳しく説明します。 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>コンテナーに対する Azure Monitor エージェントをアップグレードする方法
コンテナーに対する Azure Monitor では、コンテナー化されたバージョンの Linux 用 Log Analytics エージェント が使用されます。 エージェントの新しいバージョンがリリースされると、Azure Kubernetes Service (AKS) でホストされているマネージド Kubernetes クラスター上のエージェントが自動的にアップグレードされます。  

エージェントのアップグレードが失敗した場合のために、この記事では、エージェントを手動でアップグレードするプロセスについて説明します。 リリースされたバージョンを確認するには、[エージェントのリリースのお知らせ](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)を参照してください。   

### <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>監視対象の Kubernetes クラスター上のエージェントのアップグレード
エージェントをアップグレードするプロセスは、2 つの単純な手順で構成されます。 最初の手順は、コンテナーに対する Azure Monitor の監視を Azure CLI を使用して無効にすることです。  [監視の無効化](container-insights-optout.md?#azure-cli)に関する記事の手順に従ってください。 Azure CLI を使用して、ソリューションとワークスペースに格納されている対応するデータに影響を与えることなく、クラスター内のノードからエージェントを削除できます。 

>[!NOTE]
>このメンテナンス アクティビティを実行している間、クラスター内のノードによる収集されたデータの転送は行われず、エージェントを削除して新しいバージョンをインストールするまでの間、パフォーマンス ビューにデータは表示されません。 
>

新しいバージョンのエージェントをインストールするには、Azure CLI を使用する[監視の利用開始](container-insights-onboard.md?#enable-using-azure-cli)に関する記事の手順に従って、このプロセスを完了します。  

監視を再び有効にした後、クラスターの更新された正常性メトリックが表示されるまで、約 15 分かかる場合があります。 エージェントが正常にアップグレードされたことを確認するには、`kubectl logs omsagent-484hw --namespace=kube-system` コマンドを実行します

ステータスは次の例のようになります。ここで、*omi* と *omsagent* の値は、[エージェントのリリース履歴](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)で指定された最新バージョンと一致する必要があります。  

    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.5
    omsagent 1.6.0-163
    docker-cimprov 1.0.0.31

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>コンテナーの環境変数コレクションを無効にする方法
コンテナーに対する Azure Monitor は、ポッドで実行されているコンテナーから環境変数を収集し、**[コンテナー]** ビューで選択したコンテナーのプロパティ ウィンドウに表示します。 この動作を制御するには、AKS クラスターのデプロイ中、またはデプロイ後に環境変数 *AZMON_COLLECT_ENV* を設定して、特定のコンテナーのコレクションを無効にします。 この機能は、エージェント バージョン ciprod11292018 以降で使用できます。  

新規または既存のコンテナーの環境変数のコレクションを無効にするには、Kubernetes デプロイ yaml 構成ファイルで変数 **AZMON_COLLECT_ENV** を値 **False** に設定します。   

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

AKS コンテナーに変更を適用するには、次のコマンドを実行します: `kubectl apply -f  <path to yaml file>`。

構成の変更が有効になったことを確認するには、コンテナーに対する Azure Monitor の **[コンテナー]** ビューでコンテナーを選択し、プロパティ ウィンドウで **[環境変数]** を展開します。  このセクションには、先ほど作成した変数 - **AZMON_COLLECT_ENV = FALSE** のみが表示されます。 その他のすべてのコンテナーでは、[環境変数] セクションには、検出されたすべての環境変数がリストされます。   

環境変数の検出を再度有効にするには、先ほどと同じプロセスを適用し、値を **False** から **True** に変更して、`kubectl` コマンドを再実行してコンテナーを更新します。  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>次の手順
エージェントのアップグレード中に問題が発生した場合は、[トラブルシューティング ガイド](container-insights-troubleshoot.md)を参照してください。