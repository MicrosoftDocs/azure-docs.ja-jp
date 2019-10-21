---
title: チュートリアル - Azure HDInsight での Apache Kafka と Enterprise セキュリティ パッケージ
description: チュートリアル - Enterprise セキュリティ パッケージを使用して Azure HDInsight に Kafka 用の Apache Ranger ポリシーを構成する方法について説明します。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: tutorial
ms.date: 09/04/2019
ms.openlocfilehash: 6d92ebc743bae97ecfa1591add27f470792dcafc
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71037168"
---
# <a name="tutorial-configure-apache-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>チュートリアル:Enterprise セキュリティ パッケージを使用して HDInsight に Apache Kafka ポリシーを構成する (プレビュー)

Enterprise セキュリティ パッケージ (ESP) の Apache Kafka クラスター用の Apache Ranger ポリシーを構成する方法について説明します。 ESP クラスターは、ユーザーがドメイン資格情報で認証できるドメインに接続されます。 このチュートリアルでは、`sales` および `marketingspend` トピックへのアクセスを制限する 2 つの Ranger ポリシーを作成します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ドメイン ユーザーの作成
> * Ranger ポリシーの作成
> * Kafka クラスターでのトピックの作成
> * Ranger ポリシーのテスト

## <a name="prerequisite"></a>前提条件

[Enterprise セキュリティ パッケージがインストールされた HDInsight Kafka クラスター](./apache-domain-joined-configure-using-azure-adds.md)。

## <a name="connect-to-apache-ranger-admin-ui"></a>Apache Ranger 管理 UI への接続

1. ブラウザーから、URL `https://ClusterName.azurehdinsight.net/Ranger/` を使用して Ranger 管理ユーザー インターフェイスに接続します。 必ず、`ClusterName` をお使いの Kafka クラスターの名前に変更してください。 Ranger の資格情報は、Hadoop クラスターの資格情報と同じではありません。 ブラウザーで Hadoop のキャッシュされた資格情報が使用されないように、新しい InPrivate ブラウザー ウィンドウを使用して Ranger 管理 UI に接続してください。

2. Azure Active Directory (AD) 管理者の資格情報を使用してサインインします。 Azure AD 管理者の資格情報は、HDInsight クラスターの資格情報や Linux HDInsight ノード SSH の資格情報と同じではありません。

   ![HDInsight Apache Ranger 管理 UI](./media/apache-domain-joined-run-kafka/apache-ranger-admin-login.png)

## <a name="create-domain-users"></a>ドメイン ユーザーの作成

**sales_user** および **marketing_user** ドメイン ユーザーを作成する方法の詳細については、[Enterprise セキュリティ パッケージでの HDInsight クラスターの作成](./apache-domain-joined-configure-using-azure-adds.md)に関するページを参照してください。 運用シナリオでは、ドメイン ユーザーは Active Directory テナントに含まれます。

## <a name="create-ranger-policy"></a>Ranger ポリシーの作成

**sales_user** と **marketing_user** 用の Ranger ポリシーを作成します。

1. **Ranger 管理 UI** を開きます。

2. **[Kafka]** で **[\<ClusterName>_kafka]** を選択します。 構成済みポリシーが 1 つリストされる場合があります。

3. **[Add New Policy]\(新しいポリシーの追加\)** を選択し、次の値を入力します。

   |Setting  |推奨値  |
   |---------|---------|
   |ポリシー名  |  hdi sales* policy   |
   |トピック   |  sales* |
   |ユーザーの選択  |  sales_user1 |
   |アクセス許可  | publish、consume、create |

   トピック名には、次のワイルドカードを含めることができます。

   * '*' は、文字が 0 回以上出現することを示します。
   * '?' は、1 文字を示します。

   ![Apache Ranger 管理 UI の作成ポリシー 1](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy.png)

   **[ユーザーの選択]** にドメイン ユーザーが自動的に設定されない場合は、Ranger が Azure AD と同期されるまでしばらく待ってください。

4. **[Add]\(追加\)** をクリックしてポリシーを保存します。

5. **[Add New Policy]\(新しいポリシーの追加\)** を選択し、次の値を入力します。

   |Setting  |推奨値  |
   |---------|---------|
   |ポリシー名  |  hdi marketing policy   |
   |トピック   |  marketingspend |
   |ユーザーの選択  |  marketing_user1 |
   |アクセス許可  | publish、consume、create |

   ![Apache Ranger 管理 UI の作成ポリシー 2](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy-2.png)  

6. **[Add]\(追加\)** をクリックしてポリシーを保存します。

## <a name="create-topics-in-a-kafka-cluster-with-esp"></a>ESP を使用する Kafka クラスターでのトピックの作成

