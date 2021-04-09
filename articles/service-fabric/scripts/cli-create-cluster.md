---
title: Azure CLI スクリプトの展開サンプル
description: Azure Command Line Interface (CLI) を使用して、Azure でセキュリティで保護された Service Fabric Linux クラスターを作成する方法。
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: b69f62f6f47c656921f4f2230a296e293e423fa4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035294"
---
# <a name="create-a-secure-service-fabric-linux-cluster-in-azure"></a>セキュリティで保護された Service Fabric Linux クラスターを Azure に作成する

このコマンドは、自己署名証明書を作成してキー コンテナーに追加し、証明書をローカルでダウンロードします。  新しい証明書はクラスターのデプロイ時にセキュリティで保護するために使用します。  また、新しい証明書を作成する代わりに既存のものを使用することもできます。  いずれにしても、証明書のサブジェクト名は Service Fabric クラスターへのアクセスに使用されるドメインと一致している必要があります。 この整合性は、HTTPS 管理エンドポイントと Service Fabric Explorer 用の TLS を提供するために必要です。 CA から `.cloudapp.azure.com` ドメインの TLS/SSL 証明書を取得することはできません。 クラスターのカスタム ドメイン名を取得する必要があります。 CA に証明書を要求するときは、証明書のサブジェクト名がクラスターに使用するカスタム ドメイン名と一致している必要があります。

必要に応じて、[Azure CLI](/cli/azure/install-azure-cli) をインストールしてください。

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

| コマンド | Notes |
|---|---|
| [az sf cluster create](/cli/azure/sf/cluster) | 新しい Service Fabric クラスターを作成します。  |

## <a name="next-steps"></a>次のステップ

その他の Azure Service Fabric 用 Service Fabric CLI サンプルは、[Service Fabric CLI のサンプル](../samples-cli.md)のページにあります。
