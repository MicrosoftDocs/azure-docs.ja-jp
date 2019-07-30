---
title: Haivision KB エンコーダーを構成して、単一ビットレートのライブ ストリームを Azure に送信する | Microsoft Docs
description: このトピックでは、Haivision KB エンコーダーを構成して、ライブ エンコードで有効になっている AMS チャネルに単一ビットレートのストリームを送信する方法を示します。
services: media-services
documentationcenter: ''
author: dbgeorge
manager: vsood
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 7bb3db4861842e145689682035adc3c691538adf
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297796"
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Haivision KB エンコーダーを使用して、単一ビットレートのライブ ストリームを送信する  
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Haivision](media-services-configure-kb-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)

このトピックでは、[Haivision KB ライブ エンコーダー](https://www.haivision.com/products/kb-series/)を構成して、ライブ エンコードで有効になっている AMS チャネルに単一ビットレートのストリームを送信する方法を示します。 詳細については、「 [Azure Media Services を使用してライブ エンコードの実行が有効なチャネルを操作する](media-services-manage-live-encoder-enabled-channels.md)」をご覧ください。

このチュートリアルでは、Azure Media Service Explorer (AMSE) ツールを使用して、Azure Media Services (AMS) を管理する方法を示します。 このツールは、Windows PC でのみ実行されます。 Mac または Linux の場合は、Azure Portal を使用して、[チャネル](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel)と[プログラム](media-services-portal-creating-live-encoder-enabled-channel.md)を作成します。

## <a name="prerequisites"></a>前提条件
*   SW v5.01 以降を実行している Haivision KB エンコーダーにアクセスします。
* [Azure Media Services アカウントを作成します](media-services-portal-create-account.md)
* ストリーミング エンドポイントが実行されていることを確認します。 詳細については、「 [Media Services アカウントでストリーミング エンドポイントを管理する方法](media-services-portal-manage-streaming-endpoints.md)
* 最新バージョンの [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) ツールをインストールします。
* ツールを起動し、AMS アカウントに接続します。

## <a name="tips"></a>ヒント
* 可能な限り、有線のインターネット接続を使用します。
* 帯域幅要件の目安は、ストリーミングのビットレートの 2 倍です。 これは必須の要件ではありませんが、ネットワークの混雑の影響を軽減することができます。
* ソフトウェア ベースのエンコーダーを使用する際は、不要なプログラムを終了します。

## <a name="create-a-channel"></a>チャネルの作成
1. AMSE ツールで、 **[Live]** タブに移動して、チャネル領域内を右クリックします。 メニューから **[チャネルの作成]** を選択します。
[Haivision](./media/media-services-configure-kb-live-encoder/channel.png)
2. チャネルの名前を指定します。説明フィールドは省略可能です。 [チャネル設定] の [Live Encoding] オプションで入力プロトコルを **[RTMP]** に設定して、 **[Standard]** を選択します。 それ以外の設定はすべてそのままにしておくことができます。 **[新しいチャネルを今すぐ開始する]** が選択されていることを確認します。
3. **[チャネルの作成]** をクリックします。
[Haivision](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> チャネルの開始までに 20 分程度かかることがあります。

## <a name="configure-the-haivision-kb-encoder"></a>Haivision KB エンコーダーを構成する
このチュートリアルでは、次の出力設定が使用されます。 このセクションの残りの部分では、構成の手順の詳細について説明します。

ビデオ:
-   コーデック:H.264
-   プロファイル:高 (レベル 4.0)
-   ビットレート:5000 kbps
-   キーフレーム:2 秒 (60 フレーム)
-   フレーム レート:30

オーディオ:
-   コーデック:AAC (LC)
-   ビットレート:192 kbps
-   サンプル レート:44.1 kHz

## <a name="configuration-steps"></a>構成の手順
1.  Haivision KB のユーザー インターフェイスにログインします。
2.  チャネル コントロール センターの**メニュー ボタン**をクリックし、 **[Add Channel]\(チャネルの追加\)** を選びます  
    ![スクリーンショット 2017-08-14 午前 9.15.09](./media/media-services-configure-kb-live-encoder/step2.png)
3.  [Name]\(名前\) フィールドに**チャネル名**を入力し、[Next]\(次へ\) をクリックします。  
    ![スクリーンショット 2017-08-14 午前 9.19.07](./media/media-services-configure-kb-live-encoder/step3.png)
4.  **[Input Source]\(入力ソース\)** ドロップダウンから **[Channel Input Source]\(チャネル入力ソース\)** を選び、[Next]\(次へ\) をクリックします。
    ![スクリーンショット 2017-08-14 午前 9.20.44](./media/media-services-configure-kb-live-encoder/step4.png)
5.  **[Encoder Template]\(エンコーダー テンプレート\)** ドロップダウンから **[H264-720-AAC-192]** を選び、[Next]\(次へ\) をクリックします。
    ![スクリーンショット 2017-08-14 午前 9.23.15](./media/media-services-configure-kb-live-encoder/step5.png)
6.  **[Select New Output]\(新しい出力の選択\)** ドロップダウンから **[RTMP]** を選び、[Next]\(次へ\) をクリックします。  
    ![スクリーンショット 2017-08-14 午前 9.27.51](./media/media-services-configure-kb-live-encoder/step6.png)
7.  **[Channel Output]\(チャネル出力\)** ウィンドウで、Azure ストリームの情報を設定します。 **[Server]\(サーバー\)** 領域の初期チャネル設定から **[RTMP]** のリンクを貼り付けます。 **[Output Name]\(出力名\)** 領域に、チャネルの名前を入力します。 [Stream Name Template]\(ストリーム名テンプレート\) 領域で、テンプレート RTMPStreamName_%video_bitrate% を使ってストリームの名前を設定します。
    ![スクリーンショット 2017-08-14 午前 9.33.17](./media/media-services-configure-kb-live-encoder/step7.png)
8.  [Next]\(次へ\)、[Done]\(完了\) の順にクリックします。
9.  **再生ボタン**をクリックしてエンコーダー チャネルを開始します。  
    ![Haivision KB.png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>再生をテストする
AMSE ツールに移動し、テストするチャネルを右クリックします。 メニューの [Playback the Preview]\(プレビューの再生\) をポイントして、[Azure Media Player]\(Azure Media Player\) を選びます。

ストリームがプレーヤーに表示されている場合は、エンコーダーが AMS に接続するように正しく構成されています。

エラーが表示される場合は、チャネルをリセットして、エンコーダー設定を調整する必要があります。 ガイダンスについては、トラブルシューティングに関する記事をご覧ください。

## <a name="create-a-program"></a>プログラムを作成する
1.  チャネルの再生が確認されたら、プログラムを作成します。 AMSE ツールの [Live]\(ライブ\) タブで、プログラム領域内を右クリックし、[Create New Program]\(新しいプログラムの作成\) を選びます。
[Haivision](./media/media-services-configure-kb-live-encoder/program.png)
1.  必要に応じてプログラムに名前を付け、[Archive Window Length]\(アーカイブ ウィンドウの長さ\) を調整します (既定では 4 時間)。 ストレージの場所を指定することも、既定値のままにすることもできます。
2.  [Start the Program now]\(プログラムを今すぐ開始\) ボックスをオンにします。
3.  [Create Program]\(プログラムの作成\) をクリックします。
4.  プログラムが実行されたら、再生を確認するために、プログラムを右クリックして [Play back the program(s)]\(プログラムの再生\) に移動し、[Azure Media Player]\(Azure Media Player\) を選びます。
5.  確認したら、プログラムをもう一度右クリックし、[Copy the Output URL to Clipboard]\(出力 URL をクリップボードにコピー\) を選びます (または、メニューの [Program information and settings]\(プログラムの情報と設定\) オプションから、この情報を取得します)。

これで、ストリームをプレーヤーに埋め込んだり、ライブ表示のために対象ユーザーに配信したりできるようになりました。

> [!NOTE]
> プログラムの作成は、チャネルの作成ほど時間はかかりません。
