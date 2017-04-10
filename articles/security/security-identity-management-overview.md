---
title: "ID 管理に役立つ Azure のセキュリティ機能 | Microsoft Docs"
description: " この記事は、ID 管理に役立つ Azure のコア セキュリティ機能の概要を説明します。 Microsoft ID およびアクセス管理ソリューションは、IT が企業のデータ センター全体とクラウドのアプリケーションとリソースへのアクセスを保護するのに役立ち、他要素認証や条件付きアクセスポリシーなどの追加レベルの検証を可能にします。 "
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 5aa0a7ac-8f18-4ede-92a1-ae0dfe585e28
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/09/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: bb9d08d6164ec01ce1d76fc4a23e782213ecfcd9
ms.lasthandoff: 03/11/2017


---
# <a name="azure-identity-management-security-overview"></a>Azure ID 管理のセキュリティの概要
Microsoft ID およびアクセス管理ソリューションは、IT が企業のデータ センター全体とクラウドのアプリケーションとリソースへのアクセスを保護するのに役立ち、他要素認証や条件付きアクセスポリシーなどの追加レベルの検証を可能にします。 高度なセキュリティ報告、監査、および警告によって疑わしいアクティビティを監視し、潜在的なセキュリティ上の問題を軽減できます。 [Azure Active Directory Premium](../active-directory/active-directory-editions.md) は、何千ものクラウドへのシングル サインオン (SaaS) アプリケーション、およびオンプレミスで実行する Web アプリケーションへのアクセスを提供します。

Azure Active Directory (AD) のセキュリティ上の利点は次のとおりです。

* ハイブリッドのエンタープライズ全体の各ユーザーに個別の ID を作成して管理し、ユーザー、グループ、およびデバイスが同期された状態を維持する
* 何千もの事前統合された SaaS アプリを含むアプリケーションにシングル サインオン アクセスを提供します。
* ルール ベースの多要素認証をオンプレミスおよびクラウド アプリケーションの両方に適用することによって、アプリケーション アクセスのセキュリティを有効にする
* Azure AD アプリケーション プロキシを通じてオンプレミス Web アプリケーションへの安全なリモート アクセスをプロビジョニングする

この記事の目的は、ID 管理に役立つ Azure のコア セキュリティ機能の概要を説明することです。 それぞれの詳細について説明する記事へのリンクも用意されているため、さらに詳しく学習できます。  

この記事は、次の Azure ID 管理のコア機能の説明に重点を置いています。

* シングル サインオン
* リバース プロキシ
* 多要素認証
* セキュリティの監視、アラート、および機械学習ベースのレポート
* コンシューマーの ID とアクセスの管理
* デバイス登録
* Privileged Identity Management
* Identity Protection
* ハイブリッド ID 管理

## <a name="single-sign-on"></a>シングル サインオン
シングル サインオン (SSO) とは、1 つのユーザー アカウントを使って&1; 回サインインするだけで作業に必要なすべてのアプリケーションとリソースにアクセスできる機能です。 いったんサインインすると、もう一度認証 (パスワードの入力など) を求められることなく、必要なすべてのアプリケーションにアクセスできます。

多くの組織では、エンド ユーザーの生産性向上のため、Office 365、Box、Salesforce などのサービスとしてのソフトウェア (SaaS) アプリケーションに依存しています。 従来は、IT スタッフが各 SaaS アプリケーションのユーザー アカウントを個別に作成し、更新する必要がありました。さらに、ユーザーは、各 SaaS アプリケーションのパスワードを覚える必要がありました。

Azure AD は、オンプレミスの Active Directory をクラウドに拡張して、ユーザーがプライマリ組織アカウントを使用してドメイン参加デバイスおよび会社のリソースにサインインするだけでなく、それぞれの業務に必要なすべての Web アプリケーションおよび SaaS アプリケーションにもサインインできるようにします。

これにより、ユーザーが複数のユーザー名とパスワードのセットを管理する必要がなくなるだけでなく、組織のグループや従業員としての地位に基づいてアプリケーションのアクセスを自動的にプロビジョニングまたはプロビジョニング解除することが可能になります。 Azure AD には、SaaS アプリケーション間でユーザー アクセスを一元的に管理するためのセキュリティおよびアクセス管理コントロールが導入されています。

詳細情報:

* [シングル サインオンの概要](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../active-directory/active-directory-appssoaccess-whatis.md)
* [SaaS アプリと Azure Active Directory シングル サインオンを統合する](../active-directory/active-directory-sso-integrate-saas-apps.md)

