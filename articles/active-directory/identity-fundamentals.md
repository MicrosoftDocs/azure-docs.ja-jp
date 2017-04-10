---
title: "Azure ID 管理の基礎 | Microsoft Docs"
description: 
keywords: 
author: jeffgilb
manager: femila
ms.date: 3/28/2017
ms.topic: article
ms.prod: 
ms.service: azure
ms.technology: 
ms.assetid: 
ms.reviewer: jsnow
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 222259e1f7c5a8796fd9f652cf5e050e75d4ce49
ms.lasthandoff: 03/29/2017


---
# <a name="fundamentals-of-azure-identity-management"></a>Azure ID 管理の基礎
企業のデジタル リソースには、企業ネットワーク外部のクラウドやデバイスに存在するものがますます増加しているため、クラウド ベースの優れた ID およびアクセス管理ソリューションは、ユーザーが企業のアプリケーションやデータにアクセスする方法とタイミングを管理および把握するのに最善の方法です。

Microsoft は、10 年以上にわたってクラウド ベースの ID を保護してきました。そして現在、[Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/active-directory-editions) によって、同じ保護システムをお客様にご利用いただけるようになりました。 Azure AD を使用すると、エンタープライズ管理者はこれまでにない優れたセキュリティとガバナンスで、ユーザーと管理者の説明責任を簡単に確保できます。

