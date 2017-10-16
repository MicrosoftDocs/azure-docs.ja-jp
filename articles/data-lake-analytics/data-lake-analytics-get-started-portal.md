---
title: "Azure Portal で Azure Data Lake Analytics の使用を開始する | Microsoft Docs"
description: "Azure Portal を使用して Data Lake Analytics アカウントを作成し、U-SQL で Data Lake Analytics ジョブを作成して、ジョブを送信する方法について説明します。 "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: b1584d16-e0d2-4019-ad1f-f04be8c5b430
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: edmaca
ms.openlocfilehash: 2722a2d72ed90ea0005362563ecaee30750c040a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-portal"></a>Azure Portal で Azure Data Lake Analytics の使用を開始する
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Azure Portal を使用して Azure Data Lake Analytics アカウントを作成し、[U-SQL](data-lake-analytics-u-sql-get-started.md) でジョブを定義して、Data Lake Analytics サービスにジョブを送信する方法について説明します。 Data Lake Analytics の詳細については、「 [Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、**Azure サブスクリプション**が必要です。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

## <a name="create-a-data-lake-analytics-account"></a>Data Lake Analytics アカウントを作成する

次に、Data Lake Analytics アカウントと Data Lake Store アカウントを同時に作成します。  この手順は単純であり、所要時間は約 60 秒です。

1. [Azure Portal](https://portal.azure.com) にサインオンします。
2. **[新規]** >  **[Data + Analytics]** > **[Data Lake Analytics]** の順にクリックします。
3. 次の項目の値を選択します。
   * **名前**: Data Lake Analytics アカウントに名前を付けます (英小文字と数字のみ使用できます)。
   * **サブスクリプション**: Analytics アカウントに使用する Azure サブスクリプションを選択します。
   * **リソース グループ**。 既存の Azure リソース グループを選択するか、新しいものを作成します。
   * **場所**。 Data Lake Analytics アカウントの Azure データ センターを選択します。
   * **Data Lake Store**: 以下の指示に従って、新しい Data Lake Store アカウントを作成するか、既存のものを選択します。 
4. 必要に応じて、Data Lake Analytics アカウントの価格レベルを選択します。
5. **Create** をクリックしてください。 


## <a name="your-first-u-sql-script"></a>最初の U-SQL スクリプト

次のテキストは、非常に単純な U-SQL スクリプトです。 このスクリプトが実行するのは、スクリプト内に小さなデータセットを定義し、そのデータセットを `/data.csv` という名前のファイルとして既定の Data Lake Store に書き出すことだけです。

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

## <a name="submit-a-u-sql-job"></a>U-SQL ジョブの送信

1. Data Lake Analytics アカウントから **[新しいジョブ]** をクリックします。
2. 上記の U-SQL スクリプトのテキストを貼り付けます。 
3. **[ジョブの送信]**をクリックします。   
4. ジョブの状態が **[成功]** に変わるまで待機します。
5. ジョブが失敗した場合は、[Data Lake Analytics ジョブの監視とトラブルシューティング](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)に関する記事をご覧ください。
6. **[出力]** タブをクリックし、`data.csv` をクリックします。 

## <a name="see-also"></a>関連項目

* U-SQL アプリケーションの開発を開始する場合は、「 [チュートリアル: Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)」をご覧ください。
* U-SQL の詳細については、「 [Azure Data Lake Analytics U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)」を参照してください。
* 管理タスクについては、「 [Azure Portal を使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-portal.md)」をご覧ください。