## <a name="reverse-proxy"></a>リバース プロキシ
Azure AD アプリケーション プロキシを使用すると、[SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) サイト、[Outlook Web アプリ](https://technet.microsoft.com/library/jj657718.aspx)、[IIS](http://www.iis.net/) ベースのアプリなどのオンプレミス アプリケーションをプライベート ネットワーク内で発行し、ネットワーク外部のユーザーにセキュリティで保護されたアクセスを提供できます。 アプリケーション プロキシ では、多くの種類のオンプレミス Web アプリケーションに対応するリモート アクセスとシングル サインオン (SSO) を、Azure AD がサポートする数千もの SaaS アプリケーションとともに利用できます。 従業員は、自宅から自分のデバイスでアプリケーションにログインし、このクラウド ベースのプロキシを使用して認証を行うことができます。

詳細情報:

* [Azure AD アプリケーション プロキシの有効化](../active-directory/active-directory-application-proxy-enable.md)
* [Azure AD アプリケーション プロキシを使用してアプリケーションを発行する](../active-directory/active-directory-application-proxy-publish.md)
* [アプリケーション プロキシを使用したシングル サインオン](../active-directory/active-directory-application-proxy-sso-using-kcd.md)
* [条件付きアクセスの使用](../active-directory/active-directory-application-proxy-conditional-access.md)

## <a name="multi-factor-authentication"></a>多要素認証
Azure Multi-Factor Authentication (MFA) は、複数の確認方法の使用を要求することで、ユーザーのサインインとトランザクションに重要な&2; つ目のセキュリティ レイヤーを追加する認証方法です。 MFA では、シンプルなサインイン プロセスを好むユーザーのニーズに応えながら、データやアプリケーションへのアクセスを効果的に保護することができます。 電話やテキスト メッセージ、モバイル アプリによる通知のほか、確認コードやサード パーティの OAuth トークンなど、一連の照合方法を通じて確実な認証を行うことができます。

詳細情報:

* [多要素認証](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Azure Multi-Factor Authentication とは](../multi-factor-authentication/multi-factor-authentication.md)
* [Azure Multi-Factor Authentication のしくみ](../multi-factor-authentication/multi-factor-authentication-how-it-works.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>セキュリティの監視、アラート、および機械学習ベースのレポート
セキュリティの監視とアラートや、整合性のないアクセス パターンを識別する機械学習ベースのレポートを使用して、ビジネスを保護できます。 Azure Active Directory のアクセスおよび使用状況レポートを使用すると、組織のディレクトリの整合性とセキュリティを表示できます。 ディレクトリ管理者は、この情報を使用して、リスクを軽減するために適切に計画できるように、セキュリティ上のリスクがある箇所をより適切に確認できます。

レポートは、Azure クラシック ポータルで、次の方法で分類されます。

* 異常レポート: 異常と考えられるサインイン イベントが含まれます。 この目的は、このようなアクティビティを認識し、イベントが不審であるかどうかを判断できるようにすることです。
* 統合アプリケーション レポート – 組織内のクラウド アプリケーションの使用状況を明らかにします。 Azure Active Directory は、何千ものクラウド アプリケーションとの統合を提供します。
* エラー レポート – 外部アプリケーションにアカウントをプロビジョニングするときに発生することがあるエラーを示します。
* ユーザー固有レポート – 特定のユーザーのデバイス/サインイン アクティビティ データを示します。
* アクティビティ ログ: 過去 24 時間、過去 7 日間、または過去 30 日間のすべての監査イベントの記録、グループのアクティビティの変更、およびパスワードのリセットと登録のアクティビティが含まれます。

詳細情報:

* [アクセスおよび使用状況レポートの表示](../active-directory/active-directory-view-access-usage-reports.md)
* [Azure Active Directory レポートの使用の開始](../active-directory/active-directory-reporting-getting-started.md)
* [Azure Active Directory レポート ガイド](../active-directory/active-directory-reporting-guide.md)

## <a name="consumer-identity-and-access-management"></a>コンシューマーの ID とアクセスの管理
Azure Active Directory B2C は、数億個の ID を扱うコンシューマー向けアプリケーション用の高可用性グローバル ID 管理サービスです。 モバイルと Web の両方のプラットフォームにわたる統合を実現できます。 コンシューマーは、既に持っているソーシャル アカウントを使用するか、新たな資格情報を作成して、すべてのアプリケーションにログオンできます。その際のエクスペリエンスは、カスタマイズすることができます。

これまで、開発したアプリケーションにコンシューマーがサインアップおよびサインインすることを望むアプリケーション開発者は、独自のコードを記述していました。 開発者は、オンプレミスのデータベースまたはシステムを使用して、ユーザー名とパスワードを保存していました。 Azure Active Directory B2C では、セキュリティ保護された標準ベースのプラットフォームと機能豊富で拡張可能なポリシーのセットを使用して、より簡単にコンシューマーの ID 管理をアプリケーションに統合できます。

Azure Active Directory B2C を使用すると、コンシューマーは、既存のソーシャル アカウント (Facebook、Google、Amazon、LinkedIn) を使用するか、または新しい資格情報 (電子メール アドレスとパスワードまたはユーザー名とパスワード) を作成することによって、アプリケーションにサインアップできます。

詳細情報:

* [Azure Active Directory B2C とは](https://azure.microsoft.com/services/active-directory-b2c/)
* [Azure Active Directory B2C プレビュー: アプリケーションにコンシューマーをサインアップおよびサインインする](../active-directory-b2c/active-directory-b2c-overview.md)
* [Azure Active Directory B2C プレビュー: アプリケーションの種類](../active-directory-b2c/active-directory-b2c-apps.md)

## <a name="device-registration"></a>デバイス登録
Azure AD Device Registration は、デバイスに基づいて[条件付きでアクセス](../active-directory/active-directory-conditional-access-device-registration-overview.md)を許可するというシナリオの基礎となる機能です。 デバイスが登録されると、Azure Active Directory Device Registration によってそのデバイスの ID がプロビジョニングされます。この ID は、ユーザーのサインイン時のデバイス認証に使用されます。 認証済みのデバイスおよびデバイスの属性を使用して、クラウドおよびオンプレミスでホストされるアプリケーションに条件付きアクセス ポリシーを適用できます。

Intune などのモバイル デバイス管理 (MDM) ソリューションと組み合わせて使用すると、Azure Active Directory のデバイスの属性は、デバイスに関する情報が追加されて更新されます。 これにより、条件付きアクセス規則を作成できます。この規則に従い、デバイスからのアクセス時にセキュリティおよび法令遵守の基準を満たす必要があります。

詳細情報:

* [Azure Active Directory Device Registration の基本](../active-directory/active-directory-conditional-access-device-registration-overview.md)
* [Azure Active Directory への Windows ドメイン参加済みデバイスの自動デバイス登録](../active-directory/active-directory-conditional-access-automatic-device-registration.md)
* [Azure Active Directory への Windows ドメイン参加済みデバイスの自動登録の設定](../active-directory/active-directory-conditional-access-automatic-device-registration-setup.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management
Azure Active Directory (AD) Privileged Identity Management (PIM) を使用すると、特権 ID と、Azure AD や他の Microsoft オンライン サービス (Office 365 や Microsoft Intune など) のリソースへのアクセスを管理、制御、監視できます。

ユーザーは、Azure や Office 365 のリソース、または他の SaaS アプリで、特権操作を実行することが必要になる場合があります。 通常は、組織がユーザーに Azure AD で永続的な特権アクセスを付与する必要があります。 しかし、この措置では、ユーザーが管理者特権を使用して実行している内容を組織が十分に監視できないため、クラウドでホストされているリソースのセキュリティ リスクが増大します。 また、特権アクセスを持つユーザー アカウントが侵害された場合に、その&1; つの侵害がクラウド セキュリティ全体に影響を与える可能性もあります。 Azure AD Privileged Identity Management はこのリスクの解決に役立ちます。

Azure AD Privileged Identity Management では、次のことが可能です。

* Azure AD の管理者であるユーザーを特定する
* Office 365 や Intune などの Microsoft Online Services へのオンデマンドの "ジャスト イン タイム" な管理アクセスを可能にする
* 管理者のアクセス履歴と管理者の割り当ての変更に関するレポートを取得する
* 特権ロールへのアクセスに関するアラートを受け取る

詳細情報:

* [Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md)
* [Azure AD Privileged Identity Management におけるロール](../active-directory/active-directory-privileged-identity-management-roles.md)
* [Azure AD Privileged Identity Management: ユーザー ロールを追加または削除する方法](../active-directory/active-directory-privileged-identity-management-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Identity Protection
Azure AD Identity Protection は、リスク イベントや組織の ID に影響する潜在的な脆弱性に関する統合ビューを提供するセキュリティ サービスです。 Identity Protection は、既存の Azure Active Directory 異常検出機能 (Azure AD の異常アクティビティ レポートで利用可能) を利用し、リアルタイムで異常を検出できる新しいリスク イベントの種類が導入されています。

詳細情報:

* [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md)
* [Channel 9: Azure AD and Identity Show: Identity Protection Preview (Channel 9: Azure AD および Identity ショー: Identity Protection プレビュー)](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-management"></a>ハイブリッド ID 管理
Microsoft の ID 管理はオンプレミスとクラウドにまたがり、場所に関係なく、すべてのリソースの認証と権限付与を&1; つのユーザー ID で行います。

詳細情報:

* [ハイブリッド ID ホワイト ペーパー](http://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Active Directory チームのブログ](https://blogs.technet.microsoft.com/ad/)

