---
title: Azure CLI のサンプル スクリプト - Function App へのカスタム ドメインのマッピング | Microsoft Docs
description: Azure CLI のサンプル スクリプト - Azure における Function App へのカスタム ドメインのマッピング。
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: d127e347-7581-47d7-b289-e0f51f2fbfbc
ms.service: azure-functions
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/04/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 19fd73dff045d6d784dd70d1f3ad539a22faf413
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075466"
---
# <a name="map-a-custom-domain-to-a-function-app"></a>Function App への カスタム ドメインのマッピング

このサンプル スクリプトは、App Service プランに関数アプリを作成し、指定したカスタム ドメインにそれをマッピングします。 関数アプリが [Premium プラン](../functions-scale.md#premium-plan)または [App Service プラン](../functions-scale.md#app-service-plan)でホストされている場合、CNAME または A レコードを使用してカスタム ドメインをマップできます。 [従量課金プラン](../functions-scale.md#consumption-plan)の関数アプリの場合、CNAME オプションのみがサポートされます。 このサンプルは、App Service プランを作成します。そのドメインにマッピングするための A レコードが必要となります。 

このサンプル スクリプトを実行するには、Web アプリの既定のドメイン名を指し示す A レコードがカスタム ドメインに構成済みであることが必要です。 詳細については、[Azure App Service にカスタム ドメインをマップする方法](https://aka.ms/appservicecustomdns)に関するページを参照してください。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合は、Azure CLI バージョン 2.0 以降のバージョンを使用する必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。 


## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-custom-domain/configure-custom-domain.sh?highlight=3 "Map a custom domain to a function app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは以下のコマンドを使用します。表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Function App に必要なストレージ アカウントを作成します。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az-appservice-plan-create) | カスタム ドメインをマップするのに必要な App Service プランを作成します。 |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | App Service プランで関数アプリを作成します。 |
| [az functionapp config hostname add](https://docs.microsoft.com/cli/azure/functionapp/config/hostname#az-functionapp-config-hostname-add) | カスタム ドメインを Function App にマップします。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

その他の Azure Functions CLI のサンプル スクリプトは、[Azure Functions のドキュメント](../functions-cli-samples.md)で確認できます。
