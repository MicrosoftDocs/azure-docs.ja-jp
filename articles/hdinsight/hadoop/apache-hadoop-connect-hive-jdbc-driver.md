---
title: JDBC ドライバーを使用して Apache Hive のクエリを実行する - Azure HDInsight
description: Java アプリケーションから JDBC ドライバーを使用して、Apache Hive のクエリを HDInsight 上の Hadoop に送信する方法について説明します。 プログラムを使用して、SQuirrel SQL クライアントから接続します。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: hrasheed
ms.openlocfilehash: dc088d3a6baa4198a1d5cb566b968c631ae5f2c4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58113818"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>HDInsight 上で JDBC ドライバーを使用して Apache Hive のクエリを実行する

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Java アプリケーションから JDBC ドライバーを使用して、Apache Hive のクエリを Azure HDInsight 上の Apache Hadoop に送信する方法について説明します。 このドキュメントの情報では、プログラミングによって SQuirreL SQL クライアントから接続する方法を示します。

Hive JDBC インターフェイスの詳細については、 [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface)を参照してください。

## <a name="prerequisites"></a>前提条件

* HDInsight Hadoop クラスター。 その作成方法については、[Azure HDInsight の概要](apache-hadoop-linux-tutorial-get-started.md)に関するページをご覧ください。
* [Java Developer Kit (JDK) バージョン 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) 以降。
* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/)。 SQuirreL は、JDBC クライアント アプリケーションです。


## <a name="jdbc-connection-string"></a>JDBC 接続文字列

Azure の HDInsight クラスターに対する JDBC 接続はポート 443 を使用して行われ、トラフィックは SSL を使用してセキュリティで保護されます。 クラスターが背後に存在するパブリックのゲートウェイは HiveServer2 が実際にリッスンするポートにトラフィックをリダイレクトします。 次の接続文字列は、HDInsight に使用する形式を示しています。

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

`CLUSTERNAME` を、使用する HDInsight クラスターの名前に置き換えます。

## <a name="authentication"></a>Authentication

接続を確立するときに、HDInsight クラスターの管理者名とパスワードを使用して、クラスター ゲートウェイを認証する必要があります。 SQuirreL SQL などの JDBC クライアントから接続する場合、クライアント設定で管理者名とパスワードを入力する必要があります。

Java アプリケーションから接続を確立する場合、名前とパスワードを使用する必要があります。 たとえば、次の Java コードは、接続文字列、管理者名、およびパスワードを使用して新しい接続を開きます。

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>SQuirreL SQL クライアントとの接続

SQuirreL SQL は、HDInsight クラスターを使用して Hive クエリをリモートから実行するために使用できる JDBC クライアントです。 次の手順では、既に SQuirreL SQL がインストールされていると仮定します。

1. クラスターからコピーする特定のファイルを含むディレクトリを作成します。

