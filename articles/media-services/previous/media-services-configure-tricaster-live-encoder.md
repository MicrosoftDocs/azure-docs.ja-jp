---
title: NewTek TriCaster エンコーダーを構成して単一ビットレートのライブ ストリームを送信する | Microsoft Docs
description: このトピックでは、TriCaster ライブ エンコーダーを構成して、ライブ エンコードが有効になっている AMS チャネルに単一ビットレートのストリームを送信する方法を示します。
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: 8973181a-3059-471a-a6bb-ccda7d3ff297
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako;cenkd;anilmur
ms.openlocfilehash: adacce5c8307f3be972920bd3aef6d8ea912eb30
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57991996"
---
# <a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>NewTek TriCaster エンコーダーを使用して、単一ビットレートのライブ ストリームを送信する  
> [!div class="op_single_selector"]
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

この記事では、[NewTek TriCaster](http://newtek.com/products/tricaster-40.html) ライブ エンコーダーを構成して、ライブ エンコードが有効になっている AMS チャネルに単一ビットレートのストリームを送信する方法を示します。 詳細については、「 [Azure Media Services を使用してライブ エンコードの実行が有効なチャネルを操作する](media-services-manage-live-encoder-enabled-channels.md)」をご覧ください。

このチュートリアルでは、Azure Media Service Explorer (AMSE) ツールを使用して、Azure Media Services (AMS) を管理する方法を示します。 このツールは、Windows PC でのみ実行されます。 Mac または Linux の場合は、Azure Portal を使用して、[チャネル](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel)と[プログラム](media-services-portal-creating-live-encoder-enabled-channel.md)を作成します。

> [!NOTE]
> Live Encoding 用に有効にされている AMS チャネルへの投稿フィードで送信に Tricaster を使用する場合、フィード間の迅速なカットやスレートとの間の切り替えなど、Tricaster の一部の機能を使用すると、ライブでビデオ/オーディオの誤作動が発生する可能性があります。 AMS チームがこれらの問題の解決に取り組んでいます。問題が解決するまで、これらの機能を使用しないことをお勧めします。
>
>

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

1. AMSE ツールで、**[Live]** タブに移動して、チャネル領域内を右クリックします。 メニューから **[チャネルの作成]**  を選択します。

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. チャネルの名前を指定します。説明フィールドは省略可能です。 [チャネル設定] の [Live Encoding] オプションで入力プロトコルを **[RTMP]** に設定して、**[Standard]** を選択します。 それ以外の設定はすべてそのままにしておくことができます。

    **[新しいチャネルを今すぐ開始する]** が選択されていることを確認します。

3. **[チャネルの作成]** をクリックします。

   ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

> [!NOTE]
> チャネルの開始までに 20 分程度かかることがあります。
>
>

チャネルを開始している間に、[エンコーダーを構成する](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp)ことができます。

> [!IMPORTANT]
> チャネルが準備完了の状態になるとすぐに課金が開始されます。 詳細については、「 [チャネルの状態](media-services-manage-live-encoder-enabled-channels.md#states)」を参照してください。
>
>

## <a name="a-idconfiguretricasterrtmpconfigure-the-newtek-tricaster-encoder"></a><a id="configure_tricaster_rtmp"/>NewTek TriCaster エンコーダーを構成する

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

1. 使用中のビデオ入力ソースに応じて、新しい **NewTek TriCaster** プロジェクトを作成します。
2. そのプロジェクトの中で、 **[ストリーム]** ボタンを見つけて横にある歯車アイコンをクリックし、ストリーム構成メニューにアクセスします。

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. メニューが開いたら、[接続] という見出しの下で **[新規]** をクリックします。 接続の種類をたずねられたら、 **[Adobe Flash]** を選択します。

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)
4. Click **OK**.
5. **[ストリーミング プロファイル]** の下のドロップダウンの矢印をクリックし、**[参照]** に移動することで、FMLE プロファイルをインポートできるようになりました。

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)
6. 構成済みの FMLE プロファイルを保存した場所に移動します。
7. プロファイルを選択して、 **[OK]** をクリックします。

    プロファイルがアップロードされたら、次の手順に進みます。
