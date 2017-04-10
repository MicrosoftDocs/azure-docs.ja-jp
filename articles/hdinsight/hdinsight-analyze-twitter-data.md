---
title: "HDInsight の Hadoop を使用した Twitter データの分析 | Microsoft Docs"
description: "HDInsight の Hadoop に格納されている Twitter データを Hive で分析し、特定の単語の使用頻度を調べる方法について説明します。"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 78e4ea33-9714-424d-ac07-3d60ecaebf2e
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jgao
ROBOTS: NOINDEX
translationtype: Human Translation
ms.sourcegitcommit: 6407c371bc51461a05429fabaf38d3f9bc80d32c
ms.openlocfilehash: a90c412d2d66834cd0df3f348fa488b6ce10c898
ms.lasthandoff: 02/07/2017


---

# <a name="analyze-twitter-data-using-hive-in-hdinsight"></a>HDInsight での Hive を使用した Twitter データの分析
ビッグ データの多くはソーシャル Website からもたらされます。 Twitter などのサイトが公開している API を介して収集したデータは、現在の動向を分析して把握するための有益な情報源となります。
このチュートリアルでは、Twitter streaming API を使用して複数のツイートを取得します。さらに、Azure HDInsight の Apache Hive を使用して、特定の単語を含むツイートを多く送信した Twitter ユーザーの一覧を取得します。

