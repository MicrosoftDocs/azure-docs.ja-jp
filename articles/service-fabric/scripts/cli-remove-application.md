---
title: Azure Service Fabric CLI (sfctl) スクリプトの削除のサンプル
description: Azure Service Fabric CLI を使用した Azure Service Fabric クラスターからのアプリケーションの削除
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
ms.date: 12/06/2017
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: fd5d20ed3f2cc41f4d7d51f06d4bc90a6afd22eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "75526534"
---
# <a name="remove-an-application-from-a-service-fabric-cluster-using-the-service-fabric-cli"></a>Service Fabric CLI を使用して Service Fabric クラスターからアプリケーションを削除する

このサンプル スクリプトは、実行中の Service Fabric アプリケーション インスタンスを削除し、クラスターからアプリケーションの種類とバージョンの登録を解除します。  アプリケーション インスタンスを削除すると、そのアプリケーションに関連付けられている実行中のサービス インスタンスもすべて削除されます。 次に、アプリケーション ファイルがイメージ ストアから削除されます。 

必要に応じて、[Service Fabric CLI](../service-fabric-cli.md) をインストールします。

## <a name="sample-script"></a>サンプル スクリプト

[!code-sh[main](../../../cli_scripts/service-fabric/remove-application/remove-application.sh "Remove an application from a cluster")]

## <a name="next-steps"></a>次のステップ

詳細については、[Service Fabric CLI ドキュメント](../service-fabric-cli.md)をご覧ください。

その他の Azure Service Fabric 用 Service Fabric CLI サンプルは、[Service Fabric CLI のサンプル](../samples-cli.md)のページにあります。
