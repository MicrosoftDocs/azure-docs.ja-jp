---
title: "Stream Analytics でクエリを記述する方法 | Microsoft Docs"
description: "Stream Analytics でのクエリ記述とクエリ データ | ラーニング パス セグメント。"
keywords: "クエリの記述方法、クエリ データ、クエリの記述、クエリの記述"
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 0e9cdadd-0ee0-4bee-b65b-4a06fb863c95
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4889a2c810dfd6b1cccd00cb72a5c126f09efe99
ms.lasthandoff: 11/17/2016


---
# <a name="how-to-write-queries-in-stream-analytics"></a>Stream Analytics でクエリを記述する方法
Azure Stream Analytics のストリーム処理ロジックのクエリ記述は、ジョブが開始する前に定義され、ジョブに到着したデータに対して実行される、"継続クエリ" として実装されます。 データの変換は、SQL に似たクエリ言語で表されます。この言語は、主として T-SQL のサブセットであり、時間的なセマンティクスを表すために使用される[ウィンドウ化](https://msdn.microsoft.com/library/azure/dn835019.aspx)などの言語拡張機能が追加されています。

## <a name="writing-queries"></a>クエリの記述:
1. Microsoft Azure 管理ポータルの Stream Analytics ジョブで、 **[クエリ]**をクリックします。
   
    ![クエリの選択](./media/stream-analytics-write-queries/1-stream-analytics-write-queries.png)  
   
    Azure ポータルで、 **[クエリ]**をクリックします。
   
    ![クエリの選択のプレビュー](./media/stream-analytics-write-queries/query-preview-portal.png)  
2. 作業を始めやすいように、新しいジョブにはクエリ テンプレートが付いています。 クエリ テンプレートは、入力イベントのすべてのフィールドを出力に反映させる「パススルー」クエリを実行します。  
   
   * ジョブに少なくとも 1 つの入力と出力が定義されている場合、プレースホルダーの "[YourOutputAlias]" および "[YourInputAlias]" フィールドを最初に使用する入力および出力のエイリアスに置き換えることができます。 さらに、ジョブで入力と出力を定義していなくても、Azure クラシック ポータルでクエリを作成してテストできます。
   * 単純なパススルーでは対応できない処理を実行する必要がある場合は、クエリの定義を編集できます。 クエリの作成を始めるに際は、よく使用されるクエリのパターンを [こちら](stream-analytics-stream-analytics-query-patterns.md)で確認してください。  
   
   ![クエリ データ ウィンドウ](./media/stream-analytics-write-queries/2-stream-analytics-write-queries.png)  

## <a name="to-validate-query-data-is-working"></a>クエリ データが動作することを検証するには:
テスト データを含むローカル JSON ファイルを使用してブラウザーでクエリを実行することにより、クエリが意図したとおりに動作することを確認できます。 これを行っても、ジョブが開始したり、課金に影響することはありません。

> [!NOTE]
> 現在、Azure ポータルでは、ブラウザー内クエリ テストはサポートされていません。  
> 
> 

1. クエリにエラーがないことを確認し (エラーがある場合は [テスト] ボタンが無効になります)、[テスト] ボタンをクリックします。  
   
   ![クエリ データ テスト](./media/stream-analytics-write-queries/3-stream-analytics-write-queries.png)  
2. クエリで参照されている各入力に対するファイルの指定を求められます。 この例では、テンプレートのクエリをそのままに使用しているので、ダイアログで要求される入力の名前は "yourinputalias" です。  
   
   ![テスト データ クエリ](./media/stream-analytics-write-queries/4-stream-analytics-write-queries.png)  
3. テスト ファイルを参照します。 [github](https://github.com/Azure/azure-stream-analytics/tree/master/Sample Data) では複数のサンプル ファイルを入手でき、[入力] タブの [サンプル データ] 機能で独自のデータ ストリーム入力からサンプル データを取得することもできます。  
   
   ![クエリの入力](./media/stream-analytics-write-queries/5-stream-analytics-write-queries.png)  
4. ダイアログ ボックスを閉じると、テスト データに対してクエリが実行され、[クエリ] ページの下部に結果が表示されます。  
   
   ![クエリの概要](./media/stream-analytics-write-queries/6-stream-analytics-write-queries.png)  

## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-get-started.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)


