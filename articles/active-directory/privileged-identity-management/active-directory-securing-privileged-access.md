---
title: "Azure AD での特権アクセスのセキュリティ保護 | Microsoft Docs"
description: "このトピックでは、Azure、Azure Active Directory、および Microsoft Online Services で特権アクセスを保護するための方法について説明します。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: mwahl
ms.assetid: 235a0ce9-1daf-4433-8f65-9c6afcd64d08
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: c0e2324a2b2e6294df6e502f2e7a0ae36ff94158
ms.openlocfilehash: 936e152304dc4a4a3bd95c7bfba071a02a20d0dd


---
# <a name="securing-privileged-access-in-azure-ad"></a>Azure AD での特権アクセスのセキュリティ保護
現代の組織にとって特権アクセスのセキュリティ保護は、ビジネス資産を保護するうえで重要な最初のステップです。 特権アカウントとは、IT システムを管理するアカウントです。 サイバー攻撃では、組織のデータやシステムへのアクセス手段を得るために、このようなアカウントが標的にされます。 特権アクセスを保護するためには、悪意のあるユーザーにさらされる危険からアカウントとシステムを分離する必要があります。

クラウド サービスを経由して特権アクセスを取得するユーザーが増えつつあります。 たとえば、Office&365; のグローバル管理者、Azure サブスクリプション管理者、VM や SaaS アプリの管理アクセス権を持つユーザーなどがこれにあたります。

Microsoft では、 [特権アクセスのセキュリティ保護](https://technet.microsoft.com/library/mt631194.aspx)に関するこちらのロードマップを採用することをお勧めしています。

Azure Active Directory を使用して Azure や Office 365 などの Microsoft サービスとアプリケーションへのアクセスを管理しているお客様は、ユーザー アカウントの管理と認証を Active Directory と Azure Active Directory のどちらで行っている場合でも、これらの原則が当てはまります。 次のセクションでは、特権アクセスのセキュリティ保護をサポートする Azure AD の機能について詳しく説明します。

## <a name="multi-factor-authentication"></a>多要素認証
管理者認証のセキュリティを高めるために、権限を付与する前に多要素認証 (MFA) を要求する必要があります。 MFA は、本人性の確認において、ユーザー名とパスワードに加えて、その他の要素を取り入れる手法です。 ユーザーのサインインとトランザクションに&2; 層構造のセキュリティを確保することができます。

Azure Multi-Factor Authentication を使えば、シンプルなサインイン プロセスを好むユーザーのニーズに応えながら、データやアプリケーションへのアクセスを効果的に保護することが可能です。 電話、テキスト メッセージ、モバイル アプリによる通知、確認コードやサード パーティの OATH トークンなど、一連の簡単な照合方法を通じて確実な認証を行うことができます。

Azure Multi-Factor Authentication の概要については、次のビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Windows-Azure-Multi-Factor-Authentication/player]
> 
> 

詳細については、「 [MFA for Office 365 and MFA for Azure (Office 365 の MFA と Azure の MFA)](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/)」を参照してください。

## <a name="time-bound-privileges"></a>期限付きの特権
組織によっては、高い特権を持つロールに割り当てられたユーザーが増えすぎることがあります。 サービスのサインアップなど、特定のアクティビティのためにユーザーがロールに追加されたが、その後、ユーザーはあまり権限を使用していないというケースもあります。

特権の公開期間を短縮し、使用状況の可視性を高めるには、ユーザーがタスクの実行を必要としている場合にのみ特権をジャスト イン タイム (JIT) で取得するようにユーザーを制限します。 Azure Active Directory と Microsoft Online Services では、 [Azure AD Privileged Identity Management (PIM)](http://aka.ms/AzurePIM)を使用することができます。

![PIM dashboard][2]

## <a name="attack-detection"></a>攻撃の検出
[Azure Active Directory Identity Protection](../active-directory-identityprotection.md) は、リスク イベントや組織の ID に影響する潜在的な脆弱性に関する統合ビューを提供します。 リスク イベントを基にして、Identity Protection は各ユーザーのユーザー リスク レベルを計算し、ユーザーがリスク ベースのポリシーを構成して組織の ID を自動的に保護するできるようにします。 これらのポリシーを、Azure Active Directory や EMS によって提供される他の条件付きアクセス コントロールと組み合わせて、ユーザーを自動的にブロックしたり、パスワードのリセットや多要素認証の適用などの提案を提示したりすることができます。

![Azure AD Identity Protection][3]

## <a name="conditional-access"></a>条件付きアクセス
Azure Active Directory は、条件付きアクセス制御によって、ユーザー認証時にアプリケーションへのアクセスを許可する前に、選択されている特定の条件を確認します。 条件が満たされていれば、ユーザーは承認され、アプリケーションにアクセスできます。

![MFA による条件付きアクセス規則の設定][4]

## <a name="related-articles"></a>関連記事:
* [Azure Multi-Factor Authentication](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md) の有効化
* [Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-configure.md) の有効化
* [Azure AD Identity Protection](../active-directory-identityprotection.md) の有効化
* [条件付きアクセス制御](../active-directory-conditional-access.md)の有効化

包括的なセキュリティ ロードマップの構築に関する詳細については、ドキュメント「 [Microsoft Cloud Security for Enterprise Architects (エンタープライズ アーキテクト向けの Microsoft クラウド セキュリティ)](http://aka.ms/securecustomer) 」の「Customer responsibilities and roadmap (お客様の責任範囲とロードマップ)」のセクションを参照してください。 Microsoft サービスと連携して、これらのトピックについて支援を受ける方法の詳細については、Microsoft の担当者に問い合わせていただくか、 [サイバーセキュリティ ソリューションに関するページ](https://www.microsoft.com/microsoftservices/campaigns/cybersecurity-protection.aspx)を参照してください。

<!--Image references-->
[1]: ../media/active-directory-privileged-identity-management-configure/Search_PIM.png
[2]: ../media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ../media/active-directory-identityprotection/29.png
[4]: ../media/active-directory-conditional-access/conditionalaccess-saas-apps.png



<!--HONumber=Jan17_HO5-->


