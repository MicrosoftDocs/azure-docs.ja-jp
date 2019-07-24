---
title: Azure Application Insights のファネル
description: ファネルを使って、顧客によるアプリケーションの操作状況を把握する方法を説明します。
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/17/2017
ms.pm_owner: daviste;NumberByColors
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: 2cb7e15b701b53e74618c21bf219a355d495f985
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2019
ms.locfileid: "54076282"
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>Application Insights のファネルを使って、顧客がどのようにアプリケーションを利用しているか把握する

カスタマー エクスペリエンスの理解は、ビジネスにとって極めて重要です。 アプリケーションが複数のステージに渡る場合は、ほとんどの顧客がプロセス全体を進めているか、またはどこかの時点でプロセスを終了しているかを把握する必要があります。 Web アプリケーション内の一連の手順の進行は、*ファネル*と呼ばれています。 Azure Application Insights のファネルを使用して、ユーザーに対する洞察を取得し、手順ごとのコンバージョン レートを監視できます。 

## <a name="create-your-funnel"></a>ファネルを作成する
ファネルを作成する前に、答えを知りたいテーマを決めておきます。 たとえば、何人のユーザーがホーム ページを閲覧しているか、顧客プロファイルを閲覧しているか、チケットを作成しているか知りたい場合があります。 この例では、Fabrikam Fiber 社の事業主は、顧客チケットの作成に成功した顧客の割合を知りたいと考えています。

この事業主がファネルを作成するために行う手順は、次のとおりです。

1. Application Insights のファネル ツールで、**[新規]** を選択します。
1. **[時間の範囲]** ドロップダウン メニューから、**[90 日以内]** を選択します。 **[私のじょうご]** または **[共有じょうご]** を選択します。
1. **[手順 1]** ドロップダウン リストから、**[インデックス]** を選択します。 
1. **[手順 2]** リストから、**[顧客]** を選択します。
1. **[手順 3]** リストから、**[作成]** を選択します。
1. ファネルに名前を付けて、**[保存]** をクリックします。

以下のスクリーンショットは、ファネル ツールによって生成されるデータの種類の例を示しています。 Fabrikam の事業主は、過去 90 日間に、ホーム ページを閲覧した顧客の 54.3 パーセントが顧客チケットを作成したことを確認できます。 2,700 人の顧客がホーム ページからインデックスを訪れたことも分かります。 これは更新に関する問題を示唆している可能性があります。


![ファネル ツールとデータのスクリーンショット](media/usage-funnels/funnel1.png)

### <a name="funnels-features"></a>ファネルの機能
前掲のスクリーンショットには、強調表示されている領域が 5 箇所あります。 これらはファネルの機能です。 スクリーンショットの対応する各領域についての詳細は、次のとおりです。
1. アプリがサンプリングされる場合は、サンプリング バナーが表示されます。 バナーを選択すると、サンプリングをオフにする方法を説明するコンテキスト ペインが開きます。 
2. じょうごを [Power BI](../../azure-monitor/app/export-power-bi.md ) にエクスポートできます。
3. 手順を選択すると、右側に詳細が表示されます。 
4. コンバージョン履歴のグラフによって、過去 90 日間のコンバージョン レートが表示されます。 
5. ユーザー ツールにアクセスすることで、ユーザーの理解を深めます。 各ステップでフィルターを使用できます。 

## <a name="next-steps"></a>次の手順
  * [利用状況の概要](usage-overview.md)
  * [ユーザー、セッション、およびイベント](usage-segmentation.md)
  * [保持](usage-retention.md)
  * [ブック](../../azure-monitor/app/usage-workbooks.md)
  * [ユーザー コンテキストの追加](usage-send-user-context.md)
  * [Power BI へのエクスポート](../../azure-monitor/app/export-power-bi.md )

