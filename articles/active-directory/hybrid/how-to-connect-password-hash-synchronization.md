---
title: Azure AD Connect 同期でパスワード ハッシュ同期を実装する | Microsoft Docs
description: パスワード ハッシュ同期のしくみとその設定方法について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47d7d541ed7d9805641ffdfde381d482c8700006
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96858741"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Azure AD Connect 同期を使用したパスワード ハッシュ同期の実装
この記事では、オンプレミスの Active Directory インスタンスから、クラウドベースの Azure Active Directory (Azure AD) インスタンスへの、ユーザー パスワードの同期に必要な情報を提供します。

## <a name="how-password-hash-synchronization-works"></a>パスワード ハッシュ同期のしくみ
Active Directory ドメイン サービスは、実際のユーザー パスワードをハッシュ値表現の形式で格納します。 ハッシュ値は、一方向の数学関数 ("*ハッシュ アルゴリズム*") の結果として求められます。 一方向の関数の結果をパスワードのプレーンテキスト バージョンに戻す方法はありません。 

パスワードを同期するには、Azure AD Connect 同期でオンプレミスの Active Directory インスタンスからパスワード ハッシュを抽出します。 Azure Active Directory 認証サービスに同期される前に、パスワード ハッシュには、追加のセキュリティ処理が行われます。 パスワードは、ユーザーごとに、時間順に同期されます。

パスワード ハッシュ同期処理の実際のデータ フローは、ユーザー データの同期と似ています。 ただし、パスワードは、他の属性に対する標準のディレクトリ同期ウィンドウよりも頻繁に同期されます。 パスワード ハッシュ同期プロセスは 2 分間隔で実行されます。 このプロセスの頻度を変更することはできません。 パスワードを同期すると、既存のクラウド パスワードが上書きされます。

パスワード ハッシュ同期機能を初めて有効にすると、スコープ内のすべてのユーザーの初回同期が実行されます。 同期するユーザー パスワードのサブセットを明示的に定義することはできません。 ただし、複数のコネクタが存在する場合、[Set-ADSyncAADPasswordSyncConfiguration](../../active-directory-domain-services/tutorial-configure-password-hash-sync.md) コマンドレットを使用してパスワード ハッシュ同期を無効にできるコネクタとできないコネクタがあります。

オンプレミス パスワードを変更すると、更新されたパスワードは、多くの場合、ほんの数分で同期されます。
同期の試行に失敗すると、パスワード ハッシュ同期機能により自動的に再試行が行われます。 パスワード同期の試行中にエラーが発生した場合、イベント ビューアーにエラーが記録されます。

パスワードの同期によって、現在サインイン中の Azure ユーザーが影響を受けることはありません。
クラウド サービスにサインインしている間、パスワード変更が同期された場合、現在のクラウド サービス セッションがその影響をすぐに受けることはありません。 しかしながら、クラウド サービスで認証が再び要求された場合は、新しいパスワードを指定する必要があります。

ユーザーは、企業ネットワークにサインインしているかどうかに関係なく、Azure AD の認証のために、企業の資格情報をもう一度入力する必要があります。 ただし、ユーザーがサインイン時に [サインインしたままにする] (KMSI) チェックボックスをオンにすると、このパターンを最小限に抑えることができます。 このチェックボックスをオンにすると、180 日間認証をバイパスするセッション Cookie が設定されます。 KMSI の動作は、Azure AD 管理者が有効または無効にすることができます。 さらに、ユーザーが企業ネットワークに接続される会社のデバイスを使用するときに、自動的にサインインを行う[シームレス SSO](how-to-connect-sso.md) をオンにすることで、パスワード プロンプトを削減できます。

> [!NOTE]
> パスワード同期は、Active Directory でオブジェクトの種類がユーザーであるオブジェクトのみがサポートされます。 オブジェクトの種類が INetOrgPerson であるオブジェクトはサポートされません。

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>パスワード ハッシュ同期のしくみの詳しい説明

