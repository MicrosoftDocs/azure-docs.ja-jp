---
title: Azure Cosmos DB でのクロス オリジン リソース共有 (CORS)
description: この記事では、Azure portal と Azure Resource Manager テンプレートを使用して、Azure Cosmos DB でのクロス オリジン リソース共有 (CORS) を構成する方法について説明します。
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2018
ms.author: dech
ms.openlocfilehash: 689b0bb08e300efc7c5e93dbf346040b82a7c52d
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468480"
---
# <a name="configure-cross-origin-resource-sharing-cors"></a>クロスオリジン リソース共有 (CORS) の構成 

クロス オリジン リソース共有 (CORS) は、あるドメインで実行されている Web アプリケーションが別のドメイン内にあるリソースにアクセスできるようにする HTTP 機能です。 Web ブラウザーでは、Web ページから別のドメインの API を呼び出すことを防ぐために、同一オリジン ポリシーと呼ばれるセキュリティ制限が実装されています。 ただし、CORS を使用すれば、元のドメインから別のドメインの API を安全に呼び出すことができます。Azure Cosmos DB のコア SQL API では、"allowedOrigins" ヘッダーを使用することで、クロス オリジン リソース共有 (CORS) がサポートされるようになりました。 Azure Cosmos アカウントに対して CORS のサポートを有効すると、認証済みの要求だけが指定されたルールに従って評価され、それらが許可されるかどうかが判断されます。

クロス オリジン リソース共有 (CORS) の設定は、Azure portal または Azure Resource Manager テンプレートから構成できます。 Azure Cosmos DB のコア SQL API では、Node.js とブラウザーベースの両方の環境で動作する JavaScript ライブラリがサポートされています。 このライブラリでは、ゲートウェイ モードの使用時に CORS サポートを利用できるようになりました。 この機能を利用するために、クライアント側で必要となる構成はありません。 CORS サポートを利用すると、ブラウザー側のリソースから、[JavaScript ライブラリ](https://www.npmjs.com/package/@azure/cosmos)を通じて Azure Cosmos DB に直接アクセスしたり、[REST API](https://docs.microsoft.com/rest/api/cosmos-db/) から直接アクセスできるようにして、操作を簡素化することができます。 

## <a name="enable-cors-support-from-azure-portal"></a>Azure portal から CORS サポートを有効にする

Azure portal を使用してクロス オリジン リソース共有を有効にするには、次の手順を使用します。

1. Azure Cosmos DB アカウントに移動します。 **[CORS]** ブレードを開きます。

2. Azure Cosmos DB アカウントへのクロス オリジン呼び出しをできるようにするオリジンの、コンマ区切りリストを指定します。 たとえば、`https://www.mydomain.com`、`https://mydomain.com`、`https://api.mydomain.com` などです。 ワイルドカード (\*) を使用してすべてのオリジンを許可し、**[送信]** を選択することもできます。 

   > [!NOTE]
   > 現在、ドメイン名の一部にワイルドカードを使用することはできません。 たとえば、`https://*.mydomain.net` のような形式はまだサポートされていません。 
   
   ![Azure portal を使用してクロス オリジン リソース共有を有効にする](./media/how-to-configure-cross-origin-resource-sharing/enable-cross-origin-resource-sharing-using-azure-portal.png)
 
## <a name="enable-cors-support-from-resource-manager-template"></a>Resource Manager テンプレートから CORS サポートを有効にする

Resource Manager テンプレートを使用して CORS を有効にするには、既存のテンプレートに、"allowedOrigins" プロパティを含んだ "cors" セクションを追加します。 次の JSON は、cors を有効にした新しい Azure Cosmos アカウントを作成するテンプレートの例です。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "name": "test",
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "apiVersion": "2015-04-08",
            "location": "East US 2",
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": {
                    "defaultConsistencyLevel": "Session",
                    "maxIntervalInSeconds": 5,
                    "maxStalenessPrefix": 100
                },
                "locations": [
                    {
                        "id": "test-eastus2",
                        "failoverPriority": 0,
                        "locationName": "East US 2"
                    }
                ],
                "cors": [
                    {
                        "allowedOrigins": "*"
                    }
                ]
            },
            "dependsOn": [
            ]
        }
    ]
}
```

## <a name="using-the-azure-cosmos-db-javascript-library-from-a-browser"></a>ブラウザーから Azure Cosmos DB JavaScript ライブラリを使用する

現在、Azure Cosmos DB JavaScript ライブラリのパッケージには、CommonJS バージョンのライブラリのみが含まれています。 このライブラリをブラウザーから使用するには、Rollup や Webpack などのツールを使用してブラウザー互換ライブラリを作成する必要があります。 特定の Node.js ライブラリには、専用のブラウザー モックが必要になります。 次に示すのは、必要なモック設定を含んだ Webpack 構成ファイルのサンプルです。

```javascript
const path = require("path");

module.exports = {
  entry: "./src/index.ts",
  devtool: "inline-source-map",
  node: {
    net: "mock",
    tls: "mock"
  },
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist")
  }
};
```
 
こちらの[コード サンプル](https://github.com/christopheranderson/cosmos-browser-sample)では、TypeScript と Webpack を Azure Cosmos DB JavaScript SDK ライブラリと共に使用して、新しい項目の作成時にリアルタイムで更新を送信する Todo アプリを構築しています。
ベスト プラクティスとして、ブラウザーから Azure Cosmos DB と通信するために、主キーを使用することはしないでください。 代わりに、リソース トークンを使用して通信を行ってください。 リソース トークンについて詳しくは、「[Azure Cosmos DB のデータへのアクセスのセキュリティ保護](secure-access-to-data.md#resource-tokens)」をご覧ください。

## <a name="next-steps"></a>次の手順

Azure Cosmos アカウントを保護するその他の方法については、次の記事をご覧ください。

* [Azure Cosmos DB 用のファイアウォールの作成](how-to-configure-firewall.md)に関する記事。

* [Azure Cosmos DB アカウント用の仮想ネットワークとサブネット ベースのアクセスを構成する](how-to-configure-vnet-service-endpoint.md)
    

