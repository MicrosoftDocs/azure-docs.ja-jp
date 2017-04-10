---
title: "HDInsight の Apache Hive を使用した Twitter データの分析 | Microsoft Docs"
description: "Python を使用して特定のキーワードを含むツイートを格納してから、HDInsight で Hive と Hadoop を使用して未加工の Twitter データを検索可能な Hive テーブルに変換する方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: e1e249ed-5f57-40d6-b3bc-a1b4d9a871d3
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
translationtype: Human Translation
ms.sourcegitcommit: d391c5c6289aa63e969f63f189eb5db680883f0a
ms.openlocfilehash: db0f94bdeefac577765586f6b07ba13f9cfd2867
ms.lasthandoff: 03/01/2017

---
# <a name="analyze-twitter-data-using-hive-on-linux-based-hdinsight"></a>Linux ベースの HDInsight 上の Hive を使用した Twitter データの分析

HDInsight クラスターで Apache Hive を使用して Twitter データを処理する方法について説明します。 結果として、特定の単語が含まれた最も多くのツイートを送信した Twitter ユーザーのリストが返されます。

> [!IMPORTANT]
> このドキュメントの手順は、Linux ベースの HDInsight クラスターでテストされています。
>
> Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

* **Linux ベースの Azure HDInsight クラスター**。 クラスターの作成方法については、「 [Hadoop チュートリアル: Linux 上の HDInsight で Hive と Hadoop を使用する (プレビュー)](hdinsight-hadoop-linux-tutorial-get-started.md) 」をご覧ください。
* **SSH クライアント**。 Linux ベースの HDInsight での SSH の使用方法の詳細については、次の記事をご覧ください。
  
  * [Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)
  * [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)
* **Python** と [pip](https://pypi.python.org/pypi/pip)

## <a name="get-a-twitter-feed"></a>Twitter Feed の取得

Twitter では、REST API を使用して、JavaScript Object Notation (JSON) ドキュメントとして [各ツイートのデータ](https://dev.twitter.com/docs/platform-objects/tweets) を取得できます。 [OAuth](http://oauth.net) が必要です。

### <a name="create-a-twitter-application"></a>Twitter アプリケーションを作成する

1. Web ブラウザーから [https://apps.twitter.com/](https://apps.twitter.com/)にサインインします。 Twitter アカウントを持っていない場合は、**[今すぐ登録]** リンクをクリックします。

2. **[Create New App]**をクリックします。

3. **名前**、**説明**、**Web サイト**を入力します。 **[Website]** フィールドの URL を構成することができます。 次のテーブルは使用する値のサンプルを示しています。
   
   | フィールド | 値 |
   |:--- |:--- |
   | 名前 |MyHDInsightApp |
   | Description |MyHDInsightApp |
   | Web サイト |http://www.myhdinsightapp.com |

4. **[Yes, I agree]** をオンにして、**[Create your Twitter application]** をクリックします。

5. **[Permissions]** タブをクリックします。 既定のアクセス許可は **読み取り専用**です。

6. **[Keys and Access Tokens]** タブをクリックします。

7. **[Create my access token]**をクリックします。

8. ページの右上隅にある **[Test OAuth]** をクリックします。

9. **コンシューマー キー**、**コンシューマー シークレット**、**アクセス トークン**、**アクセス トークン シークレット**を書き留めます。

> [!NOTE]
> Windows で curl コマンドを使用する場合、オプション値には一重引用符の代わりに二重引用符を使用します。


### <a name="download-tweets"></a>ツイートをダウンロードする

次の Python コードは、Twitter から 10,000 個のツイートをダウンロードし、**tweets.txt** いう名前のファイルに保存します。

> [!NOTE]
> Python が既にインストールされているので、次の手順は HDInsight クラスターで実行します。
> 
> 

1. SSH を使用して HDInsight クラスターに接続します。
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    SSH ユーザー アカウントを保護するためにパスワードを使用している場合は、パスワードの入力を求められます。 公開キーを使用している場合、 `-i` パラメーターを使用して、対応する秘密キーを指定することが必要な場合があります。 たとえば、「 `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`」のように入力します。
   
    Linux ベースの HDInsight での SSH の使用方法の詳細については、次の記事を参照してください。
   
   * [Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)
   * [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)

2. 既定では、 **pip** ユーティリティは HDInsight ヘッド ノードにインストールされません。 次のコマンドを使用して、このユーティリティをインストールし、更新します。
   
   ```bash
   sudo apt-get install python-pip
   sudo pip install --upgrade pip
   ```

3. 次のコマンドを使用して [Tweepy](http://www.tweepy.org/) と [Progressbar](https://pypi.python.org/pypi/progressbar/2.2) をインストールします。
   
   ```bash
   sudo apt-get install python-dev libffi-dev libssl-dev
   sudo apt-get remove python-openssl
   sudo pip install tweepy progressbar pyOpenSSL requests[security]
   ```

   > [!NOTE]
   > python-openssl の削除、python-dev、libffi-dev、libssl-dev、および pyOpenSSL のインストール、requests[security] に関する注意事項は、Python から SSL 経由で Twitter に接続するときに InsecurePlatform 警告を回避することです。
   > 
   > ツイートを処理するときに発生する可能性のある [エラー](https://github.com/tweepy/tweepy/issues/576) を回避するために、Tweepy v3.2.0 が使用されます。

4. 次のコマンドを使用して、**gettweets.py** という名前のファイルを作成します。
   
   ```bash
   nano gettweets.py
   ```

5. **gettweets.py** ファイルの内容として、次のテキストを使用します。 **consumer\_secret****consumer\_key****access/\_token****access\_token\_secret** のプレースホルダー情報を、Twitter アプリケーションの情報に置き換えます。
   
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

6. **Ctrl + X** キーを押した後、**Y** キーを押してファイルを保存します。

7. 次のコマンドを使用してファイルを実行し、ツイートをダウンロードします。
   
    ```bash
    python gettweets.py
    ```
   
    進行状況のインジケーターが表示され、ツイートのダウンロードとファイルへの保存の進行状況が 100% までカウントされます。
   
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

1. 次のコマンドを使用して、HiveQL ステートメントを含むファイルを作成します。
   
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
   beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i twitter.hql
   ```

    このコマンドは、**twitter.hql** ファイルを実行します。 クエリが完了すると、`jdbc:hive2//localhost:10001/>` プロンプトが表示されます。

4. Beeline プロンプトで、次のステートメントを使用して、**twitter.hql** ファイル内の HiveQL によって作成された **tweets** テーブルからデータを選択できることを確認します。
   
   ```hiveql
   SELECT name, screen_name, count(1) as cc
       FROM tweets
       WHERE text like "%Azure%"
       GROUP BY name,screen_name
       ORDER BY cc DESC LIMIT 10;
   ```

    このクエリでは、メッセージ テキストに **Azure** という単語が含まれた最大 10 個のツイートが返されます。

## <a name="next-steps"></a>次のステップ

ここでは、構造化されていない JSON データ セットを構造化された Hive テーブルに変換する方法を学習しました。 HDInsight での Hive の詳細については、次のドキュメントを参照してください。

* [HDInsight の概要](hdinsight-hadoop-linux-tutorial-get-started.md)
* [HDInsight を使用したフライト遅延データの分析](hdinsight-analyze-flight-delay-data-linux.md)

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

