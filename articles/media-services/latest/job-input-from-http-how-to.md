---
title: HTTPS URL から Azure Media Services ジョブの入力を作成する | Microsoft Docs
description: このトピックでは、HTTP(S) URL から、ジョブの入力を作成する方法を示します。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2019
ms.author: juliako
ms.openlocfilehash: f6eee912bb3bba112bd13969f1a8d9cb5748e387
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65413828"
---
# <a name="create-a-job-input-from-an-https-url"></a>HTTPS URL からジョブの入力を作成する

Media Services v3 では、ビデオを処理するジョブを送信するときに、入力ビデオを検索する場所を Media Services に指示する必要があります。 選択肢の 1 つは、HTTPS URL をジョブの入力として指定することです (この例で示すように)。 現在、AMS v3では、HTTPS URLを介したチャンク転送のエンコーディングはサポートされていません。 完全な例については、こちらの [GitHub サンプル](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)を参照してください。

> [!TIP]
> 開発を開始する前に、[Media Services v3 API を使用した開発](media-services-apis-overview.md)に関するページを確認してください。API へのアクセス、名前付け規則などに関する情報が記載されています。

## <a name="net-sample"></a>.Net のサンプル

次のコードは、HTTPS URL 入力を使用してジョブを作成する方法を示しています。

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>ジョブ エラー コード

[エラー コード](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

[ローカル ファイルからジョブの入力を作成する](job-input-from-local-file-how-to.md)。
