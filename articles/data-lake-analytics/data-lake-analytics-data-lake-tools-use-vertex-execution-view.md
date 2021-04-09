---
title: Data Lake Tools for Visual Studio での頂点実行ビュー
description: この記事では、頂点実行ビューを使用して Data Lake Analytics ジョブを試験する方法について説明します。
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 10/13/2016
ms.openlocfilehash: 3fba7bdaa5db1d812fbcd479e5f1eab50c8d1032
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92215860"
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>Data Lake Tools for Visual Studio での頂点実行ビューの使用
頂点実行ビューを使用して Data Lake Analytics ジョブを試験する方法について説明します。


## <a name="open-the-vertex-execution-view"></a>頂点実行ビューを開く
Data Lake Tools for Visual Studio で U-SQL ジョブを開きます。 左下の **[頂点実行ビュー]** をクリックします。 最初にプロファイルを読み込むよう求められる場合があります。ネットワーク接続によっては、この処理に時間がかかることがあります。

![Data Lake Analytics Tools の頂点実行ビューを示すスクリーンショット](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>頂点実行ビューについて
頂点実行ビューは次の 3 つの部分で構成されています。

![[頂点セレクター] と中央上部および中央下部のウィンドウが強調表示されている頂点実行ビューを示すスクリーンショット。](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

左側の **[頂点セレクター]** を使用して、特性に基づいて頂点を選択できます (上位 10 のデータ読み取り、ステージ別の選択など)。 最も一般的に使用されるフィルターの 1 つに、 **[クリティカル パスの頂点]** があります。 **[クリティカル パス]** は、U-SQL ジョブの頂点の最も長いチェーンです。 クリティカル パスを理解すると、最も時間のかかっている頂点を確認することでジョブを最適化できるので便利です。
  
!["すべての頂点の実行中の状態" を表示する、頂点実行ビューの上部中央のウィンドウを示すスクリーンショット。](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

上部中央のウィンドウには、**すべての頂点の実行中の状態** も表示されます。
  
![各頂点に関する情報を表示する、頂点実行ビューの下部中央のウィンドウを示すスクリーンショット。](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

下部中央のウィンドウは、各頂点に関する情報が表示されます。
* [プロセス名]: 頂点インスタンスの名前。 名前は、StageName|VertexName|VertexRunInstance の各要素で構成されます。 たとえば、SV7_Split[62].v1 という頂点は、ステージ SV7_Split における頂点番号 62 の 2 番目の実行インスタンス (.v1。インデックスは 0 から始まる) を表します。
* [Total Data Read] (読み取られたデータの合計)/[Total Data Written] (書き込まれたデータの合計): この頂点によって読み取られたデータおよび書き込まれたデータ。
* [状態]/[Exit Status] (終了状態): 頂点が終了したときの最終的な状態。
* [終了コード]/[Failure Type] (エラーの種類): 頂点が失敗したときのエラー。
* [Creation Reason] (作成理由): 頂点が作成された理由。
* [Resource Latency (リソースの待機時間)]/[Process Latency (プロセスの待機時間)]/[PN Queue Latency (PN キューの待機時間)]: 頂点がリソースを待機していた時間、データの処理にかかった時間、およびキューに保持された時間。
* [プロセス]/[Creator GUID] (作成者 GUID): 現在実行中の頂点またはその作成者の GUID。
* [バージョン]: 実行中の頂点の N 番目のインスタンス (システムは、フェールオーバーや計算冗長性などのさまざまな理由で頂点の新しいインスタンスをスケジュールする場合があります)。
* [Version Created Time (バージョンの作成時刻)]。
* [Process Create Start Time (プロセスの作成開始時刻)]/[Process Queued Time (プロセスがキューに挿入された時刻)]/[Process Start Time (プロセス開始時刻)]/[Process Complete Time (プロセス完了時刻)]: 頂点プロセスの作成が開始された時刻、頂点プロセスのキューへの挿入が開始された時刻、特定の頂点プロセスが開始された時刻、および特定の頂点が完了した時刻。

## <a name="next-steps"></a>次のステップ
* 診断情報のログについては、「 [Accessing diagnostics logs for Azure Data Lake Analytics (Azure Data Lake Analytics の診断ログへのアクセス)](data-lake-analytics-diagnostic-logs.md)
* より複雑なクエリを表示する場合は、「 [チュートリアル: Azure Data Lake Analytics を使用して Web サイトのログを分析する](data-lake-analytics-analyze-weblogs.md)」をご覧ください。
* ジョブの詳細を表示する方法については、「[Azure Data Lake Analytics ジョブに対するジョブ ブラウザーとジョブ ビューの使用](data-lake-analytics-data-lake-tools-view-jobs.md)」を参照してください。
