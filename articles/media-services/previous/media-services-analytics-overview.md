---
title: Media Services プラットフォームの Media Analytics | Microsoft Docs
description: Media Analytics のパブリック プレビュー (エンタープライズ規模での音声およびコンピューター ビジョン サービス、コンプライアンス、セキュリティ、およびグローバル展開をまとめたもの) の概要
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: c56e3781-8510-4f7f-b5ff-a218c1bb6f4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2019
ms.author: milanga;juliako;johndeu
ms.openlocfilehash: aac9719f8d74c4b7bc283745ee2b8e01365a81f0
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58620004"
---
# <a name="media-analytics-on-the-media-services-platform"></a>Media Services プラットフォームの Media Analytics 

## <a name="overview"></a>概要
従業員のトレーニング、お客様への対応、およびビジネス機能のドキュメント化で優先的に利用するメディアとしてビデオを使用する組織が増えています。 クラウド コンピューティングでは、それらの大量のメディア ファイルを格納、ストリーミングしたり、それらのファイルにアクセスしたりできます。 しかし、企業のビデオ コンテンツのライブラリが大きくなると、コンテンツから洞察を引き出す効果的な手段も必要です。 

このニーズの増大に対応するため、Azure Media Services には Azure Media Analytics が用意されています。 Media Analytics は音声および視覚コンポーネントの集合体であり、組織や企業がこれを活用することで、ビデオ ファイルから実用的な洞察を簡単に引き出すことができます。 Media Analytics は、Media Services の基本的なプラットフォーム コンポーネントを使用して構築されているので、1 日目からいつでも規模に応じてメディアを処理することができます。

Media Analytics を使用すると、開発者は高度なビデオ機能をすばやくアプリケーションに組み入れることができます。 Media Analytics は、大規模な組織で必要とされる本格的なスケール、コンプライアンス、セキュリティ、グローバル展開に対応したエンタープライズ環境を提供します。

次の図には、Media Analytics のほか、Media Services プラットフォームの他の主要な部分が示されています。 

![VoD ワークフロー](./media/media-services-analytics-overview/media-services-analytics-overview01.png)

Media Analytics のメディア プロセッサによって MP4 ファイルまたは JSON ファイルが生成されます。 メディア プロセッサによって MP4 ファイルが生成された場合は、そのファイルのプログレッシブ ダウンロードが可能です。 メディア プロセッサによって JSON ファイルが生成された場合は、そのファイルを Azure Blob Storage からダウンロードできます。 

## <a name="media-analytics-services"></a>Media Analytics サービス

### <a name="indexer"></a>Indexer
Azure Media Indexer では、コンテンツを検索可能にしたり、クローズド キャプション トラックを生成したりできます。 前のバージョンと比較すると、 Azure Media Indexer 2 プレビューは、インデックス作成が高速化され、より多くの言語をサポートします。 英語、スペイン語、フランス語、ドイツ語、イタリア語、中国語、ポルトガル語、アラビア語などがサポートされています。 詳しい情報と例については、[Azure Media Indexer 2 を使用したビデオの処理](media-services-process-content-with-indexer2.md)に関するページをご覧ください。
### <a name="motion-detector"></a>Motion Detector
Motion Detector を使用することで、ひな形の背景を持つビデオでモーションを検出することができます。 これにより、監視カメラで検出されるモーション イベントの誤判定をチェックします。 詳細と例については、 [Azure Media Analytics でのモーション検出](media-services-motion-detection.md)に関するページを参照してください。
### <a name="face-detector"></a>Face Detector
Face Detector を使用すると、幸福、悲しみ、驚きなど、人の顔と感情を検出できます。 これは、イベントに参加している人の反応を収集し分析するなど、後で説明するいくつかの業界アプリケーションに有用です。 詳細と例については、 [Azure Media Analytics での顔と感情の検出](media-services-face-and-emotion-detection.md)に関するページを参照してください。
### <a name="video-summarization"></a>ビデオ要約
ビデオ要約では、ソース ビデオから興味深いスニペットが自動的に選択されるので、長いビデオの要約を簡単に作成することができます。 この機能は、長いビデオにおいて予定されている内容の概要をすばやく提供する場合に便利です。 詳細と例については、 [Azure Media Video Thumbnails を使用してビデオ要約を作成する](media-services-video-summarization.md)を参照してください。
### <a name="optical-character-recognition"></a>光学式文字認識
Azure Media OCR (光学式文字認識) では、ビデオ ファイル内のテキスト コンテンツを編集かつ検索可能なデジタル テキストに変換できます。 これにより、メディアのビデオ信号から有意なメタデータを自動的に抽出することができます。
### <a name="scalable-face-redaction"></a>スケーラブルな顔編集
Azure Media Redactor は、クラウドでスケーラブルな顔編集を提供する Media Analytics メディア プロセッサです。 顔編集を使用することで、ビデオを編集して選択した個人の顔をぼかすことができます。 顔編集サービスは、ニュース媒体や公共の安全が関係する場合などに使用していただけます。 複数人の顔を含んでいる映像の場合、顔編集を手作業で行うと数分の映像でも数時間かかりますが、このサービスを使えば数ステップの簡単な手順で完了します。 詳細については、[Redact faces with Azure Media Analytics](media-services-face-redaction.md)の記事を参照してください。
### <a name="content-moderation"></a>コンテンツ モデレート
Azure Content Moderator を使用すると、マシンによるモデレートをビデオに使用できます。 たとえば、ビデオに含まれる成人向けやわいせつな可能性のあるコンテンツを検出し、フラグが設定されたコンテンツを人間のモデレート チームがレビューすることができます。 ビデオを手動でモデレートして、望ましくないコンテンツが含まれていないかどうかを確認するのは、時間とコストがかかる作業です。 このサービスおよび関連するレビュー ツールを使用すると、マシンによるモデレートと目視によるレビューを組み合わせて、高いコスト効率で最適な結果を効率的に得ることができます。 詳細については、[Azure Content Moderator を使用したビデオの処理](media-services-content-moderation.md)に関する記事をご覧ください。

