---
title: "Elemental Live エンコーダーを構成して、単一ビットレートのライブ ストリームを送信する | Microsoft Docs"
description: "このトピックでは、Elemental Live エンコーダーを構成して、ライブ エンコードで有効になっている AMS チャネルに単一ビットレートのストリームを送信する方法を示します。"
services: media-services
documentationcenter: 
author: cenkdin
manager: erikre
editor: 
ms.assetid: 9c6bf6a9-6273-4fdd-9477-f0e565280b5b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/05/2017
ms.author: cenkd;anilmur;juliako
translationtype: Human Translation
ms.sourcegitcommit: f6d6b7b1051a22bbc865b237905f8df84e832231
ms.openlocfilehash: bb4966cbd2af7a14caeac108a001b0820a67d2a1
ms.lasthandoff: 01/11/2017


---
# <a name="use-the-elemental-live-encoder-to-send-a-single-bitrate-live-stream"></a>Elemental Live エンコーダーを使用して、単一ビットレートのライブ ストリームを送信する
> [!div class="op_single_selector"]
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

このトピックでは、 [Elemental Live](http://www.elementaltechnologies.com/products/elemental-live) エンコーダーを構成して、ライブ エンコードで有効になっている AMS チャネルに単一ビットレートのストリームを送信する方法を示します。  詳細については、「 [Azure Media Services を使用してライブ エンコードの実行が有効なチャネルを操作する](media-services-manage-live-encoder-enabled-channels.md)」をご覧ください。

このチュートリアルでは、Azure Media Service Explorer (AMSE) ツールを使用して、Azure Media Services (AMS) を管理する方法を示します。 このツールは、Windows PC でのみ実行されます。 Mac または Linux の場合は、Azure Portal を使用して、[チャネル](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel)と[プログラム](media-services-portal-creating-live-encoder-enabled-channel.md)を作成します。

## <a name="prerequisites"></a>前提条件
* ライブ イベントを作成するには、Elemental Live Web インターフェイスの使用に関する知識が必要です。
* [Azure Media Services アカウントを作成します](media-services-portal-create-account.md)
* ストリーミング エンドポイントが実行されていることを確認します。 詳細については、 [Media Services アカウントでストリーミング エンドポイントを管理する方法](media-services-portal-manage-streaming-endpoints.md)に関する記事をご覧ください。
* 最新バージョンの [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) ツールをインストールします。
* ツールを起動し、AMS アカウントに接続します。

## <a name="tips"></a>ヒント
* 可能な限り、有線のインターネット接続を使用します。
* 帯域幅要件の目安は、ストリーミングのビットレートの&2; 倍です。 これは必須の要件ではありませんが、ネットワークの混雑の影響を軽減することができます。
* ソフトウェア ベースのエンコーダーを使用する際は、不要なプログラムを終了します。

## <a name="elemental-live-with-rtp-ingest"></a>RTP インジェストを使用する Elemental Live
このセクションでは、RTP 経由で単一ビットレートのライブ ストリームを送信する Elemental Live エンコーダーを構成する方法を示します。  詳細については、「 [RTP 経由の MPEG-TS ストリーム](media-services-manage-live-encoder-enabled-channels.md#channel)」を参照してください。

### <a name="create-a-channel"></a>チャネルの作成

1. AMSE ツールで、 **[Live]** タブに移動して、チャネル領域内を右クリックします。 メニューから **[チャネルの作成]** を選択します。

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental1.png)

2. チャネルの名前を指定します。説明フィールドは省略可能です。 [チャネル設定] の [Live Encoding] オプションで入力プロトコルを **[RTP (MPEG TS)]** に設定して、**[Standard]** を選択します。 それ以外の設定はすべてそのままにしておくことができます。

    **[新しいチャネルを今すぐ開始する]** が選択されていることを確認します。

3. **[チャネルの作成]**をクリックします。

   ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental12.png)

> [!NOTE]
> チャネルの開始までに 20 分程度かかることがあります。
>
>

