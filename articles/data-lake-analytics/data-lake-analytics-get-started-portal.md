---
title: Azure portal で Azure Data Lake Analytics の使用を開始する
description: Azure portal を使って Azure Data Lake Analytics アカウントを作成し、U-SQL ジョブを送信します。
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: b1584d16-e0d2-4019-ad1f-f04be8c5b430
ms.topic: conceptual
ms.date: 03/21/2017
ms.openlocfilehash: 25d58bdc5791de868c6302b4d2763fa34e98af17
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60615037"
---
# <a name="get-started-with-azure-data-lake-analytics-using-the-azure-portal"></a>Azure portal で Azure Data Lake Analytics の使用を開始する
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

この記事では、Azure portal を使用して Azure Data Lake Analytics アカウントを作成し、[U-SQL](data-lake-analytics-u-sql-get-started.md) でジョブを定義して、Data Lake Analytics サービスにジョブを送信する方法について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、**Azure サブスクリプション**が必要です。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

## <a name="create-a-data-lake-analytics-account"></a>Data Lake Analytics アカウントを作成する

次に、Data Lake Analytics と Azure Data Lake Storage Gen1 アカウントを同時に作成します。  この手順は単純であり、所要時間は約 60 秒です。

1. [Azure Portal](https://portal.azure.com) にサインオンします。
2. **[リソースの作成]**  >   **[データ + 分析]**  >  **[Data Lake Analytics]** の順にクリックします。
3. 次の項目の値を選択します。
   * **[名前]** :Data Lake Analytics アカウントに名前を付けます (小文字と数字のみが許可されています)。
   * **サブスクリプション**:Analytics アカウントに使用される Azure サブスクリプションを選択します。
   * **リソース グループ**。 既存の Azure リソース グループを選択するか、新しいものを作成します。
   * **場所**。 Data Lake Analytics アカウントの Azure データ センターを選択します。
   * **[Data Lake Storage Gen1]** :手順に従って新しい Data Lake Storage Gen1 アカウントを作成するか、または既存のアカウントを選択します。 
4. 必要に応じて、Data Lake Analytics アカウントの価格レベルを選択します。
5. **Create** をクリックしてください。 


## <a name="your-first-u-sql-script"></a>最初の U-SQL スクリプト

次のテキストは、非常に単純な U-SQL スクリプトです。 このスクリプトが実行するのは、スクリプト内に小さなデータセットを定義し、そのデータセットを `/data.csv` という名前のファイルとして既定の Data Lake Storage Gen1 アカウントに書き出すことだけです。

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

1. Data Lake Analytics アカウントから **[新しいジョブ]** を選択します。
2. 上記の U-SQL スクリプトのテキストを貼り付けます。 ジョブに名前を付けます。 
3. **[送信]** ボタンを選択して、ジョブを開始します。   
4. ジョブの **[状態]** を監視して、ジョブの状態が **[成功]** に変わるまで待ちます。
5. **[データ]** タブで、 **[出力]** タブを選択します。`data.csv` という名前の出力ファイルを選択し、出力データを表示します。

## <a name="see-also"></a>関連項目

* U-SQL アプリケーションの開発を開始する場合は、「 [チュートリアル: Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)」をご覧ください。
* U-SQL の詳細については、「 [Azure Data Lake Analytics U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)」を参照してください。
* 管理タスクについては、「 [Azure Portal を使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-portal.md)」をご覧ください。
