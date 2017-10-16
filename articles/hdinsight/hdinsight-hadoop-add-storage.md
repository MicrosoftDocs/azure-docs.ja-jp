---
title: "HDInsight に Azure ストレージ アカウントを追加する | Microsoft Docs"
description: "既存の HDInsight クラスターに Azure ストレージ アカウントを追加する方法について説明します。"
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/04/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 0853e8605e07c28867676e9c13b89263ade67c88
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>HDInsight にストレージ アカウントを追加する

HDInsight に Azure ストレージ アカウントを追加するためにスクリプト アクションを使用する方法について説明します。 このドキュメントの手順では、既存の Linux ベースの HDInsight クラスターにストレージ アカウントを追加します。

> [!IMPORTANT]
> このドキュメントでは、クラスターの作成後に、そのクラスターにストレージを追加する方法を取り上げています。 クラスター作成時にストレージ アカウントを追加する方法については、「[Hadoop、Spark、Kafka などの HDInsight クラスターをセットアップする](hdinsight-hadoop-provision-linux-clusters.md)」をご覧ください。

## <a name="how-it-works"></a>動作のしくみ

このスクリプトは、次のパラメーターを受け取ります。

* __Azure ストレージ アカウント名__: HDInsight クラスターに追加するストレージ カウントの名前。 スクリプトの実行後は、HDInsight は、このストレージ アカウントに格納されているデータの読み書きができます。

* __Azure ストレージ アカウント キー__: ストレージ アカウントへのアクセスを許可するキー。

* __-p__ (省略可能): 指定した場合、キーは暗号化されず、プレーン テキストとして core-site.xml ファイルに格納されます。

このスクリプトは、処理中に次のアクションを実行します。

* クラスターの core-site.xml 構成にストレージ アカウントが既に存在する場合は、スクリプトが終了し、それ以降の操作は実行されません。

* ストレージ アカウントが存在し、キーを使用してアクセスできることを確認します。

* クラスターの資格情報を使用して、キーを暗号化します。

* core-site.xml ファイルにストレージ アカウントを追加します。

* Oozie、YARN、MapReduce2、および HDFS の各サービスを停止して再起動します。 これらのサービスを停止して再起動することで、新しいストレージ アカウントを使用できるようになります。

> [!WARNING]
> HDInsight クラスター以外の場所でストレージ アカウントを使用することはできません。

## <a name="the-script"></a>スクリプト

__スクリプトの場所__: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__要件__:

* スクリプトを__ヘッド ノード__に適用する必要があります。

## <a name="to-use-the-script"></a>スクリプトを使用するには

このスクリプトは、Azure Portal、Azure PowerShell、または Azure CLI 1.0 で使用できます。 詳しくは、「[Script Action を使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)」を参照してください。

> [!IMPORTANT]
> カスタマイズのドキュメントに記載された手順を使用する場合は、次の情報を使用してこのスクリプトを適用します。
>
> * サンプル スクリプトのアクション URI を、このスクリプトの URI (https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh) に置き換えます。
> * 例として使用されているすべてのパラメーターを、クラスターに追加するストレージ アカウントの Azure ストレージ アカウント名とキーに置き換えます。 Azure Portal を使用している場合は、これらのパラメーターをスペースで区切る必要があります。
> * このスクリプトは、クラスターの Ambari 構成を直接更新するため、__Persisted__ としてマークする必要はありません。

## <a name="known-issues"></a>既知の問題

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>ストレージ アカウントが Azure Portal またはツールに表示されない

Azure Portal で HDInsight クラスターを表示しているときに、__[プロパティ]__ の下の __[ストレージ アカウント]__ エントリを選択すると、このスクリプト アクションを通じて追加したストレージ アカウントは表示されません。 Azure PowerShell と Azure CLI でも、追加のストレージ アカウントは表示されません。

ストレージ情報が表示されないのは、スクリプトがクラスターの core-site.xml 構成を変更するだけだからです。 この情報は、Azure 管理 API を使用してクラスター情報を取得するときには使用されません。

このスクリプトを使用してクラスターに追加されたストレージ アカウント情報を表示するには、Ambari REST API を使用します。 ご使用のクラスターについてこの情報を取得するには、次のコマンドを使用します。

```PowerShell
$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$storageAccountName.blob.core.windows.net"
```

> [!NOTE]
> `$clusterName` には、HDInsight クラスターの名前を設定します。 `$storageAccountName` には、ストレージ アカウントの名前を設定します。 プロンプトが表示されたら、クラスターのログイン (管理者) とパスワードを入力します。