次のセクションでは、Active Directory と Azure AD の間のパスワード ハッシュ同期のしくみを詳しく説明します。

![パスワードの詳細なフロー](./media/how-to-connect-password-hash-synchronization/arch3b.png)

1. AD Connect サーバー上のパスワード ハッシュ同期エージェントは、保存されたパスワード ハッシュ (unicodePwd 属性) を 2 分ごとに DC に要求します。  この要求は、DC 間でデータを同期するために使用される標準の [MS-DRSR](/openspecs/windows_protocols/ms-drsr/f977faaa-673e-4f66-b9bf-48c640241d47) レプリケーション プロトコルを介して行われます。 サービス アカウントには、パスワード ハッシュを取得するために、Replicate Directory Changes (ディレクトリの変更のレプリケート) と Replicate Directory Changes All AD (ディレクトリの変更をすべての AD にレプリケート) の権限が必要になります (インストール時に既定で付与されます)。
2. DC は、送信する前に、RPC セッション キーの [MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) ハッシュであるキーと salt を使用して MD4 パスワード ハッシュを暗号化します。 次に、この結果を RPC 経由でパスワード ハッシュ同期エージェントに送信します。 DC は、DC のレプリケーション プロトコルを使用して同期エージェントにも salt を渡すので、エージェントはエンベロープの暗号化を解除できます。
3. パスワード ハッシュ同期エージェントは、暗号化されたエンベロープを受け取ると、[MD5CryptoServiceProvider](/dotnet/api/system.security.cryptography.md5cryptoserviceprovider) と salt を使用して、受信したデータの暗号化を解除し元の MD4 形式に戻すためのキーを生成します。 パスワード ハッシュ同期エージェントが、クリア テキストのパスワードにアクセスすることはありません。 パスワード ハッシュ同期エージェントによる MD5 の使用は、DC とレプリケーション プロトコルとの互換性の維持に限定されており、DC とパスワード ハッシュ同期エージェントの間でオンプレミスでのみ使用されます。
4. パスワード ハッシュ同期エージェントは、最初にハッシュを 32 バイトの 16 進数文字列に変換し、次にこの文字列を UTF-16 エンコーディングでバイナリに変換することで、16 バイトのバイナリ パスワード ハッシュを 64 バイトに拡張します。
5. パスワード ハッシュ同期エージェントは、10 バイト長の salt で構成されるユーザーごとの salt を、64 バイトの バイナリに追加し、 元のハッシュの保護を強化します。
6. それから、パスワード ハッシュ同期エージェントは、MD4 ハッシュとユーザーごとのsalt を結合し、それを [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) 関数に入力します。 [HMAC-SHA256](/dotnet/api/system.security.cryptography.hmacsha256) キー付きハッシュ アルゴリズムの 1000 のイテレーションが使用されます。 
7. パスワードハッシュ同期エージェントは、返された 32 バイトのハッシュを受け取り、(Azure AD で使用するために) ユーザーごとの salt と SHA256 のイテレーションの数の両方を連結し、SSL 経由で Azure AD Connect から Azure AD にこの文字列を送信します。</br> 
8. ユーザーが Azure AD にサインインしようとして自分のパスワードを入力すると、パスワードは同じ MD4 + salt + PBKDF2 + HMAC - SHA256 のプロセスを通じて処理されます。 返されたハッシュが Azure AD に格納されたハッシュに一致する場合、ユーザーは正しいパスワードを入力しており、認証も済んでいます。

> [!NOTE]
> 元の MD4 ハッシュは Azure AD に送信されません。 代わりに、元の MD4 ハッシュの SHA256 ハッシュが送信されます。 その結果、Azure AD に格納されているハッシュを取得しても、このハッシュをオンプレミスの Pass-the-Hash 攻撃で使用することはできません。

### <a name="security-considerations"></a>セキュリティに関する考慮事項

