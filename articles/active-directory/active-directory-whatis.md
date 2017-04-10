---
title: "Azure Active Directory とは"
description: "Azure Active Directory を使用すると、既存のオンプレミス ID をクラウドに拡張したり、Azure AD 統合アプリケーションを開発したりすることができます。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: b40ae90ea313638cbd0b60792dc4803d3d08aa0a
ms.openlocfilehash: 03c1442daf07f57476af64491229f1f38f6ffeff
ms.lasthandoff: 02/24/2017


---
# <a name="what-is-azure-active-directory"></a>Azure Active Directory とは
Azure Active Directory (Azure AD) は、マイクロソフトが提供する、マルチテナントに対応したクラウド ベースのディレクトリと ID の管理サービスです。

IT 管理者は、Azure AD により、Office365、Salesforce.com、DropBox、Concur など、 [さまざまなクラウド型 SaaS アプリケーション](active-directory-saas-tutorial-list.md) へのシングル サインオン (SSO) アクセスを従業員やビジネス パートナーに提供する使いやすいソリューションを手軽な価格で手に入れることができます。

また Azure AD は、世界中の数多くの組織が使用する卓越した ID 管理ソリューションとすばやく簡単に統合できるため、アプリケーション開発者がアプリケーションの構築に集中することを可能にします。

また Azure AD には、Multi-Factor Authentication、デバイスの登録、セルフサービスのパスワード管理、セルフサービスのグループ管理、特権を持つアカウントの管理、ロール ベースのアクセス制御、アプリケーション使用状況の監視、機能豊富な監査とセキュリティの監視、アラートなど、一連の ID 管理機能も用意されています。 これらの機能により、クラウド ベース アプリケーションのセキュリティ保護、IT プロセスの効率化、コストの削減を実現し、企業のコンプライアンス目標を確実に満たします。

さらに、わずか [4 回のクリック](./connect/active-directory-aadconnect-get-started-express.md)で Azure AD を既存の Windows Server Active Directory と統合できるため、組織は既存のオンプレミス ID への投資を活用して、クラウド ベースの SaaS アプリケーションへのアクセスを管理できます。

Office365、Azure、Dynamics CRM Online をご利用の方は、既に Azure AD を使用していることを認識していない可能性もあります。 実際に、Office365、Azure、Dynamics CRM のテナントは、いずれも既に Azure AD テナントとなっています。 必要なときはいつでも、そのテナントを使用して、Azure AD と統合するその他さまざまなクラウド アプリケーションへのアクセスを管理できます。

![Azure AD Connect Stack](./media/active-directory-whatis/Azure_Active_Directory.png)

## <a name="how-reliable-is-azure-ad"></a>Azure AD の信頼性
Azure AD はマルチテナント型で地理的に分散した高可用性のデザインです。つまり Azure AD は、最も重要なビジネス ニーズでも信頼できるツールです。 Azure AD は、フェールオーバーが自動化された世界 28 か所のデータ センターから実行されているため、Azure AD の信頼性が高く、データ センターがダウンした場合でも、地理的に分散した 2 か所以上のデータ センターでディレクトリ データのデータが有効で、すぐにアクセスできるという安心感が得られます。

詳細については、「 [サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)」を参照してください。

## <a name="what-are-the-benefits-of-azure-ad"></a>Azure AD の利点について
組織が Azure AD を使用すると、次のさまざまな方法で、従業員の生産性の向上、IT プロセスの効率化、セキュリティの強化、コストの削減を実現できます。

