---
title: Ink Recognizer とは - Ink Recognizer API
titleSuffix: Azure Cognitive Services
description: Ink Recognizer をお使いのアプリケーション、Web サイト、ツール、またはその他のソリューションに統合すると、インク ストローク データが識別されるようになり、入力として使用することができます。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: overview
ms.date: 08/24/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 6c1a720e7e9bd9c71f925f104ca7fc70a1a5ef59
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051068"
---
# <a name="what-is-the-ink-recognizer-api"></a>Ink Recognizer API とは

[!INCLUDE [ink-recognizer-deprecation](includes/deprecation-note.md)]

Ink Recognizer Cognitive Service は、デジタルインク コンテンツ を分析および認識するためのクラウド ベースの REST API を提供します。 光学式文字認識 (OCR) を使用するサービスとは異なり、この API には入力としてデジタル インク ストローク データが必要になります。 デジタル インク ストロークは、デジタル ペンや指などの入力ツールの動きを表す 2D ポイント (X 座標、Y 座標) を時間順に並べたセットです。 入力から図形と手書きのコンテンツを認識し、認識されたすべてのエンティティを含む JSON 応答を返します。

![API へのインク ストローク入力の送信を説明するフローチャート](media/ink-recognizer-pen-graph.svg)

## <a name="features"></a>特徴

Ink Recognizer API を使用すると、アプリケーション内の手書きコンテンツを簡単に認識できます。 

|特徴量  |説明  |
|---------|---------|
| 手書き認識 | 63 の主要な[言語とロケール](language-support.md)の手書きコンテンツを認識します。 | 
| レイアウト認識 | デジタル インク コンテンツに関する構造情報を取得します。 コンテンツを手書き領域、段落、行、単語、箇条書きに分割します。 これにより、お使いのアプリケーションでそのレイアウト情報を使用して、リストの自動書式設定や図形の配置などの追加機能を構築できます。 |
| 図形認識 | メモを作成するときに最も一般的に使用される[幾何学的図形](concepts/send-ink-data.md#shapes-recognized-by-the-ink-recognizer-api)を認識します。 |
| 結合図形とテキストの認識 | どのインク ストロークが図形または手書きコンテンツに属しているかを認識し、それらを個別に分類します。|

## <a name="workflow"></a>ワークフロー

Ink Recognizer API は RESTful Web サービスです。HTTP 要求を作成して JSON を解析できる任意のプログラミング言語から簡単に呼び出すことができます。

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

新規登録後:

1. インク ストローク データを取得し、有効な JSON に[形式設定](concepts/send-ink-data.md#sending-ink-data)します。 この API は、1 回の要求につき最大 1,500 インク ストロークまで受け付けます。 
1. ユーザーのデータとともに Ink Recognizer API に要求を送信します。
1. 返された JSON メッセージを解析して API 応答を処理します。

## <a name="next-steps"></a>次のステップ

次の言語でクイックスタートを実行して、Ink Recognizer API の呼び出しを開始してください。
* [C#](quickstarts/csharp.md)
* [Java](quickstarts/java.md)
* [JavaScript](quickstarts/javascript.md)

デジタル インキング アプリで Ink Recognition API がどのように動作するかを確認するには、GitHub 上の次のサンプル アプリケーションを参照してください。
* [C# およびユニバーサル Windows プラットフォーム (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# および Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript Web ブラウザー アプリ](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java および Android モバイル アプリ](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift および iOS モバイル アプリ](https://go.microsoft.com/fwlink/?linkid=2089805)
