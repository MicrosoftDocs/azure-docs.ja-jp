---
title: 条件付きアクセス ポリシーの許可コントロール - Azure Active Directory
description: Azure AD 条件付きアクセス ポリシーの許可コントロールとは
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: d01a750948f8e3c264b9bcffdaad3ae72fa40ac0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579112"
---
# <a name="conditional-access-grant"></a>条件付きアクセス:Grant

条件付きアクセス ポリシー内では、管理者はアクセス コントロールを使用して、リソースへのアクセスを許可またはブロックすることができます。

![多要素認証を必要とする許可コントロールを使用した条件付きアクセス ポリシー](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>アクセスのブロック

ブロックは、割り当てを考慮して、条件付きアクセス ポリシーの構成に基づいてアクセスを禁止します。

ブロックは強力なコントロールであるため、適切な知識を習得したうえで扱う必要があります。 ブロック ステートメントのあるポリシーには予想外の副作用が含まれることがあります。 大規模で有効にする前に、適切なテストと検証が不可欠です。 管理者は、変更を行うにあたり、[条件付きアクセスのレポート専用モード](concept-conditional-access-report-only.md)や[条件付きアクセスの What If ツール](what-if-tool.md)などのツールを利用する必要があります。

## <a name="grant-access"></a>アクセス権の付与

管理者は、アクセス権を付与するときに 1 つ以上のコントロールを適用することを選択できます。 これらのコントロールには、次のオプションがあります。 

- [多要素認証 (Azure AD Multi-Factor Authentication) を必須にする](../authentication/concept-mfa-howitworks.md)
- [デバイスが準拠としてマーク済みであることを必要とする (Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [ハイブリッド Azure AD 参加済みのデバイスを必要とする](../devices/concept-azure-ad-join-hybrid.md)
- [承認済みクライアント アプリを必須にする](app-based-conditional-access.md)
- [アプリの保護ポリシーを必須にする](app-protection-based-conditional-access.md)
- [パスワードの変更を必須にする](#require-password-change)

これらのオプションを組み合わせることを選択する場合、管理者は次の方法を選択できます。

- 選択したコントロールすべてが必要 (コントロール **と** コントロール)
- 選択したコントロールのいずれかが必要 (コントロール **または** コントロール)

既定では、条件付きアクセスでは、選択したすべてのコントロールが必要です。

### <a name="require-multi-factor-authentication"></a>多要素認証が必要です

このチェックボックスをオンにすると、ユーザーは Azure AD Multi-Factor Authentication を実行する必要があります。 Azure AD Multi-factor Authentication のデプロイの詳細については、「[クラウド ベースの Azure AD Multi-Factor Authentication のデプロイの計画](../authentication/howto-mfa-getstarted.md)」の記事を参照してください。

[Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-overview) は、条件付きアクセス ポリシーでの多要素認証の要件を満たしています。 

### <a name="require-device-to-be-marked-as-compliant"></a>デバイスは準拠としてマーク済みである必要がある

Microsoft Intune をデプロイしている組織では、デバイスから返された情報を使用して、特定の準拠要件を満たすデバイスを識別することができます。 このポリシー準拠情報は Intune から Azure AD に転送され、条件付きアクセスはそこで、リソースへのアクセスを許可するかブロックするかを決定できます。 準拠ポリシーの詳細については、「[Intune を使用して組織内のリソースへのアクセスを許可するように、デバイス上でルールを設定する](/intune/protect/device-compliance-get-started)」という記事を参照してください。

デバイスを準拠としてマークするには、Intune (任意のデバイス OS の場合) または Windows 10 デバイス用のサード パーティ製 MDM システムを使用できます。 Jamf pro は、唯一サポートされているサードパーティ製 MDM システムです。 統合の詳細については、「[コンプライアンスのために Jamf Pro を Intune と統合する](/intune/protect/conditional-access-integrate-jamf)」を参照してください。

デバイスを準拠としてマークするには、あらかじめそのデバイスが Azure AD に登録されている必要があります。 デバイス登録の詳細については、「[デバイス ID とは](../devices/overview.md)」を参照してください。

### <a name="require-hybrid-azure-ad-joined-device"></a>ハイブリッド Azure AD 参加済みのデバイスを必要とする

組織は、条件付きアクセス ポリシーの一部としてデバイス ID を使用することを選択できます。 組織は、このチェックボックスを使用して、デバイスがハイブリッド Azure AD 参加済みであることを必須にすることができます。 デバイス ID の詳細については、「[デバイス ID とは](../devices/overview.md)」の記事を参照してください。

[デバイスコードの OAuth フロー](../develop/v2-oauth2-device-code.md)を使用する場合、マネージド デバイスを要求する許可コントロールや、デバイスの状態の条件は、サポートされません。 これは、認証を実行するデバイスがそのデバイスの状態を、コードを提供するデバイスに提供できず、トークン内のデバイスの状態が、認証を行うデバイスにロックされるためです。 代わりに、多要素認証を要求する許可コントロールを使用してください。

### <a name="require-approved-client-app"></a>承認済みクライアント アプリを必須にする

組織は、選択したクラウド アプリへのアクセス試行を、承認されたクライアント アプリから行うように要求することができます。 これらの承認されたクライアント アプリは、モバイル デバイス管理 (MDM) ソリューションには一切依存せずに、[Intune アプリ保護ポリシー](/intune/app-protection-policy)をサポートします。

この許可の制御を活用するために、条件付きアクセスでは、ブローカー アプリを使用する必要がある Azure Active Directory にデバイスを登録する必要があります。 ブローカー アプリには、iOS 用の Microsoft Authenticator か、Android デバイス用の Microsoft Authenticator または Microsoft ポータル サイトを使用できます。 ユーザーが認証を試みたときにブローカー アプリがデバイスにインストールされていない場合、ユーザーは必要なブローカー アプリをインストールするために、適切な App ストアにリダイレクトされます。

この設定は、以下の iOS アプリと Android アプリに適用されます。

- Microsoft Azure Information Protection
- Microsoft Bookings
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Power Automate
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype for Business
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer
- Microsoft Whiteboard
- Microsoft 365 管理

**解説**

- 承認されたクライアント アプリは、Intune モバイル アプリケーション管理機能をサポートしています。
- **[承認されたクライアント アプリが必要です]** 要件:
   - デバイス プラットフォームの条件に関しては、iOS と Android のみがサポートされます。
   - デバイスを登録するには、ブローカー アプリが必要です。 ブローカー アプリには、iOS 用の Microsoft Authenticator か、Android デバイス用の Microsoft Authenticator または Microsoft ポータル サイトを使用できます。
- 条件付きアクセスでは、InPrivate モードの Microsoft Edge を承認されたクライアント アプリと見なすことはできません。
- 条件付きアクセス ポリシーでは、承認されたクライアント アプリとして Microsoft Power BI アプリが必要となります。ここでは、Azure AD アプリケーション プロキシを使用して、Power BI モバイル アプリからオンプレミスの Power BI Report Server に接続できるようにすることはできません。

「[方法: 条件付きアクセスを使用してクラウド アプリへのアクセスに承認されたクライアント アプリを要求する](app-based-conditional-access.md)」を参照して構成の例を確認してください。

### <a name="require-app-protection-policy"></a>アプリの保護ポリシーを必須にする

条件付きアクセス ポリシー内で、選択したクラウド アプリがアクセスできるようにする前に、クライアント アプリに [Intune アプリ保護ポリシー](/intune/app-protection-policy)が存在することを要求できます。 

この許可の制御を活用するために、条件付きアクセスでは、ブローカー アプリを使用する必要がある Azure Active Directory にデバイスを登録する必要があります。 ブローカー アプリには、iOS 用の Microsoft Authenticator か、Android デバイス用の Microsoft ポータル サイトのいずれかを使用できます。 ユーザーが認証を試みたときにブローカー アプリがデバイスにインストールされていない場合、ユーザーはブローカー アプリをインストールするために、App Store にリダイレクトされます。

アプリケーションには **Policy Assurance** が実装された **Intune SDK** が必要であり、この設定をサポートするための他の特定の要件を満たす必要があります。 Intune SDK を使用してアプリケーションを実装する開発者は、これらの要件について SDK ドキュメントで詳細を確認できます。

次のクライアン トアプリは、この設定をサポートすることが確認されています。

- Microsoft Cortana
- Microsoft Edge
- Microsoft Excel
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Word
- MultiLine for Intune
- Nine Mail - Email & Calendar

> [!NOTE]
> Microsoft Teams、Microsoft Kaizala、Microsoft Skype for Business、Microsoft Visio では、**アプリの保護ポリシーを必須にする** 許可はサポートされていません。 これらのアプリを動作させる必要がある場合は、**承認済みのアプリを必須にする** 許可を明示的に使用してください。 この 3 つのアプリケーションでは、2 つの許可の間で or 句を使用することはできません。

**解説**

- アプリ保護ポリシーのアプリは、ポリシー保護を含む Intune モバイル アプリケーション管理機能をサポートしています。
- **アプリの保護ポリシーが必要** の要件:
    - デバイス プラットフォームの条件に関しては、iOS と Android のみがサポートされます。
    - デバイスを登録するには、ブローカー アプリが必要です。 iOS では、ブローカー アプリは Microsoft Authenticator であり、Android では Intune ポータル サイト アプリです。

「[方法: 条件付きアクセスを使用して、クラウド アプリへのアクセスにアプリ保護ポリシーと承認済みクライアント アプリの使用を必須にする](app-protection-based-conditional-access.md)」を参照して構成の例を確認してください。

### <a name="require-password-change"></a>パスワードの変更を必須とする 

ユーザー リスクが検出されると、ユーザー リスク ポリシーの条件で、管理者が Azure AD セルフサービス パスワード リセットを使用して、ユーザーがパスワードを安全に変更できるようにすることができます。 ユーザー リスクが検出された場合、ユーザーは、セルフサービス パスワード リセットを実行して自己修復し、ユーザーのリスク イベントを閉じて、管理者に対する不要なノイズが発生しないようにすることができます。 

ユーザーにパスワードの変更が求められた場合は、まず多要素認証を完了する必要があります。 アカウントのリスクが検出された場合に備えて、すべてのユーザーが多要素認証に登録されていることを確認する必要があります。  

> [!WARNING]
> ユーザー リスク ポリシーをトリガーする前に、ユーザーがセルフサービス パスワード リセット に登録済みである必要があります。 

パスワード変更制御を使用してポリシーを構成する場合、いくつかの制限があります。  

1. ポリシーは 'すべてのクラウド アプリ' に割り当てる必要があります。 これにより、攻撃者は、別のアプリにサインインするだけで別のアプリを使用してユーザーのパスワードを変更し、アカウントのリスクをリセットすることができなくなります。 
1. パスワードの変更要求は、準拠デバイスの要求など、他のコントロールと共に使用することができません。  
1. パスワード変更のコントロールは、ユーザーとグループの割り当て条件、クラウド アプリの割り当て条件 ([すべて] に設定する必要があります)、およびユーザー リスク条件でのみ使用できます。 

### <a name="terms-of-use"></a>使用条件

社内で利用規約を作成している場合、許可コントロールに追加のオプションが表示されます。 これらのオプションを使用すると、ポリシーによって保護されたリソースにアクセスするための条件として、管理者は利用規約への同意を要求することができます。 利用規約の詳細については、「[Azure Active Directory の利用規約](terms-of-use.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [条件付きアクセス:セッション コントロール](concept-conditional-access-session.md)

- [Conditional Access common policies](concept-conditional-access-policy-common.md) (条件付きアクセスの一般的なポリシー)

- [レポート専用モード](concept-conditional-access-report-only.md)