パスワードを同期するとき、ユーザーのプレーンテキスト形式のパスワードは、パスワード ハッシュ同期機能にも、Azure AD や関連するどのサービスにも公開されません。

ユーザー認証は、組織の独自の Active Directory インスタンスではなく、Azure AD に対して行われます。 Azure AD に格納された SHA256 パスワード データ (元の MD4 ハッシュのハッシュ) は、Active Directory に格納されるものよりもセキュリティで保護されます。 さらに、SHA256 ハッシュの暗号化は解除できないため、組織の Active Directory 環境に戻して、Pass-the-Hash 攻撃で有効なユーザーのパスワードとして表示することはできません。

### <a name="password-policy-considerations"></a>パスワード ポリシーの考慮事項

パスワード ハッシュ同期を有効にすることによって影響を受ける 2 種類のパスワード ポリシーがあります。

* パスワードの複雑性のポリシー
* パスワードの有効期限のポリシー

#### <a name="password-complexity-policy"></a>パスワードの複雑性のポリシー

パスワード ハッシュ同期が有効になっている場合、オンプレミスの Active Directory インスタンスでのパスワードの複雑性ポリシーによって、同期済みユーザーに対するクラウドでの複雑性ポリシーがオーバーライドされます。 オンプレミスの Active Directory インスタンスからの有効なパスワードすべてを、Azure AD サービスへのアクセスに使用することができます。

> [!NOTE]
> クラウド内で直接作成されたユーザーのパスワードには、引き続きクラウドで定義されているパスワード ポリシーが適用されます。

#### <a name="password-expiration-policy"></a>パスワードの有効期限のポリシー

ユーザーがパスワード ハッシュ同期のスコープ内にいる場合、既定でクラウド アカウントのパスワードは "*期限なし*" に設定されます。

オンプレミス環境で期限切れになった同期パスワードを利用し、引き続きクラウド サービスにサインインできます。 クラウドのパスワードは、次にオンプレミス環境でパスワードを変更したときに更新されます。

##### <a name="enforcecloudpasswordpolicyforpasswordsyncedusers"></a>EnforceCloudPasswordPolicyForPasswordSyncedUsers

Azure AD 統合サービスのみを操作し、パスワードの有効期限ポリシーに準拠している必要がある同期済みユーザーがいる場合は、*EnforceCloudPasswordPolicyForPasswordSyncedUsers* 機能を有効にすることで、Azure AD パスワードの有効期限ポリシーに強制的に準拠させることができます。

