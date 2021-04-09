---
title: Azure Data Lake Analytics の定期的なジョブをデバッグする
description: Azure Data Lake Tools for Visual Studio Code を使って異常な定期的ジョブをデバッグする方法について説明します。
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 05/20/2018
ms.openlocfilehash: b95d4b0a082e8eac191d610c9516e5ce72180b4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92219175"
---
# <a name="troubleshoot-an-abnormal-recurring-job"></a>異常な定期的ジョブのトラブルシューティング

この記事では、[Azure Data Lake Tools for Visual Studio](https://aka.ms/adltoolsvs) を使って定期的なジョブの問題のトラブルシューティングを行う方法を示します。 パイプラインと定期的なジョブについて詳しくは、[Azure Data Lake と Azure HDInsight のブログ](/archive/blogs/azuredatalake/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy)をご覧ください。

通常、定期的なジョブは同じクエリ ロジックと似た入力データを共有します。 たとえば、毎週月曜日の午前 8 時に実行して 前週のアクティブなユーザーをカウントする定期的なジョブがあるとします。 これらのジョブのスクリプトはクエリ ロジックを含む 1 つのスクリプト テンプレートを共有します。 これらのジョブの入力は過去 1 週間の使用状況データです。 同じクエリ ロジックを共有して入力が似ていると、通常はこれらのジョブのパフォーマンスは似ていて安定しています。 定期的なジョブのいずれかで突然異常な実行、障害、速度低下が大量に発生した場合は、次のことを行います。

- 定期的なジョブのプレビュー実行の統計情報レポートを見て、何が起きたかを確認します。
- 異常なジョブと正常なジョブを比較して、何が変化したかを明らかにします。

Azure Data Lake Tools for Visual Studio の **関連ジョブ ビュー** を使うと、どちらの場合についてもトラブルシューティングがはかどります。

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>ステップ 1: 定期的なジョブを探して関連ジョブ ビューを開く

関連ジョブ ビューを使って定期的なジョブの問題のトラブルシューティングを行うには、最初に Visual Studio で定期的なジョブを探してから、関連ジョブ ビューを開く必要があります。

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>ケース 1: 定期的なジョブの URL がわかっている

**[ツール]**  >  **[Data Lake]**  >  **[ジョブ ビュー]** の順に選択し、ジョブの URL を貼り付けて Visual Studio でジョブ ビューを開きます。 **[関連ジョブの表示]** を選択して関連ジョブ ビューを開きます。

![Data Lake Analytics Tools の [関連ジョブの表示] リンク](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>ケース 2: 定期的なジョブのパイプラインはあるが、URL はわからない

Visual Studio で、[サーバー エクスプローラー] > Azure Data Lake Analytics のアカウント > **[パイプライン]** の順に選んで、パイプライン ブラウザーを開くことができます。 ([サーバー エクスプローラー] でこのノードが見つからない場合は、[最新のプラグインをダウンロード](https://aka.ms/adltoolsvs)してください。) 

![[パイプライン] ノードの選択](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

パイプライン ブラウザーでは、Data Lake Analytics アカウントのすべてのパイプラインが左側に表示されます。 パイプラインを展開してすべての定期的なジョブを表示し、問題があるジョブを選択できます。 右側に関連ジョブ ビューが開きます。

![パイプラインを選択して関連ジョブ ビューを開く](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-a-statistics-report"></a>ステップ 2: 統計レポートを分析する

概要および統計レポートは関連ジョブ ビューの上部に表示されます。 そこで、問題の根本原因を発見できます。 

1.  レポートでは、X 軸はジョブの送信時刻を示します。 これを使用して異常なジョブを見つけます。
2.  次の図のプロセスを使用して、統計情報を確認し、問題および考えられる解決策に関する洞察を得ます。

![統計の確認のプロセス図](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-job-to-a-normal-job"></a>ステップ 3: 異常なジョブと正常なジョブを比較する

関連ジョブ ビューの下部にあるジョブの一覧で、すべての送信された定期的なジョブを見つけることができます。 詳細情報および考えられる解決策を見つけるには、異常なジョブを右クリックします。 ジョブの相違点ビューを使用して、異常なジョブと以前の正常なジョブを比較します。

![ジョブを比較するためのショートカット メニュー](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

この 2 つのジョブの大きな違いに注目してください。 これらの違いがおそらくパフォーマンスの問題の原因です。 さらに確認するには、次の図の手順を使用します。

![ジョブ間の相違点を確認するためのプロセス図](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>次のステップ

* [データ スキュー問題を解決する](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [失敗した U-SQL ジョブに対するユーザー定義の C# コードをデバッグする](data-lake-analytics-debug-u-sql-jobs.md)