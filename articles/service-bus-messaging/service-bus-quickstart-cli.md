---
title: クイック スタート - Azure CLI を使用して Service Bus キューを作成する | Microsoft Docs
description: このクイックスタートでは、Service Bus 名前空間とその名前空間のキューを Azure CLI を使用して作成する方法について説明します。
author: spelluru
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.openlocfilehash: 14892b114f3bb8a0bb6c07f7cdd01a174b931cf9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95810628"
---
# <a name="use-the-azure-cli-to-create-a-service-bus-namespace-and-a-queue"></a>Azure CLI を使用して Service Bus 名前空間とキューを作成する
このクイックスタートでは、Azure CLI を使用して Service Bus 名前空間とキューを作成する方法について説明します。 また、クライアント アプリケーションがキューとの間でメッセージを送受信するときに使用できる承認の資格情報を取得する方法についても説明します。 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>前提条件
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント][free account]を作成できます。

このクイックスタートでは、Azure portal へのサインイン後に起動できる Azure Cloud Shell を使用します。 Azure Cloud Shell の詳細については、「[Azure Cloud Shell の概要](../cloud-shell/overview.md)」を参照してください。 Azure PowerShell を自分のマシンに[インストール](/cli/azure/install-azure-cli)して使用することもできます。 

## <a name="provision-resources"></a>リソースをプロビジョニングする
1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 次の画像に示すアイコンを選択して Azure Cloud Shell を起動します。 Cloud Shell が **PowerShell** モードになっている場合は **Bash** モードに切り替えます。 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Cloud Shell の起動":::
3. 次のコマンドを実行して、Azure リソース グループを作成します。 必要に応じて、リソース グループの名前と場所を更新します。 

    ```azurecli-interactive
    az group create --name ContosoRG --location eastus
    ```
4. 次のコマンドを実行して、Service Bus メッセージング名前空間を作成します。

    ```azurecli-interactive
    az servicebus namespace create --resource-group ContosoRG --name ContosoSBusNS --location eastus
    ```
5. 次のコマンドを実行して、前の手順で作成した名前空間にキューを作成します。 この例の `ContosoRG` は、前の手順で作成したリソース グループです。 `ContosoSBusNS` は、そのリソース グループに作成した Service Bus 名前空間の名前です。 

    ```azurecli-interactive
    az servicebus queue create --resource-group ContosoRG --namespace-name ContosoSBusNS --name ContosoOrdersQueue
    ```
6. 次のコマンドを実行して、名前空間のプライマリ接続文字列を取得します。 この接続文字列を使用してキューに接続し、メッセージを送受信します。 

    ```azurecli-interactive
    az servicebus namespace authorization-rule keys list --resource-group ContosoRG --namespace-name ContosoSBusNS --name RootManageSharedAccessKey --query primaryConnectionString --output tsv    
    ```

    接続文字列とキュー名を書き留めておきます。 これらの情報を使用して、メッセージを送受信します。 


## <a name="next-steps"></a>次の手順
この記事では、Service Bus 名前空間とその名前空間のキューを作成しました。 キューとの間でメッセージを送受信する方法については、「**メッセージを送受信する**」セクションで次のいずれかのクイックスタートを参照してください。 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

