---
title: Azure CLI を使用して Azure Data Explorer クラスターとデータベースを作成する
description: Azure CLI を使用して Azure Data Explorer クラスターとデータベースを作成する方法を学習します
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: bd53a8e29254af617b6cfa68935a191a50fc526c
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326761"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-azure-cli"></a>Azure CLI を使用して Azure Data Explorer クラスターとデータベースを作成する

> [!div class="op_single_selector"]
> * [ポータル](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [ARM テンプレート](create-cluster-database-resource-manager.md)

Azure Data Explorer は、アプリケーション、Web サイト、IoT デバイスなどからの大量のデータ ストリーミングをリアルタイムに分析するためのフル マネージドのデータ分析サービスです。 Azure Data Explorer を使用するには、最初にクラスターを作成し、そのクラスター内に 1 つまたは複数のデータベースを作成します。 その後、クエリを実行できるように、データをデータベースに取り込み (読み込み) ます。 この記事では、Azure CLI を使用して、クラスターとデータベースを 1 つずつ作成します。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、Azure サブスクリプションが必要です。 お持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0.4 以降が必要です。 バージョンを確認するには `az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli?view=azure-cli-latest)に関するページを参照してください。

## <a name="configure-the-cli-parameters"></a>CLI パラメーターを構成する

Azure Cloud Shell でコマンドを実行している場合、次の手順は必要ありません。 ローカル環境で CLI を実行している場合、次の手順に従って Azure にサインインし、現在のサブスクリプションを設定します。

1. 次のコマンドを実行して、Azure にサインインします。

    ```azurecli-interactive
    az login
    ```

1. クラスターを作成するサブスクリプションを設定します。 `MyAzureSub` は、使用する Azure サブスクリプションの名前に置き換えてください。

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Azure Data Explorer クラスターを作成する

1. 次のコマンドを使用して、クラスターを作成します。

    ```azurecli-interactive
    az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
    ```

   |**設定** | **推奨値** | **フィールドの説明**|
   |---|---|---|
   | 名前 | *azureclitest* | クラスターの任意の名前。|
   | sku | *D13_v2* | クラスターに使用される SKU。 |
   | resource-group | *testrg* | クラスターが作成されるリソース グループの名前。 |

    使用できる省略可能なパラメーターが他にも存在します (クラスターの容量など)。

1. クラスターが正常に作成されたかどうかを確認するには、次のコマンドを実行します。

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

結果に値が `Succeeded` の `provisioningState` が含まれている場合、クラスターは正常に作成されています。

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Azure Data Explorer クラスターでデータベースを作成する

1. 次のコマンドを使用して、データベースを作成します。

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period P365D --hot-cache-period P31D
    ```

   |**設定** | **推奨値** | **フィールドの説明**|
   |---|---|---|
   | cluster-name | *azureclitest* | データベースの作成先となるクラスターの名前。|
   | 名前 | *clidatabase* | データベースの名前。|
   | resource-group | *testrg* | クラスターが作成されるリソース グループの名前。 |
   | soft-delete-period | *P365D* | データをクエリに使用できるようにしておく時間を示します。 詳細については、[アイテム保持ポリシー](/azure/kusto/concepts/retentionpolicy)に関するページを参照してください。 |
   | hot-cache-period | *P31D* | データをキャッシュに保持する時間を示します。 詳細については、[キャッシュ ポリシー](/azure/kusto/concepts/cachepolicy)に関するページを参照してください。 |

1. 次のコマンドを実行して、作成したデータベースを確認します。

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

クラスターとデータベースが作成されました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

* 他の記事に進む場合は、作成したリソースをそのままにします。
* リソースをクリーンアップするには、クラスターを削除します。 クラスターを削除するときに、その中に含まれるデータベースもすべて削除されます。 クラスターを削除するには次のコマンドを使います。

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>次の手順

* [Azure Data Explorer の Python ライブラリを使用してデータを取り込む](python-ingest-data.md)
