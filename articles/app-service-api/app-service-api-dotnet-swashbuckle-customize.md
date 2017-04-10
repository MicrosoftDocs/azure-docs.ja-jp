
---
title: "Swashbuckle が生成する API 定義をカスタマイズする"
description: "Azure App Service で API アプリに対して Swashbuckle により生成される Swagger の API 定義をカスタマイズする方法を説明します。"
services: app-service\api
documentationcenter: .net
author: bradygaster
manager: erikre
editor: jimbe
ms.assetid: 6b8cbc38-d282-4a0f-b0c5-762631bae6f3
ms.service: app-service-api
ms.workload: web
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 08/29/2016
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: c83905a97fb2ee988fe06fc1f9a7379c1741fd02
ms.lasthandoff: 03/01/2017


---
# <a name="customize-swashbuckle-generated-api-definitions"></a>Swashbuckle が生成する API 定義をカスタマイズする
## <a name="overview"></a>概要
この記事では、Swashbuckle をカスタマイズして、既定の動作を変更する必要がある一般的なシナリオを処理する方法について説明します。

* Swashbuckle では、コントローラー メソッドのオーバーロードの重複する操作 ID が生成されます。
* Swashbuckle は、メソッドからの唯一の有効な応答が HTTP 200 (OK) であることを想定しています。 

## <a name="customize-operation-identifier-generation"></a>操作 ID の生成をカスタマイズします。
Swashbuckle は、コントローラー名とメソッド名を連結することによって、Swagger 操作の ID を生成します。 このパターンでは、メソッドに複数のオーバーロードがある場合に問題が発生します。Swashbuckle は重複する操作 ID を生成し、これは無効な Swagger JSON です。

たとえば、次のコントローラー コードでは、Swashbuckle は&3; つの Contact_Get 操作 ID を生成します。

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsincode.png)

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsinjson.png)

メソッドに一意の名前を付けることによって、手動で問題を解決できます。この例ではたとえば次のようにします。

* 取得
* GetById
* GetPage

あるいは、一意の操作 ID を自動的に生成するように Swashbuckle を拡張します。

次の手順では、Visual Studio API Apps Preview プロジェクト テンプレートによってプロジェクトに組み込まれる *SwaggerConfig.cs* ファイルを使用して Swashbuckle をカスタマイズする方法を示します。  または、API アプリとしてのデプロイ用に構成する Web API プロジェクトで Swashbuckle をカスタマイズすることもできます。

1. `IOperationFilter` のカスタム実装の作成 
   
    `IOperationFilter` インターフェイスは、Swagger メタデータ プロセスのさまざまな部分をカスタマイズする Swashbuckle ユーザーに機能拡張ポイントを提供します。 次のコードでは、操作 ID 生成動作を変更する&1; つの方法を示します。 このコードは、操作 ID 名にパラメーター名を追加します。  
   
        using Swashbuckle.Swagger;
        using System.Web.Http.Description;
   
        namespace ContactsList
        {
            public class MultipleOperationsWithSameVerbFilter : IOperationFilter
            {
                public void Apply(
                    Operation operation,
                    SchemaRegistry schemaRegistry,
                    ApiDescription apiDescription)
                {
                    if (operation.parameters != null)
                    {
                        operation.operationId += "By";
                        foreach (var parm in operation.parameters)
                        {
                            operation.operationId += string.Format("{0}",parm.name);
                        }
                    }
                }
            }
        }
2. *App_Start\SwaggerConfig.cs* ファイルで、`OperationFilter` メソッドを呼び出し、Swashbuckle が `IOperationFilter` の新しい実装を使うようにします。
   
        c.OperationFilter<MultipleOperationsWithSameVerbFilter>();
   
    ![](./media/app-service-api-dotnet-swashbuckle-customize/usefilter.png)
   
    Swashbuckle NuGet パッケージに含まれる *SwaggerConfig.cs* ファイルでは、多くの機能拡張ポイントの例がコメント化されています。 他のコメントはここでは示しません。 
   
    この変更を行った後は、 `IOperationFilter` の実装を使用して一意の操作 ID が生成されます。
   
    ![](./media/app-service-api-dotnet-swashbuckle-customize/uniqueids.png)

<a id="multiple-response-codes" name="multiple-response-codes"></a>

