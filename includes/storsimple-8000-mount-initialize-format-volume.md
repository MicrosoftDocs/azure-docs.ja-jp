---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: ba2985b8b6c92e299e8ab378263c9b4c062561d5
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55888759"
---
#### <a name="to-mount-initialize-and-format-a-volume"></a>ボリュームをマウント、初期化、フォーマットするには
1. Microsoft iSCSI イニシエーターを開始します。
2. **[iSCSI イニシエーターのプロパティ]** ウィンドウの **[探索]** タブで、**[ポータルの探索]** をクリックします。
3. **[ターゲット ポータルの探索]** ダイアログ ボックスで、iSCSI 対応ネットワーク インターフェイスの IP アドレスを入力し、**[OK]** をクリックします。 
4. **[iSCSI イニシエーターのプロパティ]** ウィンドウの **[ターゲット]** タブで、**[検出されたターゲット]** を見つけます。 デバイスの状態が **[非アクティブ]** になっています。
5. ターゲット デバイスを選択し、**[接続]** をクリックします。 デバイスが接続されると、状態が **[接続]** に変わります  (Microsoft iSCSI イニシエーターの使用方法の詳細については、「[Installing and Configuring Microsoft iSCSI Initiator (Microsoft iSCSI イニシエーターのインストールと構成)][1]」をご覧ください)。
6. Windows ホスト上で、Windows ロゴ キーを押しながら X キーを押し、 **[ファイル名を指定して実行]** をクリックします。 
7. **[ファイル名を指定して実行]** ダイアログ ボックスに、「**Diskmgmt.msc**」と入力します。 **[OK]** をクリックすると、**[ディスクの管理]** ダイアログ ボックスが表示されます。 右側のウィンドウに、ホスト上のボリュームが表示されます。
8. 次の図に示すように、マウントされているボリュームが **[ディスクの管理]** ウィンドウに表示されます。 検出されたボリュームを右クリックし (ディスク名をクリック)、 **[オンライン]** をクリックします。
   
     ![ボリュームの初期化とフォーマット](./media/storsimple-8000-mount-initialize-format-volume/step7initializeformatvolume.png) 
9. もう一度ボリュームを右クリックし (ディスク名をクリック)、 **[初期化]** をクリックします。
10. シンプル ボリュームをフォーマットするには、次の手順を実行します。
    
    1. ボリュームを選択して右クリックし (右側の領域をクリック)、 **[新しいシンプル ボリューム]** をクリックします。
    2. 新しいシンプル ボリューム ウィザードで、ボリュームのサイズとドライブの文字を指定し、このボリュームを NTFS ファイル システムとして構成します。
    3. 64 KB アロケーション ユニット サイズを指定します。 このアロケーション ユニット サイズは、StorSimple ソリューションで使用されている重複除去アルゴリズムに適しています。
    4. クイック フォーマットを実行します。

<!--Link references-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx
