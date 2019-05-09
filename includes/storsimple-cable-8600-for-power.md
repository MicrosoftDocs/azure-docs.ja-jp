---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9b9922602218280d58331a755ed0dfed7df96f40
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55889348"
---
#### <a name="to-cable-your-device-for-power"></a>デバイスの電源ケーブル接続方法
> [!NOTE]
> StorSimple デバイスの両方のエンクロージャには冗長な PCM があります。 両方のエンクロージャで高可用性を確保するには、両方の PCM を取り付けて、異なる電源に接続する必要があります。
> 
> 

1. すべての PCM の電源スイッチが OFF の位置になっていることを確認します。
2. 主エンクロージャの両方の PCM に電源コードを接続します。 以下の図では、電源コードが赤で示されています。
3. 主エンクロージャの 2 つの PCM が異なる電源を使用するようにします。
4. 電源ケーブルの接続図に示すように、ラックの配電ユニットに電源コードを接続します。
5. EBOD エンクロージャについて、ステップ 2 から 4 までを繰り返します。
6. 各 PCM の電源スイッチを ON の位置に切り替えて、EBOD エンクロージャの電源を入れます。
7. EBOD コントローラーの背面の緑の LED が ON になっていることを確認して、EBOD エンクロージャの電源が入っていることを確認します。
8. 各 PCM のスイッチを ON の位置に切り替えて、主エンクロージャの電源を入れます。
9. デバイス コントローラーの LED が ON になっていることを確認して、システムが稼動していることを確認します。
10. EBOD コントローラーの SAS ポートの隣の 4 個の LED を確認して、EBOD コントローラーとデバイス コントローラーの間の接続がアクティブであることを確認します。
    
    > [!IMPORTANT]
    > システムの高可用性を確保するために、以下の図に示されている電源ケーブル接続構成に厳密に従うことをお勧めします。
    > 
    > 
    
    ![4U デバイスの電源ケーブル接続](./media/storsimple-cable-8600-for-power/HCSCableYour4UDeviceforPower.png)
    
    **電源ケーブル接続**
    
    | Label | 説明 |
    |:--- |:--- |
    | 1 |主エンクロージャ |
    | 2 |PCM 0 |
    | 3 |PCM 1 |
    | 4 |コントローラー 0 |
    | 5 |コントローラー 1 |
    | 6 |EBOD コント ローラー 0 |
    | 7 |EBOD コント ローラー 1 |
    | 8 |EBOD エンクロージャ |
    | 9 |PDU |

