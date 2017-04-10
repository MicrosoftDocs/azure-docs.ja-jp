---
title: "Azure App Service を使用して PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティで保護する | Microsoft Docs"
description: " PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティ保護するための、Azure App Services のセキュリティ ベスト プラクティスについて説明します。 "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: 41528ad70598907759cdf149777195007296fe98
ms.lasthandoff: 03/22/2017


---
# <a name="securing-paas-web-and-mobile-applications-using-azure-app-services"></a>Azure App Service を使用して PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティで保護する

この記事では、PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティ保護するための、[Azure App Service](https://azure.microsoft.com/services/app-service/) の一連のセキュリティ ベスト プラクティスについて説明します。 このベスト プラクティスは、Azure に関して Microsoft が蓄積してきたノウハウと、ユーザーの皆様の経験に基づいています。

## <a name="azure-app-services"></a>Azure App Service
PaaS である [Azure App Service](../app-service/app-service-value-prop-what-is.md) を使用すると、任意のプラットフォームまたはデバイスを対象とした Web アプリとモバイル アプリを作成し、クラウドやオンプレミスにあるあらゆる場所のデータにアクセスできます。 App Services には、以前は Azure Websites および Azure Mobile Services として個別に提供されていた Web 機能とモバイル機能が含まれています。 さらに、ビジネス プロセスの自動化やクラウド API のホストに利用できる新しい機能も備えています。 単一の統合サービスである App Services により、Web、モバイル、および統合シナリオで豊富な機能セットを利用できます。

詳細については、[Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) と [Web Apps](../app-service-web/app-service-web-overview.md) の概要を参照してください。

## <a name="best-practices"></a>ベスト プラクティス

App Services を使用する場合は、次のベスト プラクティスに従ってください。

- [Azure Active Directory (AD) を使用した認証](../app-service-web/web-sites-authentication-authorization.md#authenticate-through-azure-active-directory)。 App Services は、ID プロバイダーに対して OAuth 2.0 サービスを提供します。 OAuth 2.0 は、Web アプリケーション、デスクトップ アプリケーション、および携帯電話に特定の認証フローを提供しながら、クライアント開発者のシンプル性を実現することに焦点を当てています。 Azure AD が OAuth 2.0 を使用することにより、ユーザーはモバイル アプリケーションと Web アプリケーションへのアクセスを承認することができます。
- 知る必要性と最小権限という 2 つのセキュリティ原則に基づいて、アクセスを制限します。 アクセスの制限は、データ アクセスにセキュリティ ポリシーを適用する必要がある組織にとって、絶対に欠かせないものです。 ロールベースのアクセス制御 (RBAC) を使用して、特定のスコープ内のユーザー、グループ、アプリケーションにアクセス許可を割り当てることができます。 ユーザーへのアプリケーション アクセス付与の詳細については、[アクセス管理の概要](../active-directory/role-based-access-control-what-is.md)に関するページをご覧ください。
- キーを保護します。 サブスクリプション キーを紛失してしまえば、いかにセキュリティが優れていても関係ありません。 Azure Key Vault は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットをセキュリティで保護するために役立ちます。 Key Vault を使用すると、キーとシークレット (認証キー、ストレージ アカウント キー、データ暗号化キー、PFX ファイル、パスワードなど) をハードウェア セキュリティ モジュール (HSM) で保護されたキーを使用して暗号化できます。 さらに安心感を高めたい場合には、HSM でキーのインポートや生成を行うことができます。 詳細については、「[Azure Key Vault とは](../key-vault/key-vault-whatis.md)」を参照してください。 Key Vault を使用して、自動更新で TLS 証明書の管理することもできます。
- 受信ソース IP アドレスを制限します。 App Services には、ネットワーク セキュリティ グループ (NSG) による受信ソース IP アドレスの制限に役立つ、仮想ネットワーク統合機能が用意されています。 Azure Virtual Network (VNET) とは、Azure リソースの多くをインターネット以外のルーティング可能なネットワークに配置できる機能です。配置先のネットワークへのアクセスは制御できます。 詳細については、「[アプリを Azure 仮想ネットワークに統合する](../app-service-web/web-sites-integrate-with-vnet.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
この記事では、PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティ保護するための、App Services の一連のセキュリティ ベスト プラクティスについて説明しました。 PaaS デプロイのセキュリティ保護の詳細については、次のリンク先をご覧ください。

- [PaaS デプロイをセキュリティで保護する](security-paas-deployments.md)
- [Azure SQL Database と SQL Data Warehouse を使用して PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティで保護する](security-paas-applications-using-sql.md)

