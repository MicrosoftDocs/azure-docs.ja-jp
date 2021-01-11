---
title: ライブ文字起こし
titleSuffix: Azure Media Services
description: Azure Media Services のライブ文字起こしについて学習します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: c3465e294af104c4d9c3b34960f5e95cf41e7cb8
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89291417"
---
# <a name="live-transcription-preview"></a>ライブ文字起こし (プレビュー)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services は、さまざまなプロトコルでビデオやオーディオを配信し、テキストも配信します。 MPEG-DASH や HLS/CMAF を使用してライブ ストリームを発行し、その後、ビデオやオーディオと一緒に発行するときには、Microsoft のサービスにより、IMSC 1.1 互換 TTML で文字起こしされ、テキストが配信されます。 この配信は、MPEG-4 Part 30 (ISO/IEC 14496-30) フラグメントにパッケージ化されます。 HLS/TS による配信を使用する場合、テキストはチャンク VTT として配信されます。

ライブ文字起こしを有効にすると、追加料金が適用されます。 価格については、「[Media Services の価格](https://azure.microsoft.com/pricing/details/media-services/)」ページの「ライブ ビデオ」セクションを参照してください。

この記事では、Azure Media Services でライブ イベントをストリーミングするときにライブ文字起こしを有効にする方法について説明します。 続行する前に、Media Services v3 REST API の使用に慣れておいてください (詳細については、[このチュートリアル](stream-files-tutorial-with-rest.md)を参照してください)。 また、[ライブ ストリーミング](live-streaming-overview.md)の概念について理解している必要があります。 [Media Services によるライブ ストリーミング](stream-live-tutorial-with-api.md)のチュートリアルを完了することをお勧めします。

## <a name="live-transcription-preview-regions-and-languages"></a>ライブ文字起こしプレビューのリージョンと言語

ライブ文字起こしは、次のリージョンで使用できます。

- 東南アジア
- 西ヨーロッパ
- 北ヨーロッパ
- 米国東部
- 米国中部
- 米国中南部
- 米国西部 2
- ブラジル南部

次の一覧は、API で言語コードを使用して文字起こしを行うことができる言語です。

| Language | 言語コード |
| -------- | ------------- |
| カタロニア語  | ca-ES |
| デンマーク語 (デンマーク) | da-DK |
| ドイツ語 (ドイツ) | de-DE |
| 英語 (オーストラリア) | en-AU |
| 英語 (カナダ) | en-CA |
| 英語 (イギリス) | en-GB |
| 英語 (インド) | en-IN |
| 英語 (ニュージーランド) | en-NZ |
| 英語 (米国) | en-US |
| スペイン語 (スペイン) | es-ES |
| スペイン語 (メキシコ) | es-MX |
| フィンランド語 (フィンランド) | fi-FI |
| フランス語 (カナダ) | fr-CA |
| フランス語 (フランス) | fr-FR |
| イタリア語 (イタリア) | it-IT |
| オランダ語 (オランダ) | nl-NL |
| ポルトガル語 (ブラジル) | pt-BR |
| ポルトガル語 (ポルトガル) | pt-PT |
| スウェーデン語 (スウェーデン) | sv-SE |

## <a name="create-the-live-event-with-live-transcription"></a>ライブ文字起こしでライブ イベントを作成する

文字起こしを有効にしてライブ イベントを作成するには、2019-05-01-preview API バージョンで PUT 操作を送信します。次に例を示します。

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

この操作には次の本文があります (取り込みプロトコルとしての RTMP によってパススルー ライブ イベントが作成されます)。 transcriptions プロパティが追加されていることに注目してください。

```
{
  "properties": {
    "description": "Demonstrate how to enable live transcriptions",
    "input": {
      "streamingProtocol": "RTMP",
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "preview": {
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "encoding": {
      "encodingType": "None"
    },
    "transcriptions": [
      {
        "language": "en-US"
      }
    ],
    "vanityUrl": false,
    "streamOptions": [
      "Default"
    ]
  },
  "location": "West US 2"
}
```

## <a name="start-or-stop-transcription-after-the-live-event-has-started"></a>ライブ イベントが開始された後で文字起こしを開始または停止する

ライブ イベントが実行中状態の間は、ライブ文字起こしを開始および停止できます。 ライブ イベントの開始と停止の詳細については、「[Media Services v3 API を使用して開発する](media-services-apis-overview.md#long-running-operations)」の「長時間にわたって実行される操作」セクションを参照してください。

ライブ文字起こしを有効にするか、または文字起こし言語を更新するには、ライブ イベントに "transcriptions" プロパティが含まれるように修正プログラムを適用します。 ライブ文字起こしを無効にするには、ライブ イベント オブジェクトから "transcriptions" プロパティを削除します。  

> [!NOTE]
> ライブ イベント中に文字起こしを**複数回**オンまたはオフにするシナリオは、サポートされていません。

これは、ライブ 文字起こしを有効にするための呼び出しの例です。

PATCH: ```https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview```

```
{
  "properties": {
    "description": "Demonstrate how to enable live transcriptions", 
    "input": {
      "streamingProtocol": "RTMP",
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "preview": {
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "encoding": {
      "encodingType": "None"
    },
    "transcriptions": [
      {
        "language": "en-US"
      }
    ],
    "vanityUrl": false,
    "streamOptions": [
      "Default"
    ]
  },
  "location": "West US 2"
}
```

## <a name="transcription-delivery-and-playback"></a>文字起こしの配信と再生

このサービスがダイナミック パッケージを使用して、どのようにさまざまなプロトコルでビデオやオーディオを配信し、テキストも配信しているか、[ダイナミック パッケージの概要](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery)に関する記事で確認してください。 MPEG-DASH や HLS/CMAF を使用してライブ ストリームを発行し、その後、ビデオやオーディオと一緒に発行するときには、Microsoft のサービスにより、IMSC 1.1 互換 TTML で文字起こしされ、テキストが配信されます。 この配信は、MPEG-4 Part 30 (ISO/IEC 14496-30) フラグメントにパッケージ化されます。 HLS/TS による配信を使用する場合、テキストはチャンク VTT として配信されます。 [Azure Media Player](use-azure-media-player.md) などの Web プレーヤーを使用してストリームを再生できます。  

> [!NOTE]
> Azure Media Player を使用する場合は、バージョン 2.3.3 以降を使用します。

## <a name="known-issues"></a>既知の問題

以下に、プレビューでのライブ文字起こしに関する既知の問題を示します。

- アプリは、[Media Services v3 OpenAPI の仕様](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json)で説明されているプレビュー API を使用する必要があります。
- デジタル著作権管理 (DRM) 保護はテキスト トラックには適用されず、AES エンベロープ暗号化のみが可能です。

## <a name="next-steps"></a>次のステップ

* [Media Services の概要](media-services-overview.md)
