---
title: Service Fabric CLI スクリプト サンプル - クラスター上のアプリケーションを列挙する
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
ms.devlang: na
ms.topic: sample
ms.date: 04/13/2018
ms.author: atsenthi
ms.custom: ''
ms.openlocfilehash: ba88be001702abc09cbdf6a08a61d41953f69f73
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2019
ms.locfileid: "68600098"
---
# <a name="list-applications-running-in-a-service-fabric-cluster"></a>Service Fabric クラスターで実行されているアプリケーションを列挙する

このサンプル スクリプトは、Service Fabric クラスターに接続し、プロビジョニングされているすべてのアプリケーションを列挙します。

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-sh[main](../../../cli_scripts/service-fabric/list-application/list-application.sh "List provisioned applications from a cluster")]

## <a name="next-steps"></a>次の手順

詳細については、[Service Fabric CLI ドキュメント](../service-fabric-cli.md)をご覧ください。

その他の Azure Service Fabric 用 Service Fabric CLI サンプルは、[Service Fabric CLI のサンプル](../samples-cli.md)のページにあります。
