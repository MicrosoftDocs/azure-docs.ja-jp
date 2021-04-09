---
title: sfctl でクラスター上のアプリケーションを一覧表示する
description: Service Fabric CLI スクリプト サンプル - Service Fabric クラスターにプロビジョニングされているアプリケーションを列挙します。
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 04/13/2018
ms.author: atsenthi
ms.custom: ''
ms.openlocfilehash: 41685e53eb0915f54bdc2d678191e5b767990dde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "75610269"
---
# <a name="list-applications-running-in-a-service-fabric-cluster"></a>Service Fabric クラスターで実行されているアプリケーションを列挙する

このサンプル スクリプトは、Service Fabric クラスターに接続し、プロビジョニングされているすべてのアプリケーションを列挙します。

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-sh[main](../../../cli_scripts/service-fabric/list-application/list-application.sh "List provisioned applications from a cluster")]

## <a name="next-steps"></a>次のステップ

詳細については、[Service Fabric CLI ドキュメント](../service-fabric-cli.md)をご覧ください。

その他の Azure Service Fabric 用 Service Fabric CLI サンプルは、[Service Fabric CLI のサンプル](../samples-cli.md)のページにあります。
