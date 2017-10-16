---
title: "Azure Active Directory の条件付きアクセスのコントロール | Microsoft Docs"
description: "Azure Active Directory の条件付きアクセスのコントロールのしくみについて学習します。"
services: active-directory
keywords: "アプリへの条件付きアクセス, Azure AD での条件付きアクセス, 企業リソースへの安全なアクセス, 条件付きアクセス ポリシー"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: be3631db20ae744965f9f6677c536ade45e34c49
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="controls-in-azure-active-directory-conditional-access"></a>Azure Active Directory の条件付きアクセスのコントロール 

[Azure Active Directory (Azure AD) の条件付きアクセス](active-directory-conditional-access-azure-portal.md)を使うと、承認されたユーザーがどのようにクラウド アプリにアクセスするかを制御できます。 条件付きアクセス ポリシーでは、特定の条件 ("～が発生した場合") に対する応答 ("～を実行する") を定義します。 条件付きアクセスの文脈では、 

- "**When this happens**" (これが発生した場合は) を**条件文**と呼びます

- "**Then do this**" (これを実行する) を**コントロール**と呼びます

![コントロール](./media/active-directory-conditional-access-controls/11.png)

条件文とコントロールの組み合わせによって、条件付きアクセス ポリシーを表現します。

![コントロール](./media/active-directory-conditional-access-controls/12.png)

各コントロールはいずれも、サインインしようとしているユーザーまたはシステムが満たすべき要件と、サインインした後にユーザーができることに対する制約のいずれかになります。 

コントロールには、次の 2 つの種類があります: 

- **許可コントロール**: アクセスに制約を設けるコントロール

- **セッション コントロール**: セッション内のアクセスを制限するコントロール

このトピックでは、Azure AD の条件付きアクセスで利用できるさまざまなコントロールについて説明します。 

## <a name="grant-controls"></a>許可コントロール

許可コントロールでは、アクセスをすべてブロックするか、必要なコントロールを選んで追加の要件を設定し、それを満たした場合にアクセスを許可するかのどちらかが可能です。 コントロールを複数使用する場合には、次の条件も利用できます:

- 選択したコントロールすべてを満たす (*AND*) 
- 選択したコントロールのいずれか 1 つを満たす (*OR*)

![コントロール](./media/active-directory-conditional-access-controls/17.png)



### <a name="multi-factor-authentication"></a>多要素認証

このコントロールを使用すると、指定されたクラウド アプリに対するアクセスに際して多要素認証を要求できます。 このコントロールでは、次の多要素認証プロバイダーをサポートしています: 

- Azure Multi-Factor Authentication 

- オンプレミスの多要素認証プロバイダー (ただし、Active Directory フェデレーション サービス (AD FS) を併用する必要があります)
 
多要素認証を使用すると、承認されていないユーザーが有効なユーザーのプライマリ資格情報を入手した場合でも、リソースにアクセスされる事態を防ぐことができます。



### <a name="compliant-device"></a>準拠デバイス

デバイスベースの条件付きアクセス ポリシーを構成できます。 デバイス ベースの条件付きアクセス ポリシーの目的は、構成されたリソースへのアクセスを、信頼されたデバイスのみに許可することです。 準拠デバイスの要求は、信頼されるデバイスを定義する必要のある 1 つのオプションです。 詳細については、[Azure Active Directory のデバイス ベースの条件付きアクセス ポリシーの設定](active-directory-conditional-access-policy-connected-applications.md)を参照してください。

### <a name="domain-joined-device"></a>ドメイン参加済みデバイス

デバイス ベースの条件付きアクセス ポリシーを構成するための別のオプションとして、ドメインに参加しているデバイスの要求があります。 この要件では、Windows デスクトップ、ノート PC、エンタープライズ タブレットのうち、オンプレミスの Active Directory に参加しているデバイスが必要になります。 詳細については、[Azure Active Directory のデバイス ベースの条件付きアクセス ポリシーの設定](active-directory-conditional-access-policy-connected-applications.md)を参照してください。





### <a name="approved-client-app"></a>承認されたクライアント アプリ

