---
title: "Azure PowerShell のサンプル スクリプト - Service Fabric アプリケーションのアップグレード | Microsoft Docs"
description: "Azure PowerShell のサンプル スクリプト - Service Fabric アプリケーションのアップグレード"
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
ms.date: 08/23/2017
ms.author: ryanwi
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 43c1c0d38d12a36c39a3962a3399d9c05937e8b1
ms.contentlocale: ja-jp
ms.lasthandoff: 08/24/2017

---

# <a name="upgrade-a-service-fabric-application"></a>Service Fabric アプリケーションのアップグレード

このサンプル スクリプトは、実行中の Service Fabric アプリケーション インスタンスをバージョン 1.3.0 にアップグレードします。 このスクリプトでは、新しいアプリケーション パッケージをクラスター イメージ ストアにコピーし、アプリケーションの種類を登録し、監視付きアップグレードを開始し、アップグレードが完了またはロールバックするまでアップグレードの状態を継続的に確認します。 必要に応じてパラメーターをカスタマイズします。 

必要に応じて、Service Fabric PowerShell モジュールを、[Service Fabric SDK](../service-fabric-get-started.md) と共にインストールします。 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[メイン](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "アプリケーションのアップグレード")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) | Service Fabric クラスター内のすべてのアプリケーションまたは特定のアプリケーションを取得します。  |
| [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) | Service Fabric アプリケーションのアップグレードの状態を取得します。 |
| [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) | Service Fabric クラスターに登録された Service Fabric アプリケーションの種類を取得します。 |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Service Fabric アプリケーションの種類を登録解除します。  |
| [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Service Fabric アプリケーション パッケージをイメージ ストアにコピーします。  |
| [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) | Service Fabric アプリケーションの種類を登録します。 |
| [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) | Service Fabric アプリケーションを、指定されたアプリケーションの種類のバージョンにアップグレードします。 |


## <a name="next-steps"></a>次のステップ

Service Fabric PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/service-fabric/?view=azureservicefabricps)を参照してください。

その他の Azure Service Fabric 用 PowerShell サンプルは、[Azure PowerShell サンプル](../service-fabric-powershell-samples.md)のページにあります。