```Bash
curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.azure.account.key.$STORAGEACCOUNTNAME.blob.core.windows.net"] | select(. != null)'
```

> [!NOTE]
> `$PASSWORD` には、クラスター ログイン (管理者) アカウントのパスワードを設定します。 `$CLUSTERNAME` には、HDInsight クラスターの名前を設定します。 `$STORAGEACCOUNTNAME` には、ストレージ アカウントの名前を設定します。
>
> この例では、[curl (http://curl.haxx.se/)](http://curl.haxx.se/) と [jq (https://stedolan.github.io/jq/)](https://stedolan.github.io/jq/) を使用して、JSON データを取得して解析します。

このコマンドを使用するときは、__CLUSTERNAME__ を HDInsight クラスターの名前に置き換えます。 __PASSWORD__ は、クラスターの HTTP ログイン パスワードに置き換えます。 __STORAGEACCOUNT__ は、スクリプト アクションを使って追加されたストレージ アカウントの名前に置き換えます。 このコマンドから返される情報は、次のテキストに似たものとなります。

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

このテキストは、ストレージ アカウントへのアクセスに使用される暗号化キーの例です。

### <a name="unable-to-access-storage-after-changing-key"></a>キーの変更後にストレージにアクセスできない

ストレージ アカウントのキーを変更すると、HDInsight はストレージ アカウントにアクセスできなくなります。 HDInsight は、クラスターの core-site.xml 内のキャッシュされたキーのコピーを使用します。 このキャッシュされたコピーは、新しいキーに一致するように更新する必要があります。

スクリプト アクションを再実行しても、キーは__更新されません__。スクリプトはストレージ アカウントのエントリが既に存在するかどうかを確認します。 エントリが既に存在する場合、いかなる変更もしません。

この問題を回避するには、ストレージ アカウントの既存のエントリを削除する必要があります。 既存のエントリを削除するには、次の手順を実行します。

1. Web ブラウザーで、HDInsight クラスターの Ambari Web UI を開きます。 URI は https://CLUSTERNAME.azurehdinsight.net です。 __CLUSTERNAME__ をクラスターの名前に置き換えます。

    プロンプトが表示されたら、クラスターの HTTP ログイン ユーザー名とパスワードを入力します。

2. ページの左側のサービスの一覧で、__[HDFS]__ を選択します。 ページの中央の __[Configs (構成)]__ タブを選択します。

3. __[Filter... (フィルター...)]__ フィールドに __fs.azure.account__ の値を入力します。 これで、クラスターに追加されたすべてのストレージ アカウントのエントリが返されます。 エントリには、__keyprovider__ と __key__ という 2 つの種類があります。 両方に、キー名の一部としてストレージ アカウントの名前が含まれています。

    以下に示すのは、__mystorage__ という名前のストレージ アカウントのエントリの例です。

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. 削除する必要があるストレージ アカウントのキーを特定した後、エントリの右側にある赤い "-" アイコンを使用してエントリを削除します。 次に、__[Save (保存)]__ ボタンを使用して、変更を保存します。

5. 変更が保存された後、スクリプト アクションを使用してストレージ アカウントと新しいキー値をクラスターに追加します。

### <a name="poor-performance"></a>パフォーマンスが低い

ストレージ アカウントが HDInsight クラスターとは異なるリージョンにある場合は、パフォーマンスが低下することがあります。 別のリージョンのデータにアクセスすると、そのリージョンの Azure データ センター外にネットワーク トラフィックが送信され、パブリック インターネットを経由するため、遅延が生じる場合があります。

> [!WARNING]
> HDInsight クラスター以外の場所でストレージ アカウントを使用することはできません。

### <a name="additional-charges"></a>追加料金が発生する

ストレージ アカウントが HDInsight クラスターとは異なるリージョンにある場合は、Azure の課金にエグレス料金が追加されていることがあります。 データが地域データ センターを離れると、エグレス料金が適用されます。 この料金は、トラフィックが他のリージョンの別の Azure データ センター宛てであっても適用されます。

> [!WARNING]
> HDInsight クラスター以外の場所でストレージ アカウントを使用することはできません。

## <a name="next-steps"></a>次のステップ

既存の HDInsight クラスターにストレージ アカウントを追加する方法について説明しました。 スクリプト アクションの詳細については、「[スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」を参照してください。
