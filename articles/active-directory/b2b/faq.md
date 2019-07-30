---
title: B2B コラボレーションの FAQ - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B コラボレーションに関してよく寄せられる質問への回答を示します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 04/10/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 523f1adc94870f79d198366059f33ad52f5dad68
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67293063"
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Azure Active Directory B2B コラボレーションの FAQ

Azure Active Directory (Azure AD) 企業間 (B2B) コラボレーションに関するよく寄せられる質問 (FAQ) は、新しいトピックを追加して定期的に更新されています。

### <a name="can-we-customize-our-sign-in-page-so-its-more-intuitive-for-our-b2b-collaboration-guest-users"></a>B2B コラボレーションのゲスト ユーザー向けに、より直感的にするため、サインイン ページをカスタマイズすることはできますか。
もちろんあります。 [この機能に関するブログ記事](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/)をご覧ください。 組織のサインイン ページをカスタマイズする方法の詳細については、「[サインイン ページとアクセス パネル ページに会社のブランドを追加する](../fundamentals/customize-branding.md)」をご覧ください。

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>B2B コラボレーションのユーザーは、SharePoint Online と OneDrive にアクセスできますか。
はい。 ただし、SharePoint Online で、ユーザー選択ウィンドウを使用して既存のゲスト ユーザーを検索する機能は、既定で**オフ**になっています。 既存のゲスト ユーザーを検索するオプションを有効にするには、**ShowPeoplePickerSuggestionsForGuestUsers** を **On** に設定します。 この設定は、テナント レベルで、またはサイト コレクション レベルで有効にできます。 この設定を変更するには、Set-SPOTenant および Set-SPOSite コマンドレットを使用します。 これらのコマンドレットにより、メンバーは、ディレクトリ内のすべての既存のゲスト ユーザーを検索できます。 テナントのスコープの変更は、既にプロビジョニングされている SharePoint Online サイトには影響しません。

### <a name="is-the-csv-upload-feature-still-supported"></a>CSV のアップロード機能は、まだサポートされていますか。
はい。 .csv ファイルのアップロード機能の使用の詳細については、[この PowerShell サンプル](code-samples.md)をご覧ください。

### <a name="how-can-i-customize-my-invitation-emails"></a>招待の電子メールは、どのようにカスタマイズできますか。
[B2B 招待 API](customize-invitation-api.md) を使用して、招待元プロセスのほぼすべてをカスタマイズすることができます。

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>ゲスト ユーザーは、Multi-Factor Authentication 方法をリセットできますか。
はい。 ゲスト ユーザーは、通常のユーザーと同じように、Multi-Factor Authentication 方法をリセットできます。

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>どの組織が Multi-Factor Authentication ライセンスを担当しますか。
招待側組織が Multi-Factor Authentication を実行します。 招待側組織は、Multi-Factor Authentication を使用している B2B ユーザーに対する十分なライセンスがあることを確認する必要があります。

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>パートナー組織で、既に Multi-Factor Authentication を設定している場合はどうなりますか。 パートナー組織の Multi-Factor Authentication を信頼でき、独自の Multi-Factor Authentication は使用しないのですか。
この機能は、現時点ではサポートされていません。 組織のリソースへのアクセスで多要素認証が必要な場合、取引先組織に対する多要素認証を (招待側) 組織に登録する必要があります。

### <a name="how-can-i-use-delayed-invitations"></a>招待の遅延は、どのように使用するのですか。
組織で、B2B コラボレーション ユーザーを追加し、必要に応じてそれらのユーザーをアプリケーションにプロビジョニングして、招待を送信したいと考える場合があります。 B2B コラボレーションの招待 API を使用して、オンボード ワークフローをカスタマイズできます。

