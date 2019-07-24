---
title: Shared Access Signature を使用してアクセスを制限する - Azure HDInsight
description: Azure Storage の BLOB に格納されたデータへの HDInsight のアクセスを制限するために、Shared Access Signature を使用する方法について説明します。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: d248db787db1e3945fb632c6770d45e4bf9a8f02
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2019
ms.locfileid: "58360998"
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Azure Storage の Shared Access Signature を使用して HDInsight でのデータへのアクセスを制限する

HDInsight には、クラスターに関連付けられた Azure Storage アカウントのデータへのフル アクセス権があります。 BLOB コンテナーで Shared Access Signature を使って、データへのアクセスを制限できます。 Shared Access Signature (SAS) は、データへのアクセスを制限できる Azure Storage アカウントの機能です。 たとえば、データに読み取り専用のアクセス権を提供します。

> [!IMPORTANT]  
> Apache Ranger を使うソリューションでは、ドメインに参加している HDInsight を使うことを検討してください。 詳しくは、「[ドメイン参加済み HDInsight クラスターの構成](./domain-joined/apache-domain-joined-configure.md)」をご覧ください。

> [!WARNING]  
> HDInsight には、クラスターの既定のストレージへのフル アクセス権が必要です。

## <a name="requirements"></a>必要条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Azure サブスクリプション
* C# または Python。 C# のサンプル コードは、Visual Studio のソリューションとして提供されます。

  * Visual Studio は、バージョン 2013、2015、または 2017 である必要があります
  * Python は、バージョン 2.7 以上である必要があります

* Linux ベースの HDInsight クラスターまたは [Azure PowerShell][powershell] - 既存の Linux ベースのクラスターがある場合は、Apache Ambari を使用して、クラスターに Shared Access Signature を追加することができます。 ない場合は、Azure PowerShell を使用してクラスターを作成し、クラスターの作成時に Shared Access Signature を追加することができます。

    > [!IMPORTANT]  
    > Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。

* サンプル ファイルは、[https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature) にあります。 このリポジトリには、以下が含まれます。

  * HDInsight で使用するストレージ コンテナー、保存済みのポリシーおよび SAS を作成できる Visual Studio プロジェクト
  * HDInsight で使用するストレージ コンテナー、保存済みのポリシーおよび SAS を作成できる Python スクリプト
  * HDInsight クラスターを作成して、SAS を使用するように構成できる PowerShell スクリプト

## <a name="shared-access-signatures"></a>Shared Access Signature

Shared Access Signature には、次の 2 つのフォームがあります。

* アドホック: 開始時刻、有効期限、SAS へのアクセス許可がすべて、SAS URI で指定されます。

* 保存されているアクセス ポリシー: 保存されているアクセス ポリシーは、リソース コンテナー (BLOB コンテナーなど) で定義されています。 ポリシーを使用して、1 つ以上の Shared Access Signature の制約を管理できます。 保存されているアクセス ポリシーに SAS を関連付けると、SAS は、保存されているアクセス ポリシーに定義されている制約 (開始時刻、有効期限、およびアクセス許可) を継承します。

1 つの重要なシナリオ、失効では、この 2 つの形式の相違点が重要です。 SAS は URL であるため、取得したユーザーはだれでも、どのユーザーが最初に要求したかに関係なく、SAS を使用できます。 SAS が一般ユーザーに発行された場合は、世界中のだれでも使用できます。 配布された SAS は、次の 4 つの状況のいずれかになるまで有効です。

1. SAS に指定された有効期限に達した。

2. SAS によって参照されている保存されたアクセス ポリシーで指定されている有効期限に達した。 次のシナリオの場合、有効期限に達します。

    * 指定された期間が経過した。
    * 保存されているアクセス ポリシーが、過去の有効期限に変更された。 有効期限の変更は、SAS を失効させる方法の 1 つです。

3. SAS の参照先である保存されているアクセス ポリシーが削除されている。これは、SAS を失効させる、もう 1 つの方法です。 保存されているアクセス ポリシーを、同じ名前で再作成すると、前のポリシーの SAS トークンがすべて有効になります (SAS の有効期限が過ぎていない場合)。 SAS を失効させるつもりで、将来の時間を有効期限に指定してアクセス ポリシーを再作成する場合は必ず、異なる名前を使ってください。