チャネルを開始している間に、 [エンコーダーを構成する](media-services-configure-elemental-live-encoder.md#configure_elemental_rtp)ことができます。

> [!IMPORTANT]
> チャネルが準備完了の状態になるとすぐに課金が開始されることに注意してください。 詳細については、「 [チャネルの状態](media-services-manage-live-encoder-enabled-channels.md#states)」を参照してください。
>
>

### <a name="a-idconfigureelementalrtpaconfigure-the-elemental-live-encoder"></a><a id=configure_elemental_rtp></a>Elemental Live エンコーダーを構成する
このチュートリアルでは、次の出力設定が使用されます。 このセクションの残りの部分では、構成の手順の詳細について説明します。

**ビデオ**:

* コーデック: H.264
* プロファイル: 高 (レベル 4.0)
* ビットレート: 5000 kbps
* キーフレーム: 2 秒 (60 秒)
* フレーム レート: 30

**オーディオ**:

* コーデック: AAC (LC)
* ビットレート: 192 kbps
* サンプル レート: 44.1 kHz

#### <a name="configuration-steps"></a>構成の手順
1. **Elemental Live** Web インターフェイスに移動し、**UDP/TS** ストリーミングのエンコーダーを設定します。
2. 新しいイベントが作成されたら、出力グループまでスクロールし、 **UDP/TS** 出力グループを追加します。
3. **[新しいストリーム]** を選択し、**[出力の追加]** をクリックして、新しい出力を作成します。  

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental13.png)

   > [!NOTE]
   > ストリームのエラー時にエンコーダーが再接続できるように、Elemental のイベントのタイムコードを [システム クロック] に設定することをお勧めします。
   >
   >
4. 出力が作成されたので、 **[ストリームの追加]**をクリックします。 これで、出力の設定が構成されました。
5. 作成した "Stream 1" まで下にスクロールし、左の **[ビデオ]** タブをクリックして、**[詳細]** 設定セクションを展開します。

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental4.png)

    Elemental Live では多様なカスタマイズが可能ですが、AMS へのストリーミングを開始する場合は、次の設定をお勧めします。

   * 解像度: 1280 x 720
   * フレームレート: 30
   * GOP サイズ: 60 フレーム
   * インターレース モード: プログレッシブ
   * ビットレート: 5000000 ビット/秒 (ネットワークの制限に基づいて調整可能)

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental5.png)

1. チャネルの入力 URL を取得します。

    AMSE ツールに戻り、チャネルの完了状態を確認します。 状態が **[開始中]** から **[実行中]** に変わったら、入力 URL を取得することができます。

    チャネルが実行されている場合、チャネル名を右クリックして、下へ移動して **[入力 URL をクリップボードにコピー]** にマウスを合わせ、**[プライマリ入力 URL]** を選択します。  

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental6.png)
2. この情報を Elemental の **[プライマリ宛先]** フィールドに貼り付けます。 その他のすべての設定は、既定値にしておくことができます。

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental14.png)

    冗長性を高めるには、UDP/TS のストリーミング用に別の [出力] タブを作成することで、セカンダリ入力 URL を使用してこれらの手順を繰り返します。
3. **[作成]** (新しいイベントが作成された場合) または **[更新]** (既存のイベントを編集する場合) をクリックして、エンコーダーの開始に進みます。

> [!IMPORTANT]
> Elemental Live Web インターフェイスの **[開始]** をクリックする前に、チャネルが準備可能になっていることを確認する**必要があります**。
> また、15 分を超えるイベントがある場合を除き、チャネルを準備可能のままにしないでください。
>
>

ストリームが 30 秒間実行されたら、AMSE ツールに戻り、再生をテストします。  

### <a name="test-playback"></a>再生をテストする

AMSE ツールに移動し、テストするチャネルを右クリックします。 メニューが表示されたら、**[プレビューの再生]** にマウスを合わせ、**[Azure Media Player を使用]** を選択します。  

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental8.png)

ストリームがプレーヤーに表示されている場合は、エンコーダーが AMS に接続するように正しく構成されています。

エラーが表示される場合は、チャネルをリセットして、エンコーダー設定を調整する必要があります。 ガイダンスについては、 [トラブルシューティング](media-services-troubleshooting-live-streaming.md) に関するトピックをご覧ください。   

### <a name="create-a-program"></a>プログラムを作成する
1. チャネルの再生が確認されたら、プログラムを作成します。 AMSE ツールの **[Live]** タブで、プログラム領域内を右クリックし、**[新しいプログラムの作成]** を選択します。  

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental9.png)
2. 必要に応じてプログラムに名前を付け、 **[アーカイブ ウィンドウの長さ]** (既定では 4 時間) を調整します。 ストレージの場所を指定することも、既定値のままにすることもできます。  
3. **[プログラムを今すぐ開始]** ボックスを選択します。
4. **[プログラムの作成]**をクリックします。  

    >[!NOTE]
    > プログラムの作成は、チャネルの作成ほど時間はかかりません。   
      
5. プログラムが実行されたら、再生を確認するために、プログラムを右クリックして **[プログラムの再生]** に移動し、**[Azure Media Player を使用]** を選択します。  
6. 確認したら、プログラムをもう一度クリックし、**[出力 URL をクリップボードにコピー]** を選択します (または、メニューの **[プログラムの情報と設定]** オプションから、この情報を取得します)。

これで、ストリームをプレーヤーに埋め込んだり、ライブ表示のために対象ユーザーに配信したりできるようになりました。  

## <a name="troubleshooting"></a>トラブルシューティング
ガイダンスについては、 [トラブルシューティング](media-services-troubleshooting-live-streaming.md) に関するトピックをご覧ください。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