> [!IMPORTANT]
> このドキュメントの手順では、Windows ベースの HDInsight クラスターが必要です。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。 Linux ベースのクラスターに固有の手順については、「[HDInsight での Hive を使用した Twitter データの分析 (Linux)](hdinsight-analyze-twitter-data-linux.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
このチュートリアルを読み始める前に、次の項目を用意する必要があります。

* **コンピューター**。Azure PowerShell がインストールされ構成されている必要があります。

    Windows PowerShell スクリプトを実行するには、Azure PowerShell を管理者として実行し、実行ポリシーを *RemoteSigned*に設定する必要があります。 [Windows PowerShell スクリプトの実行][powershell-script]に関するページをご覧ください。

    Windows PowerShell スクリプトを実行する前に、次のコマンドレットを使用して Azure サブスクリプションに接続されていることを確認します。

    ```powershell
    Login-AzureRmAccount
    ```

    Azure サブスクリプションが複数ある場合は、次のコマンドレットを使用して、現在のサブスクリプションを設定します。

    ```powershell
    Select-AzureRmSubscription -SubscriptionID <Azure Subscription ID>
    ```

    > [!IMPORTANT]
    > Azure サービス マネージャーを使用した HDInsight リソースの管理に関する Azure PowerShell のサポートは、2017 年 1 月 1 日までに**廃止**される予定です。 このドキュメントの手順では、Azure Resource Manager で機能する新しい HDInsight コマンドレットを使用します。
    >
    > [Azure PowerShell のインストールと構成](/powershell/azureps-cmdlets-docs) に関するページの手順に従い、Azure PowerShell の最新バージョンをインストールしてください。 Azure Resource Manager で機能する新しいコマンドレットを使用するようにスクリプトを変更する必要がある場合、詳細については、「 [Migrating to Azure Resource Manager-based development tools for HDInsight clusters (HDInsight クラスターの Azure Resource Manager ベースの開発ツールへの移行)](hdinsight-hadoop-development-using-azure-resource-manager.md) 」をご覧ください。

* **Azure HDInsight クラスター**。 クラスターのプロビジョニングの手順については、[Azure HDInsight の使用開始][hdinsight-get-started]に関するページまたは [HDInsight クラスターのプロビジョニング][hdinsight-provision]に関するページをご覧ください。 このチュートリアルでは、クラスター名は後で必要になります。

このチュートリアルで使用するファイルを次の表に示します。

| ファイル | Description |
| --- | --- |
| /tutorials/twitter/data/tweets.txt |Hive ジョブのソース データです。 |
| /tutorials/twitter/output |Hive ジョブの出力フォルダーです。 既定の Hive ジョブ出力ファイル名は **000000_0** です。 |
| tutorials/twitter/twitter.hql |HiveQL スクリプト ファイルです。 |
| /tutorials/twitter/jobstatus |Hadoop ジョブの状態です。 |

## <a name="get-twitter-feed"></a>Twitter Feed の取得
このチュートリアルでは、[Twitter streaming API][twitter-streaming-api] を使用します。 使用する特定の Twitter streaming API は [statuses/filter][twitter-statuses-filter] です。

> [!NOTE]
> 10,000 のツイートを含むファイルと Hive スクリプト ファイルは (次のセクションで説明) は、パブリック BLOB コンテナーにアップロードされています。 このセクションは、アップロードしたファイルを使用する場合は省略できます。

[ツイート データ](https://dev.twitter.com/docs/platform-objects/tweets) は、複雑なネスト構造の JavaScript Object Notation (JSON) 形式で格納されます。 従来のプログラミング言語を使用して多数のコード行を記述する代わりに、このネスト構造を Hive テーブルに変換し、構造化照会言語 (SQL) によく似た HiveQL という言語で照会するようにできます。

Twitter は OAuth を使用して、API への承認されたアクセスを提供します。 OAuth は、パスワードを共有せずに代理でアプリケーションが動作することをユーザーが承認できるようにする認証プロトコルです。 詳細については、[oauth.net](http://oauth.net/)、または Hueniverse の便利な「[Beginner's Guide to OAuth (OAuth 初心者向けガイド)](http://hueniverse.com/oauth/)」で確認できます。

OAuth を使用するための最初の手順は、Twitter 開発者サイトで新しいアプリケーションを作成することです。

**Twitter アプリケーションを作成するには**

1. [https://apps.twitter.com/](https://apps.twitter.com/)にサインインします。 Twitter アカウントを持っていない場合は、 **[今すぐ登録]** リンクをクリックします。
2. **[Create New App]**をクリックします。
3. **名前**、**説明**、**Web サイト**を入力します。 **[Website]** フィールドの URL を構成することができます。 次のテーブルは使用する値のサンプルを示しています。

   | フィールド | 値 |
   | --- | --- |
   |  名前 |MyHDInsightApp |
   |  Description |MyHDInsightApp |
   |  Web サイト |http://www.myhdinsightapp.com |
4. **[Yes, I agree]** をオンにして、**[Create your Twitter application]** をクリックします。
5. **[Permissions]** タブをクリックします。 既定のアクセス許可は **読み取り専用**です。 このチュートリアルにはこれで十分です。
6. **[Keys and Access Tokens]** タブをクリックします。
7. **[Create my access token]**をクリックします。
8. ページの右上隅にある **[Test OAuth]** をクリックします。
9. **コンシューマー キー**、**コンシューマー シークレット**、**アクセス トークン**、**アクセス トークン シークレット**を書き留めます。 これらの値は後で必要になります。

このチュートリアルでは、Windows PowerShell を使用して Web サービスを呼び出します。 .NET の c# サンプルについては、「[HDInsight 環境の HBase で Twitter のセンチメントをリアルタイム分析する][hdinsight-hbase-twitter-sentiment]」を参照してください。 Web サービスを呼び出すその他の一般的なツールは [*Curl*][curl] です。 Curl は[こちら][curl-download]からダウンロードできます。

> [!NOTE]
> Windows で curl コマンドを使用する場合、オプション値には一重引用符の代わりに二重引用符を使用します。

**ツイートを取得するには**

1. Windows PowerShell Integrated Scripting Environment (ISE) を開きます  (Windows 8 のスタート画面では、「**PowerShell_ISE**」と入力してから、**[Windows PowerShell ISE]** をクリックします。 「[Start Windows PowerShell on Windows 8 and Windows (Windows 8 と Windows での Windows PowerShell の起動)][powershell-start]」を参照してください)。
2. 次のスクリプトをスクリプト ウィンドウにコピーします。

    ```powershell
    #region - variables and constants
    $clusterName = "<HDInsightClusterName>" # Enter the HDInsight cluster name

    # Enter the OAuth information for your Twitter application
    $oauth_consumer_key = "<TwitterAppConsumerKey>";
    $oauth_consumer_secret = "<TwitterAppConsumerSecret>";
    $oauth_token = "<TwitterAppAccessToken>";
    $oauth_token_secret = "<TwitterAppAccessTokenSecret>";

    $destBlobName = "tutorials/twitter/data/tweets.txt" # This script saves the tweets into this blob.

    $trackString = "Azure, Cloud, HDInsight" # This script gets the tweets containing these keywords.
    $track = [System.Uri]::EscapeDataString($trackString);
    $lineMax = 10000  # The script will get this number of tweets. It is about 3 minutes every 100 lines.
    #endregion

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    Login-AzureRmAccount
    #endregion

    #region - Create a block blob object for writing tweets into Blob storage
    Write-Host "Get the default storage account name and Blob container name using the cluster name ..." -ForegroundColor Green
    $myCluster = Get-AzureRmHDInsightCluster -Name $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $storageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $containerName = $myCluster.DefaultStorageContainer
    Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
    Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow

    Write-Host "Define the Azure storage connection string ..." -ForegroundColor Green
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
    $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
    Write-Host "`tThe connection string is $storageConnectionString." -ForegroundColor Yellow

    Write-Host "Create block blob object ..." -ForegroundColor Green
    $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
    $storageClient = $storageAccount.CreateCloudBlobClient();
    $storageContainer = $storageClient.GetContainerReference($containerName)
    $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
    #end region

    # region - Format OAuth strings
    Write-Host "Format oauth strings ..." -ForegroundColor Green
    $oauth_nonce = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes([System.DateTime]::Now.Ticks.ToString()));
    $ts = [System.DateTime]::UtcNow - [System.DateTime]::ParseExact("01/01/1970", "dd/MM/yyyy", $null)
    $oauth_timestamp = [System.Convert]::ToInt64($ts.TotalSeconds).ToString();

    $signature = "POST&";
    $signature += [System.Uri]::EscapeDataString("https://stream.twitter.com/1.1/statuses/filter.json") + "&";
    $signature += [System.Uri]::EscapeDataString("oauth_consumer_key=" + $oauth_consumer_key + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_nonce=" + $oauth_nonce + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_signature_method=HMAC-SHA1&");
    $signature += [System.Uri]::EscapeDataString("oauth_timestamp=" + $oauth_timestamp + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_token=" + $oauth_token + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_version=1.0&");
    $signature += [System.Uri]::EscapeDataString("track=" + $track);

    $signature_key = [System.Uri]::EscapeDataString($oauth_consumer_secret) + "&" + [System.Uri]::EscapeDataString($oauth_token_secret);

    $hmacsha1 = new-object System.Security.Cryptography.HMACSHA1;
    $hmacsha1.Key = [System.Text.Encoding]::ASCII.GetBytes($signature_key);
    $oauth_signature = [System.Convert]::ToBase64String($hmacsha1.ComputeHash([System.Text.Encoding]::ASCII.GetBytes($signature)));

    $oauth_authorization = 'OAuth ';
    $oauth_authorization += 'oauth_consumer_key="' + [System.Uri]::EscapeDataString($oauth_consumer_key) + '",';
    $oauth_authorization += 'oauth_nonce="' + [System.Uri]::EscapeDataString($oauth_nonce) + '",';
    $oauth_authorization += 'oauth_signature="' + [System.Uri]::EscapeDataString($oauth_signature) + '",';
    $oauth_authorization += 'oauth_signature_method="HMAC-SHA1",'
    $oauth_authorization += 'oauth_timestamp="' + [System.Uri]::EscapeDataString($oauth_timestamp) + '",'
    $oauth_authorization += 'oauth_token="' + [System.Uri]::EscapeDataString($oauth_token) + '",';
    $oauth_authorization += 'oauth_version="1.0"';

    $post_body = [System.Text.Encoding]::ASCII.GetBytes("track=" + $track);
    #endregion

    #region - Read tweets
    Write-Host "Create HTTP web request ..." -ForegroundColor Green
    [System.Net.HttpWebRequest] $request = [System.Net.WebRequest]::Create("https://stream.twitter.com/1.1/statuses/filter.json");
    $request.Method = "POST";
    $request.Headers.Add("Authorization", $oauth_authorization);
    $request.ContentType = "application/x-www-form-urlencoded";
    $body = $request.GetRequestStream();

    $body.write($post_body, 0, $post_body.length);
    $body.flush();
    $body.close();
    $response = $request.GetResponse() ;

    Write-Host "Start stream reading ..." -ForegroundColor Green

    Write-Host "Define a MemoryStream and a StreamWriter for writing ..." -ForegroundColor Green
    $memStream = New-Object System.IO.MemoryStream
    $writeStream = New-Object System.IO.StreamWriter $memStream

    $sReader = New-Object System.IO.StreamReader($response.GetResponseStream())

    $inrec = $sReader.ReadLine()
    $count = 0
    while (($inrec -ne $null) -and ($count -le $lineMax))
    {
        if ($inrec -ne "")
        {
            Write-Host "`n`t $count tweets received." -ForegroundColor Yellow

            $writeStream.WriteLine($inrec)
            $count ++
        }

        $inrec=$sReader.ReadLine()
    }
    #endregion

    #region - Write tweets to Blob storage
    Write-Host "Write to the destination blob ..." -ForegroundColor Green
    $writeStream.Flush()
    $memStream.Seek(0, "Begin")
    $destBlob.UploadFromStream($memStream)

    $sReader.close()
    #endregion

    Write-Host "Completed!" -ForegroundColor Green
    ```

3. スクリプトに、最初の&5; ～&8; 個の変数を設定します。

    変数|Description
    ---|---
    $clusterName|アプリケーションを実行する HDInsight クラスターの名前です。
    $oauth_consumer_key|Twitter アプリケーションを作成したときに書き留めた Twitter アプリケーションの **コンシューマー キー** です。
    $oauth_consumer_secret|前に書き留めた Twitter アプリケーションの **コンシューマー シークレット** です。
    $oauth_token|前に書き留めた Twitter アプリケーションの **アクセス トークン** です。
    $oauth_token_secret|前に書き留めた Twitter アプリケーションの **アクセス トークン シークレット** です。
    $destBlobName|出力 BLOB 名です。 既定値は、 **tutorials/twitter/data/tweets.txt**です。 既定値を変更する場合は、適宜 Windows PowerShell スクリプトを更新する必要があります。
    $trackString|Web サービスはこれらのキーワードに関連するツイートを返します。 既定値は、 **Azure、クラウド、HDInsight**です。 既定値を変更する場合は、適宜 Windows PowerShell スクリプトを更新します。
    $lineMax|この値によってスクリプトが読み取るツイートの数が決まります。 100 個のツイートを読み取るに約 3 分かかります。 大きな数値を設定してもかまいませんが、ダウンロードに時間がかかります。

1. **F5** キーを押して、スクリプトを実行します。 問題が発生した場合は、回避策としてすべての行を選択し、 **F8**キーを押します。
2. 出力の最後に "Complete!"  と表示されます。 エラー メッセージが赤色で表示されます。

検証手順として、Azure ストレージ エクスプローラーまたは Azure PowerShell を使用して Azure BLOB ストレージ上で出力ファイル **/tutorials/twitter/data/tweets.txt**を確認できます。 一覧表示するファイルのサンプル Windows PowerShell スクリプトについては、[HDInsight での Blob Storage の使用][hdinsight-storage-powershell]に関するページをご覧ください。

## <a name="create-hiveql-script"></a>HiveQL スクリプトの作成
Azure PowerShell を使用して、複数の HiveQL ステートメントを一度に実行することも、HiveQL ステートメントをスクリプト ファイルにまとめることもできます。 このチュートリアルでは、HiveQL スクリプトを作成します。 スクリプト ファイルは、Azure Blob ストレージにアップロードする必要があります。 次のセクションでは、Azure PowerShell を使用してスクリプト ファイルを実行します。

> [!NOTE]
> Hive スクリプト ファイルと 10,000 のツイートが含まれているファイルは、パブリック BLOB コンテナーにアップロードされています。 このセクションは、アップロードしたファイルを使用する場合は省略できます。

HiveQL スクリプトは、次の作業を実行します。

1. **tweets_raw テーブルを削除します** (テーブルが既に存在する場合)。
2. **tweets_raw Hive テーブルを作成します**。 この一時的な Hive 構造テーブルには、さらに抽出、変換、および読み込み (ETL) 処理のデータが保持されます。 パーティションの詳細については、「[Hive tutorial (Hive のチュートリアル)][apache-hive-tutorial]」を参照してください。
3. **データの読み込み。** ソース フォルダー (/tutorials/twitter/data) からデータを読み込みます。 JSON ネスト形式の大規模なツイート データセットが、一時的な Hive テーブル構造に変換されました。
4. **ツイート テーブルを削除します** (テーブルが既に存在する場合)。
5. **ツイート テーブルを作成します**。 Hive を使用してツイート データセットを照会するには、別の ETL 処理を実行する必要があります。 この ETL 処理は、"twitter_raw" テーブルに保存したデータのための詳細なテーブル スキーマを定義します。
6. **上書きテーブルを挿入します**。 この複雑な Hive スクリプトは、Hadoop クラスターによる一連の長い MapReduce ジョブを開始します。 使用するデータセットおよびクラスターのサイズによっては、10 分ほどかかる場合があります。
7. **上書きディレクトリを挿入します**。 クエリを実行し、データセットをファイルに出力します。 このクエリは、"Azure" という単語が含まれるツイートを送信した Twitter ユーザーの一覧を返します。

**Hive スクリプトを作成して Azure にアップロードするには**

1. Windows PowerShell ISE を開きます。
2. 次のスクリプトをスクリプト ウィンドウにコピーします。

    ```powershell
    #region - variables and constants
    $clusterName = "<Existing HDInsight Cluster Name>" # Enter your HDInsight cluster name
    $subscriptionID = "<Azure Subscription ID>"

    $sourceDataPath = "/tutorials/twitter/data"
    $outputPath = "/tutorials/twitter/output"
    $hqlScriptFile = "tutorials/twitter/twitter.hql"

    $hqlStatements = @"
    set hive.exec.dynamic.partition = true;
    set hive.exec.dynamic.partition.mode = nonstrict;

    DROP TABLE tweets_raw;
    CREATE EXTERNAL TABLE tweets_raw (
        json_response STRING
    )
    STORED AS TEXTFILE LOCATION '$sourceDataPath';

    DROP TABLE tweets;
    CREATE TABLE tweets
    (
        id BIGINT,
        created_at STRING,
        created_at_date STRING,
        created_at_year STRING,
        created_at_month STRING,
        created_at_day STRING,
        created_at_time STRING,
        in_reply_to_user_id_str STRING,
        text STRING,
        contributors STRING,
        retweeted STRING,
        truncated STRING,
        coordinates STRING,
        source STRING,
        retweet_count INT,
        url STRING,
        hashtags array<STRING>,
        user_mentions array<STRING>,
        first_hashtag STRING,
        first_user_mention STRING,
        screen_name STRING,
        name STRING,
        followers_count INT,
        listed_count INT,
        friends_count INT,
        lang STRING,
        user_location STRING,
        time_zone STRING,
        profile_image_url STRING,
        json_response STRING
    );

    FROM tweets_raw
    INSERT OVERWRITE TABLE tweets
    SELECT
        cast(get_json_object(json_response, '$.id_str') as BIGINT),
        get_json_object(json_response, '$.created_at'),
        concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
        substr (get_json_object(json_response, '$.created_at'),27,4)),
        substr (get_json_object(json_response, '$.created_at'),27,4),
        case substr (get_json_object(json_response, '$.created_at'),5,3)
            when "Jan" then "01"
            when "Feb" then "02"
            when "Mar" then "03"
            when "Apr" then "04"
            when "May" then "05"
            when "Jun" then "06"
            when "Jul" then "07"
            when "Aug" then "08"
            when "Sep" then "09"
            when "Oct" then "10"
            when "Nov" then "11"
            when "Dec" then "12" end,
        substr (get_json_object(json_response, '$.created_at'),9,2),
        substr (get_json_object(json_response, '$.created_at'),12,8),
        get_json_object(json_response, '$.in_reply_to_user_id_str'),
        get_json_object(json_response, '$.text'),
        get_json_object(json_response, '$.contributors'),
        get_json_object(json_response, '$.retweeted'),
        get_json_object(json_response, '$.truncated'),
        get_json_object(json_response, '$.coordinates'),
        get_json_object(json_response, '$.source'),
        cast (get_json_object(json_response, '$.retweet_count') as INT),
        get_json_object(json_response, '$.entities.display_url'),
        array(
            trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
        array(
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
        trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
        trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
        get_json_object(json_response, '$.user.screen_name'),
        get_json_object(json_response, '$.user.name'),
        cast (get_json_object(json_response, '$.user.followers_count') as INT),
        cast (get_json_object(json_response, '$.user.listed_count') as INT),
        cast (get_json_object(json_response, '$.user.friends_count') as INT),
        get_json_object(json_response, '$.user.lang'),
        get_json_object(json_response, '$.user.location'),
        get_json_object(json_response, '$.user.time_zone'),
        get_json_object(json_response, '$.user.profile_image_url'),
        json_response
    WHERE (length(json_response) > 500);

    INSERT OVERWRITE DIRECTORY '$outputPath'
    SELECT name, screen_name, count(1) as cc
        FROM tweets
        WHERE text like "%Azure%"
        GROUP BY name,screen_name
        ORDER BY cc DESC LIMIT 10;
    "@
    #endregion

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green

    Try{
        Get-AzureRmSubscription
    }
    Catch{
        Login-AzureRmAccount
    }

    Select-AzureRmSubscription -SubscriptionId $subscriptionID

    #endregion

    #region - Create a block blob object for writing the Hive script file
    Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
    $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $defaultBlobContainerName = $myCluster.DefaultStorageContainer
    Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
    Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow

    Write-Host "Define the connection string ..." -ForegroundColor Green
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"

    Write-Host "Create block blob objects referencing the hql script file" -ForegroundColor Green
    $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
    $storageClient = $storageAccount.CreateCloudBlobClient();
    $storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
    $hqlScriptBlob = $storageContainer.GetBlockBlobReference($hqlScriptFile)

    Write-Host "Define a MemoryStream and a StreamWriter for writing ... " -ForegroundColor Green
    $memStream = New-Object System.IO.MemoryStream
    $writeStream = New-Object System.IO.StreamWriter $memStream
    $writeStream.Writeline($hqlStatements)
    #endregion

    #region - Write the Hive script file to Blob storage
    Write-Host "Write to the destination blob ... " -ForegroundColor Green
    $writeStream.Flush()
    $memStream.Seek(0, "Begin")
    $hqlScriptBlob.UploadFromStream($memStream)
    #endregion

    Write-Host "Completed!" -ForegroundColor Green
    ```

3. スクリプトの最初の&2; 個の変数を設定します。

   | 変数 | Description |
   | --- | --- |
   |  $clusterName |アプリケーションを実行する HDInsight クラスター名を入力します。 |
   |  $subscriptionID |Azure サブスクリプション ID を入力します。 |
   |  $sourceDataPath |Hive クエリがデータを読み取る Azure Blob ストレージの場所です。 この変数を変更する必要はありません。 |
   |  $outputPath |Hive クエリが結果を出力する Azure Blob ストレージの場所です。 この変数を変更する必要はありません。 |
   |  $hqlScriptFile |HiveQL スクリプト ファイルの場所とファイル名です。 この変数を変更する必要はありません。 |
4. **F5** キーを押して、スクリプトを実行します。 問題が発生した場合は、回避策としてすべての行を選択し、 **F8**キーを押します。
5. 出力の最後に "Complete!"  と表示されます。 エラー メッセージが赤色で表示されます。

検証手順として、Azure ストレージ エクスプローラーまたは Azure PowerShell を使用して Azure BLOB ストレージ上で出力ファイル **/tutorials/twitter/twitter.hql**を確認できます。 一覧表示するファイルのサンプル Windows PowerShell スクリプトについては、[HDInsight での Blob Storage の使用][hdinsight-storage-powershell]に関するページをご覧ください。

## <a name="process-twitter-data-by-using-hive"></a>Hive を使用して Twitter データを処理する
すべての準備作業が完了しました。 Hive スクリプトを呼び出して、結果を確認できます。

### <a name="submit-a-hive-job"></a>Hive ジョブを送信する
次の Windows PowerShell スクリプトを使用して Hive スクリプトを実行します。 最初の変数を設定する必要があります。

> [!NOTE]
> 最後の&2; つのセクションでアップロードしたツイートと HiveQL スクリプトを使用するには、$hqlScriptFile を "/tutorials/twitter/twitter.hql" に設定します。 パブリック BLOB にアップロードされているものを使用するには、$hqlScriptFile を "wasbs://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql" に設定します。

```powershell
#region variables and constants
$clusterName = "<Existing Azure HDInsight Cluster Name>"
$httpUserName = "admin"
$httpUserPassword = "<HDInsight Cluster HTTP User Password>"

#use one of the following
$hqlScriptFile = "wasbs://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql"
$hqlScriptFile = "/tutorials/twitter/twitter.hql"

$statusFolder = "/tutorials/twitter/jobstatus"
#endregion

$myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $myCluster.ResourceGroup
$defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value

$defaultBlobContainerName = $myCluster.DefaultStorageContainer

#region - Invoke Hive
Write-Host "Invoke Hive ... " -ForegroundColor Green

# Create the HDInsight cluster
$pw = ConvertTo-SecureString -String $httpUserPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

Use-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $httpCredential
$response = Invoke-AzureRmHDInsightHiveJob -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -file $hqlScriptFile -StatusFolder $statusFolder #-OutVariable $outVariable

Write-Host "Display the standard error log ... " -ForegroundColor Green
$jobID = ($response | Select-String job_ | Select-Object -First 1) -replace ‘\s*$’ -replace ‘.*\s’
Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $jobID -DefaultContainer $defaultBlobContainerName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -HttpCredential $httpCredential
#endregion
```

### <a name="check-the-results"></a>結果を確認する
次の Windows PowerShell スクリプトを使用して Hive ジョブ出力を確認します。 最初の&2; つの変数を設定する必要があります。

```powershell
#region variables and constants
$clusterName = "<Existing Azure HDInsight Cluster Name>"

$blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.
#endregion

#region - Create an Azure storage context object
Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
$myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $myCluster.ResourceGroup
$defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
$defaultBlobContainerName = $myCluster.DefaultStorageContainer

Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow

Write-Host "Create a context object ... " -ForegroundColor Green
$storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
#endregion

#region - Download blob and display blob
Write-Host "Download the blob ..." -ForegroundColor Green
cd $HOME
Get-AzureStorageBlobContent -Container $defaultBlobContainerName -Blob $blob -Context $storageContext -Force

Write-Host "Display the output ..." -ForegroundColor Green
Write-Host "==================================" -ForegroundColor Green
cat "./$blob"
Write-Host "==================================" -ForegroundColor Green
#end region
```

> [!NOTE]
> Hive テーブルでは \001 をフィールド区切り記号として使用します。 区切り記号は出力には表示されません。

分析結果が Azure BLOB ストレージに配置されると、Azure SQL Database/SQL Server へのデータのエクスポート、Power Query を使用してのデータの Excel へのエクスポート、または Hive ODBC ドライバーを使用してのアプリケーションのデータへの接続ができます。 詳細については、[HDInsight での Sqoop の使用][hdinsight-use-sqoop]に関するページ、[HDInsight を使用したフライト遅延データの分析][hdinsight-analyze-flight-delay-data]に関するページ、「[Power Query を使用した Excel から HDInsight への接続][hdinsight-power-query]」、および「[Microsoft Hive ODBC ドライバーを使用した Excel から HDInsight への接続][hdinsight-hive-odbc]」を参照してください。

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure 上で HDInsight を使用し、Twitter から収集したデータを照会、探索、分析するため、構造化されていない JSON データセットを構造化された Hive テーブルへ変換する方法を学習しました。 詳細については、次を参照してください。

* [HDInsight の概要][hdinsight-get-started]
* [HDInsight 環境の HBase で Twitter のセンチメントをリアルタイム分析する][hdinsight-hbase-twitter-sentiment]
* [HDInsight を使用したフライト遅延データの分析][hdinsight-analyze-flight-delay-data]
* [Power Query を使用した Excel から HDInsight への接続][hdinsight-power-query]
* [Microsoft Hive ODBC ドライバーを使用した Excel から HDInsight への接続][hdinsight-hive-odbc]
* [HDInsight での Sqoop の使用][hdinsight-use-sqoop]

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176961.aspx

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]:hdinsight-hadoop-use-blob-storage.md#access-blobs-using-azure-powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-odbc-driver.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

