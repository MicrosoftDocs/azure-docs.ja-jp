---
title: Apache Oozie ワークフローとエンタープライズ セキュリティ - Azure HDInsight
description: Azure HDInsight Enterprise セキュリティ パッケージを使用して Apache Oozie ワークフローをセキュリティで保護します。 Oozie ワークフローを定義し、Oozie ジョブを送信する方法について説明します。
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seodec18,seoapr2020
ms.date: 05/14/2020
ms.openlocfilehash: 36c04480c46cea904b072c659c5c2642a28e1f27
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647577"
---
# <a name="run-apache-oozie-in-azure-hdinsight-clusters-with-enterprise-security-package"></a>Enterprise セキュリティ パッケージを使用する Azure HDInsight クラスターで Apache Oozie を実行する

Apache Oozie は Apache Hadoop ジョブを管理するワークフローおよび調整システムです。 Oozie は Hadoop スタックと統合されており、次のジョブをサポートしています。

- Apache MapReduce
- Apache Pig
- Apache Hive
- Apache Sqoop

Oozie を使って、Java プログラムやシェル スクリプトなどの、システムに固有のジョブをスケジュールすることもできます。

## <a name="prerequisite"></a>前提条件

Enterprise セキュリティ パッケージ (ESP) を使用する Azure HDInsight Hadoop クラスター。 [ESP を使用した HDInsight クラスターの構成](./apache-domain-joined-configure-using-azure-adds.md)に関するページをご覧ください。

> [!NOTE]  
> ESP を使用していないクラスター上で Oozie を使用する方法の手順について詳しくは、[Linux ベースの Azure HDInsight での Apache Oozie ワークフローの使用](../hdinsight-use-oozie-linux-mac.md)に関するページをご覧ください。

## <a name="connect-to-an-esp-cluster"></a>ESP クラスターに接続する

Secure Shell (SSH) の詳細については、「[SSH を使用して HDInsight (Hadoop) に接続する](../hdinsight-hadoop-linux-use-ssh-unix.md)」を参照してください。

1. SSH を使って HDInsight クラスターに接続します。

    ```bash
    ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
    ```

1. Kerberos 認証が成功したかどうかを確認するには、`klist` コマンドを使用します。 そうでない場合は、`kinit` を使用して Kerberos 認証を開始します。

1. Azure Data Lake Storage にアクセスするために必要な OAuth トークンを登録するには、HDInsight ゲートウェイにサインインします。

    ```bash
    curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
    ```

    **200 OK** の状態応答コードは、登録の成功を示します。 承認されていない応答 (401 など) が受信された場合は、ユーザー名とパスワードを確認します。

## <a name="define-the-workflow"></a>ワークフローの定義

Oozie ワークフローの定義は、Apache Hadoop プロセス定義言語 (hPDL) を使って記述します。 hPDL は XML プロセス定義言語です。 次の手順に従ってワークフローを定義します。

1. ドメイン ユーザーのワークスペースを設定します。

   ```bash
   hdfs dfs -mkdir /user/<DomainUser>
   cd /home/<DomainUserPath>
   cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
   tar -xvf oozie-examples.tar.gz
   hdfs dfs -put examples /user/<DomainUser>/
   ```

   `DomainUser` をドメイン ユーザー名に置き換えます。
   `DomainUserPath` をドメイン ユーザーのホーム ディレクトリのパスに置き換えます。
   `ClusterVersion` を実際のクラスター データ プラットフォームのバージョンに置き換えます。

2. 次のステートメントを使用して、新しいファイルを作成し、編集します。

   ```bash
   nano workflow.xml
   ```

