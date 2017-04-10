---
title: "Data Lake Tools for Visual Studio での頂点実行ビューの使用 | Microsoft Docs"
description: "頂点実行ビューを使用して Data Lake Analytics ジョブを試験する方法について説明します。"
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 5366d852-e7d6-44cf-a88c-e9f52f15f7df
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/13/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: ac5a64b759376c06e058ae015b73f1b73b7d1e7b
ms.openlocfilehash: b2acf4fd95c1611ecd580b508339d1305010fdd8


---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>Data Lake Tools for Visual Studio での頂点実行ビューの使用
頂点実行ビューを使用して Data Lake Analytics ジョブを試験する方法について説明します。

## <a name="prerequisites"></a>前提条件
* Data Lake Tools for Visual Studio を使用した U-SQL スクリプトの開発に関する基本的な知識。  「[チュートリアル: Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)」を参照してください。

## <a name="open-the-vertex-execution-view"></a>頂点実行ビューを開く
特定のジョブでは、左下隅にある [Vertex Execution View (頂点実行ビュー)] のリンクをクリックできます。 最初にプロファイルを読み込むよう求められる場合があります。ネットワーク接続によっては、この処理に時間がかかることがあります。

![Data Lake Analytics Tools の頂点実行ビュー](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>頂点実行ビューについて
頂点実行ビューを表示すると、3 つの部分で構成されています。

![Data Lake Analytics Tools の頂点実行ビュー](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

* 頂点セレクター: 左側に頂点セレクターがあります。  特性に基づいて頂点を選択できます (上位 10 のデータ読み取り、ステージ別の選択など)。
  
    最も一般的に使用されるフィルターの 1 つに、クリティカル パスの頂点があります。 クリティカル パスは、U-SQL ジョブの最も長いパスです。 クリティカル パスは、最も時間のかかっている頂点を確認することでジョブを最適化するために使用されます。
* 中央上部のウィンドウ:
  
    ![Data Lake Analytics Tools の頂点実行ビュー](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)
  
    このビューには、すべての頂点の実行中の状態も表示されます。 ここでは、時間がローカル コンピューターに合わせて変換され、それぞれの状態が異なる色で示されます。
* 中央下部のウィンドウ:
  
    ![Data Lake Analytics Tools の頂点実行ビュー](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)
  
  * [プロセス名]: 頂点インスタンスの名前。 名前は、StageName|VertexName|VertexRunInstance の各要素で構成されます。 たとえば、SV7_Split[62].v1 という頂点は、ステージ SV7_Split における頂点番号 62 の 2 番目の実行インスタンス (.v1。インデックスは 0 から始まる) を表します。
  * [Total Data Read (読み取られたデータの合計)]/[Total Data Written (書き込まれたデータの合計)]: この頂点によって読み取られたデータおよび書き込まれたデータ。
  * [状態]/[Exit Status (終了状態)]: 頂点が終了したときの最終的な状態。
  * [終了コード]/[Failure Type (エラーの種類)]: 頂点が失敗したときのエラー。
  * [Creation Reason (作成理由)]: 頂点が作成された理由。
  * [Resource Latency (リソースの待機時間)]/[Process Latency (プロセスの待機時間)]/[PN Queue Latency (PN キューの待機時間)]: 頂点がリソースを待機していた時間、データの処理にかかった時間、およびキューに保持された時間。
  * [プロセス]/[Creator GUID (作成者 GUID)]: 現在実行中の頂点またはその作成者の GUID。
  * [バージョン]: 実行中の頂点の N 番目のインスタンス (システムは、フェールオーバーや計算冗長性などのさまざまな理由で頂点の新しいインスタンスをスケジュールする場合があります)。
  * [Version Created Time (バージョンの作成時刻)]。
  * [Process Create Start Time (プロセスの作成開始時刻)]/[Process Queued Time (プロセスがキューに挿入された時刻)]/[Process Start Time (プロセス開始時刻)]/[Process Complete Time (プロセス完了時刻)]: 頂点プロセスの作成が開始された時刻、頂点プロセスのキューへの挿入が開始された時刻、特定の頂点プロセスが開始された時刻、および特定の頂点が完了した時刻。

## <a name="next-steps"></a>次のステップ
* Data Lake Analytics の概要については、「 [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)」を参照してください。
* U-SQL アプリケーションの開発を開始する場合は、「 [チュートリアル: Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)」をご覧ください。
* U-SQL の詳細については、「 [Azure Data Lake Analytics U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)」を参照してください。
* 管理タスクについては、「 [Azure Portal を使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-portal.md)」をご覧ください。
* 診断情報のログについては、「 [Accessing diagnostics logs for Azure Data Lake Analytics (Azure Data Lake Analytics の診断ログへのアクセス)](data-lake-analytics-diagnostic-logs.md)
* より複雑なクエリを表示する場合は、「 [チュートリアル: Azure Data Lake Analytics を使用して Web サイトのログを分析する](data-lake-analytics-analyze-weblogs.md)」をご覧ください。
* ジョブの詳細を表示する方法については、「[Azure Data Lake Analytics ジョブに対するジョブ ブラウザーとジョブ ビューの使用](data-lake-analytics-data-lake-tools-view-jobs.md)」を参照してください。
* Data Lake Tools for Visual Studio Code については、「[Use the Azure Data Lake Tools for Visual Studio Code (Azure Data Lake Tools for Visual Studio Code の使用)](data-lake-analytics-data-lake-tools-for-vscode.md)」を参照してください。



<!--HONumber=Nov16_HO4-->