従業員は個人的な作業と業務上の作業のどちらにもモバイル デバイスを使用します。このため、会社のデータにアクセスするデバイスが自社で管理しているかどうかにかかわらず、会社のデータを保護できる必要があります。
[Intune のアプリ保護ポリシー](https://docs.microsoft.com/intune/app-protection-policy)を使うと、使用しているモバイル デバイス管理 (MDM) ソリューションを問わず、会社のデータを守ることができます。


承認されたクライアント アプリを使用する方法では、クラウド アプリにアクセスしようとするクライアント アプリに対して [Intune のアプリ保護ポリシー](https://docs.microsoft.com/intune/app-protection-policy) のサポートを要求できます。 たとえば、Exchange Online に対するアクセスを Outlook アプリのみに制限することができます。 承認されたクライアント アプリを要求する条件付きアクセス ポリシーは、[アプリベースの条件付きアクセス ポリシー](active-directory-conditional-access-mam.md)とも呼ばれます。 サポートされている承認されたクライアント アプリの一覧は、[承認されたクライアント アプリの要件](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)に関するセクションを参照してください。


### <a name="terms-of-use"></a>使用条件

テナント内のユーザーが、リソースへのアクセスを許可される前に使用条件に同意することを要求できます。 管理者として、PDF ドキュメントをアップロードすることによって使用条件を構成およびカスタマイズできます。 ユーザーがこのコントロールのスコープに入った場合、アプリケーションへのアクセスは使用条件が同意された場合にのみ許可されます。 


### <a name="custom-controls"></a>カスタム コントロール 

Azure Active Directory の外部にあるさらなる要件を満たすためにユーザーを互換性のあるサービスにリダイレクトする条件付きアクセスのカスタム コントロールを作成できます。 これにより、特定の外部の多要素認証および検証プロバイダーを使用して、条件付きアクセスの規則を適用したり、独自のカスタム サービスを構築したりできます。 このコントロールを満たすために、ユーザーのブラウザーは外部サービスにリダイレクトされ、すべての必要な認証または検証アクティビティを実行してから、元の Azure Active Directory にリダイレクトされます。 ユーザーが正常に認証または検証された場合、そのユーザーは条件付きアクセス フロー内にとどまります。 

## <a name="custom-controls"></a>カスタム コントロール

条件付きアクセスのカスタム コントロールは、Azure Active Directory の外部にあるさらなる要件を満たすためにユーザーを互換性のあるサービスにリダイレクトします。 このコントロールを満たすために、ユーザーのブラウザーは外部サービスにリダイレクトされ、すべての必要な認証または検証アクティビティを実行してから、元の Azure Active Directory にリダイレクトされます。 Azure Active Directory は応答を検証し、ユーザーが正常に認証または検証された場合、そのユーザーは条件付きアクセス フロー内にとどまります。

これらのコントロールは、特定の外部サービスまたはカスタム サービスを条件付きアクセス コントロールとして使用できるようにし、一般には条件付きアクセスの機能を拡張します。

現在、互換性のあるサービスを提供しているプロバイダーには次のものがあります。

- Duo Security

- RSA

- Trusona

これらのサービスの詳細については、プロバイダーに直接問い合せてください。

### <a name="creating-custom-controls"></a>カスタム コントロールの作成

カスタム コントロールを作成するには、まず利用するプロバイダーに連絡する必要があります。 Microsoft 以外の各プロバイダーには、サインアップ、サブスクライブ、またはそれ以外のサービス利用方法を実行し、条件付きアクセスと統合したいことを示すための独自のプロセスと要件があります。 その時点で、プロバイダーからは JSON 形式のデータ ブロックが提供されます。 このデータにより、プロバイダーと条件付きアクセスがユーザーのテナント用に連携して動作できるようになり、新しいコントロールが作成され、ユーザーがプロバイダーの検証を正常に実行した場合は条件付きアクセスからどのように伝えられるかが定義されます。

その JSON データをコピーし、それを関連するテキスト ボックスに貼り付けます。 行おうとしている変更を明示的に理解していない限り、JSON を変更しないでください。 少しでも変更すると、プロバイダーと Microsoft の間の接続が中断され、アカウントからロック アウトされる可能性があります。

カスタム コントロールを作成するオプションは、**[条件付きアクセス]** ページの **[管理]** セクションにあります。

![コントロール](./media/active-directory-conditional-access-controls/82.png)

**[New custom control] (新しいカスタム コントロール)** をクリックし、コントロールの JSON データ用のテキスト ボックスを含むブレードを開きます。  


![コントロール](./media/active-directory-conditional-access-controls/81.png)


### <a name="deleting-custom-controls"></a>カスタム コントロールの削除

カスタム コントロールを削除するには、まずそれがどの条件付きアクセス ポリシーでも使用されていないことを確認する必要があります。 完了したら、次のことを行います。

1. [Custom controls] (カスタム コントロール) 一覧に移動します。

2. […] をクリックします。  

3. **[削除]**を選択します。

### <a name="editing-custom-controls"></a>カスタム コントロールの編集

カスタム コントロールを編集するには、現在のコントロールを削除し、更新された情報で新しいコントロールを作成する必要があります。




## <a name="session-controls"></a>セッション コントロール

セッション コントロールでは、クラウド アプリ内のエクスペリエンスを制限できます。 セッション コントロールは、クラウド アプリによって適用され、Azure AD がアプリに提供するセッションに関する追加情報に依存します。

![コントロール](./media/active-directory-conditional-access-controls/31.png)

### <a name="use-app-enforced-restrictions"></a>アプリによって適用される制限を使用する

このコントロールを使用して、デバイス情報をクラウド アプリに渡すよう Azure AD に要求できます。 これにより、クラウド アプリは、ユーザーが準拠デバイスとドメイン参加済みデバイスのどちらからアクセスしているかを把握できます。 このコントロールは、現在、クラウド アプリとしての SharePoint でのみサポートされます。 SharePoint では、デバイス情報を使用して、デバイスの状態に応じて制限付きまたは完全なエクスペリエンスをユーザーに提供します。
SharePoint での制限付きアクセスを要求する方法について詳しくは、[「非管理対象デバイスからのアクセスを制御する」](https://aka.ms/spolimitedaccessdocs)をご覧ください。



## <a name="next-steps"></a>次のステップ

- 条件付きアクセスポリシーの構成方法については、「[Azure Active Directory での条件付きアクセスの基本](active-directory-conditional-access-azure-portal-get-started.md)」を参照してください。

- 環境に適用する条件付きアクセス ポリシーを構成する準備ができたら、「[Azure Active Directory の条件付きアクセスのベスト プラクティス](active-directory-conditional-access-best-practices.md)」を参照してください。 
