---
title: Azure AD Connect:ユーザー サインイン | Microsoft Docs
description: Azure AD Connect ユーザー サインインのカスタム設定。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 547b118e-7282-4c7f-be87-c035561001df
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/31/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3365a58a0c667ca55b74a5120cdd7a78ad0abc79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997785"
---
# <a name="azure-ad-connect-user-sign-in-options"></a>Azure AD Connect ユーザーのサインイン オプション
Azure Active Directory (Azure AD) Connect では、ユーザーは同じパスワードを使用して、クラウドとオンプレミス両方のリソースにサインインできます。 この記事では、Azure AD へのサインインに使用する ID を選択できるようにするために、各 ID モデルの主要な概念について説明します。

既に Azure AD の ID モデルについての知識があり、特定の方法の詳細を知りたい場合は、適切なリンクを参照してください。

* [シームレス シングル サインオン (SSO)](how-to-connect-sso.md) による[パスワード ハッシュの同期](#password-hash-synchronization)
* [シームレス シングル サインオン (SSO)](how-to-connect-sso.md) による[パススルー認証](how-to-connect-pta.md)
* [フェデレーション SSO (Active Directory フェデレーション サービス (AD FS) を使用する)](#federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2)
* [PingFederate によるフェデレーション](#federation-with-pingfederate)

> [!NOTE] 
> Azure AD に対してフェデレーションを構成することで、Azure AD テナントとフェデレーション ドメインの間に信頼が確立されるということを憶えておくことが重要です。 この信頼により、フェデレーション ドメインのユーザーは、テナント内の Azure AD クラウド リソースにアクセスできます。  
>

## <a name="choosing-the-user-sign-in-method-for-your-organization"></a>組織のユーザー サインイン方法の選択
Azure AD Connect を導入するにあたっては、まずユーザーのサインインに使用する認証方法を選択する必要があります。 所属する組織のセキュリティ要件と詳細な要件を満たした適切な方法を選択することが大切です。 認証はきわめて重要です。クラウド内のアプリとデータにアクセスするユーザーの ID は認証によって確認することになるためです。 適切な認証方法を選ぶには、時間、既存のインフラストラクチャ、複雑さ、および選んだ方法の実装にかかるコストを考慮する必要があります。 これらの要因は組織ごとに異なり、時間の経過とともに変化する場合があります。

Azure AD は、次の認証方法をサポートします。 

* **クラウド認証** - この認証方法を選ぶと、ユーザーのサインインの認証プロセスが Azure AD によって処理されます。 クラウド認証では、2 つのオプションから選ぶことができます。 
   * **パスワード ハッシュ同期 (PHS)** - パスワード ハッシュ同期では、ユーザーはオンプレミスで使用しているものと同じユーザー名とパスワードを使用でき、Azure AD Connect 以外に追加のインフラストラクチャを展開する必要はありません。 
   * **パススルー認証 (PTA)** - このオプションは、パスワード ハッシュ同期と似ていますが、セキュリティとコンプライアンスに関して厳格なポリシーを導入している組織のために、オンプレミスのソフトウェア エージェントを使用したシンプルなパスワード検証が提供されます。
* **フェデレーション認証** - この認証方法を選ぶと、信頼のおける別のシステム (AD FS やサードパーティのフェデレーション システムなど) が Azure AD から認証プロセスを引き継いでユーザーのサインインを検証します。 

Microsoft 365、SaaS アプリケーション、およびその他の Azure AD ベースのリソースへのユーザー サインインのみを有効にするほとんどの組織では、既定のパスワード ハッシュの同期オプションをお勧めします。
 
認証方法の選択の詳細については、「[Azure Active Directory ハイブリッド ID ソリューションの適切な認証方法を選択する](./choose-ad-authn.md)」を参照してください。

### <a name="password-hash-synchronization"></a>パスワード ハッシュの同期
パスワード ハッシュの同期では、ユーザー パスワードのハッシュがオンプレミスの Active Directory から Azure AD に同期されます。 オンプレミスでパスワードが変更またはリセットされると、ユーザーが常にクラウドのリソースとオンプレミスのリソースに同じパスワードを使用できるように、新しいパスワード ハッシュが直ちに Azure AD に同期されます。 パスワードがクリア テキストの状態で Azure AD に送信されたり Azure AD に格納されたりすることはありません。 パスワード ハッシュの同期をパスワードの書き戻しと共に使用すると、Azure AD でセルフサービス パスワード リセットを有効にできます。

さらに、企業ネットワーク上にある、ドメインに参加しているマシン上のユーザーに対して[シームレス SSO](how-to-connect-sso.md) を有効にすることができます。 シングル サインオンを使用すれば、有効になっているユーザーはユーザー名のみを入力して、クラウド リソースに安全にアクセスできます。

![パスワード ハッシュの同期](./media/plan-connect-user-signin/passwordhash.png)

詳細については、[パスワード ハッシュの同期](how-to-connect-password-hash-synchronization.md)に関する記事を参照してください。

### <a name="pass-through-authentication"></a>パススルー認証
パススルー認証では、ユーザーのパスワードはオンプレミスの Active Directory コントローラーに対して検証されます。 パスワードを何らかの形式で Azure AD に保存する必要はありません。 そのため、クラウド サービスへの認証時に、オンプレミスのポリシー (ログオン時間制限など) を評価することができます。

パススルー認証では、オンプレミス環境内の Windows Server 2012 R2 ドメイン参加済みコンピューター上にある、シンプルなエージェントが使用されます。 このエージェントが、パスワードの検証要求をリッスンします。 インターネットに対して受信ポートを開放する必要は一切ありません。

さらに、企業ネットワーク上にあるドメイン参加済みコンピューターのユーザーに対しても、シングル サインオンを有効化できます。 シングル サインオンを使用すれば、有効になっているユーザーはユーザー名のみを入力して、クラウド リソースに安全にアクセスできます。
![パススルー認証](./media/plan-connect-user-signin/pta.png)

詳細については、次を参照してください。
- [パススルー認証](how-to-connect-pta.md)
- [シングル サインオン](how-to-connect-sso.md)

### <a name="federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2"></a>Windows Server 2012 R2 の AD FS を使用した新規または既存のファームを使用するフェデレーション
フェデレーション サインインでは、ユーザーはオンプレミスのパスワードを使用して Azure AD ベースのサービスにサインインできます。 企業ネットワーク上にいる時には、パスワードを入力する必要すらありません。 AD FS によるフェデレーション オプションを使用すれば、Windows Server 2012 R2 の AD FS を使用した新規または既存のファームをデプロイできます。 既存のファームを指定する場合は、Azure AD Connect によってファームと Azure AD との間に信頼が構成され、それにより、ユーザーがサインインできるようになります。

<center>

![Windows Server 2012 R2 の AD FS を使用したフェデレーション](./media/plan-connect-user-signin/federatedsignin.png)</center>

#### <a name="deploy-federation-with-ad-fs-in-windows-server-2012-r2"></a>Windows Server 2012 R2 の AD FS を使用してフェデレーションをデプロイする

新しいファームをデプロイする場合は、次のものが必要です。

* フェデレーション サーバー用の Windows Server 2012 R2 サーバー。
* Web アプリケーション プロキシ用の Windows Server 2012 R2 サーバー。
* 目的のフェデレーション サービス名に対する TLS/SSL 証明書を 1 つ含んだ .pfx ファイル。 サービス名の例: www.contoso.com。

新しいファームをデプロイするか、既存のファームを使用する場合は、次のものが必要です。

* フェデレーション サーバー上のローカル管理者の資格情報。
* Web アプリケーション プロキシ ロールをデプロイするワークグループ サーバー (ドメイン不参加) 上の、ローカル管理者の資格情報。
* ウィザードを実行するコンピューター。これにより、Windows リモート管理を使用して、AD FS または Web アプリケーション プロキシをインストールする他のコンピューターに接続できるようになります。

詳しくは、「[AD FS を使用した SSO の構成](how-to-connect-install-custom.md#configuring-federation-with-ad-fs)」をご覧ください。

### <a name="federation-with-pingfederate"></a>PingFederate によるフェデレーション
フェデレーション サインインでは、ユーザーはオンプレミスのパスワードを使用して Azure AD ベースのサービスにサインインできます。 企業ネットワーク上にいる時には、パスワードを入力する必要すらありません。

Azure Active Directory で使用するための PingFederate の構成の詳細については、「[PingFederate Integration with Azure Active Directory and Office 365](https://www.pingidentity.com/AzureADConnect)」 (Azure Active Directory および Office 365 との PingFederate の統合) を参照してください。

PingFederate を使用して Azure AD Connect を設定する方法については、「[Azure AD Connect のカスタム インストール](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)」を参照してください。

#### <a name="sign-in-by-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>以前のバージョンの AD FS またはサード パーティのソリューションを使用してサインインする
以前のバージョンの AD FS (AD FS 2.0 など) またはサード パーティのフェデレーション プロバイダーを使用して、クラウド サインインを既に構成している場合は、Azure AD Connect を通じてユーザー サインインの構成をスキップできます。 これにより、既存のソリューションをサインインに使用しながら、最新の同期内容と Azure AD Connect のその他の機能を使用できるようになります。

詳しくは、「[Azure AD のサードパーティ フェデレーション互換性リスト](how-to-connect-fed-compatibility.md)」をご覧ください。


## <a name="user-sign-in-and-user-principal-name"></a>ユーザーのサインインとユーザー プリンシパル名
### <a name="understanding-user-principal-name"></a>ユーザー プリンシパル名について
Active Directory では、既定のユーザー プリンシパル名 (UPN) サフィックスは、ユーザー アカウントが作成されたドメインの DNS 名です。 ほとんどの場合、これはインターネット上で企業ドメインとして登録されたドメイン名です。 ただし、Active Directory ドメインと信頼関係を使用して、複数の UPN サフィックスを追加することもできます。

ユーザーの UPN は、username@domain という形式で表されます。 たとえば、"contoso.com" という名前の Active Directory ドメインの場合、John という名前のユーザーの UPN は "john@contoso.com" になります。 ユーザーの UPN は RFC 822 に基づいています。 UPN と電子メールは形式が同じですが、ユーザーの UPN の値は、ユーザーの電子メール アドレスと必ずしも同じものになるとは限りません。

### <a name="user-principal-name-in-azure-ad"></a>Azure AD のユーザー プリンシパル名
Azure AD Connect ウィザードでは、userPrincipalName 属性を使用することもできますが、Azure AD のユーザー プリンシパル名として使用される属性をオンプレミスから指定することもできます (後者はカスタム インストールの場合です)。 この値が、Azure AD へのサインインに使用されます。 ユーザー プリンシパル名の属性値が Azure AD の確認済みドメインに対応しない場合は、値が既定値 (.onmicrosoft.com) に置き換えられます。

Azure Active Directory のすべてのディレクトリには、contoso.onmicrosoft.com という形式のドメイン名があらかじめ設定されており、これによってユーザーは、Azure をはじめとする Microsoft の各種サービスを利用できるようになっています。 カスタム ドメインを使用すれば、サインイン エクスペリエンスを改善したり、簡素化することもできます。 Azure AD のカスタム ドメイン名とドメインの確認方法については、「[Azure Active Directory へのカスタム ドメイン名の追加](../fundamentals/add-custom-domain.md)」をご覧ください。

## <a name="azure-ad-sign-in-configuration"></a>Azure AD サインインの構成
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Azure AD Connect による Azure AD サインインの構成
Azure AD のサインイン エクスペリエンスは、同期するユーザーのユーザー プリンパル名のサフィックスが、Azure AD ディレクトリで確認済みのカスタム ドメインのいずれかと一致するかどうかによって変わります。 Azure AD Connect を使用すれば、Azure AD のサインイン設定を効率的に行って、クラウドでのユーザー サインイン エクスペリエンスをオンプレミスでのエクスペリエンスと同様のものにすることができます。

Azure AD Connect は、ドメインに対して定義されているUPN サフィックスをリストし、それらを Azure AD 内のカスタム ドメインと照合しようとします。 その後、ユーザーが実行するべき適切な操作を支援します。
Azure AD のサインイン ページには、オンプレミスの Active Directory に対して定義されている UPN サフィックスの一覧と、各サフィックスの状態が表示されます。 状態値は、次のいずれかになります。

| State | 説明 | 必要な対処 |
|:--- |:--- |:--- |
| Verified |Azure AD Connect が、Azure AD で一致する確認済みのドメインを検出しました。 このドメインのすべてのユーザーは、オンプレミスの資格情報を使用してサインインできます。 |対処は必要ありません。 |
| 未確認 |Azure AD Connect が Azure AD 内の一致するカスタム ドメインを検出しましたが、そのドメインはまだ確認されていません。 ドメインが確認されなかった場合、そのドメインのユーザーの UPN サフィックスは、同期後に既定のサフィックス (.onmicrosoft.com) へと変更されます。 | [Azure AD でカスタム ドメインを確認します。](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) |
| 追加されていません |Azure AD Connect が、UPN サフィックスに対応するカスタム ドメインを検出できませんでした。 ドメインが追加されて確認されなかった場合、そのドメインのユーザーの UPN サフィックスは、既定のサフィックス (.onmicrosoft.com) へと変更されます。 | [UPN サフィックスに対応するカスタム ドメインを追加し、確認します。](../fundamentals/add-custom-domain.md) |

Azure AD サインイン ページには、オンプレミス Active Directory に対して定義されている UPN サフィックスの一覧と、対応する Azure AD のカスタム ドメインおよび現在の確認状態が表示されます。 カスタム インストールでは、 **[Azure AD のサインイン]** ページでユーザー プリンシパル名の属性を選択できるようになりました。

![Azure AD サインイン ページ](./media/plan-connect-user-signin/custom_azure_sign_in.png)

更新ボタンをクリックすると、カスタム ドメインの最新の状態を Azure AD から再取得することができます。

### <a name="selecting-the-attribute-for-the-user-principal-name-in-azure-ad"></a>Azure AD でのユーザー プリンシパル名の属性の選択
userPrincipalName 属性は、ユーザーが Azure AD と Microsoft 365 にサインインするときに使用する属性です。 ユーザーを同期できるようにするには、まず Azure AD で使用するドメイン (UPN サフィックス) を確認する必要があります。

サインインには、既定の userPrincipalName 属性をそのまま使用することを強くお勧めします。 ただし、この属性がルーティング不可能で確認できない場合には、サインイン ID を保持する属性として、別の属性 (電子メールなど) を選択することができます。 これを代替 ID といいます。 代替 ID の属性値は、RFC822 標準に従う必要があります。 代替 ID は、サインイン ソリューションとして、パスワード SSO とフェデレーション SSO の両方で使用できます。

> [!NOTE]
> 代替 ID の使用は、すべての Microsoft 365 ワークロードに対応しているわけではありません。 詳しくは、「[Configuring Alternate Login ID (代替ログイン ID の構成)](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)」をご覧ください。
>
>

#### <a name="different-custom-domain-states-and-their-effect-on-the-azure-sign-in-experience"></a>カスタム ドメインの状態と Azure サインイン エクスペリエンスへの影響
Azure AD ディレクトリのカスタム ドメインの状態と、オンプレミスで定義された UPN サフィックスの間の関係を理解することは非常に重要です。 ここでは、Azure AD Connect を使用して同期を設定する際に遭遇しうる、いくつかの Azure サインイン エクスペリエンスについて見ていきましょう。

以下の説明では、UPN サフィックスが contoso.com である場合を想定しています。これは、オンプレミス ディレクトリで UPN の一部分として使用されます (例: user@contoso.com)。

###### <a name="express-settingspassword-hash-synchronization"></a>簡易設定/パスワード ハッシュの同期

| State | ユーザーの Azure サインイン エクスペリエンスへの影響 |
|:---:|:--- |
| 追加されていません |この場合、contoso.com のカスタム ドメインは Azure AD ディレクトリに追加されていません。 オンプレミスの UPN にサフィックス @contoso.com が付いているユーザーは、オンプレミスの UPN を使用して Azure にサインインすることができません。 代わりに、Azure AD によって提供された新しい UPN を使用する必要があります。この UPN は、既定の Azure AD ディレクトリのサフィックスを追加して作成されます。 たとえば、azurecontoso.onmicrosoft.com という Azure AD ディレクトリにユーザーを同期する場合、オンプレミス ユーザー user@contoso.com には、user@azurecontoso.onmicrosoft.com という UPN が与えられます。 |
| 未確認 |この場合、カスタム ドメイン contoso.com は既に Azure AD ディレクトリに追加されています。 ただし、まだ確認されていません。 ドメインを確認しないままユーザーの同期を進めようとすると、"追加されていません" のシナリオの場合と同様の方法で、Azure AD からユーザーに新しい UPN が割り当てられます。 |
| Verified |この場合、カスタム ドメイン contoso.com は既に Azure AD に追加され、UPN サフィックスに対して既に確認されています。 ユーザーは Azure AD に同期された後、オンプレミスのユーザー プリンシパル名 (例: user@contoso.com) を使用して Azure にサインインできます。 |

###### <a name="ad-fs-federation"></a>AD FS のフェデレーション
フェデレーションを作成する場合、Azure AD の既定の .onmicrosoft.com ドメインや、Azure AD 内のまだ確認されていないカスタム ドメインを使用することはできません。 Azure AD Connect ウィザードを実行している場合、未確認のドメインを選択してフェデレーションを作成しようとすると、そのドメインの DNS のホスト先に作成する必要があるレコードが、Azure AD Connect の確認メッセージに表示されます。 詳しくは、[「フェデレーション用に選択された Azure AD ドメインの検証」](how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation)をご覧ください。

ユーザー サインイン オプションとして **[AD FS とのフェデレーション]** を選択した場合、Azure AD でのフェデレーションの作成を続行するには、カスタム ドメインが必要です。 この例では、カスタム ドメイン contoso.com が Azure AD ディレクトリに追加されている必要があります。

| State | ユーザーの Azure サインイン エクスペリエンスへの影響 |
|:---:|:--- |
| 追加されていません |これは、Azure AD Connect が、UPN サフィックス contoso.com と一致するカスタム ドメインを Azure AD ディレクトリ内に検出できなかったことを意味します。 ユーザーが AD FS を使用して、オンプレミス UPN (user@contoso.com など) でサインインできるようにするには、カスタム ドメイン contoso.com を追加する必要があります。 |
| 未確認 |この場合は、Azure AD Connect の確認メッセージに、後でドメインを確認する方法についての適切な情報が示されます。 |
| Verified |この場合、特に対処は必要なく、そのまま構成を続行できます。 |

## <a name="changing-the-user-sign-in-method"></a>ユーザーのサインイン方法の変更
ウィザードによる Azure AD Connect の初期構成の後、Azure AD Connect で使用可能なタスクを使用して、ユーザーのサインイン方法をフェデレーション、パスワード ハッシュの同期、またはパススルー認証から変更できます。 Azure AD Connect ウィザードを再実行すると、実行できるタスクの一覧が表示されます。 タスクの一覧から **[ユーザー サインインの変更]** を選択します。

![ユーザー サインインの変更](./media/plan-connect-user-signin/changeusersignin.png)

次のページで、Azure AD の資格情報の入力を求められます。

![Azure AD の資格情報を入力する場所を示すスクリーンショット。](./media/plan-connect-user-signin/changeusersignin2.png)

「**ユーザーのサインイン**」ページで、目的のユーザーのサインインを選択します。

![Azure への接続](./media/plan-connect-user-signin/changeusersignin2a.png)

> [!NOTE]
> パスワード ハッシュの同期への一時的な切り替えを行なっているだけの場合は、 **[ユーザー アカウントを変換しない]** チェック ボックスをオンにします。 このオプションをオンにしないと、各ユーザーがフェデレーション ディレクトリに変換されることになり、数時間かかることがあります。
>
>

## <a name="next-steps"></a>次のステップ
- [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)に関する記事をご覧ください。
- [Azure AD Connect の設計概念](plan-connect-design-concepts.md)についての詳しく知る。