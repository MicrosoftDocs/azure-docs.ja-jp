---
title: PowerShell でクラスターからアプリケーションを削除する
description: Azure PowerShell のサンプル スクリプト - Service Fabric クラスターからのアプリケーションの削除。
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
ms.openlocfilehash: 686afa791df88382e3e5e1b2d233317c36bf1dd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791308"
---
# <a name="remove-an-application-from-a-service-fabric-cluster-using-powershell"></a>PowerShell を使用して Service Fabric クラスターからアプリケーションを削除する

このサンプル スクリプトは、実行中の Service Fabric アプリケーション インスタンスを削除し、クラスターからアプリケーションの種類とバージョンの登録を解除します。  アプリケーション インスタンスを削除すると、そのアプリケーションに関連付けられている実行中のサービス インスタンスもすべて削除されます。 必要に応じてパラメーターをカスタマイズします。 

必要に応じて、Service Fabric PowerShell モジュールを、[Service Fabric SDK](../service-fabric-get-started.md) と共にインストールします。 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "Remove an application from a cluster")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication) | 実行中の Service Fabric アプリケーション インスタンスをクラスターから削除します。  |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype) | Service Fabric アプリケーションの種類とバージョンの登録をクラスターから解除します。 |

## <a name="next-steps"></a>次のステップ

Service Fabric PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/service-fabric/overview)を参照してください。

その他の Azure Service Fabric 用 PowerShell サンプルは、[Azure PowerShell サンプル](../service-fabric-powershell-samples.md)のページにあります。
