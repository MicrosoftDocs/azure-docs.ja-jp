---
title: Azure Active Directory 条件付きアクセスの条件の概要 | Microsoft Docs
description: Azure Active Directory の条件付きアクセスで条件を使用してポリシーをトリガーする方法について学習します。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 05/17/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e6c18b7ffca83c8e0fe9576ec275f89b6db7b4f
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509017"
---
# <a name="what-are-conditions-in-azure-active-directory-conditional-access"></a>Azure Active Directory 条件付きアクセスの条件の概要

[Azure Active Directory (Azure AD) の条件付きアクセス](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)を使用して、お客様のクラウド アプリにユーザーがどのようにアクセスするかを制御できます。 条件付きアクセス ポリシーでは、ポリシーをトリガーする理由 ("...が発生した場合") に対する応答 ("...を実行する") を定義します。

![理由と応答](./media/conditions/10.png)

条件付きアクセスのコンテキストで、**When this happens** (この状況になった場合) は**条件**と呼ばれます。 **Then do this** (その場合はこの処理を実行する) は**アクセス制御**と呼ばれます。 条件とアクセスの制御の組み合わせによって、条件付きアクセス ポリシーを表現します。

![条件付きアクセス ポリシー](./media/conditions/61.png)

条件付きアクセス ポリシーで構成していない条件は適用されません。 一部の条件は、条件付きアクセス ポリシーを環境に適用するために[必須](best-practices.md)です。

この記事では、条件の概要と、条件付きアクセス ポリシーでの使用方法について説明します。 

## <a name="users-and-groups"></a>ユーザーとグループ

ユーザーとグループの条件は、条件付きアクセス ポリシーに必須です。 ポリシーでは、 **[すべてのユーザー]** を選択するか、特定のユーザーとグループを選択することができます。

![ユーザーとグループ](./media/conditions/111.png)

**[すべてのユーザー]** を選択すると、ゲスト ユーザーを含め、ディレクトリ内のすべてのユーザーにポリシーが適用されます。

**[ユーザーとグループの選択]** を選択すると、次のオプションを設定できます。

* **[すべてのゲスト ユーザー]** では、ポリシーのターゲットを B2B ゲスト ユーザーに設定することができます。 この条件は、**userType** 属性が **guest** に設定されている任意のユーザー アカウントと一致します。 この設定は、Azure AD の招待フローでアカウントが作成されたときにすぐにポリシーを適用する必要がある場合に使用します。
* **[ディレクトリ ロール]** では、ユーザーのロールの割り当てに基づいてポリシーのターゲットを設定できます。 この条件は、**グローバル管理者**または**パスワード管理者**などのディレクトリ ロールをサポートします。
* **[ユーザーとグループ]** は、特定セットのユーザーをターゲットとしています。 たとえば、人事部アプリがクラウド アプリとして選択されている場合は、人事部のすべてのメンバーを含むグループを選択できます。 Azure AD 内の任意の種類のグループを指定できます。これには、動的なグループや、割り当て済みのセキュリティ グループおよび配布グループが含まれます。

ポリシーから特定のユーザーまたはグループを除外することもできます。 一般的なユース ケースとして、サービス アカウントに対してポリシーで多要素認証 (MFA) を強制的に適用する例が挙げられます。

新しいポリシーをデプロイする際は、特定のユーザーのセットをターゲットにすると役立ちます。 新しいポリシーでは、初期のユーザー セットのみをターゲットにして、ポリシーの動作を検証することをお勧めします。

## <a name="cloud-apps-and-actions"></a>クラウド アプリとアクション

