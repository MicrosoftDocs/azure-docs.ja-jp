---
title: Azure CLI (az) と Azure Service Fabric CLI (sfctl) のサンプル
description: クラスター、アプリケーション、およびサービスの管理に関する Azure CLI (az) および Azure Service Fabric CLI (sfctl) のサンプル。
ms.topic: sample
ms.date: 04/09/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 12a48c525d1458b2d920f6a25d91d67e58749a96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87502428"
---
# <a name="azure-cli-az-and-azure-service-fabric-cli-sfctl-samples"></a>Azure CLI (az) と Azure Service Fabric CLI (sfctl) のサンプル

次の表には、Service Fabric クラスター、アプリ、およびサービスを管理する方法に関するサンプルへのリンクが記載されています。

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

| サンプル | 説明 |
|-|-|
| **クラスターの作成** (Azure CLI を使用)||
| [セキュリティで保護された Linux クラスターの作成](./scripts/cli-create-cluster.md)| Azure Service Fabric クラスターを作成します。 |
| **アプリの管理** (Service Fabric CLI を使用)||
| [List applications](./scripts/sfctl-list-applications.md)| クラスターにプロビジョニングされたアプリケーションの一覧を表示します。|
| [アプリケーションをデプロイする](./scripts/cli-deploy-application.md)| クラスターにアプリケーションをデプロイします。|
| [アプリケーションの削除](./scripts/cli-remove-application.md)| クラスターからアプリケーションを削除します。|
| [アプリケーションのアップグレード](./scripts/sfctl-upgrade-application.md)| 既にデプロイされているアプリケーションをアップグレードします。|
