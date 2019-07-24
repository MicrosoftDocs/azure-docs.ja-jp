---
title: Azure Databricks から Python を使用して Azure Data Explorer に接続する
description: このトピックでは、Azure Databricks で Python ライブラリを使用し、2 つの認証方法のいずれかを使って Azure Data Explorer からデータにアクセスする方法を示します。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 55257d441916971b505432247f28033d6222c3be
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59789959"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks-by-using-python"></a>Azure Databricks から Python を使用して Azure Data Explorer に接続する

[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) は、Microsoft Azure プラットフォーム用に最適化された Apache Spark ベースの分析プラットフォームです。 この記事では、Azure Databricks で Python ライブラリを使用して、Azure Data Explorer からデータにアクセスする方法を示します。 デバイスのログインや Azure Active Directory (Azure AD) アプリを含め、Azure Data Explorer での認証方法はいくつかあります。

## <a name="prerequisites"></a>前提条件

- [Azure Data Explorer クラスターとデータベースを作成します](/azure/data-explorer/create-cluster-database-portal)。
- [Azure Databricks ワークスペースを作成します](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace)。 **[Azure Databricks サービス]** の **[価格レベル]** ドロップダウン リストから **[Premium]** を選択します。 この選択により、Azure Databricks のシークレットを使用して資格情報を格納し、それらをノートブックとジョブで参照できます。

- Azure Databricks で以下の仕様 (サンプルのノートブックを実行するために必要な最小限の設定) を使用して、[クラスターを作成します](https://docs.azuredatabricks.net/user-guide/clusters/create.html)。

   ![クラスターを作成するための仕様](media/connect-from-databricks/databricks-create-cluster.png)

## <a name="install-the-python-library-on-your-azure-databricks-cluster"></a>Azure Databricks クラスター上に Python ライブラリをインストールする

Azure Databricks クラスター上に [Python ライブラリ](/azure/kusto/api/python/kusto-python-client-library)をインストールするには:

1. Azure Databricks ワークスペースに移動し、[ライブラリを作成します](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)。
2. [Python PyPI パッケージまたは Python Egg をアップロードします](https://docs.azuredatabricks.net/user-guide/libraries.html#upload-a-python-pypi-package-or-python-egg)。
   - ライブラリをアップロードし、インストールして、Databricks クラスターに接続します。
   - **azure-kusto-data** という PyPi 名を入力します。

## <a name="connect-to-azure-data-explorer-by-using-a-device-login"></a>デバイスのログインを使用して Azure Data Explorer に接続する

[Query-ADX-device-login](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-device-login.ipynb) ノートブックを使用して、[ノートブックをインポート](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook)します。 その後、ご自身の資格情報を使用して Azure Data Explorer に接続できます。

## <a name="connect-to-adx-by-using-an-azure-ad-app"></a>Azure AD アプリを使用して ADX に接続する

1. [Azure AD アプリをプロビジョニングする](/azure/kusto/management/access-control/how-to-provision-aad-app)ことによって、Azure AD アプリを作成します。
1. 次のように、Azure Data Explorer データベース上の Azure AD アプリへのアクセスを許可します。

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | データベース名 |
    | ```AAD App ID``` | Azure AD アプリ ID |
    | ```AAD Tenant ID``` | Azure AD テナント ID |

### <a name="find-your-azure-ad-tenant-id"></a>Azure AD テナント ID を見つける

Azure Data Explorer では、アプリケーションを認証するために Azure AD テナント ID が使用されます。 テナント ID を見つけるには、次の URL を使用します。 *YourDomain* をご利用のドメインに置き換えてください。

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

たとえば、ドメインが *contoso.com* の場合、URL は [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/) になります。 結果を表示するには、この URL を選択します。 最初の行は次のようになります。 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

テナント ID は `6babcaad-604b-40ac-a9d7-9fd97c0b779f` です。 

### <a name="store-and-secure-your-azure-ad-app-id-and-key"></a>Azure AD アプリの ID とキーを格納してセキュリティで保護する 

次のように、Azure Databricks の[シークレット](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets)を使用して、Azure AD アプリの ID とキーを格納し、セキュリティで保護します。
1. [CLI を設定します](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli)。
1. [CLI をインストールします](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli)。 
1. [認証を設定します](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication)。
1. 次のサンプル コマンドを使用して、[シークレット](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets)を構成します。

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="import-a-notebook"></a>ノートブックをインポートする
[Query-ADX-AAD-App](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-AAD-App.ipynb) ノートブックを使用して Azure Data Explorer に接続することで、[ノートブックをインポートします](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook)。 プレースホルダーの値は、ご利用のクラスター名、データベース名、および Azure AD テナント ID で更新してください。
