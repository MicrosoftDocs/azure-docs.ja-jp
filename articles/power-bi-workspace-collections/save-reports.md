---
title: Power BI ワークスペース コレクションでのレポートの保存 | Microsoft Docs
description: Power BI ワークスペース コレクション内でレポートを保存する方法について説明します。 この操作を正常に実行するには、適切なアクセス許可が必要です。
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: markingmyname
ms.author: maghan
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: bc3c9f71900abcbba440db259d92df53a4c7a586
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58516452"
---
# <a name="save-reports-in-power-bi-workspace-collections"></a>Power BI ワークスペース コレクションでのレポートの保存

Power BI ワークスペース コレクション内でレポートを保存する方法について説明します。 レポートの保存を正常に実行するには、適切なアクセス許可が必要です。

> [!IMPORTANT]
> Power BI ワークスペース コレクションは非推奨となっており、2018 年 6 月または契約に定める日までに限り利用できます。 アプリケーションの中断を避けるため、Power BI Embedded への移行をご検討ください。 Power BI Embedded にデータを移行する方法については、[Power BI ワークスペース コレクション コンテンツを Power BI Embedded に移行する方法](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)に関するページを参照してください。

Power BI ワークスペース コレクション内で既存のレポートを編集して保存することができます。 また、新しいレポートを作成した後、新しいレポートとして保存して別のレポートを作成することもできます。

レポートを保存するには、まず適切なスコープを持つ特定のレポート用にトークンを作成する必要があります。

* 保存操作を有効にするには、Report.ReadWrite スコープが必要です。
* "名前を付けて保存" 操作を有効にするには、Report.Read と Workspace.Report.Copy のスコープが必要です。
* 保存操作と "名前を付けて保存" 操作を有効にするには、Report.ReadWrite と Workspace.Report.Copy が必要です。

[ファイル] メニューの適切な [保存] ボタンまたは [名前を付けて保存] ボタンを有効にするには、それぞれ、レポートを埋め込むときに埋め込み構成に適切なアクセス許可を指定する必要があります。

* models.Permissions.ReadWrite
* models.Permissions.Copy
* models.Permissions.All

> [!NOTE]
> アクセス トークンにも適切なスコープが必要です。 詳細については、[Scopes](app-token-flow.md#scopes) に関するページを参照してください。

## <a name="embed-report-in-edit-mode"></a>編集モードでレポートを埋め込む

アプリ内にレポートを編集モードで埋め込むとします。そのためには、埋め込み構成で適切なプロパティを渡し、powerbi.embed() を呼び出します。 編集モードで [保存] ボタンと [名前を付けて保存] ボタンを表示するには、permissions と viewMode を指定します。 詳細については、「[Embed configuration details (埋め込み構成の詳細)](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details)」を参照してください。

JavaScript の例:

```html
   <div id="reportContainer"></div>

    <script>
    // Get models. Models, it contains enums that can be used.
    var models = window['powerbi-client'].models;

    // Embed configuration used to describe the what and how to embed.
    // This object is used when calling powerbi.embed.
    // This also includes settings and options such as filters.
    // You can find more information at https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details.
    var config= {
        type: 'report',
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        id:  '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
        permissions: models.Permissions.All /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.Edit,
        settings: {
            filterPaneEnabled: true,
            navContentPaneEnabled: true
        }
    };

    // Get a reference to the embedded report HTML element
    var reportContainer = $('#reportContainer')[0];

    // Embed the report and display it within the div container.
    var report = powerbi.embed(reportContainer, config);
    </script>
```

これで、レポートが編集モードでアプリに埋め込まれます。

## <a name="save-report"></a>レポートを保存する

適切なトークンとアクセス許可を使用してレポートを編集モードで埋め込んだ後、[ファイル] メニューまたは JavaScript を使用してレポートを保存できます。

```javascript
 // Get a reference to the embedded report.
    report = powerbi.get(reportContainer);

 // Save report
    report.save();
```

## <a name="save-as"></a>名前を付けて保存

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
> 新しいレポートは、"*名前を付けて保存*" 操作が呼び出された後でのみ作成されます。 保存後、キャンバスには、新しいレポートではなく、古いレポートが編集モードで表示されたままになります。 作成された新しいレポートを埋め込みます。 新しいレポートを埋め込むには、レポートごとに作成される新しいアクセス トークンが必要です。

次に、"*名前を付けて保存*" した後に、新しいレポートを読み込む必要があります。 新しいレポートの読み込みは、他のレポートを埋め込む場合と同様です。

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

## <a name="see-also"></a>関連項目

[Get started with Microsoft Power BI Embedded sample (Microsoft Power BI Embedded のサンプルの使用)](get-started-sample.md)  
[レポートの埋め込み](embed-report.md)  
[データセットからの新しいレポートの作成](create-report-from-dataset.md)  
[Power BI ワークスペース コレクションでの認証と承認](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript による埋め込みのサンプル](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

ご質問は、 [Power BI コミュニティ](https://community.powerbi.com/)で尋ねてみてください。

