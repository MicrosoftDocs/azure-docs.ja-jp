---
title: "StorSimple 8000 シリーズのボリュームの複製 | Microsoft Docs"
description: "異なる複製の種類と、それらをどのような場合に使用するかについて説明します。また、バックアップ セットを使用して個々のボリュームを複製する方法についても説明します。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 070ac53e-7388-4c48-b8a5-8ed7f9108b2c
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/27/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: eb56cae77722268f42e5126c45ad2878af7db94a
ms.openlocfilehash: 0db0af490f03525f4c23c4e9a23e62a1ef9f1c7f


---
# <a name="use-the-storsimple-manager-service-to-clone-a-volume-update-2"></a>StorSimple Manager サービスを使用してボリュームを複製する (Update 2)
[!INCLUDE [storsimple-version-selector-clone-volume](../../includes/storsimple-version-selector-clone-volume.md)]

## <a name="overview"></a>概要
StorSimple Manager サービスの **[バックアップ カタログ]** ページには、手動バックアップまたは自動バックアップを実行したときに作成されたすべてのバックアップ セットが表示されます。 このページを使用すると、バックアップ ポリシーまたはボリュームのすべてのバックアップを一覧表示したり、バックアップを選択または削除したりできます。また、バックアップを使用してボリュームを復元または複製することもできます。

![[バックアップ カタログ] ページ](./media/storsimple-clone-volume-u2/backupCatalog.png)  

このチュートリアルでは、バックアップ セットを使用して個々のボリュームを複製する方法について説明します。 また、*一時的な*複製と*永続的な*複製の違いについても説明します。

