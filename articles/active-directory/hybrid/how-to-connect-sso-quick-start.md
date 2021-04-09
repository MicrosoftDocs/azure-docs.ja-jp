---
title: Azure AD Connect:シームレス シングル サインオン - クイック スタート| Microsoft Docs
description: この記事では、Azure Active Directory シームレス シングル サインオンの開始方法について説明します
services: active-directory
keywords: Azure AD Connect とは, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e49ed356f294baca6e339faeebe92ca02b2723df
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644784"
---
# <a name="azure-active-directory-seamless-single-sign-on-quickstart"></a>Azure Active Directory シームレス シングル サインオン:クイック スタート

## <a name="deploy-seamless-single-sign-on"></a>シームレス シングル サインオンのデプロイ

Azure Active Directory (Azure AD) シームレス シングル サインオン (シームレス SSO) により、ユーザーは企業ネットワークにつながっている会社のデスクトップを使用するときに自動でサインインできます。 シームレス SSO により、オンプレミス コンポーネントの追加を必要とすることなく、ユーザーはクラウド ベースのアプリケーションに簡単にアクセスできるようになります。

シームレス SSO をデプロイするには、以下の手順に従います。

## <a name="step-1-check-the-prerequisites"></a>手順 1:前提条件を確認する

次の前提条件が満たされていることを確認します。

* **Azure AD Connect サーバーがセットアップされている**:サインイン方法として [パススルー認証](how-to-connect-pta.md)を使用する場合、他に確認すべき前提条件はありません。 サインイン方法として[パスワード ハッシュ同期](how-to-connect-password-hash-synchronization.md)を使用する場合や、Azure AD Connect と Azure AD の間にファイアウォールがある場合は、次の点を確認してください。
   - Azure AD Connect バージョン 1.1.644.0 以降を使用している。 
   - ファイアウォールまたはプロキシで許可している場合は、 **\*.msappproxy.net** の URL に対するポート 443 での許可リストへの接続を追加します。 プロキシ構成でワイルドカードではなく特定の URL が必要な場合は、**tenantid.registration.msappproxy.net** を構成できます。ここで、tenantid は、機能を構成しているテナントの GUID です。 組織で URL ベースのプロキシの例外が許可されない場合は、代わりに [Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)へのアクセスを許可できます。これは毎週更新されます。 この前提条件は、その機能を有効にした場合にのみ適用されます。 実際のユーザー サインインに必要な条件ではありません。

    >[!NOTE]
    >Azure AD Connect のバージョン 1.1.557.0、1.1.558.0、1.1.561.0、1.1.614.0 には、パスワード ハッシュ同期に関連する問題があります。 パスワード ハッシュ同期をパススルー認証と組み合わせて使用 _しない_ 場合の詳細については、[Azure AD Connect のリリース ノート](./reference-connect-version-history.md)をご覧ください。

* **サポートされている Azure AD Connect トポロジを使用する**:使用している Azure AD Connect トポロジが [こちら](plan-connect-topologies.md)で説明されているサポートされているトポロジの 1 つであることを確認してください。

    >[!NOTE]
    >シームレス SSO では、AD フォレスト間に AD 信頼があるかどうかを問わず、複数の AD フォレストがサポートされます。

* **ドメイン管理者の資格情報がセットアップされている**:次の各 Active Directory フォレストについて、ドメイン管理者の資格情報が必要です。
    * Azure AD Connect を使用して Azure AD に同期している。
    * シームレス SSO を有効にさせたいユーザーが含まれている。
    
* **先進認証を有効にする**:この機能を作動させるには、テナントで [先進認証](/office365/enterprise/modern-auth-for-office-2013-and-2016)を有効にする必要があります。

* **Microsoft 365 クライアントの最新バージョンを使用する**:Microsoft 365 クライアント (Outlook、Word、Excel など) でサイレント サインオンを利用するには、ユーザーは 16.0.8730.xxxx 以上のバージョンを使用している必要があります。

## <a name="step-2-enable-the-feature"></a>手順 2:機能を有効にする

[Azure AD Connect](whatis-hybrid-identity.md) を使用してシームレス SSO を有効にします。

