---
title: Azure Stack のスケール ノードの追加 | Microsoft Docs
description: Azure Stack のスケール ユニットにノードを追加します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: thoroet
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: 17da540bd6077b8e045f125fd3cf13dc0e043000
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58882040"
---
# <a name="add-additional-scale-unit-nodes-in-azure-stack"></a>Azure Stack のスケール ユニット ノードを追加する

Azure Stack オペレーターは、物理コンピューターを追加することによって、既存のスケール ユニットの全体的な容量を引き上げることができます。 物理コンピューターは、"スケール ユニット ノード" とも呼ばれます。 新たに追加する各スケール ユニット ノードは、CPU の種類、メモリ、ディスク数、ディスク サイズの点で、スケール ユニットの既存のノードと同質であることが必要です。

スケール ユニット ノードを追加するには、Azure Stack で、お使いのハードウェア機器メーカー (OEM) から提供されるツールを実行する必要があります。 OEM のツールは、新しい物理コンピューターのファームウェア レベルが既存のノードと同じであることを確認するために、ハードウェア ライフサイクル ホスト (HLH) で実行されます。

次のフロー図は、スケール ユニット ノードを追加するための一般的なプロセスを示しています。

![スケール ユニットの追加フロー](media/azure-stack-add-scale-node/add-node-flow.png) &#42; "*OEM ハードウェア ベンダーが物理サーバー ラックの配置とファームウェアの更新を行うかどうかは、サポート契約により異なります。*"

新しいノードを追加する作業は、完了までに数時間から数日かかることがあります。

> [!Note]  
> スケール ユニット ノードの追加の操作が既に進行中の場合、次の操作は行わないでください。
>
>  - Azure Stack の更新
>  - 証明書のローテーション
>  - Azure Stack の停止
>  - スケール ユニット ノードの修復


## <a name="add-scale-unit-nodes"></a>スケール ユニット ノードの追加

次の手順でノードの追加方法を簡単に説明します。 この手順を実行する前に必ず、OEM から容量拡張に関して提供されるドキュメントに目を通しておいてください。

