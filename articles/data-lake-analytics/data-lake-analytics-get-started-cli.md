---
title: "Azure コマンド ライン インターフェイスを使用して Azure Data Lake Analytics の使用を開始する | Microsoft Docs"
description: "Azure コマンド ライン インターフェイスを使用して Data Lake Analytics アカウントを作成し、U-SQL を使用して Data Lake Analytics ジョブを作成し、ジョブを送信する方法について説明します。 "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 651021d4-4591-4c48-b1ef-3ebc4606d66d
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 4db7d45678c592749831c6b12d38363134da9e93
ms.lasthandoff: 03/21/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-command-line-interface-cli"></a>チュートリアル: Azure コマンド ライン インターフェイス (CLI) を使用して Azure Data Lake Analytics の使用を開始する
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Azure CLI を使用して、Azure Data Lake Analytics アカウントを作成し、 [U-SQL](data-lake-analytics-u-sql-get-started.md)で Data Lake Analytics ジョブを定義し、Data Lake Analtyic アカウントにジョブを送信する方法について説明します。 Data Lake Analytics の詳細については、「 [Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)」を参照してください。

このチュートリアルでは、タブ区切り値 (TSV) ファイルを読み取り、それをコンマ区切り値 (CSV) ファイルに変換するジョブを開発します。 サポートされている他のツールを使用する同じチュートリアルを読み進めるには、このセクションの上部にあるタブをクリックします。

## <a name="prerequisites"></a>前提条件
このチュートリアルを読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure CLI**。 「 [Azure CLI のインストールと構成](../cli-install-nodejs.md)」をご覧ください。
  * このデモを完了するためには、 **プレリリース版の** [Azure CLI ツール](https://github.com/MicrosoftBigData/AzureDataLake/releases) をダウンロードしてインストールします。
* **認証**。次のコマンドを使用します。
  
        azure login
    職場か学校のアカウントを使用した認証の詳細については、「 [Azure CLI から Azure サブスクリプションへの接続する](../xplat-cli-connect.md)」をご覧ください。
* 次のコマンドを使用して、**Azure リソース マネージャー モードに切り替えます**。
  
        azure config mode arm

## <a name="create-data-lake-analytics-account"></a>Data Lake Analytics アカウントを作成する
ジョブを実行するには、Data Lake Analytics アカウントが必要です。 Data Lake Analytics アカウントを作成するには、以下を指定する必要があります。

* **Azure リソース グループ**: Data Lake Analytics アカウントは、Azure リソース グループ内に作成する必要があります。 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) を使用すると、アプリケーション内の複数リソースを&1; つのグループとして操作できます。 アプリケーションのこれらすべてのリソースを、1 回の連携した操作でデプロイ、更新、または削除できます。  
  
    サブスクリプションのリソース グループを列挙するには:
  
        azure group list 
  
    新しいリソース グループを作成するには:
  
        azure group create -n "<Resource Group Name>" -l "<Azure Location>"
* **Data Lake Analytics アカウント名**
* **場所**: Data Lake Analytics をサポートするいずれかの Azure データ センター。
* **既定の Data Lake アカウント**: 各 Data Lake Analytics アカウントには既定の Data Lake アカウントがあります。
  
    既存の Data Lake アカウントを一覧表示するには:
  
        azure datalake store account list
  
    新しい Data Lake アカウントを作成するには:
  
        azure datalake store account create "<Data Lake Store Account Name>" "<Azure Location>" "<Resource Group Name>"
  
  > [!NOTE]
  > Data Lake アカウント名には小文字と数字のみを含める必要があります。
  > 
  > 

**Data Lake Analytics アカウントを作成するには**

        azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"

        azure datalake analytics account list
        azure datalake analytics account show "<Data Lake Analytics Account Name>"            

![Data Lake Analytics でのアカウントの表示](./media/data-lake-analytics-get-started-cli/data-lake-analytics-show-account-cli.png)

> [!NOTE]
> Data Lake Analytics アカウント名には小文字と数字のみを含める必要があります。
> 
> 

