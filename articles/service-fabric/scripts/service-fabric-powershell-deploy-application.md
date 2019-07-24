---
title: Azure PowerShell のサンプル スクリプト - アプリケーションのクラスターへのデプロイ | Microsoft Docs
description: Azure PowerShell のサンプル スクリプト - アプリケーションの Service Fabric クラスターへのデプロイ。
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
ms.openlocfilehash: e205315530b0dc89037c1253c571c72c55f00a67
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661430"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Service Fabric クラスターへのアプリケーションのデプロイ

このサンプル スクリプトは、アプリケーション パッケージをクラスター イメージ ストアにコピーし、クラスターにそのアプリケーションの種類を登録し、不要なアプリケーション パッケージを削除し、そのアプリケーションの種類からアプリケーション インスタンスを作成します。  ターゲット アプリケーションの種類のアプリケーション マニフェストで既定のサービスが定義されている場合、それらのサービスも同時に作成されます。 必要に応じてパラメーターをカスタマイズします。 

必要に応じて、Service Fabric PowerShell モジュールを、[Service Fabric SDK](../service-fabric-get-started.md) と共にインストールします。 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトの実行後、「[アプリケーションの削除](service-fabric-powershell-remove-application.md)」のスクリプトを使用して、アプリケーション インスタンスを削除したり、アプリケーションの種類を登録解除したり、アプリケーション パッケージをイメージ ストアから削除したりできます。

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
|[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)| Service Fabric クラスターに接続します。 |
|[Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | アプリケーション パッケージをクラスター イメージ ストアにコピーします。  |
|[Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)| クラスターにアプリケーションの種類とバージョンを登録します。 |
|[New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)| 登録されているアプリケーションの種類からアプリケーションを作成します。 |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Service Fabric アプリケーション パッケージをイメージ ストアから削除します。|

## <a name="next-steps"></a>次の手順

Service Fabric PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/service-fabric/?view=azureservicefabricps)を参照してください。

その他の Azure Service Fabric 用 PowerShell サンプルは、[Azure PowerShell サンプル](../service-fabric-powershell-samples.md)のページにあります。
