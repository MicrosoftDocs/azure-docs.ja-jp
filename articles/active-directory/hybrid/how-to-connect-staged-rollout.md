---
title: Azure AD Connect:段階的なロールアウトによるクラウド認証 | Microsoft Docs
description: この記事では、段階的なロールアウトを使用して、フェデレーション認証からクラウド認証に移行する方法について説明します。
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/03/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2038587aebe8fe98bd1adcccef6b895ec7370656
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176860"
---
# <a name="migrate-to-cloud-authentication-using-staged-rollout-preview"></a>段階的なロールアウトを使用してクラウド認証に移行する (プレビュー)

段階的なロールアウトにより、ドメインを切り替える前に、Azure AD Multi-Factor Authentication (MFA)、条件付きアクセス、漏洩した資格情報の ID 保護、ID 管理などのクラウド認証機能を使用して、一連のユーザーを選択的にテストすることができます。  この記事では、スイッチの作成方法について説明します。 段階的なロールアウトを開始する前に、次の条件が 1 つ以上当てはまる場合の影響を考慮する必要があります：
    
-  現在、オンプレミスの Multi-Factor 認証サーバーを使用しています。 
-  認証にスマート カードを使用しています。 
-  お使いのサーバーは、特定のフェデレーション専用機能を提供しています。

