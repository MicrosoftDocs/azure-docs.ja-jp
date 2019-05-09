---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: b4c3fcb86fb098263840accc561785a40b767952
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55888562"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Azure Portal を使用して更新プログラムをインストールするには

1. StorSimple デバイス マネージャーに移動し、**[デバイス]** を選択します。 サービスに接続されているデバイスの一覧から、更新するデバイスを選択してクリックします。

    ![デバイスの更新](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. **[設定]** ブレードで **[デバイスの更新プログラム]** をクリックします。

    ![デバイスの更新](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. ソフトウェアの更新プログラムが利用可能な場合は、メッセージが表示されます。 更新プログラムを確認するには、**[スキャン]** をクリックします。 実行しているソフトウェアのバージョンをメモしておきます。 

    ![デバイスの更新](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate3m1.png)

    スキャンが開始され、正常に完了すると、その旨が通知されます。

    ![デバイスの更新](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate5m.png)

4. 更新プログラムのスキャンが完了したら、**[更新プログラムのダウンロード]** をクリックします。

    ![デバイスの更新](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate6m.png)

5. **[新しい更新プログラム]** ブレードで、リリース ノートを確認します。 更新プログラムをダウンロードしたら、インストールを確認する必要があることにもご注意ください。 Click **OK**.

    ![デバイスの更新](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate7m.png)

6. アップロードが開始され、正常に完了すると、その旨が通知されます。

     ![デバイスの更新](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate8m.png)

5. **[デバイスの更新プログラム]** ブレードで **[インストール]** をクリックします。

     ![デバイスの更新](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate11m1.png)

6. **[新しい更新プログラム]** ブレードで、更新には中断が伴うという警告が表示されます。 仮想アレイが単一ノード デバイスであるため、更新された後にデバイスが再起動されます。 これにより、進行中の IO が中断されます。 **[OK]** をクリックして、更新プログラムをインストールします。

    ![デバイスの更新](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate12m.png)

7. インストール ジョブの開始されると、その旨が通知されます。

    ![デバイスの更新](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate13m.png)

8.  インストール ジョブが正常に完了したら、**[デバイスの更新プログラム]** ブレードの **[ジョブの表示]** リンクをクリックして、インストールを監視します。 

    ![デバイスの更新](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate15m1.png)

    **[更新プログラムのインストール]** ブレードに移動します。 ここでは、ジョブの詳細情報を確認できます。

    ![デバイスの更新](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate16m1.png)

9. 更新プログラム 0.6 (10.0.10293.0) のソフトウェア バージョンを実行している仮想アレイで開始した場合は、この時点で更新プログラム 1 が実行されており、アップデートは完了となります。 残りの手順を省略できます。 更新プログラム 0.6 (10.0.10293.0) より前のソフトウェア バージョンを実行している仮想アレイで開始した場合、この時点で更新プログラム 0.6 に更新されます。 更新プログラムが利用できることを示すメッセージがまた表示されます。 手順 4 - 8 を繰り返して更新プログラム 1 をインストールします。

    ![デバイスの更新](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate17.png)

