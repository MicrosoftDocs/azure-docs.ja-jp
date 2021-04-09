---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 3129bbe171329ecf37f8712394cedf9b70188220
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005820"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Azure Portal を使用して更新プログラムをインストールするには

1. StorSimple デバイス マネージャーに移動し、 **[デバイス]** を選択します。 サービスに接続されているデバイスの一覧から、更新するデバイスを選択してクリックします。

    ![[最近] で、デバイス マネージャー MySSDevManager が強調表示されて選択され、[デバイス] が強調表示されて選択され、デバイスの MYSSIS1103 が強調表示されて選択されています。](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. **[設定]** ブレードで **[デバイスの更新プログラム]** をクリックします。

    ![MYSSIS1103 の情報が表示されます。 [設定] で、[デバイスの更新プログラム] が強調表示されています。](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. ソフトウェアの更新プログラムが利用可能な場合は、メッセージが表示されます。 更新プログラムを確認するには、 **[スキャン]** をクリックします。 実行しているソフトウェアのバージョンをメモしておきます。 

    ![[デバイスの更新プログラム] ウィンドウに、"新しい更新プログラムを利用できます"、および "最終スキャン日時 / 11/01/2017 10:56 AM / ソフトウェア バージョン / 10.0.10289.0" と表示されています。 バージョンが強調表示されます。](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate3m1.png)

    スキャンが開始され、正常に完了すると、その旨が通知されます。

    ![通知には次のように表示されます: "デバイス MySVAFS110 の更新プログラムをスキャンしています。 10:57 AM / 操作が進行中です。"](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate5m.png)

4. 更新プログラムのスキャンが完了したら、 **[更新プログラムのダウンロード]** をクリックします。

    ![[デバイスの更新プログラム] ウィンドウに、"新しい更新プログラムを利用できます" というメッセージが表示されます。 [更新プログラムのダウンロード] ボタンが強調表示されます。](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate6m.png)

5. **[新しい更新プログラム]** ブレードで、リリース ノートを確認します。 更新プログラムをダウンロードしたら、インストールを確認する必要があることにもご注意ください。 **[OK]** をクリックします。

    ![[新しい更新プログラム] ウィンドウに "更新プログラムをダウンロードしたら、インストールを確認する必要があります。" という内容が表示されています。 [OK] ボタンが強調表示されています。](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate7m.png)

6. アップロードが開始され、正常に完了すると、その旨が通知されます。

     ![通知には次のように表示されます: "デバイス MySVAFS の更新プログラムをダウンロードしています。 11:07 AM"。](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate8m.png)

5. **[デバイスの更新プログラム]** ブレードで **[インストール]** をクリックします。

     ![[デバイスの更新プログラム] に、"更新プログラムのインストールを確認してください" と表示されます。 [インストール] ボタンが強調表示されます。](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate11m1.png)

6. **[新しい更新プログラム]** ブレードで、更新には中断が伴うという警告が表示されます。 仮想アレイが単一ノード デバイスであるため、更新された後にデバイスが再起動されます。 これにより、進行中の IO が中断されます。 **[OK]** をクリックして、更新プログラムをインストールします。

    ![[新しい更新プログラム] ウィンドウのメッセージは、"デバイスでは、これらの更新プログラムのインストール時にダウンタイムが発生します。" です。 [OK] ボタンが強調表示されています。](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate12m.png)

7. インストール ジョブの開始されると、その旨が通知されます。

    ![通知には次のように表示されます: "デバイス MySVAFS110 の更新プログラムをインストールしています。 11:09 AM"。](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate13m.png)

8.  インストール ジョブが正常に完了したら、 **[デバイスの更新プログラム]** ブレードの **[ジョブの表示]** リンクをクリックして、インストールを監視します。 

    ![[デバイスの更新プログラム] ウィンドウで、次のようなラベルのリンクが表示されます: "更新プログラムをインストールしています。 ジョブの表示"。 [インストール] ボタンが強調表示されます。](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate15m1.png)

    **[更新プログラムのインストール]** ブレードに移動します。 ここでは、ジョブの詳細情報を確認できます。

    ![[更新プログラムのインストール] ウィンドウに、デバイス、状態、期間、開始時刻、停止時刻などのインストール情報が表示されています。](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate16m1.png)

9. 更新プログラム 0.6 (10.0.10293.0) のソフトウェア バージョンを実行している仮想アレイで開始した場合は、この時点で更新プログラム 1 が実行されており、アップデートは完了となります。 残りの手順を省略できます。 更新プログラム 0.6 (10.0.10293.0) より前のソフトウェア バージョンを実行している仮想アレイで開始した場合、この時点で更新プログラム 0.6 に更新されます。 更新プログラムが利用できることを示すメッセージがまた表示されます。 手順 4 - 8 を繰り返して更新プログラム 1 をインストールします。

    ![[デバイスの更新プログラム] ウィンドウに、"新しい更新プログラムを利用できます"、および "最終スキャン日時 / 11/1/2017 10:56 AM / ソフトウェア バージョン / 10.0.10293.0" と表示されています。](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate17.png)

