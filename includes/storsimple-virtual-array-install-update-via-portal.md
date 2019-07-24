---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 37d4c295fe8a89a8b32364f42e8fb4c5a4caf777
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55889549"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Azure Portal を使用して更新プログラムをインストールするには

1. StorSimple デバイス マネージャーに移動し、**[デバイス]** を選択します。 サービスに接続されているデバイスの一覧から、更新するデバイスを選択してクリックします。 

    ![デバイスの更新](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate1m.png) 

2. **[設定]** ブレードで **[デバイスの更新プログラム]** をクリックします。 

    ![デバイスの更新](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate2m.png)  

3. ソフトウェアの更新プログラムが利用可能な場合は、メッセージが表示されます。 更新プログラムを確認するには、**[スキャン]** をクリックします。

    ![デバイスの更新](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate3m.png)

    スキャンが開始され、正常に完了すると、その旨が通知されます。

    ![デバイスの更新](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate5m.png)

4. 更新プログラムのスキャンが完了したら、**[更新プログラムのダウンロード]** をクリックします。 

    ![デバイスの更新](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate6m.png)

5. **[新しい更新プログラム]** ブレードで、"更新プログラムをダウンロードしたら、インストールを確認する必要があります" という情報を確認します。 Click **OK**.

    ![デバイスの更新](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate7m.png)

6. アップロードが開始され、正常に完了すると、その旨が通知されます。

     ![デバイスの更新](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate8m.png)

5. **[デバイスの更新プログラム]** ブレードで **[インストール]** をクリックします。

     ![デバイスの更新](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate11m.png)   

6. **[新しい更新プログラム]** ブレードで、更新には中断が伴うという警告が表示されます。 仮想アレイが単一ノード デバイスであるため、更新された後にデバイスが再起動されます。 これにより、進行中の IO が中断されます。 **[OK]** をクリックして、更新プログラムをインストールします。 

    ![デバイスの更新](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate12m.png) 

7. インストール ジョブの開始されると、その旨が通知されます。 

    ![デバイスの更新](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate13m.png)

8.  インストール ジョブが正常に完了したら、**[デバイスの更新プログラム]** ブレードの **[ジョブの表示]** リンクをクリックして、インストールを監視します。 

    ![デバイスの更新](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate15m.png)

    **[更新プログラムのインストール]** ブレードに移動します。 ここでは、ジョブの詳細情報を確認できます。

    ![デバイスの更新](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate16m.png)

9. 更新プログラムが正常にインストールされると、その旨のメッセージが [デバイスの更新プログラム] ブレードに表示されます。 ソフトウェアのバージョンも **10.0.10288.0** に変更されます。 

    ![デバイスの更新](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate17m.png)