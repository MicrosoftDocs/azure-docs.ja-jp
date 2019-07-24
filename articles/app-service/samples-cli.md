---
title: Azure CLI のサンプル - App Service | Microsoft Docs
description: Azure CLI のサンプル - App Service
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
ms.date: 12/12/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: fe5649951b1b19ce52c13648f897f4a83e1f761b
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53628376"
---
# <a name="cli-samples-for-azure-app-service"></a>Azure App Service の CLI サンプル

次の表には、Azure CLI を使用して構築された Bash スクリプトへのリンクが含まれています。

| | |
|-|-|
|**アプリの作成**||
| [アプリを作成し、FTP を使用してファイルをデプロイする](./scripts/cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| App Service アプリを作成し、FTP を使用してファイルをデプロイします。 |
| [アプリを作成して GitHub からコードをデプロイする](./scripts/cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| App Service アプリを作成し、パブリックの GitHub リポジトリからコードをデプロイします。 |
| [GitHub からの継続的なデプロイでアプリを作成する](./scripts/cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| App Service アプリを作成し、所有する GitHub リポジトリから継続的に発行します。 |
| [アプリを作成してローカル Git リポジトリからコードをデプロイする](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | App Service アプリを作成し、ローカル Git リポジトリからのコードのプッシュを構成します。 |
| [アプリを作成してステージング環境にコードをデプロイする](./scripts/cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | コードの変更をステージングするためのデプロイ スロットを備える App Service アプリを作成します。 |
| [Docker コンテナーに ASP.NET Core アプリを作成する](./scripts/cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json)| Linux 上で App Service アプリを作成し、Docker Hub から Docker イメージを読み込みます。 |
|**アプリケーションの構成**||
| [アプリにカスタム ドメインをマップする](./scripts/cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| App Service アプリを作成し、カスタム ドメイン名をマップします。 |
| [カスタム SSL 証明書をアプリにバインドする](./scripts/cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| App Service アプリを作成し、カスタム ドメイン名の SSL 証明書をバインドします。 |
|**アプリのスケール**||
| [アプリを手動でスケーリングする](./scripts/cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | App Service アプリを作成し、2 つのインスタンス間でスケーリングします。 |
| [高可用性アーキテクチャを使用して世界規模でアプリをスケーリングする](./scripts/cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | 2 つの異なる地理的リージョンに 2 つの App Service アプリを作成し、Azure Traffic Manager を使用して、1 つのエンドポイントを介してそれらを利用できるようにします。 |
|**アプリのリソースへの接続**||
| [アプリを SQL Database に接続する](./scripts/cli-connect-to-sql.md?toc=%2fcli%2fazure%2ftoc.json)| App Service アプリと SQL データベースを作成し、データベース接続文字列をアプリ設定に追加します。 |
| [アプリをストレージ アカウントに接続する](./scripts/cli-connect-to-storage.md?toc=%2fcli%2fazure%2ftoc.json)| App Service アプリとストレージ アカウントを作成し、ストレージ接続文字列をアプリ設定に追加します。 |
| [Azure Cache for Redis にアプリを接続する](./scripts/cli-connect-to-redis.md?toc=%2fcli%2fazure%2ftoc.json) | App Service アプリと Azure Cache for Redis を作成し、Redis の接続の詳細をアプリ設定に追加します。 |
| [Cosmos DB にアプリを接続する](./scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | App Service アプリと Cosmos DB を作成し、Cosmos DB の接続の詳細をアプリ設定に追加します。 |
|**アプリのバックアップと復元**||
| [アプリをバックアップする](./scripts/cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | App Service アプリを作成し、そのアプリの 1 回限りのバックアップを作成します。 |
| [アプリのスケジュールされたバックアップを作成する](./scripts/cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | App Service アプリを作成し、そのアプリのスケジュールされたバックアップを作成します。 |
| [アプリをバックアップから復元する](./scripts/cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | App Service アプリをバックアップから復元します。 |
|**アプリの監視**||
| [Web サーバー ログを使用してアプリを監視する](./scripts/cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | App Service アプリを作成し、ログ記録を有効にし、ログをローカル コンピューターにダウンロードします。 |
| | |