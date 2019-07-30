---
title: Azure Power BI ワークスペース コレクションでレポートを埋め込む | Microsoft Docs
description: Power BI ワークスペース コレクションでレポートをアプリケーションに埋め込む方法について説明します。
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 4e125e57c290688d5a65f6d70143100cbd3462cb
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67669044"
---
# <a name="embed-a-report-in-power-bi-workspace-collections"></a>Power BI ワークスペース コレクションでレポートを埋め込む

Power BI ワークスペース コレクションでレポートをアプリケーションに埋め込む方法について説明します。

> [!IMPORTANT]
> Power BI ワークスペース コレクションは非推奨となっており、2018 年 6 月または契約に定める日までに限り利用できます。 アプリケーションの中断を避けるため、Power BI Embedded への移行をご検討ください。 Power BI Embedded にデータを移行する方法については、[Power BI ワークスペース コレクション コンテンツを Power BI Embedded に移行する方法](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)に関するページを参照してください。

実際にアプリケーションにレポートを埋め込む方法を紹介します。 ここでは、ワークスペース コレクションのワークスペース内に既にレポートが存在していることを前提としています。 その手順をまだ完了していない場合は、[Power BI ワークスペース コレクションの概要](get-started.md)に関するページを参照してください。

.NET (C#) または Node.js SDK を JavaScript と共に使用すると、Power BI ワークスペース コレクションを使用してアプリケーションを簡単に構築できます。

## <a name="using-the-access-keys-to-use-rest-apis"></a>REST API を使用するためのアクセス キーの使用

REST API を呼び出すために、特定のワークスペース コレクションに対して Azure Portal から取得できるアクセス キーを渡すことができます。 詳細については、[Power BI ワークスペース コレクションの概要](get-started.md)に関するページを参照してください。

## <a name="get-a-report-id"></a>レポート ID を取得する

すべてのアクセス トークンは、レポートに基づきます。 レポートを埋め込むには、そのレポートのレポート ID を取得する必要があります。 この操作は、[Get Reports](https://msdn.microsoft.com/library/azure/mt711510.aspx) REST API 呼び出しに基づいて実行できます。 これにより、レポート ID と埋め込み URL が返されます。 これは、Power BI .NET SDK を使用するか、REST API を直接呼び出すことで実行できます。

### <a name="using-the-power-bi-net-sdk"></a>Power BI .NET SDK の使用

.NET SDK を使用する場合は、Azure Portal で取得したアクセス キーに基づくトークン資格情報を作成する必要があります。 そのためには、[Power BI API NuGet パッケージ](https://www.nuget.org/profiles/powerbi)をインストールする必要があります。

**NuGet パッケージのインストール**

```powershell
Install-Package Microsoft.PowerBI.Api
```

**C# コード**

```csharp
using Microsoft.PowerBI.Api.V1;
using Microsoft.Rest;

var credentials = new TokenCredentials("{access key}", "AppKey");
var client = new PowerBIClient(credentials);
client.BaseUri = new Uri(https://api.powerbi.com);

var reports = (IList<Report>)client.Reports.GetReports(workspaceCollectionName, workspaceId).Value;

// Select the report that you want to work with from the collection of reports.
```

### <a name="calling-the-rest-api-directly"></a>REST API の直接呼び出し

```csharp
System.Net.WebRequest request = System.Net.WebRequest.Create("https://api.powerbi.com/v1.0/collections/{collectionName}/workspaces/{workspaceId}/Reports") as System.Net.HttpWebRequest;

request.Method = "GET";
request.ContentLength = 0;
request.Headers.Add("Authorization", String.Format("AppKey {0}", accessToken.Value));

using (var response = request.GetResponse() as System.Net.HttpWebResponse)
{
    using (var reader = new System.IO.StreamReader(response.GetResponseStream()))
    {
        // Work with JSON response to get the report you want to work with.
    }

}
```

## <a name="create-an-access-token"></a>アクセス トークンを作成する

Power BI ワークスペース コレクションでは、埋め込みトークンを使用します。これは、HMAC の署名付き JSON Web トークンです。 これらのトークンは、Power BI ワークスペース コレクションのアクセス キーで署名されます。 埋め込みトークンは、既定では、アプリケーションに埋め込むレポートへの読み取り専用アクセスを提供するために使用されます。 埋め込みトークンは特定のレポートに対して発行されます。また、埋め込み URL に関連付けられている必要があります。

アクセス トークンはサーバー上で作成する必要があります。これは、トークンの署名と暗号化にアクセス キーが使用されるためです。 アクセス トークンを作成する方法については、「[Power BI ワークスペース コレクションでの認証と承認](app-token-flow.md)」を参照してください。 また、[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) メソッドに関する説明も参照してください。 Power BI 用 .NET SDK を使用した場合の例を次に示します。

前に取得したレポート ID を使用します。 埋め込みトークンが作成されたら、アクセス キーを使用して、JavaScript の観点から使用できるトークンを生成します。 "*PowerBIToken クラス*" を使用するには、[Power BI Core NuGut パッケージ](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)をインストールする必要があります。

**NuGet パッケージのインストール**

```powershell
Install-Package Microsoft.PowerBI.Core
```

**C# コード**

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername, roles and scopes are optional.
embedToken = PowerBIToken.CreateReportEmbedToken(workspaceCollectionName, workspaceId, reportId, rlsUsername, roles, scopes);

var token = embedToken.Generate("{access key}");
```

### <a name="adding-permission-scopes-to-embed-tokens"></a>埋め込みトークンへのアクセス許可スコープの追加

埋め込みトークンを使用する場合は、アクセス権を与えたリソースの使用を制限することができます。 このため、スコープを持つアクセス許可が与えられたトークンを生成できます。 詳細については、[Scopes](app-token-flow.md#scopes) に関するページを参照してください。

## <a name="embed-using-javascript"></a>JavaScript を使用して埋め込む

アクセス トークンとレポート ID を取得したら、JavaScript を使用してレポートを埋め込むことができます。 そのためには、NuGet [Power BI JavaScript パッケージ](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)をインストールする必要があります。 embedUrl は https://embedded.powerbi.com/appTokenReportEmbed です。

> [!NOTE]
> [JavaScript によるレポートの埋め込みのサンプル](https://microsoft.github.io/PowerBI-JavaScript/demo/)を使用して、機能をテストできます。 ここでは、使用可能なさまざまな操作のコード例も紹介されています。

**NuGet パッケージのインストール**

```powershell
Install-Package Microsoft.PowerBI.JavaScript
```

**JavaScript コード**

```html
<script src="/scripts/powerbi.js"></script>
<div id="reportContainer"></div>

<script>
var embedConfiguration = {
    type: 'report',
    accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
    id: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed'
};

var $reportContainer = $('#reportContainer');
var report = powerbi.embed($reportContainer.get(0), embedConfiguration);
</script>
```

### <a name="set-the-size-of-embedded-elements"></a>埋め込み要素のサイズを設定する

レポートは、コンテナーのサイズに基づいて自動的に埋め込まれます。 埋め込まれた項目の既定のサイズは、幅と高さの CSS クラス属性またはインライン スタイルを追加するだけでオーバーライドできます。

## <a name="see-also"></a>関連項目

[Get started with Microsoft Power BI Embedded sample (Microsoft Power BI Embedded のサンプルの使用)](get-started-sample.md)  
[Power BI ワークスペース コレクションでの認証と承認](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[JavaScript による埋め込みのサンプル](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Power BI JavaScript パッケージ](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  
[Power BI API NuGet パッケージ](https://www.nuget.org/profiles/powerbi)
[Power BI Core NuGut パッケージ](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[PowerBI-CSharp Git リポジトリ](https://github.com/Microsoft/PowerBI-CSharp)  
[PowerBI-Node Git リポジトリ](https://github.com/Microsoft/PowerBI-Node)  

ご質問は、 [Power BI コミュニティ](https://community.powerbi.com/)で尋ねてみてください。
