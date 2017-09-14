---
title: "Azure Active Directory 概念実証戦略の構成要素 | Microsoft Docs"
description: "ID とアクセスの管理のシナリオを探索して迅速に実装します。"
services: active-directory
keywords: "azure active directory, 戦略, 概念実証, PoC"
documentationcenter: 
author: dstefanMSFT
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: dstefan
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 671f2d386aca23bc5c02d0e0ab23e6d9d79a8693
ms.contentlocale: ja-jp
ms.lasthandoff: 08/16/2017

---
# <a name="azure-active-directory-proof-of-concept-playbook-building-blocks"></a>Azure Active Directory 概念実証戦略: 構成要素

## <a name="catalog-of-roles"></a>ロールのカタログ

| 役割 | Description | 概念実証 (PoC) の責任範囲 |
| --- | --- | --- |
| **ID アーキテクチャ/開発チーム** | このチームは、通常、ソリューションを設計し、プロトタイプを実装して、承認を得たうえで最後にオペレーションへの引き渡しを行います。 | 必要な環境を提供します。また管理の容易さの観点からさまざまなシナリオを評価する役割を担います。 |
| **オンプレミス ID オペレーション チーム** | オンプレミスのさまざまな ID ソースを管理します (Active Directory フォレスト、LDAP ディレクトリ、人事システム、フェデレーション ID プロバイダー)。 | PoC のシナリオに必要なオンプレミス リソースへのアクセスを提供します。<br/>このチームの関与はできるだけ少なくする必要があります。|
| **アプリケーション テクニカル オーナー** | Azure AD と連携するさまざまなクラウド アプリとクラウド サービスのテクニカル オーナーです。 | SaaS アプリケーション (テスト対象のインスタンス) についての詳細を提供します。 |
| **Azure AD グローバル管理者** | Azure AD の構成を管理します。 | 同期サービスを構成するための資格情報を提供します。 通常、PoC 段階は ID アーキテクチャと同じチームが担当し、運用段階では別々のチームが担当します。|
| **データベース チーム** | データベース インフラストラクチャの所有者です。 | 特定のシナリオを準備するために SQL 環境 (ADFS または Azure AD Connect) へのアクセスを提供します。<br/>このチームの関与はできるだけ少なくする必要があります。 |
| **ネットワーク チーム** | ネットワーク インフラストラクチャの所有者です。 | 同期サーバーがデータ ソースとクラウド サービスに適切にアクセスするために、ネットワーク レベルで必要なアクセスを提供します (ファイアウォール規則、開放ポート、IPSec 規則など)。 |
| **セキュリティ チーム** | セキュリティ戦略を定義し、さまざまなソースからのセキュリティ レポートを分析して、そこで得られた知見を確実に実行に移します。 | 目標となるセキュリティ評価のシナリオを提供します。 |

## <a name="common-prerequisites-for-all-building-blocks"></a>すべての構成要素に共通の前提条件

Azure AD Premium を使ったすべての POC で必要になるいくつかの前提条件を以下に示します。

