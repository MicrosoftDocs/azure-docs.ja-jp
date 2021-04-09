---
title: Azure App Configuration でマネージド ID を構成する
description: マネージド ID が Azure App Configuration でどのように機能するのかと、マネージド ID の構成方法について説明します
author: barbkess
ms.topic: article
ms.date: 02/25/2020
ms.author: barbkess
ms.reviewer: lcozzens
ms.service: azure-app-configuration
ms.openlocfilehash: 5424b776b977d8a6939bbb28c5d4c9c428cac444
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179614"
---
# <a name="how-to-use-managed-identities-for-azure-app-configuration"></a>Azure App Configuration でマネージド ID を使用する方法

このトピックでは、Azure App Configuration のマネージド ID を作成する方法について説明します。 Azure Active Directory (AAD) のマネージド ID により、Azure App Configuration は、Azure Key Vault などの AAD で保護された他のリソースに簡単にアクセスすることができます。 この ID は Azure プラットフォームによって管理されます。 シークレットのプロビジョニングやローテーションは必要ありません。 AAD のマネージド ID については、「[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。

アプリケーションには 2 種類の ID を付与できます。

- **システム割り当て ID** は、構成ストアに関連付けられています。 これは、構成ストアが削除されると削除されます。 構成ストアは 1 つのシステム割り当て ID しか持つことはできません。
- **ユーザー割り当て ID** は、構成ストアに割り当てることができるスタンドアロンの Azure リソースです。 構成ストアは、複数のユーザー割り当て ID を持つことができます。

## <a name="adding-a-system-assigned-identity"></a>システム割り当て ID の追加

システム割り当て ID を持つ App Configuration ストアを作成するには、ストアに追加のプロパティを設定する必要があります。

### <a name="using-the-azure-cli"></a>Azure CLI の使用

Azure CLI を使用してマネージド ID を設定するには、既存の構成ストアに対して [az appconfig identity assign] コマンドを使用します。 このセクションの例を実行するためのオプションとして次の 3 つがあります。

- Azure Portal から [Azure Cloud Shell](../cloud-shell/overview.md) を使用する。
- 以下の各コード ブロックの右上隅にある [使ってみる] ボタンを利用して、埋め込まれた Azure Cloud Shell を使用します。
- ローカルの CLI コンソールを使用する場合は、[Azure CLI の最新バージョン (2.1 以降) をインストール](/cli/azure/install-azure-cli)します。

次の手順では、CLI を使用して、App Configuration ストアを作成し、それに ID を割り当てる方法について説明します。

1. ローカルのコンソールで Azure CLI を使用している場合は、最初に [az login] を使用して Azure にサインインします。 Azure サブスクリプションに関連付けられたアカウントを使用します。

    ```azurecli-interactive
    az login
    ```

1. CLI を使用して App Configuration ストアを作成します。 Azure App Configuration で CLI を使用する方法に関するその他の例については、[App Configuration CLI サンプル](scripts/cli-create-service.md)に関する記事をご覧ください。

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. [az appconfig identity assign] コマンドを実行して、この構成ストアのシステム割り当て ID を作成します。

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup
    ```

## <a name="adding-a-user-assigned-identity"></a>ユーザー割り当て ID の追加

ユーザー割り当て ID を持つ App Configuration ストアを作成するには、ID を作成してから、そのリソース ID をストアに割り当てる必要があります。

### <a name="using-the-azure-cli"></a>Azure CLI の使用

Azure CLI を使用してマネージド ID を設定するには、既存の構成ストアに対して [az appconfig identity assign] コマンドを使用します。 このセクションの例を実行するためのオプションとして次の 3 つがあります。

- Azure Portal から [Azure Cloud Shell](../cloud-shell/overview.md) を使用する。
- 以下の各コード ブロックの右上隅にある [使ってみる] ボタンを利用して、埋め込まれた Azure Cloud Shell を使用します。
- ローカル CLI コンソールを使用する場合、[Azure CLI の最新バージョン (2.0.31 以降) をインストール](/cli/azure/install-azure-cli)します。

次の手順では、CLI を使用して、ユーザー割り当て ID と App Configuration ストアを作成し、ID をストアに割り当てる方法について説明します。

1. ローカルのコンソールで Azure CLI を使用している場合は、最初に [az login] を使用して Azure にサインインします。 Azure サブスクリプションに関連付けられたアカウントを使用します。

    ```azurecli-interactive
    az login
    ```

1. CLI を使用して App Configuration ストアを作成します。 Azure App Configuration で CLI を使用する方法に関するその他の例については、[App Configuration CLI サンプル](scripts/cli-create-service.md)に関する記事をご覧ください。

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. CLI を使用して `myUserAssignedIdentity` というユーザー割り当て ID を作成します。

    ```azurecli-interactive
    az identity create -resource-group myResourceGroup --name myUserAssignedIdentity
    ```

    このコマンドの出力で、`id` プロパティの値をメモしてください。

1. [az appconfig identity assign] コマンドを実行して、新しいユーザー割り当て ID をこの構成ストアに割り当てます。 前の手順でメモした `id` プロパティの値を使用します。

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity
    ```

## <a name="removing-an-identity"></a>ID の削除

システム割り当て ID は、Azure CLI で [az appconfig identity remove](/cli/azure/appconfig/identity#az-appconfig-identity-remove) コマンドを使用してその機能を無効にすることによって削除できます。 ユーザー割り当て ID は個別に削除することはできません。 この方法でシステム割り当て ID を削除すると、AAD からも削除されます。 システム割り当て ID は、アプリ リソースが削除されると、AAD からも自動的に削除されます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure App Configuration を使用して ASP.NET Core アプリを作成する](quickstart-aspnet-core-app.md)

[az appconfig identity assign]: /cli/azure/appconfig/identity#az-appconfig-identity-assign
[az login]: /cli/azure/reference-index#az-login
