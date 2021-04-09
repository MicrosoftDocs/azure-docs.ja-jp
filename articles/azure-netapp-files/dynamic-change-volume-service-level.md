---
title: Azure NetApp Files のボリュームのサービス レベルを動的に変更する | Microsoft Docs
description: ボリュームのサービス レベルを動的に変更する方法について説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/14/2021
ms.author: b-juche
ms.openlocfilehash: 7b5bbad1f0691f76c12f161d1dd1f9d6ddc43270
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102184323"
---
# <a name="dynamically-change-the-service-level-of-a-volume"></a>ボリュームのサービス レベルを動的に変更する

> [!IMPORTANT] 
> 現時点では、レプリケーション先のボリュームのサービス レベルの動的な変更はサポートされていません。

既存のボリュームのサービス レベルは変更できます。それには、目的のボリュームで使用したい[サービス レベル](azure-netapp-files-service-levels.md)を使用中の別の容量プールに、目的のボリュームを移動します。 ボリュームのサービス レベルのインプレース変更では、データを移行する必要はありません。 ボリュームへのアクセスにも影響はありません。  

この機能により、オンデマンドでワークロードのニーズを満たすことができます。  既存のボリュームを変更し、パフォーマンス向上のために上位のサービス レベルを使用することも、コスト最適化のために下位のサービス レベルを使用することもできます。 たとえば、ボリュームが現在 *Standard* サービス レベルを使用している容量プールにあり、そのボリュームで *Premium* サービス レベルを使用する場合は、そのボリュームを、*Premium* サービス レベルを使用している容量プールに動的に移動できます。  

ボリュームの移動先になる容量プールは、既に存在している必要があります。 容量プールには、他のボリュームを含めることができます。  まったく新しい容量プールにボリュームを移動する場合は、ボリュームを移動する前に[容量プールを作成する](azure-netapp-files-set-up-capacity-pool.md)必要があります。  

## <a name="considerations"></a>考慮事項

* ボリュームを別の容量プールに移動した後は、以前のボリューム アクティビティ ログとボリューム メトリックにアクセスできなくなります。 ボリュームは、新しい容量プールのもとで新しいアクティビティ ログとメトリックを使用して開始されます。

* 上位のサービス レベルの容量プールにボリュームを移動する場合 (たとえば、*Standard* から *Premium* または *Ultra* サービス レベルに移動する) は、下位のサービス レベルの容量プールにそのボリュームを *再度* 移動する (たとえば、*Ultra* から *Premium* または *Standard* へ移動する) には、少なくとも 7 日待機する必要があります。  

## <a name="register-the-feature"></a>機能を登録する

ボリュームを別の容量プールに移動する機能は、現在プレビューの段階です。 この機能を初めて使用する場合は、まず機能を登録する必要があります。

1. 機能を登録します。 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```

2. 機能の登録の状態を確認します。 

    > [!NOTE]
    > **RegistrationState** が `Registering` 状態から `Registered` に変化するまでに最大 60 分間かかる場合があります。 この状態が **Registered** になってから続行してください。

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```
また、[Azure CLI のコマンド](/cli/azure/feature) `az feature register` と `az feature show` を使用して、機能を登録し、登録状態を表示することもできます。 
 
## <a name="move-a-volume-to-another-capacity-pool"></a>ボリュームを別の容量プールに移動する

1.  [ボリューム] ページで、サービス レベルを変更するボリュームを右クリックします。 **[Change Pool] (プールの変更)** を選択します。

    ![ボリュームを右クリックする](../media/azure-netapp-files/right-click-volume.png)

2. [Change Pool] (プールの変更) ウィンドウで、ボリュームの移動先となる容量プールを選択します。 

    ![プールの変更](../media/azure-netapp-files/change-pool.png)

3.  **[OK]** をクリックします。


## <a name="next-steps"></a>次のステップ  

* [Azure NetApp Files のサービス レベル](azure-netapp-files-service-levels.md)
* [容量プールを設定する](azure-netapp-files-set-up-capacity-pool.md)
* [ボリュームの容量プールの変更に関する問題のトラブルシューティング](troubleshoot-capacity-pools.md#issues-when-changing-the-capacity-pool-of-a-volume)
