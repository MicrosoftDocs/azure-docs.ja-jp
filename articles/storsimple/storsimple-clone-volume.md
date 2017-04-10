---
title: "StorSimple ボリュームの複製 | Microsoft Docs"
description: "異なる複製の種類と、それらをどのような場合に使用するかについて説明します。また、バックアップ セットを使用して個々のボリュームを複製する方法についても説明します。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: b5d615f2-02a7-4222-9867-6c0385ce748c
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: edaacfb8e7c179ac6e4831a113656b3cf128f985


---
# <a name="use-the-storsimple-manager-service-to-clone-a-volume"></a>StorSimple Manager サービスを使用してボリュームを複製する
[!INCLUDE [storsimple-version-selector-clone-volume](../../includes/storsimple-version-selector-clone-volume.md)]

## <a name="overview"></a>Overview
StorSimple Manager サービスの **[バックアップ カタログ]** ページには、手動バックアップまたは自動バックアップを実行したときに作成されたすべてのバックアップ セットが表示されます。 このページを使用すると、バックアップ ポリシーまたはボリュームのすべてのバックアップを一覧表示したり、バックアップを選択または削除したりできます。また、バックアップを使用してボリュームを復元または複製することもできます。

![[バックアップ カタログ] ページ](./media/storsimple-clone-volume/HCS_BackupCatalog.png)  

このチュートリアルでは、バックアップ セットを使用して個々のボリュームを複製する方法について説明します。 また、*一時的な*複製と*永続的な*複製の違いについても説明します。 

## <a name="create-a-clone-of-a-volume"></a>ボリュームの複製を作成する
複製は、ローカル スナップショットまたはクラウド スナップショットを使用して、同じデバイスや別のデバイスに作成できるほか、仮想マシンに作成することもできます。

#### <a name="to-clone-a-volume"></a>ボリュームを複製するには
1. StorSimple Manager サービスのページで、 **[バックアップ カタログ]** タブをクリックし、バックアップ セットを選択します。
2. バックアップ セットを展開して、関連付けられているボリュームを表示します。 バックアップ セットからボリュームをクリックして選択します。
   
     ![ボリュームを複製する](./media/storsimple-clone-volume/HCS_Clone.png) 
3. **[複製]** をクリックして、選択したボリュームの複製を開始します。
4. ボリュームの複製ウィザードの **[名前と場所の指定]**で、次の手順を実行します。
   
   1. ターゲット デバイスを指定します。 これは、複製が作成される場所です。 同じデバイスを選択することも、別のデバイスを指定することもできます。 別のクラウド サービス プロバイダー (Azure 以外) に関連付けられたボリュームを選択する場合、ターゲット デバイスのドロップダウン リストには物理デバイスのみが表示されます。 別のクラウド サービスのプロバイダーに関連付けられたボリュームを仮想デバイス上に複製することはできません。
      
      > [!NOTE]
      > 複製に必要な容量がターゲット デバイスで使用可能な容量を下回っていることを確認してください。
      > 
      > 
   2. 複製の一意の [ボリューム名] を指定します。 名前は 3 文字以上 127 文字以下にする必要があります。
   3. 矢印アイコン  ![矢印アイコン](./media/storsimple-clone-volume/HCS_ArrowIcon.png)  をクリックして、次のページに進みます。
5. **[このボリュームを使用できるホストの指定]**で、次の手順を実行します。
   
   1. 複製のアクセス制御レコード (ACR) を指定します。 新しい ACR を追加することも、既存の一覧から選択することもできます。
   2. 操作を完了するには、チェック マーク アイコン  ![チェック マーク アイコン](./media/storsimple-clone-volume/HCS_CheckIcon.png) をクリックします。
6. 複製ジョブが開始され、ジョブが正常に作成されると通知が表示されます。 **[ジョブの表示]** をクリックすると **[ジョブ]** ページで複製ジョブを監視できます。
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
一時的な複製と永続的な複製は、別のデバイスに複製する場合にのみ作成されます。 バックアップ セットの特定のボリュームを別のデバイスに複製できます。 このように作成された複製は *一時的な* 複製です。 一時的な複製は元のボリュームを参照し、そのボリュームを使用して読み取りを行いますが、書き込みはローカルで行います。 

一時的な複製のクラウド スナップショットを作成すると、その複製は *永続的な* 複製になります。 永続的な複製は独立していて、複製元のボリュームを参照することはありません。  

## <a name="scenarios-for-transient-and-permanent-clones"></a>一時的な複製と永続的な複製のシナリオ
ここでは、一時的な複製と永続的な複製が使用される状況の例について説明します。

### <a name="item-level-recovery-with-a-transient-clone"></a>一時的な複製による項目レベルの回復
1 年前の PowerPoint プレゼンテーション ファイルを回復する場合を考えます。 IT 管理者はその期間の特定のバックアップを識別し、ボリュームをフィルター処理します。 次に管理者はボリュームを複製し、ユーザーが探している特定のファイルを見つけ、ユーザーに提供します。 このシナリオでは、一時的な複製を使用します。 

![ビデオ](./media/storsimple-clone-volume/Video_icon.png) **ビデオ**

StorSimple の複製機能と復元機能を使用して、削除されたファイルを回復する方法を説明したビデオについては、 [こちら](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/)を参照してください。

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>永続的な複製による運用環境でのテスト
運用環境でテストのバグを確認する場合を考えます。 この複製のクラウド スナップショットを作成して、運用環境でボリュームの複製を作成します。 複製されたボリュームは独立しています。 このシナリオでは、永続的な複製を使用します。

## <a name="next-steps"></a>次のステップ
* [バックアップ セットからの StorSimple ボリュームの復元方法](storsimple-restore-from-backup-set.md)
* [StorSimple Manager サービスを使用した StorSimple デバイスの管理方法](storsimple-manager-service-administration.md)




<!--HONumber=Nov16_HO3-->


