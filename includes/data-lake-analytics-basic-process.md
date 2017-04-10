**基本的な Data Lake Analytics のプロセス:**

![Azure Data Lake Analytics プロセスのフロー図](./media/data-lake-analytics-basic-process-include/data-lake-analytics-process.png)

1. Data Lake Analytics アカウントを作成します。
2. ソース データを準備します。 Data Lake Analytic ジョブでは、Azure Data Lake Store アカウントまたは Azure Blob Storage アカウントからデータを読み取ることができます。   
3. U-SQL スクリプトを開発します。
4. ジョブ (U-SQL スクリプト) を Data Lake Analytics アカウントに送信します。 ジョブはソース データから読み取り、U-SQL スクリプトで指示されたとおりにデータを処理して、Data Lake Store アカウントまたは Blob Storage アカウントに出力を保存します。



<!--HONumber=Jan17_HO3-->


