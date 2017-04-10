---
title: "Application Insights: コード サンプルとチュートリアル"
description: "自分のアプリに合わせて調整できるサンプル。"
services: application-insights
documentationcenter: windows
author: alancameronwills
manager: douge
ms.assetid: 586d0fb2-5286-449b-b327-5c9493632fc1
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 01/05/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: d84ab993b1d9489ca9d2edaa1cb9672d9bced899
ms.openlocfilehash: 2a9cb8062e1dc505706c285269bbbd1eeab72064


---
# <a name="application-insights-code-samples-and-walkthroughs"></a>Application Insights: コード サンプルとチュートリアル


これは、 [Azure Application Insights](app-insights-overview.md)の使い方を示すコード サンプルをまとめたものです。

## <a name="sdk-labs"></a>SDK ラボ
[SDK ラボ](https://www.myget.org/gallery/applicationinsights-sdk-labs) は、Application Insights SDK の追加機能としてインストール、アンインストールできる実験用の NuGet パッケージです。 実際にお試しいただき、フィードバックをお寄せください。

## <a name="web-services"></a>Web サービス
* [テレメトリを Azure Web および worker ロールに追加します](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)。
* [Windows 8.1 ユニバーサル アプリ](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/Windows%208.1%20Universal/)

## <a name="continuous-export"></a>連続エクスポート
テレメトリ データを独自のツールで分析するには、ストレージに[そのテレメトリ データをエクスポートします](app-insights-export-telemetry.md)。そこで、解析して処理できます。

* [Power BI へのエクスポート](app-insights-export-power-bi.md) 
* [worker ロールを使用してエクスポートされたデータを解析する](app-insights-code-sample-export-telemetry-sql-database.md)
* [Stream Analytics を使用して SQL にエクスポートする](app-insights-code-sample-export-sql-stream-analytics.md)
* [Microsoft Dynamics CRM のテレメトリの有効化](app-insights-sample-mscrm.md)

## <a name="automate-tasks"></a>タスクの自動化
* [PowerShell を使用した Application Insights への Azure 診断の送信](app-insights-powershell.md)
* [新しい Application Insights リソースを作成するスクリプト](app-insights-powershell-script-create-resource.md)
* [PowerShell を使用した Application Insights への Azure 診断の送信](app-insights-powershell-azure-diagnostics.md)




<!--HONumber=Nov16_HO3-->


