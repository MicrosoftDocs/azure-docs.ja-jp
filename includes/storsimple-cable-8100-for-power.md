---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: db2104020e9478b1fedf68e1c9467f75e16044e2
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55889285"
---
#### <a name="to-cable-for-power"></a>電源ケーブル接続を行うには
1. 各 PCM (電源および冷却モジュール) の電源スイッチが OFF の位置になっていることを確認します。
2. 主エンクロージャの各 PCM に電源コードを接続します。
3. 次の図に示すように、ラックの配電ユニット (PDU) に電源コードを接続します。 2 つの PCM が別の電源を使用していることを確認します。
   
   > [!IMPORTANT]
   > システムの高可用性を確保するために、以下の図に示されている電源ケーブル接続構成に厳密に従うことをお勧めします。 
   > 
   > 
   
    ![2U デバイスの電源ケーブル接続](./media/storsimple-cable-8100-for-power/HCSCableYour2UDeviceforPower.png)
   
    **8100 デバイスの電源ケーブル接続**
   
   | Label | 説明 |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |コントローラー 1 |
   | 3 |コントローラー 0 |
   | 4 |PCM 1 |
   | 5 |PDU |
4. システムの電源を入れるには、両方の PCM の電源スイッチを ON の位置に切り替えます。

