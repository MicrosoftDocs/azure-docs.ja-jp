---
title: Azure Media Services に認証トークンを渡す | Microsoft Docs
description: クライアントから Azure Media Services キー配信サービスに認証トークンを送信する方法をについて説明します
services: media-services
keywords: コンテンツ保護, DRM, トークン認証
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: ba4daec8ef5f2cc1da3a211113e7236dfd22803d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008245"
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>クライアントが Azure Media Services キー配信サービスにトークンを渡す方法

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

プレーヤーがキーを取得できるよう検証を受けるために Azure Media Services キー配信サービスにトークンを渡す方法についてお客様から質問を受けることがよくあります。 Media Services は、単純 Web トークン (SWT) 形式と JSON Web トークン (JWT) 形式をサポートしています。 システムで共通暗号化または Advanced Encryption Standard (AES) エンベロープ暗号化のどちらが使われていても、すべての種類のキーにトークン認証が適用されます。

 対象のプレーヤーとプラットフォームに応じて、プレーヤーは次の方法でトークンを渡すことができます。

- HTTP の Authorization ヘッダーを使います。
    > [!NOTE]
    > OAuth 2.0 仕様に従い、"Bearer" プレフィックスが予期されます。 トークンが構成されたサンプル プレーヤーが、Azure Media Player の[デモ ページ](https://ampdemo.azureedge.net/)でホストされています。 ビデオ ソースを設定するには、**AES (JWT トークン)** または **AES (SWT トークン)** を選んでください。 トークンは Authorization ヘッダーで渡されます。

- "token=tokenvalue" で URL クエリ パラメーターを追加します。  
    > [!NOTE]
    > "Bearer" プレフィックスは想定されていません。 トークンは URL で送信されるので、トークン文字列の防御が必要です。 これを行う方法の C# サンプル コードを次に示します。

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- CustomData フィールドを使います。
このオプションは PlayReady ライセンス取得の場合だけであり、PlayReady ライセンス取得チャレンジの CustomData フィールドを使います。 この場合、トークンは次に示す XML ドキュメントの内部に存在する必要があります。

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    認証トークンを Token 要素の中に置きます。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