>[!NOTE]
> Azure AD Connect が要件を満たしていない場合は、[PowerShell を使用してシームレス SSO を有効にする](tshoot-connect-sso.md#manual-reset-of-the-feature)こともできます。 Active Directory フォレストごとに複数のドメインがある場合に、シームレス SSO を有効にするドメインを絞り込むには、このオプションを使用します。

新しく Azure AD Connect をインストールする場合は、[カスタム インストール パス](how-to-connect-install-custom.md)を選択します。 **[ユーザー サインイン]** ページで、 **[シングル サインオンを有効にする]** チェックボックスをオンにします。

>[!NOTE]
> このオプションは、サインオンの方法が **パスワード ハッシュ同期** または **パススルー認証** の場合にのみ選択できます。

![Azure AD Connect:ユーザーのサインイン](./media/how-to-connect-sso-quick-start/sso8.png)

Azure AD Connect を既にインストールしている場合は、Azure AD Connect の **[ユーザー サインインの変更]** ページを選択して、 **[次へ]** を選択します。 Azure AD Connect バージョン 1.1.880.0 以降を使用している場合、既定で **[シングル サインオンを有効にする]** オプションが選択されています。 それ以前のバージョンの Azure AD Connect を使用している場合は、 **[シングル サインオンを有効にする]** オプションを選択します。

![Azure AD Connect:ユーザー サインインの変更](./media/how-to-connect-sso-quick-start/changeusersignin.png)

**[シングル サインオンを有効にする]** ページが表示されるまで、ウィザードの手順を続行します。 次の各 Active Directory フォレストのドメイン管理者の資格情報を入力します。

* Azure AD Connect を使用して Azure AD に同期している。
* シームレス SSO を有効にさせたいユーザーが含まれている。

ウィザードの完了後、シームレスな SSO がテナントで有効になります。

>[!NOTE]
> ドメイン管理者の資格情報は Azure AD Connect にも Azure AD にも保存されません。 機能を有効にするためにのみ使用されます。

この手順に従って、シームレス SSO の有効化を正しく行ったことを確認します。

1. テナントのグローバル管理者の資格情報を使用して、[Azure Active Directory 管理センター](https://aad.portal.azure.com)にサインインします。
2. 左ウィンドウで、 **[Azure Active Directory]** を選択します。
3. **[Azure AD Connect]** を選びます。
4. **[シームレスなシングル サインオン]** 機能が **[有効]** になっていることを確認します。

![Azure portal:[Azure AD Connect] ウィンドウ](./media/how-to-connect-sso-quick-start/sso10.png)

>[!IMPORTANT]
> シームレス SSO は、各 Active Directory (AD) フォレスト内のオンプレミスの AD に `AZUREADSSOACC` という名前のコンピューター アカウントを作成します。 `AZUREADSSOACC` コンピューター アカウントは、セキュリティ上の理由から強固に保護する必要があります。 ドメイン管理者だけがこのコンピューター アカウントを管理できるようにしてください。 コンピューター アカウント上で Kerberos 委任が無効になっていること、および Active Directory 内の他のどのアカウントにも、`AZUREADSSOACC` コンピューター アカウント上の委任のアクセス許可がないことを確認してください。 このコンピューター アカウントは、不注意で削除されるおそれがなく、ドメイン管理者のみがアクセスできる組織単位 (OU) に格納してください。

>[!NOTE]
> オンプレミス環境内で Pass-the-Hash および Credential Theft Mitigation アーキテクチャを使用している場合は、適切な変更を行って `AZUREADSSOACC` コンピューター アカウントが最終的に検疫コンテナー内にないことを確認します。 

## <a name="step-3-roll-out-the-feature"></a>手順 3:機能をロールアウトする

以下の指示に従い、シームレス SSO をユーザーに徐々にロールアウトできます。 まず、Active Directory のグループ ポリシーを利用し、すべてのユーザー、または選択したユーザーのイントラネット ゾーン設定に次の Azure AD URL を追加します。

- `https://autologon.microsoftazuread-sso.com`

また、グループ ポリシーを使用して、 **[スクリプトを介したステータス バーの更新を許可する]** というイントラネットのゾーン ポリシー設定を有効にする必要があります。 

>[!NOTE]
> 以下の手順は、Windows 上の Internet Explorer、Microsoft Edge、Google Chrome (信頼済みサイト URL のセットを Internet Explorer と共有する場合) でのみ機能します。 macOS 上の Mozilla Firefox と Google Chrome をセットアップする方法については、次のセクションをご覧ください。

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>ユーザーのイントラネット ゾーンの設定を変更する必要がある理由

既定では、ブラウザーは指定 URL から適切なゾーン (インターネットまたはイントラネット) を自動的に判断します。 たとえば、`http://contoso/` はイントラネット ゾーンにマップされ、`http://intranet.contoso.com/` はインターネット ゾーンにマップされます (URL にピリオドが含まれているため)。 Azure AD URL と同様に、クラウド エンドポイントの URL をブラウザーのイントラネット ゾーンに明示的に追加しなければ、ブラウザーは Kerberos チケットをクラウド エンドポイントに送信しません。

ユーザーのイントラネット ゾーン設定は 2 通りの方法で変更できます。

| オプション | 管理者の考慮事項 | ユーザー エクスペリエンス |
| --- | --- | --- |
| グループ ポリシー | 管理者はイントラネット ゾーン設定の編集を禁止します | ユーザーは自分の設定を変更できません |
| グループ ポリシーの基本設定 |  管理者はイントラネット ゾーン設定の編集を許可します | ユーザーは自分の設定を変更できます |

### <a name="group-policy-option---detailed-steps"></a>"グループ ポリシー" オプション - 詳しい手順

1. グループ ポリシー管理エディター ツールを開きます。
2. 一部またはすべてのユーザーに適用されるグループ ポリシーを編集します。 この例では、**既定のドメイン ポリシー** を使用します。
3. **[ユーザーの構成]** 、 **[ポリシー]** 、 **[管理用テンプレート]** 、 **[Windows コンポーネント]** 、 **[Internet Explorer]** 、 **[インターネット コントロール パネル]** 、 **[セキュリティ ページ]** の順に移動します。 次に **[サイトとゾーンの割り当て一覧]** を選択します。
    ![[サイトとゾーンの割り当て一覧] が選択された [セキュリティ] ページを示すスクリーンショット。](./media/how-to-connect-sso-quick-start/sso6.png)
4. ポリシーを有効にしてから、ダイアログ ボックスに次の値を入力します。
   - **[値の名前]** :Kerberos チケットの転送先となる Azure AD URL。
   - **[値]** (データ):**1** (イントラネット ゾーンを示す)。

     結果は次のようになります。

     値の名前: `https://autologon.microsoftazuread-sso.com`
  
     値 (データ):1

   >[!NOTE]
   > 一部のユーザーに対してシームレス SSO の使用を許可したくない場合 ( ユーザー共有キオスクでサインインする場合など) は、前述の値を **4** に設定します。 この操作により、Azure AD の URL が [制限付きゾーン] に追加され、シームレス SSO は常に失敗するようになります。
   >

5. **[OK]** を選択してから、もう一度 **[OK]** を選択します。

    ![ゾーン割り当てが選択された [コンテンツの表示] ウィンドウを示すスクリーンショット。](./media/how-to-connect-sso-quick-start/sso7.png)

6. **[ユーザーの構成]** 、 **[ポリシー]** 、 **[管理用テンプレート]** 、 **[Windows コンポーネント]** 、 **[Internet Explorer]** 、 **[インターネット コントロール パネル]** 、 **[セキュリティ ページ]** 、 **[イントラネット ゾーン]** の順に移動します。 次に、 **[スクリプトを介したステータス バーの更新を許可する]** を選択します。

    ![[スクリプトを介したステータス バーの更新を許可する] が選択された [イントラネット ゾーン] ページを示すスクリーンショット。](./media/how-to-connect-sso-quick-start/sso11.png)

7. ポリシー設定を有効にしてから、 **[OK]** を選択します。

    ![ポリシー設定が有効化された [スクリプトを介したステータス バーの更新を許可する] ウィンドウを示すスクリーンショット。](./media/how-to-connect-sso-quick-start/sso12.png)

### <a name="group-policy-preference-option---detailed-steps"></a>"グループ ポリシーの基本設定" オプション - 詳しい手順

1. グループ ポリシー管理エディター ツールを開きます。
2. 一部またはすべてのユーザーに適用されるグループ ポリシーを編集します。 この例では、**既定のドメイン ポリシー** を使用します。
3. **[ユーザーの構成]**  >  **[基本設定]**  >  **[Windows 設定]**  >  **[レジストリ]**  >  **[新規]**  >  **[レジストリ項目]** の順に移動します。

    ![[レジストリ] および [レジストリ項目] が選択されていることを示すスクリーンショット。](./media/how-to-connect-sso-quick-start/sso15.png)

4. 次の値を該当するフィールドに入力し、 **[OK]** をクリックします。
   - **キー パス**: **_Software\Microsoft\Windows\CurrentVersion\Internet Settings\ZoneMap\Domains\microsoftazuread-sso.com\autologon_**
   - **値の名前**: **_https_**
   - **値の型**: **_REG_DWORD_**
   - **値のデータ**: **_00000001_**
 
     ![[新しいレジストリのプロパティ] ウィンドウを示すスクリーンショット。](./media/how-to-connect-sso-quick-start/sso16.png)
 
     ![シングル サインオン](./media/how-to-connect-sso-quick-start/sso17.png)

### <a name="browser-considerations"></a>ブラウザーの考慮事項

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (すべてのプラットフォーム)

Mozilla Firefox は、Kerberos 認証を自動的には使用しません。 各ユーザーが、次の手順に従って、Firefox の設定に Azure AD の URL を手動で追加する必要があります。
1. Firefox を実行し、アドレス バーに「`about:config`」と入力します。 表示されているすべての通知を無視します。
2. **network.negotiate-auth.trusted-uris** の設定を検索します。 この設定は、Kerberos 認証用の Firefox の信頼済みサイトを一覧表示します。
3. 右クリックして **[変更]** を選択します。
4. フィールドに「 `https://autologon.microsoftazuread-sso.com` 」を入力します。
5. **[OK]** を選択してから、ブラウザーをもう一度開きます。

#### <a name="safari-macos"></a>Safari (macOS)

macOS を実行しているコンピューターが AD に参加していることを確認します。 macOS デバイスを AD に参加させる手順については、この記事の対象外です。

#### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Chromium に基づく Microsoft Edge (すべてのプラットフォーム)

お使いの環境の [AuthNegotiateDelegateAllowlist](/DeployEdge/microsoft-edge-policies#authnegotiatedelegateallowlist) ポリシー設定または [AuthServerAllowlist](/DeployEdge/microsoft-edge-policies#authserverallowlist) ポリシー設定をオーバーライドした場合は、それらの設定に Azure AD の URL (`https://autologon.microsoftazuread-sso.com`) を確実に追加します。

#### <a name="microsoft-edge-based-on-chromium-macos-and-other-non-windows-platforms"></a>Chromium に基づく Microsoft Edge (macOS および他の非 Windows プラットフォーム)

macOS および他の Windows 以外のプラットフォームの Chromium に基づく Microsoft Edge の場合、統合認証用の Azure AD の URL を許可一覧に追加する方法については、[Chromium に基づく Microsoft Edge のポリシー一覧](/DeployEdge/microsoft-edge-policies#authserverallowlist)を参照してください。

#### <a name="google-chrome-all-platforms"></a>Google Chrome (すべてのプラットフォーム)

お使いの環境の [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) ポリシー設定または [AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) ポリシー設定をオーバーライドした場合は、それらの設定に Azure AD の URL (`https://autologon.microsoftazuread-sso.com`) を必ず追加してください。

#### <a name="google-chrome-macos-and-other-non-windows-platforms"></a>Google Chrome (macOS と Windows 以外のその他のプラットフォーム)

macOS などの Windows 以外のプラットフォームで Google Chrome を使用し、統合認証用の Azure AD の URL の許可一覧を制御する方法については、[Chromium プロジェクト ポリシー リスト](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist)に関する記事をご覧ください。

サード パーティの Active Directory グループ ポリシーの拡張機能を使用して、Mac 上で Firefox および Google Chrome を使用するユーザーに Azure AD の URL を ロールアウトする場合については、この記事では扱われません。

#### <a name="known-browser-limitations"></a>ブラウザーの既知の制限事項

シームレス SSO は、Firefox および Microsoft Edge (レガシー) ブラウザーのプライベート ブラウズ モードでは動作しません。 拡張保護モードで実行されている場合は、Internet Explorer ブラウザーでも機能しません。 シームレス SSO では、Chromium に基づく Microsoft Edge の次期バージョンがサポートされています。仕様により InPrivate とゲスト モードで機能します。

## <a name="step-4-test-the-feature"></a>手順 4:機能をテストする

特定のユーザーについてこの機能をテストするには、次の条件がすべて満たされていることを確認してください。
  - ユーザーが会社のデバイスでサインインしている。
  - デバイスが Active Directory ドメインに参加している。 デバイスは、[Azure AD 参加済み](../devices/overview.md)である必要は _ありません_。
  - デバイスが、企業のワイヤードまたはワイヤレス ネットワーク上や、VPN 接続などのリモート アクセス接続を介してドメイン コントローラー (DC) に直接接続している。
  - グループ ポリシーを使用して、このユーザーに[機能がロールアウト](#step-3-roll-out-the-feature)されている。

ユーザーがユーザー名のみを入力し、パスワードを入力しないシナリオをテストする場合:
   - https://myapps.microsoft.com/ にサインインします。 必ずブラウザーのキャッシュをクリアするか、サポートされているいずれかのブラウザーのプライベート モードでの新しいプライベート ブラウザー セッションを使用してください。

ユーザーがユーザー名またはパスワードを入力する必要がないシナリオをテストする場合は、次の手順のいずれかに従います。 
   - `https://myapps.microsoft.com/contoso.onmicrosoft.com` にサインインします。必ず、ブラウザーのキャッシュをクリアするか、サポートされているいずれかのブラウザーのプライベート モードで新しいプライベート ブラウザー セッションを使用してください。 *contoso* を自分のテナント名に置き換えます。
   - 新しいプライベート ブラウザー セッションで `https://myapps.microsoft.com/contoso.com` にサインインします。 *contoso.com* を、自分のテナントで検証されたドメイン (フェデレーション ドメインではなく) に置き換えます。

## <a name="step-5-roll-over-keys"></a>手順 5:キーをロール オーバーする

手順 2 では、Azure AD Connect によって、シームレス SSO を有効にしたすべての Active Directory フォレスト内でコンピューター アカウント (Azure AD を表します) が作成されます。 詳細については、「[Azure Active Directory シームレス シングル サインオン: 技術的な詳細情報](how-to-connect-sso-how-it-works.md)」をご覧ください。

>[!IMPORTANT]
>コンピューター アカウントの Kerberos 解読キーが流出した場合、それを利用し、その AD フォレストのあらゆるユーザーに対して Kerberos チケットが生成されます。 悪意のあるアクターは、Azure AD サインインを偽装し、ユーザーを危険にさらすことがあります。 定期的に (少なくとも 30 日ごとに) Kerberos の解読キーをロールオーバーすることを強くお勧めします。

キーをロールオーバーする方法の詳細については、「[Azure Active Directory シームレス シングル サインオン: よく寄せられる質問](how-to-connect-sso-faq.md)」をご覧ください。 Microsoft はキーの自動ロールオーバーを導入する機能の開発に取り組んでいます。

>[!IMPORTANT]
>この機能を有効にした後に、"_直ちに_" この手順を実行する必要はありません。 少なくとも 30 日に 1 回は、Kerberos 暗号化の解除キーをロールオーバーしてください。

## <a name="next-steps"></a>次のステップ

- [技術的な詳細](how-to-connect-sso-how-it-works.md): シームレス シングル サインオン機能のしくみを理解します。
- [よく寄せられる質問](how-to-connect-sso-faq.md): シームレス シングル サインオンに関してよく寄せられる質問への回答を示します。
- [トラブルシューティング](tshoot-connect-sso.md): シームレス シングル サインオン機能に関する一般的な問題の解決方法を説明します。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Azure Active Directory フォーラムを使用して、新しい機能の要求を行います。
