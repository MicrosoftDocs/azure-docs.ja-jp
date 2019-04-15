---
title: Azure Media Services v3 API に接続する - Node.js
description: Node.js を使用して Media Services v3 API に接続する方法について学習します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: juliako
ms.openlocfilehash: fbea3daec3d9ccac81f246d3dc762fc80cb4b18a
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759231"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Media Services v3 API に接続する - Node.js

この記事では、サービス プリンシパルによるサインイン方式を使用して Azure Media Services v3 Node.js SDK に接続する方法を説明します。

## <a name="prerequisites"></a>前提条件

- [Node.js](https://nodejs.org/en/download/) をインストールします。
- [Media Services アカウントを作成する](create-account-cli-how-to.md) リソース グループ名と Media Services アカウント名を覚えておいてください。

## <a name="create-packagejson"></a>package.json を作成する

1. 好みのエディターを使用して package.json ファイルを作成します。
1. ファイルを開き、次のコードを貼り付けます。

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.js",
  "dependencies": {
    "azure-arm-mediaservices": "^4.1.0",
    "azure-storage": "^2.8.0",
    "ms-rest": "^2.3.3",
    "ms-rest-azure": "^2.5.5"
  }
}
```

次のパッケージを指定する必要があります。

|Package|説明|
|---|---|
|`azure-arm-mediaservices`|Azure Media Services SDK。 <br/>最新の Azure Media Services パッケージを使用していることを確認するには、[NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/) をチェックします。|
|`azure-storage`|Storage SDK。 ファイルを資産にアップロードするときに使用します。|
|`ms-rest-azure`| サインインするために使用します。|

最新のパッケージを使用していることを確認するには、次のコマンドを実行します。

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Node.js クライアントに接続する

1. 好みのエディターを使用して .js ファイルを作成します。
1. ファイルを開き、次のコードを貼り付けます。
1. "endpoint config" セクションの値を、[アクセス API](access-api-cli-how-to.md) から取得した値に設定します。

```js
'use strict';

const MediaServices = require('azure-arm-mediaservices');
const msRestAzure = require('ms-rest-azure');
const msRest = require('ms-rest');
const azureStorage = require('azure-storage');

// endpoint config
// make sure your URL values end with '/'
const armAadAudience = "";
const aadEndpoint = "";
const armEndpoint = "";
const subscriptionId = "";
const accountName = "";
const region = "";
const aadClientId = "";
const aadSecret = "";
const aadTenantId = "";
const resourceGroup = "";

let azureMediaServicesClient;

///////////////////////////////////////////
//     Entrypoint for sample script      //
///////////////////////////////////////////

msRestAzure.loginWithServicePrincipalSecret(aadClientId, aadSecret, aadTenantId, {
  environment: {
    activeDirectoryResourceId: armAadAudience,
    resourceManagerEndpointUrl: armEndpoint,
    activeDirectoryEndpointUrl: aadEndpoint
  }
}, async function(err, credentials, subscriptions) {
    if (err) return console.log(err);
    azureMediaServicesClient = new MediaServices(credentials, subscriptionId, armEndpoint, { noRetryPolicy: true });
    
    console.log("connected");

});
```

## <a name="run-your-app"></a>アプリケーションを実行する

コマンド プロンプトを開きます。 サンプルのディレクトリに移動して、以下のコマンドを実行します。

```
npm install 
node index.js
```

## <a name="see-also"></a>関連項目

[.NET リファレンス](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)

## <a name="next-steps"></a>次の手順

- [Media Services の概念](concepts-overview.md)
- [クイックスタート]()
- [Node.js リファレンス](https://docs.microsoft.com/javascript/api/azure-arm-mediaservices/?view=azure-node-latest)
- [NPM インストール azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)<br>