2. 次のスクリプトで、`sshuser` をクラスターの SSH ユーザー アカウント名に置き換えます。  `CLUSTERNAME` を HDInsight クラスター名に置き換えます。  コマンド ラインで次のコマンドを入力し、HDInsight クラスターからファイルをコピーします。

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/hadoop-auth.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/hadoop-common.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/lib/log4j-*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/lib/slf4j-*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/commons-codec*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/commons-logging-*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/hive-*-1.2*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/httpclient-*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/httpcore-*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/libfb*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/libthrift-*.jar .
    ```

3. SQuirreL SQL アプリケーションを起動します。 ウィンドウの左側から **[Drivers]** を選択します。

    ![ウィンドウの左側の [Drivers] タブ](./media/apache-hadoop-connect-hive-jdbc-driver/squirreldrivers.png)

4. **[Drivers\(ドライバー\)]** ダイアログの上部にあるアイコンから、**+** アイコンを選択してドライバーを作成します。

    ![[Drivers] アイコン](./media/apache-hadoop-connect-hive-jdbc-driver/driversicons.png)

5. [Add Driver\(ドライバーの追加\)] ダイアログで、次の情報を追加します。

    * **Name**:Hive
    * **Example URL**: `jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`
    * **Extra Class Path (追加クラス パス)**:[追加] ボタンを使って、以前にダウンロードしたすべての jar ファイルを追加します
    * **Class Name**: org.apache.hive.jdbc.HiveDriver

   ![[Add Driver] ダイアログ](./media/apache-hadoop-connect-hive-jdbc-driver/adddriver.png)

   **[OK]** を選択してこれらの設定を保存します。

6. SQuirreL SQL ウィンドウの左側で、**[Aliases]** を選択します。 次に **+** アイコンを選択して接続のエイリアスを作成します。

    ![新しいエイリアスの追加](./media/apache-hadoop-connect-hive-jdbc-driver/aliases.png)

7. **[Add Alias]** ダイアログに次の値を使用します。

    * **Name**:HDInsight の Hive

    * **Driver**:ドロップダウンを使用して、**[Hive]** ドライバーを選択します

    * **URL**: `jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`

        **CLUSTERNAME** を、使用する HDInsight クラスターの名前に置き換えます。

    * **User Name**:HDInsight クラスターのクラスター ログイン アカウント名。 既定では、 `admin`です。

    * **Password**:クラスター ログイン アカウントのパスワード。

   ![[Add Alias] ダイアログ](./media/apache-hadoop-connect-hive-jdbc-driver/addalias.png)

    > [!IMPORTANT] 
    > **[Test]** ボタンを使用して、接続が機能することを確認します。 **[Connect to: Hive on HDInsight] (接続先: HDInsight 上の Hive)** ダイアログが表示されたら、**[接続]** を選択してテストを実行します。 テストが成功した場合、**[Connection successful\(接続成功\)]** ダイアログが表示されます。 エラーが発生した場合は、「[トラブルシューティング](#troubleshooting)」を参照してください。

    **[Add Alias\(エイリアスの追加\)]** ダイアログの下部にある **[OK]** ボタンを使用して、接続エイリアスを保存します。

8. SQuirreL SQL の上部にある **[Connect to]** ドロップダウンから、**[Hive on HDInsight]** を選択します。 メッセージが表示されたら、**[Connect]** を選択します。

    ![接続ダイアログ](./media/apache-hadoop-connect-hive-jdbc-driver/connect.png)

9. 接続されたら、SQL クエリ ダイアログに次のクエリを入力し、**[Run]\(実行\)** アイコン (走っている人) を選択します。 結果領域にクエリの結果が表示されます。

    ```hql
    select * from hivesampletable limit 10;
    ```

    ![結果を含む SQL クエリ ダイアログ](./media/apache-hadoop-connect-hive-jdbc-driver/sqlquery.png)

## <a name="connect-from-an-example-java-application"></a>Java アプリケーションの例からの接続

Java クライアントを使って HDInsight の Hive をクエリする例は、[https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc) にあります。 リポジトリの指示に従い、サンプルを作成して実行します。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>SQL 接続を開こうとしたときに、予期しないエラーが発生した

**症状**:バージョン 3.3 以上の HDInsight クラスターに接続するときに、予期しないエラーが発生したというエラーを受け取る場合があります。 このエラーのスタック トレースは、次の行で始まります。

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**原因**:このエラーは、SQuirreL に付属する commons-codec.jar ファイルのバージョンが古いために発生します。

**解決策**:このエラーを解決するには、次の手順を使用します。

1. SQuirreL を終了し、SQuirreL がインストールされているシステム上のディレクトリに移動します。 SquirreL ディレクトリ内の `lib` ディレクトリにある既存の commons-codec jar を、HDInsight クラスターからダウンロードしたファイルに置き換えます。

2. SQuirreL を再起動します。 これで、HDInsight の Hive に接続するときにエラーが発生しなくなります。

## <a name="next-steps"></a>次の手順

これで、JDBC を使用して Hive を操作する方法に関する説明は終わりです。次のリンクを使用して、Azure HDInsight を操作するその他の方法について調べることもできます。

* [Azure HDInsight の Microsoft Power BI で Apache Hive データを視覚化する](apache-hadoop-connect-hive-power-bi.md)。
* [Azure HDInsight の Power BI で対話型クエリの Hive データを視覚化する](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)。
* [Azure HDInsight で Apache Zeppelin を使用して Apache Hive クエリを実行する](./../hdinsight-connect-hive-zeppelin.md)。
* [Microsoft Hive ODBC Driver を使用して Excel を HDInsight に接続する](apache-hadoop-connect-excel-hive-odbc-driver.md)。
* [Power Query を使用して Excel を Apache Hadoop に接続する](apache-hadoop-connect-excel-power-query.md)。
* [Data Lake Tools for Visual Studio を使用して Azure HDInsight に接続し、Apache Hive クエリを実行する](apache-hadoop-visual-studio-tools-get-started.md)。
* [Azure HDInsight Tool for Visual Studio Code の使用](../hdinsight-for-vscode.md)。
* [HDInsight へのデータのアップロード](../hdinsight-upload-data.md)
* [HDInsight での Apache Hive の使用](hdinsight-use-hive.md)
* [HDInsight での Apache Pig の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce ジョブの使用](hdinsight-use-mapreduce.md)
