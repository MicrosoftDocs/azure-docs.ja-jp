---
title: Azure Active Directory Connect のインストールのカスタマイズ
description: この記事では、Azure AD Connect のカスタム インストール オプションについて説明します。 この手順を使用すると、Azure AD Connect から Active Directory をインストールできます。
services: active-directory
keywords: Azure AD Connect とは, Active Directory のインストール, Azure AD に必要なコンポーネント
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 6d42fb79-d9cf-48da-8445-f482c4c536af
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/10/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3afeadff71bd373354b891bd6690d94d28fc0805
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92096353"
---
# <a name="custom-installation-of-azure-active-directory-connect"></a>Azure Active Directory Connect のカスタム インストール
Azure Active Directory (Azure AD) Connect の "*カスタム設定*" は、より多くのインストール オプションが必要な場合に使用します。 これらの設定は、たとえば複数のフォレストがある場合、またはオプションの機能を構成する場合に使用します。 [高速インストール](how-to-connect-install-express.md)では対象のデプロイまたはトポロジのニーズに対応できないすべての場合に、カスタム設定を使用します。

前提条件:
- [Azure AD Connect のダウンロード](https://go.microsoft.com/fwlink/?LinkId=615771)。
- [Azure AD Connect: ハードウェアと前提条件](how-to-connect-install-prerequisites.md)に関するページに記載されている前提条件の手順を完了してください。 
- 「[Azure AD Connect: アカウントとアクセス許可](reference-connect-accounts-permissions.md)」で説明されているアカウントが利用できることを確認してください。

## <a name="custom-installation-settings"></a>カスタム インストールの設定 

Azure AD Connect のカスタム インストールをセットアップするには、次のセクションで説明するウィザードのページを参照してください。

### <a name="express-settings"></a>簡単設定
**[簡単設定]** ページで **[カスタマイズ]** をクリックして、カスタム設定を使用したインストールを開始します。  この記事の残りの部分では、カスタム インストール プロセスについて説明します。 次のリンクを使用して、特定のページの情報にすばやくアクセスします。

- [必須コンポーネント](#install-required-components)
- [ユーザー サインイン](#user-sign-in)
- [Azure への接続](#connect-to-azure-ad)
- [同期](#sync-pages)

### <a name="install-required-components"></a>必須コンポーネントのインストール
同期サービスをインストールするとき、オプションの構成セクションをオフのままにすることができます。 Azure AD Connect によってすべてが自動的に設定されます。 これにより、SQL Server 2012 Express LocalDB インスタンスの設定、適切なグループの作成、アクセス許可の割り当てが行われます。 既定値を変更する場合は、該当するボックスをオフにします。  次の表は、これらのオプションの概要を示し、追加情報へのリンクを示しています。 

![Azure AD Connect で必要なインストール コンポーネントのオプションの選択を示すスクリーンショット。](./media/how-to-connect-install-custom/requiredcomponents2.png)

| オプションの構成 | 説明 |
| --- | --- |
|カスタム インストール先を指定する| Azure AD Connect の既定のインストール パスを変更できます。|
| 既存の SQL Server を使用する |SQL Server 名とインスタンス名を指定することができます。 使用するデータベース サーバーが既にある場合は、このオプションを選択します。 SQL Server インスタンスで参照が有効になっていない場合は、 **[インスタンス名]** に、インスタンス名、コンマ、ポート番号の順に入力してください。  次に、Azure AD Connect データベースの名前を指定します。  SQL 権限によって、新しいデータベースが作成されるか、SQL 管理者が事前にデータベースを作成する必要があるかどうかが決まります。  SQL Server 管理者 (SA) のアクセス許可がある場合は、[既存のデータベースを使用した Azure AD Connect のインストール](how-to-connect-install-existing-database.md)に関するページを参照してください。  委任されたアクセス許可 (DBO) がある場合は、「[SQL によって委任された管理者のアクセス許可を使用した Azure AD Connect のインストール](how-to-connect-install-sql-delegation.md)」をご覧ください。 |
| 既存のサービス アカウントを使用する |既定では、同期サービス用の仮想サービス アカウントが Azure AD Connect によって提供されます。 SQL Server のリモート インスタンスを使用する場合、または認証が必要なプロキシを使用する場合は、ドメイン内の *マネージド サービス アカウント* またはパスワードで保護されたサービス アカウントを使用することができます。 このような場合は、使用するアカウントを入力します。 インストールを実行するには、サービス アカウントのサインイン資格情報を作成できるように、SQL の SA である必要があります。 詳細については、[Azure AD Connect アカウントとアクセス許可](reference-connect-accounts-permissions.md#adsync-service-account)に関するページを参照してください。 </br></br>最新のビルドを使用することで、SQL 管理者は帯域外でデータベースをプロビジョニングできるようになりました。 その後、Azure AD Connect 管理者は、データベース所有者権限を使用してインストールできます。  詳細については、「[SQL によって委任された管理者のアクセス許可を使用した Azure AD Connect のインストール](how-to-connect-install-sql-delegation.md)」を参照してください。|
| カスタム同期グループを指定する |既定では、同期サービスのインストール時に、Azure AD Connect によってサーバーに対してローカルな 4 つのグループが作成されます。 これらのグループは、管理者、オペレーター、参照、およびパスワード リセットです。 ここでは独自のグループを指定できます。 グループはサーバー上でローカルである必要があります。 ドメイン内に配置することはできません。 |
|同期設定をインポート (プレビュー)|別のバージョンの Azure AD Connect から設定をインポートできます。  詳しくは、「[Azure AD Connect 構成設定のインポートとエクスポート](how-to-connect-import-export-config.md)」を参照してください。|

### <a name="user-sign-in"></a>ユーザーのサインイン
必要なコンポーネントがインストールされると、ユーザーによるシングル サインオンの方法を選択します。 次の表で、使用可能なオプションについて簡単に説明します。 サインイン方法の詳細については、[ユーザーのサインイン](plan-connect-user-signin.md)に関するページを参照してください。

![[ユーザー サインイン] ページを示すスクリーンショット。 [パスワード ハッシュの同期] オプションが選択されています。](./media/how-to-connect-install-custom/usersignin4.png)

| シングル サインオン オプション | 説明 |
| --- | --- |
| パスワード ハッシュの同期 |ユーザーは、オンプレミス ネットワークで使用しているものと同じパスワードで、Microsoft 365 などの Microsoft クラウド サービスにサインインできます。 ユーザー パスワードは、パスワード ハッシュとして Azure AD に同期されています。 認証はクラウドで行われます。 詳細については、[パスワード ハッシュの同期](how-to-connect-password-hash-synchronization.md)に関するページを参照してください。 |
|パススルー認証|ユーザーは、オンプレミス ネットワークで使用しているものと同じパスワードで、Microsoft 365 などの Microsoft クラウド サービスにサインインできます。  ユーザー パスワードはオンプレミスの Active Directory ドメイン コントローラーにパススルーされて検証されます。
| AD FS とのフェデレーション |ユーザーは、オンプレミス ネットワークで使用しているものと同じパスワードで、Microsoft 365 などの Microsoft クラウド サービスにサインインできます。  ユーザーはサインインのためにオンプレミスの Azure Directory Federation Services (AD FS) インスタンスにリダイレクトされます。 認証はオンプレミスで行われます。 |
| PingFederate によるフェデレーション|ユーザーは、オンプレミス ネットワークで使用しているものと同じパスワードで、Microsoft 365 などの Microsoft クラウド サービスにサインインできます。  ユーザーはサインインのためにオンプレミスの PingFederate インスタンスにリダイレクトされます。 認証はオンプレミスで行われます。 |
| 構成しない |ユーザー サインイン機能はインストールおよび構成されません。 サード パーティのフェデレーション サーバーまたは別のソリューションが既に設置されている場合は、このオプションを選択します。 |
|シングル サインオンの有効化|このオプションは、パスワード ハッシュの同期とパススルー認証の両方で使用できます。 企業ネットワーク上のデスクトップ ユーザーにシングル サインオン エクスペリエンスを提供します。 詳細については、[シングル サインオン](how-to-connect-sso.md)に関するページを参照してください。 </br></br>**注:** AD FS のお客様の場合、このオプションは使用できません。 AD FS には、同じレベルのシングル サインオンが既に用意されています。</br>

### <a name="connect-to-azure-ad"></a>Azure への接続
**[Azure AD に接続]** ページで、グローバル管理者のアカウントとパスワードを入力します。 前のページで **[AD FS とのフェデレーション]** を選択した場合、フェデレーション用に有効にする予定があるドメイン内のアカウントでサインインしないようにしてください。 

Azure AD テナントに付属する既定の *onmicrosoft.com* ドメイン内のアカウントを使用する場合もあります。 このアカウントは、Azure AD でサービス アカウントを作成するためにのみ使用されます。 インストールの完了後は使用されません。
  
![[Azure AD に接続] ページを示すスクリーンショット。](./media/how-to-connect-install-custom/connectaad.png)

グローバル管理者アカウントで多要素認証が有効になっている場合は、サインイン ウィンドウにもう一度パスワードを入力し、多要素認証チャレンジを完了する必要があります。 チャレンジは確認コードか音声通話によって行うことができます。  

![[Azure AD に接続] ページを示すスクリーンショット。 多要素認証フィールドは、ユーザーにコードの入力を求めます。](./media/how-to-connect-install-custom/connectaadmfa.png)

グローバル管理者アカウントは [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md) も有効になっている可能性があります。

エラーが表示されるか、接続に問題がある場合は、[接続の問題に対するトラブルシューティング](tshoot-connect-connectivity.md)についてのページを参照してください。

## <a name="sync-pages"></a>ページの同期

以降では、 **[同期]** セクションの各ウィンドウについて説明します。

### <a name="connect-your-directories"></a>ディレクトリの接続
Azure AD Connect では、Active Directory ドメイン サービス (Azure AD DS) に接続するには、十分なアクセス許可を持つアカウントのフォレスト名と資格情報が必要です。

![[ディレクトリの接続] ページを示すスクリーンショット。](./media/how-to-connect-install-custom/connectdir01.png)

フォレスト名を入力して **[ディレクトリの追加]** を選択すると、ウィンドウが表示されます。 次の表では、オプションについて説明します。

| オプション | 説明 |
| --- | --- |
| 新しいアカウントを作成します | ディレクトリ同期中に Azure AD Connect が Active Directory フォレストに接続するために必要とする Azure AD DS アカウントを作成します。 このオプションを選択した後、エンタープライズ管理者アカウントのユーザー名とパスワードを入力します。  Azure AD Connect は指定したエンタープライズ管理者アカウントを使用して、必要な AD DS アカウントを作成します。 ドメイン部分は、NetBIOS 形式または FQDN 形式で入力できます。 つまり、*FABRIKAM\administrator* または *fabrikam.com\administrator* と入力します。 |
| 既存のアカウントを使用します | ディレクトリ同期中に Azure AD Connect が Active Directory フォレストに接続するために使用できる Azure AD DS アカウントを作成します。 ドメイン部分は、NetBIOS 形式または FQDN 形式で入力できます。 つまり、*FABRIKAM\syncuser* または *fabrikam.com\syncuser* と入力します。 このアカウントには既定の読み取りアクセス許可が必要なだけなので、通常のユーザー アカウントを指定できます。 ただし、シナリオによっては、アクセス許可がさらに必要になることがあります。 詳細については、[Azure AD Connect アカウントとアクセス許可](reference-connect-accounts-permissions.md#create-the-ad-ds-connector-account)に関するページを参照してください。 |

![[ディレクトリの接続] ページと [AD フォレスト アカウント] ウィンドウを示すスクリーンショット。ここでは、新しいアカウントを作成したり、既存のアカウントを使用したりできます。](./media/how-to-connect-install-custom/connectdir02.png)

>[!NOTE]
> ビルド 1.4.18.0 以降では、エンタープライズ管理者またはドメイン管理者のアカウントを Azure AD DS コネクタ アカウントとして使用することはできません。 **[既存アカウントを使用]** を選択した場合は、エンタープライズ管理者アカウントまたはドメイン管理者アカウントを入力しようとすると、次のエラーが表示されます。"AD フォレスト アカウントにエンタープライズまたはドメインの管理者アカウントを使用することはできません。 Azure AD Connect でアカウントを作成するか、適切なアクセス許可を持つ同期アカウントを指定してください。
>

### <a name="azure-ad-sign-in-configuration"></a>Azure AD サインインの構成
**[Azure AD サインインの構成]** ページで、オンプレミス Azure AD DS のユーザー プリンシパル名 (UPN) ドメインを確認します。 これらの UPN ドメインは Azure AD で検証済みです。 このページで、userPrincipalName に使用する属性を構成できます。

![[Azure AD サインインの構成] ページで未確認ドメインを示すスクリーンショット。](./media/how-to-connect-install-custom/aadsigninconfig2.png)  

**[追加されていません]** または **[未確認]** としてマークされているすべてのドメインを確認します。 使用するドメインを Azure AD で検証済みにしてください。 ドメインを確認したら、循環更新アイコンを選択します。 詳細については、[ドメインの追加と検証](../fundamentals/add-custom-domain.md)に関するページを参照してください。

ユーザーは Azure AD と Microsoft 365 にサインインするとき、*userPrincipalName* 属性を使用します。 ユーザーを同期できるようにするには、まず Azure AD で、UPN サフィックスとも呼ばれるドメインを確認する必要があります。 既定の userPrincipalName 属性のままにしておくことをお勧めします。 

userPrincipalName 属性がルーティング不可能で、検証できない場合は、別の属性を選択できます。 たとえば、サインイン ID を保持する属性として電子メールを選択することができます。 userPrincipalName 以外の属性を使用する場合、これは "*代替 ID*" と呼ばれます。 

代替 ID の属性値は、RFC822 標準に従う必要があります。 代替 ID は、パスワード ハッシュの同期、パススルー認証、およびフェデレーションで使用できます。 Active Directory では、値が 1 つのみであってもこの属性を複数値として定義できません。 代替 ID の詳細については、「[パススルー認証:よく寄せられる質問](./how-to-connect-pta-faq.md#does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname)」をご覧ください。

>[!NOTE]
> パススルー認証を有効にする場合、カスタム インストール プロセスを続行するために少なくとも 1 つの検証済みドメインが必要になります。

> [!WARNING]
> 代替 ID は、すべての Microsoft 365 ワークロードに対応しているわけではありません。 詳しくは、[代替サインイン ID の構成](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)に関する記事をご覧ください。
>

### <a name="domain-and-ou-filtering"></a>ドメインと OU のフィルター処理
既定では、すべてのドメインと組織単位 (OU) が同期されます。 一部のドメインまたは OU を Azure AD に同期しない場合は、適切な選択を解除できます。  

![[ドメインと OU のフィルタリング] ページを示すスクリーンショット。](./media/how-to-connect-install-custom/domainoufiltering.png)  

このページでは、ドメインベースおよび OU ベースのフィルター処理を構成します。 変更を予定している場合は、[ドメイン ベースのフィルター処理](how-to-connect-sync-configure-filtering.md#domain-based-filtering)に関するトピックと [OU ベースのフィルター処理](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)に関するトピックを参照してください。 一部の OU は機能に不可欠であるため、選択したままにしておく必要があります。

1\.1.524.0 より前のバージョンの Azure AD Connect で OU ベースのフィルター処理を使用している場合、新しい OU は、既定で同期されます。 新しい OU が同期されないようにするには、「[OU ベースのフィルター処理](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)」手順の後に既定の動作を調整できます。 Azure AD Connect 1.1.524.0 以降では、新しい OU を同期するか指定できます。

[グループベースのフィルター処理](#sync-filtering-based-on-groups)を使用する予定の場合は、そのグループが属する OU が含まれており、OU フィルタリングによって除外されないようにしてください。 OU ベースのフィルター処理は、グループベースのフィルター処理が評価される前に評価されます。

さらに、一部のドメインは、ファイアウォールの制限のために到達できないこともあります。 このようなドメインは既定で選択が解除されており、警告が表示されます。  

![到達できないドメインを示すスクリーンショット。](./media/how-to-connect-install-custom/unreachable.png)  

この警告が表示される場合は、これらのドメインが実際に到達不能であり、警告の表示が予想されるものであることを確認してください。

### <a name="uniquely-identifying-your-users"></a>ユーザーを一意に識別

**[ユーザーの識別]** ページで、オンプレミスのディレクトリでユーザーを識別する方法と、sourceAnchor 属性を使用してそれらを識別する方法を選択します。

#### <a name="select-how-users-should-be-identified-in-your-on-premises-directories"></a>オンプレミスのディレクトリでのユーザーの識別方法を選択する
*[フォレスト全体で一致]* 機能を使用すると、Azure AD DS フォレストのユーザーを Azure AD でどう表すかを定義することができます。 ユーザーは、すべてのフォレストで 1 回だけ表すか、有効アカウントと無効アカウントを組み合わせることができます。 一部のフォレストでは、ユーザーを連絡先として表すこともできます。

![ユーザーを一意に識別できるページを示すスクリーンショット。](./media/how-to-connect-install-custom/unique2.png)

| 設定 | 説明 |
| --- | --- |
| [ユーザーはフォレスト全体で 1 回だけ表されます](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |すべてのユーザーは、Azure AD の個々のオブジェクトとして作成されます。 オブジェクトはメタバースに結合されません。 |
| [メール属性](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |このオプションは、異なるフォレスト間でメール属性が同じ値である場合に、ユーザーと連絡先を結合します。 連絡先が GALSync を使用して作成されている場合に、このオプションを使用してください。 このオプションを選択した場合、メール属性が設定されていないユーザー オブジェクトは、Azure AD との間で同期されません。 |
| [ObjectSID 属性および msExchangeMasterAccountSID/ msRTCSIP-OriginatorSID 属性](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |このオプションでは、アカウント フォレスト内の有効なユーザーと、リソース フォレスト内の無効なユーザーが結合されます。 Exchange では、この構成はリンクされたメールボックスと呼ばれています。 このオプションは、Lync のみを使用し、リソース フォレストに Exchange が存在しない場合に使用できます。 |
| SAMAccountName 属性および MailNickName 属性 |このオプションは、ユーザーのサインイン ID が見つかると予想される属性を結合します。 |
| 特定の属性を選択する |このオプションでは、独自の属性を選択することができます。 このオプションを選択した場合、(選択された) 属性が設定されていないユーザー オブジェクトは、Azure AD との間で同期されません。 **制限:** このオプションでは、既にメタバースに存在する属性のみを使用できます。 |

#### <a name="select-how-users-should-be-identified-by-using-a-source-anchor"></a>ソース アンカーを使用してユーザーの識別方法を選択する
*sourceAnchor* 属性は、ユーザー オブジェクトの有効期間中に変更できない属性です。 オンプレミスのユーザーと Azure AD のユーザーをリンクするプライマリ キーです。

| 設定 | 説明 |
| --- | --- |
| ソース アンカーの管理を Azure に任せる | Azure AD に属性を選択させる場合は、このオプションを選択します。 このオプションを選択すると、Azure AD Connect ウィザードが sourceAnchor 属性選択ロジックを適用します。このロジックについては、「[sourceAnchor としての ms-DS-ConsistencyGuid の使用](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)」セクションで説明されています。 カスタム インストールが完了すると、sourceAnchor 属性として選択された属性が表示されます。 |
| 特定の属性を選択する | sourceAnchor 属性として既存の AD 属性を指定する場合は、このオプションを選択します。 |

SourceAnchor 属性は変更できないため、適切な属性を選択する必要があります。 適切な属性として考えられるのは objectGUID です。 この属性は、ユーザー アカウントをフォレストまたはドメイン間で移動しなければ、変更されません。 ユーザーが結婚したり割り当てが変更されたりした場合に変化する可能性のある属性は、使用しないようにしてください。 

アットマーク (@) を含む属性は使用できないため、電子メールと userPrincipalName は使用できません。 属性では大文字と小文字も区別されるため、フォレスト間でオブジェクトを移動する場合は、大文字と小文字をそのままの状態に維持するようにしてください。 バイナリ属性は Base64 でエンコードされますが、他の種類の属性はエンコードされない状態のままになります。 

フェデレーション シナリオと一部の Azure AD インターフェイスでは、sourceAnchor 属性は *immutableID* とも呼ばれます。 

ソース アンカーの詳細については、[設計概念](plan-connect-design-concepts.md#sourceanchor)に関するページを参照してください。

### <a name="sync-filtering-based-on-groups"></a>グループに基づく同期フィルタリング
グループに基づくフィルタリング機能では、パイロット用にごく一部のオブジェクトのみを同期することができます。 この機能を使用するには、この目的のグループを Active Directory のオンプレミス インスタンスに作成します。 次に、直接のメンバーとして Azure AD に同期するユーザーとグループを追加します。 このグループには後でユーザーを追加したりユーザーを削除したりして、Azure AD に表示するオブジェクトの一覧を管理することができます。 

同期するオブジェクトはすべて、グループの直接のメンバーである必要があります。 ユーザー、グループ、連絡先、およびコンピューターまたはデバイスは、すべて直接のメンバーにする必要があります。 入れ子になったグループのメンバーシップは解決されません。 グループをメンバーとして追加すると、グループ自体のみが追加されます。 そのメンバーは追加されません。

![ユーザーとデバイスをフィルター処理する方法を選択できるページを示すスクリーンショット。](./media/how-to-connect-install-custom/filter2.png)

> [!WARNING]
> この機能は、パイロット デプロイのみのサポートを目的としています。 完全な運用環境のデプロイでは使用しないでください。
>

完全な運用環境のデプロイでは、単一のグループを維持しながらすべてのオブジェクトを同期することは困難になります。 グループに基づくフィルタリング機能の代わりに、「[フィルター処理の構成](how-to-connect-sync-configure-filtering.md)」で説明されているいずれかの方法を使用します。

### <a name="optional-features"></a>オプション機能
次のページで、シナリオのオプション機能を選択できます。

>[!WARNING]
>1\.0.8641.0 とそれ以前のバージョンの Azure AD Connect では、パスワード ライトバックで Azure Access Control Service に依存しています。  このサービスは 2018 年 11 月 7 日に廃止されました。  これらのいずれかのバージョンの Azure AD Connect を使用しており、パスワード ライトバックを有効にしている場合、サービスが廃止されたときに、ユーザーはパスワードを変更またはリセットできなくなる可能性があります。 これらのバージョンの Azure AD Connect では、パスワード ライトバックはサポートされません。
>
>詳細については、「[Azure Access Control Service からの移行](../azuread-dev/active-directory-acs-migration.md)」を参照してください。
>
>パスワード ライトバックを使用する場合は、[Azure AD Connect の最新バージョン](https://www.microsoft.com/download/details.aspx?id=47594)をダウンロードします。

![[オプション機能] ページを示すスクリーンショット。](./media/how-to-connect-install-custom/optional2a.png)

> [!WARNING]
> Azure AD Sync または直接同期 (DirSync) がアクティブな場合は、Azure AD Connect でライトバック機能をアクティブにしないでください。



| オプション機能 | 説明 |
| --- | --- |
| Exchange ハイブリッドのデプロイメント |Exchange ハイブリッドのデプロイ機能を利用すると、オンプレミスと Microsoft 365 の Exchange メールボックスが共存できるようになります。 Azure AD Connect により、Azure AD の特定の[属性](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback)セットがオンプレミスのディレクトリに同期されます。 |
| Exchange メールのパブリック フォルダー | Exchange メールのパブリック フォルダー機能を使用すると、メール対応のパブリック フォルダー オブジェクトを Active Directory のオンプレミス インスタンスから Azure AD に同期することができます。 |
| Azure AD アプリと属性フィルター |Azure AD アプリと属性フィルターを有効にすると、同期される属性セットをカスタマイズできます。 このオプションにより、2 つの構成ページがウィザードに追加されます。 詳細については、「 [Azure AD アプリと属性フィルター](#azure-ad-app-and-attribute-filtering)」を参照してください。 |
| パスワード ハッシュの同期 |サインイン ソリューションとしてフェデレーションを選択した場合は、パスワード ハッシュの同期を有効にすることができます。 その後、バックアップ オプションとして使用できます。  </br></br>パススルー認証を選択した場合、このオプションを有効にして、レガシ クライアントをサポートし、バックアップを提供することもできます。</br></br> 詳細については、[パスワード ハッシュの同期](how-to-connect-password-hash-synchronization.md)に関するページを参照してください。|
| パスワードの書き戻し |このオプションを使用すると、Azure AD で行われたパスワードの変更が、オンプレミスのディレクトリに書き戻されます。 詳細については、「[パスワード管理の概要](../authentication/tutorial-enable-sspr.md)」を参照してください。 |
| グループの書き戻し |Microsoft 365 グループを使用する場合は、Active Directory のオンプレミス インスタンスのグループを表すことができます。 このオプションが使用できるのは、Active Directory のオンプレミス インスタンスに Exchange が存在する場合のみです。 詳細については、「[Azure AD Connect のグループの書き戻し](how-to-connect-group-writeback.md)」を参照してください。|
| デバイスの書き戻し |条件付きアクセスのシナリオの場合は、このオプションを使用して、Azure AD 内のデバイス オブジェクトを Active Directory のオンプレミス インスタンスに書き戻すことができます。 詳細については、[Azure AD Connect でのデバイスの書き戻しの有効化](how-to-connect-device-writeback.md)に関するページを参照してください。 |
| ディレクトリ拡張属性の同期 |指定した属性を Azure AD に同期するには、このオプションを選択します。 詳細については、[ディレクトリ拡張機能](how-to-connect-sync-feature-directory-extensions.md)に関するページを参照してください。 |

### <a name="azure-ad-app-and-attribute-filtering"></a>Azure AD アプリと属性フィルター
Azure AD に同期する属性を制限する場合、まず始めに、使用するサービスを選択します。 このページで選択を変更する場合は、インストール ウィザードを再実行して新しいサービスを明示的に選択する必要があります。

![オプションの Azure AD apps 機能を示すスクリーンショット。](./media/how-to-connect-install-custom/azureadapps2.png)

前の手順で選択したサービスに基づいて、次のページに、同期対象となるすべての属性が表示されます。 この一覧は、同期されるすべてのオブジェクトの種類の組み合わせです。 一部の属性を非同期のままにする必要がある場合、それらの属性の選択を解除できます。

![オプションの Azure AD 属性機能を示すスクリーンショット。](./media/how-to-connect-install-custom/azureadattributes2.png)

> [!WARNING]
> 属性の選択を解除すると、機能に影響が生じる場合があります。 ベスト プラクティスと推奨事項については、[同期する属性](reference-connect-sync-attributes-synchronized.md#attributes-to-synchronize)に関するページを参照してください。
>

### <a name="directory-extension-attribute-sync"></a>ディレクトリ拡張属性の同期
Azure AD のスキーマは、組織によって追加されたカスタム属性や Active Directory 内のその他の属性を使用して拡張することができます。 この機能を使用するには、 **[オプション機能]** ページの **[ディレクトリ拡張属性の同期]** を選択します。 **[ディレクトリ拡張機能]** ページで、同期する属性をさらに選択できます。

>[!NOTE]
>**[使用可能な属性]** フィールドでは大文字と小文字は区別されます。

![[ディレクトリ拡張機能] ページを示すスクリーンショット。](./media/how-to-connect-install-custom/extension2.png)

詳細については、[ディレクトリ拡張機能](how-to-connect-sync-feature-directory-extensions.md)に関するページを参照してください。

### <a name="enabling-single-sign-on"></a>シングル サインオンの有効化
**[シングル サインオン]** ページで、パスワード同期またはパススルー認証で使用するシングル サインオンを構成します。 Azure AD に同期されているフォレストごとに、この手順を一回実行します。 構成には、次の 2 つの手順が含まれます。

1.  Active Directory のオンプレミス インスタンスでの必要なコンピューター アカウントの作成。
2.  シングル サインオンをサポートするクライアント コンピューターのイントラネット ゾーンの構成。

#### <a name="create-the-computer-account-in-active-directory"></a>Active Directory でのコンピューター アカウントの作成
Azure AD Connect で追加されたフォレストごとに、ドメイン管理者の資格情報を入力する必要があります。そうすることで、フォレストごとにコンピューター アカウントを作成できます。 資格情報は、アカウントの作成にのみ使用されます。 他の操作のために保存されたり使用されたりしません。 次の図に示すように、 **[シングル サインオンを有効にする]** ページで資格情報を追加します。

![[シングル サインオンを有効にする] ページを示すスクリーンショット。 フォレストの資格情報が追加されます。](./media/how-to-connect-install-custom/enablesso.png)

>[!NOTE]
>シングル サインオンを使用しないフォレストはスキップできます。

#### <a name="configure-the-intranet-zone-for-client-machines"></a>クライアント コンピューターのイントラネット ゾーンの構成
クライアントがイントラネット ゾーンで自動的にサインインするように、その URL をイントラネット ゾーンに含めるようにします。 この手順を行うことで、ドメインに参加しているコンピューターが、企業ネットワークに接続された際に Kerberos チケットを Azure AD に自動的に送信するようになります。

グループ ポリシー管理ツールがあるコンピューターで次の手順を実行します。

1.  グループ ポリシー管理ツールを開きます。
2.  すべてのユーザーに適用されるグループ ポリシーを編集します。 既定のドメイン ポリシーなどです。
3.  **[ユーザーの構成]**  >  **[管理用テンプレート]**  >  **[Windows コンポーネント]**  >  **[Internet Explorer]**  >  **[インターネット コントロール パネル]**  >  **[セキュリティ ページ]** の順に移動します。 次に **[サイトとゾーンの割り当て一覧]** を選択します。
4.  ポリシーを有効にします。 次に、ダイアログ ボックスに値の名前として `https://autologon.microsoftazuread-sso.com` と、値として `1` を入力します。 設定は次のイメージのようになります。
  
    ![イントラネット ゾーンを示すスクリーンショット。](./media/how-to-connect-install-custom/sitezone.png)

6.  **[OK]** を 2 回選びます。

## <a name="configuring-federation-with-ad-fs"></a>AD FS とのフェデレーションの構成
AD FS の構成は、Azure AD Connect でわずか数クリックで済みます。 開始する前に、次のことを行う必要があります。

* フェデレーション サーバー用の Windows Server 2012 R2 以降。 リモート管理を有効にする必要があります。
* Web アプリケーション プロキシ サーバー用の Windows Server 2012 R2 以降。 リモート管理を有効にする必要があります。
* 使用する予定のフェデレーション サービス名の TLS/SSL 証明書 (sts.contoso.com など)。

>[!NOTE]
>フェデレーション信頼の管理に Azure AD Connect を使っていない場合でも、AD FS ファームの TLS/SSL 証明書は、Azure AD Connect を使って更新することができます。

### <a name="ad-fs-configuration-prerequisites"></a>AD FS の構成の前提条件
Azure AD Connect を使用して AD FS ファームを構成するには、リモート サーバー上で WinRM が有効になっている必要があります。 [フェデレーションの前提条件](how-to-connect-install-prerequisites.md#prerequisites-for-federation-installation-and-configuration)の他のタスクを完了したことを確認します。 また、[Azure AD Connect とフェデレーション/WAP サーバー](reference-connect-ports.md#table-3---azure-ad-connect-and-ad-fs-federation-serverswap)の表に記載されているポートの要件に従っていることを確認します。

### <a name="create-a-new-ad-fs-farm-or-use-an-existing-ad-fs-farm"></a>新しい AD FS ファームの作成または既存の AD FS ファームの使用
既存の AD FS ファームを使用するか、新しく作成することができます。 新しく作成する場合は、TLS/SSL 証明書を提供する必要があります。 TLS/SSL 証明書がパスワードで保護されている場合は、パスワードを入力するように求められます。

![[AD FS ファーム] ページを示すスクリーンショット](./media/how-to-connect-install-custom/adfs1.png)

既存の AD FS ファームを使用する場合は、AD FS と Azure AD の間の信頼関係を構成する画面を表示します。

>[!NOTE]
>Azure AD Connect を使って管理できる AD FS ファームは 1 つだけです。 Azure AD が選択された AD FS ファーム上に構成されている、既存のフェデレーション信頼がある場合、その信頼は Azure AD Connect によって最初から再作成されます。

### <a name="specify-the-ad-fs-servers"></a>AD FS サーバーの指定
AD FS をインストールするサーバーをして指定します。 容量ニーズに基づいて 1 つまたは複数のサーバーを追加することができます。 この構成を設定する前に、すべての AD FS サーバーを Active Directory に参加させてください。 Web アプリケーション プロキシ サーバーでは、この手順は必要ありません。 

テスト デプロイとパイロット デプロイ用に単一の AD FS サーバーをインストールすることをお勧めします。 初期構成の後に Azure AD Connect を再度実行し、容量拡大のニーズを満たすようにサーバーをさらに追加してデプロイします。

> [!NOTE]
> この構成を設定する前に、すべてのサーバーが 1 つの Azure AD ドメインに参加していることを確認してください。
>


![[フェデレーション サーバー] ページを示すスクリーンショット。](./media/how-to-connect-install-custom/adfs2.png)

### <a name="specify-the-web-application-proxy-servers"></a>Web アプリケーション プロキシ サーバーの指定
Web アプリケーション プロキシ サーバーを指定します。 Web アプリケーション プロキシ サーバーは、エクストラネットに接続している境界ネットワークにデプロイされます。 エクストラネットからの認証要求をサポートします。 容量ニーズに基づいて 1 つまたは複数のサーバーを追加することができます。 

テストとパイロットのデプロイ用に単一の Web アプリケーション プロキシ サーバーをインストールすることをお勧めします。 初期構成の後に Azure AD Connect を再度実行し、容量拡大のニーズを満たすようにサーバーをさらに追加してデプロイします。 イントラネットからの認証を処理するために同数のプロキシ サーバーを設定することをお勧めします。

> [!NOTE]
> - 使用しているアカウントが Web アプリケーション プロキシ サーバーのローカル管理者ではない場合、管理者の資格情報を入力するように求められます。
> - Web アプリケーション プロキシ サーバーを指定する前に、Azure AD Connect サーバーと Web アプリケーション プロキシ サーバーの間に HTTP/HTTPS 接続が確立されていることを確認してください。
> - 認証要求の通信ができるように、Web アプリケーション サーバーと AD FS サーバーの間に HTTP/HTTPS 接続が確立されていることを確認してください。
>


![[Web アプリケーション プロキシ サーバー] ページを示すスクリーンショット。](./media/how-to-connect-install-custom/adfs3.png)

Web アプリケーション サーバーが AD FS サーバーとの間にセキュリティで保護された接続を確立できるように、資格情報を入力するよう求められます。 これらの資格情報は、AD FS サーバーのローカル管理者アカウントのものである必要があります。

![[資格情報] ページを示すスクリーンショット。 管理者の資格情報は、[ユーザー名] フィールドと [パスワード] フィールドに入力します。](./media/how-to-connect-install-custom/adfs4.png)

### <a name="specify-the-service-account-for-the-ad-fs-service"></a>AD FS サービスのサービス アカウントを指定します。
AD FS サービスには、ユーザーを認証し Active Directory のユーザー情報を参照するドメイン サービス アカウントが必要です。 次の 2 種類のサービス アカウントがサポートされます。

* **グループ管理サービス アカウント**: このアカウントの種類は、Windows Server 2012 によって AD DS に導入されました。 この種類のアカウントは、AD FS などのサービスを提供します。 1 つのアカウントであり、パスワードを定期的に更新する必要はありません。 AD FS サーバーが所属するドメインに Windows Server 2012 ドメイン コントローラーが既にある場合は、このオプションを使用してください。
* **ドメイン ユーザー アカウント**: この種類のアカウントでは、パスワードを入力し、期限が切れたときに定期的に更新する必要があります。 このオプションは、AD FS サーバーが所属するドメインに Windows Server 2012 ドメイン コントローラーがない場合にのみ使用してください。

**グループ管理サービス アカウントの使用** を選択し、Active Directory でこの機能を使用したことがない場合、エンタープライズ管理者の資格情報を入力します。 入力した資格情報は、キー ストアを開始し、Active Directory でこの機能を有効にするために使用されます。

> [!NOTE]
> Azure AD Connect は、AD FS サービスがサービス プリンシパル名 (SPN) としてドメインに登録済みであるかどうかをチェックします。  Azure AD DS では、重複する SPN を同時に登録することはできません。  重複する SPN が見つかった場合、その SPN を削除するまで先に進むことができません。

![[AD FS サービス アカウント] ページを示すスクリーンショット。](./media/how-to-connect-install-custom/adfs5.png)

### <a name="select-the-azure-ad-domain-that-you-want-to-federate"></a>フェデレーションする Azure AD ドメインの選択
**[Azure AD ドメイン]** ページを使用して、AD FS と Azure AD のフェデレーション関係をセットアップします。 ここでは、Azure AD にセキュリティ トークンを提供するように AD FS を構成します。 また、この AD FS インスタンスからのトークンを信頼するように Azure AD を構成します。 

このページでは、初期インストールで 1 つのドメインしか構成できません。 後で Azure AD Connect をもう一度実行すると、追加のドメインを構成できます。

![[Azure AD ドメイン] ページを示すスクリーンショット。](./media/how-to-connect-install-custom/adfs6.png)

### <a name="verify-the-azure-ad-domain-selected-for-federation"></a>フェデレーション用に選択された Azure AD ドメインの検証
フェデレーションするドメインを選択すると、Azure AD Connect によって、検証されていないドメインを検証するために使用できる情報が提供されます。 詳細については、[ドメインの追加と検証](../fundamentals/add-custom-domain.md)に関するページを参照してください。

![ドメインの検証に使用できる情報を含む [Azure AD ドメイン] ページを示すスクリーンショット。](./media/how-to-connect-install-custom/verifyfeddomain.png)

> [!NOTE]
> Azure AD Connect では、構成段階でドメインの検証を試みます。 必要なドメイン ネーム システム (DNS) レコードを追加しない場合、構成を完了できません。
>


## <a name="configuring-federation-with-pingfederate"></a>PingFederate とのフェデレーションの構成
PingFederate の構成は、Azure AD Connect でわずか数クリックで済みます。 以下の前提条件が必要です。
- PingFederate 8.4 以降。  詳細については、[Azure Active Directory および Microsoft 365 と PingFederate の統合](https://docs.pingidentity.com/bundle/O365IG20_sm_integrationGuide/page/O365IG_c_integrationGuide.html)に関するページをご覧ください。
- 使用する予定のフェデレーション サービス名の TLS/SSL 証明書 (sts.contoso.com など)。

### <a name="verify-the-domain"></a>ドメインの検証
PingFederate を使用してフェデレーションを設定することを選択すると、フェデレーションするドメインを検証するように求められます。  ドロップダウン メニューでドメインを選択します。

![[Azure AD ドメイン] ページを示すスクリーンショット。 サンプル ドメイン "contoso.com" が選択されています。](./media/how-to-connect-install-custom/ping1.png)

### <a name="export-the-pingfederate-settings"></a>PingFederate 設定のエクスポート


各フェデレーション Azure ドメインのフェデレーション サーバーとして、PingFederate を構成します。  **[設定のエクスポート]** を選択し、この情報を PingFederate 管理者と共有します。  フェデレーション サーバーの管理者は、構成を更新し、PingFederate サーバーの URL とポート番号を指定して、Azure AD Connect がメタデータ設定を検証できるようにします。  

![[PingFederate の設定] ページを示すスクリーンショット。 ページの上部付近に [設定のエクスポート] ボタンが表示されます。](./media/how-to-connect-install-custom/ping2.png)

検証の問題を解決するには、PingFederate 管理者に問い合わせてください。  次の図は、Azure との有効な信頼関係がない PingFederate サーバーに関する情報を示しています。

![サーバー情報を示すスクリーンショット:PingFederate サーバーが見つかりましたが、Azure のサービス プロバイダー接続が見つからないか、無効になっています。](./media/how-to-connect-install-custom/ping5.png)




### <a name="verify-federation-connectivity"></a>フェデレーションの接続性の検証
Azure AD Connect は、前の手順で PingFederate メタデータから取得した認証エンドポイントの検証を試みます。  まず、Azure AD Connect は、ローカル DNS サーバーを使用してエンドポイントを解決しようとします。  次に、外部 DNS プロバイダーを使用してエンドポイントを解決しようとします。  検証の問題を解決するには、PingFederate 管理者に問い合わせてください。  

![[接続性の検証] ページを示すスクリーンショット。](./media/how-to-connect-install-custom/ping3.png)

### <a name="verify-federation-sign-in"></a>フェデレーション サインインの検証
最後に、フェデレーション ドメインにサインインして、新しく構成されたフェデレーション ログイン フローを検証できます。 サインインが成功した場合、PingFederate とのフェデレーションは正常に構成されています。

![[フェデレーション ログインの検証] ページを示すスクリーンショット。 下部にメッセージが表示された場合は、サインインに成功したことを示します。](./media/how-to-connect-install-custom/ping4.png)

## <a name="configure-and-verify-pages"></a>ページの構成および確認
構成は **[構成]** ページで行われます。

> [!NOTE]
> フェデレーションを構成している場合は、インストールを続行する前に、[フェデレーション サーバーの名前解決](how-to-connect-install-prerequisites.md#name-resolution-for-federation-servers)も構成済みであることを確認してください。
>



![[構成の準備完了] ページを示すスクリーンショット。](./media/how-to-connect-install-custom/readytoconfigure2.png)

### <a name="use-staging-mode"></a>ステージング モードの使用
ステージング モードと並行して新しい同期サーバーをセットアップすることができます。 このセットアップを使用する場合、クラウド内の 1 つのディレクトリにエクスポートできる同期サーバーは 1 つだけです。 ただし、別のサーバー (DirSync を実行するサーバーなど) から移動する場合は、ステージング モードで Azure AD Connect を有効にすることができます。 

ステージング設定を有効にすると、同期エンジンはデータを通常どおりにインポートおよび同期します。 ただし、Azure AD または Active Directory にデータをエクスポートすることはありません。 ステージング モード中は、パスワード同期機能とパスワード ライトバック機能が無効になります。

![[ステージングモードを有効にする] オプションを示すスクリーンショット。](./media/how-to-connect-install-custom/stagingmode.png)

ステージング モード中は、同期エンジンに必要な変更を加え、エクスポートされる内容を確認することができます。 構成が適切な状態になったら、インストール ウィザードをもう一度実行し、ステージング モードを無効にします。 

これで、データはこのサーバーから Azure AD にエクスポートされます。 1 つのサーバーのみをアクティブにしてエクスポートするために、このとき他のサーバーは無効にしてください。

詳細については、「[ステージング モード](how-to-connect-sync-staging-server.md)」を参照してください。

### <a name="verify-your-federation-configuration"></a>フェデレーション構成の確認
**[検証]** ボタンを選択すると、Azure AD Connect によって DNS 設定が検証されます。 以下の設定が検査されます。

* **イントラネット接続**
    * フェデレーション FQDN の解決: 接続を確保するために、DNS でフェデレーション FQDN を解決できるかどうかが Azure AD Connect によって検査されます。 Azure AD Connect が FQDN を解決できない場合、検証は失敗します。 検証を実行するために、フェデレーション サービス FQDN の DNS レコードを設定してください。
    * DNS A レコード: フェデレーション サービスに A レコードがあるかどうかが Azure AD Connect によって検査されます。 A レコードがない場合、検証は失敗します。 検証を実行するために、フェデレーション FQDN の A レコードを作成してください (CNAME レコードではありません)。
* **エクストラネット接続**
    * フェデレーション FQDN の解決: 接続を確保するために、DNS でフェデレーション FQDN を解決できるかどうかが Azure AD Connect によって検査されます。

      ![[インストールの完了] ページを示すスクリーンショット。](./media/how-to-connect-install-custom/completed.png)

      ![[インストールの完了] ページを示すスクリーンショット。 イントラネットの構成が検証されたことを示すメッセージが表示されます。](./media/how-to-connect-install-custom/adfs7.png)

エンド ツー エンド認証を検証するには、以下の 1 つ以上のテストを手動で実行します。

* 同期が完了したら、Azure AD Connect で **[フェデレーション ログインの検証]** 追加タスクを使用して、選択したオンプレミス ユーザー アカウントの認証を検証します。
* イントラネット上のドメイン参加済みマシンで、ブラウザーからサインインできることを確認します。 https://myapps.microsoft.comに接続します。 次に、ログオン アカウントを使用してサインインを確認します。 あらかじめ登録された AD DS 管理者アカウントは同期されないため、検証には使用できません。
* エクストラネット上のデバイスからサインインできることを確認します。 自宅にあるマシンまたはモバイル デバイスで https://myapps.microsoft.com に接続します。 次に、資格情報を入力します。
* リッチ クライアントのサインインを検証する。 https://testconnectivity.microsoft.comに接続します。 次に、 **[Office 365]**  >  **[Office 365 シングル サインオン テスト]** の順に選択します。

## <a name="troubleshoot"></a>トラブルシューティング
このセクションには、Azure AD Connect のインストール時に問題が発生した場合に使用できるトラブルシューティング情報が含まれています。

Azure AD Connect のインストールをカスタマイズする場合は、 **[必要なコンポーネントのインストール]** ページで、 **[既存の SQL Server を使用する]** を選択できます。 次のエラーが表示されることがあります。"ADSync データベースには既にデータが含まれていて、上書きすることはできません。 既存のデータベースを削除してからやり直してください。"

![[必須コンポーネントのインストール] ページを示すスクリーンショット。 ページの下部にエラーが表示されます。](./media/how-to-connect-install-custom/error1.png)

このエラーが表示されるのは、指定した SQL Server の SQL インスタンスに *ADSync* という名前のデータベースが既に存在するためです。

このエラーは通常、Azure AD Connect をアンインストールした後で発生します。  Azure AD Connect をアンインストールすると、SQL Server を実行しているコンピューターからデータベースが削除されません。

この問題の解決方法

1. アンインストールする前に、Azure AD Connect が使用している ADSync データベースを確認してください。 データベースが使用されなくなっていることを確認します。

2. データベースをバックアップします。

3. データベースを削除します。
    1. **Microsoft SQL Server Management Studio** を使用して、SQL インスタンスに接続します。 
    1. **ADSync** データベースを見つけて右クリックします。
    1. コンテキスト メニューで、 **[削除]** を選択します。
    1. **[OK]** を選択して、データベースを削除します。

![Microsoft SQL Server Management Studio を示すスクリーンショット。 ADSync が選択されています。](./media/how-to-connect-install-custom/error2.png)

ADSync データベースの削除後、 **[インストール]** を選択してインストールを再試行します。

## <a name="next-steps"></a>次の手順
インストールが完了したら、Windows からサインアウトします。 次に、Synchronization Service Manager または同期規則エディターを使用する前に、もう一度サインインします。

Azure AD Connect がインストールされたので、[インストールを確認し、ライセンスを割り当てる](how-to-connect-post-installation.md)ことができます。

インストール中に有効にした機能の詳細については、[誤った削除操作を防止する機能](how-to-connect-sync-feature-prevent-accidental-deletes.md)と [Azure AD Connect Health](how-to-connect-health-sync.md) に関する各ページを参照してください。

その他の一般的なトピックの詳細については、「[Azure AD Connect 同期: スケジューラ](how-to-connect-sync-feature-scheduler.md)」および「[オンプレミス ID と Azure AD の統合](whatis-hybrid-identity.md)」を参照してください。
