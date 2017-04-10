---
title: "Azure Active Directory の条件付きアクセス | Microsoft Docs"
description: "Azure Active Directory の条件付きアクセス制御を使用して、アプリケーションへのアクセスを認証するときに特定の条件を確認します。"
services: active-directory
keywords: "アプリへの条件付きアクセス, Azure AD での条件付きアクセス, 企業リソースへの安全なアクセス, 条件付きアクセス ポリシー"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: da3f0a44-1399-4e0b-aefb-03a826ae4ead
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/28/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 31659a7394a34cf8fb5b6ff11c955bdc9f8b65bb
ms.lasthandoff: 03/28/2017


---
# <a name="conditional-access-in-azure-active-directory"></a>Azure Active Directory の条件付きアクセス

> [!div class="op_single_selector"]
> * [Azure クラシック ポータル](active-directory-conditional-access.md)
> * [Azure ポータル](active-directory-conditional-access-azure-portal.md)

Azure Active Directory (Azure AD) の条件付きアクセス制御機能を使用すると、クラウドとオンプレミスのリソースを簡単に保護することができます。 多要素認証のような条件付きアクセス ポリシーを使用すると、資格情報の盗難やフィッシングの危険性から保護できます。 その他の条件付きアクセス ポリシーは、組織のデータを安全に保つのに役立ちます。 たとえば、資格情報を要求することに加え、Microsoft Intune などのモバイル デバイス管理システムに登録されているデバイスのみが組織の機密性の高いサービスにアクセスできるようにするポリシーを適用できます。