3. nano エディターが開いたら、ファイルの内容として次の XML を入力します。

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <workflow-app xmlns="uri:oozie:workflow:0.4" name="map-reduce-wf">
       <credentials>
          <credential name="metastore_token" type="hcat">
             <property>
                <name>hcat.metastore.uri</name>
                <value>thrift://<active-headnode-name>-<clustername>.<Domain>.com:9083</value>
             </property>
             <property>
                <name>hcat.metastore.principal</name>
                <value>hive/_HOST@<Domain>.COM</value>
             </property>
          </credential>
          <credential name="hs2-creds" type="hive2">
             <property>
                <name>hive2.server.principal</name>
                <value>${jdbcPrincipal}</value>
             </property>
             <property>
                <name>hive2.jdbc.url</name>
                <value>${jdbcURL}</value>
             </property>
          </credential>
       </credentials>
       <start to="mr-test" />
       <action name="mr-test">
          <map-reduce>
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <prepare>
                <delete path="${nameNode}/user/${wf:user()}/examples/output-data/mrresult" />
             </prepare>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
                <property>
                   <name>mapred.mapper.class</name>
                   <value>org.apache.oozie.example.SampleMapper</value>
                </property>
                <property>
                   <name>mapred.reducer.class</name>
                   <value>org.apache.oozie.example.SampleReducer</value>
                </property>
                <property>
                   <name>mapred.map.tasks</name>
                   <value>1</value>
                </property>
                <property>
                   <name>mapred.input.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/input-data/text</value>
                </property>
                <property>
                   <name>mapred.output.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/output-data/mrresult</value>
                </property>
             </configuration>
          </map-reduce>
          <ok to="myHive2" />
          <error to="fail" />
       </action>
       <action name="myHive2" cred="hs2-creds">
          <hive2 xmlns="uri:oozie:hive2-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <jdbc-url>${jdbcURL}</jdbc-url>
             <script>${hiveScript2}</script>
             <param>hiveOutputDirectory2=${hiveOutputDirectory2}</param>
          </hive2>
          <ok to="myHive" />
          <error to="fail" />
       </action>
       <action name="myHive" cred="metastore_token">
          <hive xmlns="uri:oozie:hive-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
             </configuration>
             <script>${hiveScript1}</script>
             <param>hiveOutputDirectory1=${hiveOutputDirectory1}</param>
          </hive>
          <ok to="end" />
          <error to="fail" />
       </action>
       <kill name="fail">
          <message>Oozie job failed, error message[${wf:errorMessage(wf:lastErrorNode())}]</message>
       </kill>
       <end name="end" />
    </workflow-app>
    ```

4. `clustername` をクラスターの名前に置き換えます。

5. ファイルを保存するには、**Ctrl + X** キーを押します。 「**Y**」と入力します。次に、**Enter** キーを押します。

    このワークフローは、次の 2 つの部分に分かれています。

   - **資格情報。** このセクションでは、Oozie アクションを認証するために使用される資格情報を取得します。

     この例では、Hive アクション用の認証を使用します。 詳細については、[アクションの認証](https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html)に関するページを参照してください。

     資格情報サービスでは、Oozie アクションが、Hadoop サービスにアクセスするためのユーザーを偽装することが許可されます。

   - **アクション。** このセクションには、map-reduce、Hive server 2、および Hive server 1 という 3 つのアクションがあります。

     - map-reduce アクションは、集計された単語数を出力する、map-reduce 用の Oozie パッケージにある例を実行します。

     - Hive server 2 および Hive server 1 アクションは、HDInsight に付属しているサンプルの Hive テーブルに対するクエリを実行します。

     Hive アクションは、アクション要素内のキーワード `cred` を使用して、認証用の資格情報セクションで定義されている資格情報を使用します。

6. 次のコマンドを使用して、`workflow.xml` ファイルを `/user/<domainuser>/examples/apps/map-reduce/workflow.xml` にコピーします。

    ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
    ```

7. `domainuser` をドメインのユーザー名に置き換えます。

## <a name="define-the-properties-file-for-the-oozie-job"></a>oozie ジョブのプロパティ ファイルを定義する

1. 次のステートメントを使用して、ジョブ プロパティのための新しいファイルを作成および編集します。

    ```bash
    nano job.properties
    ```

2. nano エディターが開いたら、ファイルの内容として次の XML を使います。

   ```bash
   nameNode=adl://home
   jobTracker=headnodehost:8050
   queueName=default
   examplesRoot=examples
   oozie.wf.application.path=${nameNode}/user/[domainuser]/examples/apps/map-reduce/workflow.xml
   hiveScript1=${nameNode}/user/${user.name}/countrowshive1.hql
   hiveScript2=${nameNode}/user/${user.name}/countrowshive2.hql
   oozie.use.system.libpath=true
   user.name=[domainuser]
   jdbcPrincipal=hive/<active-headnode-name>.<Domain>.com@<Domain>.COM
   jdbcURL=[jdbcurlvalue]
   hiveOutputDirectory1=${nameNode}/user/${user.name}/hiveresult1
   hiveOutputDirectory2=${nameNode}/user/${user.name}/hiveresult2
   ```

   - プライマリ クラスター記憶域として Azure Data Lake Storage Gen1 がある場合は、`nameNode` プロパティに `adl://home` URI を使用します。 Azure Blob Storage を使用している場合は、`wasb://home` に変更します。 Azure Data Lake Storage Gen2 を使用している場合は、`abfs://home` に変更します。
   - `domainuser` をドメインのユーザー名に置き換えます。  
   - `ClusterShortName` をクラスターの短い名前に置き換えます。 たとえば、クラスター名が https:// *[example link]* sechadoopcontoso.azurehdisnight.net である場合、`clustershortname` はクラスターの最初の 6 文字である **sechad** です。  
   - `jdbcurlvalue` を Hive 構成の JDBC URL に置き換えます。 たとえば、jdbc:hive2://headnodehost:10001/;transportMode=http とします。
   - ファイルを保存するには、Ctrl + X キー、`Y` キー、**Enter** キーの順に押します。

   このプロパティ ファイルは、Oozie ジョブの実行時にローカルに存在する必要があります。

