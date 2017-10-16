---
title: "Azure CLI Redis Cache のサンプル | Microsoft Docs"
description: "Azure Redis Cache 用の Azure CLI サンプル。"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 8d2de145-50c0-4f76-bf8f-fdf679f03698
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.date: 04/14/2017
ms.author: sdanie
ms.openlocfilehash: a0f3b294f2a655a5ff891d4fd1be9137080349a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cli-samples-for-azure-redis-cache"></a>Azure Redis Cache 用の Azure CLI サンプル

次の表には、Azure CLI を使用して構築された Bash スクリプトへのリンクが含まれています。

| | |
|---|---|
|**キャッシュの作成**||
| [キャッシュを作成する](./scripts/create-cache.md) | リソース グループと Basic レベルの Azure Redis Cache を作成します。 |
| [クラスタリングを使用した Premium キャッシュの作成](./scripts/create-premium-cache-cluster.md) | リソース グループとクラスタリングが有効になっている Premium レベルのキャッシュを作成します。|
| [キャッシュの詳細を取得する](./scripts/show-cache.md) | プロビジョニングの状態を含む、Azure Redis Cache インスタンスの詳細を取得します。 |
| [ホスト名、ポート、およびキーを取得する](./scripts/cache-keys-ports.md) | Azure Redis Cache インスタンスのホスト名、ポート、およびキーを取得します。 |
|**Web アプリとキャッシュ**||
| [Web アプリを Redis Cache に接続する](./../app-service/scripts/app-service-cli-app-service-redis.md) | Azure Web アプリと Redis Cache を作成し、Redis の接続の詳細をアプリケーション設定に追加します。 |
|**キャッシュの削除**||
| [キャッシュを削除する](./scripts/delete-cache.md) | Azure Redis Cache インスタンスを削除します。  |
| | |

Azure CLI 2.0 の詳細については、[Azure CLI 2.0 のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)と[Azure CLI 2.0 の使用開始](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)を参照してください。