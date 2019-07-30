---
title: Apache Hive を使用した Twitter データの分析 - Azure HDInsight
description: Apache Hive と Apache Hadoop を HDInsight で使用して、生の Twitter データを検索可能な Hive テーブルに変換する方法を学びます。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 8c7f6695880cfdb0a350edc37d61e771d03b92df
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543716"
---
# <a name="analyze-twitter-data-using-apache-hive-and-apache-hadoop-on-hdinsight"></a>HDInsight で Apache Hive と Apache Hadoop を使用して Twitter データを分析する

[Apache Hive](https://hive.apache.org/) を使用して Twitter データを処理する方法を説明します。 結果として、特定の単語が含まれた最も多くのツイートを送信した Twitter ユーザーのリストが返されます。

> [!IMPORTANT]  
> このドキュメントの手順は、HDInsight 3.6 でテストされています。

## <a name="get-the-data"></a>データを取得する

Twitter では、REST API を使用して、JavaScript Object Notation (JSON) ドキュメントとして各ツイートのデータを取得できます。 [OAuth](https://oauth.net) が必要です。

### <a name="create-a-twitter-application"></a>Twitter アプリケーションを作成する

1. Web ブラウザーで、[https://apps.twitter.com/](https://apps.twitter.com/) にサインインします。 Twitter アカウントを持っていない場合は、 **[今すぐ登録]** リンクをクリックします。

2. **[Create New App]** をクリックします。

3. **名前**、**説明**、**Web サイト**を入力します。 **[Website]** フィールドの URL を構成することができます。 次のテーブルは使用する値のサンプルを示しています。

   | フィールド | 値 |
   |:--- |:--- |
   | Name |MyHDInsightApp |
   | 説明 |MyHDInsightApp |
   | Web サイト |https:\//www.myhdinsightapp.com |

4. **[Yes, I agree]** をオンにして、 **[Create your Twitter application]** をクリックします。

5. **[Permissions]** タブをクリックします。既定のアクセス許可は **読み取り専用**です。

6. **[Keys and Access Tokens]** タブをクリックします。

7. **[Create my access token]** をクリックします。

8. ページの右上隅にある **[Test OAuth]** をクリックします。

9. **コンシューマー キー**、**コンシューマー シークレット**、**アクセス トークン**、**アクセス トークン シークレット**を書き留めます。

### <a name="download-tweets"></a>ツイートをダウンロードする

次の Python コードは、Twitter から 10,000 個のツイートをダウンロードし、**tweets.txt** いう名前のファイルに保存します。

> [!NOTE]  
> Python が既にインストールされているので、次の手順は HDInsight クラスターで実行します。

1. SSH を使用して HDInsight クラスターに接続します。

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

3. 次のコマンドを使用して、[Tweepy](https://www.tweepy.org/)、[Progressbar](https://pypi.python.org/pypi/progressbar/2.2)、およびその他の必要なパッケージをインストールします。

   ```bash
   sudo apt install python-dev libffi-dev libssl-dev
   sudo apt remove python-openssl
   pip install virtualenv
   mkdir gettweets
   cd gettweets
   virtualenv gettweets
   source gettweets/bin/activate
   pip install tweepy progressbar pyOpenSSL requests[security]
   ```

4. 次のコマンドを使用して、**gettweets.py** という名前のファイルを作成します。

   ```bash
   nano gettweets.py
   ```

5. **gettweets.py** ファイルの内容として、次のテキストを使用します。

   ```python
   #!/usr/bin/python

   from tweepy import Stream, OAuthHandler
   from tweepy.streaming import StreamListener
   from progressbar import ProgressBar, Percentage, Bar
   import json
   import sys

   #Twitter app information
   consumer_secret='Your consumer secret'
   consumer_key='Your consumer key'
   access_token='Your access token'
   access_token_secret='Your access token secret'

   #The number of tweets we want to get
   max_tweets=10000

   #Create the listener class that receives and saves tweets
   class listener(StreamListener):
       #On init, set the counter to zero and create a progress bar
       def __init__(self, api=None):
           self.num_tweets = 0
           self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

       #When data is received, do this
       def on_data(self, data):
           #Append the tweet to the 'tweets.txt' file
           with open('tweets.txt', 'a') as tweet_file:
               tweet_file.write(data)
               #Increment the number of tweets
               self.num_tweets += 1
               #Check to see if we have hit max_tweets and exit if so
               if self.num_tweets >= max_tweets:
                   self.pbar.finish()
                   sys.exit(0)
               else:
                   #increment the progress bar
                   self.pbar.update(self.num_tweets)
           return True

       #Handle any errors that may occur
       def on_error(self, status):
           print status

   #Get the OAuth token
   auth = OAuthHandler(consumer_key, consumer_secret)
   auth.set_access_token(access_token, access_token_secret)
   #Use the listener class for stream processing
   twitterStream = Stream(auth, listener())
   #Filter for these topics
   twitterStream.filter(track=["azure","cloud","hdinsight"])
   ```

    > [!IMPORTANT]  
    > 次の項目のプレースホルダー テキストを、twitter アプリケーションからの情報に置き換えます。
    >
    > * `consumer_secret`
    > * `consumer_key`
    > * `access_token`
    > * `access_token_secret`

    > [!TIP]  
    > よく使われているキーワードを追跡するには、最後の行のトピック フィルターを調整してください。 スクリプトの実行時によく使用されているキーワードを使用すると、高速にデータをキャプチャできます。

6. **Ctrl + X** キーを押した後、**Y** キーを押してファイルを保存します。

7. 次のコマンドを使用してファイルを実行し、ツイートをダウンロードします。

    ```bash
    python gettweets.py
    ```

    進行状況のインジケーターが表示されます。 このインジケーターは、ツイートのダウンロードの進行状況を 100% になるまでカウントします。

   > [!NOTE]  
   > 進行が遅い場合は、フィルターを変更してトレンド トピックを追跡することをお勧めします。 フィルターしたトピックに関するツイートが多いほど、必要な 10000 ツイートをすばやく取得できます。

### <a name="upload-the-data"></a>データのアップロード

HDInsight のストレージにデータをアップロードするには、次のコマンドを使用します。

```bash
hdfs dfs -mkdir -p /tutorials/twitter/data
hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt
```

クラスター内のすべてのノードがアクセスできる場所にデータが保存されます。

## <a name="run-the-hiveql-job"></a>HiveQL ジョブの実行

1. 次のコマンドを使用して、[HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) ステートメントを含むファイルを作成します。

   ```bash
   nano twitter.hql
   ```

    ファイルの内容として、次のテキストを使用します。

   ```hiveql
   set hive.exec.dynamic.partition = true;
   set hive.exec.dynamic.partition.mode = nonstrict;
   -- Drop table, if it exists
   DROP TABLE tweets_raw;
   -- Create it, pointing toward the tweets logged from Twitter
   CREATE EXTERNAL TABLE tweets_raw (
       json_response STRING
   )
   STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
   -- Drop and recreate the destination table
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
   -- Select tweets from the imported data, parse the JSON,
   -- and insert into the tweets table
   FROM tweets_raw
   INSERT OVERWRITE TABLE tweets
   SELECT
       cast(get_json_object(json_response, '$.id_str') as BIGINT),
       get_json_object(json_response, '$.created_at'),
       concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
       substr (get_json_object(json_response, '$.created_at'),27,4)),
       substr (get_json_object(json_response, '$.created_at'),27,4),
       case substr (get_json_object(json_response,    '$.created_at'),5,3)
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
   ```

2. **Ctrl + X** キーを押した後、**Y** キーを押してファイルを保存します。
3. 次のコマンドを使用して、ファイルに含まれている HiveQL を実行します。

   ```bash
   beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i twitter.hql
   ```

    このコマンドは、**twitter.hql** ファイルを実行します。 クエリが完了すると、`jdbc:hive2//localhost:10001/>` プロンプトが表示されます。

4. Beeline プロンプトで次のクエリを使用して、データがインポートされたことを確認します。

   ```hiveql
   SELECT name, screen_name, count(1) as cc
   FROM tweets
   WHERE text like "%Azure%"
   GROUP BY name,screen_name
   ORDER BY cc DESC LIMIT 10;
   ```

    このクエリでは、メッセージ テキストに **Azure** という単語が含まれた最大 10 個のツイートが返されます。

    > [!NOTE]  
    > `gettweets.py` スクリプトのフィルターを変更した場合は、**Azure** を、使用したフィルターのいずれかで置き換えてください。

## <a name="next-steps"></a>次の手順

ここでは、構造化されていない JSON データ セットを構造化された [Apache Hive](https://hive.apache.org/) テーブルに変換する方法を学習しました。 HDInsight での Hive の詳細については、次のドキュメントを参照してください。

* [HDInsight の概要](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [HDInsight を使用したフライト遅延データの分析](/azure/hdinsight/interactive-query/interactive-query-tutorial-analyze-flight-data)

[curl]: https://curl.haxx.se
[curl-download]: https://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter
