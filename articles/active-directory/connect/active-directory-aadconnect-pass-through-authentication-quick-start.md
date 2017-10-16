---
title: "Azure AD パススルー認証 - クイック スタート | Microsoft Docs"
description: "この記事では、Azure Active Directory (Azure AD) パススルー認証を使用する方法について説明します。"
services: active-directory
keywords: "Azure AD Connect パススルー認証, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: b0327b37b56987d52557f3f4e305f27eb2e5296d
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Azure Active Directory パススルー認証: クイック スタート

## <a name="how-to-deploy-azure-ad-pass-through-authentication"></a>Azure AD パススルー認証をデプロイする方法

Azure Active Directory (Azure AD) パススルー認証を使用すると、ユーザーは同じパスワードを使用して、オンプレミスのアプリケーションとクラウド ベースのアプリケーションの両方にサインインできます。 この認証では、オンプレミスの Active Directory に対してパスワードを直接検証することで、ユーザーをサインインします。

>[!IMPORTANT]
>プレビューでこの機能をご使用の場合は、[ここ](./active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md)に記載されている手順に従って、プレビュー バージョンの認証エージェントを必ずアップグレードする必要があります。

パススルー認証をデプロイするには、次の手順を実行する必要があります。

## <a name="step-1-check-prerequisites"></a>手順 1: 前提条件を確認する

次の前提条件が満たされていることを確認します。

### <a name="on-the-azure-active-directory-admin-center"></a>Azure Active Directory 管理センター

1. Azure AD テナントで、クラウド専用のグローバル Administrator アカウントを作成します。 その方法を採用すると、オンプレミス サービスが利用できなくなったとき、テナントの構成を管理できます。 クラウド専用のグローバル管理者アカウントを追加する手順については、[こちら](../active-directory-users-create-azure-portal.md)をご覧ください。 テナントからロックアウトされないようにするには、この手順を必ず実行する必要があります。
2. 1 つ以上の[カスタム ドメイン名](../active-directory-add-domain.md)を Azure AD テナントに追加します。 ユーザーは、このドメイン名のいずれかを使用してにサインインします。

### <a name="in-your-on-premises-environment"></a>オンプレミスの環境の場合

1. Windows Server 2012 R2 以降が実行されているサーバーを特定します。このサーバーでは Azure AD Connect を実行します。 このサーバーを、パスワードの検証が必要なユーザーと同じ AD フォレストに追加します。
2. [最新バージョンの Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) を、前の手順で特定したサーバーにインストールします。 Azure AD Connect が実行されている場合は、バージョンが 1.1.557.0 以降であることを確認します。
3. Windows Server 2012 R2 以降が実行されている追加のサーバーを特定します。このサーバーでは、スタンドアロンの認証エージェントを実行します。 認証エージェントのバージョンは 1.5.193.0 以降である必要があります。 このサーバーは、サインイン要求の高可用性を確保するために必要です。 このサーバーを、パスワードの検証が必要なユーザーと同じ AD フォレストに追加します。
4. サーバーと Azure AD の間にファイアウォールがある場合は、次の項目を構成する必要があります。
   - 認証エージェントが次のポートを使用して Azure AD に**送信**リクエストを送れるようにします。
   
   | ポート番号 | 用途 |
   | --- | --- |
   | **80** | SSL 証明書を検証する際に証明書失効リスト (CRL) をダウンロードする |
   | **443** | Microsoft のサービスを使用したすべての送信方向の通信 |
   
   ご利用のファイアウォールが送信元ユーザーに応じて規則を適用している場合は、Network Service として実行されている Windows サービスを送信元とするトラフィックに対してこれらのポートを開放します。
   - ファイアウォールまたはプロキシが DNS ホワイトリストを許可している場合は、**\*.msappproxy.net** と **\*.servicebus.windows.net** への接続をホワイトリストに登録できます。 そうでない場合は、毎週更新される [Azure DataCenter IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)へのアクセスを許可します。
   - 認証エージェントは初回の登録のために **login.windows.net** と **login.microsoftonline.com** にアクセスする必要があります。そこで、これらの URL にもファイアウォールを開きます。
   - 証明書の検証のために、URL **mscrl.microsoft.com:80**、**crl.microsoft.com:80**、**ocsp.msocsp.com:80**、**www.microsoft.com:80** のブロックを解除します。 他の Microsoft 製品でもこれらの URL を証明書の検証に使用しているので、URL のブロックを既に解除している可能性もあります。

## <a name="step-2-enable-exchange-activesync-support-optional"></a>手順 2: Exchange ActiveSync のサポートを有効にする (省略可能)

次の手順に従って Exchange ActiveSync のサポートを有効にします。

