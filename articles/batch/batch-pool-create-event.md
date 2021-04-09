---
title: Azure Batch プール作成イベント
description: プールが作成されると生成される Batch プール作成イベントのリファレンスです。 ログの内容はプールに関する一般的な情報です。
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: f8b020401443b2a9e80837599d6fd4b2a3001d27
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97609005"
---
# <a name="pool-create-event"></a>プール作成イベント

 プールが作成されるとイベントが生成されます。 ログの内容はプールに関する一般的な情報です。 プールのターゲット サイズがコンピューティング ノード 0 を上回る場合、イベントの直後にプール サイズ変更イベントが開始されます。

 次の例は、`CloudServiceConfiguration` プロパティを使用して作成されたプールのプール作成イベントの本文を示しています。

```
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Standard_F1s",
    "imageType": "VirtualMachineConfiguration",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "virtualMachineConfiguration": {
          "imageReference": {
            "publisher": " ",
            "offer": " ",
            "sku": " ",
            "version": " "
          },
          "nodeAgentId": " "
        },
    "resizeTimeout": "300000",
    "targetDedicatedNodes": 2,
    "targetLowPriorityNodes": 2,
    "taskSlotsPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

|要素|Type|Notes|
|-------------|----------|-----------|
|`id`|String|プールの ID。|
|`displayName`|String|プールの表示名。|
|`vmSize`|String|プール内の仮想マシンのサイズ。 プール内の仮想マシンのサイズはすべて同じです。 <br/><br/> クラウド サービスのプール (cloudServiceConfiguration で作成されたプール) で利用可能な仮想マシンのサイズについては、[クラウド サービスのサイズ](../cloud-services/cloud-services-sizes-specs.md)を参照してください。 Batch は、`ExtraSmall` を除くすべての Cloud Services VM サイズに対応しています。<br/><br/> Virtual Machines Marketplace から、イメージを使用して利用可能なプール (virtualMachineConfiguration で作成されたプール) の VM サイズについては、[仮想マシンのサイズ](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux) または[仮想マシンのサイズ](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows) を参照してください。 Batch は、Premium Storage を使用する VM (`STANDARD_GS`、`STANDARD_DS`、`STANDARD_DSV2` シリーズ) と `STANDARD_A0` を除くすべての Azure VM サイズに対応しています。|
|`imageType`|String|イメージの配置方法。 サポートされる値は `virtualMachineConfiguration` または `cloudServiceConfiguration` です。|
|[`cloudServiceConfiguration`](#bk_csconf)|複合型|プールのクラウドサービス構成。|
|[`virtualMachineConfiguration`](#bk_vmconf)|複合型|プールの仮想マシン構成。|
|[`networkConfiguration`](#bk_netconf)|複合型|プールのネットワーク構成。|
|`resizeTimeout`|Time|最終のサイズ変更時に指定されたプールに対するコンピューティング ノードのタイムアウト割り当て。  (プール作成時の初回サイズ設定もサイズ変更とカウントされます。)|
|`targetDedicatedNodes`|Int32|プールのために要求されている専用のコンピューティング ノードの数。|
|`targetLowPriorityNodes`|Int32|プールのために要求されている優先順位の低いコンピューティング ノードの数。|
|`enableAutoScale`|Bool|プールのサイズを自動的に調整し続けるかどうかを指定します。|
|`enableInterNodeCommunication`|Bool|ノード間の直接的な通信に対し、プールが設定されるかどうかを指定します。|
|`isAutoPool`|Bool|プールがジョブの AutoPool メカニズムを介して作成されたかどうかを指定します。|
|`taskSlotsPerNode`|Int32|プール内の単一コンピューティング ノードで同時に実行できるタスクの最大数。|
|`vmFillType`|String|プール内のコンピューティング ノード間で Batch サービスがタスクを分散する方法を定義します。 有効な値は、分散またはパックです。|

###  <a name="cloudserviceconfiguration"></a><a name="bk_csconf"></a> cloudServiceConfiguration

|要素名|Type|Notes|
|------------------|----------|-----------|
|`osFamily`|String|プール内の仮想マシンにインストールする Azure ゲスト OS ファミリ。<br /><br /> 次のいずれかの値になります。<br /><br /> **2** – OS ファミリ 2、Windows Server 2008 R2 SP1 に相当。<br /><br /> **3** – OS ファミリ 3、Windows Server 2012 に相当。<br /><br /> **4** – OS ファミリ 4、Windows Server 2012 R2 に相当。<br /><br /> 詳細については、[Azure ゲスト OS リリース](../cloud-services/cloud-services-guestos-update-matrix.md#releases)を参照してください。|
|`targetOSVersion`|String|プール内の仮想マシンにインストールされる Azure ゲスト OS バージョン。<br /><br /> 既定値は **\*** で、規定ファミリの最新オペレーティング システムのバージョンを指定します。<br /><br /> その他の許可値については、[Azure ゲスト OS リリース](../cloud-services/cloud-services-guestos-update-matrix.md#releases)を参照してください。|

###  <a name="virtualmachineconfiguration"></a><a name="bk_vmconf"></a>VirtualMachineConfiguration

|要素名|Type|Notes|
|------------------|----------|-----------|
|[`imageReference`](#bk_imgref)|複合型|使用するプラットフォームまたは Marketplace イメージに関する情報を指定します。|
|`nodeAgentId`|String|コンピューティング ノードでプロビジョニングされているバッチ ノード エージェントの SKU。|
|[`windowsConfiguration`](#bk_winconf)|複合型|仮想マシン上の Windows オペレーティング システムの設定を指定します。 imageReference が Linux OS イメージを参照している場合、プロパティは指定できません。|

###  <a name="imagereference"></a><a name="bk_imgref"></a>imageReference

|要素名|Type|Notes|
|------------------|----------|-----------|
|`publisher`|String|イメージの発行元。|
|`offer`|String|イメージのプラン。|
|`sku`|String|イメージの SKU。|
|`version`|String|イメージのバージョン。|

###  <a name="windowsconfiguration"></a><a name="bk_winconf"></a>windowsConfiguration

|要素名|Type|Notes|
|------------------|----------|-----------|
|`enableAutomaticUpdates`|Boolean|仮想マシンの自動更新が有効になっているかどうかを示しています。 このプロパティが指定されていない場合は、既定値が正規の値となります。|

###  <a name="networkconfiguration"></a><a name="bk_netconf"></a>NetworkConfiguration

|要素名|Type|Notes|
|------------------|--------------|----------|
|`subnetId`|String|プールのコンピューティング ノードが作成される、サブネットのリソース識別子を指定します。|