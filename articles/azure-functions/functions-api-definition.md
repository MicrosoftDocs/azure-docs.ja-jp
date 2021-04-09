---
title: Azure Functions の OpenAPI メタデータ
description: Azure Functions での OpenAPI サポートの概要
ms.topic: conceptual
ms.date: 03/23/2017
ms.openlocfilehash: 499d4f685e3802fcc37c8a3050ae367207f192d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000209"
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Azure Functions での OpenAPI 2.0 メタデータのサポート (プレビュー)
Azure Functions での OpenAPI 2.0 (旧称 Swagger) メタデータ サポートは、関数アプリ内での OpenAPI 2.0 定義の作成に使用できるプレビュー機能です。 関数アプリを使用して、そのファイルをホストできます。

> [!IMPORTANT]
> 現在、OpenAPI のプレビュー機能は、1.x ランタイムでのみ利用できます。 1\.x 関数アプリを作成する方法については、[こちらにあります](./functions-versions.md#creating-1x-apps)。

[OpenAPI メタデータ](https://swagger.io/)では、REST API をホストしている関数を、他のさまざまなソフトウェアで使用できます。 このソフトウェアには、PowerApps や [Azure App Service の API Apps 機能](../app-service/overview.md)などの Microsoft 製品、[Postman](https://www.getpostman.com/docs/importing_swagger) などのサード パーティの開発者ツール、および[多くのその他のパッケージ](https://swagger.io/tools/)が含まれています。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>特定の機能の詳細について学習するには、[概要チュートリアル](./functions-openapi-definition.md)から始めて後でこのドキュメントに戻ることをお勧めします。

## <a name="enable-openapi-definition-support"></a><a name="enable"></a>OpenAPI 定義のサポートの有効化
すべての OpenAPI 設定を、関数アプリの **プラットフォーム機能** の **API の定義** ページで構成できます。

> [!NOTE]
> 現在、関数 API の定義機能は、ベータ版のランタイムではサポートされません。

ホストされる OpenAPI と定義およびクイック スタート定義の生成を有効にするには、 **[API 定義のソース]** を **[関数 (プレビュー)]** に設定します。 **外部 URL** を使用して、他の場所でホストされている OpenAPI 定義を自分の関数で使用できます。

## <a name="generate-a-swagger-skeleton-from-your-functions-metadata"></a><a name="generate-definition"></a>関数のメタデータからの Swagger スケルトンの生成
テンプレートは、最初の OpenAPI 定義の記述を開始するのに役立ちます。 定義テンプレート機能では、HTTP トリガー関数ごとに、function.json ファイル内のすべてのメタデータを使用してスパース OpenAPI 定義が作成されます。 要求や応答のテンプレートなど、[OpenAPI 仕様](https://swagger.io/specification/)から API に関する詳細情報を入力する必要があります。

手順については、この[概要チュートリアル](./functions-openapi-definition.md)を確認してください

### <a name="available-templates"></a><a name="templates"></a>使用可能なテンプレート

|名前| 説明 |
|:-----|:-----|
|Generated Definition (生成された定義)|関数の既存のメタデータから推論可能な情報量が最大の OpenAPI 定義|

### <a name="included-metadata-in-the-generated-definition"></a><a name="quickstart-details"></a>生成された定義に含まれるメタデータ

次の表は、Azure ポータル設定と、生成された Swagger スケルトンにマップされたときの function.json 内の対応するデータを表します。

|Swagger.json|ポータル UI|Function.json|
|:----|:-----|:-----|
|[Host](https://swagger.io/specification/#fixed-fields-15)|**関数アプリの設定** > **App Service の設定** > **概要** > **URL**|*存在しない*
|[Paths](https://swagger.io/specification/#paths-object-29)|**統合** > **選択した HTTP メソッド**|バインディング: ルート
|[Path Item](https://swagger.io/specification/#path-item-object-32)|**統合** > **ルート テンプレート**|バインディング: メソッド
|[Security](https://swagger.io/specification/#security-scheme-object-112)|**[キー]**|*存在しない*|
|operationID*|**ルート + 使用できる動詞**|ルート + 使用できる動詞|

\*Operation ID は PowerApps + Flow と統合する場合のみ必要です。
> [!NOTE]
> x-ms-summary 拡張子は、Logic Apps、PowerApps、および Flow の表示名を示します。
>
> 詳細については、「[Customize your Swagger definition for PowerApps](/connectors/custom-connectors/openapi-extensions)」(PowerApps の Swagger 定義のカスタマイズ) を参照してください。

## <a name="use-cicd-to-set-an-api-definition"></a><a name="CICD"></a>CI/CD を使用した API 定義の設定

 ソース管理から API 定義を変更するためにソース管理を有効にする前に、ポータルで API 定義ホスティングを有効にする必要があります。 以下の手順に従います。

1. 関数アプリの設定で、 **[API 定義 (プレビュー)]** を参照します。
   1. **[API 定義のソース]** を **[関数]** に設定します。
   1. **[API 定義テンプレートを生成する]** をクリックし、 **[保存]** をクリックして、後で変更するためにテンプレートの定義を作成します。
   1. API 定義の URL とキーに注意してください。
1. [継続的インテグレーションと継続的デプロイ (CI/CD) のセットアップ](./functions-continuous-deployment.md#requirements-for-continuous-deployment)
2. \site\wwwroot\.azurefunctions\swagger\swagger.json でソース管理の swagger.json を変更します。

これで、リポジトリ内の swagger.json への変更は、API 定義 URL にある関数アプリ、および手順 1.c でメモしたキーでホストされるようになります。

## <a name="next-steps"></a>次のステップ
* [概要チュートリアル](./functions-openapi-definition.md)。 OpenAPI 定義が動作することを確認するチュートリアルを試してください。
* [Azure Functions GitHub リポジトリ](https://github.com/Azure/Azure-Functions/) API 定義のサポート プレビューについてフィードバックを提供するには、Functions リポジトリを参照してください。 更新が必要な内容については、Github の問題を作成してください。
* [Azure Functions の開発者向けガイド](functions-reference.md) 関数のコーディングとトリガーおよびバインドの定義に関して学習してください。
