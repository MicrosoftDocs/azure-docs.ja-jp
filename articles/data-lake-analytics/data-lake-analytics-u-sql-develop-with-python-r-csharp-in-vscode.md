---
title: Python、R、C# で U-SQL ジョブを実行する - Azure Data Lake Analytics
description: Python、R、および C# で分離コードを使用して Azure Data Lake にジョブを送信する方法について説明します。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 11/22/2017
ms.custom: devx-track-python
ms.openlocfilehash: d6066bd6ec2a4c986ae17ad0cce3e7f6f73b21e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92219974"
---
# <a name="develop-u-sql-with-python-r-and-c-for-azure-data-lake-analytics-in-visual-studio-code"></a>Visual Studio Code で Azure Data Lake Analytics の Python、R、および C# を使用して U-SQL を開発する
Visual Studio Code (VSCode) を使用して、U-SQL で Python、R、および C# 分離コードを記述し、Azure Data Lake サービスにジョブを送信する方法について説明します。 Data Lake Tools for VSCode の詳細については、「[Azure Data Lake Tools for Visual Studio Code の使用](data-lake-analytics-data-lake-tools-for-vscode.md)」を参照してください。

分離コードのカスタム コードを記述する前に、VSCode でフォルダーまたはワークスペースを開く必要があります。


## <a name="prerequisites-for-python-and-r"></a>Python および R の前提条件
ADL アカウントで Python および R の拡張機能アセンブリを登録します。 
1. ポータルで自分のアカウントを開きます。
   - **[概要]** を選択します。 
   - **[サンプル スクリプト]** をクリックします。
2. **[その他]** をクリックします。
3. **[U-SQL Extensions をインストールする]** を選択します。 
4. U-SQL 拡張機能のインストール後に、確認メッセージが表示されます。 

   ![Python および R 用の環境の設定](./media/data-lake-analytics-data-lake-tools-for-vscode/setup-the-enrionment-for-python-and-r.png)

   > [!Note]
   > Python および R 言語サービスで最適なエクスペリエンスを実現するには、VSCode Python および R 拡張機能をインストールしてください。 

## <a name="develop-python-file"></a>Python ファイルの開発
1. ワークスペースで **[新しいファイル]** をクリックします。
2. U-SQL でコードを記述します。 サンプル コードを次に示します。
    ```U-SQL
    REFERENCE ASSEMBLY [ExtPython];
    @t  = 
        SELECT * FROM 
        (VALUES
            ("D1","T1","A1","@foo Hello World @bar"),
            ("D2","T2","A2","@baz Hello World @beer")
        ) AS 
            D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer("pythonSample.usql.py", pyVersion : "3.5.1");

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();
    ```
    
3. スクリプト ファイルを右クリックし、**[ADL: Generate Python Code Behind File]\(ADL: Python 分離コード ファイルの生成\)** を選択します。 
4. 作業フォルダーに **xxx.usql.py** ファイルが生成されます。 Python ファイルでコードを記述します。 サンプル コードを次に示します。

    ```Python
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ```
5. **USQL** ファイルを右クリックし、**[スクリプトをコンパイル]** をクリックするか、**[ジョブの送信]** をクリックしてジョブを実行します。

## <a name="develop-r-file"></a>R ファイルの開発
1. ワークスペースで **[新しいファイル]** をクリックします。
2. U-SQL ファイルでコードを記述します。 サンプル コードを次に示します。
    ```U-SQL
    DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
    DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
    DECLARE @PartitionCount int = 10;

    @InputData =
        EXTRACT SepalLength double,
                SepalWidth double,
                PetalLength double,
                PetalWidth double,
                Species string
        FROM @IrisData
        USING Extractors.Csv();

    @ExtendedData =
        SELECT Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
            SepalLength,
            SepalWidth,
            PetalLength,
            PetalWidth
        FROM @InputData;

    // Predict Species

    @RScriptOutput =
        REDUCE @ExtendedData
        ON Par
        PRODUCE Par,
                fit double,
                lwr double,
                upr double
        READONLY Par
        USING new Extension.R.Reducer(scriptFile : "RClusterRun.usql.R", rReturnType : "dataframe", stringsAsFactors : false);
    OUTPUT @RScriptOutput
    TO @OutputFilePredictions
    USING Outputters.Tsv();
    ```
