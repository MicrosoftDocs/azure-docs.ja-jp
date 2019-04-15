---
title: Azure NetApp Files を使用してスナップショットを管理する | Microsoft Docs
description: Azure NetApp Files を使用して、ボリュームのスナップショットを作成したり、スナップショットから新しいボリュームを復元したりする方法について説明します。
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
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: b-juche
ms.openlocfilehash: 01387d0c219c86f33762b9c3fbf9f81cf04b4455
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58880816"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Azure NetApp Files を使用して、スナップショットを管理する

Azure NetApp Files を使用すると、ボリュームのオンデマンド スナップショットを作成したり、スナップショットから新しいボリュームを復元したりできます。

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>ボリュームのオンデマンド スナップショットを作成する

スナップショットは、オンデマンドでのみ作成できます。 スナップショット ポリシーは、現在サポートされていません。

1.  [ボリューム] ブレードで、**[スナップショット]** をクリックします。

    ![スナップショットに移動する](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  **[+ スナップショットの追加]** をクリックして、ボリュームのオンデマンド スナップショットを作成します。

    ![スナップショットを追加する](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  [新しいスナップショット] ウィンドウで、作成する新しいスナップショットの名前を指定します。   

    ![新しいスナップショット](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Click **OK**. 

## <a name="restore-a-snapshot-to-a-new-volume"></a>スナップショットから新しいボリュームを復元する

現時点では、スナップショットから復元できるのは、新しいボリュームに限られています。 
1. [ボリューム] ブレードから **[Manage Snapshots]\(スナップショットの管理\)** ブレードに移動して、スナップショットの一覧を表示します。 
2. 復元するスナップショットを選択します。  
3. スナップショット名を右クリックし、メニュー オプションから **[Restore to new volume]\(新しいボリュームに復元\)** を選択します。  

    ![スナップショットから新しいボリュームを復元する](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

4. [新しいボリューム] ウィンドウで、新しいボリュームの情報を指定します。  
    * **名前**   
        作成するボリュームの名前を指定します。  
        
        名前はリソース グループ内で一意である必要があります。 3 文字以上になるようにしてください。  任意の英数字を使用できます。

    * **ファイル パス**     
        新しいボリュームのエクスポート パスを作成する際に使用するファイル パスを指定します。 ボリュームのマウントとアクセスには、このエクスポート パスが使用されます。   
        
        マウント ターゲットは、NFS サービスの IP アドレスのエンドポイントです。 自動的に生成されます。   
        
        ファイル パス名には、文字、数字、ハイフン ("-") のみを含めることができます。 長さは 16 文字から 40 文字でなければなりません。 

    * **Quota**  
        ボリュームに割り当てられる論理ストレージの量を指定します。  

        **[使用可能なクォータ]** フィールドには、選択した容量プール内の未使用の領域のうち、新しいボリュームの作成に使用できる領域の量が示されます。 新しいボリュームのサイズが、使用可能なクォータを超えてはいけません。

    *   **仮想ネットワーク**  
        ボリュームへのアクセス元となる Azure Virtual Network (Vnet) を指定します。  
        指定する Vnet には、Azure NetApp Files に委任されているサブネットがある必要があります。 Azure NetApp Files サービスにアクセスできるのは、同じ Vnet からの場合、またはボリュームと同じリージョンにある Vnet から Vnet ピアリングを経由した場合のみです。 ボリュームには、オンプレミス ネットワークから Express Route 経由でアクセスできます。 

    * **Subnet**  
        ボリュームで使用するサブネットを指定します。  
        指定するサブネットは Azure NetApp Files サービスに委任されている必要があります。 [サブネット] フィールドにある **[新規作成]** を選択することで、新しいサブネットを作成できます。  
   <!--
    ![Restored new volume](../media/azure-netapp-files/azure-netapp-files-snapshot-new-volume.png) 
   -->

5. Click **OK**.   
    スナップショットから復元された新しいボリュームが [ボリューム] ブレードに表示されます。

## <a name="next-steps"></a>次の手順

[Azure NetApp Files のストレージ階層を理解する](azure-netapp-files-understand-storage-hierarchy.md)