1. [Exchange PowerShell](https://technet.microsoft.com/library/mt587043(v=exchg.150).aspx) を使用して次のコマンドを実行します。
```
Get-OrganizationConfig | fl per*
```

2. `PerTenantSwitchToESTSEnabled` 設定の値を確認します。 値が **true** の場合は、テナントが正しく構成されています。ほとんどのお客様の環境で、通常は true になっています。 値が **false** の場合は、次のコマンドを実行します。
```
Set-OrganizationConfig -PerTenantSwitchToESTSEnabled:$true
```

3. `PerTenantSwitchToESTSEnabled` 設定の値が **true** に設定されていることを確認します。 次の手順に進む前に、1 時間ほど待機します。

この手順で問題が発生した場合は、[トラブルシューティング ガイド](active-directory-aadconnect-troubleshoot-pass-through-authentication.md#exchange-activesync-configuration-issues)で詳細を確認してください。

## <a name="step-3-enable-the-feature"></a>手順 3: 機能を有効にする

Azure AD パススルー認証は、[Azure AD Connect](active-directory-aadconnect.md) を使用して有効にできます。

>[!IMPORTANT]
>Azure AD Connect のプライマリ サーバーまたはステージング サーバーで Azure AD パススルー認証を有効にできますが、 プライマリ サーバーから有効することを強くお勧めします。

Azure AD Connect を初めてインストールする場合は、[カスタム インストール パス](active-directory-aadconnect-get-started-custom.md)を選択します。 **[ユーザー サインイン]** ページで、サインオン方式として **[パススルー認証]** を選択します。 正常に完了すると、Azure AD Connect と同じサーバーにパススルー認証エージェントがインストールされます。 また、テナントでパススルー認証機能が有効になります。

![Azure AD Connect - [ユーザー サインイン]](./media/active-directory-aadconnect-sso/sso3.png)

([高速インストール](active-directory-aadconnect-get-started-express.md) パスまたは[カスタム インストール](active-directory-aadconnect-get-started-custom.md) パスを使用して) Azure AD Connect を既にインストールした場合は、Azure AD Connect で **[ユーザー サインインの変更]** を選択し、**[次へ]** をクリックします。 次に、サインオン方式として **[パススルー認証]** を選択します。 正常に完了すると、Azure AD Connect と同じサーバーにパススルー認証エージェントがインストールされ、テナントで機能が有効になります。

![Azure AD Connect - [ユーザー サインインの変更]](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>パススルー認証はテナント レベルの機能です。 有効にすると、テナントに含まれる "_すべての_" 管理対象ドメインのユーザー サインインに影響を及ぼします。 AD FS からパススルー認証に切り替える場合は、12 時間以上経ってから AD FS インフラストラクチャをシャットダウンすることをお勧めします。これは、移行中もユーザーが Exchange ActiveSync にサインインできるようにするための措置です。

## <a name="step-4-test-the-feature"></a>手順 4: 機能をテストする

この手順に従って、パススルー認証の有効化を正しく行ったことを確認します。

1. テナントのグローバル管理者の資格情報を使って、[Azure Active Directory 管理センター](https://aad.portal.azure.com)にサインインします。
2. 左側のナビゲーションで、**[Azure Active Directory]** を選びます。
3. **[Azure AD Connect]** を選びます。
4. **[パススルー認証]** 機能が **[有効]** と表示されていることを確認します。
5. **[パススルー認証]** を選択します。 このブレードには、認証エージェントがインストールされているサーバーが一覧表示されます。

![Azure Active Directory 管理センター - [Azure AD Connect] ブレード](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Azure Active Directory 管理センター - [パススルー認証] ブレード](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

この段階で、テナントに含まれるすべての管理対象ドメインのユーザーが、パススルー認証を使用してサインインできます。 ただし、フェデレーション ドメインのユーザーは引き続き、Active Directory フェデレーション サービス (AD FS) または既に構成済みのその他のフェデレーション プロバイダーを使用してサインインします。 ドメインをフェデレーションから管理対象に変換すると、そのドメインのすべてのユーザーが、パススルー認証を使用したサインインを自動的に開始します。 クラウド専用ユーザーはパススルー認証機能の影響を受けません。

## <a name="step-5-ensure-high-availability"></a>手順 5: 高可用性を確保する

運用環境にパススルー認証をデプロイする場合は、スタンドアロン認証エージェントをインストールする必要があります。 この 2 番目の認証エージェントを、Azure AD Connect が実行されている、最初の認証エージェント "_以外_" のサーバーにインストールします。 この設定により、サインイン要求の高可用性が確保されます。 次の手順に従って、スタンドアロン認証エージェントをデプロイします。

1. **最新バージョン (1.5.193.0 以降) の認証エージェントをダウンロードする**: テナントのグローバル管理者の資格情報で [Azure Active Directory 管理センター](https://aad.portal.azure.com)にサインインします。
2. 左側のナビゲーションで、**[Azure Active Directory]** を選びます。
3. **[Azure AD Connect]**、**[パススルー認証]** の順に選択します。 **[エージェントのダウンロード]** を選択します。
4. **[Accept terms & download]\(使用条件に同意してダウンロードする\)** をクリックします。
5. **最新バージョンの認証エージェントをインストールする**: 前の手順でダウンロードした実行可能ファイルを実行します。 求められたら、テナントのグローバル管理者の資格情報を入力します。

![Azure Active Directory 管理センター - 認証エージェントのダウンロード ボタン](./media/active-directory-aadconnect-pass-through-authentication/pta9.png)

![Azure Active Directory 管理センター - [エージェントのダウンロード] ブレード](./media/active-directory-aadconnect-pass-through-authentication/pta10.png)

>[!NOTE]
>認証エージェントは[ここ](https://aka.ms/getauthagent)からダウンロードすることもできます。 認証エージェントをインストールする "_前_" に[サービス使用条件](https://aka.ms/authagenteula)を確認して同意してください。

## <a name="next-steps"></a>次のステップ
- [**現在の制限**](active-directory-aadconnect-pass-through-authentication-current-limitations.md) - サポートされているシナリオと、サポートされていないシナリオを確認します。
- [**技術的な詳細**](active-directory-aadconnect-pass-through-authentication-how-it-works.md) - この機能のしくみを確認します。
- [**よく寄せられる質問**](active-directory-aadconnect-pass-through-authentication-faq.md) - よく寄せられる質問と回答です。
- [**トラブルシューティング**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) - 機能に関する一般的な問題を解決する方法を確認します。
- [**Azure AD シームレス SSO**](active-directory-aadconnect-sso.md) - この補完的な機能の詳細を確認します。
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 新しい機能の要求を提出します。

