---
title: HDInsight に Azure Storage アカウントを追加する
description: 既存の HDInsight クラスターに Azure Storage アカウントを追加する方法について説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/27/2020
ms.openlocfilehash: 32757fef131c5e443350f032c0ac987d7f491396
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864314"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>HDInsight にストレージ アカウントを追加する

HDInsight に Azure Storage "*アカウント*" を追加するためにスクリプト アクションを使用する方法について説明します。 このドキュメントの手順では、既存の HDInsight クラスターにストレージ "*アカウント*" を追加します。 この記事は、ストレージ "*アカウント*" (既定のクラスター ストレージ アカウントではない) に適用されます。[`Azure Data Lake Storage Gen1`](hdinsight-hadoop-use-data-lake-storage-gen1.md) や [`Azure Data Lake Storage Gen2`](hdinsight-hadoop-use-data-lake-storage-gen2.md) など、追加のストレージには適用されません。

> [!IMPORTANT]  
> このドキュメントでは、クラスターの作成後に、そのクラスターにストレージ アカウントを追加する方法を取り上げています。 クラスター作成時にストレージ アカウントを追加する方法については、「[Apache Hadoop、Apache Spark、Apache Kafka などの HDInsight クラスターをセットアップする](hdinsight-hadoop-provision-linux-clusters.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

* HDInsight 上の Hadoop クラスター。 [Linux での HDInsight の概要](./hadoop/apache-hadoop-linux-tutorial-get-started.md)に関するページを参照してください。
* ストレージ アカウントの名前とキー。 「[ストレージ アカウント アクセス キーを管理する](../storage/common/storage-account-keys-manage.md)」をご覧ください。
* PowerShell を使用する場合は、AZ モジュールが必要です。  「[Azure PowerShell の概要](/powershell/azure/)」を参照してください。

## <a name="how-it-works"></a>しくみ

処理中、スクリプトでは、次のアクションが実行されます。

* クラスターの core-site.xml 構成にストレージ アカウントが既に存在する場合は、スクリプトが終了し、それ以降のアクションは実行されません。

* ストレージ アカウントが存在し、キーを使用してアクセスできることを確認します。

* クラスターの資格情報を使用して、キーを暗号化します。

* core-site.xml ファイルにストレージ アカウントを追加します。

* Apache Oozie、Apache Hadoop YARN、Apache Hadoop MapReduce2、Apache Hadoop HDFS サービスを停止して再起動します。 これらのサービスを停止して再起動することで、新しいストレージ アカウントを使用できるようになります。

> [!WARNING]  
> HDInsight クラスター以外の場所でストレージ アカウントを使用することはできません。

## <a name="add-storage-account"></a>ストレージ アカウントの追加

[スクリプトアクション](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster) を使用して、次の点を考慮して変更を適用します。

|プロパティ | 値 |
|---|---|
|Bash スクリプト URI|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`|
|ノードの種類|Head|
|パラメーター|`ACCOUNTNAME` `ACCOUNTKEY` `-p` (省略可)|

* `ACCOUNTNAME` は HDInsight クラスターに追加するストレージ アカウントの名前です。
* `ACCOUNTKEY` は `ACCOUNTNAME` のアクセス キーです。
* `-p` はオプションです。 指定した場合、キーは暗号化されず、プレーンテキストとして core-site.xml ファイルに格納されます。

## <a name="verification"></a>検証

Azure portal で HDInsight クラスターを表示しているときに、 __[プロパティ]__ の下の __[ストレージ アカウント]__ エントリを選択すると、このスクリプト アクションを通じて追加したストレージ アカウントは表示されません。 Azure PowerShell と Azure CLI でも、追加のストレージ アカウントは表示されません。 ストレージ情報が表示されないのは、このスクリプトがクラスターの `core-site.xml` 構成のみを変更するためです。 この情報は、Azure 管理 API を使用してクラスター情報を取得するときには使用されません。

追加のストレージを確認するには、次に示す方法のいずれかを使用します。

### <a name="powershell"></a>PowerShell

このスクリプトは、指定されたクラスターに関連付けられているストレージ アカウント名を返します。 `CLUSTERNAME` を実際のクラスター名に置き換えてから、このスクリプトを実行してください。

```powershell
# Update values
$clusterName = "CLUSTERNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

$clusterName = $clusterName.ToLower();

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content

# extract account names
$value = ($respObj.items.configurations | Where type -EQ "core-site").properties | Get-Member -membertype properties | Where Name -Like "fs.azure.account.key.*"
foreach ($name in $value ) { $name.Name.Split(".")[4]}
```

### <a name="apache-ambari"></a>Apache Ambari

1. Web ブラウザーから、`https://CLUSTERNAME.azurehdinsight.net` に移動します。ここで、`CLUSTERNAME` はクラスターの名前です。

1. **[HDFS]** 、 **[構成]** 、 **[詳細]** 、 **[カスタム core-site]** の順に移動します。

1. `fs.azure.account.key` で始まるキーを確認します。 このサンプル イメージに示すように、アカウント名はキーの一部になります。

   :::image type="content" source="./media/hdinsight-hadoop-add-storage/apache-ambari-verification.png" alt-text="Apache Ambari による検証":::

## <a name="remove-storage-account"></a>ストレージ アカウントの削除

1. Web ブラウザーから、`https://CLUSTERNAME.azurehdinsight.net` に移動します。ここで、`CLUSTERNAME` はクラスターの名前です。

1. **[HDFS]** 、 **[構成]** 、 **[詳細]** 、 **[カスタム core-site]** の順に移動します。

1. 次のキーを削除します。
    * `fs.azure.account.key.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`
    * `fs.azure.account.keyprovider.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`

これらのキーを削除して構成を保存したら、Oozie、Yarn、MapReduce2、HDFS、Hive を 1 つずつ再起動する必要があります。

## <a name="known-issues"></a>既知の問題

### <a name="storage-firewall"></a>ストレージ ファイアウォール

**[選択されたネットワーク]** で **[ファイアウォールと仮想ネットワーク]** に関する制限を使用してストレージ アカウントをセキュリティで保護するように選択した場合、 **[信頼された Microsoft サービスによる...]** の例外を有効にして、HDInsight でストレージ アカウントにアクセスできるようにしてください。`.`

### <a name="unable-to-access-storage-after-changing-key"></a>キーの変更後にストレージにアクセスできない

ストレージ アカウントのキーを変更すると、HDInsight はストレージ アカウントにアクセスできなくなります。 HDInsight は、クラスターの core-site.xml 内のキャッシュされたキーのコピーを使用します。 このキャッシュされたコピーは、新しいキーに一致するように更新する必要があります。

スクリプト アクションを再実行しても、キーは **更新されません**。スクリプトで、ストレージ アカウントのエントリが既に存在するかどうかが確認されます。 エントリが既に存在する場合、いかなる変更もしません。

この問題を回避するには、次のようにします。  
1. ストレージ アカウントを削除します。
1. ストレージ アカウントを追加します。

> [!IMPORTANT]  
> クラスターに接続されているプライマリ ストレージ アカウントのストレージ キーのローテーションはサポートされていません。

## <a name="next-steps"></a>次のステップ

既存の HDInsight クラスターにストレージ アカウントを追加する方法について説明しました。 スクリプト アクションの詳細については、「[スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」を参照してください。