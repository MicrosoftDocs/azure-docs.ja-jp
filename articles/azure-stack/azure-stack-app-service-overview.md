---
title: "App Service の概要: Azure Stack | Microsoft Docs"
description: "Azure Stack 上の App Service の概要"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/3/2017
ms.author: anwestg
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: 13928744e7d2fc145662c2a0d5c26d512cf02150
ms.contentlocale: ja-jp
ms.lasthandoff: 09/15/2017

---

# <a name="app-service-on-azure-stack-overview"></a>Azure Stack 上の App Service の概要

Azure Stack 上の Azure App Service は、Azure Stack に提供される Azure サービスです。 Azure Stack 上の App Service インストーラーは、次の一連のロール インスタンスを作成します。

*  コントローラー
*  管理 (2 つのインスタンスが作成されます)
*  FrontEnd
*  発行元
*  ワーカー (共有モードの場合)

さらに、Azure Stack 上の App Service インストーラーはファイル サーバーを作成します。
    
## <a name="whats-new-in-the-first-release-candidate-of-app-service-on-azure-stack"></a>Azure Stack 上の App Service の最初のリリース候補の新機能
![Azure Stack ポータルの App Service][1]

Azure Stack 上の App Service の最初のリリース候補は 3 番目のプレビューに基づくものであり、次の新機能と強化機能を提供します。

* Active Directory フェデレーション サービス (AD FS) に基づく Azure Stack 環境における Azure Functions 
* Functions ポータルと高度な開発者ツール (Kudu) のシングル サインオン サポート
* Web、モバイル、および API アプリケーションの Java サポート
* サービス管理者用のスケール アウト機能を向上させるための仮想マシン スケール セットによるワーカー層の管理
* 管理者エクスペリエンスのローカライズ
* サービスの安定性の向上
* テナント ポータル エクスペリエンスの更新とインストール プロセスの更新

## <a name="limitations-of-the-technical-preview"></a>テクニカル プレビューの制限事項

Azure Stack 上の App Service プレビュー リリースのサポートはありませんが、Azure Stack MSDN フォーラムは監視されています。 このプレビュー リリースに実稼働ワークロードをかけないでださい。 また、Azure Stack 上の App Service プレビュー リリース間のアップグレードはありません。 これらのプレビュー リリースの主な目的は、提供内容を示すことと、フィードバックを得ることです。 

## <a name="what-is-an-app-service-plan"></a>App Service プランとは

App Service のリソース プロバイダーは、Azure App Service で使用するものと同じコードを使用します。 したがって、一部の一般的な概念のみを示します。 App Service では、アプリケーションの価格コンテナーを App Service プランと呼びます。 アプリを保持するために使用される専用の仮想マシンのセットを表します。 特定のサブスクリプション内で、複数の App Service プランを利用できます。 

Azure には、共有ワーカーと専用ワーカーがあります。 共有ワーカーは高密度のマルチテナント アプリ ホスティングをサポートします。この共有ワーカーは 1 セットしかありません。 専用サーバーは 1 つのテナントでのみ使用され、小、中、大という 3 つのサイズがあります。 オンプレミスの顧客のニーズは、必ずしもこれらの用語を使用して説明されるわけではありません。 Azure Stack 上の App Service では、リソース プロバイダー管理者は、使用可能にするワーカー層を定義できます。 管理者は、独自のホスティング ニーズに基づき、複数の共有ワーカー セットや、異なる専用ワーカー セットを定義できます。 これらのワーカー層の定義を使用することで、独自の価格 SKU を定義できます。

## <a name="portal-features"></a>ポータルの機能

Azure Stack 上の App Service では、Azure App Service と同じ UI が使用されます。これはバックエンドに当てはまります。 Azure Stack では一部の機能が無効になり、機能しません。 Azure Stack では、このような機能に必要な Azure 固有の期待値やサービスはまだ使用できません。 

## <a name="next-steps"></a>次のステップ

- [Azure Stack 上の App Service を開始する前に](azure-stack-app-service-before-you-get-started.md)
- [App Service リソース プロバイダーのインストール](azure-stack-app-service-deploy.md)

[SQL Server リソース プロバイダー](azure-stack-sql-resource-provider-deploy.md)や [MySQL リソース プロバイダー](azure-stack-mysql-resource-provider-deploy.md)など、他の[サービスとしてのプラットフォーム (PaaS) サービス](azure-stack-tools-paas-services.md)を試すこともできます。

<!--Image references-->
[1]: ./media/azure-stack-app-service-overview/AppService_Portal.png

