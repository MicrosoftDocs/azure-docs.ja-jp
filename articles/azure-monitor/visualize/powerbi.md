---
title: Azure Log Analytics データを Power BI にインポートする | Microsoft Docs
description: Power BI は、さまざまなデータのセットの分析のために豊富な視覚エフェクトとレポートを提供するマイクロソフトのクラウド ベースのビジネス分析サービスです。  この記事では、Log Analytics のデータを構成し、Power BI にインポートする方法と、それを自動的に更新するように構成する方法について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2019
ms.openlocfilehash: 69e953f6d2cb9542a589fb2e282ff166c43fc0a8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102043611"
---
# <a name="import-azure-monitor-log-data-into-power-bi"></a>Azure Monitor ログ データを Power BI にインポートする


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) は、さまざまなデータのセットの分析のために豊富な視覚エフェクトとレポートを提供するマイクロソフトのクラウド ベースのビジネス分析サービスです。  Azure Monitor ログ クエリの結果を Power BI のデータセットにインポートできます。そうすることでさまざまなソースのデータを組み合わせたり、Web とモバイルのデバイス上でレポートを共有したりするなどの機能を利用できます。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview"></a>概要
Azure Monitor の [Log Analytics ワークスペース](../logs/manage-access.md) から Power BI にデータをインポートするには、Azure Monitor の[ログ クエリ](../logs/log-query-overview.md)に基づいて Power BI にデータベースを作成します。  クエリはデータセットが更新されるたびに実行されます。  それからデータセットのデータを使用した Power BI レポートを作成できます。  Power BI でデータセットを作成するには、Log Analytics から [Power Query (M) 言語](/powerquery-m/power-query-m-language-specification)にクエリをエクスポートします。  次にこのクエリを使用して Power BI Desktop でクエリを作成し、Power BI にデータセットとして発行します。  このプロセスの詳細を以下で説明します。

![Power BI への Log Analytics](media/powerbi/overview.png)

## <a name="export-query"></a>クエリをエクスポートする
まず、Power BI データセットに取り込むデータを返す[ログ クエリ](../logs/log-query-overview.md)を作成します。  次にそのクエリを Power BI Desktop で使用できる [Power Query (M) 言語](/powerquery-m/power-query-m-language-specification)にエクスポートします。

> [!WARNING]
> 実行に長時間かかることがないように、[クエリを最適化](../logs/query-optimization.md)しないと、タイムアウトするおそれがあることにご注意ください。 エクスポートされたクエリの **timespan** 値に、クエリによって取得されるデータの期間が定義されていることにご注意ください。 クエリから返されるデータの量を制限するために必要な最小の timespan を使用します。

1. データセットのデータを抽出するために [Log Analytics にログ クエリを作成します](../logs/log-analytics-tutorial.md)。
2. **[エクスポート]**  >  **[Power BI Query (M)]** の順に選択します。  これにより、**PowerBIQuery.txt** という名前のテキスト ファイルにクエリがエクスポートされます。 

    ![ログ検索のエクスポート](media/powerbi/export-analytics.png)

3. テキスト ファイルを開き、その内容をコピーします。

## <a name="import-query-into-power-bi-desktop"></a>Power BI Desktop にクエリをインポートする
Power BI Desktop は、Power BI に発行可能なデータセットおよび レポートを作成できるデスクトップ アプリケーションです。  これを使用して、Azure Monitor からエクスポートした Power Query 言語を使ってクエリを作成することもできます。 

1. まだお持ちでない場合は [Power BI Desktop](https://powerbi.microsoft.com/desktop/) をインストールし、アプリケーションを開きます。
2. **[データの取得]**  >  **[空のクエリ]** の順に選択して新しいクエリを開きます。  次に **[詳細エディター]** を選択し、エクスポートしたファイルの内容をクエリに貼り付けます。 **[Done]** をクリックします。

    ![Power BI Desktop クエリ](media/powerbi/desktop-new-query.png)

5. クエリが実行され、結果が表示されます。  Azure に接続するための資格情報を要求される場合があります。  
6. クエリにわかりやすい名前を入力します。  規定値は "**Query1**" です。 **[閉じて適用]** をクリックして、レポートにデータセットを追加します。

    ![Power BI Desktop の名前](media/powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Power BI に発行する
Power BI に発行すると、データセットとレポートが作成されます。  Power BI Desktop でレポートを作成すると、お使いのデータを使用したレポートが発行されます。  そうしない場合は、空のレポートが作成されます。  Power BI でレポートを変更したり、データセットに基づく新しいレポートを作成したりできます。

1. お使いのデータに基づくレポートを作成します。  不慣れな場合には [Power BI Desktop のドキュメント](/power-bi/desktop-report-view)を使用してください。  
1. Power BI に送信する準備ができたら、 **[発行]** をクリックします。  
1. メッセージが表示されたら、Power BI アカウント内で発行先を選択します。  特定の発行先がない限りは、 **[マイ ワークスペース]** を使用します。

    ![Power BI Desktop の発行](media/powerbi/desktop-publish.png)

1. 発行が完了したら **[Power BI で開く]** をクリックし、新しいデータセットで Power BI を開きます。


### <a name="configure-scheduled-refresh"></a>スケジュールされた更新を構成する
Power BI で作成されたデータセットには、前に Power BI Desktop で見たのと同じデータがあります。  再度クエリを実行して Azure Monitor から最新データを取り込むために、データセットを定期的に更新する必要があります。  

1. レポートをアップロードしたワークスペースをクリックし、 **[データセット]** メニューを選択します。 
1. 新しいデータセットの横にあるコンテキスト メニューを選択し、 **[設定]** を選択します。 
1. **[データ ソースの資格情報]** に、資格情報が無効であることを示すメッセージがあるはずです。  これは、そのデータを更新するときにデータセットが使用する資格情報を、まだ指定していないためです。  
1. **[資格情報の編集]** をクリックし、Azure Monitor の Log Analytics ワークスペースにアクセスできる資格情報を指定します。 2 要素認証が必要な場合は、 **[認証方法]** で **[OAuth2]** を選択して、自分の資格情報でログインするように求められるようにします。

    ![Power BI のスケジュール](media/powerbi/powerbi-schedule.png)

5. **[スケジュールされている更新]** で、 **[データを最新の状態に保つ]** オプションをオンにします。  必要に応じて **[更新の頻度]** および更新を実行する 1 つまたは複数の特定の時間を変更できます。

    ![Power BI の更新](media/powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>次のステップ
* [ログ検索](../logs/log-query-overview.md) について学習し、Power BI にエクスポートできるクエリを作成します。
* [Power BI](https://powerbi.microsoft.com) について詳しく学習し、Azure Monitor ログのエクスポートに基づいて視覚エフェクトを作成します。