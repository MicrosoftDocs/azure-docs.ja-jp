---
title: Azure Media Services Video Indexer リリース ノート | Microsoft Docs
description: 常に最新の開発情報を把握していただけるよう、この記事では Azure Media Services Video Indexer の最新の更新情報を提供します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 08/29/2019
ms.author: juliako
ms.openlocfilehash: a7d178972a8c8b413e3c6336c403348f1eb78abb
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232602"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Azure Media Services Video Indexer リリース ノート

常に最新の開発情報を把握していただけるよう、この記事では以下に関する情報を提供します。

* 最新のリリース
* 既知の問題
* バグの修正
* 非推奨の機能

## <a name="august-2019"></a>2019 年 8 月
 
### <a name="video-indexer-deployed-in-uk-south"></a>英国南部でデプロイされる Video Indexer

Video Indexer 有料アカウントを英国南部リージョンで作成できるようになりました。

### <a name="new-editorial-shot-type-insights-available"></a>利用可能になった新しい編集ショット タイプ分析情報

ビデオ ショットに追加された新しいタグにより、コンテンツ作成ワークフローで使用される次のような一般的な編集語句を識別するための "ショット タイプ" が提供されます: 超クローズアップ、クローズアップ、ワイド、中程度、2 ショット、屋外、室内、左の顔、右の顔 (JSON で使用可能)。

### <a name="new-people-and-locations-entities-extraction-available"></a>利用可能になった新しい人と場所のエンティティ抽出

Video Indexer では、ビデオの OCR と文字起こしからの自然言語処理 (NLP) により、名前付きの場所と人が識別されます。 Video Indexer では、機械学習アルゴリズムを使用して、特定の場所 (エッフェル塔など) や人 (John Doe など) などのビデオでの言及が認識されます。

### <a name="keyframes-extraction-in-native-resolution"></a>ネイティブ解像度でのキーフレームの抽出

Video Indexer によって抽出されたキーフレームを、ビデオの元の解像度で使用できます。
 
### <a name="ga-for-training-custom-face-models-from-images"></a>GA になった画像からのカスタム顔モデルのトレーニング

画像からの顔のトレーニングが、プレビュー モードから GA になりました (API およびポータルで利用可能)。

> [!NOTE]
> "プレビューから GA" への移行に関連する価格への影響はありません。

### <a name="hide-gallery-toggle-option"></a>ギャラリー非表示の切り替えオプション

ユーザーは、ポータルからギャラリー タブを非表示にすることができます (サンプル タブの非表示と同様)。
 
### <a name="maximum-url-size-increased"></a>URL の最大サイズの拡大

ビデオのインデックス作成時にサポートされる URL クエリ文字列が (2048 文字ではなく) 4096 文字になります。
 
### <a name="support-for-multi-lingual-projects"></a>多言語プロジェクトのサポート

異なる言語でインデックス付けされた複数のビデオに基づいて、プロジェクトを作成できるようになりました (API のみ)。

## <a name="july-2019"></a>2019 年 7 月

### <a name="editor-as-a-widget"></a>ウィジェットとしてのエディター

Video Indexer AI エディターが、お客様のアプリケーションに埋め込むためのウィジェットとして提供されています。

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>ポータルでクローズド キャプション ファイルからカスタム言語モデルを更新する

お客様は、ポータルのカスタマイズ ページから、言語モデルの入力として VTT、SRT、TTML の各ファイル形式を指定できます。

## <a name="june-2019"></a>2019 年 6 月

### <a name="video-indexer-deployed-to-japan-east"></a>Video Indexer を東日本にデプロイ

Video Indexer 有料アカウントを東日本リージョンで作成できるようになりました。

### <a name="create-and-repair-account-api-preview"></a>アカウントの作成および修復 API (プレビュー)

[Azure Media Service の接続エンドポイントまたはキーを更新](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag)できる新しい API が追加されました。

### <a name="improve-error-handling-on-upload"></a>アップロードでのエラー処理の向上 

基になる Azure Media Services アカウントの構成に誤りがある場合には、説明のメッセージが返されます。

### <a name="player-timeline-keyframes-preview"></a>プレーヤー タイムラインのキーフレーム プレビュー 

プレーヤーのタイムライン上の各時間に対するイメージ プレビューを表示できるようになりました。

### <a name="editor-semi-select"></a>エディターの半選択

エディターで特定の分析情報期間を選択した結果として選択されるすべての分析情報のプレビューを表示できるようになりました。

## <a name="may-2019"></a>2019 年 5 月

### <a name="update-custom-language-model-from-closed-caption-file"></a>クローズド キャプション ファイルからのカスタム言語モデルの更新

[カスタム言語モデルの作成](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag)および[カスタム言語モデルの更新](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) API は、言語モデルの入力として、VTT、SRT、および TTML ファイル形式をサポートするようになりました。

[ビデオ トランスクリプトの更新 API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript) を呼び出すと、トランスクリプトが自動的に追加されます。 ビデオに関連付けられたトレーニング モデルも自動的に更新されます。 使用する言語モデルをカスタマイズおよびトレーニングする方法については、「[Video Indexer で言語モデルをカスタマイズする](customize-language-model-overview.md)」を参照してください。

### <a name="new-download-transcript-formats--txt-and-csv"></a>新しいダウンロード トランスクリプト形式 – TXT と CSV

Video Indexer は、既にサポートされているクローズド キャプション形式 (SRT、VTT、TTML) に加えて、TXT および CSV 形式でのトランスクリプトのダウンロードをサポートするようになりました。

## <a name="next-steps"></a>次の手順

[概要](video-indexer-overview.md)
