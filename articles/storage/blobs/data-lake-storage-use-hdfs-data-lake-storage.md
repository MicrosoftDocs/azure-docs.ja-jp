---
title: Azure Data Lake Storage Gen2 で HDFS CLI を使用する
description: Data Lake Storage Gen2 の HDFS CLI の概要
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: artek
ms.openlocfilehash: 1d5313f3f0fff128dd09f9c9857b7dd9921ea4f8
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992217"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Data Lake Storage Gen2 で HDFS CLI を使用する

ストレージ アカウント内のデータにアクセスして管理するには、[Hadoop 分散ファイル システム (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) の場合と同様にコマンド ライン インターフェイスを使用します。 この記事では、作業を始めるのに役立つ例をいくつか示します。

HDInsight では、それぞれのコンピューティング ノードにローカルに割り当てられている分散コンテナーにアクセスできます。 このコンテナーにアクセスするには、Hadoop でサポートされる HDFS などのファイル システムと直接対話するシェルを使用する必要があります。

HDFS CLI の詳細については、[公式ドキュメント](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html)、および「[HDFS Permissions Guide (HDFS 権限ガイド)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)」を参照してください。

>[!NOTE]
>HDInsight の代わりに Azure Databricks を使用しており、コマンド ライン インターフェイスを使用してデータを操作したい場合は、Databricks CLI を使用して Databricks ファイル システムを操作してください。 「[Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)」を参照してください。

## <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>Linux の HDInsight Hadoop クラスターで HDFS CLI を使用する

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

>[!IMPORTANT]
>HDInsight クラスターの課金は、クラスターが作成された後に開始し、クラスターが削除されると停止します。 課金は分単位なので、クラスターを使わなくなったら必ず削除してください。 クラスターを削除する方法については、[トピックに関する記事](../../hdinsight/hdinsight-delete-cluster.md)を参照してください。 ただし、HDInsight クラスターを削除しても、Data Lake Storage Gen2 が使用可能なストレージ アカウントに保存されているデータは削除されません。

## <a name="create-a-container"></a>コンテナーを作成する

    hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/

* `<container-name>` プレースホルダーを、ご自身のコンテナーに付ける名前に置き換えます。

* `<storage-account-name>` プレースホルダーは、実際のストレージ アカウントの名前に置き換えます。

## <a name="get-a-list-of-files-or-directories"></a>ファイルまたはディレクトリの一覧を取得する

    hdfs dfs -ls <path>

`<path>` プレースホルダーを、コンテナーまたはコンテナー フォルダーの URI に置き換えます。

次に例を示します。`hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

## <a name="create-a-directory"></a>ディレクトリを作成する

    hdfs dfs -mkdir [-p] <path>

`<path>` プレースホルダーを、ルート コンテナー名またはお使いのコンテナー内のフォルダーに置き換えます。

次に例を示します。`hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

## <a name="delete-a-file-or-directory"></a>ファイルまたはディレクトリを削除する

    hdfs dfs -rm <path>

`<path>` プレースホルダーを、削除するファイルまたはフォルダーの URI に置き換えます。

次に例を示します。`hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

## <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>ファイルとディレクトリのアクセス制御リスト (ACL) を表示する

    hdfs dfs -getfacl [-R] <path>

例:

`hdfs dfs -getfacl -R /dir`

「[getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)」を参照

## <a name="set-acls-of-files-and-directories"></a>ファイルとディレクトリの ACL を設定する

    hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]

例:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

「[setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)」を参照

## <a name="change-the-owner-of-files"></a>ファイルの所有者を変更する

    hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>

「[chown](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)」を参照

## <a name="change-group-association-of-files"></a>ファイルのグループの関連付けを変更する

    hdfs dfs -chgrp [-R] <group> <URI>

「[chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)」を参照

## <a name="change-the-permissions-of-files"></a>ファイルのアクセス許可を変更する

    hdfs dfs -chmod [-R] <mode> <URI>

「[chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)」を参照

コマンドの完全な一覧は、[Apache Hadoop 2.4.1 ファイル システム シェル ガイド](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html)の Web サイトでご覧になれます。

## <a name="next-steps"></a>次の手順

* [Use an Azure Data Lake Storage Gen2 capable account in Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md) (Azure Databricks で Azure Data Lake Storage Gen2 対応アカウントを使用する)

* [ファイルおよびディレクトリに対するアクセス制御リストについて学習する](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