| 前提条件 | リソース |
| --- | --- |
| 有効な Azure サブスクリプションで定義された Azure AD テナント | [Azure Active Directory テナントを取得する方法](active-directory-howto-tenant.md)<br/>**注:** Azure AD Premium ライセンスと環境が既にある場合は、https://aka.ms/accessaad にアクセスして無制限サブスクリプションを取得できます。 <br/>詳細情報: https://blogs.technet.microsoft.com/enterprisemobility/2016/02/26/azure-ad-mailbag-azure-subscriptions-and-azure-ad-2/ および https://technet.microsoft.com/library/dn832618.aspx |
| 定義済みかつ検証済みのドメイン | [Azure Active Directory へのカスタム ドメイン名の追加](active-directory-domains-add-azure-portal.md)<br/>**注:** Power BI など一部のワークロードでは、Azure AD テナントが暗黙的にプロビジョニングされています。 特定のドメインがテナントに関連付けられているかどうかを確認するには、https://login.microsoftonline.com/{domain}/v2.0/.well-known/openid-configuration にアクセスします。 正常に応答が得られた場合、そのドメインはテナントに割り当て済みであり、引き継ぎが必要であると考えられます。 その場合の詳しいガイダンスについては、Microsoft にお問い合わせください。 引き継ぎの方法については、「[Azure のセルフサービス サインアップについて](active-directory-self-service-signup.md)」を参照してください。 |
| Azure AD Premium または EMS 試用版が有効であること | [Azure Active Directory Premium が 1 か月間無料](https://azure.microsoft.com/trial/get-started-active-directory/) |
| Azure AD Premium または EMS のライセンスを PoC ユーザーに割り当て済みであること | [Azure Active Directory での自分とユーザーのライセンスの取得](active-directory-licensing-get-started-azure-portal.md) |
| Azure AD 全体管理者の資格情報 | [Azure Active Directory での管理者ロールの割り当て](active-directory-assign-admin-roles-azure-portal.md) |
| フォールバックとしての並列ラボ環境 (省略可、ただし強く推奨) | [Azure AD Connect の前提条件](./connect/active-directory-aadconnect-prerequisites.md) |

## <a name="directory-synchronization---password-hash-sync-phs---new-installation"></a>ディレクトリ同期 - パスワード ハッシュ同期 (PHS) - 新規インストール

推定所要時間: 1 時間 (1,000 PoC ユーザー未満の場合)

### <a name="pre-requisites"></a>前提条件

| 前提条件 | リソース |
| --- | --- |
| Azure AD Connect を実行するためのサーバー | [Azure AD Connect の前提条件](./connect/active-directory-aadconnect-prerequisites.md) |
| ターゲット POC ユーザー (同じドメインで、いずれかのセキュリティ グループに所属) と OU | [Azure AD Connect のカスタム インストール](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) |
| POC に必要な Azure AD Connect 機能がわかっていること | [Active Directory を Azure Active Directory と接続する - 同期機能を構成する](./connect/active-directory-aadconnect.md#configure-sync-features) |
| オンプレミス環境とクラウド環境に必要な資格情報を持っていること  | [Azure AD Connect: アカウントとアクセス許可](./connect/active-directory-aadconnect-accounts-permissions.md) |

### <a name="steps"></a>手順

| 手順 | リソース |
| --- | --- |
| Azure AD Connect 最新バージョンをダウンロードします。 | [Microsoft Azure Active Directory Connect のダウンロード](https://www.microsoft.com/download/details.aspx?id=47594) |
| 最も簡単な方法 (簡単設定) で Azure AD Connect をインストールします。 <br/>1.同期サイクル時間を最小限に抑えるために対象の OU をフィルター選択します。<br/>2.オンプレミス グループで対象となる一連のユーザーを選択します。<br/>手順 3.他の POC テーマに必要となる機能をデプロイします。 | [Azure AD Connect: カスタム インストール: ドメインと OU のフィルター処理](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) <br/>[Azure AD Connect: カスタム インストール: グループに基づくフィルター処理](./connect/active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups)<br/>[Azure AD Connect: オンプレミス ID と Azure Active Directory の統合: 同期機能を構成する](./connect/active-directory-aadconnect.md#configure-sync-features) |
| Azure AD Connect の UI を開いて実行中のプロファイル (インポート、同期、エクスポート) が完了したことを確認します。 | [Azure AD Connect 同期: スケジューラ](./connect/active-directory-aadconnectsync-feature-scheduler.md) |
| [Azure AD 管理ポータル](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/)を開いて [すべてのユーザー] ブレードにアクセスし、[権限ソース] 列を追加します。対象ユーザーが表示され、"Windows Server AD" からのユーザーとして適切にマークされていることを確認します。 | [Azure AD 管理ポータル](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) |

### <a name="considerations"></a>考慮事項

1. パスワード ハッシュ同期のセキュリティに関する考慮事項を[こちら](./connect/active-directory-aadconnectsync-implement-password-synchronization.md)で確認します。  パイロット運用環境のユーザーに対する選択肢としてパスワード ハッシュ同期が明らかに不適切である場合は、代わりに以下の手順を行います。
   * 運用環境ドメインにテスト ユーザーを作成します。 その他のアカウントを同期していないことを確認してください。
   * UAT 環境に移動します。
2.  フェデレーションを推進する場合は、オンプレミスの ID プロバイダーを使ったフェデレーション ソリューションに伴うコストが POC を上回ることを理解したうえで、期待するメリットと照らして評価することは大切です。
    * クリティカル パスであるため、高可用性を意図して設計する必要がある。
    * 容量計画に必要なオンプレミス サービスである。
    * 監視/保守/パッチ適用に必要なオンプレミス サービスである。

詳細情報: [Office 365 ID と Azure Active Directory について - フェデレーション ID](https://support.office.com/article/Understanding-Office-365-identity-and-Azure-Active-Directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9#bk_federated)

## <a name="branding"></a>ブランド

推定所要時間: 15 分

### <a name="pre-requisites"></a>前提条件

| 前提条件 | リソース |
| --- | --- |
| 資産 (画像、ロゴなど)。最適な表示を確保するために推奨サイズを遵守してください。 | [Azure Active Directory でサインイン ページに会社のブランドを追加する](active-directory-branding-custom-signon-azure-portal.md) |
| (省略可) 環境に ADFS サーバーがある場合は、そのサーバーにアクセスして Web テーマをカスタマイズします。 | [AD FS ユーザーのサインインのカスタマイズ](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/ad-fs-user-sign-in-customization) |
| エンド ユーザーのログイン操作を実行するためのクライアント コンピューター |  |
| (省略可) 操作性を検証するためのモバイル デバイス |  |

### <a name="steps"></a>手順

| 手順 | リソース |
| --- | --- |
| Azure AD 管理ポータルに移動します。 | [Azure AD 管理ポータル - 会社のブランド](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/LoginTenantBranding) |
| ログイン ページ用の資産 (ヒーロー ロゴ、小さいロゴ、ラベルなど) をアップロードします AD FS がある場合は、必要に応じて ADFS ログイン ページと同じ資産に統一します。 | [サインイン ページとアクセス パネル ページに会社のブランドを追加する: カスタマイズ可能な要素](active-directory-add-company-branding.md) |
| 変更が完全に反映されるまで数分待ちます。 |  |
| POC ユーザーの資格情報で https://myapps.microsoft.com にログインします。 |  |
| ブラウザーで外観を確認します。 | [サインイン ページとアクセス パネル ページに対する会社のブランドの追加](active-directory-add-company-branding.md) |
| 必要に応じて、他のデバイスで外観を確認します。 |  |

### <a name="considerations"></a>考慮事項

カスタマイズ後も以前の外観のままになっている場合は、ブラウザー クライアントのキャッシュをフラッシュしてから、操作を再試行してください。

## <a name="group-based-licensing"></a>グループベースのライセンス

推定所要時間: 10 分

### <a name="pre-requisites"></a>前提条件

| 前提条件 | リソース |
| --- | --- |
| すべての POC ユーザーがセキュリティ グループに属していること (クラウドまたはオンプレミス) | [Azure Active Directory でグループを作成し、メンバーを追加する](active-directory-groups-create-azure-portal.md) |

### <a name="steps"></a>手順

| 手順 | リソース |
| --- | --- |
| Azure AD 管理ポータルのライセンス ブレードに移動します。 | [Azure AD 管理ポータル: ライセンス](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) |
| POC ユーザーを含むセキュリティ グループにライセンスを割り当てます。 | [Azure Active Directory でのユーザーのグループへのライセンスの割り当て](active-directory-licensing-group-assignment-azure-portal.md) |

### <a name="considerations"></a>考慮事項

問題が発生した場合は、「[Azure Active Directory のライセンス管理にグループを使用する際のシナリオ、制限、および既知の問題](active-directory-licensing-group-advanced.md)」を参照してください。

## <a name="saas-federated-sso-configuration"></a>SaaS フェデレーション SSO 構成

推定所要時間: 60 分

### <a name="pre-requisites"></a>前提条件

| 前提条件 | リソース |
| --- | --- |
| SaaS アプリケーションのテスト環境が利用できること。 このガイドでは、ServiceNow を例として使用します。<br/>既存データの品質とマッピングをナビゲートする際の負荷を最小限に抑えるために、テスト インスタンスの使用を強くお勧めします。 | テスト インスタンスを取得するためのプロセスを開始するには、https://developer.servicenow.com/app.do#!/home にアクセスします。 |
| ServiceNow 管理コンソールへの管理者アクセス | [チュートリアル: Azure Active Directory と ServiceNow の統合](active-directory-saas-servicenow-tutorial.md) |
| アプリケーションの割り当て先となる一連の対象ユーザー。 PoC ユーザーを含むセキュリティ グループをお勧めします。 <br/>このグループの作成が現実的に難しい場合は、PoC のアプリケーションに直接ユーザーを割り当てます。 | [Azure Active Directory でエンタープライズ アプリケーションにユーザーまたはグループを割り当てる](active-directory-coreapps-assign-user-azure-portal.md) |

### <a name="steps"></a>手順

| 手順 | リソース |
| --- | --- |
| すべてのアクターに Microsoft ドキュメントのチュートリアルを共有します。  | [チュートリアル: Azure Active Directory と ServiceNow の統合](active-directory-saas-servicenow-tutorial.md) |
| 作業ミーティングを設定し、各アクターと共にチュートリアルの手順を実行します。 | [チュートリアル: Azure Active Directory と ServiceNow の統合](active-directory-saas-servicenow-tutorial.md) |
| 「前提条件」に示されているグループにアプリを割り当てます。 POC の範囲に条件付きアクセスが含まれている場合は、後から再度アクセスして MFA などを追加してください。 <br/>これにより、プロビジョニング プロセスが開始されることに注意してください (構成されている場合)。 |  [Azure Active Directory でエンタープライズ アプリケーションにユーザーまたはグループを割り当てる](active-directory-coreapps-assign-user-azure-portal.md) <br/>[Azure Active Directory でグループを作成し、メンバーを追加する](active-directory-groups-create-azure-portal.md) |
| Azure AD 管理ポータルを使用してギャラリーから ServiceNow アプリケーションを追加します。| [Azure AD 管理ポータル: エンタープライズ アプリケーション](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/Overview) <br/>[Azure Active Directory でのエンタープライズ アプリケーション管理の新機能](active-directory-enterprise-apps-whats-new-azure-portal.md) |
| ServiceNow アプリの [シングル サインオン] ブレードで [SAML ベースのサインオン] を有効にします。 |  |
| [サインオン URL] と [識別子] フィールドに実際の ServiceNow URL を入力します。<br/>[新しい証明書をアクティブにする] チェック ボックスをオンにし、<br/>設定を保存します。 |  |
| パネルの一番下にある [ServiceNow の構成] ブレードを開き、自分用にカスタマイズされた ServiceNow の構成手順を確認します。 |  |
| ServiceNow の構成手順に従います。 |  |
| ServiceNow アプリの [プロビジョニング] ブレードで "自動" プロビジョニングを有効にします。 | [新しい Azure Portal でエンタープライズ アプリケーションのユーザー アカウント プロビジョニングを管理する](active-directory-enterprise-apps-manage-provisioning.md) |
| プロビジョニングが完了するまで数分待ちます。  その間にプロビジョニング レポートを確認できます。 |  |
| アクセス権のあるテスト ユーザーとして https://myapps.microsoft.com/ にログインします。 | [アクセス パネルとは](active-directory-saas-access-panel-introduction.md) |
| 作成したアプリケーションのタイルをクリックします。 アクセスを確認します。 |  |
| 必要に応じて、アプリケーションの使用状況レポートを確認することができます。 多少待ち時間が生じるため、レポートにトラフィックが反映されるまでしばらく待つ必要があります。 | [Azure Active Directory ポータルのサインイン アクティビティ レポート: マネージ アプリケーションの使用状況](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Azure Active Directory レポートの保持ポリシー](active-directory-reporting-retention.md) |

### <a name="considerations"></a>考慮事項

1. 上記の[チュートリアル](active-directory-saas-servicenow-tutorial.md)では、以前の Azure AD の管理環境が使用されています。 一方 PoC は、[クイック スタート](active-directory-enterprise-apps-whats-new-azure-portal.md#quick-start-get-going-with-your-new-application-right-away)のエクスペリエンスに基づいています。
2. ギャラリーにターゲット アプリケーションが存在しない場合は、"独自のアプリの持ち込み" を使用できます。 詳細情報: [Azure Active Directory でのエンタープライズ アプリケーション管理の新機能: 1 つの場所からカスタム アプリケーションを追加する](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

## <a name="saas-password-sso-configuration"></a>SaaS パスワード SSO 構成

推定所要時間: 15 分

### <a name="pre-requisites"></a>前提条件

| 前提条件 | リソース |
| --- | --- |
| SaaS アプリケーションのテスト環境。 パスワード SSO の例としては、HipChat や Twitter があります。 その他のアプリケーションでは、HTML のサインイン フォームがあるページの正確な URL が必要です。 | [Twitter (Microsoft Azure Marketplace)](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[HipChat (Microsoft Azure Marketplace)](https://azuremarketplace.microsoft.com/marketplace/apps/aad.hipchat) |
| アプリケーションのテスト アカウント。 | [Twitter のサインアップ](https://twitter.com/signup?lang=en)<br/>[無料サインアップ: HipChat](https://www.hipchat.com/sign_up) |
| アプリケーションの割り当て先となる一連の対象ユーザー。 それらのユーザーを含むセキュリティ グループをお勧めします。 | [Azure Active Directory でエンタープライズ アプリケーションにユーザーまたはグループを割り当てる](active-directory-coreapps-assign-user-azure-portal.md) |
| Internet Explorer、Chrome、Firefox 用のアクセス パネル拡張機能をデプロイするコンピューターへのローカル管理者アクセス。 | [IE 用アクセス パネル拡張機能](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Chrome 用アクセス パネル拡張機能](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Firefox 用アクセス パネル拡張機能](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>手順

| 手順 | リソース |
| --- | --- |
| ブラウザー拡張機能をインストールします。 | [IE 用アクセス パネル拡張機能](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Chrome 用アクセス パネル拡張機能](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Firefox 用アクセス パネル拡張機能](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| ギャラリーからアプリケーションを構成します。 | [Azure Active Directory でのエンタープライズ アプリケーション管理の新機能: 改良された新しいアプリケーション ギャラリー](active-directory-enterprise-apps-whats-new-azure-portal.md#improvements-to-the-azure-active-directory-application-gallery) |
| パスワード SSO を構成します | [新しい Azure Portal でエンタープライズ アプリケーションのシングル サインオンを管理する: パスワードベースのサインオン](active-directory-enterprise-apps-manage-sso.md#password-based-sign-on) |
| 「前提条件」に示されているグループにアプリを割り当てます。 | [Azure Active Directory でエンタープライズ アプリケーションにユーザーまたはグループを割り当てる](active-directory-coreapps-assign-user-azure-portal.md) |
| アクセス権のあるテスト ユーザーとして https://myapps.microsoft.com/ にログインします。 |  |
| 作成したアプリケーションのタイルをクリックします。 | [アクセス パネルとは: パスワード ベースの SSO (ID プロビジョニングなし)](active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| アプリケーションの資格情報を指定します。 | [アクセス パネルとは: パスワード ベースの SSO (ID プロビジョニングなし)](active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| ブラウザーを閉じて再度ログインします。 今回は、ユーザーがアプリケーションにシームレスにアクセスできるはずです。 |  |
| 必要に応じて、アプリケーションの使用状況レポートを確認することができます。 多少待ち時間が生じるため、レポートにトラフィックが反映されるまでしばらく待つ必要があります。 | [Azure Active Directory ポータルのサインイン アクティビティ レポート: マネージ アプリケーションの使用状況](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Azure Active Directory レポートの保持ポリシー](active-directory-reporting-retention.md) |

### <a name="considerations"></a>考慮事項

ギャラリーにターゲット アプリケーションが存在しない場合は、"独自のアプリの持ち込み" を使用できます。 詳細情報: [Azure Active Directory でのエンタープライズ アプリケーション管理の新機能: 1 つの場所からカスタム アプリケーションを追加する](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

 以下の要件に注意してください。
   * アプリケーションには既知のログイン URL が必要です。
   * サインイン ページには、ブラウザー拡張機能で自動入力できるテキスト フィールドが 1 つ以上含まれている HTML フォームが必要です。 最低でも、ユーザー名とパスワードが必要です。

## <a name="saas-shared-accounts-configuration"></a>SaaS 共有アカウント構成

推定所要時間: 30 分

### <a name="pre-requisites"></a>前提条件

| 前提条件 | リソース |
| --- | --- |
| 対象アプリケーションのリストと実際のサインイン URL を事前に準備しておくこと。 たとえば Twitter を使用できます。 | [Twitter (Microsoft Azure Marketplace)](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[Twitter のサインアップ](https://twitter.com/signup?lang=en) |
| この SaaS アプリケーションの共有資格情報。 | [Azure AD とのアカウントの共有](active-directory-sharing-accounts.md)<br/>[Facebook、Twitter、LinkedIn の Azure AD 自動パスワード ロールオーバーがプレビュー段階になりました - Enterprise Mobility and Security のブログ] (https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/ ) |
| 同じアカウントにアクセスする少なくとも 2 人のチーム メンバーの資格情報。 これらのチーム メンバーはセキュリティ グループに属している必要があります。 | [Azure Active Directory でエンタープライズ アプリケーションにユーザーまたはグループを割り当てる](active-directory-coreapps-assign-user-azure-portal.md) |
| Internet Explorer、Chrome、Firefox 用のアクセス パネル拡張機能をデプロイするコンピューターへのローカル管理者アクセス。 | [IE 用アクセス パネル拡張機能](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Chrome 用アクセス パネル拡張機能](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Firefox 用アクセス パネル拡張機能](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>手順

| 手順 | リソース |
| --- | --- |
| ブラウザー拡張機能をインストールします。 | [IE 用アクセス パネル拡張機能](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Chrome 用アクセス パネル拡張機能](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Firefox 用アクセス パネル拡張機能](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| ギャラリーからアプリケーションを構成します。 | [Azure Active Directory でのエンタープライズ アプリケーション管理の新機能: 改良された新しいアプリケーション ギャラリー](active-directory-enterprise-apps-whats-new-azure-portal.md#improvements-to-the-azure-active-directory-application-gallery) |
| パスワード SSO を構成します | [新しい Azure Portal でエンタープライズ アプリケーションのシングル サインオンを管理する: パスワードベースのサインオン](active-directory-enterprise-apps-manage-sso.md#password-based-sign-on) |
| 「前提条件」に示されているグループにアプリを割り当てるとともに、グループに資格情報を割り当てます。 | [Azure Active Directory でエンタープライズ アプリケーションにユーザーまたはグループを割り当てる](active-directory-coreapps-assign-user-azure-portal.md) |
| **同じ共有アカウント**としてアプリにアクセスする別のユーザーとしてログインします。  |  |
| 必要に応じて、アプリケーションの使用状況レポートを確認することができます。 多少待ち時間が生じるため、レポートにトラフィックが反映されるまでしばらく待つ必要があります。 | [Azure Active Directory ポータルのサインイン アクティビティ レポート: マネージ アプリケーションの使用状況](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Azure Active Directory レポートの保持ポリシー](active-directory-reporting-retention.md) |


### <a name="considerations"></a>考慮事項

ギャラリーにターゲット アプリケーションが存在しない場合は、"独自のアプリの持ち込み" を使用できます。 詳細情報: [Azure Active Directory でのエンタープライズ アプリケーション管理の新機能: 1 つの場所からカスタム アプリケーションを追加する](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

 以下の要件に注意してください。
   * アプリケーションには既知のログイン URL が必要です。
   * サインイン ページには、ブラウザー拡張機能で自動入力できるテキスト フィールドが 1 つ以上含まれている HTML フォームが必要です。 最低でも、ユーザー名とパスワードが必要です。

## <a name="app-proxy-configuration"></a>アプリのプロキシ構成

推定所要時間: 20 分

### <a name="pre-requisites"></a>前提条件

| 前提条件 | リソース |
| --- | --- |
| Microsoft Azure AD の Basic または Premium サブスクリプションに加え、自分が全体管理者となっている Azure AD ディレクトリ。 | [Azure Active Directory のエディション](active-directory-editions.md) |
| リモート アクセスの構成対象となる、オンプレミスでホストされている Web アプリケーション。 |  |
| アプリケーション プロキシ コネクタをインストールできる Windows Server 2012 R2 または Windows 8.1 以降が実行されているサーバー。 | [Azure AD アプリケーション プロキシ コネクタについて](application-proxy-understand-connectors.md) |
| 経路上にファイアウォールがある場合、コネクタからアプリケーション プロキシに HTTPS (TCP) 要求を送信できるように、ファイアウォールを開放する必要があります。 | [Azure Portal でアプリケーション プロキシを有効にする: アプリケーション プロキシの前提条件](active-directory-application-proxy-enable.md#application-proxy-prerequisites) |
| 組織でインターネットへの接続にプロキシ サーバーを使用している場合、その構成方法の詳細については、既存のオンプレミス プロキシ サーバーの操作に関するブログ記事を参照してください。 | [既存のオンプレミス プロキシ サーバーと連携する](application-proxy-working-with-proxy-servers.md) |


### <a name="steps"></a>手順

| 手順 | リソース |
| --- | --- |
| サーバーにコネクタをインストールします。 | [Azure Portal でアプリケーション プロキシを有効にする: コネクタのインストールと登録](active-directory-application-proxy-enable.md#install-and-register-a-connector) |
| オンプレミス アプリケーションを Azure AD にアプリケーション プロキシ アプリケーションとして発行します。 | [Azure AD アプリケーション プロキシを使用してアプリケーションを発行する](application-proxy-publish-azure-portal.md) |
| テスト ユーザーを割り当てます。 | [Azure AD アプリケーション プロキシを使用したアプリケーションの発行: テスト ユーザーの選択](application-proxy-publish-azure-portal.md#add-a-test-user) |
| 必要に応じて、ユーザーのシングル サインオン エクスペリエンスを構成します。 | [Azure AD アプリケーション プロキシを使用したシングル サインオンの提供](application-proxy-sso-azure-portal.md) |
| 割り当てられたユーザーとして MyApps ポータルにサインインしてアプリをテストします。 | https://myapps.microsoft.com |

### <a name="considerations"></a>考慮事項

1. ここでは、コネクタを企業ネットワーク内に配置することをお勧めしますが、クラウドに配置した方がパフォーマンスが向上する場合もあります。 詳細情報: [Azure Active Directory アプリケーション プロキシを使用する場合のネットワーク トポロジに関する注意事項](application-proxy-network-topology-considerations.md)
2. セキュリティに関するさらに詳しい情報と、送信接続を維持するだけできわめて安全なリモート アクセス ソリューションを実現するしくみについては、「[Azure AD アプリケーション プロキシを使用したアプリへのリモート アクセス時のセキュリティに関する注意事項](application-proxy-security-considerations.md)」を参照してください。

## <a name="generic-ldap-connector-configuration"></a>Generic LDAP コネクタ構成

推定所要時間: 60 分

> [!IMPORTANT]
> これは高度な構成で、FIM/MIM に関する知識を必要とします。 運用環境で使用されている場合、この構成に関するご質問については、[Premier サポート](https://support.microsoft.com/premier)を使ってお問い合わせください。

### <a name="pre-requisites"></a>前提条件

| 前提条件 | リソース |
| --- | --- |
| Azure AD Connect がインストールされ構成されていること。 | 構成要素: [ディレクトリ同期 - パスワード ハッシュ同期](#directory-synchronization--password-hash-sync-phs--new-installation) |
| 要件を満たす ADLDS インスタンス | [Generic LDAP コネクタに関するテクニカル リファレンス: Generic LDAP コネクタの概要](./connect/active-directory-aadconnectsync-connector-genericldap.md#overview-of-the-generic-ldap-connector) |
| ユーザーが使用している一連のワークロードとそれらのワークロードに関連付けられている属性。 | [Azure AD Connect Sync: Azure Active Directory に同期される属性](./connect/active-directory-aadconnectsync-attributes-synchronized.md) |


### <a name="steps"></a>手順

| 手順 | リソース |
| --- | --- |
| Generic LDAP コネクタを追加します。 | [Generic LDAP コネクタに関するテクニカル リファレンス: 新しいコネクタの作成](./connect/active-directory-aadconnectsync-connector-genericldap.md#create-a-new-connector) |
| 作成したコネクタの実行プロファイルを作成します (フル インポート、差分インポート、完全同期、差分同期、エクスポート)。 | [管理エージェントの実行プロファイルを作成する](https://technet.microsoft.com/library/jj590219(v=ws.10).aspx)<br/> [Azure AD Connect の Sync Service Manager でコネクタを使用する](./connect/active-directory-aadconnectsync-service-manager-ui-connectors.md)|
| フル インポート プロファイルを実行して、コネクタ スペースにオブジェクトが存在することを検証します。 | [コネクタ スペース オブジェクトの検索](https://technet.microsoft.com/library/jj590287(v=ws.10).aspx)<br/>[Azure AD Connect の Sync Service Manager でコネクタを使用する: コネクタ スペースの検索](./connect/active-directory-aadconnectsync-service-manager-ui-connectors.md#search-connector-space) |
| ワークロードに必要な属性がメタバース内のオブジェクトに確実に存在するよう同期規則を作成します。 | [Azure AD Connect Sync: 既定の構成の変更するためのベスト プラクティス: 同期規則に対する変更](./connect/active-directory-aadconnectsync-best-practices-changing-default-configuration.md#changes-to-synchronization-rules)<br/>[Azure AD Connect 同期: 宣言型のプロビジョニングについて](./connect/active-directory-aadconnectsync-understanding-declarative-provisioning.md)<br/>[Azure AD Connect 同期: 宣言型のプロビジョニングの式について](./connect/active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) |
| 完全同期サイクルを開始します。 | [Azure AD Connect Sync: スケジューラ: スケジューラの開始](./connect/active-directory-aadconnectsync-feature-scheduler.md#start-the-scheduler) |
| 問題が発生した場合はトラブルシューティングを行います。 | [Azure AD と同期していないオブジェクトのトラブルシューティング](./connect/active-directory-aadconnectsync-troubleshoot-object-not-syncing.md) |
| LDAP ユーザーがアプリケーションにサインインしてアクセスできることを確認します。 | https://myapps.microsoft.com |

### <a name="considerations"></a>考慮事項

> [!IMPORTANT]
> これは高度な構成で、FIM/MIM に関する知識を必要とします。 運用環境で使用されている場合、この構成に関するご質問については、[Premier サポート](https://support.microsoft.com/premier)を使ってお問い合わせください。

## <a name="groups---delegated-ownership"></a>グループ - 委任された所有権

推定所要時間: 10 分

### <a name="pre-requisites"></a>前提条件

| 前提条件 | リソース |
| --- | --- |
| SaaS アプリケーション (フェデレーション SSO またはパスワード SSO) が既に構成されていること。 | 構成要素: [SaaS フェデレーション SSO 構成](#saas-federated-sso-configuration) |
| 1 番目のアプリケーションへのアクセス権が割り当てられているクラウド グループを把握していること。 | 構成要素: [SaaS フェデレーション SSO 構成](#saas-federated-sso-configuration) <br/>[Azure Active Directory でグループを作成し、メンバーを追加する](active-directory-groups-create-azure-portal.md) |
| グループ所有者の資格情報が利用可能であること。 | [Azure Active Directory のグループによるリソースへのアクセス管理](active-directory-manage-groups.md) |
| アプリにアクセスするインフォメーション ワーカーの資格情報を把握していること。 | [アクセス パネルとは](active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>手順

| 手順 | リソース |
| --- | --- |
| アプリケーションへのアクセスが許可されているグループを特定し、そのグループの所有者を構成します。| [Azure Active Directory でグループのメンバーを管理する](active-directory-groups-settings-azure-portal.md) |
| グループ所有者としてログインし、アクセス パネルの [グループ] タブでグループのメンバーシップを確認します。 | [Azure Active Directory グループ管理ページ](https://account.activedirectory.windowsazure.com/r/#/groups) |
| テストするインフォメーション ワーカーを追加します。 |  |
| インフォメーション ワーカーとしてログインし、タイルが利用可能であることを確認します。 | [アクセス パネルとは](active-directory-saas-access-panel-introduction.md) |

### <a name="considerations"></a>考慮事項

アプリケーションのプロビジョニングを有効にした場合、プロビジョニングが完了してインフォメーション ワーカーとしてアプリケーションにアクセスできる状態になるまでに数分かかる場合があります。

## <a name="saas-and-identity-lifecycle"></a>SaaS と ID のライフサイクル

### <a name="pre-requisites"></a>前提条件

| 前提条件 | リソース |
| --- | --- |
| SaaS アプリケーション (フェデレーション SSO またはパスワード SSO) が既に構成されていること。 | 構成要素: [SaaS フェデレーション SSO 構成](#saas-federated-sso-configuration) |
| 1 番目のアプリケーションへのアクセス権が割り当てられているクラウド グループを把握していること。 | 構成要素: [SaaS フェデレーション SSO 構成](#saas-federated-sso-configuration) <br/>[Azure Active Directory でグループを作成し、メンバーを追加する](active-directory-groups-create-azure-portal.md) |
| アプリにアクセスするインフォメーション ワーカーの資格情報を把握していること。 | [アクセス パネルとは](active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>手順

| 手順 | リソース |
| --- | --- |
| アプリが割り当てられているグループからユーザーを削除します。 | [Azure Active Directory テナントでユーザーのグループ メンバーシップを管理する](active-directory-groups-members-azure-portal.md) |
| プロビジョニングが解除されるまで数分待ちます。 | [Azure AD での SaaS アプリ ユーザー プロビジョニングの自動化: 自動プロビジョニングのしくみ](active-directory-saas-app-provisioning.md#how-does-automated-provisioning-work) |
| 別のブラウザー セッションで、インフォメーション ワーカーとして MyApps ポータルにログインし、そのタイルがなくなっていることを確認します。 | http://myapps.microsoft.com |


### <a name="considerations"></a>考慮事項

この PoC シナリオを退職や休職のシナリオに当てはめます。 ユーザーは、オンプレミス AD で無効になったり、削除されたりすると、SaaS アプリケーションにログインできなくなります。

## <a name="self-service-access-to-application-management"></a>セルフサービスでアプリケーション管理にアクセスする

推定所要時間: 10 分

### <a name="pre-requisites"></a>前提条件

| 前提条件 | リソース |
| --- | --- |
| セキュリティ グループのメンバーとしてアプリケーションへのアクセスを要求する POC ユーザーを把握していること。 | 構成要素: [SaaS フェデレーション SSO 構成](#saas-federated-sso-configuration) |
| 対象アプリケーションがデプロイされていること。 | 構成要素: [SaaS フェデレーション SSO 構成](#saas-federated-sso-configuration) |

### <a name="steps"></a>手順

| 手順 | リソース |
| --- | --- |
| Azure AD 管理ポータルの [エンタープライズ アプリケーション] ブレードにアクセスします。 | [Azure AD 管理ポータル: エンタープライズ アプリケーション](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) |
| 前提条件のアプリケーションをセルフサービスで構成します。 | [Azure Active Directory でのエンタープライズ アプリケーション管理の新機能: アプリケーションのセルフサービス アクセスを構成する](active-directory-enterprise-apps-whats-new-azure-portal.md#configure-self-service-application-access) |
| インフォメーション ワーカーとして MyApps ポータルにログインします。 | http://myapps.microsoft.com |
| ページ上部の [+アプリの追加] ボタンに注目してください。 このボタンを使ってアプリにアクセスします。 |  |

### <a name="considerations"></a>考慮事項

選択したアプリケーションにはプロビジョニングの要件があることも考えられます。そのため、すぐにアプリにアクセスしようとするとエラーが発生する可能性があります。 選択したアプリケーションが Azure AD によるプロビジョニングに対応していて、構成されている場合、これは、作業フロー全体をエンド ツー エンドで示すきっかけになる可能性があります。 詳しい推奨事項については、「[SaaS フェデレーション SSO 構成](#saas-federated-sso-configuration)」の構成要素を参照してください。

## <a name="self-service-password-reset"></a>セルフサービスによるパスワードのリセット

推定所要時間: 15 分

### <a name="pre-requisites"></a>前提条件

| 前提条件 | リソース |
| --- | --- |
| テナントでセルフサービスによるパスワード管理を有効にします。 | [IT 管理者のための Azure Active Directory のパスワード リセット](active-directory-passwords.md) |
| オンプレミスのパスワードを管理するためのパスワード ライトバックを有効にします。 これには、特定のバージョンの Azure AD Connect が必要となることに注意してください。 | [パスワード ライトバックの前提条件](active-directory-passwords-writeback.md) |
| この機能を使用する PoC ユーザーを把握し、セキュリティ グループのメンバーになっていることを確認します。 この機能を完全に実証するには、ユーザーが非管理者である必要があります。 | [カスタマイズ: Azure AD Password Management: パスワード リセットへのアクセスの制限](active-directory-passwords-writeback.md) |


### <a name="steps"></a>手順

| 手順 | リソース |
| --- | --- |
| Azure AD 管理ポータルの [パスワードのリセット] に移動します。 | [Azure AD 管理ポータル: パスワードのリセット](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) |
| パスワード リセット ポリシーを決定します。 概念実証という目的上、電話と Q & A を使用できます。アクセス パネルへのログインについては登録を必須にすることをお勧めします。 |  |
| ログアウトし、インフォメーション ワーカーとしてログインします。 |  |
| 手順 2. で構成した "セルフサービスによるパスワードのリセット" データを指定します。 | http://aka.ms/ssprsetup |
| ブラウザーを閉じます。 |  |
| 手順 4. で使用したインフォメーション ワーカーとしてログイン プロセスを最初からやり直します。 |  |
| パスワードをリセットします。 | [自分のパスワードを更新する: パスワードをリセットする](active-directory-passwords-update-your-own-password.md) |
| 新しいパスワードで Azure AD とオンプレミス リソースにログインします。 |  |

### <a name="considerations"></a>考慮事項

1. Azure AD Connect をアップグレードすることで問題が生じる場合は、クラウド アカウントに対して使用するか、別の環境に対するデモとして行ってください。
2. 管理者には異なるポリシーがあり、管理者アカウントを使用してパスワードをリセットすると、PoC に悪影響が及び、混乱が生じる可能性があります。 必ず通常のユーザー アカウントを使用してリセット操作をテストしてください。


## <a name="azure-multi-factor-authentication-with-phone-calls"></a>電話を使用した Azure Multi-Factor Authentication

推定所要時間: 10 分

### <a name="pre-requisites"></a>前提条件

| 前提条件 | リソース |
| --- | --- |
| MFA を使用する POC ユーザーを把握していること。  |  |
| MFA チャレンジ用の受信状態の良い電話。  | [Azure Multi-Factor Authentication とは](../multi-factor-authentication/multi-factor-authentication.md) |

### <a name="steps"></a>手順

| 手順 | リソース |
| --- | --- |
| Azure AD 管理ポータルの [ユーザーとグループ] ブレードに移動します。 | [Azure AD 管理ポータル: ユーザーとグループ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/Overview/menuId/) |
| [すべてのユーザー] ブレードを選択します。 |  |
| 最上部のバーにある [Multi-Factor Authentication] ボタンをクリックします。 | Azure MFA ポータルの直接 URL: https://aka.ms/mfaportal |
| [ユーザー] 設定で目的の PoC ユーザーを選択し、その MFA を有効にします。 | [Azure Multi-Factor Authentication におけるユーザーの状態](../multi-factor-authentication/multi-factor-authentication-get-started-user-states.md) |
| PoC ユーザーとしてログインし、実証プロセスを行います。  |  |

### <a name="considerations"></a>考慮事項

1. この構成要素の PoC 手順では、すべてのログイン ユーザーの MFA を明示的に設定しています。 ほかにも、条件付きアクセスや、より的を絞ったシナリオで MFA を利用する Identity Protection などのツールがあります。 この点については、概念実証から運用環境への移行時に考慮することになります。
2. この構成要素の PoC 手順では、MFA 方式として明示的に電話を使用しています。 POC から運用段階への切り替え時には可能な限り、第 2 の認証要素として [Microsoft Authenticator](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) などのアプリケーションを使うようお勧めします。
詳細情報: [DRAFT NIST Special Publication 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="mfa-conditional-access-for-saas-applications"></a>SaaS アプリケーション向けの MFA での条件付きアクセス

推定所要時間: 10 分

### <a name="pre-requisites"></a>前提条件

| 前提条件 | リソース |
| --- | --- |
| ポリシーの対象となる PoC ユーザーを把握していること。 条件付きアクセス ポリシーを適用するセキュリティ グループに、それらのユーザーが属している必要があります。 | [SaaS フェデレーション SSO 構成](#saas-federated-sso-configuration) |
| SaaS アプリケーションが構成済みであること。 |  |
| そのアプリケーションに PoC ユーザーがあらかじめ割り当てられていること。 |  |
| POC ユーザーの資格情報が用意されていること。 |  |
| POC ユーザーが MFA の対象として登録済みであること。 受信状態の良い電話を使用すること。 | http://aka.ms/ssprsetup |
| 内部ネットワーク内のデバイス。 内部アドレス範囲内で IP アドレスが構成されていること。 | IP アドレスの確認: https://www.bing.com/search?q=what%27s+my+ip |
| 外部ネットワークのデバイス (キャリアのモバイル ネットワークを使ったスマートフォンなど) |  |

### <a name="steps"></a>手順

| 手順 | リソース |
| --- | --- |
| Azure AD 管理ポータルの [条件付きアクセス] ブレードにアクセスします。 | [Azure AD 管理ポータル: 条件付きアクセス](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) |
| 条件付きアクセス ポリシーを作成します。<br/>- [ユーザーとグループ] で PoC ユーザーを指定します。<br/>- [クラウド アプリ] で PoC アプリケーションを指定します。<br/>- 信頼済みとなっている場所を除くすべての場所を [条件] の [場所] で指定します。**注:** 信頼できる IP は、[MFA ポータル](https://account.activedirectory.windowsazure.com/UserManagement/MfaSettings.aspx)で構成します。<br/>- [許可] で Multi-Factor Authentication を必須とします。 | [Azure Active Directory での条件付きアクセスの基本: ポリシーの構成の手順](active-directory-conditional-access-azure-portal-get-started.md#policy-configuration-steps) |
| 企業ネットワーク内からアプリケーションにアクセスします。 | [Azure Active Directory での条件付きアクセスの基本: ポリシーをテストする](active-directory-conditional-access-azure-portal-get-started.md#testing-the-policy) |
| パブリック ネットワークからアプリケーションにアクセスします。 | [Azure Active Directory での条件付きアクセスの基本: ポリシーをテストする](active-directory-conditional-access-azure-portal-get-started.md#testing-the-policy) |

### <a name="considerations"></a>考慮事項

フェデレーションを使用している場合、オンプレミスの ID プロバイダー (IdP) を使用し、企業ネットワークの内部/外部の状態を要求でやり取りすることができます。 この手法を使用するうえで、大規模な組織では評価および管理が難しい場合がある IP アドレスのリストの管理は必要ありません。 このセットアップには、"ネットワーク ローミング" (ユーザーが内部ネットワークからログインし、その状態のまま、コーヒー ショップなどの場所に移動するシナリオ) 用のアカウントが必要です。また、それによって生じる影響を理解しておく必要があります。 詳細情報: [Azure Multi-Factor Authentication および AD FS を使用したクラウド リソースのセキュリティ保護: フェデレーション ユーザー用の信頼できる IP](../multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud.md#trusted-ips-for-federated-users)

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

推定所要時間: 15 分

### <a name="pre-requisites"></a>前提条件

| 前提条件 | リソース |
| --- | --- |
| PIM の POC に参加する全体管理者を把握していること。 | [Azure AD Privileged Identity Management の使用開始](active-directory-privileged-identity-management-getting-started.md) |
| セキュリティ管理者となる全体管理者を把握していること。 | [Azure AD Privileged Identity Management の使用開始](active-directory-privileged-identity-management-getting-started.md)<br/> [Azure Active Directory PIM での別の管理者ロール](active-directory-privileged-identity-management-roles.md) |
| (省略可) PIM で電子メール通知を実施するための電子メール アクセスが全体管理者にあるかどうかを確認すること。 | [Azure AD Privileged Identity Management とは: ロール アクティブ化設定の構成](active-directory-privileged-identity-management-configure.md#configure-the-role-activation-settings)


### <a name="steps"></a>手順

| 手順 | リソース |
| --- | --- |
| 全体管理者 (GA) として https://portal.azure.com にログインし、PIM ブレードを開きます。 この手順を実行する全体管理者は、セキュリティ管理者として準備します。  このアクターを GA1 と呼ぶことにしましょう。 | [Azure AD Privileged Identity Management でのセキュリティ ウィザードの使用](active-directory-privileged-identity-management-security-wizard.md) |
| 全体管理者を指定し、永続から候補に変更します。 明確にするために、この管理者は、手順 1. とは別の管理者にする必要があります。 このアクターを GA2 と呼ぶことにしましょう。 | [Azure AD Privileged Identity Management: ユーザー ロールを追加または削除する方法](active-directory-privileged-identity-management-how-to-add-role-to-user.md)<br/>[Azure AD Privileged Identity Management とは: ロール アクティブ化設定の構成](active-directory-privileged-identity-management-configure.md#configure-the-role-activation-settings)  |
| 今度は GA2 として https://portal.azure.com にログインし、"ユーザー設定" の変更を試みます。 一部のオプションが淡色表示されていることに気が付きます。 | |
| 新しいタブ (かつ手順 3. と同じセッション) で、今度は https://portal.azure.com に移動し、PIM ブレードをダッシュボードに追加します。 | [Azure AD Privileged Identity Management でロールをアクティブ化または非アクティブ化する方法: Privileged Identity Management アプリケーションの追加](active-directory-privileged-identity-management-how-to-activate-role.md#add-the-privileged-identity-management-application) |
| 全体管理者ロールのアクティブ化を要求します。 | [Azure AD Privileged Identity Management でロールをアクティブ化または非アクティブ化する方法: ロールのアクティブ化](active-directory-privileged-identity-management-how-to-activate-role.md#activate-a-role) |
| GA2 が MFA にサインアップしなかった場合、Azure MFA の登録が必要になります。 |  |
| 手順 3. の元のタブに戻り、ブラウザーの更新ボタンをクリックします。 "ユーザー設定" の変更に対するアクセスが許可されていることがわかります。 | |
| 全体管理者が電子メールを有効にしている場合は、GA1 と GA2 の受信トレイを見て、ロールがアクティブ化されているという通知を確認してください。 |  |
| 8 監査履歴をチェックし、レポートを見て、GA2 が昇格されていることの記録があることを確認します。 | [Azure AD Privileged Identity Management とは: ロール アクティビティの確認](active-directory-privileged-identity-management-configure.md#review-role-activity) |

### <a name="considerations"></a>考慮事項

これは Azure AD Premium P2 と EMS E5 に備わっている機能です。

## <a name="discovering-risk-events"></a>リスク イベントを検出する

推定所要時間: 20 分

### <a name="pre-requisites"></a>前提条件

| 前提条件 | リソース |
| --- | --- |
| Tor Browser がダウンロード、インストールされているデバイス。 | [Tor Browser のダウンロード](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| ログインを実行する POC ユーザーへのアクセス。 | [Azure Active Directory Identity Protection プレイブック](active-directory-identityprotection-playbook.md) |

### <a name="steps"></a>手順

| 手順 | リソース |
| --- | --- |
| Tor Browser を開きます。 | [Tor Browser のダウンロード](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| POC ユーザー アカウントで https://myapps.microsoft.com にログインします。 | [Azure Active Directory Identity Protection プレイブック: リスク イベントのシミュレーション](active-directory-identityprotection-playbook.md#simulating-risk-events) |
| 5 ～ 7 分待ちます。 |  |
| 全体管理者として https://portal.azure.com にログインし、[Identity Protection] ブレードを開きます。 | https://aka.ms/aadipgetstarted |
| [リスク イベント] ブレードを開きます。 [匿名 IP アドレスからのサインイン] にエントリが表示されます。  | [Azure Active Directory Identity Protection プレイブック: リスク イベントのシミュレーション](active-directory-identityprotection-playbook.md#simulating-risk-events) |

### <a name="considerations"></a>考慮事項

これは Azure AD Premium P2 と EMS E5 に備わっている機能です。

## <a name="deploying-sign-in-risk-policies"></a>サインイン リスク ポリシーをデプロイする

推定所要時間: 10 分

### <a name="pre-requisites"></a>前提条件

| 前提条件 | リソース |
| --- | --- |
| Tor Browser がダウンロード、インストールされているデバイス。 | [Tor Browser のダウンロード](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| ログイン テストを実行する POC ユーザーとしてのアクセス。 |  |
| POC ユーザーが MFA に登録されていること。 必ず受信状態の良い電話を使用してください。 | 構成要素: [電話を使用した Azure Multi-Factor Authentication](#azure-multi-factor-authentication-with-phone-calls) |


### <a name="steps"></a>手順

| 手順 | リソース |
| --- | --- |
| 全体管理者として https://portal.azure.com にログインし、[Identity Protection] ブレードを開きます。 | https://aka.ms/aadipgetstarted |
| 次のようにサインイン リスク ポリシーを有効にします。<br/>- 割り当て先: POC ユーザー<br/>- 条件: 中程度またはそれ以上のサインイン リスク (匿名の場所からのサインインはリスク レベルが中程度と見なされます)<br/>- コントロール: MFA を要求 | [Azure Active Directory Identity Protection プレイブック: サインイン リスク](active-directory-identityprotection-playbook.md#sign-in-risk) |
| Tor Browser を開きます。 | [Tor Browser のダウンロード](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| PoC ユーザー アカウントで https://myapps.microsoft.com にログインします。 |  |
| MFA チャレンジを通知します。 | [Azure AD Identity Protection を使用したサインイン エクスペリエンス: リスクの高いサインインの復旧](active-directory-identityprotection-flows.md#risky-sign-in-recovery)

### <a name="considerations"></a>考慮事項

これは Azure AD Premium P2 と EMS E5 に備わっている機能です。 リスク イベントの詳細については、「[Azure Active Directory リスク イベント](active-directory-reporting-risk-events.md)」を参照してください。

## <a name="configuring-certificate-based-authentication"></a>証明書ベースの認証を構成する

推定所要時間: 20 分

### <a name="pre-requisites"></a>前提条件

| 前提条件 | リソース |
| --- | --- |
| エンタープライズ PKI からのユーザー証明書がプロビジョニングされているデバイス (Windows、iOS、Android のいずれか)。 | [ユーザー証明書をデプロイする](https://msdn.microsoft.com/library/cc770857.aspx) |
| ADFS とフェデレーションされた Azure AD ドメイン。 | [Azure AD Connect とフェデレーション](./connect/active-directory-aadconnectfed-whatis.md)<br/>[Active Directory 証明書サービスの概要](https://technet.microsoft.com/library/hh831740.aspx)|
| (iOS デバイスの場合) Microsoft Authenticator アプリがインストールされていること。 | [Microsoft Authenticator アプリの概要](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) |

### <a name="steps"></a>手順

| 手順 | リソース |
| --- | --- |
| ADFS で [証明書の認証] を有効にします。 | [認証ポリシーを構成する: Windows Server 2012 R2 でグローバルに主要な認証を構成するには](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-authentication-policies#to-configure-primary-authentication-globally-in-windows-server-2012-r2) |
| (省略可) Azure AD で Exchange Active Sync クライアントに対する証明書認証を有効にします。 | [Azure Active Directory の証明書ベースの認証の概要](active-directory-certificate-based-authentication-get-started.md) |
| アクセス パネルに移動し、ユーザー証明書を使って認証を行います。 | https://myapps.microsoft.com |

### <a name="considerations"></a>考慮事項

このデプロイの注意事項について詳しくは、「[ADFS: Certificate Authentication with Azure AD & Office 365 (ADFS: Azure AD と Office 365 を使用した証明書認証)](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/)」を参照してください。



> [!NOTE]
> 所有するユーザー証明書はしっかりと保護する必要があります。 デバイスを管理するか、スマート カードの場合は PIN を使用してください。



[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]

