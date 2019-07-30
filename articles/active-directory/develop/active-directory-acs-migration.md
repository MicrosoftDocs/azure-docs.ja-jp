---
title: Azure Access Control Service からの移行 | Microsoft Docs
description: Azure Access Control Service (ACS) からアプリとサービスを移動するオプションについて学習します。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/03/2018
ms.author: ryanwi
ms.reviewer: jlu, annaba, hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3daf44ee29a2f7b29eec9215876ca6edc18a5800
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325062"
---
# <a name="how-to-migrate-from-the-azure-access-control-service"></a>方法:Azure Access Control Service からの移行

Azure Active Directory (Azure AD) のサービスである Microsoft Azure Access Control (ACS) は、2018 年 11 月 7 日に終了する予定です。 現在 Access Control を使用しているアプリケーションとサービスは、それまでに別の認証メカニズムへと完全移行する必要があります。 この記事では、現在のお客様を対象に、Access Control の使用の廃止を計画する際の推奨事項を紹介します。 現在 Access Control を使用していない場合は、何もする必要はありません。

## <a name="overview"></a>概要

Access Control は、Web アプリケーションや Web サービスにアクセスするユーザーの認証と承認を容易にする、クラウド認証サービスです。 これにより、認証や承認に関する多くの機能について、コーディングの必要がなくなります。 Access Control は主に、Microsoft .NET クライアント、ASP.NET Web アプリケーション、および Windows Communication Foundation (WCF) Web サービスの開発者や設計者によって使用されます。

Access Control のユース ケースは、主に次の 3 つのカテゴリに分けることができます。

- 特定の Microsoft クラウド サービス (Azure Service Bus、Dynamics CRM など) に対する認証。 クライアント アプリケーションは、Access Control から取得したトークンによってこれらのサービスに対する認証を受け、さまざまなアクションを実行します。
- Web アプリケーション (カスタムとパッケージ (SharePoint など) の両方) に対する認証の追加。 Web アプリケーションで Access Control の "パッシブ" 認証を使用すると、Microsoft アカウント (以前の Live ID) だけでなく、Google、Facebook、Yahoo、Azure AD、および Active Directory フェデレーション サービス (AD FS) のアカウントを使用してサインインできるようになります。
- Access Control によって発行されるトークンを使用したカスタム Web サービスのセキュリティ保護。 Web サービスで "アクティブ" 認証を使用すると、Access Control を使用して認証された既知のクライアントに対してのみ、アクセスを許可できるようになります。

これらの各ユース ケースと推奨される移行方法については、以下のセクションで説明します。

> [!WARNING]
> ほとんどの場合、既存のアプリやサービスを新しいテクノロジに移行するには大幅なコード変更が必要になります。 停止またはダウンタイムの可能性を防ぐために、移行の計画や実行を速やかに開始することをお勧めします。

Access Control に含まれるコンポーネントを次に示します。

- セキュリティ トークン サービス (STS)。認証要求を受け取って、セキュリティ トークンを発行します。
- Azure クラシック ポータル。このポータルでは、Access Control 名前空間を作成したり、削除したり、有効/無効を切り替えることができます。
- 個別の Access Control 管理ポータル。このポータルでは、Access Control 名前空間のカスタマイズや構成を行うことができます。
- 管理サービス。ポータルの機能を自動化するために使用できます。
- トークン変換ルール エンジン。Access Control が発行するトークンの出力形式を操作する、複雑なロジックを定義できます。

これらのコンポーネントを使用するには、1 つ以上の Access Control 名前空間を作成する必要があります。 *名前空間*とは、アプリケーションやサービスと通信するために使用される、Access Control 専用のインスタンスのことです。 名前空間は、Access Control を使用するその他のすべてのお客様から分離されます。 他のお客様は、それぞれ独自の名前空間を使用します。 Access Control 名前空間には、次のような専用の URL が使用されます。

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

STS および管理操作とのすべての通信は、この URL で行われます。 目的ごとに、異なるパスを使用します。 アプリケーションやサービスが Access Control を使用しているかどうかを確認するには、 https://&lt;名前空間&gt;.accesscontrol.windows.net へのトラフィックを監視します。 この URL へのトラフィックは Access Control によって処理されるため、停止する必要があります。 

