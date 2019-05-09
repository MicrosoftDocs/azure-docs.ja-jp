---
title: Azure Media Clipper クリッピング ジョブの送信 | Microsoft Docs
description: Azure Media Clipper からクリッピング ジョブを送信する手順
services: media-services
keywords: クリップ;サブクリップ;エンコード;メディア
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: f0dc6879ccbb22dbebd57de98e4610cd593318db
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58012146"
---
# <a name="submit-clipping-jobs-from-azure-media-clipper"></a>Azure Media Clipper からのクリッピング ジョブの送信 

Azure Media Clipper には、クリッピング ジョブの送信を処理するために実装された **submitSubclipCallback** メソッドが必要です。 この関数は、Clipper 出力の Web サービスへの HTTP POST を実装するためのものです。 この Web サービスで、エンコード ジョブを送信できます。 Clipper の出力は、レンダリング ジョブでの Media Encoder Standard エンコーディング プリセット、または動的マニフェスト フィルター呼び出しでの REST API ペイロードのいずれかになります。 メディア サービス アカウントの資格情報が、クライアントのブラウザーでは安全でないために、このパススルー モデルが必要となります。

次のシーケンス図は、ブラウザー クライアント、Web サービス、および Azure Media Services の間のワークフローを示したものです。![Azure Media Clipper のシーケンス図](media/media-services-azure-media-clipper-submit-job/media-services-azure-media-clipper-sequence-diagram.PNG)

前の図の 4 つのエンティティは、エンド ユーザーのブラウザー、Web サービス、Clipper リソースをホストしている CDN エンドポイント、および Azure Media Services です。 エンド ユーザーが Web ページに移動すると、ページはホストしている CDN エンドポイントから Clipper の JavaScript と CSS リソースを取得します。 エンド ユーザーは、自分のブラウザーから、クリッピング ジョブまたは動的マニフェスト フィルター作成呼び出しを構成します。 エンド ユーザーがジョブまたはフィルター作成呼び出しを送信すると、ブラウザーはデプロイする必要がある Web サービスにジョブのペイロードを格納します。 この Web サービスは、最終的に、お使いの Media Services アカウント資格情報を使って、クリッピング ジョブまたはフィルター作成呼び出しを Azure Media Services に送信します。

次のコードのサンプルは、**submitSubclipCallback** メソッドの例です。 このメソッドに、Media Encoder Standard エンコード プリセットの HTTP POST を実装します。 POST の (**結果**) が成功であった場合、**Promise** は成功となり、それ以外の場合は拒否されてエラーの詳細が戻ります。

```javascript
// Submit Subclip Callback
//
// Parameter:
// - subclip: object that represents the subclip (output contract).
//
// Returns: a Promise object that, when resolved, returns true if the operation was accept in the back-end; otherwise, returns false.
var onSubmitSubclip = function (subclip) {
    var promise = new Promise(function (resolve, reject) {
        // TODO: perform the back-end AJAX request to submit the subclip job.
        var result = true;

        if (/* everything turned out fine */ result) {
            resolve(result);
        }
        else {
            reject(Error("error details"));
        }
    });

    return promise;
};

var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
```
ジョブ送信の出力は、レンダリング ジョブでの Media Encoder Standard エンコーディング プリセット、または動的マニフェスト フィルター での REST API ペイロードのいずれかとなります。

## <a name="submitting-encoding-job-to-create-video"></a>ビデオを作成するためのエンコード ジョブの送信
Media Encoder Standard エンコード ジョブを送信して、正確なフレームのビデオ クリップを作成できます。 エンコード ジョブは、レンダリングされたビデオ (新しいフラグメント化された MP4 ファイル) を生成します。

レンダリングされたクリッピングのジョブ出力コントラクトは、次のプロパティを持つ JSON オブジェクトです。

```json
{
  /* Required */
  "name": "My New Subclip Output Name",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: The REST API version, currently 2.0 is supported
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id-1",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }, {
    /* Required */
    "id": "my-asset-id-2",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "job",

    /* Required if "type" === "job" */
    /* NOTE: This is the preset for the Media Encoder Standard (MES) processor that can be used in the back-end to submit the subclip job.
    The encoding profile ("Codecs" property) depends on the "singleBitrateMp4Profile" and "multiBitrateMp4Profile" option parameters
    specified when creating the widget instance. */
    /* REFERENCE: https://docs.microsoft.com/azure/media-services/media-services-advanced-encoding-with-mes */
    "job": {
      "Version": 1.0,
      "Codecs": [{
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [{
            "Profile": "Auto",
            "Level": "auto",
            "Bitrate": 6750,
            "MaxBitrate": 6750,
            "BufferWindow": "00:00:05",
            "Width": 1920,
            "Height": 1080,
            "BFrames": 3,
            "ReferenceFrames": 3,
            "AdaptiveBFrame": true,
            "Type": "H264Layer",
            "FrameRate": "0/1"
          }],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [{
        "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
        "Format": {
          "Type": "MP4Format"
        }
      }],
      "Sources": [{
        "AssetId": "my-asset-id-1",
        "StartTime": "0.00:01:15.000",
        "Duration": "0.00:00:25.000"
      }, {
        "AssetId": "my-asset-id-2",
        "StartTime": "0.00:20:04.000",
        "Duration": "0.00:33:07.500"
      }]
    }
  }
}
```

エンコード ジョブを実行するには、Media Encoder Standard エンコード ジョブと共に関連付けられたプリセットを送信します。 エンコード ジョブの送信について詳しくは、[.NET SDK の記事](https://docs.microsoft.com/azure/media-services/media-services-dotnet-encode-with-media-encoder-standard)または [REST API の記事](https://docs.microsoft.com/azure/media-services/media-services-rest-encode-asset)をご覧ください。

## <a name="quickly-creating-video-clips-without-encoding"></a>エンコードを使わないビデオ クリップの簡易作成
エンコード ジョブを作成する代わりに、Azure Media Clipper を使って動的なマニフェスト フィルターを作成できます。 フィルターは、エンコードを必要とせず、新しい資産が作成されないので簡単に作成できます。 フィルター クリッピングの出力コントラクトは、次のプロパティを持つ JSON オブジェクトです。

```json
{
  /* Required: Filter name (Alphanumeric characters and hyphens only, no whitespace) */
  "name": "FilterName",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: This subclipper version always returns '2.0' in this field.
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "filter",

    /* Required if "type" === "filter" */
    /* REFERENCE: https://docs.microsoft.com/rest/api/media/operations/assetfilter */
    "filter": {
      "PresentationTimeRange": {
        "StartTimestamp": "340000000",
        "EndTimestamp": "580000000",
        "Timescale": "10000000"
      },
      "Tracks": [{
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "video",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "audio",
          "Operator": "Equal"
        }, {
          "Property": "Name",
          "Value": "audio-track-1",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "text",
          "Operator": "Equal"
        }, {
          "Property": "Language",
          "Value": "en",
          "Operator": "Equal"
        }]
      }]
    }
  }
}
```

REST 呼び出しを送信して動的マニフェスト フィルターを作成するには、[REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest) を使って関連付けられたフィルター ペイロードを送信します。
