---
title: Azure PowerShell を使用して Service Bus キューを作成する
description: このクイックスタートでは、Service Bus 名前空間とその名前空間のキューを Azure PowerShell を使用して作成する方法について説明します。
author: spelluru
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8cf024735c66e6bae9d334e3d8ce8d0a0eed1426
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "95799101"
---
# <a name="use-azure-powershell-to-create-a-service-bus-namespace-and-a-queue"></a>Azure PowerShell を使用して Service Bus 名前空間とキューを作成する
このクイックスタートでは、Azure PowerShell を使用して Service Bus 名前空間とキューを作成する方法について説明します。 また、クライアント アプリケーションがキューとの間でメッセージを送受信するときに使用できる承認の資格情報を取得する方法についても説明します。 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]


## <a name="prerequisites"></a>前提条件

このクイックスタートを完了するには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント][]を作成できます。 

このクイックスタートでは、Azure portal へのサインイン後に起動できる Azure Cloud Shell を使用します。 Azure Cloud Shell の詳細については、「[Azure Cloud Shell の概要](../cloud-shell/overview.md)」を参照してください。 Azure PowerShell を自分のマシンに[インストール](/powershell/azure/install-Az-ps)して使用することもできます。 


## <a name="provision-resources"></a>リソースをプロビジョニングする
1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 次の画像に示すアイコンを選択して Azure Cloud Shell を起動します。 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Cloud Shell の起動":::
3. 下部の Cloud Shell ウィンドウで、**Bash** から **PowerShell** に切り替えます。 

    :::image type="content" source="./media/service-bus-quickstart-powershell/cloud-power-shell.png" alt-text="PowerShell モードに切り替える":::    
4. 次のコマンドを実行して、Azure リソース グループを作成します。 必要に応じて、リソース グループの名前と場所を更新します。 

    ```azurepowershell-interactive
    New-AzResourceGroup –Name ContosoRG –Location eastus
    ```
5. 次のコマンドを実行して、Service Bus メッセージング名前空間を作成します。 この例の `ContosoRG` は、前の手順で作成したリソース グループです。 `ContosoSBusNS` は、そのリソース グループに作成した Service Bus 名前空間の名前です。 

    ```azurepowershell-interactive
    New-AzServiceBusNamespace -ResourceGroupName ContosoRG -Name ContosoSBusNS -Location eastus
    ```
6. 次のコマンドを実行して、前の手順で作成した名前空間にキューを作成します。 

    ```azurepowershell-interactive
    New-AzServiceBusQueue -ResourceGroupName ContosoRG -NamespaceName ContosoSBusNS -Name ContosoOrdersQueue 
    ```
7. 名前空間のプライマリ接続文字列を取得します。 この接続文字列を使用してキューに接続し、メッセージを送受信します。 

    ```azurepowershell-interactive    
    Get-AzServiceBusKey -ResourceGroupName ContosoRG -Namespace ContosoSBusNS -Name RootManageSharedAccessKey
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

[無料アカウント]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

