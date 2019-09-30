---
title: Video Indexer を使用して複数言語のコンテンツを自動的に識別および文字起こしする
titleSuffix: Azure Media Services
description: このトピックでは、Video Indexer を使用して複数言語のコンテンツを自動的に識別および文字起こしする方法について説明します。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: 983b66be6b51c9af5987e539ea1175a65c9269b3
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861970"
---
# <a name="automatically-identify-and-transcribe-multi-language-content-preview"></a>複数言語のコンテンツを自動的に識別および文字起こしする (プレビュー)

Video Indexer では、複数言語のコンテンツ内の自動的な言語識別と文字起こしがサポートされています。 このプロセスでは、音声から異なるセグメントにある音声言語を自動的に識別し、メディア ファイルの各セグメントを送信して文字起こしし、文字起こしを結合して元の 1 つの統合された文字起こしを作成する必要があります。 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>ポータルを使用してインデックス作成時に多言語識別を選択

ビデオをアップロードしてインデックスを作成するときに、**複数言語の検出**を選択することができます。 あるいは、ビデオのインデックスを再作成するときに、**複数言語の検出**を選択することもできます。 次の手順では、インデックスを再作成する方法について説明します。

1. [Video Indexer](https://vi.microsoft.com/) Web サイトに移動してサインインします。
1. **[ライブラリ]** ページに移動し、インデックスを再作成するビデオの名前にカーソルを合わせます。 
1. 右下隅にある **[ビデオのインデックスの再作成]** ボタンをクリックします。 
1. **[ビデオのインデックスの再作成]** ダイアログで、 **[ビデオのソース言語]** ドロップダウン ボックスから **[複数言語の検出]** を選択します。

    * ビデオが複数言語としてインデックス付けされると、分析情報ページにそのオプションが含められ、追加の種類の分析情報が表示されます。これにより、ユーザーはどのセグメントが、どの言語の "音声言語" で文字起こしされているかを確認することができます。
    * すべての言語への翻訳は、複数言語のトランスクリプトから完全に利用できます。
    * その他の分析情報はすべて、検出されたマスター言語で表示されます。これは、オーディオに最も多く登場した言語です。
    * プレーヤー上のクローズド キャプションも、複数言語で利用できます。

![ポータルでの操作](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>API を使用してインデックス作成時に多言語識別を選択

API を使用してビデオのインデックスを作成するとき、または[インデックスを再作成](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?)するときは、`sourceLanguage` パラメーターで `multi-language detection` オプションを選択します。

### <a name="model-output"></a>モデルの出力

このモデルでは、ビデオ内で検出されたすべての言語が 1 つのリストに取得されます。

```json
"sourceLanguage": null,
"sourceLanguages": [
    "es-ES",
    "en-US"
],
```

また、文字起こしセクションの各インスタンスには、それを文字起こしした言語が含まれます。

```json
{
  "id": 136,
  "text": "I remember well when my youth Minister took me to hear Doctor King I was a teenager.",
  "confidence": 0.9343,
  "speakerId": 1,
  "language": "en-US",
  "instances": [
    {
       "adjustedStart": "0:21:10.42",
       "adjustedEnd": "0:21:17.48",
       "start": "0:21:10.42",
       "end": "0:21:17.48"
    }
  ]
},
```

## <a name="guidelines-and-limitations"></a>ガイドラインと制限

* 一連のサポートされている言語: 英語、フランス語、ドイツ語、スペイン語。
* サポートされている言語が最大で 3 つ含まれる複数言語コンテンツをサポート。
* オーディオに上のサポートされている一覧以外の言語が含まれている場合は、予期しない結果になります。
* 言語ごとに検出する最小セグメント長 – 15 秒。
* 言語検出オフセットは平均で 3 秒です。
* 音声は継続的であることが期待されます。 言語を頻繁に切り替えると、モデルのパフォーマンスに影響する可能性があります。
* ネイティブでない話者の音声は、モデルのパフォーマンスに影響を与える可能性があります (たとえば、母国語を使用している話者が、別の言語に切り替える場合など)。
* このモデルは、適切なオーディオ音響を使用して、(音声コマンド、歌声などではなく) 自然な会話音声を認識するように設計されています。
* プロジェクトの作成と編集は現在、複数言語ビデオでは利用できません。
* 複数言語の検出を使用する場合、カスタム言語モデルは使用できません。
* キーワードの追加はサポートされていません。
* "トランスクリプトの更新" API はサポートされていません。
* クローズ ドキャプション ファイルをエクスポートするときに、言語表示は示されません。


## <a name="next-steps"></a>次の手順

[Video Indexer の概要](video-indexer-overview.md)
