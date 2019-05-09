---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3b596e5bad8202d88ea06c7eee114bec1063a35f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58052235"
---
# <a name="enabling-azure-ultra-ssds"></a>Azure Ultra SSD の有効化

Azure Ultra SSD では、Azure IaaS VM 用に高スループット、高 IOPS、一貫性のある低待機時間のディスク ストレージが提供されます。 この新しいオファリングは、Microsoft の既存のディスク オファリングと同じレベルの可用性で最上のパフォーマンスを提供します。 Ultra SSD のその他のメリットとして、仮想マシンを再起動する必要なしに、ワークロードに合わせてディスクのパフォーマンスを動的に変更できます。 Ultra SSD は、SAP HANA、最上位層のデータベース、トランザクションの多いワークロードなど、データ集中型のワークロードに適しています。

現在、Ultra SSD はプレビュー段階であり、アクセスするにはプレビューに[登録](https://aka.ms/UltraSSDPreviewSignUp)する必要があります。

承認された後、次のいずれかのコマンドを実行して、Ultra ディスクをデプロイする米国東部 2 内のゾーンを決定します。

PowerShell: `Get-AzComputeResourceSku | where {$_.ResourceType -eq "disks" -and $_.Name -eq "UltraSSD_LRS" }`

CLI: `az vm list-skus --resource-type disks --query “[?name==’UltraSSD_LRS’]”`

応答は下のフォームのようになります。X は、米国東部 2 でのデプロイに使用するゾーンです。 X は 1、2、3 のいずれかになります。

|ResourceType  |Name  |Location  |ゾーン  |Restriction  |機能  |値  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

コマンドから応答がなかった場合は、機能に対する登録がまだ保留中か、まだ承認されていないことを意味します。

デプロイするゾーンがわかったので、この記事のデプロイ手順に従い、Ultra SSD を使用して最初の VM をデプロイします。

## <a name="deploying-an-ultra-ssd"></a>Ultra SSD のデプロイ

最初に、デプロイする VM のサイズを決定します。 このプレビューの一部としては、DsV3 および EsV3 VM ファミリのみがサポートされています。 VM サイズについて詳しくは、こちらの[ブログ](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)の 2 番目のテーブルをご覧ください。
また、[複数の Ultra SSD を使用した VM の作成](https://aka.ms/UltraSSDTemplate)のサンプルを参照してください。複数の Ultra SSD を含む VM の作成方法が示されています。

以下では新しい/変更された Resource Manager テンプレートの変更について説明します。`Microsoft.Compute/virtualMachines` および `Microsoft.Compute/Disks` の **apiVersion** は、`2018-06-01` (またはそれ以降) に設定する必要があります。

Ultra ディスクを作成するには、ディスク SKU UltraSSD_LRS、ディスク容量、IOPS、スループット (MBps) を指定します。 次に示すのは、1,024 GiB (GiB = 2^30 バイト)、80,000 IOPS、1,200 MBps  (MBps = 10^6 バイト/秒) でディスクを作成する例です。

```json
"properties": {  
    "creationData": {  
    "createOption": "Empty"  
},  
"diskSizeGB": 1024,  
"diskIOPSReadWrite": 80000,  
"diskMBpsReadWrite": 1200,  
}
```

VM のプロパティで機能を追加して、Ultra 対応であることを示します (完全な Resource Manager テンプレートについては[サンプル](https://aka.ms/UltraSSDTemplate)をご覧ください)。

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines",
    "properties": {
                    "hardwareProfile": {},
                    "additionalCapabilities" : {
                                    "ultraSSDEnabled" : "true"
                    },
                    "osProfile": {},
                    "storageProfile": {},
                    "networkProfile": {}
    }
}
```

VM がプロビジョニングされたら、データ ディスクをパーティション分割してフォーマットし、ワークロード用に構成できます。

## <a name="additional-ultra-ssd-scenarios"></a>Ultra SSD の他のシナリオ

- VM の作成時に、Ultra SSD を暗黙で作成することもできます。 ただし、これらのディスクの IOPS (500) とスループット (8 MiB/秒) は既定値になります。
- 互換性のある VM に、追加の Ultra SSD をアタッチできます。
- Ultra SSD では、実行時に、ディスクをデタッチすることなく、仮想マシンから、ディスク パフォーマンス属性 (IOPS とスループット) を調整できます。 ディスクでディスク パフォーマンス サイズ変更操作を実行した場合、変更が実際に有効になるまでに最大で 1 時間かかることがあります。
- ディスク容量を拡大するときは、仮想マシンの割り当てを解除する必要があります。

## <a name="next-steps"></a>次の手順

新しいディスクの種類を試してみたい場合で、まだプレビューにサインアップしていないときは、[この調査からアクセスを要求](https://aka.ms/UltraSSDPreviewSignUp)してください。
