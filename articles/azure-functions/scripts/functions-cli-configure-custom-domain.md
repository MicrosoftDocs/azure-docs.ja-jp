---
title: "Azure CLI のサンプル スクリプト - Function App へのカスタム ドメインのマッピング | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - Azure における Function App へのカスタム ドメインのマッピング。"
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: d127e347-7581-47d7-b289-e0f51f2fbfbc
ms.service: functions
ms.workload: na
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 06/01/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 5a17830ad3b879382b7e82d435f1286c7ecdf7c5
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

---
# <a name="map-a-custom-domain-to-a-function-app"></a>Function App への カスタム ドメインのマッピング

このサンプル スクリプトでは、Function App を関連リソースと共に作成し、`www.<yourdomain>` にマップします。 カスタム ドメインにマップするには、Function App を従量課金プランではなく、App Service プランで作成する必要があります。 Azure Functions は、A レコードを使用したカスタム ドメインのマッピングのみをサポートします。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合は、Azure CLI バージョン 2.0 以降のバージョンを使用する必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 


## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-custom-domain/configure-custom-domain.sh?highlight=3 "カスタム ドメインを Function App にマップする")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは次のコマンドを使用します。表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#create) | Function App に必要なストレージ アカウントを作成します。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | カスタム ドメインをマップするのに必要な App Service プランを作成します。 |
| [az functionapp create]() | Function App を作成します。 |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | カスタム ドメインを Function App にマップします。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の Azure Functions CLI のサンプル スクリプトは、[Azure Functions のドキュメント]()で確認できます。