8. Tricaster の **RTMP エンドポイント**に割り当てるために、チャネルの入力 URL を取得します。

    AMSE ツールに戻り、チャネルの完了状態を確認します。 状態が **[開始中]** から **[実行中]** に変わったら、入力 URL を取得することができます。

    チャネルが実行されている場合、チャネル名を右クリックして、下へ移動して **[入力 URL をクリップボードにコピー]** にマウスを合わせ、**[プライマリ入力 URL]** を選択します。  

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)
9. Tricaster プロジェクト内で、**[Flash Server]** の下の **[場所]** フィールドにこの情報を貼り付けます。 **[ストリーム ID]** フィールドでもストリーム名を割り当てます。

    ストリームの情報が FMLE プロファイルに追加された場合、**[設定のインポート]** をクリックし、保存されている FMLE プロファイルに移動して **[OK]** をクリックしても、ストリームの情報をこのセクションにインポートできます。 関連する Flash Server フィールドに、FMLE からの情報が入力されます。

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)
10. 操作が完了したら、画面の下部で **[OK]** をクリックします。 Tricaster へのビデオおよびオーディオの入力の準備が整ったら、 **[ストリーム]** ボタンをクリックして AMS へのストリーミングを開始します。

     ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

> [!IMPORTANT]
> **[ストリーム]** をクリックする前に、チャネルの準備が整っていることを確認する**必要があります**。
> また、15 分を超える入力投稿フィードがある場合を除き、チャネルを準備可能のままにしないでください。
>
>

## <a name="test-playback"></a>再生をテストする

AMSE ツールに移動し、テストするチャネルを右クリックします。 メニューが表示されたら、**[プレビューの再生]** にマウスを合わせ、**[Azure Media Player を使用]** を選択します。  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

ストリームがプレーヤーに表示されている場合は、エンコーダーが AMS に接続するように正しく構成されています。

エラーが表示される場合は、チャネルをリセットして、エンコーダー設定を調整する必要があります。 ガイダンスについては、[トラブルシューティング](media-services-troubleshooting-live-streaming.md)に関する記事をご覧ください。  

## <a name="create-a-program"></a>プログラムを作成する

1. チャネルの再生が確認されたら、プログラムを作成します。 AMSE ツールの **[Live]** タブで、プログラム領域内を右クリックし、**[新しいプログラムの作成]** を選択します。  

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)
2. 必要に応じてプログラムに名前を付け、**[アーカイブ ウィンドウの長さ]** (既定では 4 時間) を調整します。 ストレージの場所を指定することも、既定値のままにすることもできます。  
3. **[プログラムを今すぐ開始]** ボックスを選択します。
4. **[プログラムの作成]** をクリックします。  

    >[!NOTE]
    >プログラムの作成は、チャネルの作成ほど時間はかかりません。
        
5. プログラムが実行されたら、再生を確認するために、プログラムを右クリックして **[プログラムの再生]** に移動し、**[Azure Media Player を使用]** を選択します。  
6. 確認したら、プログラムをもう一度右クリックし、**[出力 URL をクリップボードにコピー]** を選択します (または、メニューの **[プログラムの情報と設定]** オプションから、この情報を取得します)。

これで、ストリームをプレーヤーに埋め込んだり、ライブ表示のために対象ユーザーに配信したりできるようになりました。  

## <a name="troubleshooting"></a>トラブルシューティング

ガイダンスについては、[トラブルシューティング](media-services-troubleshooting-live-streaming.md)に関する記事をご覧ください。

## <a name="next-step"></a>次のステップ

Media Services のラーニング パスを確認します。

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
