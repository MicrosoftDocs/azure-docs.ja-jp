---
title: Azure Batch プール作成イベント | Microsoft Docs
description: Batch のプール作成イベントのリファレンスです。
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: a86ce329a19272eb83c431af395b330f75111361
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323294"
---
# <a name="pool-create-event"></a>プール作成イベント

 プールが作成されるとイベントが生成されます。 ログの内容はプールに関する一般的な情報です。 プールのターゲット サイズがコンピューティング ノード 0 を上回る場合、イベントの直後にプール サイズ変更イベントが開始されます。

 次の例は、CloudServiceConfiguration プロパティを用いた、プール本文によるプール イベントの作成を示しています。

```
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicated": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

|要素|型|メモ|
|-------------|----------|-----------|
|id|string|プールの ID。|
|displayName|string|プールの表示名。|
|vmSize|string|プール内の仮想マシンのサイズ。 プール内の仮想マシンのサイズはすべて同じです。 <br/><br/> クラウド サービスのプール (cloudServiceConfiguration で作成されたプール) で利用可能な仮想マシンのサイズについては、[クラウド サービスのサイズ](https://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/)を参照してください。 Batch は、`ExtraSmall` を除くすべての Cloud Services VM サイズに対応しています。<br/><br/> Virtual Machines Marketplace から、イメージを使用して利用可能なプール (virtualMachineConfiguration で作成されたプール) の VM サイズについては、[仮想マシンのサイズ](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/) (Linux) または[仮想マシンのサイズ](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) (Windows) を参照してください。 Batch は、Premium Storage を使用する VM (`STANDARD_GS`、`STANDARD_DS`、`STANDARD_DSV2` シリーズ) と `STANDARD_A0` を除くすべての Azure VM サイズに対応しています。|
|[CloudServiceConfiguration](#bk_csconf)|複合型|プールのクラウドサービス構成。|
|[virtualMachineConfiguration](#bk_vmconf)|複合型|プールの仮想マシン構成。|
|[networkConfiguration](#bk_netconf)|複合型|プールのネットワーク構成。|
|resizeTimeout|Time|最終のサイズ変更時に指定されたプールに対するコンピューティング ノードのタイムアウト割り当て。  (プール作成時の初回サイズ設定もサイズ変更とカウントされます。)|
|targetDedicated|Int32|プールに要求されたコンピューティング ノード数。|
|enableAutoScale|Bool|プールのサイズを自動的に調整し続けるかどうかを指定します。|
|enableInterNodeCommunication|Bool|ノード間の直接的な通信に対し、プールが設定されるかどうかを指定します。|
|isAutoPool|Bool|プールがジョブの AutoPool メカニズムを介して作成されたかどうかを指定します。|
|maxTasksPerNode|Int32|プール内の単一コンピューティング ノードで同時に実行できるタスクの最大数。|
|vmFillType|string|プール内のコンピューティング ノード間で Batch サービスがタスクを分散する方法を定義します。 有効な値は、分散またはパックです。|

###  <a name="bk_csconf"></a> cloudServiceConfiguration

|要素名|Type|メモ|
|------------------|----------|-----------|
|osFamily|string|プール内の仮想マシンにインストールする Azure ゲスト OS ファミリ。<br /><br /> 次のいずれかの値になります。<br /><br /> **2** – OS ファミリ 2、Windows Server 2008 R2 SP1 に相当。<br /><br /> **3** – OS ファミリ 3、Windows Server 2012 に相当。<br /><br /> **4** – OS ファミリ 4、Windows Server 2012 R2 に相当。<br /><br /> 詳細については、[Azure ゲスト OS リリース](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases)を参照してください。|
|targetOSVersion|string|プール内の仮想マシンにインストールされる Azure ゲスト OS バージョン。<br /><br /> 既定値は **\*** で、規定ファミリの最新オペレーティング システムのバージョンを指定します。<br /><br /> その他の許可値については、[Azure ゲスト OS リリース](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases)を参照してください。|

###  <a name="bk_vmconf"></a>VirtualMachineConfiguration

|要素名|Type|メモ|
|------------------|----------|-----------|
|[imageReference](#bk_imgref)|複合型|使用するプラットフォームまたは Marketplace イメージに関する情報を指定します。|
|nodeAgentSKUId|string|コンピューティング ノードでプロビジョニングされているバッチ ノード エージェントの SKU。|
|[windowsConfiguration](#bk_winconf)|複合型|仮想マシン上の Windows オペレーティング システムの設定を指定します。 imageReference が Linux OS イメージを参照している場合、プロパティは指定できません。|

###  <a name="bk_imgref"></a>imageReference

|要素名|Type|メモ|
|------------------|----------|-----------|
|publisher|string|イメージの発行元。|
|offer|string|イメージのプラン。|
|sku|string|イメージの SKU。|
|version|string|イメージのバージョン。|

###  <a name="bk_winconf"></a>windowsConfiguration

|要素名|Type|メモ|
|------------------|----------|-----------|
|enableAutomaticUpdates|Boolean|仮想マシンの自動更新が有効になっているかどうかを示しています。 このプロパティが指定されていない場合は、既定値が正規の値となります。|

###  <a name="bk_netconf"></a>NetworkConfiguration

|要素名|Type|メモ|
|------------------|--------------|----------|
|subnetId|string|プールのコンピューティング ノードが作成される、サブネットのリソース識別子を指定します。|
