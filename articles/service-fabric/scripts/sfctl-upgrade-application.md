---
title: sfctl でクラスター上のアプリケーション更新する
description: Service Fabric CLI スクリプト サンプル - アプリケーションを新しいバージョンで更新する この例では、デプロイされているアプリケーションも新しいビットでアップグレードされます。
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
ms.date: 12/06/2017
ms.author: atsenthi
ms.custom: ''
ms.openlocfilehash: 34f2ae6f3a2ff3adc35794d6e7dfd682640c646b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "75614725"
---
# <a name="update-an-application-using-the-service-fabric-cli"></a>Service Fabric CLI を使用してアプリケーションを更新する

このサンプル スクリプトは、既存のアプリケーションの新しいバージョンをアップロードしてから、デプロイされているアプリケーションを新しいビットでアップグレードします。

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-sh[main](../../../cli_scripts/service-fabric/upgrade-application/upgrade-application.sh "Upload and update an application on a Service Fabric cluster")]

## <a name="next-steps"></a>次のステップ

詳細については、[Service Fabric CLI ドキュメント](../service-fabric-cli.md)をご覧ください。

その他の Azure Service Fabric 用 Service Fabric CLI サンプルは、[Service Fabric CLI のサンプル](../samples-cli.md)のページにあります。
