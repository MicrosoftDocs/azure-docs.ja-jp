---
title: "Azure Power BI Embedded でレポートの表示モードと編集モードを切り替える | Microsoft Docs"
description: "Power BI Embedded でレポートの表示モードと編集モードを切り替える方法について説明します。"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 03/11/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: f73bf05b41523a5833cc9366fb84cb7021b4b7a9
ms.lasthandoff: 03/14/2017

---
# <a name="toggle-between-view-and-edit-mode-for-reports-in-power-bi-embedded"></a>Power BI Embedded でレポートの表示モードと編集モードを切り替える

Power BI Embedded でレポートの表示モードと編集モードを切り替える方法について説明します。

## <a name="creating-an-access-token"></a>アクセス トークンの作成

レポートを表示および編集できるようにするアクセス トークンを作成する必要があります。 レポートを編集して保存するには、**Report.ReadWrite** トークン アクセス許可が必要です。 詳細については、「[Power BI Embedded での認証と承認](power-bi-embedded-app-token-flow.md)」を参照してください。

> [!NOTE]
> これにより、既存のレポートを編集して変更を保存できます。 さらに、"**名前を付けて保存**" 操作をサポートする場合は、追加のアクセス許可を指定する必要があります。 詳細については、[Scopes](power-bi-embedded-app-token-flow.md#scopes) に関するページを参照してください。

```
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Report.ReadWrite";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="embed-configuration"></a>埋め込み構成

編集モードで [保存] ボタンを表示するには、permissions と viewMode を指定する必要があります。 詳細については、「[Embed configuration details (埋め込み構成の詳細)](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details)」を参照してください。

JavaScript の例:

```
   <div id="reportContainer"></div>

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
        permissions: models.Permissions.ReadWrite /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.View,
        settings: {
            filterPaneEnabled: true,
            navContentPaneEnabled: true
        }
    };

    // Get a reference to the embedded report HTML element
    var reportContainer = $('#reportContainer')[0];

    // Embed the report and display it within the div container.
    var report = powerbi.embed(reportContainer, config);
```

これは、**models.ViewMode.View** に設定されている **viewMode** に基づいて表示モードでレポートを埋め込むことを示します。

## <a name="view-mode"></a>表示モード

次の JavaScript を使用すると、モードが編集モードのときに表示モードに切り替えることができます。

```
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to view mode.
report.switchMode("view");

```

## <a name="edit-mode"></a>編集モード

次の JavaScript を使用すると、モードが表示モードのときに編集モードに切り替えることができます。

```
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to edit mode.
report.switchMode("edit");

```

## <a name="see-also"></a>関連項目

[Get started with Microsoft Power BI Embedded sample (Microsoft Power BI Embedded のサンプルの使用)](power-bi-embedded-get-started-sample.md)  
[レポートの埋め込み](power-bi-embedded-embed-report.md)  
[Power BI Embedded での認証と承認](power-bi-embedded-app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[JavaScript による埋め込みのサンプル](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[PowerBI-CSharp Git リポジトリ](https://github.com/Microsoft/PowerBI-CSharp)  
[PowerBI-Node Git リポジトリ](https://github.com/Microsoft/PowerBI-Node)  
ご質問は、 [Power BI コミュニティ](http://community.powerbi.com/)で尋ねてみてください。

