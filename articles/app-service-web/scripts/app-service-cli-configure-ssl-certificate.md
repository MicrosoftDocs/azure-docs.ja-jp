---
title: "Azure CLI のサンプル スクリプト - Web アプリへのカスタム SSL 証明書のバインド | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - Web アプリへのカスタム SSL 証明書のバインド"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 115749f29d73efdb81f491d9f31687b097f6d270
ms.lasthandoff: 03/21/2017

---

# <a name="bind-a-custom-ssl-certificate-to-a-web-app"></a>カスタム SSL 証明書を Web アプリにバインドする

このサンプル スクリプトは、関連するリソースを使用して App Service に Web アプリを作成し、そこにカスタム ドメイン名の SSL 証明書をバインドします。 

必要に応じて、[Azure CLI インストール ガイド](https://docs.microsoft.com/cli/azure/install-azure-cli)の手順に従って Azure CLI をインストールします。 また、次のことを確認します。

- Azure との接続が、`az login` コマンドを使用して作成されている。
- ドメイン レジストラーの DNS 構成ページにアクセスできる。
- アップロードしてバインドする SSL 証明書の有効な .PFX ファイルとパスワードを持っている。

このサンプルは、bash シェルに対応しています。 Azure CLI スクリプトを Windows クライアントで実行する方法については、[Windows での Azure CLI の実行](../../virtual-machines/virtual-machines-windows-cli-options.md)に関する記事を参照してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "カスタム SSL 証明書を Web アプリにバインドする")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | App Service プランを作成します。 |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Azure Web アプリを作成します。 |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | App Service プランを更新して、価格レベルのスケールを実行します。 |
| [az appservice web config ssl upload](https://docs.microsoft.com/cli/azure/appservice/web/config/ssl#upload) | Web アプリに SSL 証明書をアップロードします。 |
| [az appservice web config ssl bind](https://docs.microsoft.com/en-us/cli/azure/appservice/web/config/ssl#bind) | Web アプリにアップロードした SSL 証明書をバインドします。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の App Service の CLI サンプル スクリプトは、[Azure App Service のドキュメント](../app-service-cli-samples.md)のページにあります。
