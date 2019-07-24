---
title: Apache Kafka での自動トピック作成を有効にする - Azure HDInsight
description: トピックを自動的に作成するように HDInsight 上の Apache Kafka を構成する方法を説明します。 Ambari を使用して、あるいは PowerShell または Resource Manager テンプレートでクラスターを作成するときに、auto.create.topics.enable を true に設定することによって、Kafka を構成できます。
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/18/2018
ms.openlocfilehash: b9f17ad0ccfd5e58d5b93bde91e6f9c537a15fa6
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53606896"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>トピックを自動的に作成するように HDInsight 上の Apache Kafka を構成する方法

既定では、HDInsight 上の [Apache Kafka](https://kafka.apache.org/) は自動的にトピックを作成しません。 [Apache Ambari](https://ambari.apache.org/) を使って、既存のクラスターでの自動トピック作成を有効にすることができます。 Azure Resource Manager テンプレートを使って新しい Kafka クラスターを作成するときに、自動トピック作成を有効にすることもできます。

## <a name="apache-ambari-web-ui"></a>Apache Ambari Web UI

Ambari Web UI を使って既存のクラスターでトピックの自動作成を有効にするには、次の手順のようにします。

1. [Azure portal](https://portal.azure.com) で Kafka クラスターを選びます。

2. __クラスターの概要__ で __[クラスター ダッシュボード]__ を選びます。 

    ![クラスター ダッシュボードが選ばれたポータルの画像](./media/apache-kafka-auto-create-topics/kafka-cluster-overview.png)

3. 次に、__[HDInsight クラスター ダッシュボード]__ を選択します。 認証を求められたら、クラスターのログイン (管理者) 資格情報を使用して認証を行います。

    ![HDInsight クラスター ダッシュボード エントリの画像](./media/apache-kafka-auto-create-topics/hdinsight-cluster-dashboard.png)

3. ページの左側にある一覧で Kafka サービスを選びます。

    ![サービスの一覧](./media/apache-kafka-auto-create-topics/service-list.png)

4. ページ中央の [Configs]\(構成\) を選択します。

    ![サービスの [Configs]\(構成\) タブ](./media/apache-kafka-auto-create-topics/service-config.png)

5. [フィルター] フィールドに値「`auto.create`」を入力します。 

    ![フィルター フィールドの画像](./media/apache-kafka-auto-create-topics/filter.png)

    プロパティの一覧にフィルターが適用されて `auto.create.topics.enable` 設定が表示されます。

6. `auto.create.topics.enable` の値を `true` に変更して、[保存] を選びます。 メモを追加してから、もう一度 [保存] を選びます。

    ![auto.create.topics.enable エントリの画像](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

7. Kafka サービスを選び、__[Restart]\(再起動\)__ を選んで、__[Restart all affected]\(影響を受けるものをすべて再起動\)__ を選びます。 メッセージが表示されたら、__[Confirm restart all]\(すべて再起動\)__ を選択します。

    ![再起動の選択の画像](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> Ambari REST API を使って Ambari の値を設定することもできます。 現在の構成の取得やその変更のために複数の REST 呼び出しを行う必要があるため、通常はこの方法の方が困難です。詳細については、[Apache Ambari REST API を使用した HDInsight クラスターの管理](../hdinsight-hadoop-manage-ambari-rest-api.md)に関するページを参照してください。

## <a name="resource-manager-templates"></a>Resource Manager テンプレート

Azure Resource Manager テンプレートを使って Kafka クラスターを作成するときに、`kafka-broker` に追加することによって、`auto.create.topics.enable` を直接設定できます。 次の JSON スニペットでは、この値を `true` に設定する方法を示します。

```json
"clusterDefinition": {
    "kind": "kafka",
    "configurations": {
    "gateway": {
        "restAuthCredential.isEnabled": true,
        "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
        "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
    },
    "kafka-broker": {
        "auto.create.topics.enable": "true"
    }
}
```

## <a name="next-steps"></a>次の手順

このドキュメントでは、HDInsight 上の Apache Kafka でトピックの自動作成を有効にする方法を説明しました。 Kafka の使用について詳しくは、次のリンクをご覧ください。

* [Apache Kafka ログを分析する](apache-kafka-log-analytics-operations-management.md)
* [Apache Kafka クラスター間でデータをレプリケートする](apache-kafka-mirroring.md)
