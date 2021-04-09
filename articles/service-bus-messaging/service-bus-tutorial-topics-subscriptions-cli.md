---
title: Azure CLI を使用して Service Bus のトピックとサブスクリプションを作成する
description: このクイックスタートでは、Azure CLI を使用して、Service Bus のトピックとそのサブスクリプションを作成する方法について説明します
ms.date: 06/23/2020
ms.topic: quickstart
author: spelluru
ms.author: spelluru
ms.openlocfilehash: 080b089efa276779420f6d9bc8e76272f1e65788
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90069698"
---
# <a name="use-azure-cli-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Azure CLI を使用して Service Bus トピックとそのサブスクリプションを作成する
このクイックスタートでは、Azure CLI を使用して Service Bus トピックを作成した後、そのトピックのサブスクリプションを作成します。 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Service Bus トピックとサブスクリプションとは
Service Bus のトピックとサブスクリプションは、メッセージ通信の *発行/サブスクライブ* モデルをサポートします。 トピックとサブスクリプションを使用すると、分散アプリケーションのコンポーネントが互いに直接通信することがなくなり、仲介者の役割を果たすトピックを介してメッセージをやり取りすることになります。

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

各メッセージが 1 つのコンシューマーによって処理される Service Bus キューとは異なり、トピックとサブスクリプションは、発行/サブスクライブ パターンを使用して "1 対多" 形式の通信を行います。 複数のサブスクリプションを 1 つのトピックに登録できます。 トピックに送信されたメッセージはサブスクリプションに渡され、各サブスクリプションで独立して処理できます。 トピックにとってのサブスクリプションは、トピックに送信されたメッセージのコピーを受け取る仮想キューのようなものです。 トピックに対するフィルター ルールをサブスクリプション単位で登録することもできます。この場合、トピックに送信されるどのメッセージをどのトピック サブスクリプションで受信するかのフィルター処理や制限が可能になります。

Service Bus のトピックとサブスクリプションを使用すると、多数のユーザーとアプリケーションの間でやり取りされる膨大な数のメッセージを処理することもできます。

## <a name="prerequisites"></a>前提条件
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント][free account]を作成できます。

このクイックスタートでは、Azure portal へのサインイン後に起動できる Azure Cloud Shell を使用します。 Azure Cloud Shell の詳細については、「[Azure Cloud Shell の概要](../cloud-shell/overview.md)」を参照してください。 Azure PowerShell を自分のマシンに[インストール](/cli/azure/install-azure-cli)して使用することもできます。 

## <a name="create-a-service-bus-topic-and-subscriptions"></a>Service Bus トピックとサブスクリプションを作成する
[トピックへの各サブスクリプション](service-bus-messaging-overview.md#topics)は、各メッセージのコピーを受信できます。 トピックは完全にプロトコルであり、意味的には Service Bus キューと互換性があります。 Service Bus のトピックは、フィルターの条件を持つさまざまな選択ルールをサポートしています。メッセージのプロパティを設定または変更するオプションのアクションもあります。 ルールが一致するたびに、メッセージが生成されます。 ルール、フィルター、およびアクションの詳細については、こちらの[リンク](topic-filters.md)を参照してください。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 次の画像に示すアイコンを選択して Azure Cloud Shell を起動します。 Cloud Shell が **PowerShell** モードになっている場合は **Bash** モードに切り替えます。 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Cloud Shell の起動":::
3. 次のコマンドを実行して、Azure リソース グループを作成します。 必要に応じて、リソース グループの名前と場所を更新します。 

    ```azurecli-interactive
    az group create --name MyResourceGroup --location eastus
    ```
4. 次のコマンドを実行して、Service Bus メッセージング名前空間を作成します。 名前空間は、一意の名前となるように更新してください。 

    ```azurecli-interactive
    namespaceName=MyNameSpace$RANDOM
    az servicebus namespace create --resource-group MyResourceGroup --name $namespaceName --location eastus
    ```
5. 次のコマンドを実行して、名前空間にトピックを作成します。 

    ```azurecli-interactive
    az servicebus topic create --resource-group MyResourceGroup   --namespace-name $namespaceName --name MyTopic
    ```
6. トピックの 1 つ目のサブスクリプションを作成します。
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S1    
    ```
6. トピックの 2 つ目のサブスクリプションを作成します。
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S2    
    ```
6. トピックの 3 つ目のサブスクリプションを作成します。
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S3    
    ```
7. 1 つ目のサブスクリプションにフィルターを作成します。カスタム プロパティを使用してフィルター (`StoreId` が `Store1`、`Store2`、`Store3` のいずれか) を設定してください。

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --subscription-name S1 --name MyFilter --filter-sql-expression "StoreId IN ('Store1','Store2','Store3')"    
    ```
8. 2 つ目のサブスクリプションにフィルターを作成します。カスタム プロパティを使用してフィルター (`StoreId = Store4`) を設定してください。

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S2 --name MySecondFilter --filter-sql-expression "StoreId = 'Store4'"    
    ```
9. 3 つ目のサブスクリプションにフィルターを作成します。カスタム プロパティを使用してフィルター (`StoreId` が `Store1`、`Store2`、`Store3`、`Store4` のいずれでもない) を設定してください。

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --subscription-name S3 --name MyThirdFilter --filter-sql-expression "StoreId NOT IN ('Store1','Store2','Store3', 'Store4')"     
    ```
10. 次のコマンドを実行して、名前空間のプライマリ接続文字列を取得します。 この接続文字列を使用してキューに接続し、メッセージを送受信します。 

    ```azurecli-interactive
    az servicebus namespace authorization-rule keys list --resource-group MyResourceGroup --namespace-name $namespaceName --name RootManageSharedAccessKey --query primaryConnectionString --output tsv    
    ```

    接続文字列とトピック名を書き留めておきます。 これらの情報を使用して、メッセージを送受信します。 
    

## <a name="next-steps"></a>次の手順
トピックにメッセージを送信してそれらのメッセージをサブスクリプション経由で受信する方法については、次の記事を参照してください。TOC からプログラミング言語を選択してください。 

> [!div class="nextstepaction"]
> [メッセージの発行とサブスクライブ](service-bus-dotnet-how-to-use-topics-subscriptions.md)


[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
