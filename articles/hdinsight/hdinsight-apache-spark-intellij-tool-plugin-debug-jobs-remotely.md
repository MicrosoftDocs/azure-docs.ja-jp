---
title: "Azure Toolkit for IntelliJ を使用した Spark クラスター上のアプリケーションのリモート デバッグ | Microsoft Docs"
description: "Azure Toolkit for IntelliJ の HDInsight ツールを使用して、HDInsight Spark クラスター上で実行されるアプリケーションをリモートでデバッグする方法について解説します。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 55fb454f-c7dc-46de-a978-e242e9a94f4c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 9b822da3d8fd2706bc65ab842d063be66904649f
ms.lasthandoff: 03/25/2017


---
# <a name="use-hdinsight-tools-in-azure-toolkit-for-intellij-to-debug-spark-applications-remotely-on-hdinsight-spark-cluster"></a>Azure Toolkit for IntelliJ の HDInsight ツールを使用して HDInsight Spark クラスター上で Spark アプリケーションをリモートでデバッグする
この記事では、Azure Toolkit for IntelliJ の HDInsight ツールを使って HDInsight Spark クラスター上で Spark ジョブを送信し、デスクトップ コンピューターからリモートでデバッグするための詳細な手順を紹介します。 この作業に必要な手順は、大まかに言うと次のとおりです。

1. サイト間またはポイント対サイトの Azure 仮想ネットワークを作成します。 このドキュメントの手順では、サイト間ネットワークを使用することを想定しています。
2. サイト間 Azure 仮想ネットワークの一部である Azure HDInsight で Spark クラスターを作成します。
3. クラスターのヘッドノードとデスクトップの間の接続を確認します。
4. IntelliJ IDEA で Scala アプリケーションを作成し、リモート デバッグ用に構成します。
5. アプリケーションを実行し、デバッグします。

