---
title: クイック スタート:Node.js 用 Azure 管理クライアント ライブラリ
description: このクイックスタートでは、Node.js 用の Azure 管理クライアント ライブラリの使用を開始します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: fcfefb2b6cd5c23e9b77d7d27413d1ff6d775bfe
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89321621"
---
[リファレンスのドキュメント](https://docs.microsoft.com/javascript/api/@azure/arm-cognitiveservices/?view=azure-node-latest) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices) | [パッケージ (NPM)](https://www.npmjs.com/package/@azure/arm-cognitiveservices) | [サンプル](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices#sample-code)

## <a name="prerequisites"></a>前提条件

* 有効な Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)。
* 最新バージョンの [Node.js](https://nodejs.org/)

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、ご利用のアプリ用に新しいディレクトリを作成し、そこに移動します。 

```console
mkdir myapp && cd myapp
```

`npm init` コマンドを実行し、`package.json` ファイルを使用して node アプリケーションを作成します。 

```console
npm init
```

先に進む前に、_index.js_ というファイルを作成します。

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

次の NPM パッケージをインストールします。

```console
npm install @azure/arm-cognitiveservices
npm install @azure/ms-rest-js
npm install @azure/ms-rest-nodeauth
```

アプリの `package.json` ファイルが依存関係によって更新されます。

### <a name="import-libraries"></a>ライブラリをインポートする

_index.js_ スクリプトを開き、以下のライブラリをインポートします。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_imports)]

## <a name="authenticate-the-client"></a>クライアントを認証する

スクリプトのルートに以下のフィールドを追加し、作成したサービス プリンシパルと Azure アカウント情報を使用して値を指定します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_constants)]

次に、プログラムの主要な作業を処理するために、次の `quickstart` 関数を追加します。 最初のコード ブロックでは、前に入力した資格情報変数を使用して、**CognitiveServicesManagementClient** オブジェクトを構築します。 このオブジェクトは、すべての Azure 管理操作に必要です。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main_auth)]

## <a name="call-management-functions"></a>管理関数を呼び出す

`quickstart` 関数の末尾に次のコードを追加して、使用可能なリソースの一覧表示、サンプル リソースの作成、所有しているリソースの一覧表示、およびサンプル リソースの削除を行います。 これらの関数は、以降の手順で定義します。

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services リソースの作成

### <a name="choose-a-service-and-pricing-tier"></a>サービスと価格レベルを選択する

新しいリソースを作成するときには、使用するサービスの "種類" と、必要な[価格レベル](https://azure.microsoft.com/pricing/details/cognitive-services/) (つまり SKU) を把握する必要があります。 リソースを作成するときに、この情報と他の情報をパラメーターとして使用します。 次の関数は、利用可能な Cognitive Service の "種類" を一覧表示します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services リソースの作成

新しい Cognitive Services リソースを作成してサブスクライブするには、**Create** 関数を使用します。 この関数で、渡したリソース グループに新しい課金対象リソースが追加されます。 新しいリソースを作成するときには、使用するサービスの "種類"、その価格レベル (つまり SKU)、および Azure の場所を把握している必要があります。 次の関数は、これらのすべての引数を受け取り、リソースを作成します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_create)]

## <a name="view-your-resources"></a>リソースを表示する

Azure アカウントのすべてのリソース (すべてのリソース グループにわたる) を表示するには、次の関数を使用します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list)]

## <a name="delete-a-resource"></a>リソースの削除

次の関数は、特定のリソース グループから指定されたリソースを削除します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_delete)]

## <a name="run-the-application"></a>アプリケーションの実行

スクリプトの末尾に以下のコードを追加して、エラー処理付きのメイン `quickstart` 関数を呼び出します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main)]

次に、コンソール ウィンドウで `node` コマンドを使用して、アプリケーションを実行します。

```console
node index.js
```

## <a name="see-also"></a>関連項目

* [Azure Management SDK のリファレンス ドキュメント](https://docs.microsoft.com/javascript/api/@azure/arm-cognitiveservices/?view=azure-node-latest)
* [Azure Cognitive Services とは](../../Welcome.md)
* [Azure Cognitive Services に対する要求の認証](../../authentication.md)
* [Azure portal を使用して新しいリソースを作成する](../../cognitive-services-apis-create-account.md)