* クラウド サービスをすばやく採用し、Azure AD の完全に自動化された SaaS アプリのアクセス管理機能とプロビジョニング サービス機能により、従業員やパートナーが簡単にシングル サインオンできるようにします。
* 従業員が、使いたいデバイスで、どこからでも、卓越したクラウド アプリやセルフサービス機能にアクセスすることを可能にします。
* 企業のソーシャル メディア アカウントに対する従業員とベンダーのアクセスを簡単かつ安全に管理します。
* Azure AD の多要素認証と条件付きアクセスにより、アプリケーションのセキュリティを強化します。
* 一貫したセルフサービスのアプリケーション アクセスの管理を実装することで、経営者は IT コストとオーバーヘッドを削減しながら機動性を強化できます。
* セキュリティのレポート作成と監視機能により、アプリケーションの使用状況を監視し、高度な脅威からビジネスを保護します。
* オンプレミスのアプリケーションへのセキュリティで保護されたモバイル (リモート) アクセスを実現します。

## <a name="how-does-azure-ad-compare-to-on-premises-active-directory-domain-services-ad-ds"></a>Azure AD とオンプレミスの Active Directory Domain Services (AD DS) との比較

Azure Active Directory (Azure AD) とオンプレミスの Active Directory (Active Directory Domain Services or AD DS) は、両方ともディレクトリ データを格納し、ユーザーとリソース間の通信を管理するシステムであり、ユーザーのログイン プロセス、認証、およびディレクトリ検索を実行します。

AD DS は、Windows Server 上のサーバー ロールであり、物理または仮想マシンに展開できます。 X.500 に基づく階層構造になっています。 オブジェクトの検索では DNS を使用し、LDAP を使用して対話でき、認証では主に Kerberos を使用します。 Active Directory では、ドメインにマシンを参加させるだけでなく、組織単位 (OU) とグループ ポリシー オブジェクト (GPO) を使用できます。ドメイン間に信頼関係が作成されます。

Azure AD は、複数の顧客向けのパブリック ディレクトリ サービスであり、クラウド サーバーのテナントと Office 365 などのアプリケーションを Azure AD 内に作成できます。 ユーザーとグループは、OU も GPO もないフラットな構造で作成されます。 認証は、SAML、WS-Federation、OAuth などのプロトコルを通して実行されます。 Azure AD のクエリを実行できますが、LDAP を使用する代わりに、AD Graph API と呼ばれる REST API を使用する必要があります。 これらのすべてが、HTTP と HTTPS 経由で機能します。




## <a name="how-can-i-get-started"></a>利用を始めるには?

**IT 管理者の方:**

* [実際に使ってみる](https://azure.microsoft.com/trial/get-started-active-directory/) - 30 日間無料試用版に今すぐサインアップすると、このリンクを使って、初めてのクラウド ソリューションを 5 分程度でデプロイできます

* Azure AD の概要に関するページで、Azure AD テナントをすばやく稼働させるためのヒントとテクニックをお読みください。

**開発者の方:**
 
* 「 [Azure Active Directory 開発者ガイド](active-directory-developers-guide.md) 」を参照してください。

* [試用版の開始](https://azure.microsoft.com/trial/get-started-active-directory/) – 30 日の無料試用版に今すぐサインアップして、Azure AD とアプリの統合を開始してください。

## <a name="where-can-i-learn-more"></a>詳細情報を得るには?
Azure AD について理解を深めていただけるよう、豊富なオンライン リソースをご用意しています。 理解を深めるのに適した優れた記事を以下にリストしましたので、ご活用ください。

* [Azure AD Connect によるディレクトリのハイブリッド管理を有効にする](active-directory-aadconnect.md)
* [多要素認証とは](../multi-factor-authentication/multi-factor-authentication.md)
* [Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](active-directory-saas-app-provisioning.md)
* [Azure AD レポートの概要](active-directory-reporting-getting-started.md)
* [任意の場所からのパスワードの管理](active-directory-passwords.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)
* [Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](active-directory-saas-app-provisioning.md)
* [オンプレミス アプリケーションへの安全なリモート アクセスを実現する方法](active-directory-application-proxy-get-started.md)
* [Azure Active Directory グループによるリソースへのアクセス管理](active-directory-manage-groups.md)
* [Microsoft Azure Active Directory ライセンスとは](active-directory-licensing-what-is.md)
* [自分の組織内で使用される承認されていないクラウド アプリを検出する方法](active-directory-cloudappdiscovery-whatis.md)