この機能を試す前に、適切な認証方法の選択に関するガイドを確認するようお勧めします。 詳細については、「メソッドの比較」の表を参照して、[Azure Active Directory ハイブリッド ID ソリューションの適切な認証方法を選択する](./choose-ad-authn.md#comparing-methods) を参照してください。

この機能の概要については、「Azure Active Directory：段階的なロールアウトとは？」 ビデオ：

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>前提条件

-   フェデレーション ドメインを持つ Azure Active Director (Azure AD) テナントがあること。

-   次の 2 つのオプションのいずれかに移行することを決定しました：
    - **オプション A** - *パスワード ハッシュの同期 (sync)*  + *シームレス シングル サインオン (SSO)* 。  詳細については、[パスワード ハッシュ同期](whatis-phs.md)および[シームレス SSO](how-to-connect-sso.md) に関する記事を参照してください
    - **オプション B** - *パス スルー認証* + *シームレス SSO*。  詳細については、「[パススルー認証とは](how-to-connect-pta.md)」を参照してください  
    
    *シームレス SSO* は、オプションですが、企業ネットワーク内からドメインに参加しているマシンを実行しているユーザーに対して、サイレント サインイン エクスペリエンスを実現できるようにするようお勧めします。

-   クラウド認証に移行するユーザーに必要なすべての適切なテナント ブランドと条件付きアクセスポリシーを構成しました。

-   Azure AD Multi-Factor Authentication の使用を計画している場合は、[セルフサービス パスワード リセット (SSPR) と Multi-Factor Authentication の統合された登録](../authentication/concept-registration-mfa-sspr-combined.md)を使用して、ユーザーが認証方法を 1 回で登録できるようにすることをお勧めします。 注: ステージングされたロールアウト中に MyProfile ページを使用してパスワードのリセットやパスワードの変更のために SSPR を使用するときは、Azure AD Connect は新しいパスワード ハッシュを同期する必要があり、これにはリセット後最大 2 分かかることがあります。

-   この段階的なロールアウト機能を使用するには、テナントのグローバル管理者である必要があります。

-   特定の AD フォレストで *シームレス SSO* を有効にするには、ドメイン管理者である必要があります。

-  Hybrid Azure AD または Azure AD 参加を展開する場合、Windows 10 1903 Update にアップグレードする必要があります。


## <a name="supported-scenarios"></a>サポートされるシナリオ

次のシナリオは、段階的なロールアウトがサポートされています。 この機能は、次の場合にのみ機能します：

- Azure AD Connect を使用して、Azure AD にプロビジョニングされているユーザー。 「クラウドのみ」のユーザーには適用されません。

- ブラウザーおよび *最新の認証* クライアント上でのユーザー サインイン トラフィック。 レガシ認証を使用するアプリケーションまたはクラウド サービスは、フェデレーション認証のフローにフォールバックします。 例としては、最新の認証が無効になっている Exchange オンラインや、最新の認証をサポートしていない Outlook 2010 があります。

- グループ サイズは現在 50,000 ユーザーに制限されています。  50,000 ユーザーよりも大きいグループがある場合は、このグループを複数のグループに分割して、段階的なロールアウトを行うことをお勧めします。

- Windows 10 バージョン 1903 以降の、フェデレーション サーバーへの通信経路を使用しない Windows 10 ハイブリッド参加または Azure AD 参加のプライマリ更新トークンの取得 (ユーザーの UPN がルーティング可能であり、ドメイン サフィックスが Azure AD で検証されている場合)。

## <a name="unsupported-scenarios"></a>サポートされていないシナリオ

次のシナリオは、段階的なロールアウトではサポートされていません：

- POP3 や SMTP などのレガシ認証はサポートされていません。

- 特定のアプリケーションは、認証中に「domain_hint」クエリ パラメーターを Azure AD に送信します。 これらのフローは続行され、段階的なロールアウトが有効になっているユーザーは、認証にフェデレーションを引き続き使用します。

<!-- -->

- 管理者は、セキュリティ グループを使用してクラウド認証をロールアウトできます。 オンプレミスの Active Directory セキュリティグループを使用しているときに、同期の待機時間を回避するには、クラウド セキュリティ グループを使用するようお勧めします。 次の条件が適用されます：

    - 機能ごとに最大 10 個のグループを使用できます。 つまり、*パスワードハッシュ同期*、*パススルー認証*、*シームレス SSO*  に対して、それぞれ 10 個のグループを使用できます。
    - 入れ子になったグループは *サポートされていません*。 このスコープはパブリックプレビューにも適用されます。
    - 動的グループは、段階的なロールアウトでは *サポートされていません*。
    - グループ内の連絡先オブジェクトがグループ フォームの追加をブロックします。

- 段階的なロールアウトで、セキュリティ グループを初めて追加する時点では、UX タイムアウトを回避するために、200 ユーザーに制限されます。グループを追加した後、必要に応じて、そのグループにさらにユーザーを直接追加できます。

- ユーザーが段階的にロールアウトしている間、EnforceCloudPasswordPolicyForPasswordSyncedUsers が有効な場合、パスワードの有効期限ポリシーは 90 日に設定され、これをカスタマイズするオプションはありません。 

- 1903 より前の Windows 10 バージョンの、Windows 10 ハイブリッド参加または Azure AD 参加のプライマリ更新トークンの取得。 このシナリオは、ユーザーのサインインが段階的なロールアウトのスコープ内にある場合でも、フェデレーション サーバーの WS-Trust エンドポイントにフォールバックします。

- すべてのバージョンの Windows 10 ハイブリッド参加または Azure AD 参加のプライマリ更新トークンの取得 (ユーザーのオンプレミス UPN がルーティング可能でない場合)。 このシナリオは、段階的なロールアウトのモードでは WS-Trust エンドポイントにフォールバックしますが、段階的な移行が完了し、ユーザーのサインオンがフェデレーション サーバーに依存しなくなったときに機能しなくなります。

  >[!NOTE]
  >それでも、フェデレーションからクラウド認証への最終的な切り替えを、 Azure AD Connect または PowerShell を使用して行う必要があります。 段階的ロールアウトによって、ドメインがフェデレーションからマネージドに切り替えられることはありません。  ドメイン カットオーバーの詳細については、[フェデレーションからパスワード ハッシュ同期に移行する](plan-migrate-adfs-password-hash-sync.md#step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso)方法および[フェデレーションからパススルー認証に移行する](plan-migrate-adfs-password-hash-sync.md#step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso)方法に関する記事を参照してください
  
## <a name="get-started-with-staged-rollout"></a>段階的なロールアウトを使ってみる

段階的なロールアウトを使用して *パスワードハッシュ同期* サインインをテストするには、次のセクションの作業前の指示に従ってください。

使用する PowerShell コマンドレットの詳細については、「[Azure AD 2.0 プレビュー](/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)」を参照してください。

## <a name="pre-work-for-password-hash-sync"></a>パスワード ハッシュ同期の事前作業

1. Azure AD Connect の [[オプション機能]](how-to-connect-install-custom.md#optional-features) ページから、*パスワード ハッシュ* 同期を有効にします。 

   ![Azure Active Directory Connect の「オプション機能」ページのスクリーンショット](media/how-to-connect-staged-rollout/sr1.png)

1. すべてのユーザーのパスワード ハッシュが、Azure AD に同期されるように、完全な *パスワード ハッシュ同期* サイクルが実行されていることを確認します。 *パスワードハッシュ同期* の状態を確認するには、[Azure Active Directory Connect同期によるパスワードハッシュ同期のトラブルシューティング](tshoot-connect-password-hash-synchronization.md) のPowerShell 診断を使用できます。

   ![AAD Connect のトラブルシューティング ログのスクリーンショット](./media/how-to-connect-staged-rollout/sr2.png)

段階的なロールアウトを使用して *パススルー認証* サインインをテストする場合は、次のセクションの前の作業前の手順に従って有効にします。

## <a name="pre-work-for-pass-through-authentication"></a>パススルー認証の事前作業

1. *パススルー認証* エージェントを実行する、Windows Server 2012 R2 以降を実行しているサーバーを特定します。 

   Azure AD Connect サーバーを *選択しない* でください。  そのサーバーがドメインに参加していて、選択したユーザーを Active Directory で認証し、送信ポートや URL で Azure AD と通信できることを確認します。 詳細については、クイックスタートの「ステップ1：前提条件を確認する」[のセクションを確認します：Azure AD シームレス シングル サインオン](how-to-connect-sso-quick-start.md)。

1. [Microsoft Azure AD Connect 認証エージェントをダウンロード](https://aka.ms/getauthagent)して、サーバーにインストールします。 

1. [高可用性](how-to-connect-sso-quick-start.md)を有効にするには、他のサーバーに追加の認証エージェントをインストールします。

1. [スマート ロックアウトの設定](../authentication/howto-password-smart-lockout.md)が適切に構成されているか確認します。 そうすることで、ユーザーのオンプレミスの Active Directory Domain Services アカウントが悪意のある攻撃者によってロックアウトされないようにすることができます。

段階的なロールアウトのために選択するサインイン方法 (*パスワードハッシュ同期* または *パススルー認証*) に関係なく、*シームレス SSO* を有効にするようお勧めします。 *シームレス SSO* を有効にするには、次のセクションの作業前の手順に従います。

## <a name="pre-work-for-seamless-sso"></a>シームレス SSO の事前作業

PowerShell を使用して、Active Directory Domain Services フォレストで *シームレス SSO* を有効にします。 複数の Active Directory フォレストがある場合は、各フォレストに対して個別に有効にします。 *シームレス SSO* は、ステージングされたロールアウトを選択したユーザーに対してのみトリガーされます。 既存のフェデレーション設定には影響しません。

次の手順に従って、*シームレス SSO* を有効にします：

1. Azure AD Connect サーバーにログインします。

2. *%programfiles%\\Microsoft Azure Active Directory Connect* フォルダーに移動します。

3. 次のコマンドレットを実行して、*シームレス SSO* PowerShell モジュールをインポートします： 

   `Import-Module .\AzureADSSO.psd1`

4. PowerShell を管理者として実行します。 PowerShell で、`New-AzureADSSOAuthenticationContext` を呼び出します。 このコマンドを実行すると、テナントのグローバル管理者資格情報を入力できるペインが開きます。

5. `Get-AzureADSSOStatus | ConvertFrom-Json` を呼び出します。 このコマンドは、この機能が有効になっている Active Directory フォレストの一覧 (「ドメイン」リストを参照) を表示します。 これは、既定ではテナント レベルで false に設定されています。

   ![Windows PowerShell の出力例](./media/how-to-connect-staged-rollout/sr3.png)

6. `$creds = Get-Credential` を呼び出します。 プロンプトが表示されたら、目的の Active Directory フォレストのドメイン管理者の資格情報を入力します。

7. `Enable-AzureADSSOForest -OnPremCredentials $creds` を呼び出します。 このコマンドは、*シームレス SSO* に必要なこの特定の Active Directory フォレスト用のオンプレミスのドメイン コントローラーから AZUREADSSOACC コンピューターのアカウントを作成します。

8. *シームレス SSO* を使用するには、URL がイントラネット ゾーンに含まれている必要があります。 グループポリシーを使用して、これらの Url をデプロイする方法については、[クイックスタート：Azure AD シームレス シングル サインオン](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature)。

9. 完全なチュートリアルについては、*シームレス SSO* 用の [デプロイ計画](https://aka.ms/SeamlessSSODPDownload) をダウンロードすることもできます。

## <a name="enable-staged-rollout"></a>段階的なロールアウトの有効化

特定の機能 (*パススルー認証*、*パスワードハッシュ同期*、または *シームレスSSO*) をグループ内の選択したユーザーセットにロールアウトするには、次のセクションの手順に従います。

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>テナントの特定の機能を段階的なロールアウトを有効にする

これらのオプションのいずれかをロールアウトできます：

- **オプション A** - *パスワードハッシュ同期* + *シームレスSSO*
- **オプション B** - *パス スルー認証* + *シームレス SSO*
- **サポートされていない** - *パスワードハッシュ同期* + *パススルー認証* + *シームレスSSO*

次の操作を行います。

1. UX のプレビューにアクセスするには、[Azure AD ポータル](https://aka.ms/stagedrolloutux)にサインインします。

2. **[マネージド ユーザー サインインの段階的なロールアウトを有効にする (プレビュー)]**  をクリックします。

   たとえば、*オプション A* を有効にする場合は、次の図に示すように、**パスワードハッシュ同期** と **シームレス シングル サインオン**  コントロールを **[有効]** にします。

   ![Azure AD Connect ページ](./media/how-to-connect-staged-rollout/sr4.png)

   ![「段階的なロールアウト機能を有効にする (プレビュー) 」ページ](./media/how-to-connect-staged-rollout/sr5.png)

3. 各グループを機能に追加して、*パススルー認証* と *シームレス SSO* を有効にします。 UX のタイムアウトを回避するには、最初に、セキュリティ グループに含まれるメンバーが 200 人以下であることを確認してください。

   ![[パスワードハッシュ同期のグループを管理する (プレビュー) ] ページ](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >グループ内のメンバーは、段階的なロールアウトに対して自動的に有効になります。 段階的なロールアウトでは、入れ子になったグループと動的グループはサポートされていません。
   >新しいグループを追加するとき、グループ内のユーザー (新しいグループ 1 つにつき最大 200 ユーザー) が、即座にマネージド認証を使用するように更新されます。 グループを編集する (ユーザーを追加または削除する) 場合、変更が有効になるまでに最大 24 時間かかることがあります。
   >シームレス SSO は、ユーザーがシームレス SSO グループ内にあり、PTA または PHS グループのいずれかにも含まれている場合にのみ適用されます。

## <a name="auditing"></a>監査

段階的なロールアウトのために実行するさまざまなアクションの監査イベントが有効になりました：

- *パスワード ハッシュ同期*、*パススルー認証*、 *シームレス SSO* に対して段階的なロールアウトを有効にする場合の監査イベント。

  >[!NOTE]
  >段階的なロールアウトを使用して、監査イベントが *シームレス SSO* を [有効]にしたときにログに記録されます。

  ![[機能のロールアウトポリシーの作成] ウィンドウ - [アクティビティ] タブ](./media/how-to-connect-staged-rollout/sr7.png)

  ![[機能のロールアウトポリシーの作成] ウィンドウ - [変更されたプロパ ティ] タブ](./media/how-to-connect-staged-rollout/sr8.png)

- *パスワード ハッシュ同期*、*パススルー認証*、*シームレス SSO* に対してグループを追加したときの監査イベント。

  >[!NOTE]
  >グループが段階的なロールアウトのために、*パスワード ハッシュ同期* に追加されたときにログに記録される監査イベント。

  ![[機能ロールアウトへのグループの追加] ペイン - [アクティビティ] タブ](./media/how-to-connect-staged-rollout/sr9.png)

  ![[機能ロールアウトへのグループの追加] ペイン - [変更されたプロパティ] タブ](./media/how-to-connect-staged-rollout/sr10.png)

- グループに追加されたユーザーが、段階的なロールアウトに対して有効にされたときの監査イベント。

  ![[機能ロールアウトへのユーザーの追加] ペイン - [アクティビティ] タブ](media/how-to-connect-staged-rollout/sr11.png)

  ![[機能ロールアウトへのユーザーの追加] ペイン - [ターゲット] タブ](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>検証

*パスワード ハッシュ同期* または *パススルー認証* を使用したサインイン (ユーザー名とパスワードによるサインイン) をテストするには、次のようにします：

1. エクストラネットで、プライベート ブラウザー セッションの [アプリ ページ](https://myapps.microsoft.com) に移動し、段階的なロールアウト用に選択したユーザー アカウントの UserPrincipalName (UPN) を入力します。

   段階的なロールアウトの対象となっているユーザーは、フェデレーションログインページにリダイレクトされません。 代わりに、Azure AD テナントブランドのサインインページでサインインするように求められます。

1. UserPrincipalName でフィルター処理して、[Azure AD サインイン アクティビティ レポート](../reports-monitoring/concept-sign-ins.md)にサインインが正常に表示されていることを確認します。

*シームレス SSO* を使用したサインインをテストするには、次のようにします:

1. イントラネットで、プライベート ブラウザー セッションの [アプリ ページ](https://myapps.microsoft.com) に移動し、段階的なロールアウト用に選択したユーザー アカウントの UserPrincipalName (UPN) を入力します。

   そのユーザーが *シームレスな SSO* の段階的なロールアウトの対象になっている場合は、「サインインしようとしています...」というメッセージが表示されてから、サイレントにサインインが実行されます。

1. UserPrincipalName でフィルター処理して、[Azure AD サインイン アクティビティ レポート](../reports-monitoring/concept-sign-ins.md)にサインインが正常に表示されていることを確認します。

   選択した段階的なロールアウト ユーザーの Active Directory フェデレーションサービス (AD FS) (AD FS) でまだ発生しているユーザーのサインインを追跡するには、[AD FS トラブルシューティングの手順に従います：イベントとログ](/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events)。 サード パーティのフェデレーション プロバイダーで、これを確認する方法については、ベンダーのドキュメントを確認してください。

## <a name="remove-a-user-from-staged-rollout"></a>段階的なロールアウトからユーザーを削除する

ユーザーをグループから削除すると、そのユーザーの段階的なロールアウトが無効になります。 段階的ロールアウト機能を無効にするには、コントロールを **[無効]** に戻します。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

**Q:この機能を運用環境で使用できますか？**

A:はい。この機能は運用テナントで使用できますが、まずテスト テナントでこの機能を試すようお勧めします。

**Q:この機能を使用して、一部のユーザーがフェデレーション認証を使用し、その他のユーザーがクラウド認証を使用するという永続的な "共存" を維持することはできますか？**

A:いいえ。この機能は、クラウド認証をテストする目的で設計されています。 少数のユーザー グループのテストが成功したら、クラウド認証に移行する必要があります。 予期しない認証フローにつながる可能性があるため、永続的な混在状態は、お勧めしません。

**Q:PowerShell を使用して段階的なロールアウトを実行できますか？**

A:はい。 PowerShell を使用して段階的なロールアウトを実行する方法については、「[Azure AD プレビュー」](/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)を参照してください。

## <a name="next-steps"></a>次のステップ
- [Azure Active Directory 2.0 プレビュー](/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
- [サインイン方法をパスワード ハッシュ同期に変更する](plan-migrate-adfs-password-hash-sync.md#step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso)
- [サインイン方法をパススルー認証に変更する](plan-migrate-adfs-password-hash-sync.md#step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso)
