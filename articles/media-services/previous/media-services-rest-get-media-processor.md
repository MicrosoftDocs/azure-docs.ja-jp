---
title: REST を使用してメディア プロセッサ インスタンスを取得する方法 | Microsoft Docs
description: メディア プロセッサ コンポーネントを作成し、Azure Media Services 用にメディア コンテンツのエンコード、形式の変換、暗号化、または復号化を行う方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: f9ff1997-0da6-4528-aaed-792837e5be41
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 7eb4647ae5ba40688cbf39cbfe8449f59275e7e6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014317"
---
# <a name="how-to-get-a-media-processor-instance"></a>方法: メディア プロセッサ インスタンスを取得する

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)


## <a name="overview"></a>概要

メディア プロセッサは、メディア コンテンツのエンコード、形式変換、暗号化、または復号化などの特定のビデオまたはオーディオ処理タスクを扱うコンポーネントです。 Media Services に送信されるすべてのタスクは、メディア プロセッサでビデオまたはオーディオ コンテンツのエンコード、暗号化、または変換を行う必要があります。

## <a name="azure-media-processors"></a>Azure メディア プロセッサ

次のトピックは、メディア プロセッサの一覧です。

* [Encoding メディア プロセッサ](scenarios-and-availability.md)
* [Analytics メディア プロセッサ](scenarios-and-availability.md)

>[!NOTE]
>Media Services でエンティティにアクセスするときは、HTTP 要求で特定のヘッダー フィールドと値を設定する必要があります。 詳細については、「 [Media Services REST API の概要](media-services-rest-how-to-use.md)」をご覧ください。

## <a name="connect-to-media-services"></a>Media Services への接続

AMS API に接続する方法については、「[Azure AD 認証を使用した Azure Media Services API へのアクセス](media-services-use-aad-auth-to-access-ams-api.md)」を参照してください。 


## <a name="get-a-media-processor"></a>メディア プロセッサを取得する

次の REST 呼び出しは、メディア プロセッサ インスタンスを名前 (ここでは **Media Encoder Standard**) で取得する方法を示しています。 

要求:

```console
GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <token>
x-ms-version: 2.19
Host: media.windows.net
```

応答:

```console
. . .

{  
   "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
   "value":[  
      {  
         "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
         "Description":"Media Encoder Standard",
         "Name":"Media Encoder Standard",
         "Sku":"",
         "Vendor":"Microsoft",
         "Version":"1.1"
      }
   ]
}
```


## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>次の手順
これで、メディア プロセッサ インスタンスを取得する方法がわかりました。次は、[資産のエンコード方法](media-services-rest-get-started.md)に関する記事に進んでください。この記事では、Media Encoder Standard を使用して資産をエンコードする方法を説明します。

