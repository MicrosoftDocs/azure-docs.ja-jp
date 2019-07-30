---
title: Azure Active Directory 条件付きアクセスによるアクセス制御の概要 | Microsoft Docs
description: Azure Active Directory の条件付きアクセスのアクセス制御のしくみについて学習します。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 06/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5fc672898a56d8b3e1486b1d8d84cf532fa2b6d
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509408"
---
# <a name="what-are-access-controls-in-azure-active-directory-conditional-access"></a>Azure Active Directory 条件付きアクセスによるアクセス制御の概要

[Azure Active Directory (Azure AD) の条件付きアクセス](../active-directory-conditional-access-azure-portal.md)を使うと、承認されたユーザーによるクラウド アプリへのアクセスを制御できます。 条件付きアクセス ポリシーでは、ポリシーをトリガーする理由 ("～が発生した場合") に対する応答 ("～を実行する") を定義します。

![コントロール](./media/controls/10.png)

条件付きアクセスの文脈では、

- "**When this happens**" (これが発生した場合は) を**条件**と呼びます。
- "**Then do this**" (これを実行する) を**アクセス制御**と呼びます。

条件文とコントロールの組み合わせによって、条件付きアクセス ポリシーを表現します。

![コントロール](./media/controls/61.png)

各コントロールはいずれも、サインインしようとしているユーザーまたはシステムが満たすべき要件と、サインインした後にユーザーができることに対する制約のいずれかになります。

コントロールには、次の 2 つの種類があります:

- **許可コントロール**: アクセスに制約を設けるコントロール
- **セッション コントロール**: セッション内のアクセスを制限するコントロール

このトピックでは、Azure AD の条件付きアクセスで利用できるさまざまなコントロールについて説明します。 

## <a name="grant-controls"></a>許可コントロール

許可コントロールでは、アクセスをすべてブロックするか、必要なコントロールを選んで追加の要件を設定し、それを満たした場合にアクセスを許可するかのどちらかが可能です。 コントロールを複数使用する場合には、次の条件も利用できます:

- 選択したコントロールすべてを満たす (*AND*)
- 選択したコントロールのいずれか 1 つを満たす (*OR*)

![コントロール](./media/controls/18.png)

### <a name="multi-factor-authentication"></a>多要素認証

このコントロールを使用すると、指定されたクラウド アプリに対するアクセスに際して多要素認証を要求できます。 このコントロールでは、次の多要素認証プロバイダーをサポートしています:

- Azure Multi-Factor Authentication
- オンプレミスの多要素認証プロバイダー (ただし、Active Directory フェデレーション サービス (AD FS) を併用する必要があります)

多要素認証を使用すると、承認されていないユーザーが有効なユーザーのプライマリ資格情報を入手した場合でも、リソースにアクセスされる事態を防ぐことができます。

### <a name="compliant-device"></a>準拠デバイス

デバイスベースの条件付きアクセス ポリシーを構成できます。 デバイスベースの条件付きアクセス ポリシーの目的は、選択したクラウド アプリへのアクセスを、[マネージド デバイス](require-managed-devices.md)のみに許可することです。 デバイスが準拠としてマークされていることを要求するのは、マネージド デバイスへのアクセスを制限するための 1 つのオプションです。 デバイスを準拠としてマークするには、Intune (任意のデバイス OS の場合) または Windows 10 デバイス用のサード パーティ製 MDM システムを使用できます。 Windows 10 以外のデバイスの OS の種類のサードパーティ製 MDM システムはサポートされていません。 

デバイスは準拠としてマークするには、その前にデバイスを Azure AD に登録する必要があります。 デバイスを登録するには、次の 3 つのオプションがあります。 

- Azure AD 登録済みデバイス
- Azure AD 参加済みデバイス  
- ハイブリッド Azure AD 参加済みデバイス

これらの 3 つのオプションについては、「[デバイス ID とは](../devices/overview.md)」という記事で説明されています。

詳細については、[条件付きアクセスを使用してクラウド アプリへのアクセスにマネージド デバイスを要求する方法](require-managed-devices.md)に関するページを参照してください。

### <a name="hybrid-azure-ad-joined-device"></a>ハイブリッド Azure AD 参加済みデバイス