## <a name="allow-response-codes-other-than-200"></a>200 以外の応答コードを許可します。
Swashbuckle は既定で、HTTP 200 (OK) 応答がWeb API メソッドからの *唯一の* 有効な応答であることを前提としています。 ただし、他の応答コードが返された場合でもクライアントで例外が発生しないようにすることが必要な局面も考えられます。  たとえば、次の Web API コードは、クライアントで 200 と 404 のどちらも有効な応答として受け入れる場合のシナリオを示します。

    [ResponseType(typeof(Contact))]
    public HttpResponseMessage Get(int id)
    {
        var contacts = GetContacts();

        var requestedContact = contacts.FirstOrDefault(x => x.Id == id);

        if (requestedContact == null)
        {
            return Request.CreateResponse(HttpStatusCode.NotFound);
        }
        else
        {
            return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
        }
    }

このシナリオでは、Swashbuckle が生成する Swagger で、既定で有効な HTTP 状態コードとして HTTP 200 のみが指定されています。

![](./media/app-service-api-dotnet-swashbuckle-customize/http-200-output-only.png)

Visual Studio では、Swagger の API 定義を使用してクライアントのコードを生成するため、HTTP 200 以外のすべての応答について例外を生成するクライアント コードが作成されます。 以下のコードは、この Web API メソッドのサンプル用に生成された C# クライアントのものです。

    if (statusCode != HttpStatusCode.OK)
    {
        HttpOperationException<object> ex = new HttpOperationException<object>();
        ex.Request = httpRequest;
        ex.Response = httpResponse;
        ex.Body = null;
        if (shouldTrace)
        {
            ServiceClientTracing.Error(invocationId, ex);
        }
        throw ex;
    } 

Swashbuckle には、生成される予定の HTTP 応答コードの一覧をカスタマイズする方法が&2; つあります。XML コメントを使用する方法と `SwaggerResponse` 属性を使用する方法です。 属性の方が簡単ですが、使用できるのは Swashbuckle 5.1.5 以降のみです。 Visual Studio 2013 の API Apps プレビューの新しいプロジェクト テンプレートには、Swashbuckle バージョン 5.0.0 が含まれているため、テンプレートを使用していて Swashbuckle を更新しない場合、唯一のオプションは XML コメントを使用する方法となります。 

### <a name="customize-expected-response-codes-using-xml-comments"></a>XML コメントを使用して、想定される応答コードをカスタマイズする
Swashbuckle のバージョンが 5.1.5 より前の場合は、このメソッドを使用して応答コードを指定します。

1. まず、HTTP 応答コードを指定するメソッドに、XML ドキュメントのコメントを追加します。 上記のように、サンプルの Web API アクションを実行して XML ドキュメントを適用すると、次の例のようなコードが生成されます。 
   
        /// <summary>
        /// Returns the specified contact.
        /// </summary>
        /// <param name="id">The ID of the contact.</param>
        /// <returns>A contact record with an HTTP 200, or null with an HTTP 404.</returns>
        /// <response code="200">OK</response>
        /// <response code="404">Not Found</response>
        [ResponseType(typeof(Contact))]
        public HttpResponseMessage Get(int id)
        {
            var contacts = GetContacts();
   
            var requestedContact = contacts.FirstOrDefault(x => x.Id == id);
   
            if (requestedContact == null)
            {
                return Request.CreateResponse(HttpStatusCode.NotFound);
            }
            else
            {
                return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
            }
        }
2. *SwaggerConfig.cs* ファイルに説明を追加し、Swashbuckle に XML ドキュメント ファイルを使用するよう指定します。
   
   * *SwaggerConfig.cs* を開いて *SwaggerConfig* クラスにメソッドを作成し、XML ドキュメント ファイルへのパスを指定します。 
     
           private static string GetXmlCommentsPath()
           {
               return string.Format(@"{0}\XmlComments.xml", 
                   System.AppDomain.CurrentDomain.BaseDirectory);
           }
   * 以下のスクリーン ショットのように、コメント化されたコードの行が表示されるまで、 *SwaggerConfig.cs* ファイルを下にスクロールします。 
     
       ![](./media/app-service-api-dotnet-swashbuckle-customize/xml-comments-commented-out.png)
   * この行のコメント化を解除します。これにより、XML コメントで指定した処理が Swagger 生成中に実行されるようになります。 
     
       ![](./media/app-service-api-dotnet-swashbuckle-customize/xml-comments-uncommented.png)
