---
title: Power BI ワークスペース コレクションにおけるデータセットからの新しいレポートの作成 | Microsoft Docs
description: Power BI ワークスペース コレクション レポートをお使いのアプリケーションのデータセットから作成できるようになりました。
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 2034c62a17b71b92b43a7afd794c2c172288d58c
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672450"
---
# <a name="create-a-new-report-from-a-dataset-in-power-bi-workspace-collections"></a>Power BI ワークスペース コレクションにおけるデータセットからの新しいレポートの作成

Power BI ワークスペース コレクション レポートをお使いのアプリケーションのデータセットから作成できるようになりました。

> [!IMPORTANT]
> Power BI ワークスペース コレクションは非推奨となっており、2018 年 6 月または契約に定める日までに限り利用できます。 アプリケーションの中断を避けるため、Power BI Embedded への移行をご検討ください。 Power BI Embedded にデータを移行する方法については、[Power BI ワークスペース コレクション コンテンツを Power BI Embedded に移行する方法](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)に関するページを参照してください。

認証方法は、レポートを埋め込む方法と似ています。 これは、データセットに固有のアクセス トークンに基づいています。 PowerBI.com に使用されるトークンは Azure Active Directory (AAD) によって発行されます。 また、Power BI ワークスペース コレクションのトークンは、独自のアプリケーションによって発行されます。

Embedded レポートを作成する際に発行されるトークンは、特定のデータセット用です。 それぞれに一意のトークンが割り当てられるように、トークンは同じ要素の埋め込み URL に関連付けられている必要があります。 Embedded レポートを作成するには、"*Dataset.Read および Workspace.Report.Create*" スコープをアクセス トークンに指定する必要があります。

## <a name="create-access-token-needed-to-create-new-report"></a>新しいレポートを作成するために必要なアクセス トークンを作成する

Power BI ワークスペース コレクションでは、埋め込みトークンを使用します。これは、HMAC の署名付き JSON Web トークンです。 これらのトークンは、Power BI ワークスペース コレクションのアクセス キーで署名されます。 埋め込みトークンは、既定では、アプリケーションに埋め込むレポートへの読み取り専用アクセスを提供するために使用されます。 埋め込みトークンは特定のレポートに対して発行されます。また、埋め込み URL に関連付けられている必要があります。

アクセス トークンはサーバー上で作成する必要があります。これは、トークンの署名と暗号化にアクセス キーが使用されるためです。 アクセス トークンを作成する方法については、「[Power BI ワークスペース コレクションでの認証と承認](app-token-flow.md)」を参照してください。 また、[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) メソッドに関する説明も参照してください。 Power BI 用 .NET SDK を使用した場合の例を次に示します。

この例では、データセット ID に対して新しいレポートを作成します。 また、"*Dataset.Read および Workspace.Report.Create*" のスコープを追加する必要があります。

"*PowerBIToken クラス*" を使用するには、[Power BI Core NuGut パッケージ](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)をインストールする必要があります。

**NuGet パッケージのインストール**

```powershell
Install-Package Microsoft.PowerBI.Core
```

**C# コード**

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Dataset.Read Workspace.Report.Create";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="create-a-new-blank-report"></a>新しい空のレポートを作成する

新しいレポートを作成するには、作成構成を指定する必要があります。 これには、アクセス トークンと embedURL のほか、レポートを作成する datasetID が含まれている必要があります。 そのためには、NuGet [Power BI JavaScript パッケージ](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)をインストールする必要があります。 embedUrl は https://embedded.powerbi.com/appTokenReportEmbed です。

> [!NOTE]
> [JavaScript によるレポートの埋め込みのサンプル](https://microsoft.github.io/PowerBI-JavaScript/demo/)を使用して、機能をテストできます。 ここでは、使用可能なさまざまな操作のコード例も紹介されています。

**NuGet パッケージのインストール**

```powershell
Install-Package Microsoft.PowerBI.JavaScript
```

**JavaScript コード**

```html
<div id="reportContainer"></div>

<script>
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);
</script>
```

*powerbi.createReport()* を呼び出すと、編集モードの空白のキャンバスが *div* 要素内に表示されます。

![新しい空白のレポート](media/create-report-from-dataset/create-new-report.png)

## <a name="save-new-reports"></a>新しいレポートを保存する

レポートは、"**名前を付けて保存**" 操作を呼び出すまで作成されません。 この操作は、[ファイル] メニューから、または JavaScript から実行できます。

```javascript
 // Get a reference to the embedded report.
    report = powerbi.get(reportContainer);
    
    var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);
```

> [!IMPORTANT]
> 新しいレポートは、"**名前を付けて保存**" 操作が呼び出された後でのみ作成されます。 レポートを保存した後、キャンバスには、レポートではなく、データセットが編集モードで表示されたままになります。 他のレポートと同じように、新しいレポートを再度読み込む必要があります。

![[ファイル] メニュー - [ファイルとして保存]](media/create-report-from-dataset/save-new-report.png)

## <a name="load-the-new-report"></a>新しいレポートを読み込む

新しいレポートを操作するには、アプリケーションで通常のレポートを埋め込むのと同じ方法でレポートを埋め込む必要があります。つまり、新しいレポート用に新しいトークンを発行し、埋め込みメソッドを呼び出す必要があります。

```html
<div id="reportContainer"></div>
<script>
var embedConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MJ',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: '5dac7a4a-4452-46b3-99f6-a25915e0fe54',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
</script>
```

## <a name="automate-save-and-load-of-a-new-report-using-the-saved-event"></a>"saved" イベントを使用して新しいレポートの保存と読み込みを自動化する

"名前を付けて保存" 操作の後に新しいレポートを読み込むプロセスを自動化するには、"saved" イベントを使用できます。 このイベントは、保存操作が完了すると起動され、新しい reportId、レポート名、古い reportId (存在する場合)、および操作が saveAs と save のどちらであるかを示す値を含む JSON オブジェクトを返します。

```json
{
  "reportObjectId": "5dac7a4a-4452-46b3-99f6-a25915e0fe54",
  "reportName": "newReport",
  "saveAs": true,
  "originalReportObjectId": null
}
```

このプロセスを自動化するには、"saved" イベントをリッスンし、新しい reportId を取得します。次に、新しいトークンを作成し、それを使用して新しいレポートを埋め込みます。

```html
<div id="reportContainer"></div>
<script>
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);


   var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);

    // report.on will add an event handler which prints to Log window.
    report.on("saved", function(event) {
        
         // get new Token
         var newReportId =  event.detail.reportObjectId;

        // create new Token. This is a function that the application should provide
        var newToken = createAccessToken(newReportId,scopes /*provide the wanted scopes*/);
        
        
    var embedConfiguration = {
        accessToken: newToken ,
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: newReportId,
    };

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
       
   // report.off removes a given event handler if it exists.
   report.off("saved");
    });
</script>
```

## <a name="see-also"></a>関連項目

[Get started with Microsoft Power BI Embedded sample (Microsoft Power BI Embedded のサンプルの使用)](get-started-sample.md)  
[レポートの保存](save-reports.md)  
[レポートの埋め込み](embed-report.md)  
[Power BI ワークスペース コレクションでの認証と承認](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript による埋め込みのサンプル](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Power BI Core NuGut パッケージ](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Power BI JavaScript パッケージ](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  

ご質問は、 [Power BI コミュニティ](https://community.powerbi.com/)で尋ねてみてください。
