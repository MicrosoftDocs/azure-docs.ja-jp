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
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: antisch
ms.openlocfilehash: cbfe8ab565ecf7f298a9a6c0f0c8298c675f178c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="adding-applications-to-azure-batch-with-azure-cli"></a>Azure CLI を使用した Azure Batch へのアプリケーションの追加

このスクリプトでは、Azure Batch プールまたはタスクで使用するアプリケーションを設定する方法について説明します。 アプリケーションを設定するには、実行ファイルをすべての依存関係と共に .zip ファイルにパッケージ化します。 この例では、実行可能な zip ファイル名を 'my-application-exe.zip' とします。

## <a name="prerequisites"></a>前提条件

- Azure CLI をまだインストールしていない場合は、[Azure CLI インストール ガイド](https://docs.microsoft.com/cli/azure/install-azure-cli)の手順に従ってインストールします。
- Batch アカウントをまだお持ちでない場合は、作成します。 アカウント作成のサンプル スクリプトについては、「[Azure CLI で Batch アカウントを作成する](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-create-account)」を参照してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

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
| [az batch application create](https://docs.microsoft.com/cli/azure/batch/application#az_batch_application_create) | アプリケーションを作成します。  |
| [az batch application set](https://docs.microsoft.com/cli/azure/batch/application#az_batch_application_set) | アプリケーションのプロパティを更新します。  |
| [az batch application package create](https://docs.microsoft.com/cli/azure/batch/application/package#az_batch_application_package_create) | アプリケーション パッケージを指定されたアプリケーションに追加します。  |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の Batch CLI サンプル スクリプトは、[Azure Batch CLI のドキュメント](../batch-cli-samples.md)のページにあります。