クラウド アプリは、Azure AD アプリケーション プロキシで保護されている Web サイト、サービス、またはエンドポイントです。 サポートされているクラウド アプリの詳細については、[クラウド アプリの割り当て](technical-reference.md#cloud-apps-assignments)に関する記事を参照してください。 **クラウド アプリまたはアクション**の条件は、条件付きアクセス ポリシーに必須です。 ポリシーでは、 **[すべてのクラウド アプリ]** を選択するか、 **[アプリを選択]** を使用してアプリを指定することができます。

組織は、次から選択できます。

* **[すべてのクラウド アプリ]** 。組織全体に適用するベースライン ポリシーを適用する場合。 この選択は、いずれかのクラウド アプリでサインイン リスクが検出されたときに多要素認証を要求するポリシーに使用します。 [すべてのクラウド アプリ] に適用されるポリシーは、すべての Web サイトおよびサービスへのアクセスに適用されます。 この設定は、[アプリを選択] 一覧に表示されるクラウド アプリに限定されません。
* 特定のサービスをお客様のポリシーの対象にするには、 **[アプリを選択]** を選択します。 たとえば、SharePoint Online にアクセスするには準拠デバイスを使用するようユーザーに要求できます。 このポリシーは、他のサービスが SharePoint コンテンツにアクセスする際にも適用されます。 たとえば、Microsoft Teams などです。

> [!NOTE]
> ポリシーから特定のアプリを除外できます。 ただし、アクセスするサービスに適用されるポリシーがあれば、これらのアプリも影響を受けます。

**ユーザー アクション**とは、ユーザーが実行できるタスクです。 現在サポートされている唯一のアクションは、**セキュリティに関する情報の登録 (プレビュー)** です。これにより、統合された登録が有効になったユーザーが自分のセキュリティに関する情報を登録しようとすると、条件付きアクセス ポリシーが適用されます。 詳細情報については、[統合されたセキュリティ情報の登録の有効化 (プレビュー)](../authentication/howto-registration-mfa-sspr-combined.md) に関する記事を参照してください。

## <a name="sign-in-risk"></a>サインイン リスク

サインイン リスクは、サインインがユーザー アカウントの正当な所有者によって行われなかった可能性のレベル (高、中、低) を示します。 Azure AD では、ユーザーのサインイン時にサインイン リスク レベルを計算します。 計算されたサインイン リスク レベルを条件付きアクセス ポリシーの条件として利用できます。

![サインインのリスク レベル](./media/conditions/22.png)

この条件を使用するには、[Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable) を有効にする必要があります。
 
この条件の一般的なユース ケースには、次のような保護を含むポリシーがあります。 

- サインイン リスクが高いユーザーをブロックする。 このような保護で、正当ではない可能性があるユーザーがクラウド アプリにアクセスすることを防ぐことができます。 
- サインイン リスクが中であるユーザーに多要素認証を要求する。 多要素認証を強制的に適用することで、サインインがアカウントの正当な所有者によって実行されていることを確認し、信頼性を強化できます。

詳細については、[セッションのリスクが検出されたときにアクセスをブロックする](app-sign-in-risk.md)方法に関するページを参照してください。  

## <a name="device-platforms"></a>デバイス プラットフォーム

デバイス プラットフォームは、デバイスで実行されているオペレーティング システムによって分類されます。 Azure Active Directory では、ユーザー エージェントなどのデバイスによって提供される情報を使用して、プラットフォームを識別します。 この情報は検証されません。 すべてのプラットフォームにポリシーを適用することをお勧めします。 ポリシーでは、アクセスをブロックするか、Microsoft Intune ポリシーへの準拠を必須にするか、デバイスのドメインへの参加を必須にします。 既定では、すべてのデバイス プラットフォームにポリシーが適用されます。 

![デバイス プラットフォームを構成する](./media/conditions/24.png)

サポートされているデバイス プラットフォームの一覧については、[デバイス プラットフォームの条件](technical-reference.md#device-platform-condition)に関する記事を参照してください。

この条件の一般的なユース ケースとしては、クラウド アプリへのアクセスを[マネージド デバイス](require-managed-devices.md)のみに制限するポリシーが挙げられます。 デバイス プラットフォームの条件を使用するその他のシナリオについては、「[Azure Active Directory のアプリベースの条件付きアクセス](app-based-conditional-access.md)」をご覧ください。

## <a name="device-state"></a>デバイスの状態

デバイスの状態の条件では、ハイブリッド Azure AD 参加デバイスと、準拠とマークされたデバイスを条件付きアクセス ポリシーから除外します。 

![デバイスの状態を構成する](./media/conditions/112.png)

この条件は、追加のセッション セキュリティを提供するために、アンマネージド デバイスにのみポリシーを適用する必要がある場合に便利です。 たとえば、アンマネージド デバイスの場合にのみ、Microsoft Cloud App Security のセッション制御を実施します。 

## <a name="locations"></a>Locations

場所を使用すると、接続が試行された場所に基づいて条件を定義できます。 

![場所を構成する](./media/conditions/25.png)

この条件の一般的なユース ケースには、次のような保護を含むポリシーがあります。

- ユーザーが社内ネットワークから離れているときにサービスにアクセスする場合に、多要素認証を必須にする。  
- ユーザーが特定の国や地域からサービスにアクセスする場合にアクセスをブロックする。 

詳細については、「[Azure Active Directory 条件付きアクセスの場所の条件の概要](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations)」を参照してください。

## <a name="client-apps"></a>クライアント アプリ

既定では、条件付きアクセス ポリシーが次のアプリに適用されます。

- **[ブラウザー アプリ](technical-reference.md#supported-browsers)** -  ブラウザー アプリには、SAML、WS-Federation、または OpenID Connect の Web SSO プロトコルが使用された Web サイトが含まれます。 OAuth の機密クライアントとして登録されたあらゆる Web サイトまたは Web サービスも、これに該当します。 たとえば、Office 365 SharePoint Web サイトなどです。 
- **[先進認証が使用されるモバイル アプリとデスクトップ アプリ](technical-reference.md#supported-mobile-applications-and-desktop-clients)** - これらのアプリには、Office デスクトップ アプリやスマートフォン アプリなどが該当します。 


加えて、先進認証が使用されていない特定のクライアント アプリをポリシーの対象にすることができます。例:

- **[Exchange ActiveSync クライアント](conditions.md#exchange-activesync-clients)** - Exchange ActiveSync の使用がポリシーによってブロックされている場合、該当するユーザーには、ブロックされた理由が記載された検疫メールが 1 通届きます。 必要な場合は、デバイスを Intune に登録する手順がメールに記載されています。
- **[その他のクライアント](block-legacy-authentication.md)** - これらのアプリには、基本認証とメール プロトコル (IMAP、MAPI、POP、SMTP など) が使用されるクライアントや、先進認証が使用されない以前の Office アプリが含まれます。 詳細については、「[How modern authentication works for Office 2013 and Office 2016 client apps (Office 2013 クライアント アプリと Office 2016 クライアント アプリでの先進認証のしくみ)](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016)」を参照してください。

![クライアント アプリ](./media/conditions/41.png)

この条件の一般的なユース ケースには、次のような要件を含むポリシーがあります。

- データをデバイスにダウンロードするモバイル アプリケーションやデスクトップ アプリケーションについて **[マネージド デバイスを必須にする](require-managed-devices.md)** 。 同時に、ブラウザー アクセスはすべてのデバイスに許可する。 このシナリオでは、アンマネージド デバイスへのドキュメントの保存と同期が禁止されます。 この方法を使用すると、デバイスが紛失したり盗難にあったりした場合のデータ損失の可能性を低くすることができます。
- Exchange Online へのアクセスに ActiveSync が使用されるアプリについて **[マネージド デバイスを必須にする](require-managed-devices.md)** 。
- Azure AD (他のクライアント) に対する **[レガシ認証をブロックする](block-legacy-authentication.md)** 。
- Web アプリからのアクセスはブロックするが、モバイル アプリとデスクトップ アプリからのアクセスは許可する。

### <a name="exchange-activesync-clients"></a>Exchange ActiveSync クライアント

以下の場合、選択できるのは **Exchange ActiveSync クライアント**だけです。

- Microsoft Office 365 Exchange Online が、お客様が選択した唯一のクラウド アプリである。

    ![クラウド アプリ](./media/conditions/32.png)

- ポリシーに他の条件を構成していない。 ただし、この条件の範囲を絞り込んで、[サポートされるプラットフォーム](technical-reference.md#device-platform-condition)のみに適用できます。
 
    ![サポートされているプラットフォームのみにポリシーを適用する](./media/conditions/33.png)

[マネージド デバイス](require-managed-devices.md)が必須であるためにアクセスがブロックされた場合、該当するユーザーには、Intune の使い方を案内するメールが 1 通届きます。 

承認済みのアプリが必須である場合、該当するユーザーには、Outlook モバイル クライアントをインストールして使用するためのガイドラインが与えられます。

その他のケースでは (MFA が必須である場合など)、該当するユーザーはブロックされます。基本認証が使用されるクライアントで MFA がサポートされないためです。

この設定の対象にできるのは、ユーザーとグループだけです。 ゲストまたはロールはサポートされません。 ゲストまたはロールの条件が構成された場合、ポリシーをユーザーに適用する必要があるかどうかを条件付きアクセスでは判別できないため、すべてのユーザーがブロックされます。

詳細については、次を参照してください。

- [SharePoint Online と Exchange Online に Azure Active Directory の条件付きアクセスを設定する](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication)。
- [Azure Active Directory のアプリベースの条件付きアクセス](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)。 

## <a name="next-steps"></a>次の手順

- 条件付きアクセス ポリシーを構成する方法については、「[クイック スタート:Azure Active Directory の条件付きアクセスを使用して特定のアプリケーションに対して MFA を必要にする](app-based-mfa.md)」を参照してください。
- 環境に適用する条件付きアクセス ポリシーを構成するには、「[Azure Active Directory の条件付きアクセスのベスト プラクティス](best-practices.md)」を参照してください。 