## <a name="prerequisites"></a>前提条件
* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* HDInsight での Apache Spark クラスター。 手順については、 [Azure HDInsight での Apache Spark クラスターの作成](hdinsight-apache-spark-jupyter-spark-sql.md)に関するページを参照してください。
* Oracle Java Development kit。 [ここ](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)からインストールできます。
* IntelliJ IDEA。 この記事では、バージョン 15.0.1 を使用します。 [ここ](https://www.jetbrains.com/idea/download/)からインストールできます。
* Azure toolkit for IntelliJ の HDInsight ツール。 IntelliJ 用の HDInsight ツールは、Azure Toolkit for IntelliJ に付属しています。 Azure Toolkit をインストールする手順については、「[Azure Toolkit for IntelliJ のインストール](../azure-toolkit-for-intellij-installation.md)」を参照してください。
* IntelliJ IDEA から Azure サブスクリプションにログインします。 [こちら](hdinsight-apache-spark-intellij-tool-plugin.md#log-into-your-azure-subscription)で説明されている手順に従います。
* リモート デバッグを行う目的で Windows コンピューター上で Spark Scala アプリケーションを実行しているときに、Windows 上に WinUtils.exe が不足しているために発生する例外が表示される場合があります。この例外は [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) に説明されています。 このエラーを回避するには、[実行可能ファイルをこちらからダウンロード](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)して、**C:\WinUtils\bin** のような場所に保存する必要があります。 次に、環境変数 **HADOOP_HOME** を追加し、この変数の値を **C:\WinUtils** に設定します。

## <a name="step-1-create-an-azure-virtual-network"></a>手順 1. Azure 仮想ネットワークを作成する
次のリンクの手順に従って Azure 仮想ネットワークを作成し、デスクトップと Azure 仮想ネットワークの間の接続を確認します。

* [Azure ポータルを使用してサイト間 VPN 接続を持つ VNet を作成する](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [PowerShell を使用してサイト間 VPN 接続を持つ VNet を作成する](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [PowerShell を使用して仮想ネットワークへのポイント対サイト接続を構成する](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>手順 2. HDInsight Spark クラスターを作成する
先ほど作成した Azure 仮想ネットワークに属する Apache Spark クラスターを Azure HDInsight で作成する必要があります。 詳細については、「[HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。 オプションの構成の一部として、前の手順で作成した Azure 仮想ネットワークを選択します。

## <a name="step-3-verify-the-connectivity-between-the-cluster-headnode-and-your-desktop"></a>手順 3. クラスターのヘッドノードとデスクトップの間の接続を確認する
1. ヘッドノードの IP アドレスを取得します。 クラスターの Ambari UI を開きます。 クラスター ブレードから、 **[ダッシュボード]**をクリックします。
   
    ![Find headnode IP](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-ambari-ui.png)
2. Ambari UI の右上隅にある **[Hosts (ホスト)]**をクリックします。
   
    ![Find headnode IP](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-hosts.png)
3. ヘッドノード、worker ノード、zookeeper ノードの一覧が表示されます。 ヘッドノードには **hn*** プレフィックスが付きます。 最初のヘッドノードをクリックします。
   
    ![Find headnode IP](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/cluster-headnodes.png)
4. 開いたページの下部にある **[Summary (概要)]** ボックスからヘッドノードの IP アドレスとホスト名をコピーします。
   
    ![Find headnode IP](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address.png)
5. Spark ジョブの実行とリモート デバッグに使用するコンピューターの **hosts** ファイルに、ヘッドノードの IP アドレスとホスト名を記載します。 これで、ホスト名と IP アドレスのどちらを使用してもヘッドノードと通信ができるようになります。
   
   1. 管理者特権のアクセス許可を使用してメモ帳を開きます。 ファイル メニューの **[開く]** をクリックし、hosts ファイルの場所に移動します。 Windows コンピューター上での場所は `C:\Windows\System32\Drivers\etc\hosts`です。
   2. **hosts** ファイルに次のコードを追加します。
      
           # For headnode0
           192.xxx.xx.xx hn0-nitinp
           192.xxx.xx.xx hn0-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
      
           # For headnode1
           192.xxx.xx.xx hn1-nitinp
           192.xxx.xx.xx hn1-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
6. HDInsight クラスターで使用されている Azure 仮想ネットワークに接続したコンピューターから、両方のヘッドノードに対して IP アドレスとホスト名を使用して ping を実行できることを確認します。
7. [SSH を使用した HDInsight クラスターへの接続](hdinsight-hadoop-linux-use-ssh-unix.md)に関するセクションの手順に従い、クラスターのヘッドノードに SSH で接続します。 クラスターのヘッドノードから、デスクトップ コンピューターの IP アドレスに ping を実行します。 コンピューターに割り当てられた両方の IP アドレスの接続をテストする必要があります。一方はネットワーク接続のための IP アドレスで、もう一方はコンピューターが接続されている Azure 仮想ネットワーク用の IP アドレスです。
8. もう一方のヘッドノードについても同じ手順を繰り返します。 

## <a name="step-4-create-a-spark-scala-application-using-the-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>手順 4. Azure Toolkit for IntelliJ の HDInsight ツールを使用して Spark Scala アプリケーションを作成し、リモート デバッグ用に構成する
1. IntelliJ IDEA を起動し、新しいプロジェクトを作成します。 [New Project (新規プロジェクト)] ダイアログ ボックスで、次の選択を行い、 **[Next (次へ)]**をクリックします。
   
    ![Spark Scala アプリケーションの作成](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)
   
   * 左側のウィンドウで、 **[HDInsight]**を選択します。
   * 右側のウィンドウで、 **[Spark on HDInsight (Scala) (HDInsight の Spark (Scala))]**を選択します。
   * **[次へ]**をクリックします。
2. 次のウィンドウで、プロジェクトの詳細を指定します。
   
   * プロジェクト名とプロジェクトの場所を指定します。
   * **Project SDK**の場合、Java のバージョンは必ず 7 より新しいものを指定します。
   * **Scala SDK** の場合、**[Create (作成)]** をクリックし、**[Download (ダウンロード)]** をクリックして、使用する Scala のバージョンを選択します。 **バージョン 2.11.x は使用しないでください**。 このサンプルでは、バージョン **2.10.6**を使用します。
     
       ![Spark Scala アプリケーションの作成](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-version.png)
   * **Spark SDK** の場合は、[ここ](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409)から SDK をダウンロードして使用してください。 これを無視し、代わりに [Spark Maven リポジトリ](http://mvnrepository.com/search?q=spark)を使用することもできますが、Spark アプリケーションの開発に適した maven リポジトリがインストールされていることを確認してください。 (たとえば、Spark ストリーミングを使用する場合は、Spark ストリーミング部分がインストールされていることを確認する必要があります。また、Scala 2.10 とマークされているリポジトリを使用していることを確認してください。Scala 2.11 とマークされているリポジトリは使用しないでください)。
     
       ![Spark Scala アプリケーションの作成](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)
   * **[完了]**をクリックします。
3. Spark プロジェクトでは、アーティファクトが自動的に作成されます。 アーティファクトを表示するには、次の手順を実施します。
   
   1. **[File (ファイル)]** メニューの **[Project Structure (プロジェクトの構造)]** をクリックします。
   2. **[Project Structure (プロジェクトの構造)]** ダイアログ ボックスの **[Artifacts (アーティファクト)]** をクリックし、作成された既定のアーティファクトを確認します。
      
       ![JAR の作成](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/default-artifact.png)
      
      上の図で強調表示されている **+** アイコンをクリックして、独自のアーティファクトを作成することもできます。
4. **[Project Structure (プロジェクトの構造)]** ダイアログ ボックスの **[Project (プロジェクト)]** をクリックします。 **[Project SDK (プロジェクト SDK)]** が 1.8 に設定されている場合、**[Project language level (プロジェクト言語レベル)]** が **[7 - Diamonds, ARM, multi-catch, etc (7 - Diamonds、ARM、マルチキャッチなど)]** に設定されていることを確認してください。
   
    ![Set project language level](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/set-project-language-level.png)
5. プロジェクトにライブラリを追加します。 ライブラリを追加するには、プロジェクト ツリーのプロジェクト名を右クリックし、 **[Open Module Settings (モジュール設定を開く)]**をクリックします。 **[Project Structure (プロジェクトの構造)]** ダイアログ ボックスの左側のウィンドウで、**[Libraries (ライブラリ)]**、(+) 記号、**[From Maven (Maven から)]** の順にクリックします。 
   
    ![Add library](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/add-library.png) 
   
    **[Download Library from Maven Repository (Maven リポジトリからライブラリをダウンロード)]** ダイアログ ボックスで、次のライブラリを検索して追加します。
   
   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`
6. クラスターのヘッドノードから `yarn-site.xml` と `core-site.xml` をコピーし、プロジェクトに追加します。 ファイルのコピーには次のコマンドを使用します。 [Cygwin](https://cygwin.com/install.html) を使用して次の `scp` コマンドを実行し、クラスター ヘッドノードからファイルをコピーできます。
   
        scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .
   
    既にデスクトップの hosts ファイルにクラスター ヘッドノードの IP アドレスとホスト名を追加しているので、 **scp** コマンドは次の方法で使用できます。
   
        scp sshuser@hn0-nitinp:/etc/hadoop/conf/core-site.xml .
        scp sshuser@hn0-nitinp:/etc/hadoop/conf/yarn-site.xml .
   
    これらのファイルをプロジェクト ツリー内の **/src** フォルダー (例: `<your project directory>\src`) の下にコピーして、プロジェクトに追加します。
7. 次の変更を加えるために、 `core-site.xml` を更新します。
   
   1. `core-site.xml` には、クラスターに関連付けられたストレージ アカウント用の暗号化されたキーが含まれます。 プロジェクトに追加した `core-site.xml` で、暗号化されたキーを、既定のストレージ アカウントに関連付けられた実際のストレージ キーに置き換えます。 「[ストレージ アクセス キーの管理](../storage/storage-create-storage-account.md#manage-your-storage-account)」を参照してください。
      
           <property>
                 <name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
                 <value>access-key-associated-with-the-account</value>
           </property>
   2. 次のエントリを `core-site.xml`から削除します。
      
           <property>
                 <name>fs.azure.account.keyprovider.hdistoragecentral.blob.core.windows.net</name>
                 <value>org.apache.hadoop.fs.azure.ShellDecryptionKeyProvider</value>
           </property>
      
           <property>
                 <name>fs.azure.shellkeyprovider.script</name>
                 <value>/usr/lib/python2.7/dist-packages/hdinsight_common/decrypt.sh</value>
           </property>
      
           <property>
                 <name>net.topology.script.file.name</name>
                 <value>/etc/hadoop/conf/topology_script.py</value>
           </property>
   3. ファイルを保存します。
8. アプリケーションに Main クラスを追加します。 **Project Explorer** で、**[src]** を右クリックし、**[New (新規)]** をポイントして、**[Scala class (Scala クラス)]** をクリックします。
   
    ![ソース コードの追加](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)
9. **[Create New Scala Class (新規 Scala クラスの作成)]** ダイアログ ボックスで、名前を指定し、**[Kind (種類)]** ボックスの一覧で **[Object (オブジェクト)]** を選択して、**[OK]** をクリックします。
   
    ![ソース コードの追加](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)
10. 以下のコードを `MyClusterAppMain.scala` ファイルに貼り付けます。 このコードは Spark コンテキストを作成し、`SparkSample` オブジェクトから `executeJob` メソッドを起動します。

        import org.apache.spark.{SparkConf, SparkContext}

        object SparkSampleMain {
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("SparkSample")
                                      .set("spark.hadoop.validateOutputSpecs", "false")
            val sc = new SparkContext(conf)

            SparkSample.executeJob(sc,
                                   "wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
                                   "wasbs:///HVACOut")
          }
        }

1. 上の手順 8. と手順 9. を繰り返して、`SparkSample` という名前の新しい Scala オブジェクトを追加します。 このクラスには次のコードを追加します。 このコードは HVAC.csv (すべての HDInsight Spark クラスターで使用可能) からデータを読み取り、CSV の 7 番目の列で 1 桁の数字のみが含まれる行を取得し、出力をクラスター用の既定のストレージ コンテナーの下にある **/HVACOut** に書き込みます。
   
       import org.apache.spark.SparkContext
   
       object SparkSample {
         def executeJob (sc: SparkContext, input: String, output: String): Unit = {
           val rdd = sc.textFile(input)
   
           //find the rows which have only one digit in the 7th column in the CSV
           val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
   
           val s = sc.parallelize(rdd.take(5)).cartesian(rdd).count()
           println(s)
   
           rdd1.saveAsTextFile(output)
           //rdd1.collect().foreach(println)
         }
   
       }
2. 上の手順 8. と手順 9. を繰り返して、`RemoteClusterDebugging` という名前の新しいクラスを追加します。 このクラスは、アプリケーションのデバッグに使用する Spark テスト フレームワークを実装します。 次のコードを `RemoteClusterDebugging` クラスに追加します。
   
       import org.apache.spark.{SparkConf, SparkContext}
       import org.scalatest.FunSuite
   
       class RemoteClusterDebugging extends FunSuite {
   
         test("Remote run") {
           val conf = new SparkConf().setAppName("SparkSample")
                                     .setMaster("yarn-client")
                                     .set("spark.yarn.am.extraJavaOptions", "-Dhdp.version=2.4")
                                     .set("spark.yarn.jar", "wasbs:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")
                                     .setJars(Seq("""C:\WORK\IntelliJApps\MyClusterApp\out\artifacts\MyClusterApp_DefaultArtifact\default_artifact.jar"""))
                                     .set("spark.hadoop.validateOutputSpecs", "false")
           val sc = new SparkContext(conf)
   
           SparkSample.executeJob(sc,
             "wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
             "wasbs:///HVACOut")
         }
       }
   
   注意すべき重要な点がいくつかあります。
   
   * `.set("spark.yarn.jar", "wasbs:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`では、指定されたパスのクラスター ストレージで Spark アセンブリ JAR が使用可能であることを確認します。
   * `setJars`では、アーティファクト jar が作成される場所を指定します。 通常は `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar` です。 
3. `RemoteClusterDebugging` クラスで `test` キーワードを右クリックし、**[Create RemoteClusterDebugging Configuration (RemoteClusterDebugging 構成の作成)]** を選択します。
   
   ![Create remote configuration](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)
4. ダイアログ ボックスで、構成の名前を指定し、**[Test name (テスト名)]** として **[Test kind (テストの種類)]** を選択します。 その他の値はすべて既定値のままにして、**[Apply (適用)]**、**[OK]** の順にクリックします。
   
   ![Create remote configuration](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)
5. メニュー バーに、 **[Remote Run (リモート実行)]** の構成のドロップダウンが表示されます。 
   
   ![Create remote configuration](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/config-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>手順 5. デバッグ モードでアプリケーションを実行する
1. IntelliJ IDEA プロジェクトで `SparkSample.scala` を開き、"val rdd1" の次にブレークポイントを作成します。 ブレークポイントの作成に関するポップアップ メニューで、 **[line in function executeJob (executeJob 関数内の行)]**を選択します。
   
    ![Add a breakpoint](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-breakpoint.png)
2. **[Remote Run (リモート実行)]** の構成のドロップダウンの横にある **[Debug Run (デバッグを実行)]** ボタンをクリックして、アプリケーションの実行を開始します。
   
    ![Run the program in debug mode](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode.png)
3. プログラムの実行がブレークポイントに到達すると、下部のウィンドウに **[Debugger (デバッガー)]** タブが表示されます。
   
    ![Run the program in debug mode](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch.png)
4. (**+**) アイコンをクリックして、下の画像のとおりにウォッチ式を追加します。 
   
    ![Run the program in debug mode](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)
   
    この場合は変数 `rdd1` が作成される前にアプリケーションは停止しましたが、このウォッチ式を使用して変数 `rdd` の最初の 5 行を確認できます。 **Enter**キーを押します。
   
    ![Run the program in debug mode](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)
   
    上の画像からわかるように、実行時に大量のデータを照会し、アプリケーションの進行をデバッグできます。 たとえば、上の画像で示されている出力では、出力の最初の行がヘッダーになっています。 これに基づいて、必要に応じて、ヘッダー行をスキップするようにアプリケーションのコードを変更できます。
5. ここまでできたら、 **[Resume Program (プログラムの再開)]** アイコンをクリックしてアプリケーションの実行を続けます。
   
    ![Run the program in debug mode](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-run.png)
6. アプリケーションが正常に完了すると、次のような出力が表示されます。
   
    ![Run the program in debug mode](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete.png)

## <a name="seealso"></a>関連項目
* [概要: Azure HDInsight での Apache Spark](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>シナリオ
* [Spark と BI: HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark ストリーミング: リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-eventhub-streaming.md)
* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](hdinsight-apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [Azure Toolkit for IntelliJ の HDInsight ツールを使用して Spark Scala アプリケーションを作成して送信する](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Azure Toolkit for Eclipse の HDInsight ツールを使用して Spark アプリケーションを作成する](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](hdinsight-apache-spark-job-debugging.md)


