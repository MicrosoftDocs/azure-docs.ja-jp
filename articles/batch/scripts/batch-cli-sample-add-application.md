---
title: "Azure CLI スクリプト サンプル - バッチへのアプリケーションの追加 |Microsoft Docs"
description: "Azure CLI スクリプト サンプル - バッチへのアプリケーションの追加"
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/20/2017
ms.author: antisch
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 342cc2c93304f7a8f651769139e28ebf2e30d412
ms.lasthandoff: 03/24/2017

---

# <a name="adding-applications-to-azure-batch-with-azure-cli"></a>Azure CLI を使用した Azure Batch へのアプリケーションの追加

このスクリプトでは、Azure Batch プールまたはタスクで使用するアプリケーションを設定する方法について説明します。 アプリケーションを設定するには、実行ファイルをすべての依存関係と共に .zip ファイルにパッケージ化します。 この例では、実行可能な zip ファイル名を 'my-application-exe.zip' とします。
このスクリプトの実行では、Batch アカウントが既に設定されていることを想定しています。 詳細については、[Batch アカウントを作成するためのサンプル スクリプト](./batch-cli-sample-create-account.md)に関するページをご覧ください。

必要に応じて、[Azure CLI インストール ガイド](https://docs.microsoft.com/cli/azure/install-azure-cli)に関するページの手順に従って Azure CLI をインストールし、`az login` を実行して Azure にログインします。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/batch/add-application/add-application.sh "アプリケーションの追加")]

## <a name="clean-up-application"></a>アプリケーションのクリーンアップ

上記のサンプル スクリプトを実行したら、次のコマンドを実行して、アプリケーションとアップロードされたすべてのアプリケーション パッケージを削除します。

```azurecli
az batch application package delete -g myresourcegroup -n mybatchaccount --application-id myapp --version 1.0 --yes
az batch application delete -g myresourcegroup -n mybatchaccount --application-id myapp --yes
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは次のコマンドを使用して、アプリケーションを作成し、アプリケーション パッケージをアップロードします。
表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az batch application create](https://docs.microsoft.com/cli/azure/batch/application#create) | アプリケーションを作成します。  |
| [az batch application set](https://docs.microsoft.com/cli/azure/batch/application#set) | アプリケーションのプロパティを更新します。  |
| [az batch application package create](https://docs.microsoft.com/cli/azure/batch/application/package#create) | アプリケーション パッケージを指定されたアプリケーションに追加します。  |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の Batch CLI サンプル スクリプトは、[Azure Batch CLI のドキュメント](../batch-cli-samples.md)のページにあります。

