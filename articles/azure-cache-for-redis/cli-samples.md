---
title: Azure CLI を使用した Azure Cache for Redis の管理
description: Azure Cache for Redis を管理するための Azure CLI サンプル:キャッシュの作成、キャッシュの削除、キャッシュの詳細、ホスト名、ポート、およびキーの取得、Web アプリの接続。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/14/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9bfdd2d03b3ab6edd04a641787475930435a9ffc
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87499604"
---
# <a name="manage-azure-cache-for-redis-with-azure-cli"></a>Azure CLI を使用した Azure Cache for Redis の管理

次の表には、Azure CLI を使用して構築された Bash スクリプトへのリンクが含まれています。

| キャッシュの作成 | 説明 |
| ------------ | ----------- |
| [キャッシュを作成する](./scripts/create-cache.md) | リソース グループと Basic レベルの Azure Cache for Redis を作成します。 |
| [クラスタリングを使用した Premium キャッシュの作成](./scripts/create-premium-cache-cluster.md) | リソース グループとクラスタリングが有効になっている Premium レベルのキャッシュを作成します。|
| [キャッシュの詳細を取得する](./scripts/show-cache.md) | プロビジョニングの状態を含む、Azure Cache for Redis インスタンスの詳細を取得します。 |
| [ホスト名、ポート、およびキーを取得する](./scripts/cache-keys-ports.md) | Azure Cache for Redis インスタンスのホスト名、ポート、およびキーを取得します。 |
|**Web アプリとキャッシュ**| **説明**|
| [Azure Cache for Redis に Web アプリを接続する](./../app-service/scripts/cli-connect-to-redis.md) | Azure Web アプリと Azure Cache for Redis を作成し、Redis の接続の詳細をアプリケーション設定に追加します。 |
|**キャッシュの削除**| **説明** |
| [キャッシュを削除する](./scripts/delete-cache.md) | Azure Cache for Redis インスタンスを削除する  |

Azure CLI の詳細については、[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)と[Azure CLI の概要](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)に関するぺージを参照してください。
