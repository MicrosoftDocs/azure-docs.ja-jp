---
title: PowerShell でクラスターにアプリケーションをデプロイする
description: Azure PowerShell のサンプル スクリプト - アプリケーションの Service Fabric クラスターへのデプロイ。
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 145372fa872c481ec1a7c3de016c35fdc0f9d960
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87083805"
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

## <a name="next-steps"></a>次のステップ

Service Fabric PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/service-fabric/overview?view=azureservicefabricps)を参照してください。

その他の Azure Service Fabric 用 PowerShell サンプルは、[Azure PowerShell サンプル](../service-fabric-powershell-samples.md)のページにあります。
