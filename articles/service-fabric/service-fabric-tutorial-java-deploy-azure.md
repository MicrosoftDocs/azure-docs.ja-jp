---
title: Azure の Service Fabric クラスターに Java アプリをデプロイする
description: このチュートリアルでは、Azure Service Fabric クラスターに Java Service Fabric アプリケーションをデプロイする方法について説明します。
ms.topic: tutorial
ms.date: 02/26/2018
ms.custom: mvc, devx-track-java, devx-track-azurecli
ms.openlocfilehash: 48cb7df49fb918ebd30883edc573cc63b6e35e4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98789399"
---
# <a name="tutorial-deploy-a-java-application-to-a-service-fabric-cluster-in-azure"></a>チュートリアル:Azure の Service Fabric クラスターに Java アプリケーションをデプロイする

このチュートリアルはシリーズの第 3 部です。Azure のクラスターに Service Fabric アプリケーションをデプロイする方法について説明します。

シリーズの第 3 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure でのセキュリティで保護された Linux クラスターの作成
> * クラスターへのアプリケーションのデプロイ

このチュートリアル シリーズで学習する内容は次のとおりです。

> [!div class="checklist"]
> * [Java Service Fabric Reliable Services アプリケーションをビルドする](service-fabric-tutorial-create-java-app.md)
> * [ローカル クラスターでアプリケーションをデプロイおよびデバッグする](service-fabric-tutorial-debug-log-local-cluster.md)
> * アプリケーションを Azure クラスターにデプロイする
> * [アプリケーションの監視と診断を設定する](service-fabric-tutorial-java-elk.md)
> * [CI/CD を設定します](service-fabric-tutorial-java-jenkins.md)

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure サブスクリプションを持っていない場合は[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成する
* [Azure CLI のインストール](/cli/azure/install-azure-cli)
* [Mac](service-fabric-get-started-mac.md) または [Linux](service-fabric-get-started-linux.md) 向け Service Fabric SDK のインストール
* [Python 3 のインストール](https://wiki.python.org/moin/BeginnersGuide/Download)

## <a name="create-a-service-fabric-cluster-in-azure"></a>Azure に Service Fabric クラスターを作成する

以降の手順では、目的のアプリケーションを Service Fabric クラスターにデプロイするために必要なリソースを作成します。 さらに、ELK (Elasticsearch、Logstash、Kibana) スタックを使ってソリューションの正常性を監視するために必要なリソースのセットアップも行います。 具体的には、Service Fabric から送信されるログのシンクとして [Event Hubs](https://azure.microsoft.com/services/event-hubs/) を使用します。 それを適切に構成することによって、Service Fabric クラスターから受信したログを Logstash インスタンスに送信します。

1. ターミナルを開いて次のパッケージをダウンロードします。このパッケージには、Azure にリソースを作成するために必要なヘルパー スクリプトとテンプレートが含まれています。

    ```bash
    git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
    ```

2. Azure アカウントへのサインイン

    ```azurecli
    az login
    ```

3. リソースの作成に使用する Azure サブスクリプションを設定します。

    ```azurecli
    az account set --subscription [SUBSCRIPTION-ID]
    ```

4. *service-fabric-java-quickstart/AzureCluster* フォルダーから次のコマンドを実行して、Key Vault にクラスター証明書を作成します。 Service Fabric クラスターのセキュリティを確保するために、この証明書が使用されます。 リージョン (Service Fabric クラスターと同じリージョン)、キー コンテナーのリソース グループ名、キー コンテナー名、証明書のパスワード、クラスターの DNS 名を指定します。

    ```bash
    ./new-service-fabric-cluster-certificate.sh [REGION] [KEY-VAULT-RESOURCE-GROUP] [KEY-VAULT-NAME] [CERTIFICATE-PASSWORD] [CLUSTER-DNS-NAME-FOR-CERTIFICATE]

    Example: ./new-service-fabric-cluster-certificate.sh 'westus' 'testkeyvaultrg' 'testkeyvault' '<password>' 'testservicefabric.westus.cloudapp.azure.com'
    ```

    前のコマンドからは、次の情報が返されます。後で必要になるので書き留めておいてください。

    ```output
    Source Vault Resource Id: /subscriptions/<subscription_id>/resourceGroups/testkeyvaultrg/providers/Microsoft.KeyVault/vaults/<name>
    Certificate URL: https://<name>.vault.azure.net/secrets/<cluster-dns-name-for-certificate>/<guid>
    Certificate Thumbprint: <THUMBPRINT>
    ```

5. ログを格納するストレージ アカウントのリソース グループを作成します。

    ```azurecli
    az group create --location [REGION] --name [RESOURCE-GROUP-NAME]

    Example: az group create --location westus --name teststorageaccountrg
    ```

6. 生成されるログの格納に使用するストレージ アカウントを作成します。

    ```azurecli
    az storage account create -g [RESOURCE-GROUP-NAME] -l [REGION] --name [STORAGE-ACCOUNT-NAME] --kind Storage

    Example: az storage account create -g teststorageaccountrg -l westus --name teststorageaccount --kind Storage
    ```

7. [Azure Portal](https://portal.azure.com) にアクセスし、お使いのストレージ アカウントの **[Shared Access Signature]** タブに移動します。 次のように SAS トークンを生成します。

    ![ストレージの SAS を生成](./media/service-fabric-tutorial-java-deploy-azure/storagesas.png)

8. Service Fabric クラスターを作成するときのために、アカウントの SAS URL をコピーし、保存しておきます。 次のような URL になっています。

    ```output
    ?sv=2017-04-17&ss=bfqt&srt=sco&sp=rwdlacup&se=2018-01-31T03:24:04Z&st=2018-01-30T19:24:04Z&spr=https,http&sig=IrkO1bVQCHcaKaTiJ5gilLSC5Wxtghu%2FJAeeY5HR%2BPU%3D
    ```

9. Event Hubs のリソースを含んだリソース グループを作成します。 Event Hubs は、ELK のリソースが実行されているサーバーに対して Service Fabric からメッセージを送信する目的で使用します。

    ```azurecli
    az group create --location [REGION] --name [RESOURCE-GROUP-NAME]

    Example: az group create --location westus --name testeventhubsrg
    ```

10. 以下のコマンドを使用して Event Hubs のリソースを作成します。 プロンプトに従って、namespaceName、eventHubName、consumerGroupName、sendAuthorizationRule、receiveAuthorizationRule の詳細を入力してください。

    ```azurecli
    az deployment group create -g [RESOURCE-GROUP-NAME] --template-file eventhubsdeploy.json

    Example:
    az deployment group create -g testeventhubsrg --template-file eventhubsdeploy.json
    Please provide string value for 'namespaceName' (? for help): testeventhubnamespace
    Please provide string value for 'eventHubName' (? for help): testeventhub
    Please provide string value for 'consumerGroupName' (? for help): testeventhubconsumergroup
    Please provide string value for 'sendAuthorizationRuleName' (? for help): sender
    Please provide string value for 'receiveAuthorizationRuleName' (? for help): receiver
    ```

    前のコマンドの JSON 出力から **output** フィールドの内容をコピーします。 sender の情報は、Service Fabric クラスターの作成時に使用します。 receiver name と receiver key は、次のチュートリアルで Event Hubs からメッセージを受信するための構成を Logstash サービスに対して行う際に使用するので、保存しておいてください。 次の BLOB は JSON 出力の例です。

    ```json
    "outputs": {
        "receiver Key": {
            "type": "String",
            "value": "[KEY]"
        },
        "receiver Name": {
            "type": "String",
            "value": "receiver"
        },
        "sender Key": {
            "type": "String",
            "value": "[KEY]"
        },
        "sender Name": {
            "type": "String",
            "value": "sender"
        }
    }
    ```

11. *eventhubssastoken.py* スクリプトを実行して、作成した Event Hubs リソースの SAS URL を生成します。 この SAS URL は、Service Fabric クラスターが Event Hubs にログを送信するときに使用されます。 そのため、この URL の生成には **sender** ポリシーが使用されます。 このスクリプトを実行すると、次の手順で使用する Event Hubs リソースの SAS URL が返されます。

    ```python
    python3 eventhubssastoken.py 'testeventhubs' 'testeventhubs' 'sender' '[PRIMARY-KEY]'
    ```

    返された JSON から **sr** フィールドの値をコピーします。 **sr** フィールドの値は、Event Hubs の SAS トークンです。 次の URL は **sr** フィールドの例です。

    ```output
    https%3A%2F%testeventhub.servicebus.windows.net%testeventhub&sig=7AlFYnbvEm%2Bat8ALi54JqHU4i6imoFxkjKHS0zI8z8I%3D&se=1517354876&skn=sender
    ```

    Event Hubs の SAS URL は、`https://<namespacename>.servicebus.windows.net/<eventhubsname>?sr=<sastoken>` という構造になっています。 たとえば、`https://testeventhubnamespace.servicebus.windows.net/testeventhub?sr=https%3A%2F%testeventhub.servicebus.windows.net%testeventhub&sig=7AlFYnbvEm%2Bat8ALi54JqHU4i6imoFxkjKHS0zI8z8I%3D&se=1517354876&skn=sender` のように指定します。

12. *sfdeploy.parameters.json* ファイルを開いて、次の内容を、先行する手順で得た値に置き換えます。 [SAS-URL-STORAGE-ACCOUNT] は、手順 8 でメモしました。 [SAS-URL-EVENT-HUBS] は、手順 11 でメモしました。

    ```json
    "applicationDiagnosticsStorageAccountName": {
        "value": "teststorageaccount"
    },
    "applicationDiagnosticsStorageAccountSasToken": {
        "value": "[SAS-URL-STORAGE-ACCOUNT]"
    },
    "loggingEventHubSAS": {
        "value": "[SAS-URL-EVENT-HUBS]"
    }
    ```

13. **sfdeploy.parameters.json** を開きます。 次のパラメーターを変更し、ファイルを保存します。
    - **clusterName**。 使用できるのは小文字と数字だけです。
    - **adminUserName** (空白以外の値)
    - **adminPassword** (空白以外の値)

14. 次のコマンドを実行して、Service Fabric クラスターを作成します。

    ```azurecli
    az sf cluster create --location 'westus' --resource-group 'testlinux' --template-file sfdeploy.json --parameter-file sfdeploy.parameters.json --secret-identifier <certificate_url_from_step4>
    ```

## <a name="deploy-your-application-to-the-cluster"></a>アプリケーションをクラスターにデプロイする

1. アプリケーションをデプロイする前に、*Voting/VotingApplication/ApplicationManifest.xml* ファイルに以下のスニペットを追加しておく必要があります。 **X509FindValue** フィールドは、「**Azure に Service Fabric クラスターを作成する**」セクションの手順 4. で返された拇印です。 **ApplicationManifest** フィールド (ルート フィールド) 下に、入れ子となるようにこのスニペットを追加してください。

    ```xml
    <Certificates>
          <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[CERTIFICATE-THUMBPRINT]" />
    </Certificates>
    ```

2. このクラスターにアプリケーションをデプロイするには、SFCTL を使用して、クラスターへの接続を確立する必要があります。 クラスターに接続するには、SFCTL に公開キーと秘密キーの両方を含む PEM ファイルが必要です。 次のコマンドを実行して、公開キーと秘密キーの両方を含む PEM ファイルを生成します。 

    ```bash
    openssl pkcs12 -in <clustername>.<region>.cloudapp.azure.com.pfx -out sfctlconnection.pem -nodes -passin pass:<password>
    ```

3. 次のコマンドを実行してクラスターに接続します。

    ```bash
    sfctl cluster select --endpoint https://<clustername>.<region>.cloudapp.azure.com:19080 --pem sfctlconnection.pem --no-verify
    ```

4. アプリケーションをデプロイするには、*Voting/Scripts* フォルダーに移動して、**install.sh** スクリプトを実行します。

    ```bash
    ./install.sh
    ```

5. Service Fabric Explorer にアクセスするには、任意のブラウザーを開いて「 `https://testlinuxcluster.westus.cloudapp.azure.com:19080` 」と入力します。 証明書ストアから、このエンドポイントに接続する際に使用する証明書を選択します。 Linux マシンを使用している場合、Service Fabric Explorer を表示するには、*new-service-fabric-cluster-certificate.sh* スクリプトによって生成された証明書を Chrome にインポートする必要があります。 Mac を使用する場合、キーチェーンに PFX ファイルをインストールする必要があります。 目的のアプリケーションがクラスターにインストールされていることがわかります。

    ![SFX Java Azure](./media/service-fabric-tutorial-java-deploy-azure/sfxjavaonazure.png)

6. アプリケーションにアクセスするには、「 `https://testlinuxcluster.westus.cloudapp.azure.com:8080` 」と入力します。

    ![Java Azure の投票アプリケーション](./media/service-fabric-tutorial-java-deploy-azure/votingappjavaazure.png)

7. アプリケーションをクラスターからアンインストールするには、**Scripts** フォルダーの *uninstall.sh* スクリプトを実行します。

    ```bash
    ./uninstall.sh
    ```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Azure でのセキュリティで保護された Linux クラスターの作成
> * ELK を使った監視に必要なリソースの作成

次のチュートリアルに進みます。
> [!div class="nextstepaction"]
> [監視と診断のセットアップ](service-fabric-tutorial-java-elk.md)
