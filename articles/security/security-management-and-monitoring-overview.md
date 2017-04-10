---
title: "Azure セキュリティの管理と監視の概要 | Microsoft Docs"
description: " Azure には、そのクラウド サービスと仮想マシンの管理と監視を支援するセキュリティ メカニズムが備わっています。  この記事では、これらの主要なセキュリティ機能とサービスの概要を示します。 "
services: security
documentationcenter: na
author: TerryLanfear
manager: StevenPo
editor: TomSh
ms.assetid: 5cf2827b-6cd3-434d-9100-d7411f7ed424
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: f0c174d7d845dd7e48de6388270dde2469d89a12
ms.lasthandoff: 03/25/2017


---
# <a name="azure-security-management-and-monitoring-overview"></a>Azure セキュリティの管理と監視の概要
Azure には、そのクラウド サービスと仮想マシンの管理と監視を支援するセキュリティ メカニズムが備わっています。 この記事では、これらの主要なセキュリティ機能とサービスの概要を示します。 それぞれの詳細について説明する記事へのリンクが用意されているため、さらに詳しく学習できます。

Microsoft クラウド サービスのセキュリティは、パートナーシップに基づくものであり、お客様と Microsoft が共有する責任です。 共有責任において、Microsoft は、Microsoft Azure およびそのデータ センターの (入室に ID カードが必要なドア、フェンス、守衛などのセキュリティ保護の使用による) 物理的なセキュリティの責任を負います。 さらに、Azure のソフトウェア レイヤーには強力なレベルのクラウド セキュリティが備わっており、顧客が求めるセキュリティ、プライバシー、およびコンプライアンスの厳しいニーズを満たすことができます。

お客様は、自分のデータと ID を所有し、それらとオンプレミス リソースのセキュリティ、および自分が制御しているクラウド コンポーネントのセキュリティを保護する責任を持ちます。 Microsoft は、セキュリティ制御や、データとアプリケーションを保護するための機能をお客様に提供します。 お客様のセキュリティの責任は、クラウド サービスの種類に応じて決まります。

次の表は、Microsoft とお客様の責任の分担をまとめたものです。

![Shared responsibility][1]

セキュリティ管理の詳細については、「 [Azure のセキュリティ管理](azure-security-management.md)」を参照してください。

この記事では、以下の主要機能について扱います。

* ロールベースのアクセス制御
* マルウェア対策
* Multi-Factor Authentication
* ExpressRoute
* 仮想ネットワーク ゲートウェイ
* Privileged Identity Management
* Identity Protection
* セキュリティ センター

## <a name="role-based-access-control"></a>ロールベースのアクセス制御
ロールベースのアクセス制御 (RBAC) は、Azure リソースのアクセス権を詳細に管理できるようにします。 RBAC を使用すると、職務に必要な範囲のアクセス権だけをユーザーに付与することができます。  RBAC は、ユーザーが組織を離れるときに、クラウド内のリソースへのアクセス権を失うようにするためにも役立ちます。

詳細情報:

* [RBAC に関する Active Directory チームのブログ](http://i1.blogs.technet.com/b/ad/archive/2015/10/12/azure-rbac-is-ga.aspx)
* [Azure のロールベースのアクセス制御](../active-directory/role-based-access-control-configure.md)

## <a name="antimalware"></a>マルウェア対策
Azure では、Microsoft、Symantec、Trend Micro、McAfee、Kaspersky などの主要なセキュリティ ベンダーが提供するマルウェア対策ソフトウェアを利用できます。これにより、悪意のあるファイルやアドウェアなどの脅威から仮想マシンを保護できます。

Microsoft マルウェア対策は、PaaS ロールと仮想マシンの両方のためのマルウェア対策エージェントをインストールする機能を提供します。 この機能は、System Center Endpoint Protection に基づいており、実績のあるオンプレミス セキュリティ テクノロジをクラウドに持ち込みます。

また、Trend の [Deep Security](http://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/)™ と [SecureCloud](http://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/)™ 製品も Azure プラットフォームに緊密に統合されます。 DeepSecurity はウイルス対策ソリューションであり、SecureCloud は暗号化ソリューションです。 DeepSecurity は、拡張機能モデルを使用して、VM の内部にデプロイされます。 ポータル UI と PowerShell を使用すると、スピンアップされている新しい VM 内で DeepSecurity を使用するか、既にデプロイされている既存の VM 内で使用するかを選択することができます。

Symantec End Point Protection (SEP) も、Azure でサポートされています。 ポータルの統合により、お客様は SEP を VM 内で使用するように指定できます。 SEP は、Azure Portal を通じて新規の VM にインストールすることができます。また、PowerShell を使用して既存の VM にインストールすることもできます。

詳細情報:

* [Azure Virtual Machines へのマルウェア対策ソリューションのデプロイ](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Azure Cloud Services および Virtual Machines 向け Microsoft マルウェア対策](azure-security-antimalware.md)
* [Windows VM に Trend Micro Deep Security をサービスとしてインストールし、構成する方法](../virtual-machines/windows/classic/install-trend.md)
* [Windows VM に Symantec Endpoint Protection をインストールし、構成する方法](../virtual-machines/windows/classic/install-symantec.md)
* [New Antimalware Options for Protecting Azure Virtual Machines – McAfee Endpoint Protection (Azure Virtual Machines を保護するための新しいマルウェア対策オプション – McAfee Endpoint Protection)](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication
Azure Multi-Factor Authentication (MFA) は、複数の確認方法の使用を要求することで、ユーザーのサインインとトランザクションに重要な 2 つ目のセキュリティ レイヤーを追加する認証方法です。 MFA では、シンプルなサインイン プロセスを好むユーザーのニーズに応えながら、データやアプリケーションへのアクセスを効果的に保護することができます。 電話やテキスト メッセージ、モバイル アプリによる通知のほか、確認コードやサード パーティの OATH トークンなど、一連の照合方法を通じて確実な認証を行うことができます。

詳細情報:

* [多要素認証](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Azure Multi-Factor Authentication とは](../multi-factor-authentication/multi-factor-authentication.md)
* [Azure Multi-Factor Authentication のしくみ](../multi-factor-authentication/multi-factor-authentication-how-it-works.md)

## <a name="expressroute"></a>ExpressRoute
Microsoft Azure ExpressRoute を利用すれば、接続プロバイダーが提供する専用プライベート接続で、オンプレミスのネットワークを Microsoft クラウドに拡張できます。 ExpressRoute では、Microsoft Azure、Office 365、CRM Online などの Microsoft クラウド サービスへの接続を確立できます。 接続には、任意の環境間 (IP VPN) 接続、ポイントツーポイントのイーサネット接続、共有施設での接続プロバイダーによる仮想交差接続があります。 ExpressRoute 接続では、公共のインターネットを利用できません。 それにより、ExpressRoute 接続はインターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も短く、高速です。

詳細情報:

* [ExpressRoute の技術概要](../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>仮想ネットワーク ゲートウェイ
VPN Gateway (Azure Virtual Netwok ゲートウェイとも呼ばれます) は、仮想ネットワークとオンプレミスの場所の間でネットワーク トラフィックを送信するために使用されます。 また、Azure 内で複数の仮想ネットワーク間のトラフィック送信にも使用されます (VNet 間)。  VPN ゲートウェイは、Azure とお使いのインフラストラクチャの間の安全なクロスプレミス接続を提供します。

詳細情報:

* [VPN ゲートウェイについて](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Azure のネットワーク セキュリティの概要](security-network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management
ユーザーは、Azure のリソース、または他の SaaS アプリケーションで、特権操作を実行することが必要になる場合があります。 通常は、組織がユーザーに Azure Active Directory (Azure AD) で永続的な特権アクセスを付与する必要があります。 しかし、この措置では、ユーザーが特権アクセスを使用して実行している内容を組織が十分に監視できないため、クラウドでホストされているリソースのセキュリティ リスクが増大します。
また、特権アクセスを持つユーザー アカウントが侵害された場合に、その 1 つの侵害がクラウド セキュリティ全体に影響を与える可能性もあります。 Azure AD Privileged Identity Management では、特権の公開期間を短縮し、使用状況の可視性を向上させることによって、このリスクの解決を支援します。  

Privileged Identity Management には、ロールの一時的な管理者または "ジャスト イン タイム" 管理者アクセスという概念が導入されています。一時的な管理者となるユーザーは、割り当てられたロールのアクティブ化プロセスを完了する必要があります。 このアクティブ化プロセスによって、Azure AD におけるユーザーへのロールの割り当てが、8 時間などの指定された期間だけ、非アクティブからアクティブに変更されます。

詳細情報:

* [Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md)
* [Azure AD Privileged Identity Management の使用](../active-directory/active-directory-privileged-identity-management-getting-started.md)

## <a name="identity-protection"></a>Identity Protection
Azure Active Directory (AD) Identity Protection は、ビジネスを保護するために、不審なサインイン アクティビティと潜在的な脆弱性の統合ビューを提供します。 Identity Protection は、ブルート フォース攻撃、資格情報の漏えい、不明な場所および感染したデバイスからのサインインなどの前兆に基づいて、ユーザーや特権 (管理) ID の不審なアクティビティを検出します。

Identity Protection は、通知と推奨される修復を提供することで、リスクをリアルタイムで軽減できるようにします。 ユーザーのリスクの重大度が計算されるので、ユーザーはリスク ベースのポリシーを構成して、アプリケーションのアクセスが将来の脅威から自動的に保護されるようにすることができます。

詳細情報:

* [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md)
* [Channel 9: Azure AD and Identity Show: Identity Protection Preview (Channel 9: Azure AD および Identity ショー: Identity Protection プレビュー)](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>セキュリティ センター
Azure Security Center は、脅威の回避、検出、対応に役立つサービスで、Azure リソースのセキュリティを高度に視覚化して制御できます。 これにより、Azure サブスクリプション全体に統合セキュリティの監視とポリシーの管理を提供し、気付かない可能性がある脅威を検出し、セキュリティ ソリューションの広範なエコシステムと連動します。

Security Center は、Azure リソースのセキュリティの最適化と監視に役立つ次の機能を備えています。

* セキュリティに関する会社のニーズ、および各サブスクリプションでのアプリケーションのタイプやデータの機密度に合わせて、Azure サブスクリプション リソースのポリシーを定義できます。
* Azure 仮想マシン、ネットワーク、およびアプリケーションの状態を監視します。
* 統合されたパートナー ソリューションからの警告など、優先順位の付いたセキュリティの警告の一覧を提供します。また、すぐに調査するために必要な情報や、攻撃を受けたものを修復する方法についての推奨事項も表示します。

詳細情報:

* [Azure Security Center 入門](../security-center/security-center-intro.md)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png

