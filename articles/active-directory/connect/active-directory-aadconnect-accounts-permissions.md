---
title: "Azure AD Connect: アカウントとアクセス許可 | Microsoft Docs"
description: "このトピックでは、使用および作成されるアカウントと、必要なアクセス許可について説明します。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: b93e595b-354a-479d-85ec-a95553dd9cc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 68e475891a91e4ae45a467cbda2b7b51c8020dbd
ms.openlocfilehash: e5f643d444fb2bf00aa91083f5d09962372e0dbb


---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: アカウントとアクセス許可
Azure AD Connect インストール ウィザードには次の 2 つの別の設定からアクセスできます。

* 簡単設定: ウィザードにより多くの権限が必要になりますが、個別にユーザーを作成したりアクセス許可を構成しなくても構成を簡単にセットアップできます。
* カスタム設定: ウィザードで使用できる選択肢とオプションは増えますが、状況によってはあなた自身が適切なアクセス許可を持っていることを確認する必要があります。

## <a name="related-documentation"></a>関連ドキュメント
「[オンプレミス ID と Azure Active Directory の統合](../active-directory-aadconnect.md)」をまだお読みでない方のために、次の表に関連トピックへのリンクを示します。

|トピック |リンク|  
| --- | --- |
|Azure AD Connect のダウンロード | [Azure AD Connect のダウンロード](http://go.microsoft.com/fwlink/?LinkId=615771)|
|Express 設定を使用したインストール | [Azure AD Connect の高速インストール](./active-directory-aadconnect-get-started-express.md)|
|カスタマイズした設定を使用したインストール | [Azure AD Connect のカスタム インストール](./active-directory-aadconnect-get-started-custom.md)|
|DirSync からのアップグレード | [Azure AD 同期ツール (DirSync) からのアップグレード](./active-directory-aadconnect-dirsync-upgrade-get-started.md)|
|インストール後に | [インストールの確認とライセンスの割り当て ](active-directory-aadconnect-whats-next.md)|

## <a name="express-settings-installation"></a>簡単設定を使用したインストール
簡単設定では、オンプレミスの Active Directory を Azure AD Connect に必要なアクセス許可で構成できるように、インストール ウィザードで AD DS エンタープライズ管理者の資格情報が求められます。 DirSync からアップグレードする場合は、AD DS エンタープライズ管理者の資格情報を使用して、DirSync で使用されるアカウントのパスワードをリセットします。 Azure AD グローバル管理者の資格情報も必要になります。

| ウィザード ページ | 収集される資格情報 | 必要なアクセス許可 | 用途 |
| --- | --- | --- | --- |
| 該当なし |インストール ウィザードを実行するユーザー |ローカル サーバーの管理者 |<li>[同期エンジン サービス アカウント](#azure-ad-connect-sync-service-account)として使用するローカル アカウントを作成します。 |
| Azure への接続 |Azure AD ディレクトリの資格情報 |Azure AD でのグローバル管理者ロール |<li>Azure AD ディレクトリの同期を有効にします。</li>  <li>Azure AD で進行中の同期操作に使用する [Azure AD アカウント](#azure-ad-service-account)を作成します。</li> |
| AD DS に接続 |オンプレミスの Active Directory の資格情報 |Active Directory 内の Enterprise Admins (EA) グループのメンバー |<li>Active Directory で[アカウント](#active-directory-account)を作成し、それに対するアクセス許可を付与します。 この作成されたアカウントは、同期中にディレクトリ情報を読み取るまたは書き込むために使用されます。</li> |

### <a name="enterprise-admin-credentials"></a>エンタープライズ管理者の資格情報
これらの資格情報を使用するのはインストール中のみであり、インストールの完了後には使用しません。 ドメイン管理者ではなく、エンタープライズ管理者が Active Directory のアクセス許可をすべてのドメインで設定できることを確認します。

### <a name="global-admin-credentials"></a>グローバル管理者の資格情報
これらの資格情報を使用するのはインストール中のみであり、インストールの完了後には使用しません。 この資格情報は、Azure AD への変更の同期に使用される [Azure AD アカウント](#azure-ad-service-account) を作成するために使用します。 また、このアカウントにより、同期が Azure AD の機能として有効化されます。

### <a name="permissions-for-the-created-ad-ds-account-for-express-settings"></a>簡単設定用に作成された AD DS アカウントのアクセス許可
AD DS の読み取りと書き込み用に作成された [アカウント](#active-directory-account) は、簡単設定によって作成された場合、以下のアクセス許可を持ちます。

| アクセス許可 | 使用対象 |
| --- | --- |
| <li>ディレクトリの変更のレプリケート</li><li>ディレクトリの変更をすべてにレプリケート |パスワードの同期 |
| すべてのプロパティの読み取り/書き込み (ユーザー) |インポートおよび Exchange ハイブリッド |
| すべてのプロパティの読み取り/書き込み (iNetOrgPerson) |インポートおよび Exchange ハイブリッド |
| すべてのプロパティの読み取り/書き込み (グループ) |インポートおよび Exchange ハイブリッド |
| すべてのプロパティの読み取り/書き込み (連絡先) |インポートおよび Exchange ハイブリッド |
| パスワードのリセット |パスワード ライトバックを有効にするための準備 |

## <a name="custom-settings-installation"></a>カスタム設定を使用したインストール
カスタム設定を使用する場合、インストール前に Active Directory への接続に使用するアカウントを作成する必要があります。 このアカウントに付与する必要があるアクセス許可については、「 [AD DS アカウントの作成](#create-the-ad-ds-account)」を参照してください。

| ウィザード ページ | 収集される資格情報 | 必要なアクセス許可 | 用途 |
| --- | --- | --- | --- |
| 該当なし |インストール ウィザードを実行するユーザー |<li>ローカル サーバーの管理者</li><li>完全な SQL Server を使用する場合、ユーザーは SQL のシステム管理者 (SA) である必要があります</li> |既定では、 [同期エンジン サービス アカウント](#azure-ad-connect-sync-service-account)として使用するローカル アカウントを作成します。 このアカウントは、管理者が特定のアカウントを指定しなかった場合のみ作成します。 |
| 同期サービスのインストール、サービス アカウントのオプション |AD またはローカル ユーザー アカウントの資格情報 |ユーザー。アクセス許可は、インストール ウィザードにより付与されます。 |管理者がアカウントを指定している場合は、このアカウントは、同期サービスのサービス アカウントとして使用します。 |
| Azure への接続 |Azure AD ディレクトリの資格情報 |Azure AD でのグローバル管理者ロール |<li>Azure AD ディレクトリの同期を有効にします。</li>  <li>Azure AD で進行中の同期操作に使用する [Azure AD アカウント](#azure-ad-service-account)を作成します。</li> |
| ディレクトリの接続 |Azure AD に接続する各フォレストのオンプレミス Active Directory の資格情報 |アクセス許可はどの機能を有効にするかによって異なります。詳細については、「[AD DS アカウントの作成](#create-the-ad-ds-account)」を参照してください。 |このアカウントは、同期中にディレクトリ情報を読み取るまたは書き込むために使用されます。 |
| AD FS サーバー |ウィザードを実行しているユーザーのログオン資格情報では接続できない場合に、リスト内の各サーバーについてウィザードが資格情報を収集します。 |ドメイン管理者 |AD FS サーバー ロールをインストールして構成します。 |
| Web アプリケーション プロキシ サーバー |ウィザードを実行しているユーザーのログオン資格情報では接続できない場合に、リスト内の各サーバーについてウィザードが資格情報を収集します。 |ターゲット コンピューターのローカル管理者 |WAP サーバー ロールをインストールして構成します。 |
| プロキシ信頼資格情報 |フェデレーション サービスの信頼資格情報 (FS からの信頼証明書の登録にプロキシが使用する資格情報) |ドメイン アカウント (AD FS サーバーのローカル管理者) |FS-WAP 信頼証明書の初回登録 |
| AD FS サービス アカウントのページ、[ドメイン ユーザー アカウントの使用オプション] |AD ユーザー アカウントの資格情報 |ドメイン ユーザー |資格情報を指定した AD ユーザー アカウントは、AD FS サービスのログオン アカウントとして使用します。 |

### <a name="create-the-ad-ds-account"></a>AD DS アカウントの作成
Azure AD Connect をインストールした場合、**[ディレクトリの接続]** ページで指定したアカウントが Active Directory にあり、必要なアクセス許可が付与されている必要があります。 インストール ウィザードではアクセス許可は検証されません。問題があれば、同期中に検出されます。

必要なアクセス許可は、有効にしたオプションの機能によって異なります。 複数のドメインがある場合は、フォレスト内のすべてのドメインにアクセス許可を付与する必要があります。 これらのいずれの機能も有効にしなかった場合、既定の **ドメイン ユーザー** アクセス許可で十分対応できます。

| 機能 | アクセス許可 |
| --- | --- |
| パスワードの同期 |<li>ディレクトリの変更のレプリケート</li>  <li>ディレクトリの変更をすべてにレプリケート |
| Exchange ハイブリッドのデプロイメント |ユーザー、グループ、連絡先用の「[Exchange ハイブリッドの書き戻し](active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback)」に記載された属性への書き込みアクセス許可。 |
| パスワードの書き戻し |ユーザー向けの「[パスワード管理の概要](../active-directory-passwords-getting-started.md#step-4-set-up-the-appropriate-active-directory-permissions)」に記載された属性への書き込みアクセス許可。 |
| デバイスの書き戻し |「[デバイスの書き戻し](active-directory-aadconnect-feature-device-writeback.md)」に説明されているように、PowerShell スクリプトを使用して付与されたアクセス許可。 |
| グループの書き戻し |配布グループが配置されている OU 内のグループ オブジェクトの読み取り、作成、更新、および削除。 |

## <a name="upgrade"></a>アップグレード
Azure AD Connect のいずれかのバージョンから新しいリリースにアップグレードする場合、次のアクセス許可が必要です。

| プリンシパル | 必要なアクセス許可 | 用途 |
| --- | --- | --- |
| インストール ウィザードを実行するユーザー |ローカル サーバーの管理者 |バイナリを更新します。 |
| インストール ウィザードを実行するユーザー |ADSyncAdmins のメンバー |同期規則などの構成を変更します。 |
| インストール ウィザードを実行するユーザー |完全バージョンの SQL Server を使用している場合: 同期エンジン データベースの DBO (または類似のもの) |新しい列でテーブルを更新するなど、データベース レベルを変更します。 |

## <a name="more-about-the-created-accounts"></a>作成されたアカウントの詳細情報
### <a name="active-directory-account"></a>Active Directory アカウント
簡単設定を使用した場合、アカウントは、同期に使用される Active Directory に作成されます。 作成されたアカウントはユーザー コンテナーのフォレスト ルート ドメインに配置され、名前の先頭に **MSOL_** が付けられます。 アカウントの作成時には、有効期限のない長く複雑なパスワードが設定されます。 ドメインにパスワード ポリシーがある場合は、このアカウントに対して長い複雑なパスワードが許可されることを確認してください。

![AD アカウント](./media/active-directory-aadconnect-accounts-permissions/adsyncserviceaccount.png)

### <a name="azure-ad-connect-sync-service-accounts"></a>Azure AD Connect 同期サービス アカウント
ローカル サービス アカウントはインストール ウィザードで作成します (カスタム設定で使用するアカウントを指定しない限り)。 アカウントは、先頭に **AAD_** が付き、実際の同期サービスがそのアカウントで実行されます。 Azure AD Connect をドメイン コントローラーにインストールした場合、アカウントはドメインに作成されます。 SQL Server を実行しているリモート サーバーを使用するか、認証を必要とするプロキシを使用する場合は、**AAD_** サービス アカウントをドメイン内に配置する必要があります。

![同期サービス アカウント](./media/active-directory-aadconnect-accounts-permissions/syncserviceaccount.png)

アカウントの作成時には、有効期限のない長く複雑なパスワードが設定されます。

このアカウントを使用して、安全な方法で他のアカウントのパスワードを保存します。 これらの他のアカウントのパスワードは暗号化されて、データベースに格納されます。 暗号化キーの秘密キーは、Windows データ保護 API (DPAPI) を使用する暗号化サービスの秘密キー暗号化によって保護されています。 Windows は、セキュリティ上の理由から暗号化キーを破棄するため、サービス アカウントのパスワードをリセットする必要はありません。

完全バージョンの SQL Server を使用した場合、サービス アカウントは、同期エンジン用に作成されたデータベースの DBO になります。 サービスは、他のアクセス許可では意図したように機能しません。 SQL ログインも作成されます。

アカウントには、ファイル、レジストリ キー、また同期エンジンに関連するその他のオブジェクトへのアクセス許可も付与されます。

### <a name="azure-ad-service-account"></a>Azure AD サービス アカウント
Azure AD のアカウントは、同期サービスで使用するために作成されます。 このアカウントは、その表示名で識別できます。

![AD アカウント](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccount.png)

アカウントが使用されるサーバーの名前は、ユーザー名の 2 番目の部分で識別できます。 図では、サーバー名は FABRIKAMCON です。 ステージング サーバーがある場合、各サーバーに独自のアカウントが指定されます。 Azure AD での同期サービスのアカウント数の上限は 10 です。

サービス アカウントの作成時には、有効期限のない長く複雑なパスワードが設定されます。 このアカウントには、ディレクトリ同期タスクの実行権限のみを持つ特別なロール **ディレクトリ同期アカウント** が付与されます。 この特別な組み込みのロールは Azure AD Connect ウィザード以外では付与できず、Azure Portal では、このアカウントは **[ユーザー]**ロールを持つものとして表示されます。

![AD アカウント ロール](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccountrole.png)

## <a name="next-steps"></a>次のステップ
「 [オンプレミス ID と Azure Active Directory の統合](../active-directory-aadconnect.md)」をご覧ください。




<!--HONumber=Dec16_HO3-->


