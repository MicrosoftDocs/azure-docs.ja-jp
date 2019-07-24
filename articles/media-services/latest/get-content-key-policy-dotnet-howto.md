---
title: Media Services v3 .NET SDK を使用して既存のポリシーから署名キーを取得する - Azure | Microsoft Docs
description: このトピックでは、Media Services v3 .NET SDK を使用して既存のポリシーから署名キーを取得する方法について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 58b6f49f4bbbd93fefb9b616f92baf7ef30f7deb
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2019
ms.locfileid: "59615831"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>既存のポリシーから署名キーを取得する

v3 API の主要な設計原則の 1 つは、API の安全性の向上です。 v3 API シリーズは、**Get** または **List** 操作でシークレットまたは資格情報を返しません。 詳細については、次を参照してください。詳細については、[RBAC と Media Services アカウント](rbac-overview.md)に関するページをご覧ください。

この記事の例では、.NET を使用して既存のポリシーから署名キーを取得する方法を示します。 
 
## <a name="download"></a>ダウンロード 

次のコマンドを使って、.NET サンプルが含まれる GitHub リポジトリを、ご利用のコンピューターに複製します。  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
シークレットを使用した ContentKeyPolicy の例は、[EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) フォルダーにあります。

## <a name="get-contentkeypolicy-with-secrets"></a>シークレットを使用した ContentKeyPolicy を取得する 

キーを取得するには、次の例に示すように **GetPolicyPropertiesWithSecretsAsync** を使用します。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>次の手順

[アクセス制御を使用したマルチ DRM コンテンツ保護システムの設計](design-multi-drm-system-with-access-control.md) 