> [!NOTE]
> ローカル固定ボリュームは、階層化ボリュームとして複製されます。 複製されたボリュームをローカル固定ボリュームにする必要がある場合は、複製操作が正常に完了した後でローカル固定ボリュームに変換できます。 階層化ボリュームをローカル固定ボリュームに変換する方法については、「 [ボリュームの種類を変更する](storsimple-manage-volumes-u2.md#change-the-volume-type)」を参照してください。
> 
> 複製後に (ボリュームがまだ一時的な複製であるときに) 複製されたボリュームを階層化ボリュームからローカル固定ボリュームに変換しようとすると、変換は失敗し、次のエラー メッセージが表示されます。
> 
> `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.` 
> 
> このエラーは、別のデバイスに複製する場合にのみ発生します。 一時的な複製を永続的な複製に変換しておけば、ローカル固定ボリュームに問題なく変換できます。 一時的な複製を永続的な複製に変換するには、そのクラウド スナップショットを作成します。
> 
> 

## <a name="create-a-clone-of-a-volume"></a>ボリュームの複製を作成する
複製は、ローカル スナップショットまたはクラウド スナップショットを使用して、同じデバイスや別のデバイス上に作成できるほか、仮想マシン上に作成することもできます。

#### <a name="to-clone-a-volume"></a>ボリュームを複製するには
1. StorSimple Manager サービスのページで、 **[バックアップ カタログ]** タブをクリックし、バックアップ セットを選択します。
2. バックアップ セットを展開して、関連付けられているボリュームを表示します。 バックアップ セットからボリュームをクリックして選択します。
   
     ![ボリュームを複製する](./media/storsimple-clone-volume-u2/CloneVol.png) 
3. **[複製]** をクリックして、選択したボリュームの複製を開始します。
4. ボリュームの複製ウィザードの **[名前と場所の指定]**で、次の手順を実行します。
   
   1. ターゲット デバイスを指定します。 これは、複製が作成される場所です。 同じデバイスを選択することも、別のデバイスを指定することもできます。 別のクラウド サービス プロバイダー (Azure 以外) に関連付けられたボリュームを選択する場合、ターゲット デバイスのドロップダウン リストには物理デバイスのみが表示されます。 別のクラウド サービスのプロバイダーに関連付けられたボリュームを仮想デバイス上に複製することはできません。
      
      > [!NOTE]
      > 複製に必要な容量がターゲット デバイスで使用可能な容量を下回っていることを確認してください。
      > 
      > 
   2. 複製の一意の [ボリューム名] を指定します。 名前は 3 文字以上 127 文字以下にする必要があります。 
      
      > [!NOTE]
      > ローカル固定ボリュームを複製する場合でも、**[種類を指定してボリュームをクローン]** フィールドは **[階層化]** になります。 この設定を変更することはできません。ただし、複製されたボリュームをローカル固定にする必要がある場合は、複製の作成が正常に完了した後でローカル固定ボリュームに変換できます。 階層化ボリュームをローカル固定ボリュームに変換する方法については、「 [ボリュームの種類を変更する](storsimple-manage-volumes-u2.md#change-the-volume-type)」を参照してください。
      > 
      > 
      
        ![複製ウィザード 1](./media/storsimple-clone-volume-u2/clone1.png) 
   3. 矢印アイコン  ![矢印アイコン](./media/storsimple-clone-volume-u2/HCS_ArrowIcon.png) をクリックして、次のページに進みます。
5. **[このボリュームを使用できるホストの指定]**で、次の手順を実行します。
   
   1. 複製のアクセス制御レコード (ACR) を指定します。 新しい ACR を追加することも、既存の一覧から選択することもできます。
      
        ![複製ウィザード 2](./media/storsimple-clone-volume-u2/clone2.png) 
   2. チェック マーク アイコン  ![チェック マーク アイコン](./media/storsimple-clone-volume-u2/HCS_CheckIcon.png)をクリックします。
6. 複製ジョブが開始され、ジョブが正常に作成されると通知が表示されます。 **[ジョブの表示]** をクリックすると **[ジョブ]** ページで複製ジョブを監視できます。 複製ジョブが終了すると、次のメッセージが表示されます。
   
    ![複製メッセージ](./media/storsimple-clone-volume-u2/CloneMsg.png) 
7. 複製ジョブが完了したら、次の手順を実行します。
   
   1. **[デバイス]** ページに移動し、**[ボリューム コンテナー]** タブを選択します。 
   2. 複製元のボリュームに関連付けられているボリューム コンテナーを選択します。 ボリュームの一覧に、先ほど作成した複製が表示されます。

> [!NOTE]
> 複製されたボリュームの監視と既定のバックアップは自動的に無効になっています。
> 
> 

このように作成された複製は一時的な複製です。 複製の種類の詳細については、「 [一時的な複製と永続的な複製](#transient-vs.-permanent-clones)」を参照してください。

この複製はこれで通常のボリュームとなり、ボリュームに対して実行できるすべての操作を利用できます。 バックアップを行う場合は、このボリュームを構成する必要があります。

## <a name="transient-vs-permanent-clones"></a>一時的な複製と永続的な複製
一時的な複製は、別のデバイスに複製する場合にのみ作成されます。 バックアップ セットの特定のボリュームを、StorSimple Manager で管理されている別のデバイスに複製できます。 一時的な複製は元のボリュームのデータを参照し、そのデータを使用して、ターゲット デバイスに対する読み取りおよび書き込みをローカルで行います。 

一時的な複製のクラウド スナップショットを作成すると、その複製は *永続的な* 複製になります。 このプロセスで、データのコピーがクラウドに作成されます。このデータがコピーされる時間は、データのサイズと、Azure の待機時間 (これは Azure 間でのコピーです) によって決まります。 このプロセスには数日から数週間かかることがあります。 このようにして、一時的な複製は永続的な複製となります。永続的な複製は、複製元のボリュームのデータを参照しません。 

## <a name="scenarios-for-transient-and-permanent-clones"></a>一時的な複製と永続的な複製のシナリオ
ここでは、一時的な複製と永続的な複製が使用される状況の例について説明します。

### <a name="item-level-recovery-with-a-transient-clone"></a>一時的な複製による項目レベルの回復
1 年前の PowerPoint プレゼンテーション ファイルを回復する場合を考えます。 IT 管理者はその期間の特定のバックアップを識別し、ボリュームをフィルター処理します。 次に管理者はボリュームを複製し、ユーザーが探している特定のファイルを見つけ、ユーザーに提供します。 このシナリオでは、一時的な複製を使用します。 

![ビデオ](./media/storsimple-clone-volume-u2/Video_icon.png) **ビデオ**

StorSimple の複製機能と復元機能を使用して、削除されたファイルを回復する方法を説明したビデオについては、 [こちら](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/)を参照してください。

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>永続的な複製による運用環境でのテスト
運用環境でテストのバグを確認する場合を考えます。 運用環境でボリュームの複製を作成し、この複製のクラウド スナップショットを作成して、複製された独立したボリュームを作成します。 このシナリオでは、永続的な複製を使用します。  

## <a name="next-steps"></a>次のステップ
* [バックアップ セットからの StorSimple ボリュームの復元方法](storsimple-restore-from-backup-set-u2.md)
* [StorSimple Manager サービスを使用した StorSimple デバイスの管理方法](storsimple-manager-service-administration.md)




<!--HONumber=Jan17_HO4-->


