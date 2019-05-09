---
title: スタンドアロン Service Fabric クラスターでノードを追加または削除する | Microsoft Docs
description: Windows Server を実行する物理コンピューターまたは仮想マシン上で、Azure Service Fabric クラスターにノードを追加または削除する方法について説明します。追加先または削除元は、オンプレミスでも、任意のクラウドでもかまいません。
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: bc6b8fc0-d2af-42f8-a164-58538be38d02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: c9e771d97c037ddd07834025063233c3ede1f0c2
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58668494"
---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Windows Server を実行するスタンドアロン Service Fabric クラスターでノードを追加または削除する
[Windows Server マシンでスタンドアロン Service Fabric クラスターを作成](service-fabric-cluster-creation-for-windows-server.md)した後に、ビジネス ニーズが変更されて、クラスターへのノードの追加や削除が必要になることがあります。 この記事では、これを実行する詳細の手順について説明します。 ノード機能の追加/削除は、ローカル デプロイ クラスターではサポートされていない点に注意してください。

## <a name="add-nodes-to-your-cluster"></a>クラスターへのノードの追加

1. [Service Fabric クラスターのデプロイの計画と準備](service-fabric-cluster-creation-for-windows-server.md)に関するページで説明されている手順に従って、クラスターに追加する VM/マシンを準備します。
2. この VM/コンピューターを追加する障害ドメインとアップグレード ドメインを特定します。
3. クラスターに追加する VM/コンピューターにリモート デスクトップ (RDP) 接続します。
4. この VM/コンピューターに [Windows Server 用の Service Fabric のスタンドアロン パッケージをダウンロード](https://go.microsoft.com/fwlink/?LinkId=730690)するかコピーし、パッケージを展開します。
5. 昇格した特権で Powershell を実行し、展開したパッケージのある場所に移動します。
6. 追加する新しいノードを記述したパラメーターを指定して、*AddNode.ps1* スクリプトを実行します。 次の例では、名前が VM5、タイプが NodeType0、IP アドレスが 182.17.34.52 の新しいノードを UD1 と fd:/dc1/r0 に追加します。 *ExistingClusterConnectionEndPoint* は、既存のクラスターに既にあるノードの接続エンドポイントです。クラスター内にある *"任意"* のノードの IP アドレスを使用できます。

    ```
    .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA
    ```
    スクリプトの実行が完了したら、[Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) コマンドレットを実行して新しいノードが追加されたことを確認できます。

7. クラスター内にある複数のノード間で一貫性を保つには、構成のアップグレードを開始する必要があります。 [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) を実行して最新の構成ファイルを取得し、新しく追加したノードを "Nodes" セクションに追加します。 また、同じ構成のクラスターを再デプロイする必要が出てきた場合に備えて、使用できる最新のクラスター構成を常に取得しておくことをお勧めします。

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
8. [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) を実行してアップグレードを開始します。

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    アップグレードの進行状況は Service Fabric Explorer で監視できます。 また、[Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps) を実行する方法もあります。

### <a name="add-nodes-to-clusters-configured-with-windows-security-using-gmsa"></a>Windows セキュリティで gMSA を使用して構成したクラスターにノードを追加する
グループ管理サービス アカウント (gMSA) を使用して構成したクラスターの場合 (https://technet.microsoft.com/library/hh831782.aspx))、構成のアップグレードを使用して新しいノードを追加できます。
1. 既存のノードのいずれかで [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) を実行して最新の構成ファイルを取得し、"Nodes" セクションに追加する新しいノードについて詳細を追加します。 新しいノードが同じグループ管理アカウントに属していることを確認します。 このアカウントは、すべてのコンピューターで Administrator である必要があります。

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
2. [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) を実行してアップグレードを開始します。

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
    ```
    アップグレードの進行状況は Service Fabric Explorer で監視できます。 また、[Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps) を実行する方法もあります。

### <a name="add-node-types-to-your-cluster"></a>クラスターにノードの種類を追加する
新しいノードの種類を追加するには、"Properties" 以下の "NodeTypes" セクションに新しいノードの種類を追加し、[Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) を使用して構成のアップグレードを開始します。 アップグレードが完了したら、このノードの種類を使用して新しいノードをクラスターに追加できるようになります。

## <a name="remove-nodes-from-your-cluster"></a>クラスターからのノードの削除
ノードをクラスターから削除するには、次の方法で構成のアップグレードを使用します。

1. [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) を実行して最新の構成ファイルを取得し、"Nodes" セクションからノードを *"削除"* します。
"FabricSettings" セクション内の "Setup" セクションに "NodesToBeRemoved" パラメーターを追加します。 "value" は、削除する必要があるノードのノード名のコンマ区切りの一覧です。

    ```
         "fabricSettings": [
            {
            "name": "Setup",
            "parameters": [
                {
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
                },
                {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
                },
                {
                "name": "NodesToBeRemoved",
                "value": "vm0, vm1"
                }
            ]
            }
        ]
    ```
2. [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) を実行してアップグレードを開始します。

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    アップグレードの進行状況は Service Fabric Explorer で監視できます。 また、[Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps) を実行する方法もあります。

> [!NOTE]
> ノードを削除すると、複数のアップグレードが開始されることがあります。 一部のノードには `IsSeedNode=”true”` タグが付けられており、`Get-ServiceFabricClusterManifest` を使用してクラスター マニフェストをクエリすることで識別できます。 このようなシナリオでノードを削除する場合、シード ノードを削除する必要があるため、通常よりも長く時間がかかる可能性があります。 クラスターはプライマリ ノードの種類のノードを 3 つ以上維持する必要があります。
> 
> 

### <a name="remove-node-types-from-your-cluster"></a>クラスターからのノードの種類の削除
ノードの種類を削除する前に、そのノードの種類を参照しているノードがないことを再確認してください。 このようなノードを削除してから、対応するノードの種類を削除します。 すべての対応するノードを削除したら、クラスター構成から NodeType を削除し、[Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) を使用して構成のアップグレードを開始できます。


### <a name="replace-primary-nodes-of-your-cluster"></a>クラスターのプライマリ ノードの置き換え
プライマリ ノードの置き換えは、まとめて削除して後で追加するのではなく、1 ノードづつ実行する必要があります。


## <a name="next-steps"></a>次の手順
* [スタンドアロン Windows クラスターの構成設定](service-fabric-cluster-manifest.md)
* [X.509 証明書を使用した Windows でのスタンドアロン クラスターの保護](service-fabric-windows-cluster-x509-security.md)
* [Windows を実行する Azure VM を使用してスタンドアロン Service Fabric クラスターを作成する](service-fabric-cluster-creation-with-windows-azure-vms.md)

