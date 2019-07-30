---
title: Azure Active Directory の条件付きアクセスを使用して認証セッション管理を構成する
description: ユーザー サインインの頻度やブラウザー セッション永続化などの、Azure AD 認証のセッション構成をカスタマイズします。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: e15cf9b2e10a581c72a5035b52be47c3e2c9dfda
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112339"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>条件付きアクセスを使用して認証セッション管理を構成する

複雑なデプロイでは、認証セッションの制限が必要になる場合があります。 次のようなシナリオがあります。

* 管理対象外のデバイスまたは共有デバイスからのリソース アクセス
* 外部ネットワークから機密情報へのアクセス
* 影響の大きなユーザー
* 重大なビジネス アプリケーション

条件付きアクセス制御を使用すると、すべてのユーザーに影響を与えることなく、組織内の特定のユース ケースを対象とするポリシーを作成できます。

ポリシーを構成する方法を詳しく説明する前に、既定の構成を調べてみましょう。

## <a name="user-sign-in-frequency"></a>ユーザー サインインの頻度

サインインの頻度は、ユーザーがリソースにアクセスしようとしたときにサインインし直すように求められるまでの期間を定義します。

ユーザー サインインの頻度に関する Azure Active Directory (Azure AD) の既定の構成は、90 日間のローリング ウィンドウです。 多くの場合、ユーザーに資格情報を要求することは賢明であるように思われますが、逆効果になることがあります。何も考えずに資格情報を入力するように教えられたユーザーは、意図せずに、資格情報を求める悪意のあるプロンプトに入力してしまう可能性があります。

90 日間ユーザーにサインインし直すように求めないことは不安に感じられるかもしれませんが、実際は IT ポリシーのどのような違反によってもセッションは取り消されます。 たとえば、パスワードの変更、非準拠のデバイス、アカウントの無効化などがあります (ただしこれらに限定されません)。 また、明示的に [PowerShell を使用してユーザーのセッションを取り消す](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0)ことができます。 Azure AD の既定の構成は、結局、「セッションのセキュリティ体制が変更していなければ、資格情報の提供をユーザーに求めない」というものになります。

サインイン頻度設定は、標準に従って OATH2 または OIDC プロトコルを実装したアプリで動作します。 Windows、Mac、およびモバイル用のほとんどの Microsoft ネイティブ アプリは、この設定に準拠します。

## <a name="persistence-of-browsing-sessions"></a>ブラウズ セッションの永続化

永続的なブラウザー セッションでは、ユーザーはブラウザー ウィンドウを閉じてから再度開いた後でもサインインした状態を維持できます。

ブラウザー セッション永続化に対し、Azure AD では既定で、認証が成功した後に「サインインの状態を維持しますか?」というプロンプトを表示して、個人用デバイスのユーザーがセッションを持続するかどうかを 選択できるようにしています。 「[AD FS シングル サイン オンの設定](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
)」の記事のガイダンスを使用して AD FS でブラウザー永続化が構成されている場合、そのポリシーに従って、Azure AD セッションも永続化します。 また、テナント内のユーザーに「サインインの状態を維持しますか?」というプロンプトを表示するかどうかを構成することもできます。 それには、[Azure AD サインイン ページのカスタマイズ](../fundamentals/customize-branding.md)に関する記事にあるガイダンスを使用して、Azure portal の企業ブランド ウィンドウで適切な設定を変更します。

## <a name="configuring-authentication-session-controls"></a>認証セッション コントロールの構成