3. **USQL** ファイルを右クリックし、**[ADL: Generate R Code Behind File]\(ADL: R 分離コード ファイルの生成\)** を選択します。 
4. 作業フォルダーに **xxx.usql.r** ファイルが生成されます。 R ファイルでコードを記述します。 サンプル コードを次に示します。

    ```R
    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
    ```
5. **USQL** ファイルを右クリックし、**[スクリプトをコンパイル]** をクリックするか、**[ジョブの送信]** をクリックしてジョブを実行します。

## <a name="develop-c-file"></a>C# ファイルの開発
分離コード ファイルは、1 つの U-SQL スクリプトに関連付けられている C# ファイルです。 分離コード ファイルでは、スクリプト専用の UDO、UDA、UDT、UDF を定義できます。 UDO、UDA、UDT、UDF は、アセンブリを先に登録しなくても、スクリプト内で直接使用できます。 分離コード ファイルは、対になる U-SQL スクリプト ファイルと同じフォルダーに置かれます。 スクリプトに xxx.usql という名前が付けられた場合、分離コードの名前は xxx.usql.cs となります。 分離コード ファイルを手動で削除すると、関連付けられている U-SQL スクリプトの分離コード機能が無効になります。 U-SQL スクリプト用のカスタム コードを記述する方法の詳細については、「[Writing and Using Custom Code in U-SQL – User-Defined Functions (U-SQL でのカスタム コードの記述と使用 - ユーザー定義関数)]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/)」を参照してください。

1. ワークスペースで **[新しいファイル]** をクリックします。
2. U-SQL ファイルでコードを記述します。 サンプル コードを次に示します。
    ```U-SQL
    @a = 
        EXTRACT 
            Iid int,
        Starts DateTime,
        Region string,
        Query string,
        DwellTime int,
        Results string,
        ClickedUrls string 
        FROM @"/Samples/Data/SearchLog.tsv" 
        USING Extractors.Tsv();

    @d =
        SELECT DISTINCT Region 
        FROM @a;

    @d1 = 
        PROCESS @d
        PRODUCE 
            Region string,
        Mkt string
        USING new USQLApplication_codebehind.MyProcessor();

    OUTPUT @d1 
        TO @"/output/SearchLogtest.txt" 
        USING Outputters.Tsv();
    ```
3. **USQL** ファイルを右クリックし、**[ADL: Generate CS Code Behind File]\(ADL: CS 分離コード ファイルの生成\)** を選択します。 
4. 作業フォルダーに **xxx.usql.cs** ファイルが生成されます。 CS ファイルでコードを記述します。 サンプル コードを次に示します。

    ```CS
    namespace USQLApplication_codebehind
    {
        [SqlUserDefinedProcessor]

        public class MyProcessor : IProcessor
        {
            public override IRow Process(IRow input, IUpdatableRow output)
            {
                output.Set(0, input.Get<string>(0));
                output.Set(1, input.Get<string>(0));
                return output.AsReadOnly();
            } 
        }
    }
    ```
5. **USQL** ファイルを右クリックし、**[スクリプトをコンパイル]** をクリックするか、**[ジョブの送信]** をクリックしてジョブを実行します。

## <a name="next-steps"></a>次のステップ
* [Azure Data Lake Tools for Visual Studio Code の使用](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Visual Studio Code を使用した U-SQL ローカル実行とローカル デバッグ](data-lake-tools-for-vscode-local-run-and-debug.md)
* [PowerShell で Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-powershell.md)
* [Azure ポータルで Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)
* [U-SQL アプリケーションを開発するための Data Lake Tools for Visual Studio の使用](data-lake-analytics-data-lake-tools-get-started.md)
* [Data Lake Analytics(U-SQL) カタログの使用](./data-lake-analytics-u-sql-get-started.md)