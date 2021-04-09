---
title: Azure Data Lake Analytics の監視 - Azure portal
description: この記事では、Azure Portal を使用して、Data Lake Analytics ジョブのトラブルシューティングを行う方法について説明します。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 12/05/2016
ms.openlocfilehash: ddcf41a333d261bd9c5f669fde724a25eeba670e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92220280"
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>Azure Portal を使用して Azure Data Lake Analytics でジョブを監視する

## <a name="to-see-all-the-jobs"></a>すべてのジョブを表示するには

1. Azure ポータルで、左上隅にある **[Microsoft Azure]** をクリックします。

2. Data Lake Analytics アカウント名のタイルをクリックします。  ジョブの概要が **[ジョブ管理]** タイルに表示されます。

   ![Azure Data Lake Analytics ジョブの管理](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    [ジョブ管理] を見れば、ジョブの状態が一目でわかります。 失敗したジョブがあることに注目してください。
3. **[ジョブ管理]** タイルをクリックしてジョブを表示します。 ジョブは **[実行中]** 、 **[キューに登録済み]** 、および **[終了]** に分類されます。 失敗したジョブは **[終了]** セクションに表示されます。 これがリストの先頭になります。 多くのジョブがある場合は、 **[フィルター]** をクリックしてジョブを検索できます。

   ![Azure Data Lake Analytics のジョブのフィルター](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)

4. リストから失敗したジョブをクリックして、以下のようにジョブの詳細を開きます。

   ![Azure Data Lake Analytics の失敗したジョブ](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    **[再送信]** ボタンに注目してください。 問題を修正した後、ジョブを再送信することができます。

5. 前のスクリーンショットの強調表示されている部分をクリックして、エラーの詳細を開きます。  次のような結果が表示されます。

   ![Azure Data Lake Analytics の失敗したジョブの詳細](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

   ソース フォルダーが見つからないことが示されています。

6. **[スクリプトの複製]** をクリックします。

7. **FROM** パスを `/Samples/Data/SearchLog.tsv` に更新します。

8. **[ジョブの送信]** をクリックします。

## <a name="next-steps"></a>次のステップ

* [Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
* [Azure PowerShell で Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-powershell.md)
* [Azure  Portal を使用して Azure Data Lake Analytics を管理する](data-lake-analytics-manage-use-portal.md)