### <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Exchange のグローバル アドレス一覧にゲスト ユーザーを表示できますか。
はい。 既定では、ゲスト オブジェクトは組織のグローバル アドレス一覧 (GAL) には表示されませんが、Azure Active Directory PowerShell を使用してそれらを表示できます。 「[グローバル アドレス一覧にゲスト オブジェクトを表示できますか?](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups#can-i-make-guest-objects-visible-in-the-global-address-list)」をご覧ください

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>ゲスト ユーザーを制限付き管理者にできますか。
そして、 詳細については、[ゲスト ユーザーのロールへの追加](add-guest-to-role.md)に関するページをご覧ください。

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>Azure AD B2B コラボレーションでは、B2B ユーザーの Azure ポータルへのアクセスを許可していますか。
B2B コラボレーション ユーザーは、制限付き管理者のロールが割り当てられない限り、Azure portal にアクセスする必要はありません。 ただし、制限付き管理者のロールを割り当てられている B2B コラボレーション ユーザーは portal にアクセスできます。 また、これらのいずれかの管理者ロールが割り当てられていないゲスト ユーザーがポータルにアクセスすると、ユーザーは、エクスペリエンスの特定の部分にアクセスできます。 ゲスト ユーザー ロールは、ディレクトリのアクセス許可の一部を持ちます。

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>ゲスト ユーザーに対して Azure Portal へのアクセスをブロックできますか。
はい。 このポリシーを構成する場合は、誤ってメンバーと管理者のアクセスをブロックしないように注意してください。
ゲスト ユーザーの [Azure portal](https://portal.azure.com) へのアクセスをブロックするには、Windows Azure クラシック デプロイ モデル API で条件付きアクセス ポリシーを使用します。
1. **すべてのユーザー** グループをメンバーだけが含まれるように変更します。
   ![UserType が [ゲスト] と等しくない [すべてのユーザー] グループを示すスクリーンショット](media/faq/modify-all-users-group.png)
2. ゲスト ユーザーを含む動的グループを作成します。
   ![新しい [すべてのゲスト ユーザー] グループを示すスクリーンショット](media/faq/group-with-guest-users.png)
3. 次のビデオで示されているように、ゲスト ユーザーによるポータルへのアクセスをブロックする条件付きアクセス ポリシーを設定します。
  
   > [!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player] 

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>Azure AD B2B コラボレーションは、Multi-Factor Authentication とコンシューマー電子メール アカウントをサポートしていますか。
はい。 Azure AD B2B コラボレーションでは、Multi-Factor Authentication とコンシューマー電子メール アカウントのどちらもサポートされています。

### <a name="do-you-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Azure AD B2B コラボレーション ユーザーのパスワード リセットはサポートされていますか。
Azure AD テナントがユーザーのホーム ディレクトリである場合、管理者は Azure portal から[ユーザーのパスワードをリセットする](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-reset-password-azure-portal)ことができます。 ただし、別の Azure AD ディレクトリまたは外部の ID プロバイダーによって管理されているアカウントでサインインしているゲスト ユーザーのパスワードを、直接リセットすることはできません。 パスワードをリセットできるのは、ゲスト ユーザーまたはユーザーのホーム ディレクトリの管理者だけです。 ゲスト ユーザーに対するパスワード リセットの動作の例をいくつか次に示します。
 
* Microsoft アカウント (たとえば guestuser@live.com) でサインインしたゲスト ユーザーは、Microsoft アカウントのセルフサービスのパスワード リセット (SSPR) を使用して、自分のパスワードをリセットすることができます。 「[Microsoft アカウントのパスワードをリセットする方法](https://support.microsoft.com/help/4026971/microsoft-account-how-to-reset-your-password)」をご覧ください。
* Google アカウントまたはそれ以外の外部 ID プロバイダーでサインインしたゲスト ユーザーは、ID プロバイダーの SSPR 方法を使用して、自分のパスワードをリセットできます。 たとえば、Google アカウント guestuser@gmail.com のゲスト ユーザーは、「[パスワードを変更または再設定する](https://support.google.com/accounts/answer/41078)」の手順に従って自分のパスワードをリセットできます。
* ID テナントが Just-In-Time (JIT) テナントまたは "バイラル" テナント (つまり、独立したアンマネージド Azure テナント) である場合は、ゲスト ユーザーだけが自分のパスワードをリセットできます。 場合によっては、組織が、従業員が仕事用メール アドレスを使用してサービスにサインアップするときに作成される[バイラル テナントの管理を引き継ぎます](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover)。 組織がバイラル テナントを引き継いだ後は、その組織の管理者しか、ユーザーのパスワードをリセットしたり SSPR を有効にしたりできなくなります。 必要に応じて、招待側の組織としては、ディレクトリからゲスト ユーザー アカウントを削除し、招待を再送信することができます。
* ゲスト ユーザーのホーム ディレクトリが Azure AD テナントの場合は、管理者がユーザーのパスワードをリセットできます。 たとえば、管理者がオンプレミスの Active Directory からユーザーを作成または同期し、UserType を Guest に設定したような場合です。 このユーザーは管理者のディレクトリに所属しているため、管理者は Azure portal からユーザーのパスワードをリセットできます。

### <a name="does-microsoft-dynamics-365-provide-online-support-for-azure-ad-b2b-collaboration"></a>Microsoft Dynamics 365 は、Azure AD B2B コラボレーションをオンラインでサポートしていますか。
はい、Dynamics 365 (オンライン) では、Azure AD B2B コラボレーションがサポートされています。 詳細については、Dynamics 365 に関する記事「[Azure Active Directory B2B コラボレーションでユーザーを招待する](https://docs.microsoft.com/dynamics365/customer-engagement/admin/invite-users-azure-active-directory-b2b-collaboration)」を参照してください。

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>新しく作成した B2B コラボレーション ユーザー用の初期パスワードの有効期間はどうなっていますか。
Azure AD には、文字、パスワードの強度、およびアカウント ロックアウトに関する要件の固定セットがあり、これはすべての Azure AD クラウド ユーザー アカウントにも同様に適用されます。 クラウド ユーザー アカウントは、次のような別の ID プロバイダーとフェデレーションされないアカウントです。 
* Microsoft アカウント
* Facebook
* Active Directory フェデレーション サービス (AD FS)
* (B2B コラボレーション用の) 別のクラウド テナント

フェデレーション アカウントの場合、パスワード ポリシーは、オンプレミス テナントに適用されるポリシーとユーザーの Microsoft アカウント設定に依存します。

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>組織で、アプリケーションにテナント ユーザーとゲスト ユーザーに対して異なるエクスペリエンスを設定したいと考える場合があります。 これを行うための標準的なガイダンスはありますか。 ID プロバイダー要求の存在は、使用する正しいモデルですか。
ゲスト ユーザーは、任意の ID プロバイダーを使用して認証できます。 詳細については、[B2B コラボレーション ユーザーのプロパティ](user-properties.md)に関するページをご覧ください。 **UserType** プロパティを使用して、ユーザー エクスペリエンスを決定します。 **UserType 要求**は、現在トークンに含まれていません。 アプリケーションでは、Graph API を使用してディレクトリでユーザーのクエリを実行し、UserType を取得する必要があります。

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>ソリューションを共有し、アイデアを提供するための B2B コラボレーション コミュニティにはどこからアクセスできますか。
B2B コラボレーションを向上させるためのフィードバックは絶えず受け付けています。 ユーザー シナリオ、ベスト プラクティス、また Azure AD B2B コラボレーションに関する意見を提供してください。 [マイクロソフト Tech コミュニティ](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b)のディスカッションにご参加ください。
 
また、[B2B Collaboration Ideas](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas) でも、ご意見の送信や、今後の機能への投票を行うことができます。

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>ユーザーがすぐに "準備" できるように、自動的に引き換えられる招待を送信できますか。 それとも、ユーザーは常に引き換えの URL をクリックする必要があるのですか。
招待元は、UI、PowerShell スクリプト、または API を使用して、取引先組織の他のユーザーを招待できます。 その後、招待元はゲスト ユーザーに共有アプリへの直接リンクを送信できます。 ほとんどの場合、メール招待状を開いて、引き換えの URL をクリックする必要はなくなります。 「[Azure Active Directory B2B コラボレーションの招待の利用](redemption-experience.md)」をご覧ください。

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>招待されたパートナーがフェデレーションを使用して独自のオンプレミス認証を追加するとき、B2B コラボレーションはどのように機能しますか?
パートナーの Azure AD テナントがオンプレミス認証インフラストラクチャにフェデレーションされている場合、オンプレミスのシングル サインオン (SSO) が自動的に実行されます。 パートナーに Azure AD テナントがない場合、新しいユーザーに対し Azure AD アカウントが作成されます。 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Azure AD B2B は gmail.com および outlook.com の電子メール アドレスを受け付けず、このようなアカウントに対しては、B2C が使われていたと思います。
マイクロソフトでは、サポートされる ID に関して、B2B と business-to-consumer (B2C) のコラボレーションの違いをなくしています。 使用される ID は、B2B と B2C のどちらを使うかを選択する十分な理由になりません。 コラボレーション オプションの選択については、「[Azure Active Directory での B2B コラボレーションと B2C の比較](compare-with-b2c.md)」をご覧ください。

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Azure B2B ゲスト ユーザーをサポートするのはどのアプリケーションとサービスですか。
すべての Azure AD 統合型アプリケーションでは、Azure B2B ゲスト ユーザーをサポートできますが、ゲスト ユーザーを認証するにはテナントとして設定されたエンドポイントを使用する必要があります。 また、ゲスト ユーザーがアプリに対して認証を行うと発行される SAML トークン内の[要求をカスタマイズする](claims-mapping.md)ことも必要になる場合があります。 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>パートナーが Multi-Factor Authentication を使用していない場合に、B2B ゲスト ユーザーに Multi-Factor Authentication を強制できますか。
はい。 詳細は、「[B2B コラボレーション ユーザーの条件付きアクセス](conditional-access.md)」をご覧ください。

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>SharePoint では、外部ユーザーに対して "許可" または "拒否" リストを定義できます。 これを Azure で実行できますか。
はい。 Azure AD B2B コラボレーションでは、許可リストと拒否リストをサポートしています。 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Azure AD B2B を使用する必要があるライセンスは何ですか。
組織で Azure AD B2B を使用する必要があるライセンスについては、「[Azure Active Directory B2B コラボレーションのライセンスに関するガイダンス](licensing-guidance.md)」をご覧ください。

### <a name="next-steps"></a>次の手順

- [Azure AD B2B コラボレーションとは](what-is-b2b.md)

