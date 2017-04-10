---
title: "HBase で Twitter にリアルタイムの感情分析を実行する | Microsoft Docs"
description: "HDInsight (Hadoop) クラスターで HBase を使用し、Twitter からリアルタイムでビックデータのセンチメントを分析する方法について取り上げます。"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 5c798ad3-a20d-4385-a463-f4f7705f9566
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 5fa91c0fb1858a46745ff50991b843530f0a5d23
ms.lasthandoff: 03/21/2017


---
# <a name="analyze-real-time-twitter-sentiment-with-hbase-in-hdinsight"></a>HDInsight 環境の HBase で Twitter のセンチメントをリアルタイム分析する
HDInsight で HBase クラスターを使用して、Twitter のビッグ データをリアルタイムで[感情分析](http://en.wikipedia.org/wiki/Sentiment_analysis)する方法について取り上げます。

ビッグ データの多くはソーシャル Web サイトからもたらされます。 Twitter などのサイトが公開している API を介して収集したデータは、現在の動向を分析して把握するための有益な情報源となります。 このチュートリアルでは、以下の事柄を実行するためのコンソール ストリーミング サービス アプリケーションと ASP.NET Web アプリケーションを開発します。

![HDInsight HBase での Twitter センチメントの分析][img-app-arch]

* ストリーミングのアプリケーション

  * Twitter streaming API を使用して、ジオタグ付けされたツイートをリアルタイムで取得します。
  * そうしたツイートのセンチメントを評価します。
  * センチメント情報を、Microsoft HBase SDK を使用して HBase に格納します。
* Azure Web サイト アプリケーション

  * リアルタイム統計結果を、ASP.NET Web アプリケーションを使用して Bing マップにプロットします。 ツイートを視覚化すると、次のようになります。

    ![hdinsight.hbase.twitter.sentiment.bing.map][img-bing-map]

    特定のキーワードを使用してツイートを照会し、ツイートで表明された意見の感情が肯定的、否定的、中立のどれであるかを取得できます。

Visual Studio ソリューションの完全なサンプルは、GitHub: [Realtime social sentiment analysis app](https://github.com/maxluk/tweet-sentiment)に示されています。

### <a name="prerequisites"></a>前提条件
このチュートリアルを読み始める前に、次の項目を用意する必要があります。

* **HDInsight 環境の HBase クラスター**。 クラスターの作成については、「[HDInsight の Hadoop 環境での HBase の使用][hbase-get-started]」を参照してください。 このチュートリアルを読み進めるには、次のデータが必要です。

    <table border="1">
    <tr><th>クラスター プロパティ</th><th>説明</th></tr>
    <tr><td>HBase クラスター名</td><td>使用する HDInsight HBase クラスター名です。 例: https://myhbase.azurehdinsight.net/</td></tr>
    <tr><td>クラスター ユーザー名</td><td>Hadoop ユーザー アカウント名。 既定の Hadoop ユーザー名は <strong>admin</strong> です。</td></tr>
    <tr><td>クラスター ユーザー パスワード</td><td>Hadoop クラスター ユーザーのパスワード。</td></tr>
    </table>

* Visual Studio 2013/2015/2017 がインストールされている**ワークステーション**。 手順については、 [Visual Studio のインストール](http://msdn.microsoft.com/library/e2h7fzkw.aspx)を参照してください。

## <a name="create-a-twitter-application-id-and-secrets"></a>Twitter アプリケーション ID とシークレットを作成する
Twitter Streaming API は [OAuth](http://oauth.net/) を使用して要求を承認します。 OAuth を使用するための最初の手順は、Twitter 開発者サイトで新しいアプリケーションを作成することです。

**Twitter アプリケーション ID とシークレットを作成するには**

1. [Twitter アプリ](https://apps.twitter.com/)にサインインします Twitter アカウントを持っていない場合は、 **[今すぐ登録]** リンクをクリックします。
2. **[Create New App]**をクリックします。
3. **名前**、**説明**、**Web サイト**を入力します。 Twitter アプリケーションの名前は一意の名前にする必要があります。 [Website] フィールドは実際には使用しません。 有効な URL である必要はありません。
4. **[Yes, I agree]** をオンにして、**[Create your Twitter application]** をクリックします。
5. **[Permissions]** タブをクリックします。 既定のアクセス許可は **読み取り専用**です。 このチュートリアルにはこれで十分です。
6. **[Keys and Access Tokens]** タブをクリックします。
7. **[Create my access token]**をクリックします。
8. ページの右上隅にある **[Test OAuth]** をクリックします。
9. **コンシューマー キー**、**コンシューマー シークレット**、**アクセス トークン**、**アクセス トークン シークレット**の値をコピーします。 これらの値は後で必要になります。

    ![hdi.hbase.twitter.sentiment.twitter.app][img-twitter-app]

## <a name="create-twitter-streaming-service"></a>Twitter ストリーミング サービスを作成する
ツイートを取得し、ツイート センチメント スコアを計算し、処理したツイート ワードを HBase に送信するアプリケーションを作成する必要があります。

**ストリーミング アプリケーションを作成するには**

1. **Visual Studio** を開き、**TweetSentimentStreaming** という名前の Visual C# コンソール アプリケーションを作成します。
2. **パッケージ マネージャー コンソール**で、次のコマンドを実行します。

        Install-Package Microsoft.HBase.Client -version 0.4.2.0
        Install-Package TweetinviAPI -version 1.0.0.0

    これらのコマンドによって、HBase クラスターにアクセスするクライアント ライブラリの [HBase .NET SDK](https://www.nuget.org/packages/Microsoft.HBase.Client/) パッケージと、Twitter API へのアクセスに使用する [Tweetinvi API](https://www.nuget.org/packages/TweetinviAPI/) パッケージがインストールされます。

   > [!NOTE]
   > この記事で使用しているサンプルは、上記で指定したバージョンを使用してテストされています。  -version スイッチを削除することで最新のバージョンをインストールできます。
   >
   >
3. **ソリューション エクスプローラー**で、参照に **System.Configuration** を追加します。
4. **HBaseWriter.cs**という名前の新しいクラス ファイルをプロジェクトに追加し、そのコードを次のコードに置き換えます。

        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Text;
        using System.Threading;
        using Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
        using org.apache.hadoop.hbase.rest.protobuf.generated;
        using Microsoft.HBase.Client;
        using Tweetinvi.Models;

        namespace TweetSentimentStreaming
        {
            class HBaseWriter
            {
                // HDinsight HBase cluster and HBase table information
                const string CLUSTERNAME = "https://<Enter Your Cluster Name>.azurehdinsight.net/";
                const string HADOOPUSERNAME = "admin"; //the default name is "admin"
                const string HADOOPUSERPASSWORD = "<Enter the Hadoop User Password>";

                const string HBASETABLENAME = "tweets_by_words";
                const string COUNT_ROW_KEY = "~ROWCOUNT";
                const string COUNT_COLUMN_NAME = "d:COUNT";

                long rowCount = 0;

                // Sentiment dictionary file and the punctuation characters
                const string DICTIONARYFILENAME = @"..\..\dictionary.tsv";
                private static char[] _punctuationChars = new[] {
            ' ', '!', '\"', '#', '$', '%', '&', '\'', '(', ')', '*', '+', ',', '-', '.', '/',   //ascii 23--47
            ':', ';', '<', '=', '>', '?', '@', '[', ']', '^', '_', '`', '{', '|', '}', '~' };   //ascii 58--64 + misc.

                // For writting to HBase
                HBaseClient client;

                // a sentiment dictionary for estimate sentiment. It is loaded from a physical file.
                Dictionary<string, DictionaryItem> dictionary;

                // use multithread write
                Thread writerThread;
                Queue<ITweet> queue = new Queue<ITweet>();
                bool threadRunning = true;

                // This function connects to HBase, loads the sentiment dictionary, and starts the thread for writting.
                public HBaseWriter()
                {
                    ClusterCredentials credentials = new ClusterCredentials(new Uri(CLUSTERNAME), HADOOPUSERNAME, HADOOPUSERPASSWORD);
                    client = new HBaseClient(credentials);

                    // create the HBase table if it doesn't exist
                    if (!client.ListTablesAsync().Result.name.Contains(HBASETABLENAME))
                    {
                        TableSchema tableSchema = new TableSchema();
                        tableSchema.name = HBASETABLENAME;
                        tableSchema.columns.Add(new ColumnSchema { name = "d" });
                        client.CreateTableAsync(tableSchema).Wait();
                        Console.WriteLine("Table \"{0}\" is created.", HBASETABLENAME);
                    }

                    // Read current row count cell
                    rowCount = GetRowCount();

                    // Load sentiment dictionary from a file
                    LoadDictionary();

                    // Start a thread for writting to HBase
                    writerThread = new Thread(new ThreadStart(WriterThreadFunction));
                    writerThread.Start();
                }

                ~HBaseWriter()
                {
                    threadRunning = false;
                }

                private long GetRowCount()
                {
                    try
                    {
                        RequestOptions options = RequestOptions.GetDefaultOptions();
                        options.RetryPolicy = RetryPolicy.NoRetry;
                        var cellSet = client.GetCellsAsync(HBASETABLENAME, COUNT_ROW_KEY, null, null, options).Result;
                        if (cellSet.rows.Count != 0)
                        {
                            var countCol = cellSet.rows[0].values.Find(cell => Encoding.UTF8.GetString(cell.column) == COUNT_COLUMN_NAME);
                            if (countCol != null)
                            {
                                return Convert.ToInt64(Encoding.UTF8.GetString(countCol.data));
                            }
                        }
                    }
                    catch(Exception ex)
                    {
                        if (ex.InnerException.Message.Equals("The remote server returned an error: (404) Not Found.", StringComparison.OrdinalIgnoreCase))
                        {
                            return 0;
                        }
                        else
                        {
                            throw ex;
                        }

                    }

                    return 0;
                }

                // Enqueue the Tweets received
                public void WriteTweet(ITweet tweet)
                {
                    lock (queue)
                    {
                        queue.Enqueue(tweet);
                    }
                }

                // Load sentiment dictionary from a file
                private void LoadDictionary()
                {
                    List<string> lines = File.ReadAllLines(DICTIONARYFILENAME).ToList();
                    var items = lines.Select(line =>
                    {
                        var fields = line.Split('\t');
                        var pos = 0;
                        return new DictionaryItem
                        {
                            Type = fields[pos++],
                            Length = Convert.ToInt32(fields[pos++]),
                            Word = fields[pos++],
                            Pos = fields[pos++],
                            Stemmed = fields[pos++],
                            Polarity = fields[pos++]
                        };
                    });

                    dictionary = new Dictionary<string, DictionaryItem>();
                    foreach (var item in items)
                    {
                        if (!dictionary.Keys.Contains(item.Word))
                        {
                            dictionary.Add(item.Word, item);
                        }
                    }
                }

                // Calculate sentiment score
                private int CalcSentimentScore(string[] words)
                {
                    Int32 total = 0;
                    foreach (string word in words)
                    {
                        if (dictionary.Keys.Contains(word))
                        {
                            switch (dictionary[word].Polarity)
                            {
                                case "negative": total -= 1; break;
                                case "positive": total += 1; break;
                            }
                        }
                    }
                    if (total > 0)
                    {
                        return 1;
                    }
                    else if (total < 0)
                    {
                        return -1;
                    }
                    else
                    {
                        return 0;
                    }
                }

                // Popular a CellSet object to be written into HBase
                private void CreateTweetByWordsCells(CellSet set, ITweet tweet)
                {
                    // Split the Tweet into words
                    string[] words = tweet.Text.ToLower().Split(_punctuationChars);

                    // Calculate sentiment score base on the words
                    int sentimentScore = CalcSentimentScore(words);
                    var word_pairs = words.Take(words.Length - 1)
                                        .Select((word, idx) => string.Format("{0} {1}", word, words[idx + 1]));
                    var all_words = words.Concat(word_pairs).ToList();

                    // For each word in the Tweet add a row to the HBase table
                    foreach (string word in all_words)
                    {
                        string time_index = (ulong.MaxValue - (ulong)tweet.CreatedAt.ToBinary()).ToString().PadLeft(20) + tweet.IdStr;
                        string key = word + "_" + time_index;

                        // Create a row
                        var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(key) };

                        // Add columns to the row, including Tweet identifier, language, coordinator(if available), and sentiment
                        var value = new Cell { column = Encoding.UTF8.GetBytes("d:id_str"), data = Encoding.UTF8.GetBytes(tweet.IdStr) };
                        row.values.Add(value);

                        value = new Cell { column = Encoding.UTF8.GetBytes("d:lang"), data = Encoding.UTF8.GetBytes(tweet.Language.ToString()) };
                        row.values.Add(value);

                        if (tweet.Coordinates != null)
                        {
                            var str = tweet.Coordinates.Longitude.ToString() + "," + tweet.Coordinates.Latitude.ToString();
                            value = new Cell { column = Encoding.UTF8.GetBytes("d:coor"), data = Encoding.UTF8.GetBytes(str) };
                            row.values.Add(value);
                        }

                        value = new Cell { column = Encoding.UTF8.GetBytes("d:sentiment"), data = Encoding.UTF8.GetBytes(sentimentScore.ToString()) };
                        row.values.Add(value);

                        set.rows.Add(row);
                    }
                }

                // Write a Tweet (CellSet) to HBase
                public void WriterThreadFunction()
                {
                    try
                    {
                        while (threadRunning)
                        {
                            if (queue.Count > 0)
                            {
                                CellSet set = new CellSet();
                                lock (queue)
                                {
                                    do
                                    {
                                        ITweet tweet = queue.Dequeue();
                                        CreateTweetByWordsCells(set, tweet);
                                    } while (queue.Count > 0);
                                }

                                // Write the Tweet by words cell set to the HBase table
                                client.StoreCellsAsync(HBASETABLENAME, set).Wait();
                                Console.WriteLine("\tRows written: {0}", set.rows.Count);
                            }
                            Thread.Sleep(100);
                        }
                    }
                    catch (Exception ex)
                    {
                        Console.WriteLine("Exception: " + ex.Message);
                    }
                }
            }
            public class DictionaryItem
            {
                public string Type { get; set; }
                public int Length { get; set; }
                public string Word { get; set; }
                public string Pos { get; set; }
                public string Stemmed { get; set; }
                public string Polarity { get; set; }
            }
        }
5. 前述のコードに含まれる、**CLUSTERNAME**、**HADOOPUSERNAME**、**HADOOPUSERPASSWORD**、DICTIONARYFILENAME などの定数を設定します。 DICTIONARYFILENAME は、direction.tsv のファイル名と場所です。  ファイルは **https://hditutorialdata.blob.core.windows.net/twittersentiment/dictionary.tsv** からダウンロードできます。 HBase テーブル名を変更する場合には、それに応じて Web アプリケーション内のテーブル名も変更しなければなりません。
6. **Program.cs**を開き、そのコードを次のコードに置き換えます。

        using System;
        using System.Diagnostics;
        using Tweetinvi;
        using Tweetinvi.Models;

        namespace TweetSentimentStreaming
        {
            class Program
            {
                const string TWITTERAPPACCESSTOKEN = "<Enter Twitter App Access Token>";
                const string TWITTERAPPACCESSTOKENSECRET = "<Enter Twitter Access Token Secret>";
                const string TWITTERAPPAPIKEY = "<Enter Twitter App API Key>";
                const string TWITTERAPPAPISECRET = "<Enter Twitter App API Secret>";

                static void Main(string[] args)
                {
                    Auth.SetUserCredentials(TWITTERAPPAPIKEY, TWITTERAPPAPISECRET, TWITTERAPPACCESSTOKEN, TWITTERAPPACCESSTOKENSECRET);

                    Stream_FilteredStreamExample();
                }

                private static void Stream_FilteredStreamExample()
                {
                    for (;;)
                    {
                        try
                        {
                            HBaseWriter hbase = new HBaseWriter();
                            var stream = Stream.CreateFilteredStream();
                            stream.AddLocation(new Coordinates(-180, -90), new Coordinates(180, 90));

                            var tweetCount = 0;
                            var timer = Stopwatch.StartNew();

                            stream.MatchingTweetReceived += (sender, args) =>
                            {
                                tweetCount++;
                                var tweet = args.Tweet;

                                // Write Tweets to HBase
                                hbase.WriteTweet(tweet);

                                if (timer.ElapsedMilliseconds > 1000)
                                {
                                    if (tweet.Coordinates != null)
                                    {
                                        Console.ForegroundColor = ConsoleColor.Green;
                                        Console.WriteLine("\n{0}: {1} {2}", tweet.Id, tweet.Language.ToString(), tweet.Text);
                                        Console.ForegroundColor = ConsoleColor.White;
                                        Console.WriteLine("\tLocation: {0}, {1}", tweet.Coordinates.Longitude, tweet.Coordinates.Latitude);
                                    }

                                    timer.Restart();
                                    Console.WriteLine("\tTweets/sec: {0}", tweetCount);
                                    tweetCount = 0;
                                }
                            };

                            stream.StartStreamMatchingAllConditions();
                        }
                        catch (Exception ex)
                        {
                            Console.WriteLine("Exception: {0}", ex.Message);
                        }
                    }
                }

            }
        }
7. **TWITTERAPPACCESSTOKEN**、**TWITTERAPPACCESSTOKENSECRET**、**TWITTERAPPAPIKEY**、**TWITTERAPPAPISECRET** などの定数を設定します。

ストリーミング サービスを実行するには、 **F5**キーを押します。 コンソール アプリケーションのスクリーンショットは次のようになります。

![hdinsight.hbase.twitter.sentiment.streaming.service][img-streaming-service]

Web アプリケーションの作成中はストリーミング コンソール アプリケーションを実行したままにし、さらに多くのデータを使用できるようにしてください。 テーブルに挿入されたデータを確認するために、HBase シェルを使用することができます。 [HDInsight での HBase の使用](hdinsight-hbase-tutorial-get-started-linux.md#create-tables-and-insert-data)に関するページをご覧ください。

## <a name="visualize-real-time-sentiment"></a>リアルタイムのセンチメントを視覚化する
このセクションでは、リアルタイムのセンチメント データを HBase から読み取り、データを Bing マップにプロットする ASP.NET MVC Web アプリケーションを作成します。

**ASP.NET MVC Web アプリケーションを作成するには**

1. Visual Studio を開きます。
2. **[ファイル]**、**[新規]**、**[プロジェクト]** の順にクリックします。
3. 次の情報を入力します。

   * テンプレート カテゴリ: **Visual C#/Web**
   * テンプレート: **ASP.NET Web アプリケーション**
   * 名前: **TweetSentimentWeb**
   * 場所: **C:\Tutorials**
4. **[OK]**をクリックします。
5. **[テンプレートの選択]** で **[MVC]** をクリックします。
6. **[Microsoft Azure]** で **[サブスクリプションの管理]** をクリックします。
7. **[Microsoft Azure サブスクリプションの管理]** で **[サインイン]** をクリックします。
8. Azure の資格情報を入力します。 **[アカウント]** タブに、Azure サブスクリプションの情報が表示されます。
9. **[閉じる]** をクリックし、**[Microsoft Azure サブスクリプションの管理]** ウィンドウを閉じます。
10. **[新規 ASP.NET プロジェクト - TweetSentimentWeb]** で **[OK]** をクリックします。
11. **[Microsoft Azure サイト設定の構成]** で、お近くの **[リージョン]** を選択します。 データベース サーバーを指定する必要はありません。
12. **[OK]**をクリックします。

**Nuget パッケージをインストールするには**

1. **[ツール]** メニューで **[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。 コンソール パネルがページ下部で開きます。
2. 次のコマンドを使用して、HBase クラスターにアクセスするクライアント ライブラリの [HBase .NET SDK](https://www.nuget.org/packages/Microsoft.HBase.Client/) パッケージをインストールします。

        Install-Package Microsoft.HBase.Client

**HBaseReader クラスを追加するには**

1. **ソリューション エクスプローラー**で、**[TweetSentiment]** を展開します。
2. **[モデル]** を右クリックして、**[追加]**、**[クラス]** の順にクリックします。
3. **[名前]** フィールドに「**HBaseReader.cs**」と入力して、**[追加]** をクリックします。
4. コードを次のコードに置き換えます。

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;

        using System.Configuration;
        using System.Threading.Tasks;
        using System.Text;
        using Microsoft.HBase.Client;
        using org.apache.hadoop.hbase.rest.protobuf.generated;

        namespace TweetSentimentWeb.Models
        {
            public class HBaseReader
            {
                // For reading Tweet sentiment data from HDInsight HBase
                HBaseClient client;

                // HDinsight HBase cluster and HBase table information
                const string CLUSTERNAME = "<HBaseClusterName>";
                const string HADOOPUSERNAME = "<HBaseClusterHadoopUserName>"
                const string HADOOPUSERPASSWORD = "<HBaseCluserUserPassword>";
                const string HBASETABLENAME = "tweets_by_words";

                // The constructor
                public HBaseReader()
                {
                    ClusterCredentials creds = new ClusterCredentials(
                                    new Uri(CLUSTERNAME),
                                    HADOOPUSERNAME,
                                    HADOOPUSERPASSWORD);
                    client = new HBaseClient(creds);
                }

                // Query Tweets sentiment data from the HBase table asynchronously
                public async Task<IEnumerable<Tweet>> QueryTweetsByKeywordAsync(string keyword)
                {
                    List<Tweet> list = new List<Tweet>();

                    // Demonstrate Filtering the data from the past 6 hours the row key
                    string timeIndex = (ulong.MaxValue -
                        (ulong)DateTime.UtcNow.Subtract(new TimeSpan(6, 0, 0)).ToBinary()).ToString().PadLeft(20);
                    string startRow = keyword + "_" + timeIndex;
                    string endRow = keyword + "|";
                    Scanner scanSettings = new Scanner
                    {
                        batch = 100000,
                        startRow = Encoding.UTF8.GetBytes(startRow),
                        endRow = Encoding.UTF8.GetBytes(endRow)
                    };

                    // Make async scan call
                    ScannerInformation scannerInfo =
                        await client.CreateScannerAsync(HBASETABLENAME, scanSettings);

                    CellSet next;

                    while ((next = await client.ScannerGetNextAsync(scannerInfo)) != null)
                    {
                        foreach (CellSet.Row row in next.rows)
                        {
                            // find the cell with string pattern "d:coor"
                            var coordinates =
                                row.values.Find(c => Encoding.UTF8.GetString(c.column) == "d:coor");

                            if (coordinates != null)
                            {
                                string[] lonlat = Encoding.UTF8.GetString(coordinates.data).Split(',');

                                var sentimentField =
                                    row.values.Find(c => Encoding.UTF8.GetString(c.column) == "d:sentiment");
                                Int32 sentiment = 0;
                                if (sentimentField != null)
                                {
                                    sentiment = Convert.ToInt32(Encoding.UTF8.GetString(sentimentField.data));
                                }

                                list.Add(new Tweet
                                {
                                    Longtitude = Convert.ToDouble(lonlat[0]),
                                    Latitude = Convert.ToDouble(lonlat[1]),
                                    Sentiment = sentiment
                                });
                            }

                            if (coordinates != null)
                            {
                                string[] lonlat = Encoding.UTF8.GetString(coordinates.data).Split(',');
                            }
                        }
                    }

                    return list;
                }
            }

            public class Tweet
            {
                public string IdStr { get; set; }
                public string Text { get; set; }
                public string Lang { get; set; }
                public double Longtitude { get; set; }
                public double Latitude { get; set; }
                public int Sentiment { get; set; }
            }
        }
5. **HBaseReader** クラス内で、次の定数値を変更します。

   * **CLUSTERNAME**: *https://<HBaseClusterName>.azurehdinsight.net/* などの HBase cluster 名。
   * **HADOOPUSERNAME**: HBase クラスター Hadoop ユーザーのユーザー名。 既定の名前は *admin*です。
   * **HADOOPUSERPASSWORD**: HBase クラスター Hadoop ユーザーのパスワード。
   * **HBASETABLENAME** = "tweets_by_words";

     HBase テーブル名は **"tweets_by_words";** です。 値は、ストリーミング サービスで送信した値と同じでなければなりません。そのようにすると、Web アプリケーションは同じ HBase テーブルのデータを読み取ることができます。

**TweetsController コントローラーを追加するには**

1. **ソリューション エクスプローラー**で **[TweetSentimentWeb]** を展開します。
2. **[コントローラー]** を右クリックし、**[追加]**、**[コントローラー]** の順にクリックします。
3. **[Web API 2 コントローラー - 空]** をクリックしてから **[追加]** をクリックします。
4. **[コントローラー名]** フィールドに「**TweetsController**」と入力し、**[追加]** をクリックします。
5. **ソリューション エクスプローラー**で TweetsController.cs をダブルクリックしてファイルを開きます。
6. ファイルを以下のように変更します。

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Net;
        using System.Net.Http;
        using System.Web.Http;

        using System.Threading.Tasks;
        using TweetSentimentWeb.Models;

        namespace TweetSentimentWeb.Controllers
        {
            public class TweetsController : ApiController
            {
                HBaseReader hbase = new HBaseReader();

                public async Task<IEnumerable<Tweet>> GetTweetsByQuery(string query)
                {
                    return await hbase.QueryTweetsByKeywordAsync(query);
                }
            }
        }

**heatmap.js を追加するには**

1. **ソリューション エクスプローラー**で **[TweetSentimentWeb]** を展開します。
2. **[スクリプト]** を右クリックし、**[追加]**、**[JavaScript ファイル]** の順にクリックします。
3. **[項目名]** フィールドに「**heatmap.js**」と入力します。
4. 以下のコードをファイルに貼り付けます。 このコードの作成者は Alastair Aitchison です。 詳細については、「 [Bing Maps AJAX v7 HeatMap Library (Bing Maps AJAX v7 HeatMap ライブラリ)](http://alastaira.wordpress.com/2011/04/15/bing-maps-ajax-v7-heatmap-library/)」をご覧ください。

        /*******************************************************************************
        * Author: Alastair Aitchison
        * Website: http://alastaira.wordpress.com
        * Date: 15th April 2011
        *
        * Description:
        * This JavaScript file provides an algorithm that can be used to add a heatmap
        * overlay on a Bing Maps v7 control. The intensity and temperature palette
        * of the heatmap are designed to be easily customisable.
        *
        * Requirements:
        * The heatmap layer itself is created dynamically on the client-side using
        * the HTML5 &lt;canvas> element, and therefore requires a browser that supports
        * this element. It has been tested on IE9, Firefox 3.6/4 and
        * Chrome 10 browsers. If you can confirm whether it works on other browsers or
        * not, I'd love to hear from you!
        *
        * Usage:
        * The HeatMapLayer constructor requires:
        * - A reference to a map object
        * - An array or Microsoft.Maps.Location items
        * - Optional parameters to customise the appearance of the layer
        *  (Radius,, Unit, Intensity, and ColourGradient), and a callback function
        */

        var HeatMapLayer = function (map, locations, options) {

            /* Private Properties */
            var _map = map,
                _canvas,
                _temperaturemap,
                _locations = [],
                _viewchangestarthandler,
                _viewchangeendhandler;

            // Set default options
            var _options = {
                // Opacity at the centre of each heat point
                intensity: 0.5,

                // Affected radius of each heat point
                radius: 1000,

                // Whether the radius is an absolute pixel value or meters
                unit: 'meters',

                // Colour temperature gradient of the map
                colourgradient: {
                    "0.00": 'rgba(255,0,255,20)',  // Magenta
                    "0.25": 'rgba(0,0,255,40)',    // Blue
                    "0.50": 'rgba(0,255,0,80)',    // Green
                    "0.75": 'rgba(255,255,0,120)', // Yellow
                    "1.00": 'rgba(255,0,0,150)'    // Red
                },

                // Callback function to be fired after heatmap layer has been redrawn
                callback: null
            };

            /* Private Methods */
            function _init() {
                var _mapDiv = _map.getRootElement();

                if (_mapDiv.childNodes.length >= 3 && _mapDiv.childNodes[2].childNodes.length >= 2) {
                    // Create the canvas element
                    _canvas = document.createElement('canvas');
                    _canvas.style.position = 'relative';

                    var container = document.createElement('div');
                    container.style.position = 'absolute';
                    container.style.left = '0px';
                    container.style.top = '0px';
                    container.appendChild(_canvas);

                    _mapDiv.childNodes[2].childNodes[1].appendChild(container);

                    // Override defaults with any options passed in the constructor
                    _setOptions(options);

                    // Load array of location data
                    _setPoints(locations);

                    // Create a colour gradient from the suppied colourstops
                    _temperaturemap = _createColourGradient(_options.colourgradient);

                    // Wire up the event handler to redraw heatmap canvas
                    _viewchangestarthandler = Microsoft.Maps.Events.addHandler(_map, 'viewchangestart', _clearHeatMap);
                    _viewchangeendhandler = Microsoft.Maps.Events.addHandler(_map, 'viewchangeend', _createHeatMap);

                    _createHeatMap();

                    delete _init;
                } else {
                    setTimeout(_init, 100);
                }
            }

            // Resets the heat map
            function _clearHeatMap() {
                var ctx = _canvas.getContext("2d");
                ctx.clearRect(0, 0, _canvas.width, _canvas.height);
            }

            // Creates a colour gradient from supplied colour stops on initialisation
            function _createColourGradient(colourstops) {
                var ctx = document.createElement('canvas').getContext('2d');
                var grd = ctx.createLinearGradient(0, 0, 256, 0);
                for (var c in colourstops) {
                    grd.addColorStop(c, colourstops[c]);
                }
                ctx.fillStyle = grd;
                ctx.fillRect(0, 0, 256, 1);
                return ctx.getImageData(0, 0, 256, 1).data;
            }

            // Applies a colour gradient to the intensity map
            function _colouriseHeatMap() {
                var ctx = _canvas.getContext("2d");
                var dat = ctx.getImageData(0, 0, _canvas.width, _canvas.height);
                var pix = dat.data; // pix is a CanvasPixelArray containing height x width x 4 bytes of data (RGBA)
                for (var p = 0, len = pix.length; p < len;) {
                    var a = pix[p + 3] * 4; // get the alpha of this pixel
                    if (a != 0) { // If there is any data to plot
                        pix[p] = _temperaturemap[a]; // set the red value of the gradient that corresponds to this alpha
                        pix[p + 1] = _temperaturemap[a + 1]; //set the green value based on alpha
                        pix[p + 2] = _temperaturemap[a + 2]; //set the blue value based on alpha
                    }
                    p += 4; // Move on to the next pixel
                }
                ctx.putImageData(dat, 0, 0);
            }

            // Sets any options passed in
            function _setOptions(options) {
                for (attrname in options) {
                    _options[attrname] = options[attrname];
                }
            }

            // Sets the heatmap points from an array of Microsoft.Maps.Locations  
            function _setPoints(locations) {
                _locations = locations;
            }

            // Main method to draw the heatmap
            function _createHeatMap() {
                // Ensure the canvas matches the current dimensions of the map
                // This also has the effect of resetting the canvas
                _canvas.height = _map.getHeight();
                _canvas.width = _map.getWidth();

                _canvas.style.top = -_canvas.height / 2 + 'px';
                _canvas.style.left = -_canvas.width / 2 + 'px';

                // Calculate the pixel radius of each heatpoint at the current map zoom
                if (_options.unit == "pixels") {
                    radiusInPixel = _options.radius;
                } else {
                    radiusInPixel = _options.radius / _map.getMetersPerPixel();
                }

                var ctx = _canvas.getContext("2d");

                // Convert lat/long to pixel location
                var pixlocs = _map.tryLocationToPixel(_locations, Microsoft.Maps.PixelReference.control);
                var shadow = 'rgba(0, 0, 0, ' + _options.intensity + ')';
                var mapWidth = 256 * Math.pow(2, _map.getZoom());

                // Create the Intensity Map by looping through each location
                for (var i = 0, len = pixlocs.length; i < len; i++) {
                    var x = pixlocs[i].x;
                    var y = pixlocs[i].y;

                    if (x < 0) {
                        x += mapWidth * Math.ceil(Math.abs(x / mapWidth));
                    }

                    // Create radial gradient centred on this point
                    var grd = ctx.createRadialGradient(x, y, 0, x, y, radiusInPixel);
                    grd.addColorStop(0.0, shadow);
                    grd.addColorStop(1.0, 'transparent');

                    // Draw the heatpoint onto the canvas
                    ctx.fillStyle = grd;
                    ctx.fillRect(x - radiusInPixel, y - radiusInPixel, 2 * radiusInPixel, 2 * radiusInPixel);
                }

                // Apply the specified colour gradient to the intensity map
                _colouriseHeatMap();

                // Call the callback function, if specified
                if (_options.callback) {
                    _options.callback();
                }
            }

            /* Public Methods */

            this.Show = function () {
                if (_canvas) {
                    _canvas.style.display = '';
                }
            };

            this.Hide = function () {
                if (_canvas) {
                    _canvas.style.display = 'none';
                }
            };

            // Sets options for intensity, radius, colourgradient etc.
            this.SetOptions = function (options) {
                _setOptions(options);
            }

            // Sets an array of Microsoft.Maps.Locations from which the heatmap is created
            this.SetPoints = function (locations) {
                // Reset the existing heatmap layer
                _clearHeatMap();
                // Pass in the new set of locations
                _setPoints(locations);
                // Recreate the layer
                _createHeatMap();
            }

            // Removes the heatmap layer from the DOM
            this.Remove = function () {
                _canvas.parentNode.parentNode.removeChild(_canvas.parentNode);

                if (_viewchangestarthandler) { Microsoft.Maps.Events.removeHandler(_viewchangestarthandler); }
                if (_viewchangeendhandler) { Microsoft.Maps.Events.removeHandler(_viewchangeendhandler); }

                _locations = null;
                _temperaturemap = null;
                _canvas = null;
                _options = null;
                _viewchangestarthandler = null;
                _viewchangeendhandler = null;
            }

            // Call the initialisation routine
            _init();
        };

        // Call the Module Loaded method
        Microsoft.Maps.moduleLoaded('HeatMapModule');

**TwitterStream.js を追加するには**

1. **ソリューション エクスプローラー**で **[TweetSentimentWeb]** を展開します。
2. **[スクリプト]** を右クリックし、**[追加]**、**[JavaScript ファイル]** の順にクリックします。
3. **[項目名]** フィールドに、「**twitterStream.js**」と入力します。
4. 以下のコードをコピーして、ファイルに貼り付けます。

        var liveTweetsPos = [];
        var liveTweets = [];
        var liveTweetsNeg = [];
        var map;
        var heatmap;
        var heatmapNeg;
        var heatmapPos;

        function initialize() {
            // Initialize the map
            var options = {
                credentials: "AvFJTZPZv8l3gF8VC3Y7BPBd0r7LKo8dqKG02EAlqg9WAi0M7la6zSIT-HwkMQbx",
                center: new Microsoft.Maps.Location(23.0, 8.0),
                mapTypeId: Microsoft.Maps.MapTypeId.ordnanceSurvey,
                labelOverlay: Microsoft.Maps.LabelOverlay.hidden,
                zoom: 2.5
            };
            var map = new Microsoft.Maps.Map(document.getElementById('map_canvas'), options);

            // Heatmap options for positive, neutral and negative layers

            var heatmapOptions = {
                // Opacity at the centre of each heat point
                intensity: 0.5,

                // Affected radius of each heat point
                radius: 15,

                // Whether the radius is an absolute pixel value or meters
                unit: 'pixels'
            };

            var heatmapPosOptions = {
                // Opacity at the centre of each heat point
                intensity: 0.5,

                // Affected radius of each heat point
                radius: 15,

                // Whether the radius is an absolute pixel value or meters
                unit: 'pixels',

                colourgradient: {
                    0.0: 'rgba(0, 255, 255, 0)',
                    0.1: 'rgba(0, 255, 255, 1)',
                    0.2: 'rgba(0, 255, 191, 1)',
                    0.3: 'rgba(0, 255, 127, 1)',
                    0.4: 'rgba(0, 255, 63, 1)',
                    0.5: 'rgba(0, 127, 0, 1)',
                    0.7: 'rgba(0, 159, 0, 1)',
                    0.8: 'rgba(0, 191, 0, 1)',
                    0.9: 'rgba(0, 223, 0, 1)',
                    1.0: 'rgba(0, 255, 0, 1)'
                }
            };

            var heatmapNegOptions = {
                // Opacity at the centre of each heat point
                intensity: 0.5,

                // Affected radius of each heat point
                radius: 15,

                // Whether the radius is an absolute pixel value or meters
                unit: 'pixels',

                colourgradient: {
                    0.0: 'rgba(0, 255, 255, 0)',
                    0.1: 'rgba(0, 255, 255, 1)',
                    0.2: 'rgba(0, 191, 255, 1)',
                    0.3: 'rgba(0, 127, 255, 1)',
                    0.4: 'rgba(0, 63, 255, 1)',
                    0.5: 'rgba(0, 0, 127, 1)',
                    0.7: 'rgba(0, 0, 159, 1)',
                    0.8: 'rgba(0, 0, 191, 1)',
                    0.9: 'rgba(0, 0, 223, 1)',
                    1.0: 'rgba(0, 0, 255, 1)'
                }
            };

            // Register and load the Client Side HeatMap Module
            Microsoft.Maps.registerModule("HeatMapModule", "scripts/heatmap.js");
            Microsoft.Maps.loadModule("HeatMapModule", {
                callback: function () {
                    // Create heatmap layers for positive, neutral and negative tweets
                    heatmapPos = new HeatMapLayer(map, liveTweetsPos, heatmapPosOptions);
                    heatmap = new HeatMapLayer(map, liveTweets, heatmapOptions);
                    heatmapNeg = new HeatMapLayer(map, liveTweetsNeg, heatmapNegOptions);
                }
            });

            $("#searchbox").val("xbox");
            $("#searchBtn").click(onsearch);
            $("#positiveBtn").click(onPositiveBtn);
            $("#negativeBtn").click(onNegativeBtn);
            $("#neutralBtn").click(onNeutralBtn);
            $("#neutralBtn").button("toggle");
        }

        function onsearch() {
            var uri = 'api/tweets?query=';
            var query = $('#searchbox').val();
            $.getJSON(uri + query)
                .done(function (data) {
                    liveTweetsPos = [];
                    liveTweets = [];
                    liveTweetsNeg = [];

                    // On success, 'data' contains a list of tweets.
                    $.each(data, function (key, item) {
                        addTweet(item);
                    });

                    if (!$("#neutralBtn").hasClass('active')) {
                        $("#neutralBtn").button("toggle");
                    }
                    onNeutralBtn();
                })
                .fail(function (jqXHR, textStatus, err) {
                    $('#statustext').text('Error: ' + err);
                });
        }

        function addTweet(item) {
            //Add tweet to the heat map arrays.
            var tweetLocation = new Microsoft.Maps.Location(item.Latitude, item.Longtitude);
            if (item.Sentiment > 0) {
                liveTweetsPos.push(tweetLocation);
            } else if (item.Sentiment < 0) {
                liveTweetsNeg.push(tweetLocation);
            } else {
                liveTweets.push(tweetLocation);
            }
        }

        function onPositiveBtn() {
            if ($("#neutralBtn").hasClass('active')) {
                $("#neutralBtn").button("toggle");
            }
            if ($("#negativeBtn").hasClass('active')) {
                $("#negativeBtn").button("toggle");
            }

            heatmapPos.SetPoints(liveTweetsPos);
            heatmapPos.Show();
            heatmapNeg.Hide();
            heatmap.Hide();

            $('#statustext').text('Tweets: ' + liveTweetsPos.length + "   " + getPosNegRatio());
        }

        function onNeutralBtn() {
            if ($("#positiveBtn").hasClass('active')) {
                $("#positiveBtn").button("toggle");
            }
            if ($("#negativeBtn").hasClass('active')) {
                $("#negativeBtn").button("toggle");
            }

            heatmap.SetPoints(liveTweets);
            heatmap.Show();
            heatmapNeg.Hide();
            heatmapPos.Hide();

            $('#statustext').text('Tweets: ' + liveTweets.length + "   " + getPosNegRatio());
        }

        function onNegativeBtn() {
            if ($("#positiveBtn").hasClass('active')) {
                $("#positiveBtn").button("toggle");
            }
            if ($("#neutralBtn").hasClass('active')) {
                $("#neutralBtn").button("toggle");
            }

            heatmapNeg.SetPoints(liveTweetsNeg);
            heatmapNeg.Show();
            heatmap.Hide();;
            heatmapPos.Hide();;

            $('#statustext').text('Tweets: ' + liveTweetsNeg.length + "\t" + getPosNegRatio());
        }

        function getPosNegRatio() {
            if (liveTweetsNeg.length == 0) {
                return "";
            }
            else {
                var ratio = liveTweetsPos.length / liveTweetsNeg.length;
                var str = parseFloat(Math.round(ratio * 10) / 10).toFixed(1);
                return "Positive/Negative Ratio: " + str;
            }
        }

**layout.cshtml を変更するには**

1. **ソリューション エクスプローラー**で、**[TweetSentimentWeb]**、**[ビュー]**、**[共有]** の順に展開し、**[Layout.cshtml]** をダブルクリックします。
2. 次のコンテンツに置き換えます。

        <!DOCTYPE html>
        <html>
        <head>
            <meta charset="utf-8" />
            <meta name="viewport" content="width=device-width, initial-scale=1.0">
            <title>@ViewBag.Title</title>
            @Styles.Render("~/Content/css")
            @Scripts.Render("~/bundles/modernizr")
            <!-- Bing Maps -->
            <script type="text/javascript" src="http://ecn.dev.virtualearth.net/mapcontrol/mapcontrol.ashx?v=7.0&mkt=en-gb"></script>
            <!-- Spatial Dashboard JavaScript -->
            <script src="~/Scripts/twitterStream.js" type="text/javascript"></script>
        </head>
        <body onload="initialize()">
            <div class="navbar navbar-inverse navbar-fixed-top">
                <div class="container">
                    <div class="navbar-header">
                        <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                            <span class="icon-bar"></span>
                            <span class="icon-bar"></span>
                            <span class="icon-bar"></span>
                        </button>
                    </div>
                    <div class="navbar-collapse collapse">
                        <div class="row">
                            <ul class="nav navbar-nav col-lg-5">
                                <li class="col-lg-12">
                                    <div class="navbar-form">
                                        <input id="searchbox" type="search" class="form-control">
                                        <button type="button" id="searchBtn" class="btn btn-primary">Go</button>
                                    </div>
                                </li>
                            </ul>
                            <ul class="nav navbar-nav col-lg-7">
                                <li>
                                    <div class="navbar-form">
                                        <div class="btn-group" data-toggle="buttons-radio">
                                            <button type="button" id="positiveBtn" class="btn btn-primary">Positive</button>
                                            <button type="button" id="neutralBtn" class="btn btn-primary">Neutral</button>
                                            <button type="button" id="negativeBtn" class="btn btn-primary">Negative</button>
                                        </div>
                                    </div>
                                </li>
                                <li><span id="statustext" class="navbar-text"></span></li>
                            </ul>
                        </div>
                    </div>
                </div>
            </div>
            <div class="map_container">
                @RenderBody()
            </div>
            @Scripts.Render("~/bundles/jquery")
            @Scripts.Render("~/bundles/bootstrap")
            @RenderSection("scripts", required: false)
        </body>
        </html>

**Index.cshtml を変更するには**

1. **ソリューション エクスプローラー**で **[TweetSentimentWeb]**、**[ビュー]**、**[ホーム]** の順に展開し、**[Index.cshtml]** をダブルクリックします。
2. 次のコンテンツに置き換えます。

        @{
            ViewBag.Title = "Tweet Sentiment";
        }

        <div class="map_container">
            <div id="map_canvas"/>
        </div>

**site.css ファイルを変更するには**

1. **ソリューション エクスプローラー**で、**[TweetSentimentWeb]**、**[コンテンツ]** の順に展開し、**[Site.css]** をダブルクリックします。
2. ファイルに、以下のコードを追加します。

        /* make container, and thus map, 100% width */
        .map_container {
            width: 100%;
            height: 100%;
        }

        #map_canvas{
          height:100%;
        }

        #tweets{
          position: absolute;
          top: 60px;
          left: 75px;
          z-index:1000;
          font-size: 30px;
        }

**global.asax ファイルを変更するには**

1. **ソリューション エクスプローラー**で **[TweetSentimentWeb]** を展開し、**[Global.asax]** をダブルクリックします。
2. 次の **using** ステートメントを追加します。

        using System.Web.Http;
3. 以下の行を **Application_Start()** 関数内に追加します。

        // Register API routes
        GlobalConfiguration.Configure(WebApiConfig.Register);

    API ルートの登録を変更し、Web API コントローラーが MVC アプリケーション内で動作するようにします。

**Web アプリケーションを実行するには**

1. ストリーミング サービス コンソール アプリケーションが実行され、リアルタイムの変更が表示されることを確認します。
2. **F5** を押して、Web アプリケーションを実行します。

    ![hdinsight.hbase.twitter.sentiment.bing.map][img-bing-map]
3. テキスト ボックスにキーワードを入力し、 **[検索]**をクリックします。  HBase テーブルで収集したデータによっては、キーワードが検出できない場合もあります。 「love」、「xbox」、「playstation」などの一般的なキーワードを試してください。
4. **[肯定]**、**[中立]**、**[否定]** を切り替えて、対象の感情を比較します。
5. 別の時間帯にストリーミング サービスを実行し、同じキーワードを検索して結果を比較してください。

Azure Web サイトにこのアプリケーションをデプロイすることもできます。 手順については、[Azure Websites と ASP.NET の概要][website-get-started]に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、ツイートを取得し、ツイートのセンチメントを分析し、センチメント データを HBase に保存し、リアルタイムの Twitter センチメント データを Bing マップに表示する方法について学習しました。 詳細については、次を参照してください。

* [HDInsight の概要][hdinsight-get-started]
* [HDInsight での HBase レプリケーションの構成](hdinsight-hbase-replication.md)
* [HDInsight での Hadoop を使用した Twitter データの分析][hdinsight-analyze-twitter-data]
* [HDInsight を使用したフライト遅延データの分析][hdinsight-analyze-flight-delay-data]
* [HDInsight 用 Java MapReduce プログラムの開発][hdinsight-develop-mapreduce]

[hbase-get-started]: hdinsight-hbase-tutorial-get-started-linux.md
[website-get-started]: ../app-service-web/app-service-web-get-started-dotnet.md



[img-app-arch]: ./media/hdinsight-hbase-analyze-twitter-sentiment/AppArchitecture.png
[img-twitter-app]: ./media/hdinsight-hbase-analyze-twitter-sentiment/TwitterApp.png
[img-streaming-service]: ./media/hdinsight-hbase-analyze-twitter-sentiment/StreamingService.png
[img-bing-map]: ./media/hdinsight-hbase-analyze-twitter-sentiment/TwitterSentimentBingMap.png



[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-analyze-twitter-data]: hdinsight-analyze-twitter-data.md




[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]: hdinsight-hadoop-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md

