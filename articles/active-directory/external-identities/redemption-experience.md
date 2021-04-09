---
title: B2B コラボレーションの招待の利用 - Azure AD
description: プライバシー条件への同意など、エンド ユーザー向けの Azure AD B2B コラボレーションの招待の利用エクスペリエンスについて説明します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/04/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cd0febe5ffbc1b17718043d5fc97b804f87cc46
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199735"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory B2B コラボレーションの招待の利用

この記事では、ゲスト ユーザーがリソースにアクセスできる方法と実行する同意プロセスについて説明します。 招待メールをゲストに送信する場合、その招待には、ゲストがアプリまたはポータルにアクセスするために利用できるリンクを含めます。 招待メールは、ゲストがリソースにアクセスできる方法の 1 つにすぎません。 別の方法として、ゲストをディレクトリに追加し、共有したいポータルまたはアプリへの直接リンクを提供することができます。 使用する方法に関係なく、ゲストには初回の同意プロセスが示されます。 このプロセスにより、ゲストは確実にプライバシー条項に同意し、設定された[利用規約](../conditional-access/terms-of-use.md)を承諾することになります。

ゲスト ユーザーをディレクトリに追加すると、そのゲスト ユーザーのアカウントは同意状態 (PowerShell で表示可能) になります。これは、最初は **PendingAcceptance** に設定されます。 ゲストが招待を受け入れ、プライバシー ポリシーと利用規約に同意するまで、この設定は維持されます。 その後、同意の状態が **承認済み** に変わり、同意ページはゲストに表示されなくなります。

   > [!IMPORTANT]
   > - **2021 年 1 月 4 日以降**、Google は [WebView サインインのサポートを廃止](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html)します。 Gmail で Google フェデレーションまたはセルフサービス サインアップを使用している場合は、[基幹業務ネイティブ アプリケーションの互換性をテストする](google-federation.md#deprecation-of-webview-sign-in-support)必要があります。
   > - **2021 年 10 月以降**、Microsoft では、B2B コラボレーション シナリオ向けのアンマネージド Azure AD アカウントとテナントを作成することによる招待の利用をサポートしなくなります。 準備として、お客様は、[電子メール ワンタイム パスコード認証](one-time-passcode.md)をオプトインすることをお勧めします。 さらに多くの方法で共同作業を行うことができるように、このパブリック プレビュー機能についてフィードバックをお待ちしております。

## <a name="redemption-and-sign-in-through-a-common-endpoint"></a>共通のエンドポイントを使用した引き換えとサインイン

ゲスト ユーザーは、`https://myapps.microsoft.com` のような共通のエンドポイント (URL) を介して、マルチテナント アプリまたは Microsoft のファーストパーティ アプリにサインインできるようになりました。 これまで、ゲスト ユーザーは認証のために共通 URL によって、リソース テナントではなくホーム テナントにリダイレクトされていたため、テナント固有のリンク (例: `https://myapps.microsoft.com/?tenantid=<tenant id>`) が必要でした。 現在、ゲスト ユーザーはアプリケーションの共通 URL にアクセスして、 **[サインイン オプション]** を選択し、 **[Sign in to an organization]\(組織にサインイン\)** を選択できます。 次に、ユーザーは組織の名前を入力します。

![共通のエンドポイントへのサインイン](media/redemption-experience/common-endpoint-flow-small.png)

その後、ユーザーはテナント エンドポイントにリダイレクトされます。このエンドポイントでは、メール アドレスを使用してサインインするか、構成済みの ID プロバイダーを選択することができます。

## <a name="redemption-through-a-direct-link"></a>直接リンクによる利用

招待メールまたはアプリケーションの共通 URL の代わりに、アプリまたはポータルへの直接リンクをゲストに提供することができます。 まず、[Azure portal](./b2b-quickstart-add-guest-users-portal.md) または [PowerShell](./b2b-quickstart-invite-powershell.md) を介して、ゲスト ユーザーをディレクトリに追加する必要があります。 その後、直接サインオン リンクを含む、[ユーザーにアプリケーションをデプロイするためのカスタマイズ可能な方法](../manage-apps/end-user-experiences.md)のいずれかを使用できます。 ゲストには、招待メールではなく直接リンクを使用する際にも、初回の同意エクスペリエンスが示されます。

> [!NOTE]
> 直接リンクはテナント固有です。 つまり、共有アプリが配置されている、テナントでゲストを認証できるように、テナント ID または確認済みドメインが含まれています。 テナント コンテキストを含む直接リンクの例をいくつか以下に示します。
 > - アプリ アクセス パネル: `https://myapps.microsoft.com/?tenantid=<tenant id>`
 > - 確認済みドメインのアプリ アクセス パネル: `https://myapps.microsoft.com/<;verified domain>`
 > - Azure portal: `https://portal.azure.com/<tenant id>`
 > - 個々のアプリ: [直接サインオン リンク](../manage-apps/end-user-experiences.md#direct-sign-on-links)の使用方法を参照してください

直接リンク経由の招待メールが推奨されるケースがいくつかあります。 このような特殊なケースが組織にとって重要な場合は、引き続き招待メールを送信する方法を使用してユーザーを招待することをお勧めします。
 - ユーザーが Azure AD、MSA、あるいはフェデレーション組織のメール アカウントを持っていません。 ワンタイム パスコード機能を使用していない場合は、ゲストは、MSA の作成手順が示される招待メールを利用する必要があります。
 - 連絡先オブジェクト (Outlook の連絡先オブジェクトなど) と競合するため、招待されたユーザー オブジェクトにメール アドレスを持っていないことがあります。 この場合、ユーザーは招待メールの利用 URL をクリックする必要があります。
 - ユーザーは、招待されたメール アドレスの別名でサインインすることができます (エイリアスは、メール アカウントに関連付けられた追加のメール アドレスです)。この場合、ユーザーは招待メールの利用 URL をクリックする必要があります。

## <a name="redemption-through-the-invitation-email"></a>招待メールによる利用

[Azure portal](./b2b-quickstart-add-guest-users-portal.md) を使用してディレクトリにゲスト ユーザーを追加すると、そのプロセスで招待メールがゲストに送信されます。 ゲスト ユーザーをディレクトリに追加するために [PowerShell を使用する](./b2b-quickstart-invite-powershell.md)際に、招待メールを送信するように選択することもできます。 以下は、メールのリンクを利用する場合のゲストのエクスペリエンスの説明です。

1. ゲストは、**Microsoft Invitations** から送信される [招待メール](./invitation-email-elements.md)を受け取ります。
2. ゲストは、電子メールで **[招待の承諾]** を選択します。
3. ゲストは、自分の資格情報を使用してディレクトリにサインインします。 ゲストがディレクトリにフェデレーションできるアカウントを持っておらず、[電子メール ワンタイム パスコード (OTP)](./one-time-passcode.md) 機能が有効になっていない場合、ゲストは個人用 [MSA](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) または [Azure AD セルフサービス アカウント](../enterprise-users/directory-self-service-signup.md)を作成するように求められます。 詳細については、「[招待の引き換えフロー](#invitation-redemption-flow)」を参照してください。
4. ゲストには、以下に説明されている[同意エクスペリエンス](#consent-experience-for-the-guest)が示されます。
## <a name="invitation-redemption-flow"></a>招待の引き換えフロー

ユーザーが [招待メール](invitation-email-elements.md)の **[招待の承諾]** リンクをクリックすると、Azure AD では下の画像のように、引き換えフローに基づいて招待が自動的に引き換えられます。

![引き換えフローを図解したスクリーンショット](media/redemption-experience/invitation-redemption-flow.png)

"**ユーザーのユーザー プリンシパル名 (UPN) が既存の Azure AD と個人用 MSA アカウントの両方と一致する場合、ユーザーは引き換えるアカウントを選択するように求められます。* "

1. Azure AD ではユーザー基準の検出が実行され、[既存の Azure AD テナント](./what-is-b2b.md#easily-invite-guest-users-from-the-azure-ad-portal)にユーザーが存在するかどうかが判断されます。

2. 管理者が[直接フェデレーション](./direct-federation.md)を有効にしている場合、Azure AD では、構成されている SAML/WS-Fed ID プロバイダーのドメインにユーザーのドメイン サフィックスが一致するかどうかが確認され、事前に構成されている ID プロバイダーにユーザーがリダイレクトされます。

3. 管理者が [Google フェデレーション](./google-federation.md)を有効にしている場合、Azure AD では、ユーザーのドメイン サフィックスが gmail.com か googlemail.com であるかどうかが確認され、ユーザーが Google にリダイレクトされます。

4. 引き換えプロセスでは、Just-In-Time (JIT) 引き換えの場合、ユーザーに既存の個人用 [Microsoft アカウント (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) があるかどうかが確認されます (ただし、招待メール リンクの引き換えの場合は除外)。 ユーザーに既存の MSA がある場合は、既存の MSA でサインインします。

5. ユーザーの **ホーム ディレクトリ** が確認されると、ユーザーはサインインするため、それに対応する ID プロバイダーの元に送られます。  

6. 手順 1 から 4 によって招待したユーザーのホーム ディレクトリを見つけられない場合、Azure AD によって、招待元のテナントがゲスト用の[電子メール ワンタイム パスコード (OTP)](./one-time-passcode.md) を有効にしているかどうかが判断されます。

7. [ゲスト用の電子メール ワンタイム パスコードが有効になっている](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode)場合、招待メール経由でパスコードがユーザーに送信されます。 ユーザーはこのパスコードを取得し、Azure AD サインイン ページで入力します。

8. ゲスト用の電子メール ワンタイム パスコードが無効になっている場合、Azure AD によって、ドメイン サフィックスがチェックされ、コンシューマー アカウントに属しているかどうかが判断されます。 該当する場合、ユーザーは、個人用 [Microsoft アカウント](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)を作成するように求められます。 該当しない場合、[Azure AD セルフサービス アカウント](../enterprise-users/directory-self-service-signup.md)を作成するように求められます。

9. Azure AD では、メールへのアクセスを確認することで、[Azure AD セルフサービス アカウント](../enterprise-users/directory-self-service-signup.md)の作成が試行されます。 アカウントの確認は、コードをメールに送信し、ユーザーにそのコードを取得させ、Azure AD に送信させることで行われます。 ただし、招待されたユーザーのテナントがフェデレーションされているか、または招待されたユーザーのテナントで AllowEmailVerifiedUsers フィールドが false に設定されている場合、ユーザーは引き換えを完了できず、フローは結果的にエラーになります。 詳細については、「[Azure Active Directory B2B コラボレーションのトラブルシューティング](./troubleshoot.md#the-user-that-i-invited-is-receiving-an-error-during-redemption)」を参照してください。

10. ユーザーは、個人用 [Microsoft アカウント (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) を作成するように求められます。

11. 該当する ID プロバイダーに対して認証を行った後、[同意エクスペリエンス](#consent-experience-for-the-guest)を完了するため、ユーザーは Azure AD にリダイレクトされます。  

テナント化されたアプリケーションのリンク経由で引き換える Just-In-Time (JIT) 引き換えについては、手順 8 から 10 までを利用できません。 ユーザーが手順 6 に到達したとき、電子メール ワンタイム パスコード機能が有効になっていない場合、ユーザーはエラー メッセージを取得し、招待を引き換えることができません。 このエラーを防ぐには、管理者が[電子メール ワンタイム パスコードを有効にする](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode)か、ユーザーが招待リンクを確実にクリックするようにします。

## <a name="consent-experience-for-the-guest"></a>ゲストの同意エクスペリエンス

ゲストがサインインし、初めてパートナー組織のリソースにアクセスすると、以下のページが示されます。 

1. ゲストは、招待元の組織のプライバシーに関する声明について説明されている **[アクセス許可の確認]** ページを確認します。 ユーザーは、操作を続行するために、招待元の組織のプライバシー ポリシーに従って情報を使用することに **同意する** 必要があります。

   ![[アクセス許可の確認] ページのスクリーンショット](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > テナント管理者として組織のプライバシー ステートメントにリンクする方法については、[Azure Active Directory に組織のプライバシー情報を追加する方法](../fundamentals/active-directory-properties-area.md)に関するページを参照してください。

2. 利用規約が構成されている場合、ゲストはその利用規約を開き、確認してから、 **[同意]** を選択します。 

   ![新しい利用規約を示すスクリーンショット](media/redemption-experience/terms-of-use-accept.png) 

   **[外部 ID]**  >  **[使用条件]** で [使用条件](../conditional-access/terms-of-use.md)を構成できます。

3. 特に指定されていない限り、ゲストはアプリ アクセス パネルにリダイレクトされます。そこには、ゲストがアクセスできるアプリケーションがリスト表示されています。

   ![アプリ アクセス パネルを示すスクリーンショット](media/redemption-experience/myapps.png) 

ディレクトリでは、ゲストの **[招待が受け入れられました]** の値が **[はい]** に変わります。 MSA が作成された場合、ゲストの **[ソース]** には **Microsoft アカウント** が示されます。 ゲスト ユーザー アカウントのプロパティの詳細については、[Azure AD B2B コラボレーション ユーザーのプロパティ](user-properties.md)に関するページを参照してください。 

## <a name="next-steps"></a>次のステップ

- [Azure AD B2B コラボレーションとは](what-is-b2b.md)
- [Azure Portal で Azure Active Directory B2B コラボレーション ユーザーを追加する](add-users-administrator.md)
- [インフォメーション ワーカーが B2B コラボレーション ユーザーを Azure Active Directory に追加する方法](add-users-information-worker.md)
- [PowerShell を使用して Azure Active Directory B2B コラボレーション ユーザーを追加する](customize-invitation-api.md#powershell)
- [ゲスト ユーザーとして組織を脱退する](leave-the-organization.md)
