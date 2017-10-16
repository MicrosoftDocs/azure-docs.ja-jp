---
title: "Azure Service Fabric CLI スクリプトのデプロイのサンプル"
description: "Azure Service Fabric CLI を使用して Azure にセキュリティで保護された Service Fabric Linux クラスターを作成します。"
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 09/18/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 571d1d599508f09aff4dde27dffcf0f8e3c6a7d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-secure-service-fabric-linux-cluster-in-azure"></a>セキュリティで保護された Service Fabric Linux クラスターを Azure に作成する

このコマンドは、自己署名証明書を作成してキー コンテナーに追加し、証明書をローカルでダウンロードします。  新しい証明書はクラスターのデプロイ時にセキュリティで保護するために使用します。  また、新しい証明書を作成する代わりに既存のものを使用することもできます。  いずれにしても、証明書のサブジェクト名は Service Fabric クラスターへのアクセスに使用されるドメインと一致している必要があります。 この整合性は、HTTPS 管理エンドポイントと Service Fabric Explorer 用の SSL を提供するために必要です。 CA から `.cloudapp.azure.com` ドメインの SSL 証明書を取得することはできません。 クラスターのカスタム ドメイン名を取得する必要があります。 CA に証明書を要求するときは、証明書のサブジェクト名がクラスターに使用するカスタム ドメイン名と一致している必要があります。

必要に応じて、[Azure CLI 2.0](/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) をインストールしてください。

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

| コマンド | メモ |
|---|---|
| [az sf cluster create](https://docs.microsoft.com/en-us/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) | 新しい Service Fabric クラスターを作成します。  |

## <a name="next-steps"></a>次のステップ

その他の Azure Service Fabric 用 Service Fabric CLI サンプルは、[Service Fabric CLI のサンプル](../samples-cli.md)のページにあります。