## <a name="prerequisites"></a>前提条件
Azure AD の条件付きアクセスは、[Azure Active Directory Premium](http://www.microsoft.com/identity) の機能です。 条件付きアクセス ポリシーが適用されたアプリケーションにアクセスする各ユーザーは、Azure AD Premium のライセンスを所有する必要があります。 ライセンス要件の詳細については、[ライセンスのないユーザーのレポート](https://aka.ms/utc5ix)に関するページを参照してください。

## <a name="how-is-conditional-access-control-enforced"></a>条件付きアクセス制御の適用のしくみ
条件付きアクセス制御が適用されると、Azure AD は、アプリケーションにアクセスするユーザーに対して設定された特定の条件を確認します。 アクセス要件が満たされると、ユーザーは承認され、アプリケーションにアクセスできます。  

![条件付きアクセスの概要](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## <a name="conditions"></a>条件
条件付きアクセス ポリシーに含めることができる条件は次のとおりです。

* **グループ メンバーシップ**。 グループのメンバーシップに基づいてユーザーのアクセスを制御します。
* **場所**。 ユーザーの場所を使用して多要素認証をトリガーし、ユーザーが信頼されたネットワーク上にいないときにブロック コントロールを使用します。
* **デバイス プラットフォーム**。 iOS、Android、Windows Mobile、Windows などのデバイス プラットフォームを、ポリシーを適用するための条件として使用します。
* **デバイスの有効/無効**。 デバイス ポリシーの評価中にデバイスの状態 (有効か無効か) が検証されます。 紛失したデバイスや盗難されたデバイスをディレクトリ内で無効にすると、そのデバイスはポリシーの要件を満たさなくなります。
* **サインインとユーザーのリスク**。 条件付きアクセス リスク ポリシーに [Azure AD Identity Protection](active-directory-identityprotection.md) を使用できます。 リスクに基づく条件付きアクセス ポリシーを使用すると、リスク イベントと疑わしいサインイン アクティビティに基づいて高度な保護を組織に提供できます。

## <a name="controls"></a>コントロール
条件付きアクセス ポリシーを適用するために使用できるコントロールは次のとおりです。

* **多要素認証**。 多要素認証による強力な認証を必須にすることができます。 多要素認証は、Azure Multi-Factor Authentication で使用するか、オンプレミスの多要素認証プロバイダーを Active Directory フェデレーション サービス (AD FS) と組み合わせることで使用することができます。 多要素認証を使用すると、承認されていないユーザーが有効なユーザーの資格情報を入手してリソースにアクセスするのを防ぐことができます。
* **ブロック**。 ユーザーの場所などの条件を適用してユーザー アクセスをブロックできます。 たとえば、ユーザーが信頼されたネットワーク上にいない場合にアクセスをブロックできます。
* **準拠デバイス**。 条件付きアクセス ポリシーをデバイス レベルで設定できます。 ドメインに参加しているコンピューターのみ、またはモバイル デバイス管理アプリケーションに登録されているモバイル デバイスのみが組織のリソースにアクセスできるようにポリシーを設定できます。 たとえば、ユーザーがアプリケーションへのアクセスを試みたときに、Intune を使用してデバイスの準拠を確認し、その結果を Azure AD に報告することができます。 Intune を使用してアプリとデータを保護する方法の詳細なガイダンスについては、「[Microsoft Intune でアプリとデータを保護する](https://docs.microsoft.com/intune/deploy-use/protect-apps-and-data-with-microsoft-intune)」を参照してください。 また、Intune を使用して紛失したデバイスや盗難されたデバイスのデータ保護を適用することもできます。 詳細については、「 [Help protect your data with full or selective wipe using Microsoft Intune (Microsoft Intune を使用した全体または選択的ワイプによるデータの保護)](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)」を参照してください。

## <a name="applications"></a>アプリケーション
条件付きアクセス ポリシーをアプリケーション レベルで適用できます。 アプリケーションとサービスのアクセス レベルをクラウドまたはオンプレミスで設定します。 ポリシーは、Web サイトまたはサービスに直接適用されます。 ポリシーは、ブラウザーへのアクセス、およびサービスにアクセスするアプリケーションへのアクセスに対して適用されます。

## <a name="device-based-conditional-access"></a>デバイス ベースの条件付きアクセス
Azure AD に登録されていて、特定の条件を満たすデバイスからのアプリケーションのアクセスを制限することができます。 デバイス ベースの条件付きアクセスは、次の場所からリソースにアクセスしようとするユーザーから組織のリソースを保護します。

* 不明なデバイスや管理されていないデバイス
* 組織で設定されているセキュリティ ポリシーを満たしていないデバイス

ポリシーは、次の要件に基づいて設定できます。

* **ドメイン参加デバイス**。 オンプレミスの Active Directory ドメインに参加しており、Azure AD に登録されているデバイスのアクセスを制限するポリシーを設定できます。 このポリシーは、Windows のデスクトップ、ノート PC、およびエンタープライズ タブレットに適用されます。
  Azure AD へのドメイン参加デバイスの自動登録をセットアップする方法の詳細については、[Azure Active Directory への Windows ドメイン参加済みデバイスの自動登録の設定](active-directory-conditional-access-automatic-device-registration-setup.md)に関する記事を参照してください。
* **準拠デバイス**。 管理システムのディレクトリで**準拠**とマークされているデバイスのアクセスを制限するポリシーを設定します。 このポリシーにより、デバイスにファイルの暗号化を強制するなど、セキュリティ ポリシーを満たすデバイスのみアクセスが許可されます。 このポリシーは、次のデバイスからのアクセスを制限するために使用できます。
  
  * **Windows ドメイン参加デバイス**。 ハイブリッド構成でデプロイされた System Center Configuration Manager (現在のブランチ) によって管理されます。
  * **業務用または個人用の Windows 10 Mobile デバイス**。 Intune またはサポートされているサード パーティのモバイル デバイス管理システムによって管理されます。
  * **iOS および Android デバイス**。 Intune によって管理されます。

デバイス ベースの証明機関ポリシーによって保護されているアプリケーションにアクセスするユーザーは、このポリシーの要件を満たすデバイスからアプリケーションにアクセスする必要があります。 ポリシー要件を満たしていないデバイスからアクセスしようとすると、拒否されます。

Azure AD でデバイスベースの証明機関ポリシーを構成する方法については、「[Azure Active Directory 接続アプリケーションに対するデバイス ベースの条件付きアクセス ポリシーを設定する方法](active-directory-conditional-access-policy-connected-applications.md)」を参照してください。

## <a name="resources"></a>リソース
組織向けの条件付きアクセスの設定方法の詳細については、次のリソース カテゴリと記事を参照してください。

### <a name="multi-factor-authentication-and-location-policies"></a>多要素認証と場所のポリシー
* [グループ、場所、および多要素認証ポリシーに基づく Azure AD 接続アプリへの条件付きアクセスの概要](active-directory-conditional-access-azuread-connected-apps.md)
* [サポートされているアプリケーションとブラウザー](active-directory-conditional-access-supported-apps.md)

### <a name="device-based-conditional-access"></a>デバイス ベースの条件付きアクセス
* [Azure Active Directory 接続アプリケーションに対するアクセス制御に関するデバイス ベースの条件付きアクセス ポリシーの設定](active-directory-conditional-access-policy-connected-applications.md)
* [Azure Active Directory への Windows ドメイン参加済みデバイスの自動登録の設定](active-directory-conditional-access-automatic-device-registration-setup.md)
* [Azure Active Directory のアクセスに関する問題のトラブルシューティング](active-directory-conditional-access-device-remediation.md)
* [Microsoft Intune を使用して紛失したデバイスや盗難されたデバイス上のデータを保護する](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)

### <a name="protect-resources-based-on-sign-in-risk"></a>サインイン リスクに基づいてリソースを保護する
* [Azure AD Identity Protection](active-directory-identityprotection.md)

### <a name="next-steps"></a>次のステップ
* [条件付きアクセスのよく寄せられる質問](active-directory-conditional-faqs.md)
* [テクニカル リファレンス](active-directory-conditional-access-technical-reference.md)


