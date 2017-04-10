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
ms.date: 02/23/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
translationtype: Human Translation
ms.sourcegitcommit: d391c5c6289aa63e969f63f189eb5db680883f0a
ms.openlocfilehash: b8c5e53ed5fe86ed099e37644d405080477f8c27
ms.lasthandoff: 03/01/2017

---

# <a name="add-additional-storage-accounts-to-hdinsight"></a>HDInsight にストレージ アカウントを追加する

Linux をオペレーティング システムとして使用する既存の HDInsight クラスターに Azure ストレージ アカウントを追加するためにスクリプト アクションを使用する方法について説明します。

> [!IMPORTANT]
> このドキュメントでは、クラスターの作成後に、そのクラスターにストレージを追加する方法を取り上げています。 クラスター作成時にストレージ アカウントを追加する方法については、[Linux ベースの HDInsight クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md#use-additional-storage)に関するドキュメントの「__追加のストレージの使用__」セクションを参照してください。

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

* Oozie、YARN、MapReduce2、および HDFS サービスが新しいストレージ アカウント情報を取得できるように、これらのサービスを停止してから再開します。

> [!WARNING]
> ストレージ アカウントが HDInsight クラスターとは異なるリージョンにある場合は、パフォーマンスが低下することがあります。 別のリージョンのデータにアクセスすると、そのリージョンの Azure データ センター外にネットワーク トラフィックが送信され、パブリック インターネットを経由するため、遅延が生じる場合があります。 また、リージョンのデータ センター外にデータを送信すると、データがデータ センターから出るときにエグレス料金がかかるため、コストが増える可能性があります。

## <a name="the-script"></a>スクリプト

__スクリプトの場所__: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__要件__:

* スクリプトを__ヘッド ノード__に適用する必要があります。

## <a name="to-use-the-script"></a>スクリプトを使用するには

Azure Portal、Azure PowerShell、および Azure CLI を通じてスクリプト アクションを使用する方法については、ドキュメント「[スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)」の「実行中のクラスターにスクリプト アクションを適用する」セクションを参照してください。

カスタマイズのドキュメントに記載された情報を利用する場合は、サンプル スクリプト アクション URI を、このスクリプトの URI (https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh) に置き換えます。 例として使用されているすべてのパラメーターを、クラスターに追加するストレージ アカウントの Azure ストレージ アカウント名とキーに置き換えます。

> [!NOTE]
> このスクリプトは、クラスターの Ambari 構成を直接更新するため、__Persisted__ としてマークする必要はありません。

## <a name="known-issues"></a>既知の問題

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>ストレージ アカウントが Azure Portal またはツールに表示されない

Azure Portal で HDInsight クラスターを表示しているときに、__[プロパティ]__ の下の __[ストレージ アカウント]__ エントリを選択すると、このスクリプト アクションを通じて追加したストレージ アカウントは表示されません。 Azure PowerShell と Azure CLI でも、追加のストレージ アカウントは表示されません。

ストレージ情報が表示されないのは、スクリプトがクラスターの core-site.xml 構成を変更するだけだからです。 この情報は、Azure 管理 API を使用してクラスター情報を取得するときには使用されません。

このスクリプトを使用してクラスターに追加されたストレージ アカウント情報を表示するには、Ambari REST API を使用します。 次のコマンドは、Ambari の JSON データを取得および解析するために [cURL (http://curl.haxx.se/)](http://curl.haxx.se/) および [jq (https://stedolan.github.io/jq/)](https://stedolan.github.io/jq/) を使用する方法を示しています。

> [!div class="tabbedCodeSnippets" data-resources="OutlookServices.Calendar"]
> ```PowerShell
> curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["""fs.azure.account.key.STORAGEACCOUNT.blob.core.windows.net"""] | select(. != null)'
> ```
> ```Bash
> curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.azure.account.key.STORAGEACCOUNT.blob.core.windows.net"] | select(. != null)'
> ```

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

3. __[Filter... (フィルター...)]__ フィールドに __fs.azure.account__ の値を入力します。 これで、クラスターに追加されたすべてのストレージ アカウントのエントリが返されます。 エントリには、__keyprovider__ と __key__ という&2; つの種類があります。 両方に、キー名の一部としてストレージ アカウントの名前が含まれています。 

    以下に示すのは、__mystorage__ という名前のストレージ アカウントのエントリの例です。

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. 削除する必要があるストレージ アカウントのキーを特定した後、エントリの右側にある赤い "-" アイコンを使用してエントリを削除します。 次に、__[Save (保存)]__ ボタンを使用して、変更を保存します。

5. 変更が保存された後、スクリプト アクションを使用してストレージ アカウントと新しいキー値をクラスターに追加します。

### <a name="poor-performance"></a>パフォーマンスが低い

ストレージ アカウントが HDInsight クラスターとは異なるリージョンにある場合は、パフォーマンスが低下することがあります。 別のリージョンのデータにアクセスすると、そのリージョンの Azure データ センター外にネットワーク トラフィックが送信され、パブリック インターネットを経由するため、遅延が生じる場合があります。

### <a name="additional-charges"></a>追加料金が発生する

ストレージ アカウントが HDInsight クラスターとは異なるリージョンにある場合は、Azure の課金にエグレス料金が追加されていることがあります。 エグレス料金は、トラフィックが他のリージョンの別の Azure データ センター宛てであっても、データがリージョンのデータ センターの外に出る場合に適用されます。

## <a name="next-steps"></a>次のステップ

既存の HDInsight クラスターにストレージ アカウントを追加する方法について説明しました。 スクリプト アクションの詳細については、「[スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」を参照してください。