条件付きアクセスは Azure AD Premium の機能であり、Premium ライセンスが必要です。 条件付きアクセスの詳細については、[Azure Active Directory の条件付きアクセスの概要](overview.md#license-requirements)に関するページを参照してください。

> [!WARNING]
> 現在、パブリック プレビューで[構成可能なトークン有効期間](../develop/active-directory-configurable-token-lifetimes.md)機能を使用している場合、同一のユーザーまたはアプリの組み合わせに対し、異なる 2 つのポリシー (1 つはこの機能で、もう 1 つは構成可能なトークン有効期間機能で使用) を作成することはサポートしていないことに注意してください。 マイクロソフトは、11 月 1 日に構成可能なトークン有効期間機能を廃止し、条件付きアクセス認証セッション管理機能に置き換える予定です。  

### <a name="policy-1-sign-in-frequency-control"></a>ポリシー 1:サインイン頻度コントロール

1. 新しいポリシーを作成します
1. ターゲット クラウド アプリなど、顧客の環境に必要なすべての条件を選択します。

   > [!NOTE]
   > 最高のユーザー エクスペリエンスが得られるように、Exchange Online や SharePoint Online などの主要な Microsoft Office アプリに、同じ認証プロンプト頻度を設定することをお勧めします。

1. **[アクセス制御]**  >  **[セッション]** に移動して、 **[サインインの頻度]** をクリックします
1. 最初のテキスト ボックスに必要な日数および時間数の値を入力します
1. ドロップダウンから **[時間]** または **[日]** の値を選択します
1. ポリシーを保存します

![サインイン頻度が構成された条件付きアクセス ポリシー](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

Azure AD 登録済み Windows デバイスでは、デバイスへのサインインはプロンプトと見なされます。 たとえば、Office アプリのサインイン頻度を 24 時間に構成している場合、Azure AD 登録済み Windows デバイスのユーザーは、デバイスにサインインすることによって、サインイン頻度ポリシーを満たし、Office アプリを開いたときに再度プロンプトされることはありません。

同じブラウザー セッションで実行している別の Web アプリに異なるサインイン頻度を構成している場合は、同じブラウザー セッションで実行しているすべてのアプリが単一のセッション トークンを共有するので、最も厳しいポリシーが両方のアプリに適用されます。

### <a name="policy-2-persistent-browser-session"></a>ポリシー 2:永続的ブラウザー セッション

1. 新しいポリシーを作成します
1. すべての必要な条件を選択します。

   > [!NOTE]
   > このコントロールは条件として [すべてのクラウド アプリ] を選択する必要があることに注意してください。 ブラウザー セッション永続化は認証セッション トークンによって制御されます。 ブラウザー セッションのすべてのタブは 1 つのセッション トークンを共有するため、それらすべては永続性の状態を共有する必要があります。

1. **[アクセス制御]**  >  **[セッション]** に移動し、 **[永続的ブラウザー セッション]** をクリックします
1. ドロップダウンから値を選択します
1. ポリシーを保存します

![永続的ブラウザーが構成された条件付きアクセス ポリシー](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Azure AD 条件付きアクセスの永続的ブラウザー セッション構成は、 両方のポリシーが構成されている場合、同じユーザーの Azure portal の企業ブランド ウィンドウにある「サインインの状態を維持しますか?」の設定を上書きします。

## <a name="validation"></a>検証

What-If ツールを使用して、ポリシーをどのように構成するかに基づき、ユーザーからターゲット アプリケーションへのログインおよび他の条件をシミュレートします。 認証セッションの管理コントロールが、ツールの結果に表示されます。

![条件付きアクセスの What If ツールの結果](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>ポリシーのデプロイ

ポリシーが期待どおりに機能することを確実にするために推奨されるベスト プラクティスは、運用環境にロールアウトする前にポリシーをテストすることです。 テスト テナントを使用して、新しいポリシーが意図したとおりに機能するかどうかを確認するのが理想的です。 詳細については、「[Azure Active Directory の条件付きアクセスのベスト プラクティス](best-practices.md)」の記事を参照してください。

## <a name="next-steps"></a>次の手順

* 条件付きアクセス ポリシーを構成する方法については、「[Azure Active Directory の条件付きアクセスを使用して特定のアプリケーションに対して MFA を必要にする](app-based-mfa.md)」の記事を参照してください。
* 環境のための条件付きアクセス ポリシーを構成する準備ができている場合は、「[Azure Active Directory の条件付きアクセスのベスト プラクティス](best-practices.md)」の記事を参照してください。
