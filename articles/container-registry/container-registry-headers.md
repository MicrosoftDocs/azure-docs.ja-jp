---
title: "Azure Container Registry リポジトリ |Microsoft Docs"
description: "Docker イメージ用に Azure Container Registry リポジトリを使用する方法"
services: container-registry
documentationcenter: 
author: cristy
manager: balans
editor: dlepow
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/22/2017
ms.author: cristyg
ms.openlocfilehash: 2090d4c951e2261529bf1b7b361510d5822060a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-container-registry-repositories"></a>Azure Container Registry リポジトリ

Azure Container Registries とさまざまなサービスやオーケストレーターとの間に互換性があります。 ACR のソースであるサービスやエージェントを追跡記録しやすくするために、Docker.config ファイルで Docker ヘッダー フィールドを使用するようにしました。



## <a name="viewing-repositories-in-the-portal"></a>Portal でのリポジトリの表示

ACR ヘッダーの形式:
```
X-Meta-Source-Client: <cloud>/<service>/<optionalservicename>
```

* Cloud: Azure、Azure Stack、その他の政府機関向けまたは国向け Azure クラウド。 Azure Stack と政府機関向けクラウドは現在のところサポートされていませんが、このパラメーターで将来のサポートが有効になります。
* Service: サービスの名前。
* Optionalservicename: サブサービスが含まれるサービスのための、あるいは SKU を指定するための任意のパラメーター (例: Azure/app-service/web-apps に対応する Web アプリ)。

Microsoft の利用統計情報収集を支援していただくため、パートナーのサービスやオーケストレーターには、特定のヘッダー値の利用を推奨しています。 ユーザーは、必要に応じてヘッダーに渡される値を変更することもできます。

"X-Meta-Source-Client" フィールドに入力する目的で、ACR パートナーに Microsoft が使用を希望している値:

| サービス名              | ヘッダー                                |
| ------------------------- | ------------------------------------- |
| Azure Container Service   | azure/compute/azure-container-service |
| App Service - Web Apps    | azure/app-service/web-apps            |
| App Service - Logic Apps  | azure/app-service/logic-apps          |
| Batch                     | azure/compute/batch                   |
| Cloud Console             | azure/cloud-console                   |
| 関数                 | azure/compute/functions               |
| モノのインターネット (IoT) - ハブ  | azure/iot/hub                         |
| HDInsight                 | azure/data/hdinsight                  |
| Jenkins                   | azure/jenkins                         |
| Machine Learning          | azure/data/machile-learning           |
| Service Fabric            | azure/compute/service-fabric          |
| VSTS                      | azure/vsts                            |


## <a name="next-steps"></a>次のステップ
[レジストリとサポートされているサービスまたはオーケストレーターについて学習してください。](container-registry-intro.md)
