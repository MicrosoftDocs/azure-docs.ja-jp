---
title: ノード タイプと仮想マシン スケール セット
description: Azure Service Fabric のノードの種類を仮想マシン スケール セットに関連付ける方法のほか、スケール セット インスタンスまたはクラスター ノードにリモートで接続する方法について説明します。
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: pepogors
ms.openlocfilehash: 9e30c02de54806006a1881448bcb9f788a57310c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97095255"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Azure Service Fabric のノードの種類と仮想マシン スケール セット

[仮想マシン スケール セット](../virtual-machine-scale-sets/index.yml)は、Azure コンピューティング リソースです。 スケール セットを使用すると、仮想マシンのコレクションをセットとしてデプロイおよび管理できます。 Azure Service Fabric クラスターで定義するノードの種類ごとに、正確に 1 つのスケール セットが設定されます。複数のノードの種類は同じスケール セットでサポートできず、1 つのノードの種類は複数のスケール セットでサポートできません。

Service Fabric ランタイムは、*Microsoft.Azure.ServiceFabric* 仮想マシン拡張機能によって、スケール セット内の各仮想マシンにインストールされます。 各ノードの種類を個別にスケールアップまたはスケールダウンしたり、各クラスター ノードで実行されている OS SKU を変更したり、異なるポートのセットを開いたり、別の容量メトリックを使用したりできます。

次の図は、*FrontEnd* と *BackEnd* という名前の 2 種類のノードがあるクラスターを示しています。 各ノードの種類には、5 つのノードがあります。

![2 種類のノードがあるクラスター][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>仮想マシン スケール セットのインスタンスをノードにマップする

前の図に示されているように、スケール セットのインスタンスはインスタンス 0 で始まり、1 つずつ増えていきます。 番号はノード名に反映されます。 たとえば、ノード BackEnd_0 は、BackEnd スケール セットのインスタンス 0 です。 この特定のスケール セットには、BackEnd_0、BackEnd_1、BackEnd_2、BackEnd_3、BackEnd_4 という名前の 5 つのインスタンスがあります。

スケール セットをスケールアウトすると、新しいインスタンスが作成されます。 通常、新しいスケール セットのインスタンス名は、スケール セットの名前に次のインスタンス番号を付加したものになります。 この例では、BackEnd_5 です。

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>スケール セットのロード バランサーをノードの種類とスケール セットにマップする

Azure Portal でクラスターをデプロイした場合、またはサンプル Azure Resource Manager テンプレートを使用した場合、リソース グループのすべてのリソースが一覧に表示されます。 また、各スケール セットまたはノードの種類のロード バランサーを確認できます。 ロード バランサー名には、次の形式が使用されます: **LB-&lt;ノードの種類の名前&gt;** 。 たとえば、次の図で示すように、LB-sfcluster4doc-0 などです。

![2 つのロード バランサーが強調表示されているリソース グループを表示するスクリーンショット。][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>Service Fabric 仮想マシン拡張機能

Service Fabric 仮想マシン拡張機能は、Service Fabric を　Azure 仮想マシンにブートス トラップし、ノードのセキュリティを構成します。

以下は、Service Fabric 仮想マシン拡張機能のスニペットです。

```json
"extensions": [
  {
    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
    "properties": {
      "type": "ServiceFabricLinuxNode",
      "autoUpgradeMinorVersion": true,
      "protectedSettings": {
        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
       },
       "publisher": "Microsoft.Azure.ServiceFabric",
       "settings": {
         "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
         "nodeTypeRef": "[variables('vmNodeType0Name')]",
         "durabilityLevel": "Silver",
         "enableParallelJobs": true,
         "nicPrefixOverride": "[variables('subnet0Prefix')]",
         "dataPath": "D:\\\\SvcFab",
         "certificate": {
           "commonNames": [
             "[parameters('certificateCommonName')]"
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
         }
       },
       "typeHandlerVersion": "1.1"
     }
   },
```

プロパティの説明を次に示します。

| **名前** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
| name | string | 拡張機能の一意の名前 |
| type | "ServiceFabricLinuxNode" または "ServiceFabricWindowsNode" | OS の Service Fabric が次に対してブートストラップすることを識別します。 |
| autoUpgradeMinorVersion | true または false | SF ランタイムのマイナー バージョンの自動アップグレードを有効にします。 |
| publisher | Microsoft.Azure.ServiceFabric | Service Fabric 拡張機能パブリッシャーの名前 |
| clusterEndpont | string | 管理エンドポイントへに対する URI:PORT |
| nodeTypeRef | string | nodeType の名前 |
| durabilityLevel | bronze、silver、gold、platinum | 不変 Azure インフラストラクチャを一時停止できる時間 |
| enableParallelJobs | true または false | 同じスケール セットにおいて並列で VM を削除し VM を再起動するように ParallelJobs の計算を有効にします。 |
| nicPrefixOverride | string | "10.0.0.0/24" などサブネットのプレフィックス |
| commonNames | string[] | インストール済みのクラスター証明書の共通名 |
| x509StoreName | string | インストールされているクラスター証明書が配置されているストアの名前 |
| typeHandlerVersion | 1.1 | 拡張機能のバージョン。 拡張機能のクラシック バージョン 1.0 を 1.1 にアップグレードすることをお勧めします |
| dataPath | string | Service Fabric システムのサービスおよびアプリケーション データの状態を保存するために使用されるドライブへのパス。

## <a name="next-steps"></a>次のステップ

* ["任意の場所にデプロイ" 機能の概要と Azure で管理されるクラスターとの比較](service-fabric-deploy-anywhere.md)に関するページを参照します。
* [クラスター セキュリティ](service-fabric-cluster-security.md)について学習します。
* 特定のスケール セット インスタンスに[リモート接続する](service-fabric-cluster-remote-connect-to-azure-cluster-node.md)
* 展開後にクラスター VM で [RDP ポートの範囲値を更新する](./scripts/service-fabric-powershell-change-rdp-port-range.md)
* クラスター VM の[管理者ユーザー名とパスワードを変更する](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md)

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
