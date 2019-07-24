---
title: Azure CLI のサンプル スクリプト - アプリへのカスタム SSL 証明書のバインド | Microsoft Docs
description: Azure CLI のサンプル スクリプト - アプリへのカスタム SSL 証明書のバインド
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 89483c6396ea344a035ec22bf79e2f90dada4c96
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53634645"
---
# <a name="bind-a-custom-ssl-certificate-to-an-app-service-app-using-cli"></a>CLI を使用してカスタム SSL 証明書を App Service アプリにバインドする

このサンプル スクリプトでは、App Service でアプリを関連リソースと合わせて作成し、カスタム ドメイン名の SSL 証明書をバインドします。 このサンプルでは、以下が必要です。

* ドメイン レジストラーの DNS 構成ページへのアクセス許可。
* アップロードしてバインドする SSL 証明書の有効な .PFX ファイルとパスワード。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合は、Azure CLI バージョン 2.0 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to an app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | App Service プランを作成します。 |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | App Service アプリを作成します。 |
| [`az webapp config hostname add`](/cli/azure/webapp/config/hostname?view=azure-cli-latest#az-webapp-config-hostname-add) | カスタム ドメインを App Service アプリにマップします。 |
| [`az webapp config ssl upload`](/cli/azure/webapp/config/ssl?view=azure-cli-latest#az-webapp-config-ssl-upload) | SSL 証明書を App Service アプリにアップロードします。 |
| [`az webapp config ssl bind`](/cli/azure/webapp/config/ssl?view=azure-cli-latest#az-webapp-config-ssl-bind) | アップロードした SSL 証明書を App Service アプリにバインドします。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

その他の App Service の CLI サンプル スクリプトは、[Azure App Service のドキュメント](../samples-cli.md)のページにあります。
