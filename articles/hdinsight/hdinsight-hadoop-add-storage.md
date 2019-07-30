---
title: HDInsight に Azure ストレージ アカウントを追加する
description: 既存の HDInsight クラスターに Azure ストレージ アカウントを追加する方法について説明します。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: hrasheed
ms.openlocfilehash: 8a844465f7ba2222acd7efaf100c7b682c15adb2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67433512"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>HDInsight にストレージ アカウントを追加する

HDInsight に Azure ストレージ *アカウント*を追加するためにスクリプト アクションを使用する方法について説明します。 このドキュメントの手順では、既存の Linux ベースの HDInsight クラスターにストレージ *アカウント*を追加します。 この記事は、ストレージ *アカウント* (既定のクラスター ストレージ アカウントではなく) に適用されます。[Azure Data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-store.md) および [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md) などの追加ストレージには適用されません。

> [!IMPORTANT]  
> このドキュメントでは、クラスターの作成後に、そのクラスターにストレージを追加する方法を取り上げています。 クラスター作成時にストレージ アカウントを追加する方法については、「[Apache Hadoop、Apache Spark、Apache Kafka などの HDInsight クラスターをセットアップする](hdinsight-hadoop-provision-linux-clusters.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

* HDInsight 上の Hadoop クラスター。 [Linux での HDInsight の概要](./hadoop/apache-hadoop-linux-tutorial-get-started.md)に関するページを参照してください。
* ストレージ アカウントの名前とキー。 「[Azure portal でストレージ アカウント設定を管理する](../storage/common/storage-account-manage.md)」を参照してください。
* [大文字と小文字が正しく区別されたクラスター名](hdinsight-hadoop-manage-ambari-rest-api.md#identify-correctly-cased-cluster-name)。
* PowerShell を使用する場合は、AZ モジュールが必要です。  「[Azure PowerShell の概要](https://docs.microsoft.com/powershell/azure/overview)」を参照してください。
* Azure CLI をインストールしていない場合は、「[Azure コマンド ライン インターフェイス (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)」を参照してください。
* Bash または Windows コマンド プロンプトを使用している場合、**jq** (コマンド ライン JSON プロセッサ) も必要になります。  [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/) をご覧ください。 Windows 10 での Ubuntu の Bash については、「[Windows Subsystem for Linux Installation Guide for Windows 10](https://docs.microsoft.com/windows/wsl/install-win10)」(Windows 10 用 Windows Subsystem for Linux インストール ガイド) を参照してください。

## <a name="how-it-works"></a>動作のしくみ

このスクリプトは、次のパラメーターを受け取ります。

* __Azure ストレージ アカウント名__: HDInsight クラスターに追加するストレージ カウントの名前。 スクリプトの実行後は、HDInsight は、このストレージ アカウントに格納されているデータの読み書きができます。

* __Azure ストレージ アカウント キー__: ストレージ アカウントへのアクセスを許可するキー。

* __-p__ (省略可能): 指定した場合、キーは暗号化されず、プレーンテキストとして core-site.xml ファイルに格納されます。

このスクリプトは、処理中に次のアクションを実行します。

* クラスターの core-site.xml 構成にストレージ アカウントが既に存在する場合は、スクリプトが終了し、それ以降の操作は実行されません。

* ストレージ アカウントが存在し、キーを使用してアクセスできることを確認します。

* クラスターの資格情報を使用して、キーを暗号化します。

* core-site.xml ファイルにストレージ アカウントを追加します。

* [Apache Oozie](https://oozie.apache.org/)、 [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)、 [Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html)、および[Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html)サービスを停止して再起動します。 これらのサービスを停止して再起動することで、新しいストレージ アカウントを使用できるようになります。

> [!WARNING]  
> HDInsight クラスター以外の場所でストレージ アカウントを使用することはできません。

## <a name="the-script"></a>スクリプト

__スクリプトの場所__: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__要件__:スクリプトを __ヘッド ノード__ に適用する必要があります。 このスクリプトは、クラスターの Ambari 構成を直接更新するため、__Persisted__ としてマークする必要はありません。

## <a name="to-use-the-script"></a>スクリプトを使用するには

このスクリプトは、Azure PowerShell、Azure CLI、Azure portal で使用できます。

### <a name="powershell"></a>PowerShell

[Submit-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/submit-azhdinsightscriptaction) を使用します。 `CLUSTERNAME`、`ACCOUNTNAME`、および `ACCOUNTKEY` を適切な値に置き換えます。

```powershell
# Update these parameters
$clusterName = "CLUSTERNAME"
$parameters = "ACCOUNTNAME ACCOUNTKEY"

$scriptActionName = "addStorage"
$scriptActionUri = "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh"

# Execute script
Submit-AzHDInsightScriptAction `
    -ClusterName $clusterName `
    -Name $scriptActionName `
    -Uri $scriptActionUri `
    -NodeTypes "headnode" `
    -Parameters $parameters
```

### <a name="azure-cli"></a>Azure CLI

[az hdinsight script-action execute](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute) を使用します。  `CLUSTERNAME`、`RESOURCEGROUP`、 `ACCOUNTNAME`、および `ACCOUNTKEY` を適切な値に置き換えます。

```cli
az hdinsight script-action execute ^
    --name CLUSTERNAME ^
    --resource-group RESOURCEGROUP ^
    --roles headnode ^
    --script-action-name addStorage ^
    --script-uri "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh" ^
    --script-parameters "ACCOUNTNAME ACCOUNTKEY"
```

### <a name="azure-portal"></a>Azure ポータル

「[実行中のクラスターにスクリプト アクションを適用する](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)」を参照してください。

## <a name="known-issues"></a>既知の問題

### <a name="storage-firewall"></a>ストレージ ファイアウォール

**[選択されたネットワーク]** で **[ファイアウォールと仮想ネットワーク]** に関する制限を使用してストレージ アカウントをセキュリティで保護する場合、 **[信頼された Microsoft サービスによる ... を許可します]** の例外を有効にして、HDInsight ストレージ アカウントにアクセスできるようにしてください。

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>ストレージ アカウントが Azure Portal またはツールに表示されない

Azure Portal で HDInsight クラスターを表示しているときに、 __[プロパティ]__ の下の __[ストレージ アカウント]__ エントリを選択すると、このスクリプト アクションを通じて追加したストレージ アカウントは表示されません。 Azure PowerShell と Azure CLI でも、追加のストレージ アカウントは表示されません。

ストレージ情報が表示されないのは、スクリプトがクラスターの core-site.xml 構成を変更するだけだからです。 この情報は、Azure 管理 API を使用してクラスター情報を取得するときには使用されません。

このスクリプトを使用してクラスターに追加されたストレージ アカウント情報を表示するには、Ambari REST API を使用します。 ご使用のクラスターについてこの情報を取得するには、次のコマンドを使用します。

### <a name="powershell"></a>PowerShell

`CLUSTERNAME` を大文字と小文字が正しく区別されたクラスター名に置き換えます。 最初に以下のコマンドを入力して、使われているサービス構成バージョンを識別します。

```powershell
# getting service_config_version
$clusterName = "CLUSTERNAME"

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version
```

`ACCOUNTNAME` を実際の名前に置き換えます。 次に、`4` を実際のサービス構成バージョンに置き換え、コマンドを入力します。 入力を求められたら、クラスター ログイン パスワードを入力します。

```powershell
# Update values
$accountName = "ACCOUNTNAME"
$version = 4

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$version" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$accountName.blob.core.windows.net"
```

### <a name="bash"></a>Bash
`myCluster` を大文字と小文字が正しく区別されたクラスター名に置き換えます。

```bash
export CLUSTERNAME='myCluster'

curl --silent -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" \
| jq ".Clusters.desired_service_config_versions.HDFS[].service_config_version" 
```

`myAccount` を実際のストレージ アカウント名に置き換えます。 次に、`4` を実際のサービス構成バージョンに置き換え、コマンドを入力します。

```bash
export ACCOUNTNAME='"fs.azure.account.key.myAccount.blob.core.windows.net"'
export VERSION='4'

curl --silent -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=$VERSION" \
| jq ".items[].configurations[].properties[$ACCOUNTNAME] | select(. != null)"
```

### <a name="cmd"></a>cmd

両方のスクリプトで、`CLUSTERNAME` を大文字と小文字が正しく区別されたクラスター名に置き換えます。 最初に以下のコマンドを入力して、使われているサービス構成バージョンを識別します。

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" | ^
jq-win64 ".Clusters.desired_service_config_versions.HDFS[].service_config_version" 
```

`ACCOUNTNAME` を実際のストレージ アカウント名に置き換えます。 次に、`4` を実際のサービス構成バージョンに置き換え、コマンドを入力します。

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=4" | ^
jq-win64 ".items[].configurations[].properties["""fs.azure.account.key.ACCOUNTNAME.blob.core.windows.net"""] | select(. != null)"
```
---

 このコマンドから返される情報は、次のテキストに似たものとなります。

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

このテキストは、ストレージ アカウントへのアクセスに使用される暗号化キーの例です。

### <a name="unable-to-access-storage-after-changing-key"></a>キーの変更後にストレージにアクセスできない

ストレージ アカウントのキーを変更すると、HDInsight はストレージ アカウントにアクセスできなくなります。 HDInsight は、クラスターの core-site.xml 内のキャッシュされたキーのコピーを使用します。 このキャッシュされたコピーは、新しいキーに一致するように更新する必要があります。

スクリプト アクションを再実行しても、キーは __更新されません__。スクリプトはストレージ アカウントのエントリが既に存在するかどうかを確認します。 エントリが既に存在する場合、いかなる変更もしません。

この問題を回避するには、ストレージ アカウントの既存のエントリを削除する必要があります。 既存のエントリを削除するには、次の手順を実行します。

> [!IMPORTANT]  
> クラスターに接続されているプライマリ ストレージ アカウントのストレージ キーのローテーションはサポートされていません。

1. Web ブラウザーで、HDInsight クラスターの Ambari Web UI を開きます。 URI は `https://CLUSTERNAME.azurehdinsight.net` です。 `CLUSTERNAME` をクラスターの名前に置き換えます。

    プロンプトが表示されたら、クラスターの HTTP ログイン ユーザー名とパスワードを入力します。

2. ページの左側のサービスの一覧で、 __[HDFS]__ を選択します。 ページの中央の __[Configs (構成)]__ タブを選択します。

3. __[Filter... (フィルター...)]__ フィールドに __fs.azure.account__ の値を入力します。 これで、クラスターに追加されたすべてのストレージ アカウントのエントリが返されます。 エントリには、__keyprovider__ と __key__ という 2 つの種類があります。 両方に、キー名の一部としてストレージ アカウントの名前が含まれています。

    以下に示すのは、__mystorage__ という名前のストレージ アカウントのエントリの例です。

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. 削除する必要があるストレージ アカウントのキーを特定した後、エントリの右側にある赤い "-" アイコンを使用してエントリを削除します。 次に、 __[Save (保存)]__ ボタンを使用して、変更を保存します。

5. 変更が保存された後、スクリプト アクションを使用してストレージ アカウントと新しいキー値をクラスターに追加します。

### <a name="poor-performance"></a>パフォーマンスが低い

ストレージ アカウントが HDInsight クラスターとは異なるリージョンにある場合は、パフォーマンスが低下することがあります。 別のリージョンのデータにアクセスすると、そのリージョンの Azure データ センター外にネットワーク トラフィックが送信され、パブリック インターネットを経由するため、遅延が生じる場合があります。

### <a name="additional-charges"></a>追加料金が発生する

ストレージ アカウントが HDInsight クラスターとは異なるリージョンにある場合は、Azure の課金にエグレス料金が追加されていることがあります。 データが地域データ センターを離れると、エグレス料金が適用されます。 この料金は、トラフィックが他のリージョンの別の Azure データ センター宛てであっても適用されます。

## <a name="next-steps"></a>次の手順

既存の HDInsight クラスターにストレージ アカウントを追加する方法について説明しました。 スクリプト アクションの詳細については、「[スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」を参照してください。
