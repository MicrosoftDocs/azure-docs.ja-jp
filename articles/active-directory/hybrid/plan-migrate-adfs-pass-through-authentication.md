---
title: Azure AD Connect:フェデレーションから Azure AD 用 PTA への移行
description: この記事には、フェデレーションからパススルー認証へのハイブリッド ID 環境の移行に関する情報が含まれています。
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
ms.openlocfilehash: 27f5a7d8bb6dc347414d84d8cf536f1c2d7a9910
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67109353"
---
# <a name="migrate-from-federation-to-pass-through-authentication-for-azure-active-directory"></a>Azure Active Directory でフェデレーションからパススルー認証に移行する

この記事では、組織のドメインを Active Directory フェデレーション サービス (AD FS) からパススルー認証に移行する方法について説明します。

[この記事はダウンロード](https://aka.ms/ADFSTOPTADPDownload)することができます。

## <a name="prerequisites-for-migrating-to-pass-through-authentication"></a>パススルー認証への移行に関する前提条件

AD FS の使用からパススルー認証の使用に移行するには、次の前提条件を満たしている必要があります。

### <a name="update-azure-ad-connect"></a>Azure AD Connect を更新する

パススルー認証の使用への移行手順を正常に完了するには、[Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) (Azure AD Connect) 1.1.819.0 以降のバージョンが必要です。 Azure AD Connect 1.1.819.0 では、サインイン変換の実行方法が大幅に変更されています。 このバージョンでは、AD FS からクラウド認証への移行にかかる時間が、全体で数時間から数分に短縮できる可能性があります。

> [!IMPORTANT]
> 古いドキュメント、ツール、およびブログでは、ドメインをフェデレーション ID からマネージド ID に変換する際に、ユーザーの変換が必要であると記載されている場合があります。 "*ユーザーの変換*" は必要なくなりました。 Microsoft では、この変更を反映するようにドキュメントやツールを更新しています。

Azure AD Connect を更新するには、「[Azure AD Connect:旧バージョンから最新バージョンにアップグレードする](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version)」の手順を完了します。

### <a name="plan-authentication-agent-number-and-placement"></a>認証エージェントの数と配置を計画する

パススルー認証では、Azure AD Connect サーバー、および Windows サーバーを実行しているオンプレミス コンピューターで軽量のエージェントをデプロイする必要があります。 待ち時間を短縮するには、Active Directory ドメイン コントローラーのできるだけ近くにエージェントをインストールします。

ほとんどのお客様の場合、高可用性と必要な容量を提供するのに、2 つまたは 3 つの認証エージェントがあれば十分です。 テナントには、最大 12 個のエージェントを登録できます。 最初のエージェントは、常に Azure AD Connect サーバー自体にインストールされます。 エージェントの制限事項とエージェントのデプロイ オプションの詳細については、「[Azure Active Directory パススルー認証:現在の制限事項](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-current-limitations)」を参照してください。

### <a name="plan-the-migration-method"></a>移行方法を計画する

フェデレーション ID 管理からパススルー認証とシームレスなシングル サインオン (SSO) に移行する場合、2 つの方法から選ぶことができます。 使用する方法は、お客様が最初に AD FS インスタンスをどのように構成したかによって決まります。

* **Azure AD Connect**。 最初に Azure AD Connect を使用して AD FS を構成した場合は、Azure AD Connect ウィザードを使ってパススルー認証に変更する*必要があります*。

   ‎ユーザー サインインの方法を変更すると、Azure AD Connect によって自動的に **Set-MsolDomainAuthentication** コマンドレットが実行されます。 Azure AD Connect によって、Azure AD テナント内のすべての確認済みフェデレーション ドメインが自動的にフェデレーション解除されます。

   > [!NOTE]
   > 現在、最初に Azure AD Connect を使用して AD FS を構成した場合、ユーザー サインインをパススルー認証に変更したときに、お客様のテナントに含まれているすべてのドメインがフェデレーション解除されることは避けられません。
‎
* **Azure AD Connect と PowerShell**。 この方法は、最初に Azure AD Connect を使用して AD FS を構成していない場合にのみ使用できます。 このオプションでも、Azure AD Connect ウィザードを使用してユーザー サインインの方法を変更する必要があります。 このオプションの主な違いは、ウィザードで **Set-MsolDomainAuthentication** コマンドレットが自動的に実行されないことです。 このオプションでは、どのドメインをどの順序で変換するかを完全に制御できます。

どの方法を使用すればよいかを理解するために、次のセクションの手順を行います。

#### <a name="verify-current-user-sign-in-settings"></a>現在のユーザー サインイン設定を確認する

1. グローバル管理者アカウントを使用して、[Azure AD ポータル](https://aad.portal.azure.com/)にサインインします。
2. **[ユーザー サインイン]** セクションで、次のように設定されていることを確認します。
   * **[フェデレーション]** が **[有効]** に設定されている。
   * **[シームレス シングル サインオン]** が **[無効]** に設定されている。
   * **[パススルー認証]** が **[無効]** に設定されている。

   ![Azure AD Connect の [ユーザー サインイン] セクションの設定を示すスクリーンショット](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image1.png)

#### <a name="verify-how-federation-was-configured"></a>フェデレーションが構成された方法を確認する

1. Azure AD Connect サーバーで、Azure AD Connect を開きます。 **[構成]** をクリックします。
2. **[追加のタスク]** ページで **[現在の構成を表示する]** を選んでから、 **[次へ]** を選択します。<br />
 
   ![[追加のタスク] ページの [現在の構成を表示する] オプションのスクリーンショット](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image2.png)<br />
3. **[ソリューションのレビュー]** ページで、 **[Active Directory Federation Services (AD FS)]** までスクロールします。<br />

   * このセクションに AD FS 構成が表示される場合は、AD FS が最初に Azure AD Connect を使用して構成されたと見なすことができます。 Azure AD Connect の **[ユーザー サインインの変更]** オプションを使用して、ドメインをフェデレーション ID からマネージド ID に変換できます。 このプロセスの詳細については、「**オプション 1:Azure AD Connect を使用してパススルー認証を構成する**」セクションを参照してください。
   * AD FS が現在の設定の一覧に表示されていない場合は、PowerShell を使用して、手動でドメインをフェデレーション ID からマネージド ID に変換する必要があります。 このプロセスの詳細については、「**オプション 2:Azure AD Connect と PowerShell を使用してフェデレーションからパススルー認証に切り替える**」セクションを参照してください。

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
> **SupportsMfa** が **True** に設定されている場合は、第 2 要素チャレンジをユーザー認証フローに挿入するためにオンプレミス多要素認証ソリューションを使用しています。 このセットアップは、Azure AD 認証シナリオでは動作しなくなりました。 
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

認証を行うユーザーが企業ネットワーク内に存在する場合、AD FS によって **InsideCorporateNetwork** 要求が発行されます。 その後、この要求を Azure AD に渡すことができます。 要求は、ユーザーのネットワークの場所に基づいて多要素認証をバイパスするために使用されます。 現在、AD FS でこの機能を利用できるかどうかを判断する方法については、「[フェデレーション ユーザー用の信頼できる IP](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud)」を参照してください。

ドメインがパススルー認証に変換された後、**InsideCorporateNetwork** 要求は使用できません。 この機能の代わりに、[Azure AD の名前付きの場所](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations)を使用できます。

ネームド ロケーションを構成した後は、ネットワークの **[すべての信頼できる場所]** または **[MFA の信頼できる IP]** の値を含めるか除外するために構成されたすべての条件付きアクセス ポリシーを、新しいネームド ロケーションを反映するように更新する必要があります。

条件付きアクセスでの**場所**の条件の詳細については、[Active Directory の条件付きアクセスの場所](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations)に関するページを参照してください。

#### <a name="hybrid-azure-ad-joined-devices"></a>ハイブリッド Azure AD 参加済みデバイス

Azure AD にデバイスを参加させる場合、デバイスがセキュリティとコンプライアンスに関するアクセス基準を満たすよう、条件付きアクセス規則を作成して適用することができます。 また、ユーザーは個人アカウントではなく、職場や学校のアカウントを使用してデバイスにサインインできます。 ハイブリッド Azure AD 参加済みデバイスを使用する場合、Active Directory ドメイン参加済みデバイスを Azure AD に参加させることができます。 フェデレーション環境は、この機能を使用するように設定されている可能性があります。

ドメインがパススルー認証に変換された後もドメインに参加させたすべてのデバイスでハイブリッド参加が機能し続けるようにするには、Windows 10 クライアントでは、Azure AD Connect を使用して、Active Directory コンピューター アカウントを Azure AD に同期させる必要があります。

Windows 8 および Windows 7 のコンピューター アカウントの場合、ハイブリッド参加ではコンピューターを Azure AD に登録するためにシームレス SSO が使用されます。 Windows 10 デバイスの場合のように、Windows 8 および Windows 7 のコンピューター アカウントを同期させる必要はありません。 ただし、Windows 8 および Windows 7 のクライアントには、それらがシームレス SSO を使用して自身を登録できるよう、更新された workplacejoin.exe ファイルを (.msi ファイルを通じて) デプロイする必要があります。 [.msi ファイルをダウンロード](https://www.microsoft.com/download/details.aspx?id=53554)します。

詳細については、[ハイブリッド Azure AD 参加済みデバイスの構成](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup)に関するページを参照してください。

#### <a name="branding"></a>ブランド

組織でより関連性の高い情報を表示するために [AD FS サインイン ページをカスタマイズ](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization)している場合は、同様に [Azure AD サインイン ページをカスタマイズ](https://docs.microsoft.com/azure/active-directory/customize-branding)することを検討してください。

同様のカスタマイズを行うことができますが、変換後は、サインイン ページの外観が多少変更されることが予想されます。 予想される変更について、事前にユーザーに連絡しておくとよいでしょう。

> [!NOTE]
> 組織のブランド化を利用できるのは、Azure Active Directory の Premium ライセンスまたは Basic ライセンスを購入した場合、あるいは Office 365 のライセンスを所有している場合だけです。

## <a name="plan-for-smart-lockout"></a>スマート ロックアウトを計画する

Azure AD スマート ロックアウトでは、ブルートフォース パスワード攻撃を防ぎます。 スマート ロックアウトでは、パススルー認証が使用されていて、Active Directory にアカウント ロックアウト グループ ポリシーが設定されている場合に、オンプレミスの Active Directory アカウントがロックアウトされるのを防ぐことができます。

詳細については、「[Azure Active Directory スマート ロックアウト](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout)」を参照してください。

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

デプロイとサポートの計画で重要な部分は、今後の変更について、ユーザーに必ず事前に通知することです。 ユーザーは、何が起きるかや必要なものについて、事前に知っておく必要があります。

パススルー認証とシームレス SSO の両方がデプロイされた後、Azure AD を通じて認証される Office 365 およびその他のリソースにアクセスするためのユーザーのサインイン エクスペリエンスが変更されます。 ネットワークの外部のユーザーには、Azure AD サインイン ページのみが表示されます。 これらのユーザーは、外部に接続する Web アプリケーション プロキシ サーバーによって表示されるフォームベースのページにはリダイレクトされません。

連絡に関する戦略に次の要素を含めます。

* 以下を使用して、近日公開される機能とリリース済みの機能をユーザーに通知します。
   * 電子メールおよびその他の内部通信チャネル。
   * ポスターなどのビジュアル。
   * 経営幹部による直接の連絡またはその他の連絡。
* 誰が連絡内容をカスタマイズするか、誰が連絡を送るか、およびそのタイミングを決めます。

## <a name="implement-your-solution"></a>ソリューションを実装する

ソリューションの計画は済みました。 これで、実装することができます。 実装には次のコンポーネントが含まれます。

* シームレス SSO の準備。
* サインイン方法のパススルー認証への変更とシームレス SSO の有効化。

### <a name="step-1-prepare-for-seamless-sso"></a>手順 1:シームレス SSO を準備する

デバイスでシームレス SSO を使用する場合は、Active Directory のグループ ポリシーを使って、ユーザーのイントラネット ゾーン設定に Azure AD URL を追加する必要があります。

既定では、Web ブラウザーで、URL から適切なゾーン (インターネットまたはイントラネット) が自動的に判断されます。 たとえば、**http:\/\/contoso/** はイントラネット ゾーンにマップされ、**http:\/\/intranet.contoso.com** はインターネット ゾーンにマップされます (URL にピリオドが含まれているため)。 Azure AD URL と同様に、URL をブラウザーのイントラネット ゾーンに明示的に追加した場合にのみ、ブラウザーから Kerberos チケットがクラウド エンドポイントに送信されます。

必要な変更をデバイスに[ロールアウト](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start)する手順を完了してください。

> [!IMPORTANT]
> この変更を行っても、ユーザーが Azure AD にサインインする方法は変わりません。 ただし、先に進む前に、すべてのデバイスにこの構成を適用することが重要です。 この構成を受け取っていないデバイスでサインインするユーザーは、Azure AD にサインインするために、ユーザー名とパスワードを入力するだけで済みます。

### <a name="step-2-change-the-sign-in-method-to-pass-through-authentication-and-enable-seamless-sso"></a>手順 2:サインイン方法をパススルー認証に変更し、シームレス SSO を有効にする

サインイン方法をパススルー認証に変更し、シームレス SSO を有効にするためのオプションは 2 つあります。

#### <a name="option-a-configure-pass-through-authentication-by-using-azure-ad-connect"></a>オプション A: Azure AD Connect を使用してパススルー認証を構成する

最初に Azure AD Connect を使用して AD FS 環境を構成した場合は、この方法を使用します。 最初に Azure AD Connect を使用して AD FS 環境を構成*しなかった*場合は、この方法を使用できません。

> [!IMPORTANT]
> 次の手順を完了した後、すべてのドメインがフェデレーション ID からマネージド ID に変換されます。 詳細については、「[移行方法を計画する](#plan-the-migration-method)」を確認してください。

まず、ユーザー サインイン方法を変更します。

1. Azure AD Connect サーバーで、Azure AD Connect ウィザードを開きます。
2. **[ユーザー サインインの変更]** を選択してから、 **[次へ]** を選びます。 
3. **[Azure AD に接続]** ページで、グローバル管理者アカウントのユーザー名とパスワードを入力します。
4. **[ユーザー サインイン]** ページで、 **[パススルー認証]** ボタン、 **[シングル サインオンを有効にする]** 、 **[次へ]** の順に選択します。
5. **[シングル サインオンを有効にする]** ページで、ドメイン管理者アカウントの資格情報を入力し、 **[次へ]** を選択します。

   > [!NOTE]
   > シームレス SSO を有効にするには、ドメイン管理者アカウントの資格情報が必要です。 このプロセスでは、管理者特権のアクセス許可を必要とする、以下のアクションが実行されます。 ドメイン管理者アカウントの資格情報は、Azure AD Connect にも Azure AD にも格納されません。 ドメイン管理者アカウントの資格情報は、機能を有効にするためだけに使用されます。 プロセスが正常に終了したら、資格情報は破棄されます。
   >
   > 1. オンプレミスの Active Directory インスタンスに、(Azure AD を表す) AZUREADSSOACC という名前のコンピューター アカウントが作成されます。
   > 2. コンピューター アカウントの Kerberos 復号化キーが、Azure AD と安全に共有されます。
   > 3. Azure AD のサインイン時に使用される 2 つの URL を表す、2 つの Kerberos サービス プリンシパル名 (SPN) が作成されます。

6. **[構成の準備完了]** ページで、 **[構成が完了したら、同期プロセスを開始してください]** チェック ボックスがオンになっていることを確認します。 次に、 **[構成]** を選択します。<br />

   ![[構成の準備完了] ページのスクリーンショット](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image8.png)<br />
7. Azure AD ポータルで、 **[Azure Active Directory]** を選択してから、 **[Azure AD Connect]** を選びます。
8. 以下の設定を確認します。
   * **[フェデレーション]** が **[無効]** に設定されている。
   * **[シームレス シングル サインオン]** が **[有効]** に設定されている。
   * **[パススルー認証]** が **[有効]** に設定されている。<br />

   ![[ユーザー サインイン] セクションの設定を示すスクリーンショット](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image9.png)<br />

次に、 追加の認証方法をデプロイします。

1. Azure portal で、 **[Azure Active Directory]**  >  **[Azure AD Connect]** の順に移動して、 **[パススルー認証]** を選択します。
2. **[パススルー認証]** ページで、 **[ダウンロード]** ボタンを選択します。
3. **[エージェントのダウンロード]** ページで、 **[Accept terms and download]\(利用規約に同意してダウンロード\)** を選択します。

   追加の認証エージェントのダウンロードが開始されます。 セカンダリ認証エージェントは、ドメイン参加済みサーバーにインストールします。 

   > [!NOTE]
   > 最初のエージェントは、Azure AD Connect ツールの **[ユーザー サインイン]** セクションで行われた構成変更の一部として、常に Azure AD Connect サーバー自体にインストールされます。 追加の認証エージェントはすべて、別のサーバーにインストールします。 2 個または 3 個の追加の認証エージェントを使用できるようにすることをお勧めします。 

4. 認証エージェントのインストールを実行します。 インストール中に、グローバル管理者アカウントの資格情報を入力する必要があります。

   ![Microsoft Azure AD Connect 認証エージェント パッケージのページにある [インストール] ボタンを示すスクリーンショット](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image11.png)

   ![サインイン ページを示すスクリーンショット](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image12.png)

5. 認証エージェントがインストールされたら、パススルー認証エージェントの正常性ページに戻って、追加のエージェントの状態を確認できます。

「[テストと次のステップ](#testing-and-next-steps)」に進みます。

> [!IMPORTANT]
> 「**オプション B:Azure AD Connect と PowerShell を使用してフェデレーションからパススルー認証に切り替える**」セクションはスキップします。 そのセクションの手順は、サインイン方法をパススルー認証に変更し、シームレス SSO を有効にするオプション A を選択した場合、適用されません。 

#### <a name="option-b-switch-from-federation-to-pass-through-authentication-by-using-azure-ad-connect-and-powershell"></a>オプション B: Azure AD Connect と PowerShell を使用してフェデレーションからパススルー認証に切り替える

フェデレーション ドメインを最初に Azure AD Connect を使用して構成しなかった場合は、このオプションを使用します。

まず、次のようにしてパススルー認証を有効にします。

1. Azure AD Connect サーバーで、Azure AD Connect ウィザードを開きます。
2. **[ユーザー サインインの変更]** を選択してから、 **[次へ]** を選びます。
3. **[Azure AD に接続]** ページで、グローバル管理者アカウントのユーザー名とパスワードを入力します。
4. **[ユーザー サインイン]** ページで、 **[パススルー認証]** ボタンを選択します。 **[シングル サインオンを有効にする]** を選択してから、 **[次へ]** を選びます。
5. **[シングル サインオンを有効にする]** ページで、ドメイン管理者アカウントの資格情報を入力し、 **[次へ]** を選択します。

   > [!NOTE]
   > シームレス SSO を有効にするには、ドメイン管理者アカウントの資格情報が必要です。 このプロセスでは、管理者特権のアクセス許可を必要とする、以下のアクションが実行されます。 ドメイン管理者アカウントの資格情報は、Azure AD Connect にも Azure AD にも格納されません。 ドメイン管理者アカウントの資格情報は、機能を有効にするためだけに使用されます。 プロセスが正常に終了したら、資格情報は破棄されます。
   > 
   > 1. オンプレミスの Active Directory インスタンスに、(Azure AD を表す) AZUREADSSOACC という名前のコンピューター アカウントが作成されます。
   > 2. コンピューター アカウントの Kerberos 復号化キーが、Azure AD と安全に共有されます。
   > 3. Azure AD のサインイン時に使用される 2 つの URL を表す、2 つの Kerberos サービス プリンシパル名 (SPN) が作成されます。

6. **[構成の準備完了]** ページで、 **[構成が完了したら、同期プロセスを開始してください]** チェック ボックスがオンになっていることを確認します。 次に、 **[構成]** を選択します。<br />

   ‎![[構成の準備完了] ページと [構成] ボタンを示すスクリーンショット](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image18.png)<br />
   **[構成]** を選択した場合は、以下の手順が行われます。

   1. パススルー認証エージェントがインストールされます。
   2. パススルー機能が有効になります。
   3. シームレス SSO が有効になります。

7. 以下の設定を確認します。
   * **[フェデレーション]** が **[有効]** に設定されている。
   * **[シームレス シングル サインオン]** が **[有効]** に設定されている。
   * **[パススルー認証]** が **[有効]** に設定されている。
   
   ![[ユーザー サインイン] セクションの設定を示すスクリーンショット](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image19.png)
8. **[パススルー認証]** を選択し、状態が **[アクティブ]** であることを確認します。<br />
   
   認証エージェントがアクティブでない場合は、次の手順でドメインの変換プロセスを続行する前に、いくつかの[トラブルシューティング手順](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication)を完了します。 パススルー認証エージェントが正常にインストールされたことと、Azure portal でそれらの状態が **[アクティブ]** になっていることを確認する前にドメインを変換すると、認証が停止する危険性があります。

次に、追加の認証エージェントをデプロイします。

1. Azure portal で、 **[Azure Active Directory]**  >  **[Azure AD Connect]** の順に移動して、 **[パススルー認証]** を選択します。
2. **[パススルー認証]** ページで、 **[ダウンロード]** ボタンを選択します。 
3. **[エージェントのダウンロード]** ページで、 **[Accept terms and download]\(利用規約に同意してダウンロード\)** を選択します。
 
   認証エージェントのダウンロードが開始されます。 セカンダリ認証エージェントは、ドメイン参加済みサーバーにインストールします。

   > [!NOTE]
   > 最初のエージェントは、Azure AD Connect ツールの **[ユーザー サインイン]** セクションで行われた構成変更の一部として、常に Azure AD Connect サーバー自体にインストールされます。 追加の認証エージェントはすべて、別のサーバーにインストールします。 2 個または 3 個の追加の認証エージェントを使用できるようにすることをお勧めします。
 
4. 認証エージェントのインストールを実行します。 インストール中に、グローバル管理者アカウントの資格情報を入力する必要があります。<br />

   ![Microsoft Azure AD Connect 認証エージェント パッケージのページにある [インストール] ボタンを示すスクリーンショット](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image23.png)<br />
   ![サインイン ページを示すスクリーンショット](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image24.png)<br />
5. 認証エージェントがインストールされた後、パススルー認証エージェントの正常性ページに戻って、追加のエージェントの状態を確認できます。

この時点では、フェデレーション認証はまだアクティブであり、ドメインのために機能しています。 デプロイを続行するには、各ドメインをフェデレーション ID からマネージド ID に変換し、パススルー認証でドメインに対する認証要求の処理が開始されるようにする必要があります。

すべてのドメインを同時に変換する必要はありません。 運用環境テナントのテスト ドメインや、ユーザー数が最も少ないドメインから開始することができます。

Azure AD PowerShell モジュールを使用して、変換を完了します。

1. PowerShell で、全体管理者アカウントを使用して Azure AD にサインインします。
2. 最初のドメインを変換するには、次のコマンドを実行します。
 
   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```
 
3. Azure AD ポータルで、 **[Azure Active Directory]**  >  **[Azure AD Connect]** の順に選択します。
4. フェデレーション ドメインをすべて変換した後、次の設定を確認します。
   * **[フェデレーション]** が **[無効]** に設定されている。
   * **[シームレス シングル サインオン]** が **[有効]** に設定されている。
   * **[パススルー認証]** が **[有効]** に設定されている。<br />

   ![[ユーザー サインイン] セクションの設定を示すスクリーンショット](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image26.png)<br />

## <a name="testing-and-next-steps"></a>テストと次のステップ

パススルー認証を確認する場合と、変換プロセスを終了する場合は、以下のタスクを実行します。

### <a name="test-pass-through-authentication"></a>パススルー認証をテストする 

テナントでフェデレーション ID が使用されていたときに、ユーザーは Azure AD サインイン ページから AD FS 環境にリダイレクトされていました。 フェデレーション認証ではなく、パススルー認証を使用するようにテナントが構成されたので、ユーザーは AD FS にリダイレクトされません。 代わりに、ユーザーは Azure AD サインイン ページで直接サインインします。

パススルー認証をテストするには、次のようにします。

1. シームレス SSO によって自動的にサインインされないように、InPrivate モードで Internet Explorer を開きます。
2. Office 365 のサインイン ページ ([https://portal.office.com](https://portal.office.com/)) に移動します。
3. ユーザーの UPN を入力し、 **[次へ]** を選択します。 必ず、オンプレミスの Active Directory インスタンスから同期されており、以前はフェデレーション認証を使用していた、ハイブリッド ユーザーの UPN を入力してください。 ユーザー名とパスワードを入力するページが表示されます。

   ![ユーザー名を入力するサインイン ページを示すスクリーンショット](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image27.png)

   ![パスワードを入力するサインイン ページを示すスクリーンショット](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image28.png)

4. パスワードを入力して **[サインイン]** を選択すると、Office 365 ポータルにリダイレクトされます。

   ![Office 365 ポータルを示すスクリーンショット](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image29.png)

### <a name="test-seamless-sso"></a>シームレス SSO をテストする

シームレス SSO をテストするには、次のようにします。

1. 企業ネットワークに接続されているドメイン参加済みマシンにサインインします。
2. Internet Explorer または Chrome で、次のいずれかの URL に移動します ("contoso" はご自分のドメインに置き換えてください)。

   * https:\/\/myapps.microsoft.com/contoso.com
   * https:\/\/myapps.microsoft.com/contoso.onmicrosoft.com

   ユーザーはすぐに Azure AD サインイン ページにリダイレクトされ、"サインインしようとしています" というメッセージが表示されます。 ユーザーにはユーザー名やパスワードの入力は求められません。<br />

   ![Azure AD サインイン ページとメッセージを示すスクリーンショット](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image30.png)<br />
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

この機能を確認または有効にする方法については、[userPrincipalName 更新の同期](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsyncservice-features)に関するページを参照してください。

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>シームレス SSO の Kerberos 復号化キーのロールオーバー

(Azure AD を表す) AZUREADSSOACC コンピューター アカウントの Kerberos 復号化キーを頻繁にロールオーバーすることが重要です。 AZUREADSSOACC コンピューター アカウントは、オンプレミスの Active Directory フォレストで作成されます。 Active Directory ドメイン メンバーがパスワードの変更を送信する方法に合わせて、少なくとも 30 日ごとに Kerberos 復号化キーをロールオーバーすることを強くお勧めします。 関連するデバイスが AZUREADSSOACC コンピューター アカウント オブジェクトにアタッチされていないため、ロールオーバーは手動で実行する必要があります。

Azure AD Connect を実行しているオンプレミス サーバーで、シームレス SSO Kerberos 復号化キーのロールオーバーを開始します。

詳細については、[AZUREADSSOACC コンピューター アカウントの Kerberos 暗号化解除キーをロールオーバーする方法](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq)に関するページを参照してください。

## <a name="monitoring-and-logging"></a>監視およびログ記録

ソリューションの可用性を維持するには、認証エージェントを実行するサーバーを監視します。 認証エージェントでは、一般的なサーバー パフォーマンス カウンターに加えて、認証の統計情報とエラーを把握するのに役立つパフォーマンス オブジェクトが公開されます。

認証エージェントによって、操作のログが、アプリケーションとサービス ログ\Microsoft\AzureAdConnect\AuthenticationAgent\Admin にある Windows イベント ログに記録されます。

トラブルシューティングのためのログを有効にすることもできます。

詳細については、「[Azure Active Directory パススルー認証のトラブルシューティング](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-Pass-through-authentication)」を参照してください。

## <a name="next-steps"></a>次の手順

* [Azure AD Connect の設計概念](plan-connect-design-concepts.md)について学習する。
* [適切な認証](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)を選択する。
* [サポートされているトポロジ](plan-connect-design-concepts.md)について学習する。
