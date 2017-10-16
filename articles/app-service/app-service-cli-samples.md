---
title: "Azure CLI のサンプル - App Service | Microsoft Docs"
description: "Azure CLI のサンプル - App Service"
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 03/08/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 6718694af487929d193dae54ecb2d85ece64887a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cli-samples"></a>Azure CLI のサンプル

次の表には、Azure CLI を使用して構築された Bash スクリプトへのリンクが含まれています。

| | |
|-|-|
|**アプリの作成**||
| [Web アプリを作成して GitHub からコードをデプロイする](./scripts/app-service-cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Azure Web アプリを作成し、パブリックの GitHub リポジトリからコードをデプロイします。 |
| [GitHub からの継続的なデプロイで Web アプリを作成する](./scripts/app-service-cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Azure Web アプリを作成し、所有する GitHub リポジトリから継続的に発行します。 |
| [Web アプリを作成してローカル Git リポジトリからコードをデプロイする](./scripts/app-service-cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Azure Web アプリを作成し、ローカル Git リポジトリからのコードのプッシュを構成します。 |
| [Web アプリを作成してステージング環境にコードをデプロイする](./scripts/app-service-cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | コードの変更をステージングするためのデプロイ スロットを持つ Azure Web アプリを作成します。 |
| [Docker コンテナーに ASP.NET Core Web アプリを作成する](./scripts/app-service-cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json)| Linux 上で Azure Web アプリを作成し、Docker Hub から Docker イメージを読み込みます。 |
|**アプリケーションの構成**||
| [カスタム ドメインを Web アプリにマップする](./scripts/app-service-cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Azure Web アプリを作成し、カスタム ドメイン名をマップします。 |
| [カスタム SSL 証明書を Web アプリにバインドする](./scripts/app-service-cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Azure Web アプリを作成し、カスタム ドメイン名の SSL 証明書をバインドします。 |
|**アプリのスケール**||
| [Web アプリを手動でスケールする](./scripts/app-service-cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Azure Web アプリを作成し、2 つのインスタンス間でスケールします。 |
| [高可用性アーキテクチャを使用して世界規模で Web アプリをスケールする](./scripts/app-service-cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | 2 つの異なる地理的リージョンに 2 つの Azure Web アプリを作成し、Azure Traffic Manager を使用して、1 つのエンドポイントを介して利用できるようにします。 |
|**アプリのリソースへの接続**||
| [Web アプリを SQL Database に接続する](./scripts/app-service-cli-app-service-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Azure Web アプリと SQL データベースを作成し、データベース接続文字列をアプリケーション設定に追加します。 |
| [Web アプリをストレージ アカウントに接続する](./scripts/app-service-cli-app-service-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Azure Web アプリとストレージ アカウントを作成し、ストレージ接続文字列をアプリケーション設定に追加します。 |
| [Web アプリを Redis Cache に接続する](./scripts/app-service-cli-app-service-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Azure Web アプリと Redis Cache を作成し、Redis の接続の詳細をアプリケーション設定に追加します。 |
| [Web アプリと Cosmos DB の接続](./scripts/app-service-cli-app-service-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Azure Web アプリと Cosmos DB を作成し、Cosmos DB の接続の詳細をアプリケーション設定に追加します。 |
|**アプリの監視**||
| [Web サーバー ログによる Web アプリの監視](./scripts/app-service-cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Azure Web アプリを作成し、ログ記録を有効にし、ログをローカル コンピューターにダウンロードします。 |
| | |