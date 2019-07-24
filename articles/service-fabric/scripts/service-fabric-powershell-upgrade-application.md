---
title: Azure PowerShell のサンプル スクリプト - Service Fabric アプリケーションのアップグレード | Microsoft Docs
description: Azure PowerShell のサンプル スクリプト - Service Fabric アプリケーションのアップグレード
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 8a291e49272c47035f73ad70534ac5393060940a
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663555"
---
# <a name="upgrade-a-service-fabric-application"></a>Service Fabric アプリケーションのアップグレード

このサンプル スクリプトは、実行中の Service Fabric アプリケーション インスタンスをバージョン 1.3.0 にアップグレードします。 スクリプトは、クラスターのイメージ ストアに新しいアプリケーション パッケージをコピーし、アプリケーションの種類を登録し、不要なアプリケーション パッケージを削除します。  スクリプトは、監視対象のアップグレードを開始し、アップグレードが完了するかロールバックされるまで、アップグレードの状態を継続的に確認します。 必要に応じてパラメーターをカスタマイズします。 

必要に応じて、Service Fabric PowerShell モジュールを、[Service Fabric SDK](../service-fabric-get-started.md) と共にインストールします。 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Upgrade an application")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) | Service Fabric クラスター内のすべてのアプリケーションまたは特定のアプリケーションを取得します。  |
| [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) | Service Fabric アプリケーションのアップグレードの状態を取得します。 |
| [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) | Service Fabric クラスターに登録された Service Fabric アプリケーションの種類を取得します。 |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Service Fabric アプリケーションの種類を登録解除します。  |
| [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Service Fabric アプリケーション パッケージをイメージ ストアにコピーします。  |
| [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) | Service Fabric アプリケーションの種類を登録します。 |
| [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) | Service Fabric アプリケーションを、指定されたアプリケーションの種類のバージョンにアップグレードします。 |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Service Fabric アプリケーション パッケージをイメージ ストアから削除します。|


## <a name="next-steps"></a>次の手順

Service Fabric PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/service-fabric/?view=azureservicefabricps)を参照してください。

その他の Azure Service Fabric 用 PowerShell サンプルは、[Azure PowerShell サンプル](../service-fabric-powershell-samples.md)のページにあります。
