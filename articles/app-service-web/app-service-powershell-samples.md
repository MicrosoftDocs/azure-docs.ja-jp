---
title: "Azure PowerShell のサンプル - App Service | Microsoft Docs"
description: "Azure PowerShell のサンプル - App Service"
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: app-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 03/08/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: c8167eaeff389a1bb77a8f13e522b1a2ce58aff8
ms.lasthandoff: 03/10/2017


---
# <a name="azure-powershell-samples"></a>Azure PowerShell のサンプル

次の表には、Azure PowerShell を使用して構築された Bash スクリプトへのリンクが含まれています。

| | |
|-|-|
|**アプリの作成**||
| [GitHub からのデプロイで Web アプリを作成する](./scripts/app-service-powershell-deploy-github.md)| GitHub からコードを取得する Azure Web アプリを作成します。 |
| [GitHub からの継続的なデプロイで Web アプリを作成する](./scripts/app-service-powershell-continuous-deployment-github.md)| GitHub から継続的にコードをデプロイする Azure Web アプリを作成します。 |
| [Web アプリを作成し、FTP を使用してコードをデプロイする](./scripts/app-service-powershell-deploy-ftp.md) | Azure Web アプリを作成し、FTP を使用してローカル ディレクトリからファイルをアップロードします。 |
| [Web アプリを作成してローカル Git リポジトリからコードをデプロイする](./scripts/app-service-powershell-deploy-local-git.md) | Azure Web アプリを作成し、ローカル Git リポジトリからのコードのプッシュを構成します。 |
| [Web アプリを作成してステージング環境にコードをデプロイする](./scripts/app-service-powershell-deploy-staging-environment.md) | コードの変更をステージングするためのデプロイ スロットを持つ Azure Web アプリを作成します。 |
|**アプリケーションの構成**||
| [カスタム ドメインを Web アプリにマップする](./scripts/app-service-powershell-configure-custom-domain.md)| Azure Web アプリを作成し、カスタム ドメイン名をマップします。 |
| [カスタム SSL 証明書を Web アプリにバインドする](./scripts/app-service-powershell-configure-ssl-certificate.md)| Azure Web アプリを作成し、カスタム ドメイン名の SSL 証明書をバインドします。 |
|**アプリのスケール**||
| [Web アプリを手動でスケールする](./scripts/app-service-powershell-scale-manual.md) | Azure Web アプリを作成し、2 つのインスタンス間でスケールします。 |
| [高可用性アーキテクチャを使用して世界規模で Web アプリをスケールする](./scripts/app-service-powershell-scale-high-availability.md) | 2 つの異なる地理的リージョンに&2; つの Azure Web アプリを作成し、Azure Traffic Manager を使用して、1 つのエンドポイントを介して利用できるようにします。 |
|**アプリのリソースへの接続**||
| [Web アプリを SQL Database に接続する](./scripts/app-service-powershell-connect-to-sql.md)| Azure Web アプリと SQL データベースを作成し、データベース接続文字列をアプリケーション設定に追加します。 |
| [Web アプリをストレージ アカウントに接続する](./scripts/app-service-powershell-connect-to-storage.md)| Azure Web アプリとストレージ アカウントを作成し、ストレージ接続文字列をアプリケーション設定に追加します。 |
|**アプリの監視**||
| [Web サーバー ログによる Web アプリの監視](./scripts/app-service-powershell-monitor.md) | Azure Web アプリを作成し、ログ記録を有効にし、ログをローカル コンピューターにダウンロードします。 |
| | |
