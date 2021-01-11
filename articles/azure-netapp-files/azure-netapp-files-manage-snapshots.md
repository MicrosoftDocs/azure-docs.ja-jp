---
title: Azure NetApp Files を使用してスナップショットを管理する | Microsoft Docs
description: Azure NetApp Files を使用してスナップショットを作成して管理する方法について説明します。
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
ms.date: 07/24/2020
ms.author: b-juche
ms.openlocfilehash: 85990aee5143c9ccc0362a00597a748763977204
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88080217"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Azure NetApp Files を使用して、スナップショットを管理する

Azure NetApp Files では、オンデマンドのスナップショットの作成と、スナップショット ポリシーを使用した自動スナップショット作成のスケジュール設定がサポートされています。  スナップショットから新しいボリュームを復元することもできます。  

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>ボリュームのオンデマンド スナップショットを作成する

ボリュームのスナップショットをオンデマンドで作成できます。 

1.  スナップショットを作成するボリュームに移動します。 **[スナップショット]** をクリックします。

    ![スナップショットに移動する](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  **[+ スナップショットの追加]** をクリックして、ボリュームのオンデマンド スナップショットを作成します。

    ![スナップショットを追加する](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  [新しいスナップショット] ウィンドウで、作成する新しいスナップショットの名前を指定します。   

    ![新しいスナップショット](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. **[OK]** をクリックします。 

## <a name="manage-snapshot-policies"></a>スナップショット ポリシーを管理する

スナップショット ポリシーを使用して、ボリュームのスナップショットが自動的に取得されるようにスケジュールすることができます。 必要に応じてスナップショット ポリシーを変更したり、不要になったスナップショット ポリシーを削除したりすることもできます。  

### <a name="register-the-feature"></a>機能を登録する

**スナップショット ポリシー**機能は、現在プレビュー段階です。 この機能を初めて使用する場合は、まず機能を登録する必要があります。 

1. 機能を登録します。 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

2. 機能の登録の状態を確認します。 

    > [!NOTE]
    > **RegistrationState** が `Registering` 状態から `Registered` に変化するまでに最大 60 分間かかる場合があります。 この状態が **Registered** になってから続行してください。

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

### <a name="create-a-snapshot-policy"></a>スナップショット ポリシーを作成する 

スナップショット ポリシーを使用すると、スナップショットの作成頻度 (時間単位、日単位、週単位、または月単位) を指定できます。 また、ボリュームに対して保持するスナップショットの最大数も指定する必要があります。  

1.  NetApp アカウント ビューで、 **[スナップショット ポリシー]** をクリックします。

    ![スナップショット ポリシーのナビゲーション](../media/azure-netapp-files/snapshot-policy-navigation.png)

2.  [スナップショット ポリシー] ウィンドウで、[ポリシーの状態] を **[有効]** に設定します。 

3.  **[時間単位]** 、 **[日単位]** 、 **[週単位]** 、または **[月単位]** タブをクリックして、時間単位、日単位、週単位、または月単位のスナップショット ポリシーを作成します。 **[保持するスナップショット数]** を指定します。  

    1 つのボリュームで許可されるスナップショットの最大数については、「[Azure NetApp Files のリソースの制限](azure-netapp-files-resource-limits.md)」を参照してください。 

    次の例は、時間単位のスナップショット ポリシーの構成を示しています。 

    ![スナップショット ポリシー (時間単位)](../media/azure-netapp-files/snapshot-policy-hourly.png)

    次の例は、日単位のスナップショット ポリシーの構成を示しています。

    ![スナップショット ポリシー (日単位)](../media/azure-netapp-files/snapshot-policy-daily.png)

    次の例は、週単位のスナップショット ポリシーの構成を示しています。

    ![スナップショット ポリシー (週単位)](../media/azure-netapp-files/snapshot-policy-weekly.png)

    次の例は、月単位のスナップショット ポリシーの構成を示しています。

    ![スナップショット ポリシー (月単位)](../media/azure-netapp-files/snapshot-policy-monthly.png) 

4.  **[保存]** をクリックします。  

追加のスナップショット ポリシーを作成する必要がある場合は、手順 3 を繰り返します。
作成したポリシーは、[スナップショット ポリシー] ページに表示されます。

ボリュームでスナップショット ポリシーを使用する場合は、[ボリュームにポリシーを適用する](azure-netapp-files-manage-snapshots.md#apply-a-snapshot-policy-to-a-volume)必要があります。 

### <a name="apply-a-snapshot-policy-to-a-volume"></a>ボリュームにスナップショット ポリシーを適用する

作成したスナップショット ポリシーをボリュームで使用する場合は、ボリュームにポリシーを適用する必要があります。 

1.  **[ボリューム]** ページに移動し、スナップショット ポリシーを適用するボリュームを右クリックし、 **[編集]** を選択します。

    ![ボリュームの右クリックメニュー](../media/azure-netapp-files/volume-right-cick-menu.png) 

2.  [編集] ウィンドウの **[スナップショット ポリシー]** で、ボリュームに使用するポリシーを選択します。  **[OK]** をクリックしてポリシーを適用します。  

    ![スナップショット ポリシーの編集](../media/azure-netapp-files/snapshot-policy-edit.png) 

### <a name="modify-a-snapshot-policy"></a>スナップショット ポリシーを変更する 

既存のスナップショット ポリシーを変更して、ポリシーの状態、スナップショットの頻度 (時間単位、日単位、週単位、または月単位)、または保持するスナップショットの数を変更できます。  
 
1.  NetApp アカウント ビューで、 **[スナップショット ポリシー]** をクリックします。

2.  変更するスナップショット ポリシーを右クリックし、 **[編集]** を選択します。

    ![スナップショット ポリシーの右クリック メニュー](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  表示された [スナップショット ポリシー] ウィンドウで変更を行い、 **[保存]** をクリックします。 

### <a name="delete-a-snapshot-policy"></a>スナップショット ポリシーを削除する 

保持する必要がなくなったスナップショット ポリシーを削除できます。   

1.  NetApp アカウント ビューで、 **[スナップショット ポリシー]** をクリックします。

2.  変更するスナップショット ポリシーを右クリックし、 **[削除]** を選択します。

    ![スナップショット ポリシーの右クリック メニュー](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  **[はい]** をクリックして、スナップショット ポリシーの削除を確認します。   

    ![スナップショット ポリシーの削除の確認](../media/azure-netapp-files/snapshot-policy-delete-confirm.png) 

## <a name="restore-a-snapshot-to-a-new-volume"></a>スナップショットから新しいボリュームを復元する

現時点では、スナップショットから復元できるのは、新しいボリュームに限られています。 
1. [ボリューム] ブレードから **[スナップショット]** を選択して、スナップショットの一覧を表示します。 
2. 復元するスナップショットを右クリックし、メニュー オプションから **[新しいボリュームに復元]** を選択します。  

    ![スナップショットから新しいボリュームを復元する](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

3. [ボリュームの作成] ウィンドウで、新しいボリュームの情報を指定します。  
    * **[名前]**    
        作成するボリュームの名前を指定します。  
        
        名前はリソース グループ内で一意である必要があります。 3 文字以上になるようにしてください。  任意の英数字を使用できます。

    * **[クォータ]**  
        ボリュームに割り当てる論理ストレージの量を指定します。  

    ![新しいボリュームに復元](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

4. **[確認および作成]** をクリックします。  **Create** をクリックしてください。   
    新しいボリュームでは、スナップショットで使用されていたものと同じプロトコルが使用されます。   
    スナップショットから復元された新しいボリュームが [ボリューム] ブレードに表示されます。

## <a name="next-steps"></a>次のステップ

* [Azure NetApp Files のストレージ階層を理解する](azure-netapp-files-understand-storage-hierarchy.md)
* [Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md)