1. 新しい物理サーバーをラックに設置し、正しく配線します。 
2. 物理スイッチ ポートを有効にし、アクセス制御リスト (ACL) を調整します (該当する場合)。
3. ベースボード管理コントローラー (BMC) で正しい IP アドレスを設定し、すべての BIOS 設定を適用します。OEM から提供されたドキュメントに従ってください。
4. HLH 上で実行されているハードウェア メーカー製ツールを使って、最新のファームウェア ベースラインをすべてのコンポーネントに適用します。
5. Azure Stack 管理ポータルでノードの追加操作を実行します。
6. ノードの追加操作が成功したことを確認します。 [スケール ユニットの **[状態]**](#monitor-add-node-operations) をチェックしてください。 

## <a name="add-the-node"></a>ノードの追加

新しいノードは、管理ポータルまたは PowerShell を使って追加できます。 ノードの追加操作では、まず新しいスケール ユニット ノードを利用可能な計算処理能力として追加します。その後、ストレージ容量が自動的に拡張されます。 Azure Stack は、"*計算*" と "*ストレージ*" が一体となってスケーリングされるハイパーコンバージド システムであるため、容量は自動的に拡張されます。

### <a name="use-the-admin-portal"></a>管理ポータルの使用

1. Azure Stack 管理ポータルに Azure Stack オペレーターとしてサインインします。
2. **[+ リソースの作成]** > **[キャパシティ]** > **[Scale Unit Node]\(スケール ユニット ノード\)** に移動します。
   ![スケール ユニット ノード](media/azure-stack-add-scale-node/select-node1.png)
3. **[ノードの追加]** ウィンドウで *[リージョン]* を選択し、ノードを追加する *スケール ユニット* を選択します。 また、追加するスケール ユニット ノードには、*BMC IP アドレス*を指定します。 一度に追加できるノードは 1 つだけです。
   ![ノードの詳細の追加](media/azure-stack-add-scale-node/select-node2.png)
 

### <a name="use-powershell"></a>PowerShell の使用

ノードの追加には、**New-AzsScaleUnitNodeObject** コマンドレットを使用します。  

次のいずれかのサンプル PowerShell スクリプトを使用する前に、<*node names*> と <*IP addresses*> を実際の Azure Stack 環境の値に置き換えてください。

  > [!Note]  
  > ノードの名前を付けるときは、長さを 15 文字未満にする必要があります。 また、スペースを含む名前や次の文字を含む名前は使用できません: `\`、`/`、`:`、`*`、`?`、`"`、`<`、`>`、`|`、`\`、`~`、`!`、`@`、`#`、`$`、`%`、`^`、`&`、`(`、`)`、`{`、`}`、`_`。

**ノードを追加する: **
  ```powershell
  ## Add a single Node 
  $NewNode=New-AzsScaleUnitNodeObject -computername "<name_of_new_node>" -BMCIPv4Address "<BMCIP_address_of_new_node>" 
 
  Add-AzsScaleUnitNode -NodeList $NewNode -ScaleUnit "<name_of_scale_unit_cluster>" 
  ```  

## <a name="monitor-add-node-operations"></a>ノードの追加操作の監視 
ノードの追加操作の状態は、管理ポータルまたは PowerShell を使用して取得できます。 ノードの追加操作は、完了までに数時間から数日かかることがあります。

### <a name="use-the-admin-portal"></a>管理ポータルの使用 
新しいノードの追加を監視するには、管理ポータルで、スケール ユニットまたはスケール ユニット ノード オブジェクトを確認します。 そのためには、**[Region management]\(リージョン管理\)** > **[Scale units]\(スケール ユニット\)** に移動します。 次に、確認するスケール ユニットまたはスケール ユニット ノードを選択します。 

### <a name="use-powershell"></a>PowerShell の使用
スケール ユニットとスケール ユニット ノードの状態は、次のように PowerShell を使って取得できます。
  ```powershell
  #Retrieve Status for the Scale Unit
  Get-AzsScaleUnit|select name,state
 
  #Retrieve Status for each Scale Unit Node
  Get-AzsScaleUnitNode |Select Name, ScaleUnitNodeStatus
```

### <a name="status-for-the-add-node-operation"></a>ノードの追加操作の状態 
**スケール ユニットの場合:**

|Status               |説明  |
|---------------------|---------|
|実行中              |すべてのノードは、アクティブにスケール ユニットに参加しています。|
|停止済み              |スケール ユニット ノードはダウンしているか、到達不能です。|
|拡張中            |現在少なくとも 1 つのスケール ユニット ノードが計算処理能力として追加されています。|
|記憶域を構成しています  |計算処理能力は拡張済みで、現在、ストレージの構成が実行されています。|
|修復が必要 |スケール ユニット ノードの修復を必要とするエラーが少なくとも 1 つ検出されました。|


**スケール ユニット ノードの場合: **

|Status                |説明  |
|----------------------|---------|
|実行中               |ノードは、アクティブにスケール ユニットに参加しています。|
|停止済み               |ノードは利用不可です。|
|追加中                |ノードは、アクティブにスケール ユニットに追加されています。|
|修復中             |ノードは現在、アクティブに修復されています。|
|メンテナンス            |ノードは一時停止され、アクティブなユーザー ワークロードは実行されていません。 |
|修復が必要  |ノードの修復を必要とするエラーが検出されました。|


## <a name="troubleshooting"></a>トラブルシューティング
ノードを追加するときに多く見られる問題は次のとおりです。 

**シナリオ 1:** スケール ユニット ノードの追加操作は失敗するが、一覧に停止状態として表示されるノードが少なくとも 1 つ存在する。  
- 修復: 修復操作を使用してノードを修復してください。 一度に実行できる修復操作は 1 つだけです。

**シナリオ 2:** 1 つまたは複数のスケール ユニット ノードが追加されましたが、ストレージの拡張に失敗した。 このシナリオでは、スケール ユニット ノード オブジェクトから "実行中" という状態が報告されますが、"記憶域を構成しています" タスクが開始されません。  
- 修復: 特権のあるエンドポイントを使用し、次の PowerShell コマンドレットを実行してストレージの正常性を確認してください。
  ```powershell
     Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
  ```
 
**シナリオ 3:** ストレージのスケールアウト ジョブが失敗したことを示すアラートを受信した。  
- 修復: このケースでは、ストレージの構成タスクが失敗しています。 この問題はサポートに連絡する必要があります。


## <a name="next-steps"></a>次の手順 
[パブリック IP アドレスの追加](azure-stack-add-ips.md) 
