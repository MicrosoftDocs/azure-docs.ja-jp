---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 71f18cf8448060385ea38be9b2719b1ed545c5d2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100545420"
---
> [!NOTE] 
> 暗号化の設定と、ボリューム コンテナーに関連付けられているストレージ アカウントの資格情報は、ボリューム コンテナーの作成後に変更することはできません。

#### <a name="to-modify-a-volume-container"></a>ボリューム コンテナーを変更するには

1. StorSimple デバイス マネージャー サービスに移動し、 **[管理]、[ボリューム コンテナー]** の順に移動します。

2. ボリューム コンテナーの表形式の一覧で、変更するボリューム コンテナーを選択します。 **[デバイス]** ページでデバイスを選択し、ダブルクリックします。次に、 **[ボリューム コンテナー]** タブをクリックします。

3. ボリューム コンテナーの表形式の一覧で、変更するボリューム コンテナーを選択します。 表示されたブレードで、コマンド バーから **[変更]** をクリックします。

    ![ボリューム コンテナーの変更](./media/storsimple-8000-modify-volume-container/modify-volume-container-01.png)

4. **[ボリューム コンテナーの変更]** ブレードで、次の操作を行います。
   
   1. ボリューム コンテナーに関連付けられている名前、暗号化キー、ストレージ アカウントは、指定後に変更することはできません。 関連付けられている帯域幅の設定を変更します。<!--STEPS NEED WORK. Updated screen doesn't show alternative to Unlimited or subsequent steps if they customize bandwidth. Can we talk them through this (briefly)?-->
      
       ![帯域幅の設定の変更](./media/storsimple-8000-modify-volume-container/modify-volume-container-02.png)<!--New graphic based on: modify-volume-container-bw-setting.png-->

   1.  **[OK]** をクリックします。<!--If they choose Custom, do they still click OK, or are there more steps?-->

5. **[ボリューム コンテナーの変更]** ダイアログ ボックスの次のページで、次の操作を行います。<!--This step happens only if they choose Custom bandwidth? Are the steps similar to those in "Add volume container," step 3f, above?"-->
   
   1. ドロップダウン リストから、既存の帯域幅テンプレートを選択します。
   1. 指定した帯域幅テンプレートのスケジュール設定を確認します。
   1. **[保存]** をクリックして変更を確定します。
      
       ![変更の確定](./media/storsimple-8000-modify-volume-container/modify-volume-container-03.png)

      **[ボリューム コンテナー]** ブレードが更新され、変更が反映されます。
