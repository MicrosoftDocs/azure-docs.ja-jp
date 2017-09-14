---
title: "Azure PowerShell のサンプル スクリプト - Service Fabric クラスターの作成 | Microsoft Docs"
description: "Azure PowerShell のサンプル スクリプト - Service Fabric クラスターを作成します。"
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 06/20/2017
ms.author: ryanwi
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 7322570c90b3c83fc79d428a7ac0b57ecb2e4b75
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="create-a-service-fabric-cluster"></a>Service Fabric クラスターの作成

このサンプル スクリプトでは、Service Fabric クラスター (X.509 証明書で保護される 5 つのノードのクラスター) を作成します。  コマンドで自己署名証明書を作成し、それを新しい Key Vault にアップロードします。 証明書はローカル ディレクトリにもコピーされます。  *-OS* パラメーターを設定して、クラスター ノードで実行される Windows または Linux のバージョンを選択します。  必要に応じてパラメーターをカスタマイズします。

必要に応じて、[Azure PowerShell ガイド](/powershell/azure/overview)の手順に従って Azure PowerShell をインストールし、`Login-AzureRmAccount` を実行して、Azure との接続を作成します。 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/service-fabric/create-secure-cluster/create-secure-cluster.ps1 "Service Fabric クラスターの作成")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトの実行後、次のコマンドを使用して、リソース グループ、クラスター、およびすべての関連リソースを削除できます。

```powershell
$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) | 新しい Service Fabric クラスターを作成します。 |

## <a name="next-steps"></a>次のステップ

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の Azure Service Fabric 用 Azure PowerShell サンプルは、[Azure PowerShell サンプル](../service-fabric-powershell-samples.md)のページにあります。

