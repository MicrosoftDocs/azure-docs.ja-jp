---
title: ポリシーから署名キーを取得する .NET
description: このトピックでは、Media Services v3 .NET SDK を使用して既存のポリシーから署名キーを取得する方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 71d4b00d3ebb56d72cbb16cd42394c720f29849f
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277645"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>既存のポリシーから署名キーを取得する

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

v3 API の主要な設計原則の 1 つは、API の安全性の向上です。 v3 API シリーズは、**Get** または **List** 操作でシークレットまたは資格情報を返しません。 詳細については、次を参照してください。詳細については、[Azure RBAC と Media Services アカウント](security-rbac-concept.md)に関するページをご覧ください。

この記事の例では、.NET を使用して既存のポリシーから署名キーを取得する方法を示します。 
 
## <a name="download"></a>ダウンロード 

次のコマンドを使って、.NET サンプルが含まれる GitHub リポジトリを、ご利用のコンピューターにクローンします。  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
シークレットを使用した ContentKeyPolicy の例は、[EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) フォルダーにあります。

## <a name="get-contentkeypolicy-with-secrets"></a>シークレットを使用した ContentKeyPolicy を取得する 

キーを取得するには、次の例に示すように **GetPolicyPropertiesWithSecretsAsync** を使用します。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>次のステップ

[アクセス制御を使用したマルチ DRM コンテンツ保護システムの設計](architecture-design-multi-drm-system.md) 
