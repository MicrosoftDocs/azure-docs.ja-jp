---
title: Translator Speech API から Speech Service に移行する
titleSuffix: Azure Cognitive Services
description: アプリケーションを Translator Speech API から Speech Service に移行する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: aahi
ms.openlocfilehash: 2fb03721baa80e77a5fd387600a272e6b1cfc7d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "95013641"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Translator Speech API から Speech Service に移行する

アプリケーションを Microsoft Translator Speech API から [Speech Service](index.yml) に移行するにあたっては､この記事を参考にしてください。 このガイドでは、Translator Speech API と Speech Service の違いを簡単に説明し、アプリケーションの移行方法を提案します。

> [!NOTE]
> Speech Service によって Translator Speech API サブスクリプション キーが受け付けられることはありません。 新しい Speech Service サブスクリプションを作成する必要があります。

## <a name="comparison-of-features"></a>機能の比較

| 機能                                           | Translator Speech API                                  | Speech サービス | 詳細                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| テキストに変換                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 音声に変換                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| グローバル エンドポイント                                   | :heavy_check_mark:                                              | :heavy_minus_sign:                 | Speech Service では、グローバル エンドポイントは提供されません。 グローバル エンドポイントでは、最も近いリージョン固有エンドポイントにトラフィックに自動的に誘導するため、アプリケーションでの待機時間を短縮できます。                                                    |
| 地域のエンドポイント                                | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 接続時間の制限                             | 90 分                                               | SDK では無制限です。 WebSocket 接続で 10 分。                                                                                                                                                                                                                                                                                   |
| ヘッダーに Auth キー                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 1 つの要求での複数言語への翻訳 | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| SDK が使用可能                                    | :heavy_minus_sign:                                              | :heavy_check_mark:                 | 使用できる SDK については､[Speech Service のドキュメント](index.yml)をご覧ください。                                                                                                                                                    |
| WebSocket 接続                            | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 言語 API                                     | :heavy_check_mark:                                              | :heavy_minus_sign:                 | Speech サービスは、[Translator の言語リファレンス]()記事に記載された同じ言語範囲をサポートしています。 |
| 不適切な表現のフィルターとマーカー                       | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 入力として .WAV/PCM                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 入力としてその他のファイルの種類                         | :heavy_minus_sign:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| 部分結果                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| タイミング情報                                       | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                 |
| 関連付け ID                                    | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| カスタム音声モデル                              | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Speech Service には、音声認識を組織の一意のボキャブラリにカスタマイズできるカスタム音声モデルが用意されています。                                                                                                                                           |
| カスタム変換モデル                         | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Microsoft Text Translation API に登録すると、[カスタム トランスレーター](https://www.microsoft.com/translator/business/customization/)により、独自のデータを使ってより正確な翻訳ができます。                                                 |

## <a name="migration-strategies"></a>移行方法

Translator Speech API を使用しているアプリケーションを開発中の場合､あるいはそうしたアプリケーションを運用している場合は､Speech Service を使用するように更新することをお勧めします。 使用できる SDK やサンプル コード､チュートリアルについては､[Speech Service](index.yml) のドキュメントをご覧ください。 移行する際は、次を考慮してください。

* Speech Service では、グローバル エンドポイントは提供されません。 アプリケーションのすべてのトラフィックに単一のリージョン エンドポイントを使用しているときにアプリケーションが効率的に機能するかどうかを判断します。 効率的に機能しない場合は､geolocation を使って最も効率的なエンドポイントを探してください｡

* アプリケーションが長時間維持される接続を使用していて、利用可能な SDK を使用できない場合は、WebSocket 接続を使用できます。 適切なタイミングで再接続して、10 分のタイムアウト制限を管理してください。

* アプリケーションで Translator サービスと Translator Speech API を使用してカスタム変換を有効にしている場合は、Speech Service を使用して直接カテゴリ ID を追加することができます。

* Translator Speech API とは異なり、Speech Service では、1 つの要求で複数の言語への翻訳を完了できます。

## <a name="next-steps"></a>次のステップ

* [Speech Service を無料で試す](overview.md#try-the-speech-service-for-free)
* [クイック スタート: UWP アプリで Speech SDK を使用して音声を認識する](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>関連項目

* [Speech Service とは](overview.md)
* [Speech Service と Speech SDK のドキュメント](./speech-devices-sdk-quickstart.md?pivots=platform-android)