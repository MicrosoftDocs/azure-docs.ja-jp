---
title: Azure で ELK を使用して Service Fabric 内のアプリを監視する
description: このチュートリアルでは、ELK を設定して Service Fabric アプリケーションを監視する方法について説明します。
ms.topic: tutorial
ms.date: 02/26/2018
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 5faf26230618161a5b908c9a544a43ec5c33b807
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91532024"
---
# <a name="tutorial-monitor-your-service-fabric-applications-using-elk"></a>チュートリアル: ELK を使用して Service Fabric アプリケーションを監視する

このチュートリアルは、シリーズの第 4 部です。 ここでは、ELK (Elasticsearch、Logstash、Kibana) を使用して、Azure で実行されている Service Fabric アプリケーションを監視する方法について説明します。

シリーズの第 4 部では、次の方法を学習します。
> [!div class="checklist"]
> * Azure で ELK サーバーを設定する
> * Event Hubs からログを受信するよう Logstash を構成する
> * Kibana でプラットフォームとアプリケーションのログを視覚化する

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * [Java Service Fabric Reliable Services アプリケーションをビルドする](service-fabric-tutorial-create-java-app.md)
> * [ローカル クラスターでアプリケーションをデプロイおよびデバッグする](service-fabric-tutorial-debug-log-local-cluster.md)
> * [アプリケーションを Azure クラスターにデプロイする](service-fabric-tutorial-java-deploy-azure.md)
> * アプリケーションの監視と診断を設定する
> * [CI/CD を設定します](service-fabric-tutorial-java-jenkins.md)

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure サブスクリプションを持っていない場合は[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成する
* [第 2 部](service-fabric-tutorial-debug-log-local-cluster.md)で指定した場所にログを出力するようアプリケーションを設定します。
* [第 3 部](service-fabric-tutorial-java-deploy-azure.md)を完了し、ログを Event Hubs に送信するよう構成された Service Fabric クラスターを実行します。
* "リッスン" アクセス許可が含まれた Event Hubs のポリシーと、第 3 部の関連付けられた主キー。

## <a name="download-the-voting-sample-application"></a>投票サンプル アプリケーションをダウンロードする

[このチュートリアル シリーズの第 1 部](service-fabric-tutorial-create-java-app.md)で投票サンプル アプリケーションをビルドしていない場合は、ダウンロードすることができます。 コマンド ウィンドウで、次のコマンドを実行して、サンプル アプリのリポジトリをローカル コンピューターに複製します。

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart
```

## <a name="create-an-elk-server-in-azure"></a>Azure で ELK サーバーを作成する

このチュートリアルでは、構成済みの ELK 環境を使用できます。また、既にお持ちの場合、**Logstash の設定** に関するセクションまでスキップできます。 しかし、お持ちでない場合は、次の手順に従って Azure で作成してください。

1. Azure で ELK Certified by [Bitnami](https://ms.portal.azure.com/#create/bitnami.elk4-6) を作成します。 チュートリアルの目的上、このサーバーを作成するために従わなければならない仕様は特にありません。

2. Azure Portal でリソースに移動し、 **[サポート + トラブルシューティング]** セクションの **[ブート診断]** を押します。 次に、 **[シリアル ログ]** タブをクリックします。

    ![ブート診断](./media/service-fabric-tutorial-java-elk/bootdiagnostics.png)
3. Kibana インスタンスへのアクセスに必要なパスワードをログで検索します。 これは次のようなスニペットです。

    ```bash
    [   25.932766] bitnami[1496]: #########################################################################
    [   25.948656] bitnami[1496]: #                                                                       #
    [   25.962448] bitnami[1496]: #        Setting Bitnami application password to '[PASSWORD]'           #
    [   25.978137] bitnami[1496]: #        (the default application username is 'user')                   #
    [   26.004770] bitnami[1496]: #                                                                       #
    [   26.029413] bitnami[1496]: #########################################################################
    ```

4. Azure Portal のサーバーの [概要] ページにある [接続] ボタンを押して、ログインの詳細を取得します。

    ![VM の接続](./media/service-fabric-tutorial-java-elk/vmconnection.png)

5. 次のコマンドを使用して、ELK イメージがホストされているサーバーに SSH 接続します

    ```bash
    ssh [USERNAME]@[CONNECTION-IP-OF-SERVER]

    Example: ssh testaccount@104.40.63.157
    ```

## <a name="set-up-elk"></a>ELK の設定

1. 最初の手順では ELK 環境を読み込みます

    ```bash
    sudo /opt/bitnami/use_elk
    ```

2. 既存の環境を使用している場合、次のコマンドを実行して Logstash サービスを停止する必要があります

    ```bash
    sudo /opt/bitnami/ctlscript.sh stop logstash
    ```

3. 次のコマンドを実行して、Event Hubs 用の Logstash プラグインをインストールします。

    ```bash
    logstash-plugin install logstash-input-azureeventhub
    ```

4. 次の内容で Logstash 構成ファイルを作成します。または、既存の Logstash 構成ファイルを変更します。ファイルを作成する場合、Azure で ELK Bitnami イメージを使用するのであれば、```/opt/bitnami/logstash/conf/access-log.conf``` で Logstash 構成ファイルを作成する必要があります。

    ```json
    input
    {
        azureeventhub
        {
            key => "[RECEIVER-POLICY-KEY-FOR-EVENT-HUB]"
            username => "[RECEIVER-POLICY-NAME]"
            namespace => "[EVENTHUB-NAMESPACENAME]"
            eventhub => "[EVENTHUB-NAME]"
            partitions => 4
        }
    }

    output {
         elasticsearch {
             hosts => [ "127.0.0.1:9200" ]
         }
     }
    ```

5. 構成を確認するには、次のコマンドを実行します。

    ```bash
    /opt/bitnami/logstash/bin/logstash -f /opt/bitnami/logstash/conf/ --config.test_and_exit
    ```

6. Logstash サービスを開始します

    ```bash
    sudo /opt/bitnami/ctlscript.sh start logstash
    ```

7. Elasticsearch をチェックして、データを受信していることを確認します

    ```bash
    curl 'localhost:9200/_cat/indices?v'
    ```

8. **http:\//SERVER-IP** で Kibana ダッシュボードにアクセスし、Kibana のユーザー名とパスワードを入力します。 Azure で ELK イメージを使用した場合、既定のユーザー名は "user" になり、パスワードは **ブート診断** で取得したものになります。

    ![プラットフォームとアプリケーション ログを視覚化する Kibana ダッシュボードを示すスクリーンショット。](./media/service-fabric-tutorial-java-elk/kibana.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * ELK サーバーを立ち上げて Azure で実行する
> * Service Fabric クラスターから診断情報を受信するようサーバーを構成する

次のチュートリアルに進みます。
> [!div class="nextstepaction"]
> [Jenkins で CI/CD を設定する](service-fabric-tutorial-java-jenkins.md)
