---
title: Azure CLI スクリプトの展開サンプル
description: Azure CLI を使用して Azure にセキュリティで保護された Service Fabric Linux クラスターを作成します。
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 3a50d6672833ab1b4986e38ca1ddf24a8c33c562
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664456"
---
# <a name="create-a-secure-service-fabric-linux-cluster-in-azure"></a>セキュリティで保護された Service Fabric Linux クラスターを Azure に作成する

このコマンドは、自己署名証明書を作成してキー コンテナーに追加し、証明書をローカルでダウンロードします。  新しい証明書はクラスターのデプロイ時にセキュリティで保護するために使用します。  また、新しい証明書を作成する代わりに既存のものを使用することもできます。  いずれにしても、証明書のサブジェクト名は Service Fabric クラスターへのアクセスに使用されるドメインと一致している必要があります。 この整合性は、HTTPS 管理エンドポイントと Service Fabric Explorer 用の SSL を提供するために必要です。 CA から `.cloudapp.azure.com` ドメインの SSL 証明書を取得することはできません。 クラスターのカスタム ドメイン名を取得する必要があります。 CA に証明書を要求するときは、証明書のサブジェクト名がクラスターに使用するカスタム ドメイン名と一致している必要があります。

必要に応じて、[Azure CLI](/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) をインストールしてください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-sh[main](../../../cli_scripts/service-fabric/create-cluster/create-cluster.sh "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

サンプル スクリプトの実行後、次のコマンドを使用して、リソース グループ、クラスター、およびすべての関連リソースを削除できます。

```azurecli
ResourceGroupName = "aztestclustergroup"
az group delete --name $ResourceGroupName
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az sf cluster create](https://docs.microsoft.com/cli/azure/sf/cluster?view=azure-cli-latest) | 新しい Service Fabric クラスターを作成します。  |

## <a name="next-steps"></a>次の手順

その他の Azure Service Fabric 用 Service Fabric CLI サンプルは、[Service Fabric CLI のサンプル](../samples-cli.md)のページにあります。
