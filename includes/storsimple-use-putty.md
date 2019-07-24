---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: a5c62c67cd401c043352b06e6e6070a7fc0f1296
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55889184"
---
#### <a name="to-connect-through-the-serial-console"></a>シリアル コンソールを使用して接続するには
1. シリアル ケーブルをデバイスに接続します (直接、または USB シリアル アダプターを使用)。
2. **コントロール パネル**を開き、**[デバイス マネージャー]** を開きます。
3. 次の図に示すように COM ポートを確認します。
   
     ![シリアル コンソールを使用した接続](./media/storsimple-use-putty/HCS_ConnectingDeviceS-include.png)
4. PuTTY を起動します。 
5. 右側のウィンドウで、**[接続の種類]** を **[シリアル]** に変更します。
6. 右側のウィンドウで、適切な COM ポートを入力します。 シリアル構成パラメーターが次のように設定されていることを確認します。
   
   * 速度:115,200
   * データ ビット:8
   * ストップ ビット:1
   * パリティ:なし
   * フロー制御:なし
     
     これらの設定を次の図に示します。
     
     ![PuTTY 設定](./media/storsimple-use-putty/HCS_PuttyConfig-include.png) 
     
     > [!NOTE]
     > 既定のフロー制御の設定が動作しない場合は、フロー制御を [XON/XOFF] に設定してみてください。
     > 
     > 
7. **[開く]** をクリックして、シリアル セッションを開始します。