4. SAS の作成に使用したアカウント キーが再度生成された。 キーを再生成すると、前のキーを使うすべてのアプリケーションが、認証に失敗します。 すべてのコンポーネントを新しいキーに更新してください。

> [!IMPORTANT]  
> Shared Access Signature URI は、署名の作成に使用されたアカウント キーと、保存済みのアクセス ポリシー (存在する場合) に関連付けられます。 保存済みのアクセス ポリシーが指定されていない場合、Shared Access Signature を取り消すにはアカウント キーを変更する以外に方法はありません。

保存済みのアクセス ポリシーを常に使用することをお勧めします。 保存済みのポリシーを使用している場合、必要に応じて署名を失効させるか、有効期限を延長することができます。 ドキュメント内のこれらの手順は、保存済みのアクセス ポリシーを使用して、SAS を生成します。

Shared Access Signature の詳細については、「 [SAS モデルについて](../storage/common/storage-dotnet-shared-access-signature-part-1.md)」を参照してください。

### <a name="create-a-stored-policy-and-sas-using-c"></a>C\# を使用して保存済みのポリシーと SAS を作成する

1. Visual Studio でソリューションを開きます。

2. ソリューション エクスプローラーで **[SASToken]** プロジェクトを右クリックし、**[プロパティ]** を選択します。

3. **[設定]** を選択し、次のエントリに値を追加します。

   * StorageConnectionString: 保存済みのポリシーと SAS を作成するストレージ アカウントの接続文字列。 `myaccount` がストレージ アカウントの名前で、`mykey` がストレージ アカウントのキーである場合、形式は `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` になります。

   * ContainerName: アクセスを制限するストレージ アカウントのコンテナー。

   * SASPolicyName: 作成する保存済みのポリシーに使用する名前。

   * FileToUpload: コンテナーにアップロードされるファイルのパス。

4. プロジェクトを実行します。 SAS が生成されると、次のテキストのような情報が表示されます。

        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    SAS ポリシー トークン、ストレージ アカウント名、コンテナー名を保存します。 これらの値は、HDInsight クラスターにストレージ アカウントを関連付けるときに使用されます。

### <a name="create-a-stored-policy-and-sas-using-python"></a>Python を使用して保存済みのポリシーと SAS を作成する

1. SASToken.py ファイルを開き、次の値を変更します。

   * policy\_name: 作成する保存済みのポリシーに使用する名前。

   * storage\_account\_name: ご利用のストレージ アカウントの名前。

   * storage\_account\_key: ストレージ アカウントのキー。

   * storage\_container\_name: アクセスを制限するストレージ アカウントのコンテナー。

   * example\_file\_path: コンテナーにアップロードされるファイルのパス。

2. スクリプトを実行します。 スクリプトが完了すると、次のテキストのような SAS トークンが表示されます。

        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    SAS ポリシー トークン、ストレージ アカウント名、コンテナー名を保存します。 これらの値は、HDInsight クラスターにストレージ アカウントを関連付けるときに使用されます。

## <a name="use-the-sas-with-hdinsight"></a>HDInsight での SAS の使用

HDInsight クラスターを作成するときは、プライマリ ストレージ アカウントを指定する必要があり、任意で追加のストレージ アカウントを指定することもできます。 これらのストレージを追加する両方の方法で、使用するストレージ アカウントとコンテナーへのフル アクセスが必要です。

コンテナーへのアクセスを制限するために Shared Access Signature を使用するには、カスタム エントリをクラスターの **core-site** 構成に追加します。

* **Windows ベース**または **Linux ベース**の HDInsight クラスターでは、PowerShell を使用してクラスターを作成するときにエントリを追加できます。
* **Linux ベース** の HDInsight クラスターでは、Ambari を使用してクラスターを作成した後に構成を変更します。

### <a name="create-a-cluster-that-uses-the-sas"></a>SAS を使用するクラスターの作成

SAS を使用する HDInsight クラスターを作成する例は、リポジトリの `CreateCluster` ディレクトリにあります。 この例を使用するには、次の手順を実行してください。

