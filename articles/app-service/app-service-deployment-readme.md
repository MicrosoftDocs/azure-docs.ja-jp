---
title: "Azure App Service へのアプリケーションのデプロイ"
description: "App Service へのアプリケーションのデプロイ方法について説明します。"
keywords: "App Service, Azure App Service, デプロイ, デプロイ"
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: 
ms.assetid: de12cd6e-e124-4e48-90bc-c3a3801305da
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2016
ms.author: dariagrigoriu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2778057e772fcba7e6021ac11ec5bdcf3720a703
ms.lasthandoff: 11/17/2016


---
# <a name="azure-app-service-deployment-overview"></a>Azure App Service のデプロイの概要
Azure App Service には、強力で柔軟性の高いデプロイ ワークフローの作成を支援する豊富かつ包括的な機能セットが備わっています。 継続的インテグレーション、ローカル ソース管理からの発行、WebDeploy、FTP など各種の方法をアプリのデプロイに利用できます。 運用アプリのデプロイ方法としては、デプロイ スロットの切り替えが推奨されています。 デプロイ スロットは、運用アプリに関連付けられた統合環境とステージング環境を表します。 検証用の Web トラフィックのターゲットとなるデプロイ スロットを構成し、必要に応じてトラフィックを運用環境にスワップしてデプロイすることで、ダウン タイムや自動ウォームアップを回避することができます。 デプロイ ワークフローのステップは、Visual Studio Release Management などのリリース管理製品を使って簡単に自動化できます。 これは、他のソリューション リソース (データ ストアなど) との連携や、定期実行、複数のデプロイ ユニット間のレプリケーションに役立ちます。 

[!INCLUDE [app-service-blueprint-deployment](../../includes/app-service-blueprint-deployment.md)]


