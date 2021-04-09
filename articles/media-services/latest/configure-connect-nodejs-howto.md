---
title: Azure Media Services v3 API に接続する - Node.js
description: この記事では、Node.js を使用して Media Services v3 API に接続する方法を示します。
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
ms.date: 02/17/2021
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: 33d84ca86ac3cd4696dce3797b015b861884182a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102216430"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Media Services v3 API に接続する - Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

この記事では、サービス プリンシパルによるサインイン方式を使用して Azure Media Services v3 Node.js SDK に接続する方法を説明します。 *media-services-v3-node-tutorials* サンプルのリポジトリにあるファイルを使用します。 *HelloWorld-ListAssets* サンプルには、接続してからアカウントの資産を一覧表示するためのコードが含まれています。

## <a name="prerequisites"></a>前提条件

- Visual Studio Code のインストール。
- [Node.js](https://nodejs.org/en/download/) をインストールします。
- [Typescript](https://www.typescriptlang.org/download) をインストールします。
- [Media Services アカウントを作成する](./create-account-howto.md) リソース グループ名と Media Services アカウント名を覚えておいてください。
- アプリケーションのサービス プリンシパルを作成します。 [API へのアクセスする](./access-api-howto.md)に関するページを参照してください。<br/>**Pro ヒント!** このウィンドウを開いたままにするか、JSON タブ内のすべてをメモ帳にコピーします。 
- [AzureMediaServices SDK for JavaScript](https://www.npmjs.com/package/@azure/arm-mediaservices) の最新バージョンを入手していることを確認してください。

> [!IMPORTANT]
> エンティティの名前付けに関する重要な制限事項を理解するには、Azure Media Services の「[名前付け規則](media-services-apis-overview.md#naming-conventions)」を確認してください。

## <a name="clone-the-nodejs-samples-repo"></a>Node.JS のサンプル リポジトリを複製する

Azure のサンプルのいくつかのファイルを使用します。 Node.JS のサンプル リポジトリを複製します。

```git
git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
```

## <a name="install-the-packages"></a>パッケージのインストール

### <a name="install-azurearm-mediaservices"></a>@azure/arm-mediaservices のインストール

```bash
npm install @azure/arm-mediaservices
```

### <a name="install-azurems-rest-nodeauth"></a>@azure/ms-rest-nodeauth のインストール

"@azure/ms-rest-nodeauth" の最小バージョン ("^3.0.0") をインストールしてください。

```bash
npm install @azure/ms-rest-nodeauth@"^3.0.0"
```

この例では、`package.json` ファイル内の次のパッケージを使用します。

|Package|説明|
|---|---|
|`@azure/arm-mediaservices`|Azure Media Services SDK。 <br/>最新の Azure Media Services パッケージを使用していることを確認するには、[npm install @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices) を確認してください。|
|`@azure/ms-rest-nodeauth` | サービス プリンシパルまたはマネージド ID を使用した AAD 認証に必要です|
|`@azure/storage-blob`|Storage SDK。 ファイルを資産にアップロードするときに使用します。|
|`@azure/ms-rest-js`| サインインするために使用します。|
|`@azure/storage-blob` | エンコードのために Azure Media Services の資産にファイルをアップロードおよびダウンロードするために使用します。|
|`@azure/abort-controller`| 長時間実行されるダウンロード操作をタイムアウトにするために、ストレージ クライアントと共に使用されます|

### <a name="create-the-packagejson-file"></a>package.json ファイルを作成する

1. 好みのエディターを使用して `package.json` ファイルを作成します。
1. ファイルを開き、次のコードを貼り付けます。

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.ts",
  "dependencies": {
    "@azure/arm-mediaservices": "^8.0.0",
    "@azure/abort-controller": "^1.0.2",
    "@azure/ms-rest-nodeauth": "^3.0.6",
    "@azure/storage-blob": "^12.4.0",
  }
}
```

## <a name="connect-to-nodejs-client-using-typescript"></a>TypeScript を使用して Node.js クライアントに接続する



### <a name="sample-env-file"></a>サンプルの *.env* ファイル

このファイルの内容を、" *.env*" という名前のファイルにコピーします。 これは、作業リポジトリのルートに格納されている必要があります。 これらは、ポータルの Media Services アカウントの [API アクセス] ページから取得した値です。

" *.env*" ファイルを作成したら、サンプルの使用を開始できます。

```nodejs
# Values from the API Access page in the portal
AZURE_CLIENT_ID=""
AZURE_CLIENT_SECRET= ""
AZURE_TENANT_ID= ""

# Change this to match your AAD Tenant domain name. 
AAD_TENANT_DOMAIN = "microsoft.onmicrosoft.com"

# Set this to your Media Services Account name, resource group it is contained in, and location
AZURE_MEDIA_ACCOUNT_NAME = ""
AZURE_LOCATION= ""
AZURE_RESOURCE_GROUP= ""

# Set this to your Azure Subscription ID
AZURE_SUBSCRIPTION_ID= ""

# You must change these if you are using Gov Cloud, China, or other non standard cloud regions
AZURE_ARM_AUDIENCE= "https://management.core.windows.net"
AZURE_ARM_ENDPOINT="https://management.azure.com"

# DRM Testing
DRM_SYMMETRIC_KEY="add random base 64 encoded string here"
```

## <a name="run-the-sample-application-helloworld-listassets"></a>サンプル アプリケーション *HelloWorld-ListAssets* を実行する

1. *AMSv3Samples* フォルダーに移動します

```bash
cd AMSv3Samples
```

2. *packages.json* ファイルに使用されているパッケージをインストールします。

```
npm install 
```

3. *HelloWorld-ListAssets* フォルダーに移動します。

```bash
cd HelloWorld-ListAssets
```

4. AMSv3Samples フォルダーから Visual Studio Code を起動します。 これは、".vscode" フォルダーと tsconfig.json ファイルが格納されているフォルダーから起動するために必要です

```dotnetcli
cd ..
code .
```

*HelloWorld-ListAssets* のフォルダーを開き、Visual Studio Code エディターで *index.ts* ファイルを開きます。

*index.ts* ファイルで F5 キーを押してデバッガーを起動します。 アカウントに資産が既に存在する場合は、表示される資産の一覧が表示されます。 アカウントが空の場合は、空の一覧が表示されます。  

一覧表示される資産をすばやく確認するには、ポータルを使用して、いくつかのビデオ ファイルをアップロードします。 資産はそれぞれ自動的に作成され、このスクリプトを再度実行すると、それらの名前が返されます。

### <a name="a-closer-look-at-the-helloworld-listassets-sample"></a>*HelloWorld-ListAssets* サンプルの詳細

*HelloWorld-ListAssets* サンプルでは、サービス プリンシパルを使用して Media Services クライアントに接続し、アカウントの資産を一覧表示する方法が示されています。 実行される内容の詳細については、コード内のコメントを参照してください。

```ts
import * as msRestNodeAuth from "@azure/ms-rest-nodeauth";
import { AzureMediaServices } from '@azure/arm-mediaservices';
import { AzureMediaServicesOptions } from "@azure/arm-mediaservices/esm/models";
// Load the .env file if it exists
import * as dotenv from "dotenv";
dotenv.config();

export async function main() {
  // Copy the samples.env file and rename it to .env first, then populate it's values with the values obtained 
  // from your Media Services account's API Access page in the Azure portal.
  const clientId = process.env.AZURE_CLIENT_ID as string;
  const secret = process.env.AZURE_CLIENT_SECRET as string;
  const tenantDomain = process.env.AAD_TENANT_DOMAIN as string;
  const subscriptionId = process.env.AZURE_SUBSCRIPTION_ID as string;
  const resourceGroup = process.env.AZURE_RESOURCE_GROUP as string;
  const accountName = process.env.AZURE_MEDIA_ACCOUNT_NAME as string;

  let clientOptions: AzureMediaServicesOptions = {
    longRunningOperationRetryTimeout: 5, // set the time out for retries to 5 seconds
    noRetryPolicy: false // use the default retry policy.
  }

  const creds = await msRestNodeAuth.loginWithServicePrincipalSecret(clientId, secret, tenantDomain);
  const mediaClient = new AzureMediaServices(creds, subscriptionId, clientOptions);

  // List Assets in Account
  console.log("Listing Assets Names in account:")
  var assets = await mediaClient.assets.list(resourceGroup, accountName);

  assets.forEach(asset => {
    console.log(asset.name);
  });

  if (assets.odatanextLink) {
    console.log("There are more than 1000 assets in this account, use the assets.listNext() method to continue listing more assets if needed")
    console.log("For example:  assets = await mediaClient.assets.listNext(assets.odatanextLink)");
  }
}

main().catch((err) => {
  console.error("Error running sample:", err.message);
});
```

## <a name="more-samples"></a>その他のサンプル

次のサンプルは、[リポジトリ](https://github.com/Azure-Samples/media-services-v3-node-tutorials)で入手可能です

|プロジェクト名|ユース ケース|
|---|---|
|Live/index.ts| ライブ ストリーミングの基本的な例。 **警告**: ライブを使用する場合は、すべてのリソースがクリーンアップされていて、ポータルで課金されなくなっていることを確認してください|
|StreamFilesSample/index.ts| ソース URL からローカル ファイルまたはエンコードをアップロードするための基本的な例。 サンプルでは、Storage SDK を使用してコンテンツをダウンロードする方法と、プレーヤーにストリーミングする方法が示されています |
|StreamFilesWithDRMSample/index.ts| Widevine と PlayReady DRM を使用したエンコードとストリーミングの方法を示しています |
|VideoIndexerSample/index.ts| ビデオおよびオーディオ アナライザーのプリセットを使用して、ビデオまたはオーディオ ファイルからメタデータと分析情報を生成する例 |

## <a name="see-also"></a>関連項目

- [Node.js 用 Azure Media Services モジュールのリファレンス ドキュメント](/javascript/api/overview/azure/media-services)
- [JavaScript および Node.js 開発者向けの Azure](/azure/developer/javascript/)
- [@azure/azure-sdk-for-js GitHub リポジトリにおける Media Services のソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Node.js 開発者向けの Azure パッケージのドキュメント](/javascript/api/overview/azure/)
- [Media Services の概念](concepts-overview.md)
- [npm install @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [JavaScript および Node.js 開発者向けの Azure](/azure/developer/javascript/)
- [@azure/azure-sdk-for-js リポジトリにおける Media Services のソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>次のステップ

Media Services の [Node.js リファレンス](/javascript/api/overview/azure/mediaservices/management) ドキュメントを調べて、node.js で Media Services API を使用する方法が示されている[サンプル](https://github.com/Azure-Samples/media-services-v3-node-tutorials)をご覧ください。
