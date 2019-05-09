---
title: Azure Data Lake Storage Gen2 で HDFS CLI を使用する
description: Data Lake Storage Gen2 の HDFS CLI の概要
services: storage
author: artemuwka
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: artek
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 051681150501f7c5737f335f8eb48144b08bb990
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58482668"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Data Lake Storage Gen2 で HDFS CLI を使用する

Azure Data Lake Storage Gen2 では、[Hadoop 分散ファイル システム (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) を利用する場合と同様に、データの管理およびアクセスを可能にします。 HDInsight クラスターをアタッチする場合でも、Azure Databricks を使用して Apache Spark ジョブを実行し、Azure Storage アカウントに保存されているデータを分析する場合でも、コマンド ライン インターフェイス (CLI) を使用して、読み込まれたデータを取得したり、操作したりできます。

## <a name="hdfs-cli-with-hdinsight"></a>HDInsight での HDFS CLI

HDInsight では、それぞれのコンピューティング ノードにローカルに割り当てられている分散ファイル システムにアクセスします。 このファイル システムにアクセスするには、Hadoop でサポートされる HDFS などのファイル システムと直接対話するシェルを使用する必要があります。 よく使用されるコマンドと、役立つリソースへのリンクを以下に示します。

>[!IMPORTANT]
>HDInsight クラスターの課金は、クラスターが作成された後に開始し、クラスターが削除されると停止します。 課金は分単位なので、クラスターを使わなくなったら必ず削除してください。 クラスターを削除する方法については、[トピックに関する記事](../../hdinsight/hdinsight-delete-cluster.md)を参照してください。 ただし、HDInsight クラスターを削除しても、Data Lake Storage Gen2 が使用可能なストレージ アカウントに保存されているデータは削除されません。

### <a name="create-a-file-system"></a>ファイル システムを作成する

    hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/

* `<file-system-name>` プレースホルダーを、ファイル システムに付ける名前に置き換えます。

* `<storage-account-name>` プレースホルダーは、実際のストレージ アカウントの名前に置き換えます。

### <a name="get-a-list-of-files-or-directories"></a>ファイルまたはディレクトリの一覧を取得する

    hdfs dfs -ls <path>

`<path>` プレースホルダーを、ファイル システムまたはファイル システム フォルダーの URI に置き換えます。

次に例を示します。`hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

### <a name="create-a-directory"></a>ディレクトリを作成する

    hdfs dfs -mkdir [-p] <path>

`<path>` プレースホルダーを、ルート ファイル システム名またはファイル システム内のフォルダーに置き換えます。

次に例を示します。`hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

### <a name="delete-a-file-or-directory"></a>ファイルまたはディレクトリを削除する

    hdfs dfs -rm <path>

`<path>` プレースホルダーを、削除するファイルまたはフォルダーの URI に置き換えます。

次に例を示します。`hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

### <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>Linux の HDInsight Hadoop クラスターで HDFS CLI を使用する

最初に、[サービスへのリモート アクセス](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services)を確立します。 [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) を選択した場合、PowerShell のサンプル コードは、次のようになります。

```powershell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
接続文字列は、Azure portal 内の HDInsight クラスター ブレードの "SSH + Cluster login" セクションで確認できます。 SSH 資格情報は、クラスターの作成時に指定されています。

HDFS CLI の詳細については、[公式ドキュメント](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html)、および「[HDFS Permissions Guide](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)」 (HDFS 権限ガイド) を参照してください。 Databricks の ACL の詳細については、「[Secrets CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#secrets-cli)」を参照してください。

## <a name="hdfs-cli-with-azure-databricks"></a>Azure Databricks での HDFS CLI

Databricks は、Databricks REST API 上に構築された使いやすい CLI を備えています。 このオープンソース プロジェクトは、[GitHub](https://github.com/databricks/databricks-cli) 上でホストされています。 よく使用されるコマンドを次に示します。

### <a name="get-a-list-of-files-or-directories"></a>ファイルまたはディレクトリの一覧を取得する

    dbfs ls [-l]

### <a name="create-a-directory"></a>ディレクトリを作成する

    dbfs mkdirs

### <a name="delete-a-file"></a>ファイルを削除する

    dbfs rm [-r]

Databricks と対話するもう 1 つの方法は、ノートブックです。 ノートブックには 1 次言語がありますが、セルの先頭で言語のマジック コマンド %language を指定することにより、複数の言語を混在させることができます。 特に、%sh を使用すると、前述した HDInsight の例の場合と同様に、ノートブックでシェル コードを実行できます。

### <a name="get-a-list-of-files-or-directories"></a>ファイルまたはディレクトリの一覧を取得する

    %sh ls <args>

### <a name="create-a-directory"></a>ディレクトリを作成する

    %sh mkdir [-p] <paths>

### <a name="delete-a-file-or-a-directory"></a>ファイルまたはディレクトリを削除する

    %sh rm [-skipTrash] URI [URI ...]

Azure Databricks で Spark クラスターを起動した後、新しいノートブックを作成します。 ノートブックのスクリプトの例は、次のようになります。

    #Execute basic HDFS commands invoking the shell. Display the hierarchy.
    %sh ls /
    #Create a sample directory.
    %sh mkdir /samplefolder
    #Get the ACL of the newly created directory.
    hdfs dfs -getfacl /samplefolder

Databricks CLI の詳細については、[公式ドキュメント](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)を参照してください。 ノートブックの詳細については、このドキュメントの「[notebooks](https://docs.azuredatabricks.net/user-guide/notebooks/index.html)」 (ノートブック) セクションを参照してください。

## <a name="set-file-and-directory-level-permissions"></a>ファイルとディレクトリ レベルのアクセス許可を設定する

ファイルとディレクトリ レベルでアクセス許可を設定および取得します。 手始めとして、いくつかのコマンドを次に示します。 

Azure Data Lake Gen2 ファイルシステムのファイルとディレクトリ レベルのアクセス許可の詳細については、「[Access control in Azure Data Lake Storage Gen2](storage-data-lake-storage-access-control.md)」 (Azure Data Lake Storage Gen2 のアクセス制御) を参照してください。

### <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>ファイルとディレクトリのアクセス制御リスト (ACL) を表示する

    hdfs dfs -getfacl [-R] <path>

例:

`hdfs dfs -getfacl -R /dir`

「[getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)」を参照

### <a name="set-acls-of-files-and-directories"></a>ファイルとディレクトリの ACL を設定する

    hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]

例:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

「[setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)」を参照

### <a name="change-the-owner-of-files"></a>ファイルの所有者を変更する

    hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>

「[chown](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)」を参照

### <a name="change-group-association-of-files"></a>ファイルのグループの関連付けを変更する

    hdfs dfs -chgrp [-R] <group> <URI>

「[chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)」を参照

### <a name="change-the-permissions-of-files"></a>ファイルのアクセス許可を変更する

    hdfs dfs -chmod [-R] <mode> <URI>

「[chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)」を参照

コマンドの完全な一覧は、[Apache Hadoop 2.4.1 ファイル システム シェル ガイド](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html)の Web サイトでご覧になれます。

## <a name="next-steps"></a>次の手順

[Use an Azure Data Lake Storage Gen2 capable account in Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md) (Azure Databricks で Azure Data Lake Storage Gen2 対応アカウントを使用する) 
