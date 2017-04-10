---
title: "StorSimple EBOD コントローラーを交換する | Microsoft Docs"
description: "StorSimple 8600 デバイスの一方または両方の EBOD コントローラー モジュールを取り外して交換する方法について説明します。"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 8cbfa507-1a56-4e24-99dd-7db9abd3b850
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 23d819ddc3bbcbaf2847cdcc9191407ead0ff43d


---
# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>StorSimple デバイスの EBOD コントローラーを交換する
## <a name="overview"></a>Overview
このチュートリアルでは、Microsoft Azure StorSimple デバイスの障害が発生した EBOD コントローラー モジュールを交換する方法について説明します。 EBOD コントローラー モジュールを交換するには、次の操作を行う必要があります。

* 障害が発生している EBOD コントローラーを取り外す
* 新しい EBOD コントローラーを取り付ける

開始する前に、次の情報を考慮してください。

* すべての未使用スロットに空の EBOD モジュールを挿入する必要があります。 スロットを開いたままにすると、エンクロージャが適切に冷却されません。
* EBOD コントローラーはホットスワップ可能であり、取り外しと交換が可能です。 交換モジュールを用意するまで、障害が発生しているモジュールを取り外さないでください。 交換処理は、開始後 10 分以内に完了する必要があります。

> [!IMPORTANT]
> StorSimple のコンポーネントを取り外して交換する前に、[安全性アイコンの表記規則](storsimple-safety.md#safety-icon-conventions)とその他の[安全に関する注意事項](storsimple-safety.md)を必ず確認してください。
> 
> 

## <a name="remove-an-ebod-controller"></a>EBOD コントローラーを取り外す
StorSimple デバイスの障害が発生した EBOD コントローラー モジュールを交換する前に、その他の EBOD コントローラー モジュールがアクティブであり実行中であることを確認します。 次の手順と表で、EBOD コントローラー モジュールを取り外す方法について説明します。

#### <a name="to-remove-an-ebod-module"></a>EBOD モジュールを取り外すには
1. Azure クラシック ポータルを開きます。
2. **[デバイス]** > **[メンテナンス]** > **[ハードウェアの状態]** を選択し、アクティブな EBOD コントローラー モジュールの LED の状態が緑色であり、障害が発生している EBOD コントローラー モジュールの LED が赤色になっていることを確認します。
3. デバイスの背面で、障害が発生している EBOD コントローラー モジュールを見つけます。
4. EBOD モジュールをシステムから取り出す前に、EBOD コントローラー モジュールをコントローラーに接続しているケーブルを外します。
5. コントローラーに接続されていた EBOD コントローラー モジュールの正確な SAS ポートをメモしておきます。 EBOD モジュールを交換した後、システムをこの構成に復元する必要があります。 
   
   > [!NOTE]
   > 通常、これはポート A になり、次の図に示す **ホスト イン** になります。
   > 
   > 
   
    ![Backplane of EBOD controller](./media/storsimple-ebod-controller-replacement/IC741049.png)
   
     **図 1** EBOD モジュールの背面
   
   | ラベル | 説明 |
   |:--- |:--- |
   | 1 |障害 LED |
   | 2 |電源 LED |
   | 3 |SAS コネクタ |
   | 4 |SAS LED |
   | 5 |工場使用専用シリアル ポート |
   | 6 |ポート A (ホスト イン) |
   | 7 |ポート B (ホスト アウト) |
   | 8 |ポート C (工場使用専用) |

## <a name="install-a-new-ebod-controller"></a>新しい EBOD コントローラーを取り付ける
次の手順と表で、StorSimple デバイスに EBOD コントローラー モジュールを取り付ける方法について説明します。

#### <a name="to-install-an-ebod-controller"></a>EBOD コントローラーを取り付けるには
1. EBOD デバイスが破損していないことを確認します。特にインターフェイス コネクタが破損していないことを確認します。 新しい EBOD コントローラーのピンが曲がっている場合は取り付けないでください。
2. ラッチが開いた状態でモジュールをエンクロージャに差し込み、ラッチをかけられる状態になるまで押し込みます。
   
    ![Installing EBOD controller](./media/storsimple-ebod-controller-replacement/IC741050.png)
   
    **図 2** EBOD コントローラー モジュールを取り付ける
3. ラッチを閉じます。 ラッチがかかると、カチッと音がします。
   
    ![Releasing EBOD latch](./media/storsimple-ebod-controller-replacement/IC741047.png)
   
    **図 3** EBOD モジュールのラッチを閉じる
4. ケーブルを再び接続します。 交換前と完全に同じ構成を使用します。 次の図と表で、ケーブルをどのように接続するかについての詳細を参照してください。
   
    ![4U デバイスの電源ケーブル接続](./media/storsimple-ebod-controller-replacement/IC770723.png)
   
    **図 4**  ケーブルの再接続
   
   | ラベル | 説明 |
   |:--- |:--- |
   | 1 |主エンクロージャ |
   | 2 |PCM 0 |
   | 3 |PCM 1 |
   | 4 |コントローラー 0 |
   | 5 |コントローラー 1 |
   | 6 |EBOD コント ローラー 0 |
   | 7 |EBOD コント ローラー 1 |
   | 8 |EBOD エンクロージャ |
   | 9 |電力配分装置 |

## <a name="next-steps"></a>次のステップ
「 [StorSimple ハードウェア コンポーネントの交換](storsimple-hardware-component-replacement.md)」の説明を参照してください。




<!--HONumber=Nov16_HO3-->


