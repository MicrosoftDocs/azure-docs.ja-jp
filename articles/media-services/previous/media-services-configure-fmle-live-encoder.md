---
title: FMLE エンコーダーを構成して単一ビットレートのライブ ストリームを送信する | Microsoft Docs
description: このトピックでは、Flash Media Live Encoder (FMLE) エンコーダーを構成して、ライブ エンコードが有効になっている AMS チャネルに単一ビットレートのストリームを送信する方法を示します。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 3113f333-517a-47a1-a1b3-57e200c6b2a2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako;cenkdin;anilmur
ms.openlocfilehash: 01bb628a6520488dcebf49a1e868213b955abc31
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "61466014"
---
# <a name="use-the-fmle-encoder-to-send-a-single-bitrate-live-stream"></a>FMLE エンコーダーを使用して、単一ビットレートのライブ ストリームを送信する 
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>
>

この記事では、[Flash Media Live Encoder](https://www.adobe.com/products/flash-media-encoder.html) (FMLE) エンコーダーを構成して、ライブ エンコードが有効になっている AMS チャネルに単一ビットレートのストリームを送信する方法を示します。 詳細については、「 [Azure Media Services を使用してライブ エンコードの実行が有効なチャネルを操作する](media-services-manage-live-encoder-enabled-channels.md)」をご覧ください。

このチュートリアルでは、Azure Media Service Explorer (AMSE) ツールを使用して、Azure Media Services (AMS) を管理する方法を示します。 このツールは、Windows PC でのみ実行されます。 Mac または Linux の場合は、Azure Portal を使用して、[チャネル](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel)と[プログラム](media-services-portal-creating-live-encoder-enabled-channel.md)を作成します。

このチュートリアルでは、AAC の使用方法について説明します。 ただし、FMLE は、既定では AAC をサポートしません。 AAC エンコーディングのためのプラグイン (MainConcept の [AAC プラグイン](https://www.mainconcept.com/products/plug-ins/plug-ins-for-adobe/aac-encoder-fmle.html)など) を購入する必要があります。

## <a name="prerequisites"></a>前提条件
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

    ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle1.png)

2. チャネルの名前を指定します。説明フィールドは省略可能です。 [チャネル設定] の [Live Encoding] オプションで入力プロトコルを **[RTMP]** に設定して、 **[Standard]** を選択します。 それ以外の設定はすべてそのままにしておくことができます。

    **[新しいチャネルを今すぐ開始する]** が選択されていることを確認します。

3. **[チャネルの作成]** をクリックします。

   ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle2.png)

> [!NOTE]
> チャネルの開始までに 20 分程度かかることがあります。
>
>

チャネルを開始している間に、[エンコーダーを構成する](media-services-configure-fmle-live-encoder.md)ことができます。

> [!IMPORTANT]
> チャネルが準備完了の状態になるとすぐに課金が開始されることに注意してください。 詳細については、「 [チャネルの状態](media-services-manage-live-encoder-enabled-channels.md#states)」を参照してください。
>
>

## <a id=configure_fmle_rtmp></a>FMLE エンコーダーを構成する
このチュートリアルでは、次の出力設定が使用されます。 このセクションの残りの部分では、構成の手順の詳細について説明します。

**ビデオ**:

* コーデック:H.264
* プロファイル:高 (レベル 4.0)
* ビットレート:5000 kbps
* キーフレーム:2 秒 (60 秒)
* フレーム レート:30

**オーディオ**:

* コーデック:AAC (LC)
* ビットレート:192 kbps
* サンプル レート:44.1 kHz

### <a name="configuration-steps"></a>構成の手順
1. 使用中のコンピューターで、Flash Media Live Encoder (FMLE) のインターフェイスに移動します。

    インターフェイスは、設定のメイン ページの 1 つです。 FMLE を使用するストリーミングを開始するには、次の推奨設定にご注意ください。

   * 形式:H.264 フレーム レート: 30.00
   * 入力サイズ: 1280 x 720
   * ビットレート: 5000 Kbps (ネットワークの制限に基づいて調整可能)  

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle3.png)

     インターレースされたソースを使用する場合は、"インターレース解除" オプションをオフにしてください。
2. 形式の横にあるレンチのアイコンをクリックして、追加設定を次のようにする必要があります。

   * プロファイル:メイン
   * レベル: 4.0
   * キーフレームの頻度: 2 秒

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle4.png)
3. 次の重要なオーディオの設定を行います。

   * 形式:AAC
   * サンプル レート:44100 Hz
   * ビットレート:192 Kbps

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle5.png)
4. FMLE の **RTMP エンドポイント**に割り当てるために、チャネルの入力 URL を取得します。

    AMSE ツールに戻り、チャネルの完了状態を確認します。 状態が **[開始中]** から **[実行中]** に変わったら、入力 URL を取得することができます。

    チャネルが実行されている場合、チャネル名を右クリックして、下へ移動して **[入力 URL をクリップボードにコピー]** にマウスを合わせ、 **[プライマリ入力 URL]** を選択します。  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle6.png)
5. 出力セクションの **[FMS URL]** フィールドにこの情報を貼り付け、ストリーム名を割り当てます。

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle7.png)

    冗長性をさらに高めるには、セカンダリ入力 URL に対して次の手順を繰り返します。
6. **[接続]** を選択します。

> [!IMPORTANT]
> **[接続]** をクリックする前に、チャネルの準備が整っていることを確認する**必要があります**。
> また、15 分を超える入力投稿フィードがある場合を除き、チャネルを準備可能のままにしないでください。
>
>

## <a name="test-playback"></a>再生をテストする

AMSE ツールに移動し、テストするチャネルを右クリックします。 メニューが表示されたら、 **[プレビューの再生]** にマウスを合わせ、 **[Azure Media Player を使用]** を選択します。  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle8.png)

ストリームがプレーヤーに表示されている場合は、エンコーダーが AMS に接続するように正しく構成されています。

エラーが表示される場合は、チャネルをリセットして、エンコーダー設定を調整する必要があります。 ガイダンスについては、[トラブルシューティング](media-services-troubleshooting-live-streaming.md)に関する記事をご覧ください。  

## <a name="create-a-program"></a>プログラムを作成する
1. チャネルの再生が確認されたら、プログラムを作成します。 AMSE ツールの **[Live]** タブで、プログラム領域内を右クリックし、 **[新しいプログラムの作成]** を選択します。  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle9.png)
2. 必要に応じてプログラムに名前を付け、 **[アーカイブ ウィンドウの長さ]** (既定では 4 時間) を調整します。 ストレージの場所を指定することも、既定値のままにすることもできます。  
3. **[プログラムを今すぐ開始]** ボックスを選択します。
4. **[プログラムの作成]** をクリックします。  

    >[!NOTE]
    >プログラムの作成は、チャネルの作成ほど時間はかかりません。
        
5. プログラムが実行されたら、再生を確認するために、プログラムを右クリックして **[プログラムの再生]** に移動し、 **[Azure Media Player を使用]** を選択します。  
6. 確認したら、プログラムをもう一度右クリックし、 **[出力 URL をクリップボードにコピー]** を選択します (または、メニューの **[プログラムの情報と設定]** オプションから、この情報を取得します)。

これで、ストリームをプレーヤーに埋め込んだり、ライブ表示のために対象ユーザーに配信したりできるようになりました。  

## <a name="troubleshooting"></a>トラブルシューティング
ガイダンスについては、[トラブルシューティング](media-services-troubleshooting-live-streaming.md)に関する記事をご覧ください。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
