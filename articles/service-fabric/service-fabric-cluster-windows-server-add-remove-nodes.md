---
title: スタンドアロン Service Fabric クラスターでノードを追加または削除する
description: Windows Server を実行する物理コンピューターまたは仮想マシン上で、Azure Service Fabric クラスターにノードを追加または削除する方法について説明します。追加先または削除元は、オンプレミスでも、任意のクラウドでもかまいません。
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: 26945b4785a0591d997139f2427b0ae6b59fa742
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98790598"
---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Windows Server を実行するスタンドアロン Service Fabric クラスターでノードを追加または削除する
[Windows Server マシンでスタンドアロン Service Fabric クラスターを作成](service-fabric-cluster-creation-for-windows-server.md)した後に、ビジネス ニーズが変更されて、この記事で説明するように、クラスターへのノードの追加や削除が必要になることがあります。

> [!NOTE]
> ノードの追加と削除機能は、ローカル開発クラスターではサポートされていません。

## <a name="add-nodes-to-your-cluster"></a>クラスターへのノードの追加

1. 「[Service Fabric クラスターのデプロイの計画と準備](service-fabric-cluster-standalone-deployment-preparation.md)」で概説されている手順に従って、クラスターに追加する VM またはコンピューターを準備します。

2. この VM またはコンピューターを追加する障害ドメインとアップグレード ドメインを特定します。

   証明書を使用してクラスターをセキュリティで保護する場合は、ノードをクラスターに参加させるための準備として、証明書がローカルの証明書ストアにインストールされることが想定されます。 他の形式のセキュリティを使用する場合は、これと類似した方法が適用されます。

3. クラスターに追加する VM/マシンにリモート デスクトップ (RDP) 接続します。

4. VM またはコンピューターに [Windows Server 用の Service Fabric のスタンドアロン パッケージをダウンロード](https://go.microsoft.com/fwlink/?LinkId=730690)するかコピーして、パッケージを解凍します。

5. 昇格された特権で PowerShell を実行し、解凍されたパッケージの場所に移動します。

6. 追加する新しいノードを記述したパラメーターを指定して、*AddNode.ps1* スクリプトを実行します。 次の例では、名前が VM5、種類が NodeType0、IP アドレスが 182.17.34.52 の新しいノードを UD1 と fd:/dc1/r0 に追加します。 `ExistingClusterConnectionEndPoint` は、既存のクラスターに既にあるノード用の接続エンドポイントです。クラスター内にある "*任意*" のノードの IP アドレスにすることができます。 

   セキュリティ保護なし (プロトタイプ):

   ```
   .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA
   ```

   セキュリティ保護あり (証明書ベース):

   ```  
   $CertThumbprint= "***********************"
    
   .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -X509Credential -ServerCertThumbprint $CertThumbprint  -AcceptEULA

   ```

   スクリプトの実行が完了したら、[Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode) コマンドレットを実行して、新しいノードが追加されたかどうかを確認できます。

7. クラスター内にある複数のノード間で一貫性を保つには、構成のアップグレードを開始する必要があります。 [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration) を実行して最新の構成ファイルを取得し、新しく追加したノードを "Nodes" セクションに追加します。 また、同じ構成のクラスターの再デプロイが必要になった場合に備えて、常に最新のクラスター構成を使用できるようにしておくことをお勧めします。

   ```
    {
        "nodeName": "vm5",
        "iPAddress": "182.17.34.52",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD1"
    }
   ```

8. [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) を実行してアップグレードを開始します。

   ```
   Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
   ```

   アップグレードの進行状況は Service Fabric Explorer で監視できます。 [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade) を実行することもできます。

### <a name="add-nodes-to-clusters-configured-with-windows-security-using-gmsa"></a>Windows セキュリティで gMSA を使用して構成したクラスターにノードを追加する
グループ管理サービス アカウント (gMSA) を使用して構成したクラスターの場合 (https://technet.microsoft.com/library/hh831782.aspx) )、構成のアップグレードを使用して新しいノードを追加できます。
1. 既存のノードのいずれかで [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration) を実行して最新の構成ファイルを取得し、"Nodes" セクションに追加する新しいノードについて詳細を追加します。 新しいノードが同じグループ管理アカウントに属していることを確認します。 このアカウントは、すべてのコンピューターで Administrator である必要があります。

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
2. [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) を実行してアップグレードを開始します。

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
    ```
    アップグレードの進行状況は Service Fabric Explorer で監視できます。 また、[Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade) を実行する方法もあります。

### <a name="add-node-types-to-your-cluster"></a>クラスターにノードの種類を追加する
新しいノードの種類を追加するには、"Properties" 以下の "NodeTypes" セクションに新しいノードの種類を追加し、[Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) を使用して構成のアップグレードを開始します。 アップグレードが完了したら、このノードの種類を使用して新しいノードをクラスターに追加できるようになります。

## <a name="remove-nodes-from-your-cluster"></a>クラスターからのノードの削除
ノードをクラスターから削除するには、次の方法で構成のアップグレードを使用します。

1. [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration) を実行して最新の構成ファイルを取得し、"Nodes" セクションからノードを *"削除"* します。
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
2. [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) を実行してアップグレードを開始します。

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    アップグレードの進行状況は Service Fabric Explorer で監視できます。 [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade) を実行することもできます。

> [!NOTE]
> ノードを削除すると、複数のアップグレードが開始されることがあります。 一部のノードには `IsSeedNode=”true”` タグが付けられており、`Get-ServiceFabricClusterManifest` を使用してクラスター マニフェストをクエリすることで識別できます。 このようなシナリオでノードを削除する場合、シード ノードを削除する必要があるため、通常よりも長く時間がかかる可能性があります。 クラスターはプライマリ ノードの種類のノードを 3 つ以上維持する必要があります。
> 
> 

### <a name="remove-node-types-from-your-cluster"></a>クラスターからのノードの種類の削除
ノードの種類を削除する前に、そのノードの種類を参照しているノードがないかどうかを調べてください。 このようなノードを削除してから、対応するノードの種類を削除します。 すべての対応するノードを削除したら、クラスター構成から NodeType を削除し、[Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) を使用して構成のアップグレードを開始できます。


### <a name="replace-primary-nodes-of-your-cluster"></a>クラスターのプライマリ ノードの置き換え
プライマリ ノードの置き換えは、まとめて削除して後で追加するのではなく、1 ノードづつ実行する必要があります。


## <a name="next-steps"></a>次のステップ
* [スタンドアロン Windows クラスターの構成設定](service-fabric-cluster-manifest.md)
* [X.509 証明書を使用した Windows でのスタンドアロン クラスターの保護](service-fabric-windows-cluster-x509-security.md)
* [Windows を実行する Azure VM を使用してスタンドアロン Service Fabric クラスターを作成する](./service-fabric-cluster-creation-via-arm.md)
