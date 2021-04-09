---
title: Azure Percept DK にシリアルで接続する
description: PuTTY と USB to TTL シリアル ケーブルを使用して、Azure Percept DK へのシリアル接続を設定する方法について説明します
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 93b8ab0ce53202402e86b059abe3c600590d549e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101660538"
---
# <a name="connect-to-your-azure-percept-dk-over-serial"></a>Azure Percept DK にシリアルで接続する

[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) から Azure Percept DK へのシリアル接続を設定するには、次の手順に従います。

> [!WARNING]
> 極端な障害 (デバイスが使用不能になるなど) の場合を除き、シリアルで開発キットを接続しようと **しないで** ください。 シリアル ケーブルを接続するためにキャリア ボードの格納装置を取り外すことは非常に困難であり、Wi-Fi アンテナ ケーブルが破損します。

## <a name="prerequisites"></a>前提条件

- [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
- ホスト PC
- Azure Percept DK
- [USB to TTL シリアル ケーブル](https://www.adafruit.com/product/954)

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/usb-serial-cable.png" alt-text="USB to TTL シリアル ケーブル。":::

## <a name="initiate-the-serial-connection"></a>シリアル接続の開始

1. キャリア ボードが 80/20 レールに接続されている場合は、16 進値キー (開発キットのウェルカム カードに含まれています) を使用してレールから取り外します。

1. キャリア ボードの格納装置の底面にあるネジを取り外し、マザーボードを引き出します。

    > [!WARNING]
    > マザーボードを取り外すと、Wi-Fi アンテナ ケーブルが破損します。 デバイスを復旧する手段が他にない場合以外は、シリアル接続を続行 **しないで** ください。

1. ヒートシンクを取り外します。

1. GPIO ピンからジャンパー ボードを取り外します。

    > [!TIP]
    > ジャンパー ボードを取り外す前に、その向きを書き留めておきます。 たとえば、回路の方を指すように、ジャンパー ボードに矢印を描いておくか、ステッカーを貼っておきます。 ジャンパー ボードは方向が固定されておらず、キャリア ボードを組み立て直すときに誤って逆に接続してしまう場合があります。

1. 次に示すように、[USB to TTL シリアル ケーブル](https://www.adafruit.com/product/954)をマザーボードの GPIO ピンに接続します。 赤いワイヤーは接続されていないことに注意してください。

    - 黒いケーブル (GND) をピン 6 に接続します。
    - 白いケーブル (RX) をピン 8 に接続します。
    - 緑のケーブル (TX) をピン 10 に接続します。

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/serial-connection-carrier-board.png" alt-text="キャリア ボードのシリアル ピンの接続。":::

1. 開発キットの電源を入れ、シリアル ケーブルの USB 側を PC に接続します。

1. Windows で、 **[スタート]**  ->  **[Windows Update の設定]**  ->  **[View optional updates]\(オプションの更新プログラムの表示\)**  ->  **[ドライバー更新プログラム]** の順に移動します。 一覧でシリアルから USB への更新プログラムを探し、その横にあるチェック ボックスをオンにして、 **[ダウンロードしてインストール]** をクリックします。  

1. 次に、Windows デバイス マネージャーを開きます ( **[スタート]**  ->  **[デバイス マネージャー]** )。 **[ポート]** に移動し、 **[USB to UART]\(USB から UART へ\)** をクリックして、 **[プロパティ]** を開きます。 デバイスが接続されている COM ポートを書き留めておきます。

1. **[ポートの設定]** タブをクリックします。 **[bps]** が 115200 に設定されていることを確認します。

1. PuTTY を開きます。 次のように入力し、 **[開く]** をクリックして、シリアルで開発キットに接続します。

    1. シリアル回線: COM [ポート番号]
    1. 速度: 115200
    1. 接続の種類: シリアル

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/putty-serial-session.png" alt-text="シリアル パラメーターが選択された PuTTY セッション ウィンドウ。":::

## <a name="next-steps"></a>次のステップ

[USB to TTL シリアル ケーブル](https://www.adafruit.com/product/954)を使用して、起動できないデバイスをシリアルで更新するには、非標準の状況用の USB 更新ガイドを参照してください。

[comment]: # (使用可能な場合は、USB 更新ガイドへのリンクを追加します。)