これに対する例外は、`https://accounts.accesscontrol.windows.net` へのすべてのトラフィックです。 この URL へのトラフィックは既に他のサービスによって対処されており、Access Control の廃止の影響を**受けません**。 

Access Control の詳細については、「[Access Control Service 2.0](https://msdn.microsoft.com/library/hh147631.aspx)」(アーカイブ) を参照してください。

## <a name="find-out-which-of-your-apps-will-be-impacted"></a>影響を受けるアプリケーションを確認する

このセクションの手順に従って、どのアプリケーションが ACS の提供終了によって影響を受けるかを確認します。

### <a name="download-and-install-acs-powershell"></a>ACS PowerShell をダウンロードしてインストールする

1. PowerShell ギャラリーに移動し、[Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2) をダウンロードします。
1. 次を実行してモジュールをインストールします

    ```powershell
    Install-Module -Name Acs.Namespaces
    ```

1. 次を実行して、使用できるすべてのコマンドの一覧を取得します

    ```powershell
    Get-Command -Module Acs.Namespaces
    ```

    特定のコマンドのヘルプを表示するには、次を実行します。

    ```
     Get-Help [Command-Name] -Full
    ```
    
    `[Command-Name]` は、ACS コマンドの名前です。

### <a name="list-your-acs-namespaces"></a>ACS 名前空間を一覧表示する

1. **Connect-AcsAccount** コマンドレットを使用して ACS に接続します。
  
    コマンドを実行する前に `Set-ExecutionPolicy -ExecutionPolicy Bypass` を実行し、コマンドを実行するためにそれらのサブスクリプションの管理者になっておくことが必要な場合があります。

1. **Get-AcsSubscription** コマンドレットを使用して、使用可能な Azure サブスクリプションを一覧表示します。
1. **Get-AcsNamespace** コマンドレットを使用して、ACS 名前空間を一覧表示します。

### <a name="check-which-applications-will-be-impacted"></a>どのアプリケーションが影響を受けるかを確認する

1. 前の手順で取得した名前空間を使用して、`https://<namespace>.accesscontrol.windows.net` に移動します

    たとえば、名前空間の 1 つが contoso-test である場合は、`https://contoso-test.accesscontrol.windows.net` に移動します

1. **[信頼関係]** で、 **[証明書利用者アプリケーション]** を選択し、ACS の提供終了の影響を受けるアプリケーションの一覧を表示します。
1. その他に ACS 名前空間があれば、それらについて手順 1 ～ 2 を繰り返します。

## <a name="retirement-schedule"></a>提供終了のスケジュール

2017 年 11 月の時点では、すべての Access Control コンポーネントは完全にサポートされており、使用可能です。 ただし、[Azure クラシック ポータルを使用して新しい Access Control 名前空間を作成することはできません](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)。

Access Control コンポーネントの廃止スケジュールを次に示します。

- **2017 年 11 月**: Azure クラシック ポータルでの Azure AD 管理エクスペリエンスの[提供は終了します](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/)。 この時点で、Access Control の名前空間の管理は、新しい専用の URL `https://manage.windowsazure.com?restoreClassic=true` で利用できるようになります。 この URl を使用すれば、既存の名前空間を表示したり、名前空間を有効または無効にしたり、名前空間を削除することができます。
- **2018 年 4 月 2 日**: Azure クラシック ポータルは完全に廃止され、Access Control の名前空間管理はすべての URL で使用できなくなります。 これ以降、Access Control 名前空間を有効または無効にしたり、削除したり、列挙することはできなくなります。 ただし、Access Control 管理ポータルはその機能を完全に維持され、`https://\<namespace\>.accesscontrol.windows.net` に配置されます。 その他の Access Control コンポーネントもすべて、引き続き正常に動作します。
- **2018 年 11 月 7 日**: すべての Access Control コンポーネントが完全にシャットダウンされます。 これには、Access Control 管理ポータル、管理サービス、STS、およびトークン変換ルール エンジンが含まれます。 これ以降、(\<名前空間\>.accesscontrol.windows.net にある) Access Control へ送信されるすべての要求は失敗するようになります。 この時点までに、すべての既存アプリケーションとサービスを他のテクノロジへと移行しておく必要があります。

> [!NOTE]
> ポリシーは、一定期間トークンを要求していない名前空間を無効にします。 2018 年初頭現在、この期間は 14 日間の非アクティブですが、今後数週間以内に 7 日間の非アクティブに短縮されます。 現在無効になっている Access Control 名前空間がある場合、[ACS PowerShell をダウンロードしてインストールし](#download-and-install-acs-powershell)、名前空間を再度有効にすることができます。

## <a name="migration-strategies"></a>移行方法

次のセクションでは、Access Control から他の Microsoft テクノロジに移行する際の推奨事項の概要を示します。

### <a name="clients-of-microsoft-cloud-services"></a>Microsoft クラウド サービスのクライアント

Access Control によって発行されたトークンを受け入れる各 Microsoft クラウド サービスでは、少なくとも 1 つの代替認証形式がサポートされています。 使用できる認証メカニズムは、サービスごとに異なります。 各サービスの固有のドキュメントで、公式のガイダンスを参照することをお勧めします。 ここでは簡単に、各ドキュメントのセットを示します。

| Service | ガイダンス |
| ------- | -------- |
| Azure Service Bus | [Shared Access Signatures への移行](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Azure Service Bus Relay | [Shared Access Signatures への移行](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Azure Managed Cache | [Azure Cache for Redis への移行](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Azure DataMarket | [Cognitive Services APIs への移行](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| BizTalk Services | [Azure App Service の Logic Apps 機能への移行](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Azure Media Services | [Azure AD 認証への移行](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [Azure Backup エージェントのアップグレード](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: https://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->

### <a name="sharepoint-customers"></a>SharePoint のユーザー

SharePoint 2013、2016、SharePoint Online のユーザーは、クラウド、オンプレミス、およびハイブリッドのシナリオでの認証のために長い間 ACS を使ってきました。 SharePoint の一部の機能とユース ケースは ACS 提供終了の影響を受けますが、それ以外には影響ありません。 次の表は、ACS を利用する最も一般的な SharePoint 機能の一部に対する移行のガイダンスをまとめたものです。

| 機能 | ガイダンス |
| ------- | -------- |
| Azure AD からのユーザーの認証 | これまで、Azure AD は SharePoint での認証に必要な SAML 1.1 トークンをサポートしておらず、SharePoint を Azure AD トークン形式と互換性があるようにする手段として ACS が使われていました。 現在は、[Azure AD アプリ ギャラリーの SharePoint オンプレミス アプリを使って SharePoint を Azure AD に直接接続する](https://docs.microsoft.com/azure/active-directory/saas-apps/sharepoint-on-premises-tutorial)ことができます。 |
| [オンプレミスの SharePoint でのアプリ認証とサーバー間認証](https://technet.microsoft.com/library/jj219571(v=office.16).aspx) | ACS の提供終了の影響を受けません。変更は必要ありません。 | 
| [SharePoint アドインに対する低信頼度の承認 (プロバイダーおよび SharePoint によるホスト)](https://docs.microsoft.com/sharepoint/dev/sp-add-ins/three-authorization-systems-for-sharepoint-add-ins) | ACS の提供終了の影響を受けません。変更は必要ありません。 |
| [SharePoint クラウド ハイブリッド検索](https://blogs.msdn.microsoft.com/spses/2015/09/15/cloud-hybrid-search-service-application/) | ACS の提供終了の影響を受けません。変更は必要ありません。 |

### <a name="web-applications-that-use-passive-authentication"></a>パッシブ認証を使用する Web アプリケーション

ユーザー認証に Access Control を使用してる Web アプリケーションを考慮し、Access Control では、Web アプリケーションの開発者や設計者向けに次の機能が提供されています。

- Windows Identity Foundation (WIF) との緊密な統合
- Google、Facebook、Yahoo、Azure Active Directory、AD FS アカウント、および Microsoft アカウント とのフェデレーション。
- 次の認証プロトコルのサポート: OAuth 2.0 Draft 13、WS-Trust、Web Services Federation (WS-Federation)。
- 次のトークン形式のサポート: JSON Web トークン (JWT)、SAML 1.1、SAML 2.0、Simple Web Token (SWT)。
- ホーム領域検出のエクスペリエンス (WIF に統合)。サインインに使用するアカウントの種類を選択できます。 このエクスペリエンスは Web アプリケーションによってホストされ、完全にカスタマイズ可能です。
- トークン変換。Web アプリケーションが Access Control から受け取った要求の豊富なカスタマイズが可能です。これには次のようなものがあります。
    - ID プロバイダーからの要求をパススルー。
    - その他のカスタム要求の追加。
    - 特定の条件下で要求を発行する単純な if-then ロジック。

残念ながら、これらに匹敵するすべての機能を提供する単一のサービスはありません。 必要な Access Control の機能を評価して、[Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/) (Azure AD)、[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) (Azure AD B2C)、または他のクラウド認証サービスのいずれを使用するかを検討する必要があります。

#### <a name="migrate-to-azure-active-directory"></a>Azure Active Directory への移行

候補となる経路として、アプリケーションとサービスの Azure AD との直接統合があります。 Azure AD は、Microsoft の職場または学校アカウントに対応した、クラウド ベースの ID プロバイダーです。 Azure AD は、Office 365、Azure などに対応した ID プロバイダーです。 これは、Access Control と同様のフェデレーション認証機能を提供しますが、一部の Access Control の機能をサポートしていません。 

主な例は、Facebook、Google、Yahoo などのソーシャル ID プロバイダーとのフェデレーションです。 ユーザーがこの種類の資格情報でサインインする場合、Azure AD はソリューションとして適しません。 

また、Azure AD では Access Control とまったく同じ認証プロトコルがサポートされるとは限りません。 たとえば、OAuth は Access Control と Azure AD の両方でサポートされていますが、各実装間で微妙な違いがあります。 移行に際しては、実装ごとにコードを変更する必要があります。

ただし、Azure AD には Access Control のお客様にとっての潜在的な利点がいくつかあります。 たとえば、クラウドでホストされる、Microsoft の職場または学校のアカウント (Access Control のお客様によって広く使用されているアカウント) がネイティブにサポートされます。 

Azure AD テナントは、AD FS を通じて、1 つ以上のオンプレミス Active Directory インスタンスにフェデレーションすることもできます。 そのため、クラウド ベースのユーザーだけでなく、オンプレミスでホストされているユーザーもアプリで認証することができます。 また、WS-Federation プロトコルにも対応しています。これにより、WIF を使用して、Web アプリケーションと比較的簡単に統合できます。

次の表は、Web アプリケーションに関連する Access Control の機能を、Azure AD で利用できる機能と比較したものです。 

大まかに言うと、*ユーザーのサインインに Microsoft の職場または学校アカウントだけを使用するのであれば、おそらく Azure Active Directory が移行に最適な選択肢です*。

| 機能 | Access Control でのサポート | Azure AD でのサポート |
| ---------- | ----------- | ---------------- |
| **アカウントの種類** | | |
| Microsoft の職場または学校のアカウント | サポートされています | サポートされています |
| Windows Server Active Directory および AD FS のアカウント |- サポート対象 (Azure AD テナントとのフェデレーションを使用) <br />- サポート対象 (AD FS との直接フェデレーションを使用) | Azure AD テナントとのフェデレーションによってのみサポート対象 | 
| その他のエンタープライズ ID 管理システムのアカウント |- Azure AD テナントとのフェデレーションにより可能 <br />- サポート対象 (直接フェデレーションを使用) | Azure AD テナントとのフェデレーションにより可能 |
| 個人的な使用のための Microsoft アカウント | サポートされています | サポート対象 (Azure AD v2.0 OAuth プロトコルを使用。その他のプロトコルは使用できません) | 
| Facebook、Google、Yahoo アカウント | サポートされています | 未サポート |
| **プロトコルと SDK の互換性** | | |
| WIF | サポートされています | サポート対象。ただし、使用できる手順に制限あり |
| WS-Federation | サポートされています | サポートされています |
| OAuth 2.0 | Draft 13 をサポート | RFC 6749 (ほとんどの最新の仕様) をサポート |
| WS-Trust | サポートされています | サポートされていません |
| **トークン形式** | | |
| JWT | Beta でサポート | サポートされています |
| SAML 1.1 | サポートされています | プレビュー |
| SAML 2.0 | サポートされています | サポートされています |
| SWT | サポートされています | サポートされていません |
| **カスタマイズ** | | |
| カスタマイズ可能なホーム領域検出/アカウント選択 UI | アプリに組み込めるダウンロード可能なコード | サポートされていません |
| カスタムのトークン署名証明書をアップロード | サポートされています | サポートされています |
| トークンの要求をカスタマイズ |- ID プロバイダーからの入力要求をパススルー<br />- ID プロバイダーからアクセス トークンを要求として取得<br />- 入力要求の値に基づいて出力要求を発行<br />- 定数値を持つ出力要求を発行 |- フェデレーション ID プロバイダーからの要求をパススルーすることはできません<br />- ID プロバイダーからアクセス トークンを要求として取得できません<br />- 入力要求の値に基づいて出力要求を発行できません<br />- 定数値を持つ出力要求を発行できます<br />- Azure AD に同期されたユーザーのプロパティに基づいて出力要求を発行できます |
| **Automation** | | |
| 構成および管理タスクを自動化 | サポート対象 (Access Control 管理サービスを使用) | サポート対象 (Microsoft Graph と Azure AD Graph API を使用) |

Azure AD がアプリケーションとサービスの最善の移行経路であると判断した場合は、アプリを Azure AD と統合する 2 つの方法を認識する必要があります。

WS-Federation または WIF を使用して Azure AD と統合する場合は、「[ギャラリー以外のアプリケーションのフェデレーション シングル サインオンを構成する方法](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)」で説明されているアプローチに従うことをお勧めします。 この記事は、Azure AD を SAML ベースのシングル サインオン用に構成する方法を説明したものですが、WS-Federation の構成にも使用できます。 このアプローチを使用するには、Azure AD Premium ライセンスが必要です。 このアプローチには 2 つの利点があります。

- Azure AD トークン カスタマイズの柔軟性をフルに活用できます。 Azure AD によって発行された要求を、Access Control によって発行された要求と一致するようにカスタマイズすることができます。 これは、ユーザー ID や名前識別子要求に特に便利です。 テクノロジが変更された後もユーザー ID の一貫性が維持されるようにするには、Azure AD によって発行されたユーザー ID を、Access Control によって発行されたユーザー ID と一致させる必要があります。
- アプリケーション固有のトークン署名証明書を構成し、その有効期間を制御できます。

> [!NOTE]
> このアプローチを使用するには、Azure AD Premium ライセンスが必要です。 Access Control をご使用のお客様で、アプリケーションのシングル サインオンを設定するためのプレミアム ライセンスが必要なお客様は、Microsoft までご連絡ください。 必要な開発者ライセンスをご提供します。

もう 1 つのアプローチは、[こちらのコード サンプル](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation)に従う方法です。この方法では、WS-Federation の設定方法の手順が若干異なります。 このコード サンプルでは、WIF を使用せず、代わりに ASP.NET 4.5 OWIN ミドルウェアを使用します。 ただし、アプリ登録の手順は、WIF を使用するアプリに対しても有効であり、Azure AD Premium ライセンスは必要ありません。 

このアプローチを選ぶ場合は、[Azure AD での署名キー ロールオーバー](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover)について理解する必要があります。 このアプローチでは、Azure AD のグローバル署名キーを使用してトークンを発行します。 既定では、WIF は署名キーを自動的に更新しません。 Azure AD がそのグローバル署名キーを回転する場合、変更を確定するために WIF 実装を準備する必要があります。 詳細については、「[Important information about signing key rollover in Azure AD](https://msdn.microsoft.com/library/azure/dn641920.aspx)」 (Azure AD の署名キーのロールオーバーに関する重要な情報) を参照してください。

OpenID Connect または OAuth プロトコル経由で Azure AD と統合できる場合は、その方法で統合することをお勧めします。 Azure AD を Web アプリケーションに統合する方法については、[Azure AD 開発者ガイド](https://aka.ms/aaddev)にさまざまなドキュメントとガイダンスが記載されています。

#### <a name="migrate-to-azure-active-directory-b2c"></a>Azure Active Directory B2C への移行

検討すべきその他の移行パスは、Azure AD B2C です。 Azure AD B2C はクラウド認証サービスです。Access Control と同様、開発者は認証と ID 管理のロジックをクラウド サービスに外部委託できます。 これは、最大数百万人ものアクティブ ユーザーを抱えることがあるコンシューマー向けアプリケーション用に設計された、有料サービスです (Free レベルと Premium レベルがあります)。

Azure AD B2C の最も魅力的な機能の 1 つは、Access Control と同様、さまざまな種類のアカウントをサポートできることです。 Azure AD B2C では、Microsoft アカウントや、Facebook、Google、LinkedIn、GitHub、Yahoo アカウントなどを使用して、ユーザーをサインインさせることができます。 さらに Azure AD B2C では、"ローカル アカウント"、つまりユーザーがアプリケーション専用に作成するユーザー名とパスワードもサポートされます。 Azure AD B2C には、サインイン フローのカスタマイズに使用できる豊富な機能拡張も用意されています。 

ただし、Azure AD B2C では、Access Control のお客様が必要とする可能性がある、広範な認証プロトコルやトークン形式はサポートされません。 また、Azure AD B2C では、トークンを取得したり、ユーザーに関する追加情報を ID プロバイダーや Microsoft などにクエリしたりすることもできません。

次の表は、Web アプリケーションに関連する Access Control の機能を、Azure AD B2C で利用できる機能と比較したものです。 大まかに言えば、*おそらく Azure AD B2C は、アプリケーションがコンシューマー向けの場合、またはさまざまな種類のアカウントをサポートする場合の移行に適した選択肢です。*

| 機能 | Access Control でのサポート | Azure AD B2C でのサポート |
| ---------- | ----------- | ---------------- |
| **アカウントの種類** | | |
| Microsoft の職場または学校のアカウント | サポートされています | サポート対象 (カスタム ポリシーを使用)  |
| Windows Server Active Directory および AD FS のアカウント | サポート対象 (AD FS との直接フェデレーションを使用) | サポート対象 (カスタム ポリシーを使用した SAML フェデレーション経由) |
| その他のエンタープライズ ID 管理システムのアカウント | サポート対象 (WS-Federation 上の直接フェデレーションを使用) | サポート対象 (カスタム ポリシーを使用した SAML フェデレーション経由) |
| 個人的な使用のための Microsoft アカウント | サポートされています | サポートされています | 
| Facebook、Google、Yahoo アカウント | サポートされています | Facebook と Google はネイティブにサポートされ、Yahoo はカスタム ポリシーを使用して OpenID Connect フェデレーションを介してサポートされます |
| **プロトコルと SDK の互換性** | | |
| Windows Identity Foundation (WIF) | サポートされています | サポートされていません |
| WS-Federation | サポートされています | サポートされていません |
| OAuth 2.0 | Draft 13 をサポート | RFC 6749 (ほとんどの最新の仕様) をサポート |
| WS-Trust | サポートされています | サポートされていません |
| **トークン形式** | | |
| JWT | Beta でサポート | サポートされています |
| SAML 1.1 | サポートされています | サポートされていません |
| SAML 2.0 | サポートされています | サポートされていません |
| SWT | サポートされています | サポートされていません |
| **カスタマイズ** | | |
| カスタマイズ可能なホーム領域検出/アカウント選択 UI | アプリに組み込めるダウンロード可能なコード | カスタム CSS を使用して完全にカスタマイズできます |
| カスタムのトークン署名証明書をアップロード | サポートされています | 証明書ではなくカスタム署名キーがサポート対象です (カスタム ポリシーを使用) |
| トークンの要求をカスタマイズ |- ID プロバイダーからの入力要求をパススルー<br />- ID プロバイダーからアクセス トークンを要求として取得<br />- 入力要求の値に基づいて出力要求を発行<br />- 定数値を持つ出力要求を発行 |- ID プロバイダーからの要求をパススルーできます。 一部の要求にはカスタム ポリシーが必要です<br />- ID プロバイダーからアクセス トークンを要求として取得できません<br />- カスタム ポリシーを使用して入力要求の値に基づいて出力要求を発行できます<br />- カスタム ポリシーを使用して定数値を持つ出力要求を発行できます |
| **Automation** | | |
| 構成および管理タスクを自動化 | サポート対象 (Access Control 管理サービスを使用) |- Azure AD Graph API を使用してユーザーを作成できます<br />- B2C テナント、アプリケーション、またはポリシーをプログラムで作成することはできません |

Azure AD B2C がアプリケーションとサービスの最善の移行経路であると判断した場合は、次のリソースで開始してください。

- [Azure AD B2C ドキュメント](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Azure AD B2C カスタム ポリシー](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Azure AD B2C の価格](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Ping Identity または Auth0 への移行

場合によっては、Web アプリケーションの Access Control を Azure AD や Azure AD B2C に置き換えるために、大量のコード変更が必要になることがあります。 たとえば、次のような場合です。

- Web アプリケーションで、Google や Facebook などのソーシャル ID プロバイダーにサインインするために、WIF または WS-Federation を使用してしている。
- Web アプリケーションで、WS-Federation プロトコルを使用して、エンタープライズ ID プロバイダーへの直接フェデレーションを実行している。
- Web アプリケーションで、Access Control によって発行されたトークン内の要求として、ソーシャル ID プロバイダー (Google や Facebook など) によって発行されたアクセス トークンが必要である。
- Web アプリケーションで、Azure AD や Azure AD B2C では再現できない複雑なトークン変換を使用している。
- マルチテナントの Web アプリケーションで、多数の ID プロバイダーへのフェデレーションを一元管理するために、ACS を使用している

以上のような場合には、Web アプリケーションを別のクラウド認証サービスに移行することを検討してください。 次のオプションを検討することをお勧めします。 これらのオプションでは、Access Control と同様の機能が提供されます。

|     |     |
| --- | --- |
| ![この図は Auth0 のロゴを示します](./media/active-directory-acs-migration/rsz_auth0.png) | [Auth0](https://auth0.com/acs) は柔軟なクラウド ID サービスで、[Access Control のお客様向けの高レベルな移行ガイダンス](https://auth0.com/acs)が作成されているほか、ACS で提供される機能がほぼすべてサポートされます。 |
| ![この図は Ping Identity のロゴを示します](./media/active-directory-acs-migration/rsz_ping.png) | [Ping Identity](https://www.pingidentity.com) では、ACS によく似た 2 つのソリューションが提供されます。 PingOne は、ACS と同じ機能を多数サポートしているクラウド ID サービスであり、PingFederate は、より高度な柔軟性を提供する、類似のオンプレミス ID 製品です。 これらの製品の使用の詳細については、[ACS の提供終了に関する Ping のガイダンス](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html)を参照してください。 |

Microsoft では、Access Control を使用するすべてのお客様に、アプリやサービスの最適な移行経路を提供し、Access Control の移行に伴う作業を最小限に抑えていただく目的で、Ping Identity および Auth0 と連携しています。

<!--

## SharePoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>アクティブな認証を使用する Web サービス

Access Control によって発行されたトークンでセキュリティ保護された Web サービスに対して、Access Control は次の機能を提供しています。

- 1 つ以上の*サービス ID* を Access Control 名前空間に登録する機能。 サービス ID は、トークンを要求するために使用できます。
- 次の種類の資格情報を使用してトークンを要求するための OAuth WRAP および OAuth 2.0 Draft 13 プロトコルのサポート。
    - サービス ID に対して作成されるシンプルなパスワード
    - 対称キーまたは X509 証明書を使用した署名付き SWT
    - 信頼できる ID プロバイダー (通常は AD FS インスタンス) によって発行された SAML トークン
- 次のトークン形式のサポート: JWT、SAML 1.1、SAML 2.0、SWT。
- シンプルなトークン変換ルール。

Access Control のサービス ID は、通常はサーバー対サーバー認証の実装に使用されます。 

#### <a name="migrate-to-azure-active-directory"></a>Azure Active Directory への移行

この種類の認証フローに対する推奨事項は、[Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/) に移行することです。 Azure AD は、Microsoft の職場または学校アカウントに対応した、クラウド ベースの ID プロバイダーです。 Azure AD は、Office 365、Azure などに対応した ID プロバイダーです。 

OAuth クライアント資格情報の付与の Azure AD 実装を使用して、Azure AD をサーバー対サーバー認証に使用することもできます。 次の表は、サーバー対サーバー認証での Access Control の機能を、Azure AD で使用可能な機能と比較したものです。

| 機能 | Access Control でのサポート | Azure AD でのサポート |
| ---------- | ----------- | ---------------- |
| Web サービスを登録する方法 | Access Control 管理ポータルで証明書利用者を作成します | Azure Portal で Azure AD Web アプリケーションを作成します |
| クライアントを登録する方法 | Access Control 管理ポータルでサービス ID を作成します | Azure Portal で別の Azure AD Web アプリケーションを作成します |
| 使用されるプロトコル |- OAuth WRAP プロトコル<br />- OAuth 2.0 Draft 13 クライアント資格情報の付与 | OAuth 2.0 クライアント資格情報の付与 |
| クライアント認証方法 |- シンプルなパスワード<br />- 署名付き SWT<br />- フェデレーション ID プロバイダーからの SAML トークン |- シンプルなパスワード<br />- 署名付き JWT |
| トークン形式 |- JWT<br />- SAML 1.1<br />- SAML 2.0<br />- SWT<br /> | JWT のみ |
| トークン変換 |- カスタム要求の追加<br />- シンプルな if-then 要求発行ロジック | カスタム要求の追加 | 
| 構成および管理タスクを自動化 | サポート対象 (Access Control 管理サービスを使用) | サポート対象 (Microsoft Graph と Azure AD Graph API を使用) |

サーバー対サーバーのシナリオの実装のガイダンスについては、次のリソースをご覧ください。

- [Azure AD 開発者ガイド](https://aka.ms/aaddev) の「サービス間」セクション
- [シンプルなパスワード クライアント資格情報を使用したデーモン コード サンプル](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [証明書クライアント資格情報を使用したデーモン コード サンプル](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Ping Identity または Auth0 への移行

場合によっては、Access Control を Azure AD クライアント資格情報や OAuth の付与の実装へと移行するために、大量のコード変更が必要になることがあります。 たとえば、次のような場合です。

- サーバー対サーバー認証で、JWT 以外のトークン形式を使用している。
- サーバー対サーバー認証で、外部の ID プロバイダーから提供される入力トークンを使用している。
- サーバー対サーバー認証で、Azure AD では再現できないトークン変換ルールを使用している。

以上のような場合には、Web アプリケーションを別のクラウド認証サービスに移行することを検討してください。 次のオプションを検討することをお勧めします。 これらのオプションでは、Access Control と同様の機能が提供されます。

|     |     |
| --- | --- |
| ![この図は Auth0 のロゴを示します](./media/active-directory-acs-migration/rsz_auth0.png) | [Auth0](https://auth0.com/acs) は柔軟なクラウド ID サービスで、[Access Control のお客様向けの高レベルな移行ガイダンス](https://auth0.com/acs)が作成されているほか、ACS で提供される機能がほぼすべてサポートされます。 |
| ![この図は Ping Identity のロゴを示します](./media/active-directory-acs-migration/rsz_ping.png) | [Ping Identity](https://www.pingidentity.com) では、ACS によく似た 2 つのソリューションが提供されます。 PingOne は、ACS と同じ機能を多数サポートしているクラウド ID サービスであり、PingFederate は、より高度な柔軟性を提供する、類似のオンプレミス ID 製品です。 これらの製品の使用の詳細については、[ACS の提供終了に関する Ping のガイダンス](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html)を参照してください。 |

Microsoft では、Access Control を使用するすべてのお客様に、アプリやサービスの最適な移行経路を提供し、Access Control の移行に伴う作業を最小限に抑えていただく目的で、Ping Identity および Auth0 と連携しています。

#### <a name="passthrough-authentication"></a>パススルー認証

任意のトークン変換によるパススルー認証の場合、ACS に相当する Microsoft のテクノロジはありません。 お客様がそれを必要としている場合は、Auth0 が最も近い類似のソリューションを提供しています。

## <a name="questions-concerns-and-feedback"></a>質問、懸念事項、フィードバック

Microsoft では、Access Control のお客様の多くがこの記事を読んでも明確な移行パスを見つけられず、 適切なプランの決定において支援やガイダンスを必要とする可能性があることを認識しています。 移行シナリオと質問について話し合いをご希望の場合は、このページにコメントを残してください。
