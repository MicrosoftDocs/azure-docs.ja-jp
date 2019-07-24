---
title: Azure CLI のサンプル スクリプト - アプリのスケジュールされたバックアップを作成する | Microsoft Docs
description: Azure CLI のサンプル スクリプト - アプリのスケジュールされたバックアップを作成する
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu;cephalin
ms.custom: seodec18
ms.openlocfilehash: b5f75ded0b51b78d56354bf97b5ebb0488022509
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53635749"
---
# <a name="create-a-scheduled-backup-for-an-app-service-app-using-cli"></a>CLI を使用して App Service アプリのスケジュールされたバックアップを作成する

このサンプル スクリプトでは、App Service でアプリを関連リソースと共に作成し、アプリのスケジュールされたバックアップを作成します。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合は、Azure CLI バージョン 2.0 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。 

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/backup-scheduled/backup-scheduled.sh?highlight=3-7 "Create a scheduled backup for an app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [`az storage account create`](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) | ストレージ アカウントを作成します。 |
| [`az storage container create`](/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) | Azure ストレージ コンテナーを作成します。 |
| [`az storage container generate-sas`](/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-generate-sas) | Azure ストレージ コンテナーの SAS トークンを生成します。  |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | App Service プランを作成します。 |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | App Service アプリを作成します。 |
| [`az webapp config backup update`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az-webapp-config-backup-update) | App Service アプリに対して新しいバックアップ スケジュールを構成します。 |
| [`az webapp config backup show`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az-webapp-config-backup-show) | App Service アプリのバックアップ スケジュールを表示します。 |
| [`az webapp config backup list`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az-webapp-config-backup-list) | App Service アプリのバックアップの一覧を取得します。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

その他の App Service の CLI サンプル スクリプトは、[Azure App Service のドキュメント](../samples-cli.md)のページにあります。