*EnforceCloudPasswordPolicyForPasswordSyncedUsers* が無効になっている場合 (既定の設定)、Azure AD Connect では、同期済みユーザーの PasswordPolicies 属性は "DisablePasswordExpiration" に設定されます。 これは、ユーザーのパスワードが同期されるたびに実行され、そのユーザーのクラウド パスワードの有効期限ポリシーを無視するように Azure AD へ指示されます。 Azure AD PowerShell モジュールを使用して、次のコマンドを使って属性の値を確認できます。

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`

EnforceCloudPasswordPolicyForPasswordSyncedUsers 機能を有効にするには、下に示すように、MSOnline PowerShell モジュールを使用して次のコマンドを実行します。 場合によっては、下に示すように、Enable パラメーターに「yes」と入力する必要があります。

```
Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers
cmdlet Set-MsolDirSyncFeature at command pipeline position 1
Supply values for the following parameters:
Enable: yes
Confirm
Continue with this operation?
[Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
```

有効にすると、Azure AD では、PasswordPolicies 属性から `DisablePasswordExpiration` 値を削除するために、同期済みの各ユーザーに移動することがなくなります。 代わりに、オンプレミス AD での次回のパスワードの変更時、各ユーザーの次のパスワード ハッシュ同期中に PasswordPolicies から `DisablePasswordExpiration` 値が削除されます。

パスワード ハッシュの初期同期によってユーザーの PasswordPolicies 属性に `DisablePasswordExpiration` 値が追加されないように、パスワード ハッシュ同期を有効にする前に、EnforceCloudPasswordPolicyForPasswordSyncedUsers を有効にすることをお勧めします。

既定の Azure AD パスワード ポリシーでは、90 日ごとにパスワードを変更するようにユーザーへ要求します。 AD でのポリシーも 90 日になっている場合は、2 つのポリシーが一致しているはずです。 しかし、AD のポリシーが 90 日でない場合は、Set-MsolPasswordPolicy PowerShell コマンドを使用して、Azure AD パスワード ポリシーが一致するように更新できます。

Azure AD では、登録されたドメインごとに、個別のパスワード有効期限ポリシーがサポートされます。

注意:有効期限内のパスワードを保持している必要がある同期済みアカウントが Azure AD 上にある場合、Azure AD 上でそのユーザー ブジェクトの PasswordPolicies 属性に `DisablePasswordExpiration` 値を明示的に追加する必要があります。  これを行うには、次のコマンドを実行します。

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> Set-MsolPasswordPolicy PowerShell コマンドはフェデレーション ドメインで機能しません。 

#### <a name="synchronizing-temporary-passwords-and-force-password-change-on-next-logon"></a>一時パスワードと "次回ログオン時にパスワード変更を適用" を同期する

通常は、最初のログオン時、特に管理者によるパスワードのリセットが行われた後に、ユーザーにパスワードの変更を強制します。  一般的に "一時" パスワードの設定と呼ばれており、Active Directory (AD) では、ユーザー オブジェクト上の [ユーザーは次回ログオン時にパスワード変更が必要] フラグのチェックをオンにすることで実現できます。
  
一時パスワード機能を使用すると、資格情報の所有権の譲渡が初回使用時に確実に完了し、複数の個人がその資格情報に関する知識を持つ期間を最小限に抑えることができます。

同期済みユーザーに対して Azure AD 上で一時パスワードをサポートするには、Azure AD Connect サーバー上で次のコマンドを実行して *ForcePasswordChangeOnLogOn* 機能を有効にします。

`Set-ADSyncAADCompanyFeature -ForcePasswordChangeOnLogOn $true`

> [!NOTE]
> 次回ログオン時のパスワード変更をユーザーに強制すると、同時でのパスワード変更が必要になります。  Azure AD Connect では、パスワードの強制変更フラグは、パスワード ハッシュの同期中に行われた検出済みのパスワード変更に対する補足であり、単独では取得されません。

> [!CAUTION]
> この機能は、テナント上で SSPR とパスワード ライトバックが有効になっている場合にのみ使用してください。  これは、ユーザーが SSPR を使用してパスワードを変更した場合に、Active Directory に同期されるようにするためです。

#### <a name="account-expiration"></a>アカウントの有効期限

組織でユーザー アカウント管理の一環として accountExpires 属性を使用する場合、この属性は Azure AD に同期されません。 この結果、パスワード ハッシュ同期用に構成された環境の期限切れの Active Directory アカウントが、Azure AD では引き続きアクティブなままとなります。 アカウントが期限切れの場合はユーザーの Azure AD アカウントを無効にする PowerShell スクリプトをワークフロー アクションでトリガーすることをお勧めします ([Set-AzureADUser](/powershell/module/azuread/set-azureaduser) コマンドレットを使用)。 逆に、アカウントを有効にしたときは、Azure AD インスタンスを有効にする必要があります。

### <a name="overwrite-synchronized-passwords"></a>同期されたパスワードの上書き

管理者は Windows PowerShell を使用してパスワードを手動でリセットできます。

この場合、新しいパスワードによって同期されたパスワードは上書きされ、クラウドで定義されているすべてのパスワード ポリシーが新しいパスワードに適用されます。

オンプレミスのパスワードを再び変更した場合、新しいパスワードはクラウドに同期され、手動で更新したパスワードを上書きします。

パスワードの同期によって、サインイン中の Azure ユーザーが影響を受けることはありません。 クラウド サービスにログインしている間、パスワード変更が同期された場合、現在のクラウド サービス セッションがその影響をすぐに受けることはありません。 KMSI を使用すると、この差異のある期間が長くなります。 クラウド サービスで認証が再び要求された場合は、新しいパスワードを指定する必要があります。

### <a name="additional-advantages"></a>その他の利点

- 通常、パスワード ハッシュ同期は、フェデレーション サービスよりも実装が簡単です。 パスワード同期は追加のサーバーを必要としないため、ユーザーを認証するために高可用性フェデレーション サービスに依存するという状況を解消できるからです。
- フェデレーションだけでなく、パスワード ハッシュ同期を有効にすることもできます。 これは、フェデレーション サービスで障害が発生した場合に、フォールバックとして使用できます。

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Azure AD Domain Services のパスワード ハッシュ同期プロセス

Azure AD Domain Services を使用して、Kerberos、LDAP、または NTLM を使用する必要があるアプリケーションやサービスにレガシ認証を提供する場合、追加のプロセスがパスワード ハッシュ同期フローに含まれます。 Azure AD Connect は、次の追加プロセスを使用して、Azure AD Domain Services で使用するためにパスワード ハッシュを Azure AD に同期します。

> [!IMPORTANT]
> Azure AD Connect は、オンプレミスの AD DS 環境との同期のためにのみインストールおよび構成する必要があります。 オブジェクトを Azure AD に同期するために、Azure AD DS マネージド ドメインに Azure AD Connect をインストールすることはサポートされていません。
>
> Azure AD Connect によってレガシ パスワード ハッシュが同期されるのは、Azure AD DS を Azure AD テナントに対して有効にしたときだけです。 Azure AD Connect を使用してオンプレミス AD DS 環境と Azure AD の同期しか行わない場合、次の手順は使用されません。
>
> レガシ アプリケーションが NTLM 認証または LDAP simple bind を使用していない場合は、Azure AD DS の NTLM パスワード ハッシュ同期を無効にすることをお勧めします。 詳しくは、「[弱い暗号スイートと NTLM 資格情報ハッシュの同期を無効にする](../../active-directory-domain-services/secure-your-domain.md)」をご覧ください。

1. Azure AD Connect はテナントの Azure AD Domain Services インスタンスの公開キーを取得します。
1. ユーザーがパスワードを変更すると、オンプレミスのドメイン コントローラーは、パスワード変更 (ハッシュ) の結果を次の 2 つの属性に格納します。
    * *unicodePwd*: NTLM パスワード ハッシュ。
    * *supplementalCredentials*: Kerberos パスワード ハッシュ。
1. Azure AD Connect では、ディレクトリ レプリケーション チャネル 介してパスワードの変更が検出されます (他のドメイン コントローラーにレプリケートする必要がある属性の変更)。
1. パスワードが変更されたユーザーごとに、Azure AD Connect が次の手順を実行します。
    * ランダムな AES 256 ビット対称キーを生成します。
    * 暗号化の最初のラウンドに必要なランダムな初期化ベクターを生成します。
    * Kerberos パスワードハッシュを *supplementalCredentials* 属性から抽出します。
    * Azure AD Domain Services セキュリティ構成の *SyncNtlmPasswords* 設定を確認します。
        * この設定が無効になっている場合、ランダムな高エントロピ NTLM ハッシュ (ユーザーのパスワードとは異なる) が生成されます。 このハッシュは、*supplementalCrendetials* 属性から抽出された Kerberos パスワード ハッシュと結合されて 1 つのデータ構造になります。
        * 有効になっている場合は、*unicodePwd* 属性の値と *supplementalCredentials* から抽出された Kerberos パスワード ハッシュが結合されて 1 つのデータ構造になります。
    * AES 対称キーを使用して 1 つのデータ構造を暗号化します。
    * テナントの Azure AD Domain Services 公開キーを使用して AES 対称キーを暗号化します。
1. Azure AD Connect は、暗号化された AES 対称キー、暗号化されたデータ構造 (パスワード ハッシュを含む)、および初期化ベクターを Azure AD に送信します。
1. Azure AD は、暗号化された AES 対称キー、暗号化されたデータ構造、および初期化ベクターをユーザーについて格納します。
1. Azure AD は、暗号化された HTTP セッション上で内部同期メカニズムを使用して、暗号化された AES 対称キー、暗号化されたデータ構造、および初期化ベクターを Azure AD Domain Services にプッシュします。
1. Azure AD Domain Services は、テナントのインスタンスの秘密キーを Azure Key Vault から取得します。
1. 暗号化されたデータのセット (1 人のユーザーのパスワード変更を表す) ごとに、Azure AD Domain Services は次の手順を実行します。
    * 秘密キーを使用して AES 対称キーを復号化します。
    * AES 対称キーを初期化ベクターと共に使用し、暗号化されたデータ構造 (パスワード ハッシュが含まれる) を復号化します。
    * 受け取った Kerberos パスワード ハッシュを Azure AD Domain Services ドメイン コントローラーに書き込みます。 ハッシュは、ユーザー オブジェクトの *supplementalCredentials* 属性 (Azure AD Domain Services ドメイン コントローラーの公開キーに暗号化される) に保存されます。
    * Azure AD Domain Services は、受け取った NTLM パスワード ハッシュを Azure AD Domain Services ドメイン コントローラーに書き込みます。 ハッシュは、ユーザー オブジェクトの *unicodePwd* 属性 (Azure AD Domain Services ドメイン コントローラーの公開キーに暗号化される) に保存されます。

## <a name="enable-password-hash-synchronization"></a>パスワード ハッシュ同期を有効にする

>[!IMPORTANT]
>AD FS (または他のフェデレーション テクノロジ) からパスワード ハッシュの同期に移行する場合は、[こちら](https://aka.ms/adfstophsdpdownload)に公開されている詳細なデプロイ ガイドに従うことを強くお勧めします。

**簡単設定** オプションを使用して Azure AD Connect をインストールすると、パスワード ハッシュ同期が自動的に有効になります。 詳細については、「[簡単設定を使用した Azure AD Connect の開始](how-to-connect-install-express.md)」を参照してください。

Azure AD Connect のインストール時にカスタム設定を使用すると、ユーザー サインイン ページでパスワード ハッシュ同期が使用可能になります。 詳細については、「[Azure AD Connect のカスタム インストール](how-to-connect-install-custom.md)」をご覧ください。

![パスワード ハッシュ同期を有効にする](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>パスワード ハッシュ同期と FIPS
Federal Information Processing Standard (FIPS) に従ってサーバーがロックされた場合、MD5 は無効になります。

**パスワード ハッシュ同期で MD5 を有効にするには、次の手順を実行します。**

1. %programfiles%\Azure AD Sync\Bin に移動します。
2. miiserver.exe.config を開きます。
3. ファイルの末尾にある configuration/runtime ノードに移動します。
4. `<enforceFIPSPolicy enabled="false"/>`
5. 変更を保存します。

参考までに、このスニペットは次のようになります。

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

セキュリティと FIPS の詳細については、[Azure AD のパスワード ハッシュ同期、暗号化、および FIPS コンプライアンス](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)に関するページを参照してください。

## <a name="troubleshoot-password-hash-synchronization"></a>パスワード ハッシュ同期のトラブルシューティング
パスワード ハッシュ同期に問題がある場合は、[パスワード ハッシュ同期のトラブルシューティング](tshoot-connect-password-hash-synchronization.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ
* [Azure AD Connect 同期:同期オプションをカスタマイズする](how-to-connect-sync-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)
* [ADFS からパスワード ハッシュ同期に移行するための詳細なデプロイ計画の取得](https://aka.ms/authenticationDeploymentPlan)
