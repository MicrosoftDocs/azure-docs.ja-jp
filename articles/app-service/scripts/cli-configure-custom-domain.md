---
title: Azure CLI のサンプル スクリプト - アプリへのカスタム ドメインのマッピング | Microsoft Docs
description: Azure CLI のサンプル スクリプト - アプリへのカスタム ドメインのマッピング
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 5ac4a680-cc73-4578-bcd6-8668c08802c2
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 58d13498e1b86421ae04f5a3aa60cf0c44242a39
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53629137"
---
# <a name="map-a-custom-domain-to-an-app-service-app-using-cli"></a>CLI を使用してカスタム ドメインを App Service アプリにマップする

このサンプル スクリプトでは、App Service でアプリを関連リソースと合わせて作成し、`www.<yourdomain>` を作成したアプリにマップします。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合は、Azure CLI バージョン 2.0 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/configure-custom-domain/configure-custom-domain.sh?highlight=3 "Map a custom domain to an app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | App Service プランを作成します。 |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | App Service アプリを作成します。 |
| [`az webapp config hostname add`](/cli/azure/webapp/config/hostname?view=azure-cli-latest#az-webapp-config-hostname-add) | カスタム ドメインを App Service アプリにマップします。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

その他の App Service の CLI サンプル スクリプトは、[Azure App Service のドキュメント](../samples-cli.md)のページにあります。
