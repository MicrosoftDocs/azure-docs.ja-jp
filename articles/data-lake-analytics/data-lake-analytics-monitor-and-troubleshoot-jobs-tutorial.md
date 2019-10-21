---
title: Azure Data Lake Analytics の監視 - Azure portal
description: この記事では、Azure Portal を使用して、Data Lake Analytics ジョブのトラブルシューティングを行う方法について説明します。
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 7fb85f179f1e1e1ac873fc7d6d937b34c1d48ada
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316585"
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>Azure Portal を使用して Azure Data Lake Analytics でジョブを監視する

**すべてのジョブを表示するには**

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
7. **FROM** パスを以下のように更新します。

    "/Samples/Data/SearchLog.tsv"
8. **[ジョブの送信]** をクリックします。

## <a name="see-also"></a>関連項目
* [Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
* [Azure PowerShell で Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-powershell.md)
* [Azure  Portal を使用して Azure Data Lake Analytics を管理する](data-lake-analytics-manage-use-portal.md)
