---
title: "Azure Stack の物理ディスクを交換する | Microsoft Docs"
description: "Azure Stack の物理ディスクを交換する方法の概要を説明します。"
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 26e5e9f8882cc764922a2cbf0f39e7a3d1b6995b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="replace-a-physical-disk-in-azure-stack"></a>Azure Stack の物理ディスクを交換する

*適用対象: Azure Stack 統合システムと Azure Stack Development Kit*

この記事では、Azure Stack で物理ディスクを交換する一般的な手順について説明します。 物理ディスクが正常に機能しなくなった場合、早急に交換する必要があります。

この手順は、統合システムと、ホット スワップ可能なディスクを含む開発キットのデプロイに使用できます。

実際のディスク交換手順は、ご利用の OEM (Original Equipment Manufacturer) ハードウェア ベンダーによって異なります。 お使いのシステムに特化した詳しい手順については、ベンダーの現場交換可能ユニット (FRU) ドキュメントをご覧ください。 

## <a name="review-disk-alert-information"></a>ディスクのアラート情報の確認
ディスクが正常に機能しなくなると、物理ディスクへの接続が切断されたことを通知するアラートが表示されます。 

 ![物理ディスクへの接続が切断されたことを示すアラート](media/azure-stack-replace-disk/DiskAlert.png)

アラートを開くと、スケール ユニット ノードおよび交換が必要なディスクの厳密な物理スロットの場所が、アラートの説明に示されます。 Azure Stack では LED インジケーター機能を使用して、障害が発生したディスクを識別するさらなる手助けをします。

 ## <a name="replace-the-disk"></a>ディスクの交換

実際のディスク交換については、OEM ハードウェア ベンダーの FRU 手順に従ってください。

統合システムでサポートされていないディスクの使用を回避するために、ベンダーでサポートされていないディスクはシステムでブロックされます。 サポートされていないディスクを使用しようとすると、サポートされていないモデルまたはファームウェアであるためディスクが検疫された、というアラートが新たに表示されます。

ディスクを交換すると、Azure Stack が自動的に新しいディスクを検出し、仮想ディスクの修復プロセスを開始します。  
 
 ## <a name="check-the-status-of-virtual-disk-repair"></a>仮想ディスクの修復状態の確認
 
 ディスクの交換後、特権エンドポイントを使用して、仮想ディスクの正常性状態と修復ジョブの進行を監視できます。 特権エンドポイントにネットワーク接続されている任意のコンピューターから、次の手順に従います。

1. Windows PowerShell セッションを開き、特権エンドポイントに接続します。
    ````PowerShell
        $cred = Get-Credential
        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```` 
  
2. 仮想ディスクの正常性を表示するには、次のコマンドを実行します。
    ````PowerShell
        Get-VirtualDisk -CimSession s-cluster
    ````
   ![Get-VirtualDisk コマンドの Powershell 出力](media/azure-stack-replace-disk/GetVirtualDiskOutput.png)

3. 現在の記憶域ジョブの状態を表示するには、次のコマンドを実行します。
    ```PowerShell
        Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
    ````
      ![Get-StorageJob コマンドの Powershell 出力](media/azure-stack-replace-disk/GetStorageJobOutput.png)

## <a name="troubleshoot-virtual-disk-repair"></a>仮想ディスク修復のトラブルシューティング

仮想ディスクの修復ジョブが進行しないようであれば、次のコマンドを実行してジョブを最初からやり直してください。
  ````PowerShell
        Get-VirtualDisk -CimSession s-cluster | Repair-VirtualDisk
  ```` 
