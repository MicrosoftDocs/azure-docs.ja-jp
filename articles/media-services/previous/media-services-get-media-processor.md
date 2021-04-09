---
title: .NET 用 Azure Media Services SDK を使用してメディア プロセッサを作成する方法 | Microsoft Docs
description: メディア プロセッサ コンポーネントを作成し、Azure Media Services 用にメディア コンテンツのエンコード、形式の変換、暗号化、または復号化を行う方法について説明します。 コード サンプルは C# で記述され、Media Services SDK for .NET を利用しています。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 6ce2a28ff9b34373cc716ea397927ef160bd1097
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013348"
---
# <a name="how-to-get-a-media-processor-instance"></a>方法:メディア プロセッサ インスタンスを取得する

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)

## <a name="overview"></a>概要

メディア プロセッサは、Media Services のコンポーネントとして、メディア コンテンツのエンコード、形式変換、暗号化、復号化など、特定の処理タスクを担います。 通常、メディア コンテンツのエンコード、暗号化、形式変換を行うタスクの作成時にメディア プロセッサを作成します。

## <a name="azure-media-processors"></a>Azure メディア プロセッサ

次のトピックは、メディア プロセッサの一覧です。

* [Encoding メディア プロセッサ](scenarios-and-availability.md)
* [Analytics メディア プロセッサ](scenarios-and-availability.md)

## <a name="get-media-processor"></a>メディア プロセッサの取得

次のメソッドは、メディア プロセッサ インスタンスを取得する方法を示しています。 このコード例では、 **_context** という名前のモジュール レベルの変数の使用を前提にしてサーバー コンテキストを参照しています。これは、[Media Services にプログラムから接続する方法](media-services-use-aad-auth-to-access-ams-api.md)に関するセクションで説明されています。

```csharp
private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}
```

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>次の手順

これで、メディア プロセッサ インスタンスを取得する方法がわかりました。次は、[資産のエンコード方法](media-services-dotnet-encode-with-media-encoder-standard.md)に関するトピックに進んでください。このトピックでは、Media Encoder Standard を使用して資産をエンコードする方法を説明します。