1. テキスト エディターで `CreateCluster\HDInsightSAS.ps1` ファイルを開き、このドキュメントの先頭にある次の値を変更します。

    ```powershell
    # Replace 'mycluster' with the name of the cluster to be created
    $clusterName = 'mycluster'
    # Valid values are 'Linux' and 'Windows'
    $osType = 'Linux'
    # Replace 'myresourcegroup' with the name of the group to be created
    $resourceGroupName = 'myresourcegroup'
    # Replace with the Azure data center you want to the cluster to live in
    $location = 'North Europe'
    # Replace with the name of the default storage account to be created
    $defaultStorageAccountName = 'mystorageaccount'
    # Replace with the name of the SAS container created earlier
    $SASContainerName = 'sascontainer'
    # Replace with the name of the SAS storage account created earlier
    $SASStorageAccountName = 'sasaccount'
    # Replace with the SAS token generated earlier
    $SASToken = 'sastoken'
    # Set the number of worker nodes in the cluster
    $clusterSizeInNodes = 3
    ```

    たとえば、 `'mycluster'` を作成するクラスターの名前に変更します。 ストレージ アカウントと SAS トークンを作成するときに、SAS の値は、前の手順の値と一致する必要があります。

    値を変更したら、ファイルを保存します。

2. 新しい Azure PowerShell プロンプトを開きます。 Azure PowerShell に慣れていない場合、またはインストールしていない場合は、「[Azure PowerShell のインストールと構成][powershell]」を参照してください。

1. プロンプトから次のコマンドを使用して、Azure サブスクリプションを認証します。

    ```powershell
    Connect-AzAccount
    ```

    プロンプトが表示されたら、Azure サブスクリプションのアカウントを使用してサインインします。

    アカウントが複数の Azure サブスクリプションに関連付けられている場合は、`Select-AzSubscription` を使用して、使用するサブスクリプションを選択します。

4. プロンプトから、ディレクトリを HDInsightSAS.ps1 ファイルを含む `CreateCluster` ディレクトリに移動します。 次のコマンドを使用してスクリプトを実行します。

    ```powershell
    .\HDInsightSAS.ps1
    ```

    このスクリプトを実行すると、リソース グループとストレージ アカウントを作成するときに、PowerShell プロンプトにログ出力されます。 HDInsight クラスターの HTTP ユーザーを入力するように求められます。 このアカウントは、クラスターへの HTTP(S) アクセスのセキュリティを保護するために使用されます。

    Linux ベースのクラスターを作成している場合、SSH ユーザー アカウント名とパスワードを求めるメッセージが表示されます。 このアカウントは、クラスターへのリモート サインインに使用されます。

   > [!IMPORTANT]  
   > HTTP(S) または SSH のユーザー名とパスワードを求められると、次の条件を満たすパスワードを指定する必要があります。
   >
   > * 10 文字以上にする。
   > * 数字を 1 つ以上含める。
   > * 英数字以外を 1 文字以上含める。
   > * 大文字または小文字を 1 文字以上含める。

このスクリプトが完了するにはしばらく時間がかかります。通常、約 15 分です。 エラーが発生することなく、このスクリプトが完了すると、クラスターが作成されています。

### <a name="use-the-sas-with-an-existing-cluster"></a>既存のクラスターで SAS を使用する

既存の Linux ベースのクラスターがある場合は、次の手順を使用して、SAS を **core-site** 構成に追加することができます。

1. クラスターの Ambari Web UI を開きます。 このページのアドレスは https://YOURCLUSTERNAME.azurehdinsight.net です。 入力を要求されたら、クラスターを作成するときに使用した管理者名 (admin) とパスワードを使用してクラスターを認証します。

2. Ambari Web UI の左側から、**[HDFS]** を選択して、ページ中央にある **[Configs]** タブを選択します。

3. **[詳細設定]** タブをクリックし、**[カスタム core-site]** セクションが表示されるまでスクロールします。

4. **[カスタム core-site]** セクションを展開して、最後までスクロールし、**[プロパティの追加...]** リンクを選択します。 **[キー]** と **[値]** フィールドに、次の値を使用します。

   * **キー**: fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
   * **値**: 以前に実行した C# または Python アプリケーションが返した SAS

     **CONTAINERNAME** を C# または SAS のアプリケーションで使用したコンテナー名に置き換えます。 **STORAGEACCOUNTNAME** を使用したストレージ アカウント名に置き換えます。

