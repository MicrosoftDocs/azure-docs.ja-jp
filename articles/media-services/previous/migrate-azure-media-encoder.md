---
title: Azure Media Encoder から Media Encoder Standard に移行する | Microsoft Docs
description: このトピックでは、Azure Media Encoder から Media Encoder Standard メディア プロセッサに移行する方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 4f528cea9b475c0158524ad9b46623a78df5761d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103016204"
---
# <a name="migrate-from-azure-media-encoder-to-media-encoder-standard"></a>Azure Media Encoder から Media Encoder Standard に移行する

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

この記事では、(廃止される) 従来の Azure Media Encoder (AME) メディア プロセッサから Media Encoder Standard メディア プロセッサに移行する手順について説明します。 提供終了日については、この[レガシ コンポーネント](legacy-components.md)に関するトピックを参照してください。

AME でファイルをエンコードする場合、通常は `H264 Adaptive Bitrate MP4 Set 1080p` などの名前付きプリセット文字列が使用されます。 移行するには、AME の代わりに **Media Encoder Standard** メディア プロセッサを使用し、いずれかの同等の [システム プリセット](media-services-mes-presets-overview.md) (`H264 Multiple Bitrate 1080p` など) を使用するようにコードを更新する必要があります。 

## <a name="migrating-to-media-encoder-standard"></a>Media Encoder Standard への移行

レガシ メディア プロセッサを使用する一般的な C# コードの例を次に示します。 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("AME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    " H264 Adaptive Bitrate MP4 Set 1080p", 
    TaskOptions.None); 
```

Media Encoder Standard を使用する更新されたバージョンを次に示します。

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("Media Encoder Standard Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Multiple Bitrate 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Multiple Bitrate 1080p", 
    TaskOptions.None); 
```

### <a name="advanced-scenarios"></a>高度なシナリオ 

そのスキーマを使用して AME 用に独自のエンコード プリセットを作成済みの場合は、[Media Encoder Standard 用の同等のスキーマ](media-services-mes-schema.md)があります。 古い設定を新しいエンコーダーにマップする方法についてご質問がある場合は、 mailto:amshelp@microsoft.com にお問い合わせください。  
## <a name="known-differences"></a>既知の相違点 

Media Encoder Standard は、従来の AME エンコーダーよりも堅牢であり、信頼性が高く、パフォーマンスが向上し、高品質の出力を生成します。 さらに: 

* Media Encoder Standard で生成される出力ファイルでは、AME とは異なる名前付け規則が使用されます。
* Media Encoder Standard では、[入力ファイルのメタデータ](media-services-input-metadata-schema.md)と[出力ファイルのメタデータ](media-services-output-metadata-schema.md)を含むファイルなどの成果物が生成されます。

## <a name="next-steps"></a>次のステップ

* [レガシ コンポーネント](legacy-components.md)
* [価格ページ](https://azure.microsoft.com/pricing/details/media-services/#encoding)