## <a name="upload-data-to-data-lake-store"></a>Data Lake Store にデータをアップロードする
このチュートリアルでは、いくつかの検索ログを処理します。  検索ログは、Data Lake Store または Azure Blob Storage に格納できます。 

Azure ポータルには、検索ログ ファイルを含むサンプル データ ファイルを既定の Data Lake アカウントにコピーするためのユーザー インターフェイスが用意されています。 「 [ソース データの準備](data-lake-analytics-get-started-portal.md#prepare-source-data) 」を参照して、データを既定の Data Lake Store アカウントにアップロードします。

CLI を使用してファイルをアップロードするには、次のコマンドを使用します。

      azure datalake store filesystem import "<Data Lake Store Account Name>" "<Path>" "<Destination>"
      azure datalake store filesystem list "<Data Lake Store Account Name>" "<Path>"

Data Lake Analytics は、Azure BLOB ストレージにもアクセスできます。  Azure BLOB ストレージへのデータのアップロードについては、「 [Azure ストレージでの Azure CLI の使用](../storage/storage-azure-cli.md)」を参照してください。

## <a name="submit-data-lake-analytics-jobs"></a>Data Lake Analytics ジョブを送信する
Data Lake Analtyics ジョブは U-SQL 言語で記述されます。 U-SQL の詳細については、[U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)に関する記事および「[U-SQL Language Reference (U-SQL 言語リファレンス)](http://go.microsoft.com/fwlink/?LinkId=691348)」をご覧ください。

**Data Lake Analytics ジョブ スクリプトを作成するには**

* 次の U-SQL スクリプトでテキスト ファイルを作成し、ワークステーションにテキスト ファイルを保存します。
  
        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
  
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();
  
    この U-SQL スクリプトでは、**Extractors.Tsv()** を使用してソース データ ファイルを読み取ってから、**Outputters.Csv()** を使用して csv ファイルを作成します。 
  
    ソース ファイルを別の場所にコピーしない限り、2 つのパスを変更しないでください。  存在しない場合、Data Lake Analytics は出力フォルダーを作成します。
  
    既定の Data Lake アカウントに格納されたファイルの相対パスを使用する方が簡単です。 絶対パスを使用することもできます。  たとえば、次のように入力します。 
  
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
  
    リンクされたストレージ アカウント内のファイルへのアクセスには、絶対パスを使用する必要があります。  リンクされた Azure ストレージ アカウントに格納されているファイルの構文は以下のとおりです。
  
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv
  
  > [!NOTE]
  > パブリック BLOB またはパブリック コンテナーのアクセス許可を持つ Azure BLOB コンテナーは、現在サポートされていません。      
  > 
  > 

**ジョブを送信するには**

    azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"


次のコマンドを使用して、ジョブの一覧表示、ジョブの詳細の取得、およびジョブの取り消しを実行できます。

```
azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job Id>"
azure datalake analytics job list "<Data Lake Analytics Account Name>"
azure datalake analytics job show "<Data Lake Analytics Account Name>" "<Job Id>"
```

ジョブが完了したら、以下のコマンドレットを使用し、ファイルをリストしてダウンロードできます。

    azure datalake store filesystem list "<Data Lake Store Account Name>" "/Output"
    azure datalake store filesystem export "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" "<Destination>"
    azure datalake store filesystem read "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" <Length> <Offset>

## <a name="see-also"></a>関連項目
* 他のツールを使用する同じチュートリアルを表示するには、ページの上部にあるタブ セレクターをクリックします。
* より複雑なクエリを表示する場合は、「 [チュートリアル: Azure Data Lake Analytics を使用して Web サイトのログを分析する](data-lake-analytics-analyze-weblogs.md)」をご覧ください。
* U-SQL アプリケーションの開発を開始する場合は、「 [チュートリアル: Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)」をご覧ください。
* U-SQL の詳細については、「 [Azure Data Lake Analytics U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)」を参照してください。
* 管理タスクについては、「 [Azure ポータルを使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-portal.md)」をご覧ください。
* Data Lake Analytics の概要については、「 [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)」を参照してください。


