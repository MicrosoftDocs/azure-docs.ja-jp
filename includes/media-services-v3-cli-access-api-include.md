---
title: インクルード ファイル
description: インクルード ファイル
services: media-services
author: juliako
ms.service: media-services
ms.topic: include
ms.date: 05/01/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: b0f93f950b55052ea8d8b31538c47226413dc82a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181266"
---
## <a name="access-the-media-services-api"></a>Media Services API にアクセスする

Azure Media Services API に接続するには、Azure AD サービス プリンシパル認証を使用します。 以下のコマンドでは、Azure AD アプリケーションを作成し、そのアカウントにサービス プリンシパルをアタッチしています。 返された値を使って、アプリケーションを構成する必要があります。

スクリプトを実行する前に、`amsaccount` と `amsResourceGroup` を、これらのリソースを作成するときに選んだ名前に置き換える必要があります。 `amsaccount` は、サービス プリンシパルのアタッチ先となる Azure Media Services アカウントの名前です。

次のコマンドは、`json` の出力を返します。

```azurecli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup
```

このコマンドからは、次のような応答が生成されます。

```json
{
  "AadClientId": "00000000-0000-0000-0000-000000000000",
  "AadEndpoint": "https://login.microsoftonline.com",
  "AadSecret": "00000000-0000-0000-0000-000000000000",
  "AadTenantId": "00000000-0000-0000-0000-000000000000",
  "AccountName": "amsaccount",
  "ArmAadAudience": "https://management.core.windows.net/",
  "ArmEndpoint": "https://management.azure.com/",
  "Region": "West US 2",
  "ResourceGroup": "amsResourceGroup",
  "SubscriptionId": "00000000-0000-0000-0000-000000000000"
}
```

応答で `xml` を取得したい場合は、次のコマンドを使います。

```azurecli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```
