---
title: "Azure Application Insights のファネル"
description: "ファネルを使って、顧客によるアプリケーションの操作状況を把握する方法を説明します。"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 2b25ffd844507074a8467b1135b1331feb9d3ed7
ms.contentlocale: ja-jp
ms.lasthandoff: 08/09/2017

---

# <a name="discover-how-customers-are-using-your-application-with-the-application-insights-funnels"></a>Application Insights のファネルを使って、顧客がどのようにアプリケーションを利用しているかを把握します。

カスタマー エクスペリエンスを理解することは、ビジネスにとって極めて重要です。 アプリケーションが複数のステージに関わっている場合は、ほとんどの顧客がプロセス全体を円滑に処理しているか、または任意の時点でプロセスを終了しているかを把握する必要があります。 Web アプリケーションにおける一連の手順の進行は、"ファネル" と呼ばれています。 Application Insights のファネルを使って、ユーザーに対する洞察を得て、手順ごとの変換率を監視します。 

## <a name="get-started-with-the-funnels-blade"></a>ファネルのブレードの使い方
ファネルを習得するための最も簡単な方法は、サンプルを確認することです。 以下の説明は、e コマース ビジネスの事業主が顧客による Web アプリケーションの操作状況を把握するために実行する手順を示しています。  

### <a name="create-your-funnel"></a>ファネルを作成する
ファネルを作成する前に、自分が答えを知りたいテーマを決めておく必要があります。 たとえば、広告をクリックしてホーム ページを閲覧している顧客の数を知りたい場合などがあります。 この例では、Fabrikam Fiber 社の事業主は、先月中に買い物かごにアイテムを追加した後に購入を行った顧客の割合を知りたいと考えています。

この事業主がファネルを作成するために行う手順は、次のとおりです。

1. ファネルのブレードで、[新規] ボタンをクリックします。
1. **[時間の範囲]** ボックスの一覧から、"先月" という時間の範囲を選びます。 
1. **[手順 1]** ボックスの一覧から、**[Product page]\(商品ページ\)** イベントを選びます。 
1. **[手順 2]** ボックスの一覧から、**[Add to shopping cart]\(買い物かごに追加する\)** イベントを選びます。
1. **[手順 3]** ボックスの一覧から、**[Click purchase]\(購入をクリックする\)** イベントを選びます。
1. ファネルに名前を付けて、**[保存]** をクリックします。

次の図は、ファネルのブレードによって生成されたデータを示しています。 この図から、Fabrikam 社の事業主は、先週中に買い物かごにアイテムを追加した顧客の 22.7% が購入を完了したことがわかります。 また、顧客の 1% は商品ページを訪問する前に広告をクリックしており、顧客の 20% は購入を完了した後にサインアウトしたこともわかります。


![データを示したファネルのブレード](./media/app-insights-understand-usage-patterns/funnel1.png)

## <a name="next-steps"></a>次のステップ
  * [利用状況の概要](app-insights-usage-overview.md)
  * [ユーザー、セッション、およびイベント](app-insights-usage-segmentation.md)
  * [保持](app-insights-usage-retention.md)
  * [ブック](app-insights-usage-workbooks.md)
  * [ユーザー コンテキストの追加](app-insights-usage-send-user-context.md)

