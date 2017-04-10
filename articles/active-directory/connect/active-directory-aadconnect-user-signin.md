---
title: "Azure AD Connect: ユーザー サインイン | Microsoft Docs"
description: "Azure AD Connect ユーザー サインインのカスタム設定。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: 547b118e-7282-4c7f-be87-c035561001df
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 1673e14285456307441c836ba2225556416d4a7b
ms.openlocfilehash: 4c50968dd66ab351a5ef1b8e06557855870c5d4e
ms.lasthandoff: 03/02/2017


---
# <a name="azure-ad-connect-user-sign-in-options"></a>Azure AD Connect ユーザーのサインイン オプション
Azure Active Directory (Azure AD) Connect では、ユーザーは同じパスワードを使用して、クラウドとオンプレミス両方のリソースにサインインできます。 この記事では、Azure AD へのサインインに使用する ID を選択できるようにするために、各 ID モデルの主要な概念について説明します。

既に Azure AD の ID モデルについての知識があり、特定の方法の詳細を知りたい場合は、適切なリンクを参照してください。

* [シングル サインオン (SSO)](active-directory-aadconnect-sso.md) による[パスワード同期](#password-synchronization)
* [パススルー認証](active-directory-aadconnect-pass-through-authentication.md)
* [フェデレーション SSO (Active Directory フェデレーション サービス (AD FS) を使用する)](#federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2)

## <a name="choosing-the-user-sign-in-method-for-your-organization"></a>組織のユーザー サインイン方法の選択
多くの組織においては Office 365、SaaS アプリケーション、およびその他の Azure AD ベースのリソースへのユーザー サインインを可能にするのみなので、その場合は既定のパスワードの同期オプションをお勧めします。 ただし、組織によっては、特定の理由によってこのオプションを使用できない場合があります。 その場合は、フェデレーション サインイン オプション (AD FS など) か、パススルー認証を選択できます。 次の表を使用して、適切な選択を行ってください。

必要事項 | PS と SSO| PA と SSO| AD FS |
 --- | --- | --- | --- |
オンプレミスの Active Directory にある新しいユーザー、連絡先、およびグループ アカウントを、自動的にクラウドに同期する。|○|○|○|
テナントを Office 365 ハイブリッド シナリオ用にセット アップする。|○|○|○|
ユーザーがオンプレミスのパスワードを使用してサインインし、クラウド サービスにアクセスできるようにする。|○|○|○|
会社の資格情報を使用してシングル サインオンを実装する。|○|○|○|
クラウドにパスワードが保存されていないことを確認する。||○*|○|
オンプレミスの Multi-factor Authentication ソリューションを有効化する。|||○|

* 軽量コネクタ経由。

>[!NOTE]
> パススルー認証では、現在、リッチ クライアントにいくつかの制限があります。 詳細については「[パススルー認証](active-directory-aadconnect-pass-through-authentication.md)」を参照してください。 

### <a name="password-synchronization"></a>パスワードの同期
パスワードの同期では、ユーザーのパスワードのハッシュが、オンプレミスの Active Directory から Azure AD に同期されます。 オンプレミスでパスワードが変更またはリセットされると、新しいパスワードは Azure AD にすぐに同期されるため、ユーザーは常に、クラウドのリソースとオンプレミスのリソースに同じパスワードを使用できます。 パスワードがクリア テキストの状態で Azure AD に送信されたり Azure AD に格納されたりすることはありません。 パスワードの同期とパスワードの書き戻しを組み合わせて使用すると、Azure AD でセルフ サービス パスワードのリセットを有効にすることができます。

さらに、企業ネットワーク上にあるドメイン参加済みコンピューターのユーザーに対しても、[SSO](active-directory-aadconnect-sso.md) を有効化できます。 シングル サインオンを使用すれば、有効になっているユーザーはユーザー名のみを入力して、クラウド リソースに安全にアクセスできます。

![パスワードの同期](./media/active-directory-aadconnect-user-signin/passwordhash.png)

詳しくは、[パスワード同期](active-directory-aadconnectsync-implement-password-synchronization.md)に関する記事をご覧ください。

### <a name="pass-through-authentication"></a>パススルー認証
パススルー認証では、ユーザーのパスワードはオンプレミスの Active Directory コントローラーに対して検証されます。 パスワードを何らかの形式で Azure AD に保存する必要はありません。 そのため、クラウド サービスへの認証時に、オンプレミスのポリシー (ログオン時間制限など) を評価することができます。

パススルー認証では、オンプレミス環境内の Windows Server 2012 R2 ドメイン参加済みコンピューター上にある、シンプルなエージェントが使用されます。 このエージェントが、パスワードの検証要求をリッスンします。 インターネットに対して受信ポートを開放する必要は一切ありません。

さらに、企業ネットワーク上にあるドメイン参加済みコンピューターのユーザーに対しても、シングル サインオンを有効化できます。 シングル サインオンを使用すれば、有効になっているユーザーはユーザー名のみを入力して、クラウド リソースに安全にアクセスできます。
![パススルー認証](./media/active-directory-aadconnect-user-signin/pta.png)

詳細については、次を参照してください。
- [パススルー認証](active-directory-aadconnect-pass-through-authentication.md)
- [シングル サインオン](active-directory-aadconnect-sso.md)

### <a name="federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2"></a>Windows Server 2012 R2 の AD FS を使用した新規または既存のファームを使用するフェデレーション
フェデレーション サインインでは、ユーザーはオンプレミスのパスワードを使用して Azure AD ベースのサービスにサインインできます。 企業ネットワーク上にいる時には、パスワードを入力する必要すらありません。 AD FS によるフェデレーション オプションを使用すれば、Windows Server 2012 R2 の AD FS を使用した新規または既存のファームをデプロイできます。 既存のファームを指定する場合は、Azure AD Connect によってファームと Azure AD との間に信頼が構成され、それにより、ユーザーがサインインできるようになります。

<center>![Windows Server 2012 R2 の AD FS を使用したフェデレーション](./media/active-directory-aadconnect-user-signin/federatedsignin.png)</center>

#### <a name="deploy-federation-with-ad-fs-in-windows-server-2012-r2"></a>Windows Server 2012 R2 の AD FS を使用してフェデレーションをデプロイする

新しいファームをデプロイする場合は、次のものが必要です。

* フェデレーション サーバー用の Windows Server 2012 R2 サーバー。
* Web アプリケーション プロキシ用の Windows Server 2012 R2 サーバー。
* 目的のフェデレーション サービス名に対する SSL 証明書を&1; つ含んだ .pfx ファイル。 サービス名の例: www.contoso.com。

新しいファームをデプロイするか、既存のファームを使用する場合は、次のものが必要です。

* フェデレーション サーバー上のローカル管理者の資格情報。
* Web アプリケーション プロキシ ロールをデプロイするワークグループ サーバー (ドメイン不参加) 上の、ローカル管理者の資格情報。
* ウィザードを実行するコンピューター。これにより、Windows リモート管理を使用して、AD FS または Web アプリケーション プロキシをインストールする他のコンピューターに接続できるようになります。

詳しくは、「[AD FS を使用した SSO の構成](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)」をご覧ください。

#### <a name="sign-in-by-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>以前のバージョンの AD FS またはサード パーティのソリューションを使用してサインインする
以前のバージョンの AD FS (AD FS 2.0 など) またはサード パーティのフェデレーション プロバイダーを使用して、クラウド サインインを既に構成している場合は、Azure AD Connect を通じてユーザー サインインの構成をスキップできます。 これにより、既存のソリューションをサインインに使用しながら、最新の同期内容と Azure AD Connect のその他の機能を使用できるようになります。

詳しくは、「[Azure AD のサードパーティ フェデレーション互換性リスト](active-directory-aadconnect-federation-compatibility.md)」をご覧ください。


## <a name="user-sign-in-and-user-principal-name"></a>ユーザーのサインインとユーザー プリンシパル名
### <a name="understanding-user-principal-name"></a>ユーザー プリンシパル名について
Active Directory では、既定のユーザー プリンシパル名 (UPN) サフィックスは、ユーザー アカウントが作成されたドメインの DNS 名です。 ほとんどの場合、これはインターネット上で企業ドメインとして登録されたドメイン名です。 ただし、Active Directory ドメインと信頼関係を使用して、複数の UPN サフィックスを追加することもできます。

ユーザーの UPN は、username@domain という形式で表されます。 たとえば、"contoso.com" という名前の Active Directory ドメインの場合、John という名前のユーザーの UPN は "john@contoso.com" になります。 ユーザーの UPN は RFC 822 に基づいています。 UPN と電子メールは形式が同じですが、ユーザーの UPN の値は、ユーザーの電子メール アドレスと必ずしも同じものになるとは限りません。

### <a name="user-principal-name-in-azure-ad"></a>Azure AD のユーザー プリンシパル名
Azure AD Connect ウィザードでは、userPrincipalName 属性を使用することもできますが、Azure AD のユーザー プリンシパル名として使用される属性をオンプレミスから指定することもできます (後者はカスタム インストールの場合です)。 この値が、Azure AD へのサインインに使用されます。 ユーザー プリンシパル名の属性値が Azure AD の確認済みドメインに対応しない場合は、値が既定値 (.onmicrosoft.com) に置き換えられます。

Azure Active Directory のすべてのディレクトリには、contoso.onmicrosoft.com という形式のドメイン名があらかじめ設定されており、これによってユーザーは、Azure をはじめとする Microsoft の各種サービスを利用できるようになっています。 カスタム ドメインを使用すれば、サインイン エクスペリエンスを改善したり、簡素化することもできます。 Azure AD のカスタム ドメイン名とドメインの確認方法については、「[Azure Active Directory へのカスタム ドメイン名の追加](../active-directory-add-domain.md#add-a-custom-domain-name-to-your-directory)」をご覧ください。

## <a name="azure-ad-sign-in-configuration"></a>Azure AD サインインの構成
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Azure AD Connect による Azure AD サインインの構成
Azure AD のサインイン エクスペリエンスは、同期するユーザーのユーザー プリンパル名のサフィックスが、Azure AD ディレクトリで確認済みのカスタム ドメインのいずれかと一致するかどうかによって変わります。 Azure AD Connect を使用すれば、Azure AD のサインイン設定を効率的に行って、クラウドでのユーザー サインイン エクスペリエンスをオンプレミスでのエクスペリエンスと同様のものにすることができます。

Azure AD Connect は、ドメインに対して定義されているUPN サフィックスをリストし、それらを Azure AD 内のカスタム ドメインと照合しようとします。 その後、ユーザーが実行するべき適切な操作を支援します。
Azure AD のサインイン ページには、オンプレミスの Active Directory に対して定義されている UPN サフィックスの一覧と、各サフィックスの状態が表示されます。 状態値は、次のいずれかになります。

| 状態 | 説明 | 必要な対処 |
|:--- |:--- |:--- |
| Verified |Azure AD Connect が、Azure AD で一致する確認済みのドメインを検出しました。 このドメインのすべてのユーザーは、オンプレミスの資格情報を使用してサインインできます。 |対処は必要ありません。 |
| 未確認 |Azure AD Connect が Azure AD 内の一致するカスタム ドメインを検出しましたが、そのドメインはまだ確認されていません。 ドメインが確認されなかった場合、そのドメインのユーザーの UPN サフィックスは、同期後に既定のサフィックス (.onmicrosoft.com) へと変更されます。 | [Azure AD でカスタム ドメインを確認します。](../active-directory-add-domain.md#verify-the-domain-name-with-azure-ad) |
| 追加されていません |Azure AD Connect が、UPN サフィックスに対応するカスタム ドメインを検出できませんでした。 ドメインが追加されて確認されなかった場合、そのドメインのユーザーの UPN サフィックスは、既定のサフィックス (.onmicrosoft.com) へと変更されます。 | [UPN サフィックスに対応するカスタム ドメインを追加し、確認します。](../active-directory-add-domain.md) |

Azure AD サインイン ページには、オンプレミス Active Directory に対して定義されている UPN サフィックスの一覧と、対応する Azure AD のカスタム ドメインおよび現在の確認状態が表示されます。 カスタム インストールでは、**[Azure AD のサインイン]** ページでユーザー プリンシパル名の属性を選択できるようになりました。

![Azure AD サインイン ページ](./media/active-directory-aadconnect-user-signin/custom_azure_sign_in.png)

更新ボタンをクリックすると、カスタム ドメインの最新の状態を Azure AD から再取得することができます。

### <a name="selecting-the-attribute-for-the-user-principal-name-in-azure-ad"></a>Azure AD でのユーザー プリンシパル名の属性の選択
userPrincipalName 属性は、ユーザーが Azure AD と Office 365 にサインインするときに使用する属性です。 ユーザーを同期できるようにするには、まず Azure AD で使用するドメイン (UPN サフィックス) を確認する必要があります。

サインインには、既定の userPrincipalName 属性をそのまま使用することを強くお勧めします。 ただし、この属性がルーティング不可能で確認できない場合には、サインイン ID を保持する属性として、別の属性 (電子メールなど) を選択することができます。 これを代替 ID といいます。 代替 ID の属性値は、RFC822 標準に従う必要があります。 代替 ID は、サインイン ソリューションとして、パスワード SSO とフェデレーション SSO の両方で使用できます。

> [!NOTE]
> 代替 ID の使用は、すべての Office 365 ワークロードやパススルー認証と互換性があるわけではありません。 詳しくは、「[Configuring Alternate Login ID (代替ログイン ID の構成)](https://technet.microsoft.com/library/dn659436.aspx)」をご覧ください。
>
>

#### <a name="different-custom-domain-states-and-their-effect-on-the-azure-sign-in-experience"></a>カスタム ドメインの状態と Azure サインイン エクスペリエンスへの影響
Azure AD ディレクトリのカスタム ドメインの状態と、オンプレミスで定義された UPN サフィックスの間の関係を理解することは非常に重要です。 ここでは、Azure AD Connect を使用して同期を設定する際に遭遇しうる、いくつかの Azure サインイン エクスペリエンスについて見ていきましょう。

以下の説明では、UPN サフィックスが contoso.com である場合を想定しています。これは、オンプレミス ディレクトリで UPN の一部分として使用されます (例: user@contoso.com)。

###### <a name="express-settingspassword-synchronization"></a>簡単設定とパスワード同期
| 状態 | ユーザーの Azure サインイン エクスペリエンスへの影響 |
|:---:|:--- |
| 追加されていません |この場合、contoso.com のカスタム ドメインは Azure AD ディレクトリに追加されていません。 オンプレミスの UPN にサフィックス @contoso.com が付いているユーザーは、オンプレミスの UPN を使用して Azure にサインインすることができません。 代わりに、Azure AD によって提供された新しい UPN を使用する必要があります。この UPN は、既定の Azure AD ディレクトリのサフィックスを追加して作成されます。 たとえば、azurecontoso.onmicrosoft.com という Azure AD ディレクトリにユーザーを同期する場合、オンプレミス ユーザー user@contoso.com には、user@azurecontoso.onmicrosoft.com という UPN が与えられます。 |
| 未確認 |この場合、カスタム ドメイン contoso.com は既に Azure AD ディレクトリに追加されています。 ただし、まだ確認されていません。 ドメインを確認しないままユーザーの同期を進めようとすると、"追加されていません" のシナリオの場合と同様の方法で、Azure AD からユーザーに新しい UPN が割り当てられます。 |
| Verified |この場合、カスタム ドメイン contoso.com は既に Azure AD に追加され、UPN サフィックスに対して既に確認されています。 ユーザーは Azure AD に同期された後、オンプレミスのユーザー プリンシパル名 (例: user@contoso.com) を使用して Azure にサインインできます。 |

###### <a name="ad-fs-federation"></a>AD FS のフェデレーション
フェデレーションを作成する場合、Azure AD の既定の .onmicrosoft.com ドメインや、Azure AD 内のまだ確認されていないカスタム ドメインを使用することはできません。 Azure AD Connect ウィザードを実行している場合、未確認のドメインを選択してフェデレーションを作成しようとすると、そのドメインの DNS のホスト先に作成する必要があるレコードが、Azure AD Connect の確認メッセージに表示されます。 詳しくは、[「フェデレーション用に選択された Azure AD ドメインの検証」](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation)をご覧ください。

ユーザー サインイン オプションとして **[AD FS とのフェデレーション]** を選択した場合、Azure AD でのフェデレーションの作成を続行するには、カスタム ドメインが必要です。 この例では、カスタム ドメイン contoso.com が Azure AD ディレクトリに追加されている必要があります。

| 状態 | ユーザーの Azure サインイン エクスペリエンスへの影響 |
|:---:|:--- |
| 追加されていません |これは、Azure AD Connect が、UPN サフィックス contoso.com と一致するカスタム ドメインを Azure AD ディレクトリ内に検出できなかったことを意味します。 ユーザーが AD FS を使用して、オンプレミス UPN (user@contoso.com など) でサインインできるようにするには、カスタム ドメイン contoso.com を追加する必要があります。 |
| 未確認 |この場合は、Azure AD Connect の確認メッセージに、後でドメインを確認する方法についての適切な情報が示されます。 |
| Verified |この場合、特に対処は必要なく、そのまま構成を続行できます。 |

## <a name="changing-the-user-sign-in-method"></a>ユーザーのサインイン方法の変更
ウィザードを使用して Azure AD Connect の最初の構成を完了したら、Azure AD Connect で利用可能なタスクを使用して、ユーザーのサインイン方法 (フェデレーション、パスワード同期、またはパススルー認証) を変更できます。 Azure AD Connect ウィザードを再実行すると、実行できるタスクの一覧が表示されます。 タスクの一覧から **[ユーザー サインインの変更]** を選択します。

![ユーザー サインインの変更](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

次のページで、Azure AD の資格情報の入力を求められます。

![Azure への接続](./media/active-directory-aadconnect-user-signin/changeusersignin2.png)

「**ユーザーのサインイン**」ページで、目的のユーザーのサインインを選択します。

![Azure への接続](./media/active-directory-aadconnect-user-signin/changeusersignin2a.png)

> [!NOTE]
> 一時的にパスワード同期に切り替えるだけの場合は、 **[ユーザー アカウントを変換しない]** チェック ボックスをオンにします。 このオプションをオンにしないと、各ユーザーがフェデレーション ディレクトリに変換されることになり、数時間かかることがあります。
>
>

## <a name="next-steps"></a>次のステップ
- [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)に関する記事をご覧ください。
- [Azure AD Connect の設計概念](active-directory-aadconnect-design-concepts.md)についての詳しく知る。

