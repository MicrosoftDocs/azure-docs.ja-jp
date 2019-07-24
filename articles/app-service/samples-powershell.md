---
title: Azure PowerShell のサンプル - App Service | Microsoft Docs
description: Azure PowerShell のサンプル - App Service
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 03/08/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 69fd27785e5fc16a79fc23728b6d1e50a0a7b834
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53632859"
---
# <a name="powershell-samples-for-azure-app-service"></a>Azure App Service の PowerShell サンプル

次の表には、Azure PowerShell を使用して構築された PowerShell スクリプトへのリンクが含まれています。

| | |
|-|-|
|**アプリの作成**||
| [GitHub からのデプロイでアプリを作成する](./scripts/powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| GitHub からコードをプルする App Service アプリを作成します。 |
| [GitHub からの継続的なデプロイでアプリを作成する](./scripts/powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| GitHub から継続的にコードをデプロイする App Service アプリを作成します。 |
| [アプリを作成し、FTP を使用してコードをデプロイする](./scripts/powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | App Service アプリを作成し、FTP を使用してローカル ディレクトリからファイルをアップロードします。 |
| [アプリを作成してローカル Git リポジトリからコードをデプロイする](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | App Service アプリを作成し、ローカル Git リポジトリからのコードのプッシュを構成します。 |
| [アプリを作成してステージング環境にコードをデプロイする](./scripts/powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | コードの変更をステージングするためのデプロイ スロットを備える App Service アプリを作成します。 |
|**アプリケーションの構成**||
| [アプリにカスタム ドメインをマップする](./scripts/powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| App Service アプリを作成し、カスタム ドメイン名をマップします。 |
| [カスタム SSL 証明書をアプリにバインドする](./scripts/powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| App Service アプリを作成し、カスタム ドメイン名の SSL 証明書をバインドします。 |
|**アプリのスケール**||
| [アプリを手動でスケーリングする](./scripts/powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | App Service アプリを作成し、2 つのインスタンス間でスケーリングします。 |
| [高可用性アーキテクチャを使用して世界規模でアプリをスケーリングする](./scripts/powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 2 つの異なる地理的リージョンに 2 つの App Service アプリを作成し、Azure Traffic Manager を使用して、1 つのエンドポイントを介してそれらを利用できるようにします。 |
|**アプリのリソースへの接続**||
| [アプリを SQL Database に接続する](./scripts/powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| App Service アプリと SQL データベースを作成し、データベース接続文字列をアプリ設定に追加します。 |
| [アプリをストレージ アカウントに接続する](./scripts/powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| App Service アプリとストレージ アカウントを作成し、ストレージ接続文字列をアプリ設定に追加します。 |
|**アプリのバックアップと復元**||
| [アプリをバックアップする](./scripts/powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | App Service アプリを作成し、そのアプリの 1 回限りのバックアップを作成します。 |
| [アプリのスケジュールされたバックアップを作成する](./scripts/powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | App Service アプリを作成し、そのアプリのスケジュールされたバックアップを作成します。 |
| [アプリのバックアップを削除する](./scripts/powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | アプリの既存のバックアップを削除します。 |
| [アプリをバックアップから復元する](./scripts/powershell-backup-restore.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 以前に完了したバックアップからアプリを復元します。 |
| [複数のサブスクリプションにわたってバックアップを復元する](./scripts/powershell-backup-restore-diff-sub.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Web アプリを他のサブスクリプション内のバックアップから復元します。 |
|**アプリの監視**||
| [Web サーバー ログを使用してアプリを監視する](./scripts/powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | App Service アプリを作成し、ログ記録を有効にし、ログをローカル コンピューターにダウンロードします。 |
| | |