5. **[追加]** ボタンをクリックしてこのキーと値を保存し、**[保存]** ボタンをクリックして構成の変更を保存します。 プロンプトが表示されたら、変更の説明 (「SAS ストレージ アクセスの追加」など) を追加し、**[保存]** をクリックします。

    変更が完了したら、 **[OK]** をクリックします。

   > [!IMPORTANT]  
   > 変更を有効にするには、複数のサービスを再起動する必要があります。

6. Ambari Web UI で、左側の一覧から **[HDFS]** を選択して、右側の **[サービス アクション]** ドロップダウン リストから **[すべて再起動]** を選択します。 メッセージが表示されたら、__[Confirm Restart All]\(すべての再起動の確認\)__ を選択します。

    この手順を MapReduce2 と YARN に対して繰り返します。

7. これらのサービスが再起動されたら、各サービスを選択して、 **[サービス アクション]** ドロップダウンからメンテナンス モードを無効化します。

## <a name="test-restricted-access"></a>制限付きアクセスをテストする

アクセスが制限されていることを確認するには、SSH を使用してクラスターに接続します。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

クラスターに接続したら、次の手順を使用して、SAS ストレージ アカウント上の項目を読み取りまたは一覧表示のみすることができることを確認します。

1. コンテナーの内容を一覧表示するには、プロンプトから次のコマンドを使用します。 

    ```bash
    hdfs dfs -ls wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    **SASCONTAINER** を SAS ストレージ アカウントに対して作成されたコンテナーの名前に置き換えます。 **SASACCOUNTNAME** を SAS に使用するストレージ アカウントの名前に置き換えます。

    一覧には、コンテナーと SAS が作成されたときにアップロードされたファイルも含まれます。

2. 次のコマンドを使用して、ファイルの内容を読み取ることができることを確認します。 前の手順のとおりに、**SASCONTAINER** と **SASACCOUNTNAME** を置き換えます。 **FILENAME** を前のコマンドで表示されたファイルの名前に置き換えます。

    ```bash
    hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
    ```

    このコマンドにより、ファイルの内容が一覧表示されます。

3. 次のコマンドを使用して、ローカル ファイル システムにファイルをダウンロードします。

    ```bash
    hdfs dfs -get wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
    ```

    このコマンドは、**testfile.txt** という名前のローカル ファイルにファイルをダウンロードします。

4. 次のコマンドを使用して、ローカル ファイルを SAS ストレージ上の **testupload.txt** という名前の新しいファイルにアップロードします。

    ```bash
    hdfs dfs -put testfile.txt wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    次のテキストのようなメッセージが返されます。

        put: java.io.IOException

    このエラーは、ストレージの場所が読み取りと一覧表示のみであるために発生します。 次のコマンドを使用して、書き込み可能なクラスターの既定のストレージ上にデータを置きます。

    ```bash
    hdfs dfs -put testfile.txt wasb:///testupload.txt
    ```

    このとき、操作が正常に完了する必要があります。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="a-task-was-canceled"></a>タスクはキャンセルされました

**現象**: PowerShell スクリプトを使用してクラスターを作成するときに、次のエラー メッセージが表示される場合があります。

    New-AzHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

**原因**: クラスターの管理者または HTTP ユーザー、または (Linux ベースのクラスターの場合) SSH ユーザーのパスワードを使用する場合に、このエラーが発生する可能性があります。

**解決策**: 次の条件を満たすパスワードを使用します。

* 10 文字以上にする。
* 数字を 1 つ以上含める。
* 英数字以外を 1 文字以上含める。
* 大文字または小文字を 1 文字以上含める。

## <a name="next-steps"></a>次の手順

HDInsight クラスターにアクセスが制限されたストレージを追加する方法を学習しました。クラスターのデータと連携するその他の方法を確認してください。

* [HDInsight での Apache Hive の使用](hadoop/hdinsight-use-hive.md)
* [HDInsight での Apache Pig の使用](hadoop/hdinsight-use-pig.md)
* [HDInsight での MapReduce の使用](hadoop/hdinsight-use-mapreduce.md)

[powershell]: /powershell/azureps-cmdlets-docs
