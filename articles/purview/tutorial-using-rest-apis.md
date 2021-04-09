---
title: チュートリアル:REST API を使用する
description: このチュートリアルでは、Azure Purview REST API を使用してカタログの内容にアクセスする方法について説明します。
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/03/2020
ms.openlocfilehash: bfb808c634ba946e1a4825d7828db6df8963352c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98951245"
---
# <a name="tutorial-use-the-rest-apis"></a>チュートリアル:REST API を使用する

このチュートリアルでは、Azure Purview REST API の使用方法を説明します。 Azure Purview カタログにデータを送信したり、そのカタログを自動プロセスに追加したり、カタログをベースにして独自のユーザー エクスペリエンスを構築したりする際には、REST API を使用することができます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * サービス プリンシパル (アプリケーション) を作成する。
> * サービス プリンシパル (アプリケーション) を信頼するようにカタログを構成する。
> * REST API ドキュメントを参照する。
> * REST API を使用するために必要な値を収集する。
> * Postman クライアントを使用して REST API を呼び出す。
> * REST API を呼び出すための .NET クライアントを生成する。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)してください。

## <a name="prerequisites"></a>前提条件

* 最初に、既存の Azure Purview アカウントが必要です。 カタログがない場合は、[Azure Purview アカウントの作成に関するクイックスタート](create-catalog-portal.md)を参照してください。

* データをカタログに追加する必要がある場合は、[スタート キットを実行してデータをスキャンするチュートリアル](tutorial-scan-data.md)を参照してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-a-service-principal-application"></a>サービス プリンシパル (アプリケーション) を作成する

カタログにアクセスする REST API クライアントは、サービス プリンシパル (アプリケーション) と、カタログが認識し信頼するように構成された ID とを備えている必要があります。 このサービス プリンシパルの ID が、カタログへの REST API 呼び出しを行う際に使用されます。

既存のサービス プリンシパル (アプリケーション ID) を使用した場合に、高い確率でエラーが発生しています。 したがって API の呼び出しには、新しいサービス プリンシパルを作成することをお勧めします。

新しいサービス プリンシパルを作成するには:

1. [Azure portal](https://portal.azure.com) で **[Azure Active Directory]** を検索して選択します。
1. **[Azure Active Directory]** ページの左ペインで **[アプリの登録]** を選択します。
1. **[新規登録]** を選択します。
1. **[アプリケーションの登録]** ページで、次のようにします。
    1. **アプリケーション** の名前 (サービス プリンシパル名) を入力します。
    1. **[この組織ディレクトリのみに含まれるアカウント ( _&lt;ご利用のテナント名&gt;_ のみ - シングル テナント)]** を選択します。
    1. **[リダイレクト URI (省略可能)]** で **[Web]** を選択し、値を入力します。 この値は有効な URI である必要はありません。
    1. **[登録]** を選択します。
1. 新しいサービス プリンシパル ページで、 **[表示名]** と **[アプリケーション (クライアント) ID]** の値をコピーして、後で使用するために保存します。

   サンプル コード内の `client_id` の値がアプリケーション ID です。

サービス プリンシパル (アプリケーション) を使用するには、そのパスワードを取得する必要があります。 その方法は次のとおりです。

1. Azure portal で **[Azure Active Directory]** を検索して選択し、左ペインから **[アプリの登録]** を選択します。
1. 一覧から自分のサービス プリンシパル (アプリケーション) を選択します。
1. 左ペインから **[証明書とシークレット]** を選択します。
1. **[新しいクライアント シークレット]** を選択します。
1. **[クライアント シークレットの追加]** ページで **説明** を入力し、 **[有効期限]** で有効期限を選択して、 **[追加]** を選択します。

   **[クライアント シークレット]** ページに表示される新しいシークレットの **[値]** 列の文字列が自分のパスワードです。 この値を保存します。

   :::image type="content" source="./media/tutorial-using-rest-apis/client-secret.png" alt-text="クライアント シークレットを示すスクリーンショット。":::

## <a name="configure-your-catalog-to-trust-the-service-principal-application"></a>サービス プリンシパル (アプリケーション) を信頼するようにカタログを構成する

新しいサービス プリンシパルを信頼するように Azure Purview を構成するには:

1. 自分の Purview アカウントに移動します

1. **[Purview アカウント]** ページの **[アクセス制御 (IAM)]** タブを選択します。

1. **[+ 追加]** をクリックします。

1. **[ロールの割り当ての追加]** を選択します

1. [ロール] に「**Purview データ キュレーター**」と入力します

    > [!Note]
    > Azure Purview Data Catalog のアクセス許可について詳しくは、[カタログのアクセス許可](catalog-permissions.md)に関するページを参照してください。 たとえば、スキャンをトリガーするためのアクセス許可が必要な場合、ロールの種類は **[Purview データ ソース管理者]** にする必要があります。

1. **[アクセスの割り当て先]** は、既定値の **[User, group, or service principal]\(ユーザー、グループ、またはサービス プリンシパル\)** のままにします

1. **[選択]** で、以前に作成した割り当てたいサービス プリンシパルの名前を入力し、その名前を結果ペインでクリックします。

1. [設定] メニューの **[保存]**

サービス プリンシパルがアプリケーション管理者として構成され、コンテンツをカタログに送信できるようになりました。

## <a name="view-the-rest-apis-documentation"></a>REST API ドキュメントを参照する

API Swagger ドキュメントを参照するには、[PurviewCatalogAPISwagger.zip](https://github.com/Azure/Purview-Samples/raw/master/rest-api/PurviewCatalogAPISwagger.zip) をダウンロードし、そのファイルを抽出して、index.html を開きます。

Azure Purview が備えている高度な検索および候補 API の詳細については、REST API の検索フィルターに関するドキュメントを参照してください。 AutoRest によって生成されるクライアントは現在、カスタマイズされた検索パラメーターをサポートしていません。 回避策としては、検索フィルターのドキュメントに従って、API 呼び出しパラメーターとしてのフィルター クラスをコード内に定義してください。 それらの API の例は、index.html ドキュメントに掲載されています。

## <a name="collect-the-necessary-values-to-use-the-rest-apis"></a>REST API を使用するために必要な値を収集する

以下の値を探して保存します。

* テナント ID:
  * [Azure portal](https://portal.azure.com) で、 **[Azure Active Directory]** を検索して選択します。
  * 左ペインの **[管理]** セクションで **[プロパティ]** を選択し、 **[テナント ID]** を見つけて **[クリップボードにコピー]** アイコンを選択し、その値を保存します。
* Atlas エンドポイント:
  * Azure portal の [Azure Purview アカウント ページ](https://aka.ms/purviewportal)で、自分の Azure Purview アカウントを一覧から探して選択します。
  * **[プロパティ]** を選択して **[Atlas Endpoint]\(Atlas エンドポイント\)** を探し、 **[クリップボードにコピー]** アイコンを選択してその値を保存します。 後でこれを使用する際には、*https://* の部分を削除してください。
* アカウント名:
  * Atlas エンドポイント文字列から自分のカタログの名前を抽出します。 たとえば、Atlas エンドポイントが `https://ThisIsMyCatalog.catalog.purview.azure.com` である場合、自分のアカウント名は `ThisIsMyCatalog` です。

## <a name="use-the-postman-client-to-call-the-rest-apis"></a>Postman クライアントを使用して REST API を呼び出す

1. [Postman クライアント](https://www.getpostman.com/)をインストールします。
1. クライアントから **[Import]\(インポート\)** を選択し、[Test.postman_collection.json](https://raw.githubusercontent.com/Azure/Purview-Samples/master/rest-api/Test.postman_collection.json) を使用します。
1. **[Collections]\(コレクション\)** を選択し、 **[Test]\(テスト\)** を選択します。
1. **[Get Token]\(トークンの取得\)** を選択します。
    1. [POST] の横に表示される URL の *&lt;your-tenant-id&gt;* を、前のセクションでコピーしたテナント ID に置き換えます。
    1. **[Body]\(本文\)** タブを選択し、パスと本文のプレースホルダーを前の手順で取得した値に置き換えます。

       **[Send]\(送信\)** を選択した後、*access_token* という名前と引用符で囲まれた文字列値とを含む JSON 構造が応答本文に格納されます。 
    1. 次の手順で使用するために、ベアラー トークン値 (引用符を除く) をコピーします。

1. **[/v2/types/typedefs]** を選択します。
    1. パスのプレースホルダーを、自分の Atlas エンドポイントの値に置き換えます。 この値は、Ibiza ポータルでカタログ インスタンスに移動し、[overview]\(概要\) をクリックして取得できます。 

       ベアラー トークンは、最初の手順によって設定されます ([Authorization]\(承認\) タブで手動でコピーすることもできます)。

    1. **[Send]\(送信\)** を選択して応答を取得します。

## <a name="generate-a-net-client-to-call-the-rest-apis"></a>REST API を呼び出すための .NET クライアントを生成する

### <a name="install-autorest"></a>AutoRest をインストールする



1. [Node.js をインストール](https://github.com/Azure/autorest/blob/v2/docs/installing-autorest.md)します。
1. PowerShell を開き、次のコマンドを実行します。

   ```powershell
   npm install -g autorest@V2
   ```

### <a name="download-rest-api-specszip-and-create-the-client"></a>rest-api-specs.zip をダウンロードしてクライアントを作成する

1. [rest-api-specs.zip](https://github.com/Azure/Purview-Samples/raw/master/rest-api/rest-api-specs.zip) をダウンロードして、ファイルを抽出します。
1. rest-api-specs の抽出フォルダーから、PowerShell で次のコマンドを実行します。

   ```powershell
   autorest --input-file=data-plane/preview/purviewcatalog.json --csharp --output-folder=PurviewCatalogClient/CSharp --namespace=PurviewCatalog --add-credentials
   ```

   このプロセスの出力によって、rest-api-specs フォルダーに PurviewCatalogClient フォルダーと CSharp サブフォルダーが作成されます。

### <a name="create-a-sample-net-console-application"></a>サンプル .NET コンソール アプリケーションを作成する

1. Visual Studio 2019 を開きます。 こちらの手順は、無料の Community Edition でテストされています。
1. **[新しいプロジェクトの作成]** ページで、C# の **[コンソール アプリ (.NET Core)]** プロジェクトを作成します。
1. 用意されている[サンプル コード](#sample-code-for-the-console-application)をコピーして貼り付けます。
1. *accountName*、*servicePrincipalId*、*servicePrincipalKey*、*tenantId* を、前もって収集した値に置き換えます。
1. **ソリューション エクスプローラー** を使用して、この Visual Studio プロジェクトに **Generated** という名前のフォルダーを追加します。
1. 先ほど作成した rest-api-specs\PurviewCatalogClient\CSharp フォルダーを \Generated フォルダーにコピーします。 コピー操作にはエクスプローラーまたはコマンドライン プロンプトを使用してください。それにより、Visual Studio プロジェクトに自動的にファイルが追加されます。
1. **ソリューション エクスプローラー** でプロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。
1. **[参照]** タブを選択します。 **[Microsoft.Rest.ClientRuntime]** を探して選択します。
1. バージョンが 2.3.21 以上であることを確認し、 **[インストール]** を選択します。
1. アプリケーションをビルドして実行します。

サンプル コードは、カタログに含まれる typedef の数を返すもので、ロールの割り当てを処理する方法を示しています。 詳細については、サンプル コードの `DoRoleAssignmentOperations()` を参照してください。 プロジェクトの詳細については、「[プロジェクトの設定](https://github.com/Azure/autorest/blob/v2/docs/client/proj-setup.md)」を参照してください。

### <a name="sample-code-for-the-console-application"></a>コンソール アプリケーションのサンプル コード

```csharp
using Microsoft.Rest;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;

namespace PurviewCatalogSdkTest
{
    class Program
    {
        private static string accountName = "{account-name}";
        private static string servicePrincipalId = "{service-principal-id}";
        private static string servicePrincipalKey = "{service-principal-key}";
        private static string tenantId = "{tenant-id}";

        static void Main(string[] args)
        {
            Console.WriteLine("Azure Purview client");

            // You need to change the api path below (e.g. /api) based on what you're trying to call
            string baseUri = string.Format("https://{0}.catalog.purview.azure.com/api", accountName);

            // Get token and set auth
            var svcClientCreds = new TokenCredentials(getToken(), "Bearer");
            var client = new PurviewCatalog.PurviewCatalogServiceRESTAPIDocument(svcClientCreds);
            client.BaseUri = new System.Uri(baseUri);

            // /v2/types/typedefs
            var task = client.TypesREST.GetAllTypeDefsWithHttpMessagesAsync();
            Console.WriteLine("\nURI:\n" + task.Result.Request.RequestUri);
            Console.WriteLine("\nStatus Code:\n" + task.Result.Response.StatusCode);
            Console.WriteLine("\nResult:\n" + JsonConvert.SerializeObject(task.Result.Body, Formatting.Indented));
        }

        // Replace client_id and client_secret with application ID and password value from service principal
        private static string getToken()
        {
            var values = new Dictionary<string, string>

            // The "resource" could be "https://purview.azure.net" or "73c2949e-da2d-457a-9607-fcc665198967"
            {
                { "grant_type", "client_credentials" },
                { "client_id", servicePrincipalId },
                { "client_secret", servicePrincipalKey },
                { "resource", "73c2949e-da2d-457a-9607-fcc665198967" }
            };

            string authUrl = string.Format("https://login.windows.net/{0}/oauth2/token", tenantId);
            var content = new FormUrlEncodedContent(values);

            HttpClient authClient = new HttpClient();
            var bearerResult = authClient.PostAsync(authUrl, content);
            bearerResult.Wait();
            var resultContent = bearerResult.Result.Content.ReadAsStringAsync();
            resultContent.Wait();
            var bearerToken = JObject.Parse(resultContent.Result)["access_token"].ToString();
            var svcClientCreds = new TokenCredentials(bearerToken, "Bearer");

            return bearerToken;
        }
    }
}
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [データ ソースを管理する](manage-data-sources.md)