3. XML ドキュメント ファイルを生成するために、次のスクリーン ショットに示すように、プロジェクトのプロパティに移動して XML ドキュメント ファイルを有効にします。 
   
    ![](./media/app-service-api-dotnet-swashbuckle-customize/enable-xml-documentation-file.png) 

これらの手順を実行すると、Swashbuckle により生成される Swagger JSON に、XML コメントで指定した HTTP 応答コードが反映されます。 次のスクリーン ショットは、この新しい JSON ペイロードを示します。 

![](./media/app-service-api-dotnet-swashbuckle-customize/swagger-multiple-responses.png)

Visual Studio を使用して REST API のクライアント コードを再生成すると、C# コードは HTTP OK と Not Found の両方の状態コードを受け入れ、例外が発生することもないため、コードで null の Contact レコードの戻り値を処理する方法を決定できます。 

        if (statusCode != HttpStatusCode.OK && statusCode != HttpStatusCode.NotFound)
        {
            HttpOperationException<object> ex = new HttpOperationException<object>();
            ex.Request = httpRequest;
            ex.Response = httpResponse;
            ex.Body = null;
            if (shouldTrace)
            {
                ServiceClientTracing.Error(invocationId, ex);
            }
                throw ex;
        }

このデモのコードは、 [この GitHub リポジトリ](https://github.com/Azure-Samples/app-service-api-dotnet-swashbuckle-swaggerresponse)にあります。 XML ドキュメントのコメントでマークアップされた Web API プロジェクトと共に、この API 用に生成されたクライアントを含むコンソール アプリケーション プロジェクトがあります。 

### <a name="customize-expected-response-codes-using-the-swaggerresponse-attribute"></a>SwaggerResponse 属性を使用して、想定される応答コードをカスタマイズする
[SwaggerResponse](https://github.com/domaindrivendev/Swashbuckle/blob/master/Swashbuckle.Core/Swagger/Annotations/SwaggerResponseAttribute.cs) 属性は、Swashbuckle 5.1.5 以降で利用できます。 プロジェクトに以前のバージョンが含まれている場合のために、このセクションでは、まずこの属性を使用できるように、Swashbuckle NuGet パッケージの更新方法について説明します。

1. **[ソリューション エクスプローラー]** で Web API プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。 
   
    ![](./media/app-service-api-dotnet-swashbuckle-customize/manage-nuget-packages.png)
2. *Swashbuckle* NuGet パッケージの横にある *[更新]* ボタンをクリックします。 
   
    ![](./media/app-service-api-dotnet-swashbuckle-customize/update-nuget-dialog.png)
3. 有効な HTTP 応答コードを指定する Web API アクション メソッドに *SwaggerResponse* 属性を追加します。 
   
        [SwaggerResponse(HttpStatusCode.OK)]
        [SwaggerResponse(HttpStatusCode.NotFound)]
        [ResponseType(typeof(Contact))]
        public HttpResponseMessage Get(int id)
        {
            var contacts = GetContacts();
   
            var requestedContact = contacts.FirstOrDefault(x => x.Id == id);
            if (requestedContact == null)
            {
                return Request.CreateResponse(HttpStatusCode.NotFound);
            }
            else
            {
                return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
            }
        }
4. 属性の名前空間の `using` ステートメントを追加します。
   
        using Swashbuckle.Swagger.Annotations;
5. プロジェクトの URL */swagger/docs/v1* を参照すると、さまざまな HTTP 応答コードが Swagger JSON に表示されます。 
   
    ![](./media/app-service-api-dotnet-swashbuckle-customize/multiple-responses-post-attributes.png)

このデモのコードは、 [この GitHub リポジトリ](https://github.com/Azure-Samples/API-Apps-DotNet-Swashbuckle-Customization-MultipleResponseCodes-With-Attributes)にあります。 *SwaggerResponse* 属性で修飾した Web API プロジェクトと共に、この API 用に生成されたクライアントを含むコンソール アプリケーション プロジェクトがあります。 

## <a name="next-steps"></a>次のステップ
この記事では Swashbuckle により操作 ID と有効な応答コードを生成する方法のカスタマイズについて説明しました。 詳細については、 [GitHub の Swashbuckle](https://github.com/domaindrivendev/Swashbuckle)をご覧ください。