## <a name="common-scenarios"></a>一般的なシナリオ
Media Analytics は、ビデオから新しい見識を収集する組織や企業を支援し、大量のビデオ コンテンツのより効果的な管理に役立ちます。 いくつかのシナリオを次に示します。

* **コール センター**。 ソーシャル メディアが登場しても、顧客サービス トランザクションの大部分は引き続きカスタマー コール センターによって支えられています。 このオーディオ データにコード化された大量の顧客情報を分析して、顧客満足度を高めることができます。 Media Indexer を使用することで、組織はテキストを抽出して検索インデックスとダッシュボードを構築できます。 これにより、一般的な苦情、苦情の原因、その他の関連データに関する知見を引き出すことができます。
* **ユーザー生成のコンテンツ モデレート**。 新しい報道発信源から警察まで、多くの組織に一般向けのポータルがあり、そこでビデオや画像などのユーザー生成のメディアを受け入れています。 コンテンツの量は、予期しないイベントが原因で急上昇することがあります。 これらのシナリオで、コンテンツが適切かどうかを手動で効果的に確認することは困難です。 顧客は、コンテンツ モデレート サービスを利用して、適切なコンテンツに的を絞ることができます。
* **監視**。 IP カメラの使用の増加に伴い、監視ビデオのインベントリが増加しています。 監視ビデオを手動で確認するとなると、時間がかかり、ヒューマン エラーが発生しやすくなります。 Media Analytics では、派生物を確認、管理、および作成するプロセスをより簡単にするために、モーション検出、顔検出、Hyperlapse などのサービスを用意しています。

## <a name="media-analytics-media-processors"></a>Media Analytics のメディア プロセッサ
ここでは、Media Analytics のメディア プロセッサを紹介し、.NET または REST を使用してメディア プロセッサ (MP) オブジェクトを取得する方法について説明します。

### <a name="mp-names"></a>MP 名
* Azure Media Indexer 2 プレビュー
* Azure Media Indexer
* Azure Media Hyperlapse
* Azure Media Face Detector
* Azure Media Motion Detector
* Azure Media Video Thumbnails
* Azure Media OCR
* Azure Media Content Moderator

### <a name="net"></a>.NET
次の関数は、指定された MP 名のいずれかを取得し、MP オブジェクトを返します。

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
            .Where(p => p.Name == mediaProcessorName)
            .ToList()
            .OrderBy(p => new Version(p.Version))
            .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }


### <a name="rest"></a>REST
要求:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20OCR' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.12
    Host: media.windows.net

応答:

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:074c3899-d9fb-448f-9ae1-4ebcbe633056",
             "Description":"Azure Media OCR",
             "Name":"Azure Media OCR",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

## <a name="demos"></a>デモ
[Azure Media Analytics デモ](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)を参照してください。

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>関連記事
[Media Services Analytics のお知らせ](https://azure.microsoft.com/blog/introducing-azure-media-analytics/)を参照してください。

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png

## <a name="next-steps"></a>次の手順
Media Services のラーニング パスを確認します。

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
