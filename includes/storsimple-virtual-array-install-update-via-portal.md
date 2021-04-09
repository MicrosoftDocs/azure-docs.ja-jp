---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: df572b88cf8a67163b28ec87154dcea01646b9a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95562618"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Azure Portal を使用して更新プログラムをインストールするには

1. StorSimple デバイス マネージャーに移動し、 **[デバイス]** を選択します。 サービスに接続されているデバイスの一覧から、更新するデバイスを選択してクリックします。 

    ![[最近] で、デバイス マネージャー MySSDevManager が強調表示されて選択され、[デバイス] が強調表示されて選択され、デバイスの MYSSIS1103 が強調表示されて選択されています。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate1m.png) 

2. **[設定]** ブレードで **[デバイスの更新プログラム]** をクリックします。 

    ![MYSSIS1103 の情報が表示されます。 [設定] で、[デバイスの更新プログラム] が強調表示されています。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate2m.png)  

3. ソフトウェアの更新プログラムが利用可能な場合は、メッセージが表示されます。 更新プログラムを確認するには、 **[スキャン]** をクリックします。

    ![[デバイスの更新プログラム] ウィンドウの [スキャン] ボタンが強調表示されています。 以下の 4 行のメッセージが表示されています。最終スキャン日時 / スキャンなし / ソフトウェア バージョン / 10.0.10280.0。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate3m.png)

    スキャンが開始され、正常に完了すると、その旨が通知されます。

    ![通知には、"デバイス MySSIS1103.2:12 PM の更新プログラムをスキャンしています / 操作は正常に完了しました" というメッセージが表示されています。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate5m.png)

4. 更新プログラムのスキャンが完了したら、 **[更新プログラムのダウンロード]** をクリックします。 

    ![[デバイスの更新プログラム] ウィンドウに、"新しい更新プログラムを利用できます"、および "最終スキャン日 / 11/3/2016 2:12 PM / ソフトウェア バージョン / 10.0.10280.0" と表示されています。 バージョンが強調表示されています。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate6m.png)

5. **[新しい更新プログラム]** ブレードで、"更新プログラムをダウンロードしたら、インストールを確認する必要があります" という情報を確認します。 **[OK]** をクリックします。

    ![[新しい更新プログラム] ウィンドウに "更新プログラムをダウンロードしたら、インストールを確認する必要があります。" という内容が表示されています。 [OK] ボタンが強調表示されています。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate7m.png)

6. アップロードが開始され、正常に完了すると、その旨が通知されます。

     ![通知には "Downloading updates for device MySSIS… 2:13 PM" と表示されています。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate8m.png)

5. **[デバイスの更新プログラム]** ブレードで **[インストール]** をクリックします。

     ![[デバイスの更新プログラム] ウィンドウに、"更新プログラムのインストールを確認してください" というメッセージが表示されています。 [インストール] ボタンが強調表示されています。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate11m.png)   

6. **[新しい更新プログラム]** ブレードで、更新には中断が伴うという警告が表示されます。 仮想アレイが単一ノード デバイスであるため、更新された後にデバイスが再起動されます。 これにより、進行中の IO が中断されます。 **[OK]** をクリックして、更新プログラムをインストールします。 

    ![[新しい更新プログラム] ウィンドウのメッセージは、"デバイスでは、これらの更新プログラムのインストール時にダウンタイムが発生します。" です。 [OK] ボタンが強調表示されています。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate12m.png) 

7. インストール ジョブの開始されると、その旨が通知されます。 

    ![通知には "デバイス MYSSIS1103 の更新プログラムをインストールしています"。 2:15 PM と表示されています。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate13m.png)

8.  インストール ジョブが正常に完了したら、 **[デバイスの更新プログラム]** ブレードの **[ジョブの表示]** リンクをクリックして、インストールを監視します。 

    ![[デバイスの更新プログラム] ウィンドウで、"更新プログラムをインストールしています。 ジョブの表示" というラベルのリンクが表示されています。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate15m.png)

    **[更新プログラムのインストール]** ブレードに移動します。 ここでは、ジョブの詳細情報を確認できます。

    ![[更新プログラムのインストール] ウィンドウに、デバイス、状態、期間、開始時刻、停止時刻などのインストール情報が表示されています。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate16m.png)

9. 更新プログラムが正常にインストールされると、その旨のメッセージが [デバイスの更新プログラム] ブレードに表示されます。 ソフトウェアのバージョンも **10.0.10288.0** に変更されます。 

    ![[デバイスの更新プログラム] ウィンドウに、"ご利用のデバイスは最新の状態です" と、ソフトウェアのバージョン (10.0.10288.0) が表示されています。](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate17m.png)