デバイスベースの条件付きアクセス ポリシーを構成するための別のオプションとして、ハイブリッド Azure AD 参加済みデバイスの要求があります。 この要件では、Windows デスクトップ、ノート PC、エンタープライズ タブレットのうち、オンプレミスの Active Directory に参加しているデバイスが必要になります。 このオプションを選ぶと、条件付きアクセス ポリシーは、オンプレミスの Active Directory とユーザーの Azure Active Directory に参加しているデバイスで行われるアクセスの試行にアクセスを許可します。  

詳細については、[Azure Active Directory のデバイスベースの条件付きアクセス ポリシーの設定](require-managed-devices.md)に関するページを参照してください。

### <a name="approved-client-app"></a>承認されたクライアント アプリ

従業員は個人的な作業と業務上の作業のどちらにもモバイル デバイスを使用します。このため、会社のデータにアクセスするデバイスが自社で管理しているかどうかにかかわらず、会社のデータを保護できる必要があります。
[Intune のアプリ保護ポリシー](https://docs.microsoft.com/intune/app-protection-policy)を使うと、使用しているモバイル デバイス管理 (MDM) ソリューションを問わず、会社のデータを守ることができます。

承認されたクライアント アプリを使用する方法では、クラウド アプリにアクセスしようとするクライアント アプリに対して [Intune のアプリ保護ポリシー](https://docs.microsoft.com/intune/app-protection-policy) のサポートを要求できます。 たとえば、Exchange Online に対するアクセスを Outlook アプリのみに制限することができます。 承認されたクライアント アプリを要求する条件付きアクセス ポリシーは、[アプリベースの条件付きアクセス ポリシー](app-based-conditional-access.md)とも呼ばれます。 サポートされている承認されたクライアント アプリの一覧は、[承認されたクライアント アプリの要件](technical-reference.md#approved-client-app-requirement)に関するセクションを参照してください。

### <a name="app-protection-policy-preview"></a>アプリ保護ポリシー (プレビュー)

従業員は個人的な作業と業務上の作業のどちらにもモバイル デバイスを使用します。このため、会社のデータにアクセスするデバイスが自社で管理しているかどうかにかかわらず、会社のデータを保護できる必要があります。
[Intune のアプリ保護ポリシー](https://docs.microsoft.com/intune/app-protection-policy)を使うと、使用しているモバイル デバイス管理 (MDM) ソリューションを問わず、会社のデータを守ることができます。

アプリ保護ポリシーによって、[Intune アプリ保護ポリシー](https://docs.microsoft.com/intune/app-protection-policy)を受け取ったことを Azure AD に報告したクライアント アプリケーションにアクセスを制限できます。 たとえば、Exchange Online に対するアクセスを、Intune のアプリ保護ポリシーがある Outlook アプリのみに制限することができます。 アプリ保護ポリシーを必要とする条件付きアクセス ポリシーは、[アプリ保護ベースの条件付きアクセス ポリシー](app-protection-based-conditional-access.md)とも呼ばれます。 

アプリケーションをポリシー保護とマークするには、その前にデバイスを Azure AD に登録する必要があります。

サポートされているポリシー保護されたクライアント アプリの一覧については、[アプリの保護ポリシーの要件](technical-reference.md#app-protection-policy-requirement)に関するセクションを参照してください。

### <a name="terms-of-use"></a>使用条件

テナント内のユーザーが、リソースへのアクセスを許可される前に使用条件に同意することを要求できます。 管理者として、PDF ドキュメントをアップロードすることによって使用条件を構成およびカスタマイズできます。 ユーザーがこのコントロールのスコープに入った場合、アプリケーションへのアクセスは使用条件が同意された場合にのみ許可されます。

## <a name="custom-controls-preview"></a>カスタム コントロール (プレビュー)

カスタム コントロールは、Azure Active Directory Premium P1 エディションの機能です。 カスタム コントロールを使用すると、Azure Active Directory の外部でさらなる要件を満たすために、ユーザーが互換性のあるサービスにリダイレクトされます。 このコントロールを満たすために、ユーザーのブラウザーは外部サービスにリダイレクトされ、すべての必要な認証または検証アクティビティを実行してから、元の Azure Active Directory にリダイレクトされます。 Azure Active Directory は応答を検証し、ユーザーが正常に認証または検証された場合、そのユーザーは条件付きアクセス フロー内にとどまります。

これらのコントロールは、特定の外部サービスまたはカスタム サービスを条件付きアクセス コントロールとして使用できるようにし、一般には条件付きアクセスの機能を拡張します。

現在、互換性のあるサービスを提供しているプロバイダーには次のものがあります。

- [Duo Security](https://duo.com/docs/azure-ca)
- [Entrust Datacard](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [GSMA](https://mobileconnect.io/azure/)
- [Ping Identity](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- RSA
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Silverfort](https://www.silverfort.io/company/using-silverfort-mfa-with-azure-active-directory/)
- [Symantec VIP](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [Thales (Gemalto)](https://resources.eu.safenetid.com/help/AzureMFA/Azure_Help/Index.htm)
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

これらのサービスの詳細については、プロバイダーに直接問い合せてください。

### <a name="creating-custom-controls"></a>カスタム コントロールの作成

カスタム コントロールを作成するには、まず利用するプロバイダーに連絡する必要があります。 Microsoft 以外の各プロバイダーには、サインアップ、サブスクライブ、またはサービスの一部になることや、条件付きアクセスとの統合の意思表示について、独自のプロセスと要件があります。 その時点で、プロバイダーからは JSON 形式のデータ ブロックが提供されます。 このデータにより、プロバイダーと条件付きアクセスがユーザーのテナント用に連携して動作できるようになり、新しいコントロールが作成され、ユーザーがプロバイダーの検証を正常に実行した場合は条件付きアクセスからどのように伝えられるかが定義されます。

カスタム コントロールは、多要素認証を必要とする Identity Protection の自動化に使用できず、 Privileged Identity Manager (PIM) でのロールの昇格に使用できません。

その JSON データをコピーし、それを関連するテキスト ボックスに貼り付けます。 行おうとしている変更を明示的に理解していない限り、JSON を変更しないでください。 少しでも変更すると、プロバイダーと Microsoft の間の接続が中断され、アカウントからロック アウトされる可能性があります。

カスタム コントロールを作成するオプションは、 **[条件付きアクセス]** ページの **[管理]** セクションにあります。

![コントロール](./media/controls/82.png)

**[New custom control] \(新しいカスタム コントロール)** をクリックし、コントロールの JSON データ用のテキスト ボックスを含むブレードを開きます。  

![コントロール](./media/controls/81.png)

### <a name="deleting-custom-controls"></a>カスタム コントロールの削除

カスタム コントロールを削除するには、まずそれがどの条件付きアクセス ポリシーでも使用されていないことを確認する必要があります。 完了したら、次のことを行います。

1. [Custom controls] \(カスタム コントロール) 一覧に移動します。
1. […] をクリックします。  
1. **[削除]** を選択します。

### <a name="editing-custom-controls"></a>カスタム コントロールの編集

カスタム コントロールを編集するには、現在のコントロールを削除し、更新された情報で新しいコントロールを作成する必要があります。

## <a name="session-controls"></a>セッション コントロール

セッション コントロールでは、クラウド アプリ内のエクスペリエンスを制限できます。 セッション コントロールは、クラウド アプリによって適用され、Azure AD がアプリに提供するセッションに関する追加情報に依存します。

![コントロール](./media/controls/31.png)

### <a name="use-app-enforced-restrictions"></a>アプリによって適用される制限を使用する

このコントロールを使用して、選択されたクラウド アプリにデバイス情報を渡すように Azure AD に要求できます。 クラウド アプリは、デバイス情報によって、接続が準拠またはドメイン参加済みデバイスから開始されているかどうかを認識できます。 このコントロールでは、選択されたクラウド アプリとして SharePoint Online と Exchange Online のみがサポートされます。 選択されたクラウド アプリは、デバイス情報を使用して、デバイスの状態に応じて制限付きまたは完全なエクスペリエンスをユーザーに提供します。

詳細については、次を参照してください。

- [SharePoint Online での制限付きアクセスの有効化](https://aka.ms/spolimitedaccessdocs)
- [Exchange Online での制限付きアクセスの有効化](https://aka.ms/owalimitedaccess)

## <a name="next-steps"></a>次の手順

- 条件付きアクセス ポリシーの構成方法を把握するには、「[Azure Active Directory の条件付きアクセスを使用して特定のアプリケーションに対して MFA を必要にする](app-based-mfa.md)」を参照してください。
- 環境に適用する条件付きアクセス ポリシーを構成する準備ができたら、「[Azure Active Directory の条件付きアクセスのベスト プラクティス](best-practices.md)」を参照してください。
