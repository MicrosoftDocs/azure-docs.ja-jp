---
title: "Azure Portal を使用する Azure Data Lake Analytics ジョブのトラブルシューティング | Microsoft Docs"
description: "Azure ポータルを使用して、Data Lake Analytics ジョブのトラブルシューティングを行う方法について説明します。 "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 194b5d79505afbfd0208f63dd182a0e03227ba69
ms.openlocfilehash: 1915201480f57c5f132e13bb5ce4283fbea975fe


---
# <a name="troubleshoot-azure-data-lake-analytics-jobs-using-azure-portal"></a>Azure ポータルを使用する Azure Data Lake Analytics ジョブのトラブルシューティング
Azure ポータルを使用して、Data Lake Analytics ジョブのトラブルシューティングを行う方法について説明します。

このチュートリアルでは、不足しているソース ファイルの問題をセットアップし、Azure ポータルを使用してその問題のトラブルシューティングを行います。

**前提条件**

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

* **Data Lake Analytics ジョブ プロセスの基本的な知識**。 「 [Azure ポータルで Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)」を参照してください。
* **Data Lake Analytics アカウント**。 「[Azure Portal で Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md#create-data-lake-analytics-account)」を参照してください。
* **既定の Data Lake Store アカウントへのサンプル データのコピー**。  「 [ソース データの準備](data-lake-analytics-get-started-portal.md#prepare-source-data)

## <a name="submit-a-data-lake-analytics-job"></a>Data Lake Analytics ジョブの送信
ここでは、不適切なソース ファイル名で U-SQL ジョブを作成します。  

**ジョブを送信するには**

1. Azure ポータルで、左上隅にある **[Microsoft Azure]** をクリックします。
2. Data Lake Analytics アカウント名のタイルをクリックします。  ここにはアカウントの作成時にピン留めされます。
   アカウントがそこにピン留めされていない場合は、 [ポータルから Analytics アカウントを開く](data-lake-analytics-manage-use-portal.md#access-adla-account)手順を参照してください。
3. 上部のメニューから **[新しいジョブ]** をクリックします。
4. ジョブ名および以下の U-SQL スクリプトを入力します。

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv1"
            USING Extractors.Tsv();

        OUTPUT @searchlog   
            TO "/output/SearchLog-from-adls.csv"
        USING Outputters.Csv();

    スクリプトで定義されているソース ファイルは **/Samples/Data/SearchLog.tsv1** ですが、これは **/Samples/Data/SearchLog.tsv** でなければなりません。
5. 上部の **[ジョブの送信]** をクリックします。 新しいジョブの詳細ペインが開きます。 タイトル バーに、ジョブの状態が示されます。 完了するまで数分かかります。 **[更新]** をクリックして最新の状態を取得することができます。
6. ジョブの状態が **[失敗]**に変わるまで待機します。  ジョブが **[成功]**と示された場合は、/Samples フォルダーが削除されていません。 チュートリアルの冒頭にある「 **前提条件** 」を参照してください。

小規模なジョブにどうしてこんなにも時間がかかるのか疑問に思われるかもしれません。  Data Lake Analytics はビッグ データを処理するように設計されていることに注意してください。  これは、分散システムを使用して大量のデータを処理する場合に役立ちます。

ジョブを送信したと仮定し、ポータルを閉じます。  次のセクションでは、ジョブのトラブルシューティングを行う方法を学習します。

## <a name="troubleshoot-the-job"></a>ジョブのトラブルシューティング
この最後のセクションでは、ジョブが送信済みで、そのジョブは失敗した状態にあります。  

**すべてのジョブを表示するには**

1. Azure ポータルで、左上隅にある **[Microsoft Azure]** をクリックします。
2. Data Lake Analytics アカウント名のタイルをクリックします。  ジョブの概要が **[ジョブ管理]** タイルに表示されます。

    ![Azure Data Lake Analytics ジョブの管理](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    [ジョブ管理] を見れば、ジョブの状態が一目でわかります。 失敗したジョブがあることに注目してください。
3. **[ジョブ管理]** タイルをクリックしてジョブを表示します。 ジョブは **[実行中]**、**[キューに登録済み]**、および **[終了]** に分類されます。 失敗したジョブは **[終了]** セクションに表示されます。 これがリストの先頭になります。 多くのジョブがある場合は、 **[フィルター]** をクリックしてジョブを検索できます。

    ![Azure Data Lake Analytics のジョブのフィルター](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. リストから失敗したジョブをクリックして、以下のように新しいブレードでジョブの詳細を開きます。

    ![Azure Data Lake Analytics の失敗したジョブ](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    **[再送信]** ボタンに注目してください。 問題を修正した後、ジョブを再送信することができます。
5. 前のスクリーンショットの強調表示されている部分をクリックして、エラーの詳細を開きます。  次のような結果が表示されます。

    ![Azure Data Lake Analytics の失敗したジョブの詳細](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    ソース フォルダーが見つからないことが示されています。
6. **[スクリプトの複製]**をクリックします。
7. **FROM** パスを以下のように更新します。

    "/Samples/Data/SearchLog.tsv"
8. **[ジョブの送信]**をクリックします。

## <a name="see-also"></a>関連項目
* [Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
* [Azure PowerShell で Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-powershell.md)
* [Visual Studio で Azure Data Lake Analytics と U-SQL の使用を開始する](data-lake-analytics-u-sql-get-started.md)
* [Azure ポータルを使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-portal.md)



<!--HONumber=Dec16_HO2-->


