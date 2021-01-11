---
title: .NET で Azure Media Services Encoder Standard を使用してサムネイルを生成する方法
description: この記事では、.NET で Media Encoder Standard を使用して、資産をエンコードするのと同時にサムネイルを生成する方法を説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 5e4ad7ba75edd1899cbe2d7cd7d3b1b6c124ce35
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267651"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-net"></a>.NET で Encoder Standard を使用してサムネイルを生成する方法

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Encoder Standard を使用してビデオ入力から 1 つまたは複数の [JPEG](https://en.wikipedia.org/wiki/JPEG)、[PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics)、または [BMP](https://en.wikipedia.org/wiki/BMP_file_format) 画像ファイル形式のサムネイルを生成することができます。

## <a name="recommended-reading-and-practice"></a>推奨される資料とプラクティス

「[カスタム変換を使用してエンコードする方法 - .NET](customize-encoder-presets-how-to.md)」を参照して、カスタム変換について理解しておくことをお勧めします。

## <a name="transform-code-example"></a>変換コードの例

次のコード例では、サムネイルだけが作成されます。  次のパラメーターを設定する必要があります。

- **start** - サムネイルの生成を開始する入力ビデオ内の位置。 この値は、ISO 8601 形式 (たとえば、PT05S の場合は 5 秒で開始する)、フレーム数 (たとえば、10 の場合は 10 番目のフレームで開始する)、またはストリーム期間に対する相対値 (たとえば、10% の場合はストリーム期間の 10% で開始する) とすることができます。 また、マクロ {Best} もサポートされています。これにより、ビデオの最初の数秒から最適なサムネイルを選択するようにエンコーダーに指示が送られ、ステップと範囲の他の設定に関係なく、1 つのサムネイルのみが生成されることになります。 既定値はマクロ {Best} です。
- **step** - サムネイルが生成される間隔。 この値は、ISO 8601 形式 (たとえば、PT05S の場合は 5 秒ごとに 1 つの画像)、フレーム数 (たとえば、30 の場合は 30 フレームごとに 1 つの画像)、またはストリーム期間に対する相対値 (たとえば、10% の場合はストリーム期間の 10% ごとに 1 つの画像) とすることができます。 step 値は最初に生成されたサムネイルに影響します。これは、変換のプリセットの開始時に指定されたものと厳密に一致しない場合があります。 これは、開始時刻と開始時刻からのステップ位置との間で最適なサムネイルを最初の出力として選択しようと試みるエンコーダーに原因があります。 既定値は 10% であるため、ストリームの期間が長い場合は、最初に生成されるサムネイルが開始時刻に指定されているものより離れている可能性があります。 最初のサムネイルが開始時刻に近いことが予想される場合は、step に対して妥当な値を選択してみてください。また、開始時刻にサムネイルが 1 つだけ必要な場合は、range 値を 1 に設定してみてください。
- **range** - 入力ビデオ内の変換のプリセットの開始時刻を基準とする位置であり、そこでサムネイルの生成が停止されます。 この値は、ISO 8601 形式 (たとえば、PT5M30S の場合は開始時刻から 5 分 30 秒で停止する)、またはフレーム数 (たとえば、300 の場合は開始時刻のフレームから 300 番目のフレームで停止する。 この値が 1 の場合は、開始時刻にサムネイルを 1 つだけ生成することを意味します)、またはストリーム期間に対する相対値 (たとえば、50% の場合は開始時刻から半分のストリーム期間で停止する) とすることができます。 既定値は 100% です。これは、ストリームの末尾で停止することを意味します。
- **layers** - エンコーダーによって生成される出力画像レイヤーのコレクション。

```csharp

private static Transform EnsureTransformExists(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string transformName)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = client.Transforms.Get(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // Create a new Transform Outputs array - this defines the set of outputs for the Transform
        TransformOutput[] outputs = new TransformOutput[]
        {
            // Create a new TransformOutput with a custom Standard Encoder Preset
            // This demonstrates how to create custom codec and layer output settings

          new TransformOutput(
                new StandardEncoderPreset(
                    codecs: new Codec[]
                    {
                        // Generate a set of PNG thumbnails
                        new PngImage(
                            start: "25%",
                            step: "25%",
                            range: "80%",
                            layers: new PngLayer[]{
                                new PngLayer(
                                    width: "50%",
                                    height: "50%"
                                )
                            }
                        )
                    },
                    // Specify the format for the output files for the thumbnails
                    formats: new Format[]
                    {
                        new PngFormat(
                            filenamePattern:"Thumbnail-{Basename}-{Index}{Extension}"
                        )
                    }
                ),
                onError: OnErrorType.StopProcessingJob,
                relativePriority: Priority.Normal
            )
        };

        string description = "A transform that includes thumbnails.";
        // Create the custom Transform with the outputs defined above
        transform = client.Transforms.CreateOrUpdate(resourceGroupName, accountName, transformName, outputs, description);
    }

    return transform;
}
```

## <a name="next-steps"></a>次の手順
[REST を使用してサムネイルを生成する](media-services-generate-thumbnails-rest.md)
