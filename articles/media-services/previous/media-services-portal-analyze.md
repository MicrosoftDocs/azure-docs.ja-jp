---
title: Azure Portal を使用してメディアを分析する | Microsoft Docs
description: このトピックでは、Azure Portal を使用して Media Analytics メディア プロセッサ (MP) でメディアを処理する方法について説明します。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: d3917f65d8be08d6355013393f6c6675ea6c7fc5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "61131821"
---
# <a name="analyze-your-media-using-the-azure-portal"></a>Azure Portal を使用してメディアを分析する 
> [!NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。 
> 
> 

## <a name="overview"></a>概要
Azure Media Services Analytics は、組織や企業がビデオ ファイルからアクションにつながる分析データを簡単に得られるようにする、(エンタープライズ向けの規模、コンプライアンス、セキュリティ、グローバルな展開で使用できる) 音声コンポーネントと視覚コンポーネントのコレクションです。 Azure Media Services Analytics の概要について、詳しくは[こちら](media-services-analytics-overview.md)のトピックを参照してください。 

このトピックでは、Azure Portal を使用して Media Analytics メディア プロセッサ (MP) でメディアを処理する方法について説明します。 Media Analytics MP によって MP4 または JSON ファイルが生成されます。 メディア プロセッサによって MP4 ファイルが生成された場合は、そのファイルをプログレッシブ ダウンロードします。 メディア プロセッサによって JSON ファイルが生成された場合は、そのファイルを Azure Blob Storage からダウンロードします。 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>分析する資産を選択する
1. [Azure Portal](https://portal.azure.com/) で Azure Media Services アカウントを選択します。
2. **[設定]** ウィンドウで、 **[資産]** を選択します。  
   
    ![ビデオを分析する](./media/media-services-portal-analyze/media-services-portal-analyze001.png)
3. 分析する資産を選択し、 **[分析]** をクリックします。
   
    ![ビデオを分析する](./media/media-services-portal-analyze/media-services-portal-analyze002.png)
4. **[メディア分析を使用したメディア資産の処理]** ウィンドウでプロセッサを選択します。 
   
    この記事の残りの部分で、各プロセッサを使用する理由とその方法について説明します。 
5. **[作成]** をクリックしてジョブを開始します。

## <a name="azure-media-indexer"></a>Azure Media Indexer
**Azure Media Indexer** メディア プロセッサを使用して、メディア ファイルとコンテンツを検索可能にすると共に、クローズド キャプション トラックを生成できます。 このセクションでは、この MP に対して指定するオプションについて詳細を説明します。

![ビデオを分析する](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>言語
マルチメディア ファイル内で認識される自然言語。 たとえば、英語やスペイン語です。 

### <a name="captions"></a>字幕
コンテンツから生成される字幕形式を選択できます。 インデックス作成ジョブでは、クローズド キャプション ファイルを次の形式で生成できます。  

* **SAMI**
* **TTML**
* **WebVTT**

これらの形式のクローズド キャプション (CC) ファイルを使用して、聴覚障がいを持つユーザーがオーディオ ファイルとビデオ ファイルにアクセスできるようにします。

### <a name="aib-file"></a>AIB ファイル
カスタム SQL Server IFilter と併用するオーディオ インデックス BLOB ファイルを生成する場合にこのオプションを選択します。 詳細については、 [このブログ](https://azure.microsoft.com/blog/using-aib-files-with-azure-media-indexer-and-sql-server/) をご覧ください。

### <a name="keywords"></a>キーワード
キーワード XML ファイルを生成する場合にこのオプションを選択します。 キーワード XML ファイルには音声コンテンツから抽出されるキーワードと、頻度とオフセットの情報が含まれます。

### <a name="job-name"></a>ジョブ名
ジョブを特定するためのフレンドリ名。 [こちら](media-services-portal-check-job-progress.md)の記事で、ジョブの進行状況を監視する方法について説明しています。 

### <a name="output-file"></a>出力ファイル
出力コンテンツを特定するためのフレンドリ名。 

### <a name="speed"></a>速度
入力ビデオの高速化に使用する速度を指定します。 出力は、入力ビデオの安定した低速度撮影画像となります。

### <a name="job-name"></a>ジョブ名
ジョブを特定するためのフレンドリ名。 [こちら](media-services-portal-check-job-progress.md)の記事で、ジョブの進行状況を監視する方法について説明しています。 

### <a name="output-file"></a>出力ファイル
出力コンテンツを特定するためのフレンドリ名。 

## <a name="azure-media-face-detector"></a>Azure Media Face Detector
**Azure Media Face Detector** メディア プロセッサ (MP) を使用すると、対象ユーザーを数えたり、動きを追跡したり、表情によって対象ユーザーの参加や反応を測定することさえできます。 このサービスには 2 つの機能があります。 

* **顔検出**
  
    顔検出は、ビデオの中で人の顔を検出して追跡します。 複数の顔を検出した後、画面内の移動を追跡でき、時間と位置のメタデータが JSON ファイルで返されます。 追跡中、この機能は画面内を移動する同じ人の顔には同じ ID をできる限り設定し、顔が遮られたり一瞬フレームの外に出たりしても維持されます。
  
  > [!NOTE]
  > このサービスでは、顔認識は実行されません。 顔が遮られたりフレームから外れる時間が長すぎると、戻ったときには新しい ID が設定されます。
  > 
  > 
* **感情検出**
  
    感情検出は Face Detection メディア プロセッサのオプションのコンポーネントであり、喜び、悲しみ、恐怖、怒りなど、検出された顔から複数の感情的属性の分析を返します。 

![ビデオを分析する](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>検出モード
プロセッサでは、次の各モードのいずれかを使用できます。

* 顔検出
* 顔ごとの感情認識
* 感情認識の集計

### <a name="job-name"></a>ジョブ名
ジョブを特定するためのフレンドリ名。 [こちら](media-services-portal-check-job-progress.md)の記事で、ジョブの進行状況を監視する方法について説明しています。 

### <a name="output-file"></a>出力ファイル
出力コンテンツを特定するためのフレンドリ名。 

## <a name="azure-media-motion-detector"></a>Azure Media Motion Detector
**Azure Media Motion Detector** メディア プロセッサ (MP) を使用すると、長くて動きの少ないビデオから注目すべき部分を効率よく識別できます。 モーション検出は、ビデオでモーションが発生したセクションを識別するために、静止カメラ映像で使用できます。 この機能は、イベントが発生したタイムスタンプと境界範囲のメタデータを含む JSON ファイルを生成します。

セキュリティ ビデオ フィードを対象とするこのテクノロジは、関連するイベントと、影や照明の変化などの誤検出に、モーションを分類できません。 これにより、いつまでも続く関係のないイベントに煩わされることなくカメラ フィードからセキュリティ アラートを生成することができ、非常に長い監視ビデオから関心のある瞬間を抽出できます。

![ビデオを分析する](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Azure Media Video Thumbnails
このプロセッサにより、ソース ビデオから興味深い部分が自動的に抜粋されるので、長いビデオの要約を簡単に作成することができます。 これは、長いビデオにおいて予定されている内容の概要をすばやく提供する場合に便利です。 詳細と例については、 [Azure Media Video Thumbnails を使用してビデオ要約を作成する](media-services-video-summarization.md)

![ビデオを分析する](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>ジョブ名
ジョブを特定するためのフレンドリ名。 [こちら](media-services-portal-check-job-progress.md)の記事で、ジョブの進行状況を監視する方法について説明しています。 

### <a name="output-file"></a>出力ファイル
出力コンテンツを特定するためのフレンドリ名。 

## <a name="azure-media-content-moderator"></a>Azure Media Content Moderator
このプロセッサは、ビデオに含まれる成人向けやわいせつな可能性のあるコンテンツを検出するのに役立ちます。 このプロセッサはビデオ内のショットとキーフレームを自動的に検出します。 成人向けやわいせつな可能性のあるコンテンツのキーフレームをスコア付けし、既定のしきい値に基づいてレビューを提案します。 詳細と例については、[Azure Media Content Moderator を使用したビデオの管理](media-services-content-moderation.md)に関するページを参照してください。

![ビデオのモデレート](./media/media-services-portal-analyze/media-services-portal-analyze-content-moderator.PNG)

### <a name="version"></a>バージョン 
"2.0" を使用しています。

### <a name="mode"></a>Mode
バージョン 2.0 では `Mode` 設定が無視されます。

## <a name="next-steps"></a>次の手順
Media Services のラーニング パスを確認します。

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
