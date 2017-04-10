---
title: "Azure Application Insights のテクニカル サポート | Microsoft Docs"
description: "Application Insights で発生した問題に関するサポートを利用できます。"
services: application-insights
documentationcenter: 
author: alexbulankou
manager: douge
ms.assetid: 39035206-c836-4dca-93df-5013b7753aed
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/01/2016
ms.author: albulank
translationtype: Human Translation
ms.sourcegitcommit: 08ce387dd37ef2fec8f4dded23c20217a36e9966
ms.openlocfilehash: b3306ebddae08f07650d91feba25e90de55656b4


---
# <a name="get-help-with-application-insights"></a>Application Insights に関するサポートを利用する
[Azure Application Insights](app-insights-overview.md)で技術的な問題が発生した場合、サポートを利用するためのオプションは次のとおりです。

## <a name="1-check-the-documents"></a>1.ドキュメントを確認する
* データが見つからない場合は、 確認: [サンプリング](app-insights-sampling.md)、[クォータ、スロットル](app-insights-pricing.md)。
* トラブルシューティング: [ASP.NET](app-insights-troubleshoot-faq.md) | [Java](app-insights-java-troubleshoot.md)

## <a name="2-search-the-forums"></a>2.フォーラムを検索する
* [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)
* [StackOverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)

## <a name="3-azure-support-plan"></a>手順&3;.Azure サポート プランを確認する
開発者に自身の具体的な事例を調査してほしい場合もあります。 

[Microsoft Azure でサポート プラン](https://azure.microsoft.com/support/plans/)に加入している場合は、[サポート チケットを開く](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ことができます。

## <a name="4-feedback-to-the-application-insights-team"></a>4.Application Insights チームにフィードバックを送信する
Microsoft の開発チームは、発生した問題についてぜひ知りたいと考えています。上記の解決方法で問題が解決されなかった場合は特にそうです。 Azure Portal にあるフォームからフィードバックを送信してください。 Application Insights チームの開発者は、送信された内容に注意を払っています。

1. [Application Insights ポータル](https://portal.azure.com)で、右上隅にあるスマイル アイコンをクリックします。  
   
    ![フィードバック ボタン](./media/app-insights-get-dev-support/01.png)   
2. コメント ボックスで、最初の行に **AppInsights** を指定した後、次の情報を含めるようにしてください。   
   
    ```
   
    AppInsights   
    ikey: <instrumentation key>   
    sdk: <SDK that you are using, including name and version>  
    issue: <please describe the problem you are having>
   
    ```   
   
    ![フィードバックのダイアログ](./media/app-insights-get-dev-support/02.png)   
3. [はい、このフィードバックに関してメールをお送りいただいてもかまいません] チェック ボックスをオンにします。 
   
    ![送信セクション](./media/app-insights-get-dev-support/03.png)  






<!--HONumber=Jan17_HO4-->


