---
title: Azure AD Connect:Azure AD でフェデレーションから PHS に移行する | Microsoft Docs
description: この記事では、ハイブリッド ID 環境をフェデレーションからパスワード ハッシュ同期に移行する方法について説明します。
services: active-directory
author: billmath
manager: daveba
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ce9c0c6d4f9002b061afd2ad09f02266d452979
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67109264"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-active-directory"></a>Azure Active Directory でフェデレーションからパスワード ハッシュ同期に移行する

この記事では、組織のドメインを Active Directory フェデレーション サービス (AD FS) からパスワード ハッシュ同期に移行する方法について説明します。

[この記事はダウンロード](https://aka.ms/ADFSTOPHSDPDownload)することができます。

## <a name="prerequisites-for-migrating-to-password-hash-synchronization"></a>パスワード ハッシュ同期への移行の前提条件

AD FS の使用からパスワード ハッシュ同期の使用に移行するには、次の前提条件を満たしている必要があります。

### <a name="update-azure-ad-connect"></a>Azure AD Connect を更新する

パスワード ハッシュ同期に移行する手順を正常に実行するには、最低でも [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0 が必要です。 このバージョンでは、サインイン変換の実行方法が大幅に変更されており、フェデレーションからクラウド認証への移行にかかる時間全体が数時間から数分に短縮できる可能性があります。


> [!IMPORTANT]
> 古いドキュメント、ツール、およびブログでは、ドメインをフェデレーション ID からマネージド ID に変換する際に、ユーザーの変換が必要であると記載されている場合があります。 "*ユーザーの変換*" は必要なくなりました。 Microsoft では、この変更を反映するようにドキュメントやツールを更新しています。

Azure AD Connect を更新するには、「[Azure AD Connect:旧バージョンから最新バージョンにアップグレードする](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version)」の手順を完了してください。

### <a name="password-hash-synchronization-required-permissions"></a>パスワード ハッシュ同期に必要なアクセス許可

Azure AD Connect は、簡易設定またはカスタム インストールを使用して構成できます。 カスタム インストール オプションを使用した場合、パスワード ハッシュ同期に[必要なアクセス許可](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-accounts-permissions)が設定されていない可能性があります。

パスワード ハッシュを同期するには、Azure AD Connect の Active Directory Domain Services (AD DS) サービス アカウントに以下のアクセス許可が必要です。

* ディレクトリの変更のレプリケート
* ディレクトリの変更をすべてにレプリケート

ここで、これらのアクセス許可がフォレスト内のすべてのドメインに対して設定されていることを確認しておくとよいでしょう。

### <a name="plan-the-migration-method"></a>移行方法を計画する

フェデレーション ID 管理からパスワード ハッシュ同期とシームレスなシングル サインオン (SSO) に移行する場合、2 つの方法から選ぶことができます。 使用する方法は、最初に AD FS インスタンスをどのように構成したかによって決まります。

* **Azure AD Connect**。 最初に Azure AD Connect を使用して AD FS を構成した場合は、Azure AD Connect ウィザードを使用してパスワード ハッシュ同期に変更する "*必要があります*"。

   ‎ユーザー サインインの方法を変更する際、Azure AD Connect によって自動的に **Set-MsolDomainAuthentication** コマンドレットが実行されます。 Azure AD Connect によって、Azure AD テナント内のすべての確認済みフェデレーション ドメインが自動的にフェデレーション解除されます。

   > [!NOTE]
   > 現在のところ、最初に Azure AD Connect を使用して AD FS を構成した場合、ユーザー サインインをパスワード ハッシュ同期に変更する際に、テナント内のすべてのドメインがフェデレーション解除されることは避けられません。 ‎
* **Azure AD Connect と PowerShell**。 この方法は、最初に Azure AD Connect を使用して AD FS を構成していない場合にのみ使用できます。 このオプションでも、Azure AD Connect ウィザードを使用してユーザー サインインの方法を変更する必要があります。 このオプションの主な違いは、ウィザードで **Set-MsolDomainAuthentication** コマンドレットが自動的に実行されないことです。 このオプションでは、どのドメインをどの順序で変換するかを完全に制御できます。

どの方法を使用すればよいかを理解するために、次のセクションの手順を行います。

#### <a name="verify-current-user-sign-in-settings"></a>現在のユーザー サインイン設定を確認する

現在のユーザー サインイン設定を確認するには:

1. 全体管理者アカウントを使用して、[Azure AD ポータル](https://aad.portal.azure.com/)にサインインします。
2. **[ユーザー サインイン]** セクションで、次のように設定されていることを確認します。
   * **[フェデレーション]** が **[有効]** に設定されている。
   * **[シームレス シングル サインオン]** が **[無効]** に設定されている。
   * **[パススルー認証]** が **[無効]** に設定されている。

   ![Azure AD Connect の [ユーザー サインイン] セクションの設定のスクリーン ショット](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-the-azure-ad-connect-configuration"></a>Azure AD Connect の構成を確認する

1. Azure AD Connect サーバーで、Azure AD Connect を開きます。 **[構成]** をクリックします。
2. **[追加のタスク]** ページで **[現在の構成を表示する]** を選択し、 **[次へ]** を選択します。<br />

   ![[追加のタスク] ページで選択されている [現在の構成を表示する] オプションのスクリーンショット](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)<br />
3. **[ソリューションのレビュー]** ページで、 **[パスワード ハッシュの同期]** の状態を確認します。<br /> 

   * **[パスワード ハッシュの同期]** が **[無効]** に設定されている場合は、この記事の手順を完了して有効にします。
   * **[パスワード ハッシュの同期]** が **[有効]** に設定されている場合は、この記事の「**手順 1:パスワード ハッシュ同期を有効にする**」セクションをスキップします。
4. **[ソリューションのレビュー]** ページで、 **[Active Directory フェデレーション サービス (AD FS)]** までスクロールします。<br />

   * ‎このセクションに AD FS 構成が表示されている場合は、AD FS が最初に Azure AD Connect を使用して構成されたと見なすことができます。 Azure AD Connect の **[ユーザー サインインの変更]** オプションを使用して、ドメインをフェデレーション ID からマネージド ID に変換できます。 このプロセスの詳細については、次のセクションを参照してください。「**オプション A:Azure AD Connect を使用してフェデレーションからパスワード ハッシュ同期に移行する**」。
   * AD FS が現在の設定の一覧に表示されていない場合は、PowerShell を使用して、ドメインをフェデレーション ID からマネージド ID に手動で変換する必要があります。 このプロセスの詳細については、次のセクションを参照してください。「**オプション B:Azure AD Connect と PowerShell を使用してフェデレーションからパスワード ハッシュ同期に移行する**」。

### <a name="document-current-federation-settings"></a>現在のフェデレーション設定をドキュメント化する

現在のフェデレーション設定を確認するには、**Get-MsolDomainFederationSettings** コマンドレットを実行します。

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

例:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```

ご利用のフェデレーションの設計とデプロイのドキュメント用にカスタマイズされた可能性のある、すべての設定を確認します。 具体的には、**PreferredAuthenticationProtocol**、**SupportsMfa**、および **PromptLoginBehavior** のカスタマイズを確認します。

詳細と例については、次の記事をご覧ください。

* [AD FS prompt=login パラメーターのサポート](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)
* [Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> **SupportsMfa** が **True** に設定されている場合は、第 2 要素チャレンジをユーザー認証フローに挿入するためにオンプレミス多要素認証ソリューションを使用しています。 このセットアップは、このドメインをフェデレーション認証からマネージド認証に変換した後の Azure AD 認証シナリオには機能しなくなります。 フェデレーションを無効にした後に、オンプレミスのフェデレーションとの関係を切断しますが、これにはオンプレミスの MFA アダプターが含まれます。 
>
> 代わりに、同じ機能を実行するために、Azure Multi-Factor Authentication クラウドベース サービスを使用します。 先に進む前に、多要素認証の要件を慎重に評価してください。 Azure Multi-Factor Authentication の使用方法、ライセンスの内容、およびユーザーの登録プロセスについて理解してから、ドメインの変換を行ってください。

#### <a name="back-up-federation-settings"></a>フェデレーション設定をバックアップする

この記事で説明されているプロセス中に AD FS ファームの他の証明書利用者への変更は行われませんが、復元できるように AD FS ファームの現在の有効なバックアップを確保しておくことをお勧めします。 現在の有効なバックアップは、無料の Microsoft [AD FS Rapid Restore Tool](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool) を使用して作成できます。 このツールを使用して、AD FS をバックアップし、既存のファームを復元するか、新しいファームを作成できます。

AD FS Rapid Restore Tool を使用しない場合は、少なくとも、Microsoft Office 365 ID プラットフォームの証明書利用者信頼と、追加したすべての関連カスタム要求規則をエクスポートする必要があります。 証明書利用者信頼と関連する要求規則は、次の PowerShell の例を使用してエクスポートできます。

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-using-ad-fs"></a>デプロイに関する考慮事項と AD FS の使用状況

このセクションでは、デプロイに関する考慮事項と AD FS の使用状況の詳細について説明します。

### <a name="current-ad-fs-use"></a>現在の AD FS の使用状況

フェデレーション ID からマネージド ID に変換する前に、Azure AD、Office 365、およびその他のアプリケーション (証明書利用者信頼) で現在どのように AD FS を使用しているかを詳しく調べます。 具体的には、次の表に記載されているシナリオについて検討します。

| 状況 | 対処 |
|-|-|
| 引き続き AD FS を (Azure AD と Office 365 以外の) 他のアプリケーションと一緒に使用する予定である。 | ドメインを変換した後、AD FS と Azure AD の両方を使用します。 ユーザー エクスペリエンスをよく検討してください。 一部のシナリオでは、ユーザーの認証が 2 回必要になる可能性があります。1 回は Azure AD に対するもので (これにより、ユーザーは Office 365 などの他のアプリケーションに対する SSO アクセスを取得します)、もう 1 回は証明書利用者信頼として AD FS にまだバインドされているすべてのアプリケーションに対するものです。 |
| AD FS インスタンスが大幅にカスタマイズされていて、onload.js ファイル内の特定のカスタマイズ設定に依存している (たとえば、ユーザー名にユーザー プリンシパル名 (UPN) ではなく **SamAccountName** 形式のみを使用するようにサインイン エクスペリエンスを変更している場合や、組織でサインイン エクスペリエンスを大幅にブランド化している場合)。 Azure AD で onload.js ファイルを複製できない。 | 続行する前に、Azure AD で現在のカスタマイズ要件を満たせることを確認する必要があります。 詳細情報とガイダンスについては、AD FS のブランド化と AD FS のカスタマイズに関するセクションを参照してください。|
| AD FS を使用して、以前のバージョンの認証クライアントをブロックしている。| [条件付きアクセス制御](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions)と [Exchange Online のクライアント アクセス規則](https://aka.ms/EXOCAR)の組み合わせを使用して、以前のバージョンの認証クライアントをブロックする AD FS 制御を置き換えることを検討します。 |
| ユーザーに、AD FS への認証時にオンプレミスの多要素認証サーバー ソリューションに対する多要素認証を行うことを要求している。| マネージド ID ドメインでは、オンプレミスの多要素認証ソリューションを介して認証フローに多要素認証チャレンジを挿入することはできません。 ただし、ドメインの変換後は、Azure Multi-Factor Authentication サービスを使用して多要素認証を行うことができます。<br /><br /> 現在、ユーザーが Azure Multi-Factor Authentication を使用していない場合は、1 回限りのユーザー登録手順が必要になります。 計画した登録の準備を行い、ユーザーに連絡する必要があります。 |
| 現在、Office 365 へのアクセスを制御するために、AD FS でアクセス制御ポリシー (AuthZ 規則) を使用している。| ポリシーを同等の Azure AD [条件付きアクセス ポリシー](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)と [Exchange Online のクライアント アクセス規則](https://aka.ms/EXOCAR)に置き換えることを検討します。|

### <a name="common-ad-fs-customizations"></a>一般的な AD FS のカスタマイズ

このセクションでは、一般的な AD FS のカスタマイズについて説明します。

#### <a name="insidecorporatenetwork-claim"></a>InsideCorporateNetwork 要求

認証を行うユーザーが企業ネットワーク内に存在する場合、AD FS によって **InsideCorporateNetwork** 要求が発行されます。 その後、この要求を Azure AD に渡すことができます。 要求は、ユーザーのネットワークの場所に基づいて多要素認証をバイパスするために使用されます。 現在、AD FS でこの機能が有効になっているかどうかを判断する方法については、「[フェデレーション ユーザー用の信頼できる IP](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud)」を参照してください。

**InsideCorporateNetwork** 要求は、ドメインがパスワード ハッシュ同期に変換された後は使用できません。 この機能の代わりに、[Azure AD の名前付きの場所](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations)を使用できます。

ネームド ロケーションを構成した後は、ネットワークの **[すべての信頼できる場所]** または **[MFA の信頼できる IP]** の値を含めるか除外するために構成されたすべての条件付きアクセス ポリシーを、新しいネームド ロケーションを反映するように更新する必要があります。

条件付きアクセスでの**場所**の条件の詳細については、[Active Directory の条件付きアクセスの場所](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations)に関するページを参照してください。

#### <a name="hybrid-azure-ad-joined-devices"></a>ハイブリッド Azure AD 参加済みデバイス

Azure AD にデバイスを参加させる場合、デバイスがセキュリティとコンプライアンスに関するアクセス基準を満たすよう、条件付きアクセス規則を作成して適用することができます。 また、ユーザーは個人アカウントではなく、職場や学校のアカウントを使用してデバイスにサインインできます。 ハイブリッド Azure AD 参加済みデバイスを使用する場合、Active Directory ドメイン参加済みデバイスを Azure AD に参加させることができます。 フェデレーション環境は、この機能を使用するように設定されている可能性があります。

ドメインがパスワード ハッシュ同期に変換された後もドメインに参加させたすべてのデバイスでハイブリッド参加が機能し続けるようにするには、Windows 10 クライアントでは、Azure AD Connect を使用して、Active Directory コンピューター アカウントを Azure AD に同期させる必要があります。 

Windows 8 および Windows 7 のコンピューター アカウントの場合、ハイブリッド参加ではコンピューターを Azure AD に登録するためにシームレス SSO が使用されます。 Windows 10 デバイスの場合のように、Windows 8 および Windows 7 のコンピューター アカウントを同期させる必要はありません。 ただし、Windows 8 および Windows 7 のクライアントには、それらがシームレス SSO を使用して自身を登録できるよう、更新された workplacejoin.exe ファイルを (.msi ファイルを通じて) デプロイする必要があります。 [.msi ファイルをダウンロード](https://www.microsoft.com/download/details.aspx?id=53554)します。

詳細については、[ハイブリッド Azure AD 参加済みデバイスの構成](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup)に関するページを参照してください。

#### <a name="branding"></a>ブランド

組織でより関連性の高い情報を表示するために [AD FS サインイン ページをカスタマイズ](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization)している場合は、同様に [Azure AD サインイン ページをカスタマイズ](https://docs.microsoft.com/azure/active-directory/customize-branding)することを検討してください。

同様のカスタマイズを行うことができますが、変換後は、サインイン ページの外観が多少変更されることが予想されます。 予想される変更について、事前にユーザーに連絡しておくとよいでしょう。

> [!NOTE]
> 組織のブランド化を利用できるのは、Azure Active Directory の Premium ライセンスまたは Basic ライセンスを購入した場合、あるいは Office 365 のライセンスを所有している場合だけです。

## <a name="plan-deployment-and-support"></a>デプロイとサポートを計画する

このセクションで説明されているタスクを完了します。これらのタスクはデプロイとサポートの計画に役立ちます。

### <a name="plan-the-maintenance-window"></a>メンテナンス期間の計画

ドメイン変換プロセスは比較的高速ですが、Azure AD ではドメイン変換の終了後も最大 4 時間、何らかの認証要求を AD FS サーバーに送信し続ける可能性があります。 この 4 時間の期間に、さまざまなサービス側のキャッシュによっては、これらの認証が Azure AD で受け入れられない可能性があります。 ユーザーはエラーを受け取る可能性があります。 ユーザーはまだ AD FS に対して正常に認証できますが、フェデレーションの信頼は削除されるため、Azure AD ではユーザーの発行したトークンが受け入れられなくなります。

この変換後の期間に、サービス側のキャッシュがクリアされるまでに Web ブラウザーを介してサービスにアクセスするユーザーのみが影響を受けます。 レガシ クライアント (Exchange ActiveSync、Outlook 2010/2013) に影響することはありません。Exchange Online では、一定の期間、それらの資格情報のキャッシュが保持されるためです。 キャッシュは、ユーザーを自動的に再認証するために使用されます。 ユーザーが AD FS に戻る必要はありません。 これらのクライアントのためにデバイスに格納されている資格情報は、このキャッシュがクリアされた後、自身を自動的に再認証するために使用されます。 ドメイン変換プロセスの結果として、パスワードの入力を求めるメッセージがユーザーに表示されることはありません。 

先進認証クライアント (Office 2016 と Office 2013、iOS、および Android アプリ) では、リソースへのアクセスを継続するための新しいアクセス トークンを取得するために、AD FS に戻るのではなく、有効な更新トークンが使用されます。 これらのクライアントに、ドメイン変換プロセスの結果としてパスワードの入力を求めるメッセージを表示する必要はありません。 クライアントは、追加の構成を行わなくても機能し続けます。

> [!IMPORTANT]
> すべてのユーザーがクラウド認証を使用して正常に認証されることを確認できるまで、AD FS 環境をシャットダウンしたり、Office 365 証明書利用者信頼を削除したりしないでください。

### <a name="plan-for-rollback"></a>ロールバックのための計画

すぐに解決できない重大な問題が見つかった場合は、ソリューションをフェデレーションにロールバックする可能性があります。 デプロイが意図したとおりにロールアウトされない場合の対応を計画することは重要です。 デプロイ中にドメインまたはユーザーの変換が失敗した場合、あるいはフェデレーションにロールバックする必要がある場合は、停止時間を短縮してユーザーへの影響を軽減する方法を理解する必要があります。

#### <a name="to-roll-back"></a>ロールバックするには

ロールバックを計画する場合は、特定のデプロイの詳細について、フェデレーションの設計とデプロイに関するドキュメントを確認してください。 プロセスには、以下のタスクを含める必要があります。

* **Convert-MSOLDomainToFederated** コマンドレットを使用して、マネージド ドメインをフェデレーション ドメインに変換する。
* 必要な場合は、追加の要求規則を構成する。

### <a name="plan-communications"></a>連絡を計画する

デプロイとサポートの計画で重要な部分は、今後の変更について、ユーザーに必ず事前に通知することです。 ユーザーは、何が起きるか、また何を行う必要があるかについて、事前に知っておく必要があります。 

パスワード ハッシュ同期とシームレス SSO の両方がデプロイされた後、Azure AD を通じて認証される Office 365 およびその他のリソースにアクセスするためのユーザーのサインイン エクスペリエンスが変更されます。 ネットワークの外部のユーザーには、Azure AD サインイン ページのみが表示されます。 これらのユーザーは、外部に接続する Web アプリケーション プロキシ サーバーによって表示されるフォームベースのページにはリダイレクトされません。

連絡に関する戦略に次の要素を含めます。

* 以下を使用して、近日公開される機能とリリース済みの機能をユーザーに通知します。
   * 電子メールおよびその他の内部通信チャネル。
   * ポスターなどのビジュアル。
   * 経営幹部による直接の連絡またはその他の連絡。
* 誰が連絡内容をカスタマイズするか、誰が連絡を送るか、およびそのタイミングを決めます。

## <a name="implement-your-solution"></a>ソリューションを実装する

ソリューションの計画は済みました。 これで、実装することができます。 実装には次の要素が含まれます。

* パスワード ハッシュ同期を有効にする。
* シームレス SSO を準備する。
* サインイン方法をパスワード ハッシュ同期に変更し、シームレス SSO を有効にする。

### <a name="step-1-enable-password-hash-synchronization"></a>手順 1:パスワード ハッシュ同期を有効にする

このソリューションを実装する最初の手順は、Azure AD Connect ウィザードを使用してパスワード ハッシュ同期を有効にすることです。 パスワード ハッシュ同期は、フェデレーションを使用する環境で有効にできるオプションの機能です。 認証フローへの影響はありません。 この場合、フェデレーションを使用してサインインするユーザーに影響が及ぶことなく、Azure AD Connect によってパスワード ハッシュの同期が開始されます。

このため、ドメインのサインイン方法を変更するかなり前に、準備タスクとしてこの手順を完了することをお勧めします。 そうすることで、パスワード ハッシュ同期が正しく動作することを確認するための十分な時間を確保できます。

パスワード ハッシュ同期を有効にするには:

1. Azure AD Connect サーバーで、Azure AD Connect ウィザードを開き、 **[構成]** を選択します。
2. **[同期オプションのカスタマイズ]** を選択し、 **[次へ]** を選択します。
3. **[Azure AD に接続]** ページで、全体管理者アカウントのユーザー名とパスワードを入力します。
4. **[ディレクトリの接続]** ページで、 **[次へ]** を選択します。
5. **[ドメインと OU のフィルタリング]** ページで、 **[次へ]** を選択します。
6. **[オプション機能]** ページで **[パスワード同期]** を選択し、 **[次へ]** を選択します。
 
   ![[パスワード同期] オプションが選択された [オプション機能] ページのスクリーンショット](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)<br />
7. 残りのページで **[次へ]** を選択します。 最後のページで **[構成]** を選択します。
8. Azure AD Connect によって、次回の同期時にパスワード ハッシュの同期が開始されます。

パスワード ハッシュ同期が有効になると、Azure AD Connect 同期スコープに含まれているすべてのユーザーのパスワード ハッシュが再ハッシュされ、Azure AD に書き込まれます。 ユーザー数に応じて、この操作には数分から数時間かかる場合があります。

計画のためには、1 時間で約 20,000 人のユーザーが処理されると見積もる必要があります。

パスワード ハッシュ同期が正しく動作していることを確認するには、Azure AD Connect ウィザードの**トラブルシューティング** タスクを完了します。

1. [管理者として実行] オプションを使用して、Azure AD Connect サーバーで新しい Windows PowerShell セッションを開きます。
2. `Set-ExecutionPolicy RemoteSigned` または `Set-ExecutionPolicy Unrestricted` を実行します。
3. Azure AD Connect ウィザードを開始します。
4. **[追加のタスク]** ページに移動し、 **[トラブルシューティング]** を選択して **[次へ]** を選択します。
5. **[トラブルシューティング]** ページで、 **[起動]** を選択して PowerShell でトラブルシューティング メニューを起動します。
6. メイン メニューで、 **[Troubleshoot password hash synchronization]\(パスワード ハッシュ同期のトラブルシューティング\)** を選択します。
7. サブメニューで、 **[Password hash synchronization does not work at all]\(パスワード ハッシュ同期がまったく機能しない\)** を選択します。

問題をトラブルシューティングする際は、「[Azure AD Connect Sync を使用したパスワード ハッシュ同期のトラブルシューティング](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization)」を参照してください。

### <a name="step-2-prepare-for-seamless-sso"></a>手順 2:シームレス SSO を準備する

デバイスでシームレス SSO を使用する場合は、Active Directory のグループ ポリシーを使って、ユーザーのイントラネット ゾーン設定に Azure AD URL を追加する必要があります。

既定では、Web ブラウザーで、URL から適切なゾーン (インターネットまたはイントラネット) が自動的に判断されます。 たとえば、**http:\/\/contoso/** はイントラネット ゾーンにマップされ、**http:\/\/intranet.contoso.com** はインターネット ゾーンにマップされます (URL にピリオドが含まれているため)。 Azure AD URL と同様に、URL をブラウザーのイントラネット ゾーンに明示的に追加した場合にのみ、ブラウザーから Kerberos チケットがクラウド エンドポイントに送信されます。

必要な変更をデバイスに[ロールアウト](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start)する手順を完了してください。

> [!IMPORTANT]
> この変更を行っても、ユーザーが Azure AD にサインインする方法は変わりません。 ただし、先に進む前に、すべてのデバイスにこの構成を適用することが重要です。 この構成を受け取っていないデバイスでサインインするユーザーは、Azure AD にサインインするために、ユーザー名とパスワードを入力するだけで済みます。

### <a name="step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso"></a>手順 3:サインイン方法をパスワード ハッシュ同期に変更し、シームレス SSO を有効にする

サインイン方法をパスワード ハッシュ同期に変更し、シームレス SSO を有効にするためのオプションは 2 つあります。

#### <a name="option-a-switch-from-federation-to-password-hash-synchronization-by-using-azure-ad-connect"></a>オプション A: Azure AD Connect を使用してフェデレーションからパスワード ハッシュ同期に移行する

最初に Azure AD Connect を使用して AD FS 環境を構成した場合は、この方法を使用します。 最初に Azure AD Connect を使用して AD FS 環境を構成 "*しなかった*" 場合は、この方法を使用できません。

まず、サインイン方法を変更します。

1. Azure AD Connect サーバーで、Azure AD Connect ウィザードを開きます。
2. **[ユーザー サインインの変更]** を選択し、 **[次へ]** を選択します。 

   ![[追加のタスク] ページの [ユーザー サインインの変更] オプションのスクリーンショット](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)<br />
3. **[Azure AD に接続]** ページで、全体管理者アカウントのユーザー名とパスワードを入力します。
4. **[ユーザー サインイン]** ページで、 **[パスワードハッシュ同期] ボタン**を選択します。 **[ユーザー アカウントを変換しない]** チェック ボックスがオンになっていることを確認してください。 このオプションは非推奨になりました。 **[シングル サインオンを有効にする]** を選択してから、 **[次へ]** を選択します。

   ![[シングル サインオンを有効にする] ページのスクリーンショット](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)<br />

   > [!NOTE]
   > Azure AD Connect バージョン 1.1.880.0 以降では、 **[シームレスなシングル サインオン]** チェック ボックスが既定でオンになっています。

   > [!IMPORTANT]
   > フェデレーションからクラウド認証への変換には、ユーザーの変換と完全なパスワード ハッシュ同期の手順が必要であるという警告は、無視してかまいません。 これらの手順はもう必要ないことに注意してください。 これらの警告がまだ表示される場合は、Azure AD Connect の最新バージョンを実行していることと、このガイドの最新バージョンを使用していることを確認してください。 詳細については、「[Azure AD Connect を更新する](#update-azure-ad-connect)」セクションを参照してください。

5. **[シングル サインオンを有効にする]** ページで、ドメイン管理者アカウントの資格情報を入力し、 **[次へ]** を選択します。

   ![[シングル サインオンを有効にする] ページのスクリーンショット](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)<br />

   > [!NOTE]
   > シームレス SSO を有効にするには、ドメイン管理者アカウントの資格情報が必要です。 このプロセスでは、管理者特権のアクセス許可を必要とする、以下のアクションが実行されます。 ドメイン管理者アカウントの資格情報は、Azure AD Connect にも Azure AD にも格納されません。 ドメイン管理者アカウントの資格情報は、機能を有効にするためだけに使用されます。 プロセスが正常に終了したら、資格情報は破棄されます。
   >
   > 1. オンプレミスの Active Directory インスタンスに、(Azure AD を表す) AZUREADSSOACC という名前のコンピューター アカウントが作成されます。
   > 2. コンピューター アカウントの Kerberos 復号化キーが、Azure AD と安全に共有されます。
   > 3. Azure AD のサインイン時に使用される 2 つの URL を表す、2 つの Kerberos サービス プリンシパル名 (SPN) が作成されます。

6. **[構成の準備完了]** ページで、 **[構成が完了したら、同期プロセスを開始してください]** チェック ボックスがオンになっていることを確認します。 次に、 **[構成]** を選択します。

      ![[構成の準備完了] ページのスクリーンショット](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)<br />

   > [!IMPORTANT]
   > この時点で、すべてのフェデレーション ドメインがマネージド認証に変更されます。 パスワード ハッシュ同期が新しい認証方法になります。

7. Azure AD ポータルで、 **[Azure Active Directory]**  >  **[Azure AD Connect]** の順に選択します。
8. 以下の設定を確認します。
   * **[フェデレーション]** が **[無効]** に設定されている。
   * **[シームレス シングル サインオン]** が **[有効]** に設定されている。
   * **[パスワード同期]** が **[有効]** に設定されている。<br /> 

   ![[ユーザー サインイン] セクションの設定を示すスクリーンショット](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)<br />

「[テストと次のステップ](#testing-and-next-steps)」に進みます。

   > [!IMPORTANT]
   > 次のセクションはスキップします。「**オプション B:Azure AD Connect と PowerShell を使用してフェデレーションからパスワード ハッシュ同期に移行する**」。 このセクションの手順は、サインイン方法をパスワード ハッシュ同期に変更し、シームレス SSO を有効にするオプション A を選択した場合、適用されません。

#### <a name="option-b-switch-from-federation-to-password-hash-synchronization-using-azure-ad-connect-and-powershell"></a>オプション B: Azure AD Connect と PowerShell を使用してフェデレーションからパスワード ハッシュ同期に移行する

フェデレーション ドメインを最初に Azure AD Connect を使用して構成しなかった場合は、このオプションを使用します。 このプロセスの間に、シームレス SSO を有効にし、ドメインをフェデレーションからマネージドに切り替えます。

1. Azure AD Connect サーバーで、Azure AD Connect ウィザードを開きます。
2. **[ユーザー サインインの変更]** を選択し、 **[次へ]** を選択します。
3. **[Azure AD に接続]** ページで、全体管理者アカウントのユーザー名とパスワードを入力します。
4. **[ユーザー サインイン]** ページで、 **[パスワード ハッシュ同期]** ボタンを選択します。 **[シングル サインオンを有効にする]** を選択してから、 **[次へ]** を選択します。

   パスワード ハッシュ同期を有効にする前:![[ユーザー サインイン] ページの [構成しない] オプションを示すスクリーンショット](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)<br />

   パスワード ハッシュ同期を有効にした後:![[ユーザー サインイン] ページの新しいオプションを示すスクリーンショット](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)<br />
   
   > [!NOTE]
   > Azure AD Connect バージョン 1.1.880.0 以降では、 **[シームレスなシングル サインオン]** チェック ボックスが既定でオンになっています。

5. **[シングル サインオンを有効にする]** ページで、ドメイン管理者アカウントの資格情報を入力し、 **[次へ]** を選択します。

   > [!NOTE]
   > シームレス SSO を有効にするには、ドメイン管理者アカウントの資格情報が必要です。 このプロセスでは、管理者特権のアクセス許可を必要とする、以下のアクションが実行されます。 ドメイン管理者アカウントの資格情報は、Azure AD Connect にも Azure AD にも格納されません。 ドメイン管理者アカウントの資格情報は、機能を有効にするためだけに使用されます。 プロセスが正常に終了したら、資格情報は破棄されます。
   >
   > 1. オンプレミスの Active Directory インスタンスに、(Azure AD を表す) AZUREADSSOACC という名前のコンピューター アカウントが作成されます。
   > 2. コンピューター アカウントの Kerberos 復号化キーが、Azure AD と安全に共有されます。
   > 3. Azure AD のサインイン時に使用される 2 つの URL を表す、2 つの Kerberos サービス プリンシパル名 (SPN) が作成されます。

6. **[構成の準備完了]** ページで、 **[構成が完了したら、同期プロセスを開始してください]** チェック ボックスがオンになっていることを確認します。 次に、 **[構成]** を選択します。

   ![[構成の準備完了] ページの [構成] ボタンを示すスクリーンショット](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)<br />
   **[構成]** ボタンを選択すると、前の手順で示されているように、シームレス SSO が構成されます。 パスワード ハッシュ同期の構成は、既に有効になっているので変更されません。

   > [!IMPORTANT]
   > この時点で、ユーザーがサインインする方法は変更されません。

7. Azure AD ポータルで、これらの設定を確認します。
   * **[フェデレーション]** が **[有効]** に設定されている。
   * **[シームレス シングル サインオン]** が **[有効]** に設定されている。
   * **[パスワード同期]** が **[有効]** に設定されている。

   ![[ユーザー サインイン] セクションの設定を示すスクリーンショット](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>ドメインをフェデレーションからマネージドに変換する

この時点では、ドメインに対してフェデレーションはまだ有効であり、機能しています。 デプロイを続行するには、各ドメインをフェデレーションからマネージドに変換し、ユーザー認証がパスワード ハッシュ同期を通じて行われるように強制する必要があります。

> [!IMPORTANT]
> すべてのドメインを同時に変換する必要はありません。 運用環境テナントのテスト ドメインや、ユーザー数が最も少ないドメインから開始することができます。

Azure AD PowerShell モジュールを使用して、変換を完了します。

1. PowerShell で、全体管理者アカウントを使用して Azure AD にサインインします。
2. 最初のドメインを変換するには、次のコマンドを実行します。

   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```

3. Azure AD ポータルで、 **[Azure Active Directory]**  >  **[Azure AD Connect]** の順に選択します。
4. 次のコマンドを実行して、ドメインがマネージドに変換されたことを確認します。

   ``` PowerShell
   Get-MsolDomain -DomainName <domain name>
   ```

## <a name="testing-and-next-steps"></a>テストと次のステップ

パスワード ハッシュ同期を確認し、変換プロセスを終了するために、以下のタスクを実行します。

### <a name="test-authentication-by-using-password-hash-synchronization"></a>パスワード ハッシュ同期を使用して認証をテストする 

テナントでフェデレーション ID が使用されていたときに、ユーザーは Azure AD サインイン ページから AD FS 環境にリダイレクトされていました。 フェデレーション認証ではなく、パスワード ハッシュ同期を使用するようにテナントが構成されたので、ユーザーは AD FS にリダイレクトされません。 代わりに、ユーザーは Azure AD サインイン ページから直接サインインします。

パスワード ハッシュ同期をテストするには:

1. シームレス SSO によって自動的にサインインされないように、InPrivate モードで Internet Explorer を開きます。
2. Office 365 のサインイン ページ ([https://portal.office.com](https://portal.office.com/)) に移動します。
3. ユーザーの UPN を入力し、 **[次へ]** を選択します。 必ず、オンプレミスの Active Directory インスタンスから同期されており、以前はフェデレーション認証を使用していた、ハイブリッド ユーザーの UPN を入力してください。 ユーザー名とパスワードを入力するページが表示されます。

   ![ユーザー名を入力するサインイン ページを示すスクリーンショット](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

   ![パスワードを入力するサインイン ページを示すスクリーンショット](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

4. パスワードを入力して **[サインイン]** を選択すると、Office 365 ポータルにリダイレクトされます。

   ![Office 365 ポータルを示すスクリーンショット](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)


### <a name="test-seamless-sso"></a>シームレス SSO をテストする

1. 企業ネットワークに接続されたドメイン参加済みマシンにサインインします。
2. Internet Explorer または Chrome で、次のいずれかの URL に移動します ("contoso" はご自分のドメインに置き換えてください)。

   * https:\/\/myapps.microsoft.com/contoso.com
   * https:\/\/myapps.microsoft.com/contoso.onmicrosoft.com

   ユーザーはすぐに Azure AD サインイン ページにリダイレクトされ、"サインインしようとしています" というメッセージが表示されます。 ユーザーにはユーザー名やパスワードの入力は求められません。<br />

   ![Azure AD サインイン ページとメッセージを示すスクリーンショット](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)<br />
3. ユーザーはリダイレクトされ、アクセス パネルに正常にサインインされます。

   > [!NOTE]
   > シームレス SSO は、ドメイン ヒント (myapps.microsoft.com/contoso.com など) をサポートする Office 365 サービスで機能します。 現在、Office 365 ポータル (portal.office.com) では、ドメイン ヒントがサポートされていません。 ユーザーは UPN を入力する必要があります。 UPN が入力された後、ユーザーの代わりにシームレス SSO によって Kerberos チケットが取得されます。 ユーザーは、パスワードを入力しなくてもサインインできます。

   > [!TIP]
   > SSO のエクスペリエンスを向上させるために、[Windows 10 に Azure AD ハイブリッド結合](https://docs.microsoft.com/azure/active-directory/device-management-introduction)をデプロイすることを検討してください。

### <a name="remove-the-relying-party-trust"></a>証明書利用者信頼を削除する

すべてのユーザーとクライアントが Azure AD を通じて正常に認証されていることを確認したら、Office 365 の証明書利用者信頼を削除しても問題ありません。

AD FS を他の目的で (つまり、他の証明書利用者信頼で) 使用していない場合は、この時点で AD FS の使用を停止しても問題ありません。

### <a name="rollback"></a>ロールバック

重大な問題が見つかり、すぐに解決できない場合は、ソリューションをフェデレーションにロールバックする可能性があります。

特定のデプロイの詳細について、フェデレーションの設計とデプロイに関するドキュメントを確認してください。 プロセスには、以下のタスクが含まれる必要があります。

* **Convert-MSOLDomainToFederated** コマンドレットを使用して、マネージド ドメインをフェデレーション認証に変換する。
* 必要に応じて、追加の要求規則を構成する。

### <a name="sync-userprincipalname-updates"></a>userPrincipalName の更新を同期する

これまで、次の条件が両方とも当てはまらない限り、オンプレミス環境から同期サービスを使用する、**UserPrincipalName** 属性の更新はブロックされていました。

* ユーザーがマネージド (非フェデレーション) ID ドメインに存在する。
* ユーザーにライセンスが割り当てられていない。

この機能を確認または有効にする方法については、「[userPrincipalName の更新を同期する](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsyncservice-features)」を参照してください。

### <a name="troubleshooting"></a>トラブルシューティング

お客様のサポート チームは、フェデレーションからマネージドへの変更中または変更後に発生する認証の問題のトラブルシューティング方法を理解する必要があります。 以下のトラブルシューティングのドキュメントは、お客様のサポート チームが一般的なトラブルシューティングの手順と、問題の特定および解決に役立つ適切な対処を理解するために役立ちます。

[Azure Active Directory のパスワード ハッシュ同期のトラブルシューティングを行う](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization)

[Azure Active Directory シームレス シングル サインオンのトラブルシューティングを行う](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sso)

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>シームレス SSO の Kerberos 復号化キーのロールオーバー

(Azure AD を表す) AZUREADSSOACC コンピューター アカウントの Kerberos 復号化キーを頻繁にロールオーバーすることが重要です。 AZUREADSSOACC コンピューター アカウントは、オンプレミスの Active Directory フォレストで作成されます。 Active Directory ドメイン メンバーがパスワードの変更を送信する方法に合わせて、少なくとも 30 日ごとに Kerberos 復号化キーをロールオーバーすることを強くお勧めします。 関連するデバイスが AZUREADSSOACC コンピューター アカウント オブジェクトにアタッチされていないため、ロールオーバーは手動で実行する必要があります。

Azure AD Connect を実行しているオンプレミス サーバーで、シームレス SSO Kerberos 復号化キーのロールオーバーを開始します。

詳細については、「[AZUREADSSOACC コンピューター アカウントの Kerberos の復号化キーをロールオーバーするにはどうすればよいですか](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq)」を参照してください。

## <a name="next-steps"></a>次の手順

* [Azure AD Connect の設計概念](plan-connect-design-concepts.md)について学習する。
* [適切な認証](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)を選択する。
* [サポートされているトポロジ](plan-connect-design-concepts.md)について学習する。
