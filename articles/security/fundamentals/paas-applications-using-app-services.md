---
title: PaaS の Web およびモバイル アプリケーションをセキュリティで保護する
titleSuffix: Azure App Service
description: 'PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティ保護するための、Azure App Service のセキュリティ ベスト プラクティスについて説明します。 '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2019
ms.author: terrylan
ms.openlocfilehash: 849743dd756eff27ec0670cc39d2419c60538183
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94701016"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Azure App Service を使用して PaaS の Web アプリケーションを保護するベスト プラクティス

この記事では、PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティ保護するための、[Azure App Service](../../app-service/overview.md) の一連のセキュリティ ベスト プラクティスについて説明します。 このベスト プラクティスは、Azure に関して Microsoft が蓄積してきたノウハウと、ユーザーの皆様の経験に基づいています。

PaaS (platform-as-a-service) である Azure App Service を使用すると、任意のプラットフォームまたはデバイスを対象とした Web アプリとモバイル アプリを作成し、クラウドやオンプレミスにあるあらゆる場所のデータにアクセスできます。 App Service には、以前は Azure Websites および Azure Mobile Services として個別に提供されていた Web 機能とモバイル機能が含まれています。 さらに、ビジネス プロセスの自動化やクラウド API のホストに利用できる新しい機能も備えています。 単一の統合サービスである App Service により、Web、モバイル、および統合シナリオで豊富な機能セットを利用できます。

## <a name="authenticate-through-azure-active-directory-ad"></a>Azure Active Directory (AD) を使用した認証
App Service は、ID プロバイダーに対して OAuth 2.0 サービスを提供します。 OAuth 2.0 は、Web アプリケーション、デスクトップ アプリケーション、および携帯電話に特定の認証フローを提供しながら、クライアント開発者のシンプル性を実現することに焦点を当てています。 Azure AD が OAuth 2.0 を使用することにより、ユーザーはモバイル アプリケーションと Web アプリケーションへのアクセスを承認することができます。 詳細については、「 [Azure App Service での認証および認可](../../app-service/overview-authentication-authorization.md)」を参照してください。

## <a name="restrict-access-based-on-role"></a>役割に基づいてアクセスを制限する
アクセスの制限は、データ アクセスにセキュリティ ポリシーを適用する必要がある組織にとって、絶対に欠かせないものです。 知る必要性と最低限の特権セキュリティなど、ユーザー、グループ、および特定のスコープ内のアプリケーションに権限を割り当てるには、Azure ロールベースのアクセス制御 (Azure RBAC) を使用できます。 アプリケーションへのアクセスをユーザーに付与する方法の詳細については、「[Azure ロールベースのアクセス制御 (Azure RBAC) とは](../../role-based-access-control/overview.md)」を参照してください。

## <a name="protect-your-keys"></a>キーを保護します
サブスクリプション キーを紛失してしまえば、いかにセキュリティが優れていても関係ありません。 Azure Key Vault は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットをセキュリティで保護するために役立ちます。 Key Vault を使用すると、キーとシークレット (認証キー、ストレージ アカウント キー、データ暗号化キー、PFX ファイル、パスワードなど) をハードウェア セキュリティ モジュール (HSM) で保護されたキーを使用して暗号化できます。 さらに安心感を高めたい場合には、HSM でキーのインポートや生成を行うことができます。 Key Vault を使用して、自動更新で TLS 証明書の管理することもできます。 詳細については、「[Azure Key Vault とは](../../key-vault/general/overview.md)」を参照してください。

## <a name="restrict-incoming-source-ip-addresses"></a>受信ソース IP アドレスを制限します
[App Service 環境](../../app-service/environment/intro.md)には、ネットワーク セキュリティ グループ (NSG) による受信ソース IP アドレスの制限に役立つ、仮想ネットワーク統合機能が用意されています。 Azure Virtual Network (VNET) とは、Azure リソースの多くをインターネット以外のルーティング可能なネットワークに配置できる機能です。配置先のネットワークへのアクセスは制御できます。 詳細については、「[アプリを Azure 仮想ネットワークに統合する](../../app-service/web-sites-integrate-with-vnet.md)」を参照してください。

Windows 上の App Service の場合、web.config を構成して IP アドレスを動的に制限することもできます。詳細については、「[動的 IP セキュリティ](/iis/configuration/system.webServer/security/dynamicIpSecurity/)」を参照してください。


## <a name="next-steps"></a>次のステップ
この記事では、PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティ保護するための、App Service の一連のセキュリティ ベスト プラクティスについて説明しました。 PaaS デプロイのセキュリティ保護の詳細については、次のリンク先をご覧ください。

- [PaaS デプロイをセキュリティで保護する](paas-deployments.md)
- [Azure で Paas データベースをセキュリティ保護する](paas-applications-using-sql.md)