`salesevents` および `marketingspend` という 2 つのトピックを作成するには、次のようにします。

1. 次のコマンドを使用して、クラスターへの SSH 接続を開きます。

   ```cmd
   ssh DOMAINADMIN@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   `DOMAINADMIN` は、[クラスターの作成](./apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp)時に構成したクラスターの管理者ユーザーに置き換えます。また、`CLUSTERNAME` はクラスターの名前に置き換えます。 メッセージが表示されたら、管理者ユーザー アカウントのパスワードを入力します。 HDInsight での `SSH` の使用の詳細については、「[HDInsight で SSH を使用する](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)」を参照してください。

2. 次のコマンドを使用して、クラスター名を変数に保存して、JSON 解析ユーティリティ `jq` をインストールします。 プロンプトが表示されたら、Kafka クラスター名を入力します。

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. 次のコマンドを使用して、Kafka ブローカー ホストを取得します。 プロンプトが表示されたら、クラスターの管理者アカウント用のパスワードを入力します。

   ```bash
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```

   開発環境をまだ設定していない場合は、先に進む前に設定してください。 SSH クライアントと scp、Java JDK、Apache Maven などのコンポーネントが必要となります。 詳細については、[設定手順](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer)に関するページを参照してください。

1. [Apache Kafka Domain-Joined Producer Consumer サンプル](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer)をダウンロードします。

1. 「**例を構築してデプロイする**」の手順 2. と手順 3. を行います (「[チュートリアル: Apache Kafka Producer および Consumer API の使用](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example)」)。

1. 次のコマンドを実行します。

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create salesevents $KAFKABROKERS
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create marketingspend $KAFKABROKERS
   ```

## <a name="test-the-ranger-policies"></a>Ranger ポリシーのテスト

構成された Ranger ポリシーに基づき、**sales_user** では `salesevents` トピックをプロデュースおよびコンシュームできますが、`marketingspend` トピックをプロデュースおよびコンシュームすることはできません。 これに対して、**marketing_user** では `marketingspend` トピックをプロデュースおよびコンシュームできますが、`salesevents` トピックをプロデュースおよびコンシュームすることはできません。

1. クラスターへの新しい SSH 接続を開きます。 次のコマンドを使用して、**sales_user1** としてサインインします。

   ```bash
   ssh sales_user1@CLUSTERNAME-ssh.azurehdinsight.net
   ```

2. 次のコマンドを実行します。

   ```bash
   export KAFKA_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf"
   ```

3. 前のセクションのブローカーの名前を使用して、次の環境変数を設定します。

   ```bash
   export KAFKABROKERS=<brokerlist>:9092
   ```

   例: `export KAFKABROKERS=wn0-khdicl.contoso.com:9092,wn1-khdicl.contoso.com:9092`

4. 「**例を構築してデプロイする**」の手順 3. (「[チュートリアル: Apache Kafka Producer および Consumer API の使用](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example)」) に従って、`kafka-producer-consumer.jar` を **sales_user** が利用できるようにします。

5. **sales_user1** は、次のコマンドを実行することによってトピック `salesevents` を生成できます。

   ```bash
   java -jar kafka-producer-consumer.jar producer salesevents $KAFKABROKERS
   ```

6. 次のコマンドを実行して、`salesevents` トピックからコンシュームします。

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   メッセージの読み取りができることを確認します。

7. 同じ ssh ウィンドウで次のコマンドを実行して、**sales_user1** では `marketingspend` トピックにプロデュースできないことを確認します。

   ```bash
   java -jar kafka-producer-consumer.jar producer marketingspend $KAFKABROKERS
   ```

   承認エラーが発生しますが、これは無視してかまいません。

8. **marketing_user1** では `salesevents` トピックからコンシュームできないことに注目してください。

   上記の手順 1. から 4. を繰り返しますが、今回は **marketing_user1** として行います。

   次のコマンドを実行して、`salesevents` トピックからコンシュームします。

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   前のメッセージは表示されません。

9. Ranger UI から監査アクセス イベントを表示します。

   ![Ranger UI ポリシーの監査アクセス イベント ](./media/apache-domain-joined-run-kafka/apache-ranger-admin-audit.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このアプリケーションを引き続き使用しない場合は、次の手順で作成した Kafka クラスターを削除します。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
1. 上部の**検索**ボックスに「**HDInsight**」と入力します。
1. **[サービス]** の下の **[HDInsight クラスター]** を選択します。
1. 表示される HDInsight クラスターの一覧で、このチュートリアル用に作成したクラスターの横にある **[...]** をクリックします。 
1. **[削除]** をクリックします。 **[はい]** をクリックします。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Apache Kafka に自分のキーを持ち込む](../kafka/apache-kafka-byok.md)