Azure AD Premium は、高度な保護機能を備えた、クラウド ベースの ID およびアクセス管理ソリューションです。これにより、すべてのアプリに対する 1 つの安全な ID、([Microsoft インテリジェンス セキュリティ グラフ](https://www.microsoft.com/en-us/security/intelligence)によって強化された) ID 保護、Privileged Identity Management が実現します。 Azure AD Premium は、よくある監視およびレポート ツールではなく、ユーザーの ID をリアルタイムで保護したり、組織のデータを保護するためにリスク ベースで適応性の高いアクセス ポリシーを作成できるようにしたりできます。

Azure AD の ID 管理および保護の概要については、この簡単なビデオをご覧ください。
<iframe width="560" height="315" src="https://www.youtube.com/embed/9LGIJ2-FKIM" frameborder="0" allowfullscreen></iframe>

Microsoft は、どこでも使用できる ID だけでなく、組織の IT の自動化、セキュリティ支援、管理のための一連のツールも提供しています。 クラウド コンピューティングの登場後も、ヘルプデスクへのユーザー パスワードのリセット依頼、ユーザー グループ管理、アプリケーションの要求など、IT タスクの管理と制御の需要はまだ存在します。 さらに事態を複雑にしているのが、従業員による職場への個人用デバイスの持ち込みと、すぐに利用可能な SaaS アプリケーションの使用です。 その結果、企業のデータセンターとパブリック クラウド プラットフォーム全体でアプリケーションの制御を維持することが重要な課題です。

> [!Note]
> この記事で説明されている機能を使用するには、Azure Active Directory P1 または P2 サブスクリプションを別途購入するか、[Enterprise Mobility + Security E3 または E5](https://docs.microsoft.com/enterprise-mobility-security/solutions/learn-about-ems) サブスクリプションの一部として購入する必要があります。

## <a name="connect-on-premises-active-directory-with-azure-ad-and-office-365"></a>オンプレミスの Active Directory を Azure AD と Office 365 に接続する
オンプレミスの Active Directory に大規模な投資を行った組織は、オンプレミスのディレクトリと Azure AD を[ハイブリッド ID 管理](https://docs.microsoft.com/azure/active-directory/active-directory-hybrid-identity-design-considerations-overview)に統合することによって、その投資をクラウドに拡張することができます。 こうすると、場所に関係なくリソースにアクセスするための共通 ID を提供できるため、ユーザーの生産性が向上します。 ユーザーと組織は、シングル サインオン (SSO) を使用して、オンプレミス リソースとクラウド サービス (Office 365 など) の両方にアクセスできます。

[Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) は、統合の実施に必要な唯一のツールです。 Azure AD Connect は、ID 同期のニーズをサポートするための最新機能が用意されており、DirSync や Azure AD Sync などの古いバージョンの ID 統合ツールに代わります。 Azure AD Connect を使用すると、次の機能によってオンプレミスと Azure AD 間の ID 管理と同期が可能になります。

- 同期 - ユーザーやグループなどのオブジェクトを作成するためのコンポーネントです。 さらに、オンプレミス ユーザーやオンプレミス グループの ID 情報をクラウド側と一致させる働きをします。 ユーザーが Azure AD でパスワードを更新したときにオンプレミスのディレクトリを同期した状態にしておくために、パスワード ライトバックも有効にすることができます。
- AD FS - Azure AD Connect のオプション コンポーネントです。オンプレミスの AD FS インフラストラクチャと組み合わせることでハイブリッド環境を構成できます。 ドメイン参加による SSO や AD サインイン ポリシーの適用、スマート カードやサード パーティの MFA など、複雑なデプロイに対応する組織で利用できます。
- 正常性の監視 - [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health) により、厳重に監視し、Azure Portal でこのアクティビティを一元的に表示できます。

## <a name="increase-productivity-and-reduce-helpdesk-costs-with-self-service-and-single-sign-on-experiences"></a>セルフサービスとシングル サインオン エクスペリエンスで生産性の向上とヘルプデスクのコスト削減を実現する

従業員は、記憶するユーザー名とパスワードが 1 つで、すべてのデバイスからのエクスペリエンスが一貫している場合に生産性が向上します。 また、[忘れたパスワードのリセット](https://docs.microsoft.com/azure/active-directory/active-directory-passwords)、アプリケーションへのアクセスの要求などのタスクを、ヘルプデスクの支援を待たずにセルフサービスで実行できると、時間の節約にもなります。

Azure AD は[オンプレミスの Active Directory をクラウドに拡張](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)するため、ユーザーは、ドメイン参加デバイスおよび会社のリソースにも、業務で使用する必要があるすべての Web アプリケーションおよび SaaS アプリケーションにもプライマリ組織アカウントを使用できます。 複数のユーザー名とパスワードのセットを覚える必要がなくなるだけでなく、組織のグループ メンバーシップや従業員としての地位に基づいて、ユーザーのアプリケーション アクセスも自動的にプロビジョニング (またはプロビジョニング解除) されるようにすることができます。 また、ギャラリー アプリ、または [Azure AD アプリケーション プロキシ](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started)で開発および公開した独自のオンプレミス アプリについてそのアクセスを制御できます。

## <a name="manage-and-control-access-to-corporate-resources"></a>会社のリソースへのアクセスを管理および制御する
Microsoft ID およびアクセス管理ソリューションは、IT が企業のデータ センター全体とクラウドのアプリケーションとリソースへのアクセスを保護するのに役立ち、[他要素認証](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next)や[条件付きアクセス ポリシー](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)などの追加レベルの検証を可能にします。 高度なセキュリティ報告、監査、および警告によって疑わしいアクティビティを監視し、潜在的なセキュリティ上の問題を軽減できます。

Azure AD Premium の条件付きアクセス システムを使用すると、エンタープライズ管理者は、任意の Azure AD 接続アプリケーション (SaaS アプリ、クラウドで実行されているカスタム アプリ、またはオンプレミス Web アプリケーション) に対してポリシー ベースのアクセス規則を作成できます。 Azure AD はこれらのポリシーをリアルタイムで評価し、ユーザーがアプリケーションにアクセスしようとするたびにポリシーを適用します。 Azure ID 保護ポリシーを使用すると、疑わしいアクティビティが検出された場合に、自動的に対処できます。たとえば、リスクの高いユーザーのアクセスのブロック、多要素認証の強制、資格情報が侵害されたと思われる場合のパスワードのリセットなどです。

## <a name="azure-active-directory-privileged-identify-management"></a>Azure Active Directory Privileged Identity Management

Azure Active Directory Premium P2 契約に含まれている [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) を使用すると、管理者アカウントと、Azure Active Directory およびその他の Microsoft オンライン サービス内のリソースへのアクセスを検出、制限、および監視することができます。 また、必要とされる正確な期間にわたって、オンデマンドの管理アクセスを管理するためにも役立ちます。

Privileged Identity Management は、管理者が多要素認証される一時的な特権の昇格を要求できるように、オンデマンドで管理者権限を適用できます。そのアカウントが通常のユーザーの状態に戻るまでの期間は、事前に構成できます。

## <a name="benefits-of-azure-identity"></a>Azure ID の利点

Azure の ID 管理では、次のことが可能です。

-   ハイブリッドのエンタープライズ全体の各ユーザーに個別の ID を作成して管理し、ユーザー、グループ、およびデバイスが [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)と同期された状態を維持する。

-   何千もの事前統合された SaaS アプリを含むアプリケーションにシングル サインオン アクセスを提供したり、[Azure AD アプリケーション プロキシ](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started)を使用してオンプレミス SaaS アプリケーションへのセキュリティで保護されたリモート アクセスを提供したりする。

-   ルール ベースの[多要素認証](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next)をオンプレミス アプリケーションとクラウド アプリケーションの両方に適用することによって、アプリケーション アクセスのセキュリティを有効にする。

-   [セルフサービスによるパスワードのリセット](https://docs.microsoft.com/azure/active-directory/active-directory-passwords)、[MyApps ポータル](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-user-help)を使用したグループおよびアプリケーション アクセス要求によって、ユーザーの生産性を向上させる。

-   世界規模でエンタープライズ レベルでクラウド ベースの ID およびアクセス管理ソリューションの[高可用性および信頼性](https://docs.microsoft.com/azure/architecture/resiliency/high-availability-azure-applications)の利点を活用する。