## <a name="create-custom-hive-scripts-for-oozie-jobs"></a>Oozie ジョブのカスタム Hive スクリプトの作成

以下に示すように、Hive server 1 と Hive server 2 のための 2 つの Hive スクリプトを作成できます。

### <a name="hive-server-1-file"></a>Hive server 1 のファイル

1. Hive server 1 アクション用のファイルを作成して編集します。

    ```bash
    nano countrowshive1.hql
    ```

2. スクリプトを作成します。

    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    select devicemake from hivesampletable limit 2;
    ```

3. ファイルを Apache Hadoop 分散ファイル システム (HDFS) に保存します。

    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

### <a name="hive-server-2-file"></a>Hive server 2 のファイル

1. Hive server 2 アクション用のファイルを作成して編集します。

    ```bash
    nano countrowshive2.hql
    ```

2. スクリプトを作成します。

    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3. ファイルを HDFS に保存します。

    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submit-oozie-jobs"></a>Oozie ジョブの送信

ESP クラスターの Oozie ジョブの送信は、非 ESP クラスター内の Oozie ジョブの送信と同様です。

詳細については、「[Apache Hadoop で Apache Oozie を使用して Linux ベースの Azure HDInsight でワークフローを定義して実行する](../hdinsight-use-oozie-linux-mac.md)」を参照してください。

## <a name="results-from-an-oozie-job-submission"></a>Oozie ジョブの送信の結果

Oozie ジョブはユーザーの代わりに実行されます。 そのため、Apache Hadoop YARN と Apache Ranger の両方の監査ログには、これらのジョブが偽装されたユーザーとして実行されたことが示されています。 Oozie ジョブのコマンド ライン インターフェイス出力は次のコードのようになります。

```output
Job ID : 0000015-180626011240801-oozie-oozi-W
------------------------------------------------------------------------------------------------
Workflow Name : map-reduce-wf
App Path      : adl://home/user/alicetest/examples/apps/map-reduce/wf.xml
Status        : SUCCEEDED
Run           : 0
User          : alicetest
Group         : -
Created       : 2018-06-26 19:25 GMT
Started       : 2018-06-26 19:25 GMT
Last Modified : 2018-06-26 19:30 GMT
Ended         : 2018-06-26 19:30 GMT
CoordAction ID: -

Actions
------------------------------------------------------------------------------------------------
ID                      Status  Ext ID          ExtStatus   ErrCode
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@:start:    OK  -           OK      -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@mr-test    OK  job_1529975666160_0051  SUCCEEDED   -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@myHive2    OK  job_1529975666160_0053  SUCCEEDED   -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@myHive OK  job_1529975666160_0055  SUCCEEDED   -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@end    OK  -           OK      -
-----------------------------------------------------------------------------------------------
```

Hive server 2 アクションの Ranger 監査ログには、Oozie がユーザーに代わってアクションを実行したことが示されています。 Ranger や YARN のビューは、クラスター管理者にのみ表示されます。

## <a name="configure-user-authorization-in-oozie"></a>Oozie でのユーザーの承認の構成

Oozie はそれ自体に、ユーザーが他のユーザーのジョブを停止または削除することをブロックできるユーザーの承認の構成を持っています。 この構成を有効にするには、`oozie.service.AuthorizationService.security.enabled` を `true` に設定します。 

詳細については、[Apache Oozie のインストールと構成](https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html)に関するページを参照してください。

Ranger プラグインが使用できないか、またはサポートされていない Hive server 1 などのコンポーネントの場合は、粒度の粗い HDFS 承認のみが可能です。 きめ細かい承認は、Ranger プラグインを通してのみ使用できます。

## <a name="get-the-oozie-web-ui"></a>Oozie Web UI の取得

Oozie Web UI は、クラスターでの Oozie ジョブの状態を表示する Web ベースのビューを提供します。 Web UI を取得するには、ESP クラスターで次の手順を実行します。

1. [エッジ ノード](../hdinsight-apps-use-edge-node.md)を追加し、[SSH Kerberos 認証](../hdinsight-hadoop-linux-use-ssh-unix.md)を有効にします。

2. [Oozie Web UI](../hdinsight-use-oozie-linux-mac.md) の手順に従ってエッジ ノードへの SSH トンネリングを有効にし、Web UI にアクセスします。

## <a name="next-steps"></a>次のステップ

- [Apache Hadoop で Apache Oozie を使用して Linux ベースの Azure HDInsight でワークフローを定義して実行する](../hdinsight-use-oozie-linux-mac.md)。
- [SSH を使用して HDInsight (Apache Hadoop) に接続する](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight)。
