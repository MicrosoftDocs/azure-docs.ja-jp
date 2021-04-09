---
title: 管理および監視セキュリティ機能 - Microsoft Azure | Microsoft Docs
description: この記事では、Azure クラウド サービスと仮想マシンの管理と監視を支援するために Azure が提供するセキュリティ機能とサービスの概要を説明します。
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 5cf2827b-6cd3-434d-9100-d7411f7ed424
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: terrylan
ms.openlocfilehash: f87ea1e1c9f43de4e9e0f94d1cd855615a0a880c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101712047"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Azure セキュリティの管理と監視の概要
この記事では、Azure クラウド サービスと仮想マシンの管理と監視を支援するために Azure が提供するセキュリティ機能とサービスの概要を説明します。

## <a name="azure-role-based-access-control"></a>Azure ロールベースのアクセス制御

Azure ロールベースのアクセス制御 (Azure RBAC) は、Azure リソースのアクセス権を詳細に管理できるようにします。 Azure RBAC を使用すると、職務に必要な範囲のアクセス権だけをユーザーに付与することができます。 Azure RBAC は、ユーザーが組織を離れるときに、クラウド内のリソースへのアクセス権を失うようにするためにも役立ちます。

詳細情報:

* [Azure RBAC に関する Active Directory チームのブログ](https://cloudblogs.microsoft.com/enterprisemobility/?product=azure-active-directory)
* [Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>マルウェア対策

Azure では、Microsoft、Symantec、Trend Micro、McAfee、Kaspersky などの主要セキュリティ ベンダーのマルウェア対策ソフトウェアを使用できます。 このソフトウェアは、悪意のあるファイル、アドウェア、他の脅威から仮想マシンを保護するのに役立ちます。

Azure Cloud Services および Virtual Machines の Microsoft マルウェア対策は、PaaS ロールと仮想マシンの両方のためのマルウェア対策エージェントをインストールする機能を提供します。 この機能は、System Center Endpoint Protection に基づいており、実績のあるオンプレミス セキュリティ テクノロジをクラウドに持ち込みます。

また、Trend の [Deep Security](https://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) と [SecureCloud](https://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) 製品も Azure プラットフォームに緊密に統合されます。 DeepSecurity はウイルス対策ソリューションであり、SecureCloud は暗号化ソリューションです。 DeepSecurity は、拡張機能モデルを使って、VM の内部に展開されます。 Azure portal UI と PowerShell を使うと、スピンアップされている新しい VM 内で DeepSecurity を使うか、既にデプロイされている既存の VM 内で使うかを選択することができます。

Symantec Endpoint Protection (SEP) も、Azure でサポートされています。 ポータルの統合により、お客様は SEP を VM 上で使うように指定できます。 SEP は、Azure Portal を通じて新しい VM にインストールすることができます。また、PowerShell を使って既存の VM にインストールすることもできます。

詳細情報:

* [Azure Virtual Machines へのマルウェア対策ソリューションのデプロイ](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Azure Cloud Services および Virtual Machines 向け Microsoft マルウェア対策](antimalware.md)
* [Windows VM に Trend Micro Deep Security をサービスとしてインストールし、構成する方法](/previous-versions/azure/virtual-machines/extensions/trend)
* [Windows VM に Symantec Endpoint Protection をインストールし、構成する方法](../../virtual-machines/extensions/symantec.md)
* [Azure Virtual Machines を保護するための新しいマルウェア対策オプション](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Azure AD Multi-Factor Authentication は、複数の検証方法の使用を要求する認証の方法です。 ユーザーのサインインとトランザクションに重要な第 2 のセキュリティ レイヤーを追加できます。

Multi-Factor Authentication を使えば、シンプルなサインイン プロセスを好むユーザーのニーズに応えながら、データやアプリケーションへのアクセスを効果的に保護できます。 電話やテキスト メッセージ、モバイル アプリによる通知のほか、確認コードやサード パーティの OATH トークンなど、一連の照合方法を通じて確実な認証を行うことができます。

詳細情報:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Azure AD Multi-Factor Authentication とは](../../active-directory/authentication/concept-mfa-howitworks.md)
* [Azure AD の Multi-Factor Authentication の仕組み](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute

Azure ExpressRoute を使うと、接続プロバイダーが提供する専用プライベート接続で、オンプレミスのネットワークを Microsoft クラウドに拡張できます。 ExpressRoute では、Azure、Microsoft 365、CRM Online などの Microsoft クラウド サービスへの接続を確立できます。 以下のものから接続できます。

* 任意の環境間 (VPN IP) ネットワーク。
* ポイント ツー ポイントのイーサネット ネットワーク。
* コロケーション施設の接続プロバイダー経由での仮想クロス接続。

ExpressRoute 接続はパブリックなインターネットを経由しません。 ExpressRoute 接続は一般的なインターネットでの接続よりも信頼性が高く、高速で、待ち時間が短く、セキュリティの高い接続を提供できます。

詳細情報:

* [ExpressRoute の技術概要](../../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>仮想ネットワーク ゲートウェイ

VPN ゲートウェイ (Azure 仮想ネットワーク ゲートウェイとも呼ばれます) は、仮想ネットワークとオンプレミスの場所の間でネットワーク トラフィックを送信するために使用されます。 また、Azure 内で複数の仮想ネットワーク間のトラフィック送信にも使用されます (ネットワーク間)。 VPN ゲートウェイは、Azure とお使いのインフラストラクチャの間の安全なクロスプレミス接続を提供します。

詳細情報:

* [VPN ゲートウェイについて](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Azure のネットワーク セキュリティの概要](network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

ユーザーは、Azure のリソース、または他の SaaS アプリケーションで、特権操作を実行することが必要になる場合があります。 通常は、組織がユーザーに Azure Active Directory (Azure AD) で永続的な特権アクセスを付与します。

しかし、この措置では、ユーザーが特権アクセスを使用して実行している内容を組織が十分に監視できないため、クラウドでホストされているリソースのセキュリティ リスクが増大します。 また、特権アクセスを持つユーザー アカウントが侵害された場合に、その 1 つの侵害が組織のクラウド セキュリティ全体に影響を与える可能性もあります。 Azure AD Privileged Identity Management では、特権の公開期間を短縮し、使用状況の可視性を向上させることによって、このリスクの解決を支援します。  

Privileged Identity Management は、ロールまたは "ジャスト イン タイム" 管理者アクセス用の一時的管理者の概念を導入します。 この種の管理者は、割り当てられたロールのアクティブ化プロセスを完了する必要があるユーザーです。 このアクティブ化プロセスによって、Azure AD におけるユーザーへのロールの割り当てが、指定された期間だけ、非アクティブからアクティブに変更されます。

詳細情報:

* [Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
* [Azure AD Privileged Identity Management の使用](../../active-directory/privileged-identity-management/pim-getting-started.md)

## <a name="identity-protection"></a>Identity Protection

Azure AD Identity Protection は、ビジネスを保護するために、不審なサインイン アクティビティと潜在的な脆弱性の統合ビューを提供します。 Identity Protection は、次のようなシグナルに基づいて、ユーザーおよび特権 (管理者) ID に対する不審なアクティビティを検出します。

* ブルート フォース攻撃。
* 漏洩した資格情報。
* 未知の場所および感染したデバイスからのサインイン。

Identity Protection は、通知と推奨される修復を提供することで、リスクをリアルタイムで軽減できるようにします。 ユーザー リスクの重大度を計算します。 ユーザーはリスク ベースのポリシーを構成して、アプリケーションのアクセスが将来の脅威から自動的に保護されるようにすることができます。

詳細情報:

* [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md)
* [Channel 9: Azure AD and Identity Show: Identity Protection Preview (Channel 9: Azure AD および Identity ショー: Identity Protection プレビュー)](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Security Center

Azure Security Center は、脅威の防御、検出、対応を可能にする機能です。 Security Center により、Azure リソースやハイブリッド クラウド環境内のリソースのセキュリティの可視性が向上すると共に、そのきめ細かい制御が可能になります。 

Security Center では、接続されているリソースの継続的なセキュリティ評価を実行し、それらの構成やデプロイを [Azure セキュリティ ベンチマーク](../benchmarks/introduction.md)と比較して、その環境向けに調整されたセキュリティに関する詳細な推奨事項を提供します。

Security Center は、Azure リソースのセキュリティの最適化と監視に役立つ次の機能を備えています。

- 以下に従って、Azure サブスクリプション リソースに対するポリシーを定義できるようにします。
    - 組織のセキュリティ ニーズ。
    - 各サブスクリプション内のアプリケーションの種類またはデータの機密度。
    - サブスクリプションに適用する業界または規制の基準やベンチマーク。 
- Azure 仮想マシン、ネットワーク、およびアプリケーションの状態を監視します。
- 統合パートナー ソリューションからのアラートなど、優先度の高いセキュリティ アラートの一覧を提供します。 迅速に攻撃を調査するために必要な情報と、修復方法に関する推奨事項も提供します。

詳細情報:

* [Azure Security Center 入門](../../security-center/security-center-introduction.md)
* [Azure Security Center 上のセキュリティ スコアの向上](../../security-center/secure-score-security-controls.md)

## <a name="intelligent-security-graph"></a>インテリジェント セキュリティ グラフ

インテリジェント セキュリティ グラフは、Microsoft 製品およびサービスでリアルタイムの脅威の防止を行います。 これは、膨大な量の脅威インテリジェンスとセキュリティ データをリンクする高度な分析を使用して、組織のセキュリティを強化することができる分析情報を提供します。 Microsoft はより豊富な分析情報を提供するため、高度な分析を使用しながら、1 か月に 4,500 億件以上の認証を処理したり、4,000 億件以上のメールをスキャンしてマルウェアやフィッシングの有無を確認したり、10 億台以上のデバイスを更新したりしています。 これらの分析情報は、組織が攻撃を検出して迅速に対応することに役立ちます。

* [インテリジェント セキュリティ グラフ](https://www.microsoft.com/security/intelligence)

## <a name="next-steps"></a>次の手順
[共同責任モデル](shared-responsibility.md)について学習します。また、Microsoft が処理するセキュリティ タスクとお客様が処理するタスクについても確認しましょう。

セキュリティの管理について詳しくは、「[Azure のセキュリティ管理](management.md)」をご覧ください。