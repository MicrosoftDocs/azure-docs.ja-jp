---
title: Telestream Wirecast エンコーダーを構成して単一ビットレートのライブ ストリームを送信する | Microsoft Docs
description: 'このトピックでは、Wirecast ライブ エンコーダーを構成して、ライブ エンコードが有効になっている AMS チャネルに単一ビットレートのストリームを送信する方法を示します。 '
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: cenkdin;anilmur
ms.openlocfilehash: a736270919f36cb7d4bbd4e686a98ddc57f402fa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103016833"
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Wirecast エンコーダーを使用して、単一ビットレートのライブ ストリームを送信する

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
> [!div class="op_single_selector"]
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>

この記事では、[Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) ライブ エンコーダーを構成して、ライブ エンコードが有効になっている AMS チャネルに単一ビットレートのストリームを送信する方法を示します。 詳細については、「 [Azure Media Services を使用してライブ エンコードの実行が有効なチャネルを操作する](media-services-manage-live-encoder-enabled-channels.md)」をご覧ください。

このチュートリアルでは、Azure Media Service Explorer (AMSE) ツールを使用して、Azure Media Services (AMS) を管理する方法を示します。 このツールは、Windows PC でのみ実行されます。 Mac または Linux の場合は、Azure Portal を使用して、[チャネル](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel)と[プログラム](media-services-portal-creating-live-encoder-enabled-channel.md)を作成します。

> [!NOTE]
> RTMPS プロトコルを使用する場合、エンコーダーで TLS 1.2 がサポートされている必要があります。 TLS 1.2 の要件により、Wirecast バージョン 13.0.2 以降を使用します。

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

    ![スクリーンショットは、メニューから選択された [チャネルの作成] を示しています。](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. チャネルの名前を指定します。説明フィールドは省略可能です。 [チャネル設定] の [Live Encoding] オプションで入力プロトコルを **[RTMP]** に設定して、 **[Standard]** を選択します。 それ以外の設定はすべてそのままにしておくことができます。

    **[新しいチャネルを今すぐ開始する]** が選択されていることを確認します。

3. **[チャネルの作成]** をクリックします。

   ![スクリーンショットは、[ライブ チャンネルの作成] ダイアログ ボックスを示しています。](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> チャネルの開始までに 20 分程度かかることがあります。
>
>

チャネルを開始している間に、[エンコーダーを構成する](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp)ことができます。

> [!IMPORTANT]
> チャネルが準備完了の状態になるとすぐに課金が開始されます。 詳細については、「 [チャネルの状態](media-services-manage-live-encoder-enabled-channels.md#states)」を参照してください。
>
>

## <a name="configure-the-telestream-wirecast-encoder"></a><a id="configure_wirecast_rtmp" />Telestream Wirecast エンコーダーを構成する
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
1. 使用中のコンピューターで、Telestream Wirecast アプリケーションを開き、RTMP ストリーミング用の設定を行います。
2. **[出力]** タブに移動して **[出力設定]** を選択し、出力を構成します。

    **[出力先]** に **[RTMP Server]** が設定されていることを確認します。
3. **[OK]** をクリックします。
4. 設定ページで、 **[ターゲット]** フィールドを **[Azure Media Services]** に設定します。

    エンコードのプロファイルは、 **Azure H.264 720 p 16:9 (1280 x 720)** に事前に選択されています。 これらの設定をカスタマイズするには、ドロップダウンの右側にある歯車アイコンを選択し、 **[新しいプリセット]** を選択します。

    ![スクリーンショットは、BlobTrigger が選択された [テンプレートを選択] ダイアログ ボックスを示しています。](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. エンコーダー プリセットを構成します。

    プリセットに名前を付けて、次の推奨設定を確認します。

    **ビデオ**

   * エンコーダー:MainConcept H.264
   * 1 秒あたりのフレーム数:30
   * 平均ビットレート:5000 kbps (ネットワークの制限に基づいて調整可能)
   * プロファイル:メイン
   * キー フレームの距離:60 フレーム

     **オーディオ**

   * ターゲットのビットレート:192 kbps
   * サンプル レート:44.100 kHz

     ![スクリーンショットは、AzureTest1 のエンコーダー プリセットを示しています。](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. **[保存]** をクリックします。

    エンコードのフィールドで、新しく作成したプロファイルを選択できるようになりました。

    新しいプロファイルが選択されていることを確認します。
7. Wirecast の **RTMP エンドポイント** に割り当てるために、チャネルの入力 URL を取得します。

    AMSE ツールに戻り、チャネルの完了状態を確認します。 状態が **[開始中]** から **[実行中]** に変わったら、入力 URL を取得することができます。

    チャネルが実行されている場合、チャネル名を右クリックして、下へ移動して **[入力 URL をクリップボードにコピー]** にマウスを合わせ、 **[プライマリ入力 URL]** を選択します。  

    ![スクリーンショットは、プライマリ入力 URL の [クリップボードに URL をコピーする] オプションを示しています。](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. Wirecast の **[出力設定]** ウィンドウで、この情報を出力セクションの **[アドレス]** フィールドに貼り付け、ストリーム名を割り当てます。

    ![スクリーンショットに、[出力設定] が示されています。](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. **[OK]** を選択します。
2. メインの **[Wirecast]** 画面で、ビデオとオーディオの入力ソースの準備が整っていることを確認し、左上隅の **[ストリーム]** をクリックします。

    ![スクリーンショットは、Wirecast ストリーム ボタンを示しています。](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> **[ストリーム]** をクリックする前に、チャネルの準備が整っていることを確認する **必要があります**。
> また、15 分を超える入力投稿フィードがある場合を除き、チャネルを準備可能のままにしないでください。
>
>

## <a name="test-playback"></a>再生をテストする

AMSE ツールに移動し、テストするチャネルを右クリックします。 メニューが表示されたら、 **[プレビューの再生]** にマウスを合わせ、 **[Azure Media Player を使用]** を選択します。  

![スクリーンショットは、[Azure Media Player を使用した再生] オプションが選択されていることを示しています。](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

ストリームがプレーヤーに表示されている場合は、エンコーダーが AMS に接続するように正しく構成されています。

エラーが表示される場合は、チャネルをリセットして、エンコーダー設定を調整する必要があります。 ガイダンスについては、[トラブルシューティング](media-services-troubleshooting-live-streaming.md)に関する記事をご覧ください。  

## <a name="create-a-program"></a>プログラムを作成する
1. チャネルの再生が確認されたら、プログラムを作成します。 AMSE ツールの **[Live]** タブで、プログラム領域内を右クリックし、 **[新しいプログラムの作成]** を選択します。  

    ![スクリーンショットは、[プログラムの作成] オプションが選択されていることを示しています。](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
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
