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
ms.date: 07/22/2019
ms.author: juliako
ms.openlocfilehash: fd67e867b5eac58838551ac29e1fca5e860b15ff
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414217"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Azure Media Services Video Indexer リリース ノート

常に最新の開発情報を把握していただけるよう、この記事では以下に関する情報を提供します。

* 最新のリリース
* 既知の問題
* バグの修正
* 非推奨の機能

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
