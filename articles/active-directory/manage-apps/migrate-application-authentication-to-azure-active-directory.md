---
title: アプリケーション認証を Azure Active Directory に移行する
description: このホワイトペーパーでは、Azure AD へのアプリケーション認証の移行の計画と利点について詳しく説明します。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 02/05/2021
ms.author: kenwith
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e05a7af3f0b95470432b4fb9602e1b41da9f72f
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952968"
---
# <a name="migrate-application-authentication-to-azure-active-directory"></a>アプリケーション認証を Azure Active Directory に移行する

## <a name="about-this-paper"></a>本書について

このホワイトペーパーでは、Azure AD へのアプリケーション認証の移行の計画と利点について詳しく説明します。 これは、Azure 管理者と ID 担当者向けに設計されています。

プロセスは 4 つのフェーズに分割されており、それぞれに詳細な計画と終了基準があります。これは、移行戦略を計画し、Azure AD 認証によって組織の目標がどのようにサポートされるかを理解するのに役立つように設計されています。

## <a name="introduction"></a>はじめに

現在、あなたの組織では、ユーザーの作業がはかどるようにするために大量のアプリケーション (アプリ) が必要とされています。 引き続き、毎日アプリを追加、開発、または削除する可能性があります。 ユーザーはさまざまな企業や個人のデバイス、および場所からこれらのアプリケーションにアクセスします。 彼らは次のようなさまざまな方法でアプリを開きます。

- 会社のホームページまたはポータルを経由する

- ブラウザーでブックマークを設定する

- サービスとしてのソフトウェア (SaaS) アプリのベンダーの URL を使用する

- モバイル デバイスまたはアプリケーション管理 (MDM または MAM) ソリューションを介して、ユーザーのデスクトップまたはモバイル デバイスに直接プッシュされたリンクを使用する

アプリケーションでは次の種類の認証が使用されている可能性があります。

- オンプレミスのフェデレーション ソリューション (Active Directory フェデレーション サービス (AD FS) や Ping など)

- Active Directory (Kerberos 認証や Windows 統合認証など)

- その他のクラウドベースの ID およびアクセス管理 (IAM) ソリューション (Okta や Oracle など)

- オンプレミスの Web インフラストラクチャ (IIS や Apache など)

- クラウドでホストされるインフラストラクチャ (Azure や AWS など)

**確実にユーザーがアプリケーションに簡単かつ安全にアクセスできるようにするための目標は、オンプレミスとクラウドの環境全体で 1 セットのアクセス制御とポリシーを使用することです。**

[Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) によって提供されるユニバーサル ID プラットフォームでは、ユーザー、パートナー、顧客に対し、任意のプラットフォームやデバイスから必要なアプリケーションにアクセスして共同作業を行うための 1 つの ID が提供されます。

![Azure Active Directory の接続の図](media/migrating-application-authentication-to-azure-active-directory-1.jpg)

Azure AD には、[ID 管理機能の完全なスイート](../fundamentals/active-directory-whatis.md#which-features-work-in-azure-ad)が備わっています。 アプリの認証と承認を Azure AD に標準化することで、これらの機能によって提供される利点が得られます。

その他の移行リソースについては、[https://aka.ms/migrateapps](./migration-resources.md) を参照してください

## <a name="benefits-of-migrating-app-authentication-to-azure-ad"></a>Azure AD にアプリ認証を移行する利点

アプリ認証を Azure AD に移行すると、リスクとコストの管理、生産性の向上、コンプライアンスとガバナンスの要件への対応に役立ちます。

### <a name="manage-risk"></a>リスクの管理

アプリを保護するには、すべてのリスク要因を完全に把握する必要があります。 アプリを Azure AD に移行すると、セキュリティ ソリューションが統合されます。 これを使用すると、次のことができます。

- [条件付きアクセス ポリシー](../conditional-access/overview.md)、[多要素認証](../authentication/concept-mfa-howitworks.md)、リアルタイムのリスクベースの [Identity Protection](../identity-protection/overview-identity-protection.md) テクノロジを使用して、アプリケーションおよび関連する企業データへのセキュリティで保護されたユーザー アクセスを強化する。

- [Just-In-Time](../../azure-resource-manager/managed-applications/request-just-in-time-access.md) の管理者アクセスを使用して、環境への特権ユーザーのアクセスを保護する。

- 最も重要なビジネス ニーズに応じて、[Azure AD のマルチテナント型で地理的に分散した高可用性の設計](https://cloudblogs.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/)を使用する。

- 既にデプロイしている可能性のある[セキュリティで保護されたハイブリッド アクセス パートナー統合](https://aka.ms/secure-hybrid-access)のいずれかを使用して、レガシ アプリケーションを保護する。

### <a name="manage-cost"></a>コストを管理する

組織に、複数の ID アクセス管理 (IAM) ソリューションが設置されている場合があります。 1 つの Azure AD インフラストラクチャに移行することは、IAM ライセンスへの依存関係 (オンプレミスまたはクラウド内) とインフラストラクチャ コストを削減する機会になります。 Microsoft 365 ライセンス経由で既に Azure AD に支払っている場合、別の IAM ソリューションの追加コストを支払う理由はありません。

**Azure AD を使用すると、次のようにしてインフラストラクチャのコストを削減できます。**

- [Azure AD アプリケーション プロキシ](./application-proxy.md)を使用して、オンプレミス アプリへのセキュリティで保護されたリモート アクセスを提供する。

- [信頼されたユニバーサル ID プロバイダーとして Azure AD を設定する](../hybrid/plan-connect-user-signin.md#choosing-the-user-sign-in-method-for-your-organization)ことによって、テナントのオンプレミスの資格情報アプローチからアプリを分離する。

### <a name="increase-productivity"></a>生産性の向上

経済上およびセキュリティ上の利点により、組織での Azure AD の導入が促進されますが、ユーザーも利点を得られる場合は、完全な導入とコンプライアンスの可能性が高くなります。 Azure AD では、次のことができます。

- 任意のデバイスや場所から、任意のアプリケーションにシームレスかつ安全にアクセスすることにより、エンドユーザーの[シングル サインオン (SSO)](./what-is-single-sign-on.md) エクスペリエンスを向上させる。

- [セルフサービス パスワード リセット](../authentication/concept-sspr-howitworks.md)や[セルフサービス グループ管理](../enterprise-users/groups-self-service-management.md)など、セルフサービスの IAM 機能を使用する。

- クラウドとオンプレミスの環境全体のユーザーごとに 1 つの ID のみを管理することで、管理オーバーヘッドを減らす。

  - Azure AD ID に基づく ([Azure AD ギャラリー](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)の) ユーザー アカウントの[自動プロビジョニング](../app-provisioning/user-provisioning.md)
  - [Azure portal](https://portal.azure.com/) の MyApps パネルからすべてのアプリにアクセスする

- 開発者が自分のアプリへのアクセスをセキュリティで保護し、Microsoft Authentication Library (MSAL) で [Microsoft ID プラットフォーム](../develop/v2-overview.md)を使用して、エンドユーザー エクスペリエンスの向上を可能にする。

- [Azure AD B2B コラボレーション](../external-identities/what-is-b2b.md)を使用して、パートナーがクラウド リソースにアクセスできるようにする。 クラウド リソースにより、パートナーとのポイントツーポイント フェデレーションを構成するオーバーヘッドがなくなります。

### <a name="address-compliance-and-governance"></a>コンプライアンスとガバナンスに対応する

企業アクセス ポリシーを適用し、統合された監査ツールと API を使用してアプリケーションおよび関連データへのユーザー アクセスを監視することにより、規制要件に確実に準拠するようにします。 Azure AD では、[セキュリティ情報とイベント管理 (SIEM) ツール](../reports-monitoring/plan-monitoring-and-reporting.md)を使用したレポート経由で、アプリケーション サインインを監視できます。 ポータルまたは API からレポートにアクセスし、アプリケーションにアクセスできるユーザーをプログラムで監査し、アクセス レビューを通じて非アクティブなユーザーへのアクセスを削除できます。

## <a name="plan-your-migration-phases-and-project-strategy"></a>移行フェーズとプロジェクト戦略を計画する

テクノロジ プロジェクトが失敗した場合、予測が一致しない、適切な利害関係者が関与していない、またはコミュニケーション不足が原因であることがよくあります。 プロジェクト自体を計画し、確実に成功するようにしてください。

### <a name="the-phases-of-migration"></a>移行のフェーズ

ツールの詳細を確認する前に、移行プロセスをどのように検討するかを理解しておく必要があります。 いくつかの直接顧客ワークショップでは、次の 4 つのフェーズをお勧めします。

![移行フェーズの図](media/migrating-application-authentication-to-azure-active-directory-2.jpg)

### <a name="assemble-the-project-team"></a>プロジェクト チームを結成する

アプリケーションの移行はチームの作業です。あなたは確実に重要な職位をすべて埋める必要があります。 シニア ビジネス リーダーからのサポートが重要です。 必ず、エグゼクティブ スポンサー、ビジネス上の意思決定者、対象分野の専門家 (SME) の適切なセットを含めるようにしてください。

移行プロジェクト中は、組織の規模と構造に応じて、1 人のユーザーが複数の役割を果たす場合や、複数のユーザーがそれぞれの役割を果たす場合があります。 また、セキュリティ ランドスケープにおいて重要な役割を果たす他のチームにも依存関係がある場合があります。

次の表には、重要な役割とその貢献が含まれています。

| Role          | 貢献                                              |
| ------------- | ---------------------------------------------------------- |
| **プロジェクト マネージャー** | プロジェクトを導く責任があるプロジェクト コーチは次のことを行います。<br /> - 経営幹部からのサポートを得る<br /> - 利害関係者を参加させる<br /> - スケジュール、ドキュメント、コミュニケーションを管理する |
| **ID アーキテクトまたは Azure AD アプリ管理者** | 以下のことを担当します。<br /> - 利害関係者と連携してソリューションを設計する<br /> - 運用チームにハンドオフするためのソリューションの設計と運用手順を文書化する<br /> - 運用前および運用環境を管理する |
| **オンプレミスの AD 運用チーム** | AD フォレスト、LDAP ディレクトリ、HR システムなど、さまざまなオンプレミス ID ソースを管理する組織。<br /> - 同期する前に必要な修復タスクを実行する<br /> - 同期に必要なサービス アカウントを提供する<br /> - Azure AD へのフェデレーションを構成するためのアクセスを提供する |
| **IT サポート マネージャー** | ヘルプデスクの観点から、この変更のサポート可能性に関する情報を提供できる、IT サポート組織の代表。 |
| **セキュリティ所有者**  | 計画が組織のセキュリティ要件を満たすことを保証できるセキュリティ チームの代表。 |
| **アプリケーションの技術所有者** | Azure AD と統合されるアプリとサービスの技術所有者が含まれます。 彼らは、同期プロセスに含める必要があるアプリケーションの ID 属性を提供します。 通常、CSV 担当者と関係があります。 |
| **アプリケーションのビジネス所有者** | ユーザーの観点から、この変更のユーザー エクスペリエンスと有用性に関する情報を提供でき、アプリケーションの全体的なビジネスの側面 (アクセスの管理を含む場合がある) を所有する代表的な同僚。 |
| **ユーザーのパイロット グループ** | 日常業務の一部としてパイロット エクスペリエンスをテストし、その他のデプロイをガイドするためのフィードバックを提供するユーザー。 |

### <a name="plan-communications"></a>連絡を計画する

効果的なビジネス エンゲージメントとコミュニケーションは成功への鍵です。 情報を取得し、スケジュールの更新を常に把握するための手段を利害関係者とエンドユーザーに与えることが重要です。 移行の価値、予想されるタイムライン、および一時的なビジネスの中断を計画する方法について、すべてのユーザーを対象に教育します。 ブリーフィング セッション、電子メール、1 対 1 の会議、バナー、タウンホールなど、複数の手段を使用します。

アプリに対して選択したコミュニケーション戦略に基づいて、保留中のダウンタイムをユーザーに通知することができます。 また、デプロイの延期が必要となる最近の変更やビジネスへの影響がないことを確認する必要もあります。

次の表で、利害関係者が常に状況を把握できるようにするために推奨される最小限のコミュニケーションについて説明します。

**計画フェーズとプロジェクト戦略**:

| 通信      | 対象ユーザー                                          |
| ------------------ | ------------------------------------------------- |
| プロジェクトの認識とビジネスまたは技術的価値 | エンドユーザーを除くすべて |
| パイロット アプリの要請 | - アプリのビジネス所有者<br />- アプリの技術所有者<br />- アーキテクトと ID チーム |

**フェーズ 1 - 検出してスコープを設定する**:

| 通信      | 対象ユーザー                                          |
| ------------------ | ------------------------------------------------- |
| - アプリケーション情報の要請<br />- スコーピング演習の結果 | - アプリの技術所有者<br />- アプリのビジネス所有者 |

**フェーズ 2 - アプリを分類し、パイロットを計画する**:

| 通信      | 対象ユーザー                                          |
| ------------------ | ------------------------------------------------- |
| - 分類の結果と移行スケジュールにおける意味<br />- 事前の移行スケジュール | - アプリの技術所有者<br /> - アプリのビジネス所有者 |

**フェーズ 3 – 移行とテストを計画する**:

| 通信      | 対象ユーザー                                          |
| ------------------ | ------------------------------------------------- |
| - アプリケーションの移行テストの結果 | - アプリの技術所有者<br />- アプリのビジネス所有者 |
| - 移行が予定されていることの通知と、その結果として得られるエンドユーザー エクスペリエンスについての説明。<br />- 現在必要な作業、フィードバック、ヘルプの入手方法を含む、ダウンタイムの予定と完了の伝達 | - エンド ユーザー (およびその他すべて) |

**フェーズ 4 – 管理し、分析情報を得る**:

| 通信      | 対象ユーザー                                          |
| ------------------ | ------------------------------------------------- |
| 利用可能な分析とアクセス方法 | - アプリの技術所有者<br />- アプリのビジネス所有者 |

### <a name="migration-states-communication-dashboard"></a>移行の状態に関するコミュニケーション ダッシュボード

移行プロジェクトの全体的な状態を伝えることは、進行状況を示すうえで重要であり、移行が予定されているアプリの所有者がその移行を準備するのに役立ちます。 Power BI またはその他のレポート ツールを使用して、シンプルなダッシュボードを作成することにより、移行中にアプリケーションの状態を可視化することができます。

使用を検討する可能性がある移行の状態は次のとおりです。

| 移行の状態       | 行動計画                                   |
| ---------------------- | --------------------------------------------- |
| **最初の要求** | アプリを見つけて、詳細について所有者に問い合わせる |
| **評価の完了** | アプリの所有者がアプリの要件を評価し、アプリのアンケートを返す</td>
| **構成が進行中** | Azure AD に対する認証を管理するために必要な変更を進める |
| **テスト構成に成功** | 変更を評価し、テスト環境のテスト Azure AD テナントに対してアプリを認証する |
| **運用環境の構成に成功** | 運用 AD テナントに対して動作するように構成を変更し、テスト環境でアプリの認証を評価する |
| **完了または署名** | アプリの変更を運用環境にデプロイし、運用 Azure AD テナントに対して実行する |

これにより、アプリの所有者は、アプリが移行のために稼働しているときのアプリの移行とテストのスケジュール、および既に移行されている他のアプリからの結果を確実に把握できます。 また、所有者が移行中のアプリの問題を報告および表示できるように、バグ トラッカー データベースへのリンクを提供することも検討できます。

### <a name="best-practices"></a>ベスト プラクティス

お客様とパートナーの成功事例および推奨されるベスト プラクティスを以下に示します。

- [Azure Active Directory への移行プロセスを改善するための 5 つのヒント](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Five-tips-to-improve-the-migration-process-to-Azure-Active/ba-p/445364)。これは、お客様が Microsoft のクラウド ソリューションを安全にデプロイするのを支援することに重点を置いたパートナー ネットワークのメンバーである、Patriot Consulting によるものです。

- [Azure AD アプリケーションの移行のリスク管理戦略を策定する](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Develop-a-risk-management-strategy-for-your-Azure-AD-application/ba-p/566488)。これは、IAM およびリスク管理ソリューションに重点を置いたパートナーである、Edgile によるものです。

## <a name="phase-1-discover-and-scope-apps"></a>フェーズ 1:アプリを検出してスコープを設定する

**アプリケーションの検出と分析は、適切な出発点を提供するための基本的な演習です。** すべてを把握していない場合があるため、不明なアプリに対応できるように準備する必要があります。

### <a name="find-your-apps"></a>自分のアプリを見つける

アプリケーションを移行する場合は、まず、どのアプリを移行するのか、どれを維持する必要があるのか (存在する場合)、どのアプリを非推奨にするのかを決定します。 組織で使用しないアプリを非推奨にする機会は常にあります。 組織でアプリを見つける方法はいくつかあります。 **アプリの検出時に、確実に開発中および計画されたアプリを含めるようにします。今後のすべてのアプリで認証に Azure AD を使用します。**

**Active Directory フェデレーション サービス (AD FS) を使用して、正しいアプリ インベントリを収集するには、次のようにします。**

- **Azure AD Connect Health を使用する**。 Azure AD Premium ライセンスをお持ちの場合は、オンプレミス環境でアプリの使用状況を分析するために [Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md) をデプロイすることをお勧めします。 [ADFS アプリケーション レポート](./migrate-adfs-application-activity.md) (プレビュー) を使用して、移行可能な ADFS アプリケーションを検出し、移行するアプリケーションの準備状況を評価することができます。 移行の完了後にクラウドに移動したら、組織内のシャドウ IT を継続的に監視できるように [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery) をデプロイします。

- **AD FS ログの解析**。 Azure AD Premium ライセンスをお持ちでない場合は、[PowerShell](https://github.com/AzureAD/Deployment-Plans/tree/master/ADFS%20to%20AzureAD%20App%20Migration) に基づく ADFS から Azure AD へのアプリの移行ツールを使用することをお勧めします。 この[ソリューション ガイド](./migrate-adfs-apps-to-azure.md)を参照してください:

[Active Directory フェデレーション サービス (AD FS) から Azure AD へのアプリの移行](./migrate-adfs-apps-to-azure.md)に関するページ。

### <a name="using-other-identity-providers-idps"></a>他の ID プロバイダー (IdP) の使用

他の ID プロバイダー (Okta や Ping など) の場合は、それらのツールを使用してアプリケーション インベントリをエクスポートできます。 組織内の Web アプリに対応する Active Directory に登録されているサービス プリンシパルの確認を検討することをお勧めします。

### <a name="using-cloud-discovery-tools"></a>Cloud Discovery ツールの使用

クラウド環境では、ご利用のクラウド サービス全体にわたるサイバー攻撃の脅威を検出し、対処するために、豊富な表示機能、データ送受信の制御、高度な分析が必要です。 次のツールを使用して、クラウド アプリのインベントリを収集できます。

- **クラウド アクセス セキュリティ ブローカー (CASB**) – [CASB](/cloud-app-security/) は通常、ファイアウォールと共に機能し、従業員のクラウド アプリケーションの使用状況を可視化します。これは、サイバーセキュリティの脅威から企業データを保護するのに役立ちます。 CASB レポートは、組織内で最も使用されているアプリと、Azure AD に移行する初期ターゲットを特定するのに役立つ場合があります。

- **Cloud Discovery** - [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery) を構成することにより、クラウド アプリの使用状況を把握し、承認されていないまたはシャドウ IT アプリを検出できます。

- **API** - クラウド インフラストラクチャに接続されているアプリでは、それらのシステム上の API とツールを使用して、ホストされているアプリのインベントリを確認できます。 Azure 環境では、次のことを行います。

  - [Get-AzureWebsite](/powershell/module/servicemanagement/azure.service/get-azurewebsite) コマンドレットを使用して、Azure Web サイトに関する情報を取得する。

  - [Get-AzureRMWebApp](/powershell/module/azurerm.websites/get-azurermwebapp) コマンドレットを使用して、Azure Web Apps に関する情報を取得する。
D
  - [Appcmd.exe](/iis/get-started/getting-started-with-iis/getting-started-with-appcmdexe#working-with-sites-applications-virtual-directories-and-application-pools) を使用して、Windows コマンド ラインから Microsoft IIS で実行されているすべてのアプリを見つけることができます。

  - [アプリケーション](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity)と[サービス プリンシパル](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity)を使用して、Azure AD のディレクトリにあるアプリとアプリ インスタンスに関する情報を取得する。

### <a name="using-manual-processes"></a>手動プロセスの使用

前述の自動化されたアプローチを使用すると、アプリケーションを適切に処理できます。 しかし、すべてのユーザー アクセス領域で十分な範囲を確保するために、次のようにすることを検討してください。

- 組織内のさまざまなビジネス所有者に連絡して、組織内で使用されているアプリケーションを見つける。

- プロキシ サーバーで HTTP 検査ツールを実行するか、プロキシ ログを分析して、トラフィックが一般的にルーティングされる場所を確認する。

- 人気のある会社のポータル サイトからの Web ログを調べ、ユーザーが最もアクセスするリンクを確認する。

- 経営幹部やその他の主要なビジネス メンバーに連絡し、ビジネスクリティカル アプリが対象であることを保証する。

### <a name="type-of-apps-to-migrate"></a>移行するアプリの種類

アプリが見つかったら、組織内の次の種類のアプリを識別します。

- 既に先進認証プロトコルを使用しているアプリ

- 最新化することを選択したレガシ認証プロトコルを使用しているアプリ

- 最新化しないことを選択したレガシ認証プロトコルを使用しているアプリ

- 新しい基幹業務 (LoB) アプリ

### <a name="apps-that-use-modern-authentication-already"></a>既に先進認証を使用しているアプリ

既に最新化されているアプリは、Azure AD に移行される可能性が最も高くなります。 これらのアプリでは既に先進認証プロトコル (SAML や OpenID Connect など) を使用しており、Azure AD で認証するように再構成することができます。

[Azure AD アプリ ギャラリー](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)の選択項目に加えて、組織内に既に存在するアプリや、Azure AD ギャラリーに含まれていないベンダーからのサードパーティ製アプリ ([ギャラリー以外のアプリケーション](./add-application-portal.md)) もあります。

最新化することを選択したレガシ アプリ

最新化を行うレガシ アプリでは、コア認証と承認のために Azure AD に移行することで、[Microsoft Graph](https://developer.microsoft.com/graph/gallery/?filterBy=Samples,SDKs) および[インテリジェント セキュリティ グラフ](https://www.microsoft.com/security/operations/intelligence?rtc=1)で提供する必要のあるすべての機能と豊富なデータが解放されます。

これらのアプリケーションについては、レガシ プロトコル (Windows 統合認証、Kerberos の制約付き委任、HTTP ヘッダーベースの認証など) から最新のプロトコル (SAML や OpenID Connect など) に **認証スタック コードを更新する** ことをお勧めします。

### <a name="legacy-apps-that-you-choose-not-to-modernize"></a>最新化しないことを選択したレガシ アプリ

レガシ認証プロトコルを使用している特定のアプリでは、ビジネス上の理由により、認証の最新化が適切ではない場合があります。 これには、次の種類のアプリが含まれます。

- コンプライアンスまたは制御上の理由により、オンプレミスに保持されているアプリ。

- 変更したくないオンプレミスの ID またはフェデレーション プロバイダーに接続されているアプリ。

- 移動する予定がないオンプレミスの認証標準を使用して開発されたアプリ

Azure AD はこれらのレガシ アプリに大きな利点をもたらすことができます。これらのアプリに対して、そのアプリにまったく触れることなく、[多要素認証](../authentication/concept-mfa-howitworks.md)、[条件付きアクセス](../conditional-access/overview.md)、[Identity Protection](../identity-protection/index.yml)、[委任されたアプリケーション アクセス](./access-panel-manage-self-service-access.md)、[アクセス レビュー](../governance/manage-user-access-with-access-reviews.md#create-and-perform-an-access-review)など、最新の Azure AD セキュリティとガバナンス機能を有効にすることができるからです。

まず、シンプルな認証方法 (パスワード保管など) を使用し、Azure AD の [アプリケーション プロキシ](./application-proxy-configure-single-sign-on-password-vaulting.md)で **これらのアプリをクラウドに拡張** してユーザーを迅速に移行するか、既にデプロイされている可能性のあるアプリケーション配信コントローラーとの [パートナー統合](https://azure.microsoft.com/services/active-directory/sso/secure-hybrid-access/)を利用します。

### <a name="new-line-of-business-lob-apps"></a>新しい基幹業務 (LoB) アプリ

通常は、組織の社内で使用する LoB アプリを開発します。 パイプラインに新しいアプリがある場合は、[Microsoft ID プラットフォーム](../develop/v2-overview.md)を使用して OpenID Connect を実装することをお勧めします。

### <a name="apps-to-deprecate"></a>非推奨にするアプリ

所有者およびメンテナンスと監視が明確でないアプリは、組織にセキュリティ上のリスクをもたらします。 次の場合は、アプリケーションを非推奨にすることを検討してください。

- 他のシステムと **機能が非常に重複している** • **ビジネス所有者がいない**

- **使用されていないこと** が明らかである。

**影響が大きい、ビジネスクリティカル アプリケーションは非推奨にしない** ことをお勧めします。 そのような場合は、ビジネス所有者と協力して適切な戦略を決定してください。

### <a name="exit-criteria"></a>終了基準

次のような場合、このフェーズは成功です。

- 移行の対象となるシステムについてよく理解している (Azure AD に移動したら、削除できる)

- 以下を含むアプリの一覧がある:

  - これらのアプリの接続先はどのシステムか
  - ユーザーはどこからどのデバイスでそれらにアクセスするのか
  - それらを移行するか、非推奨にするか、[Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) に接続するか。

> [!NOTE]
> [アプリケーション検出ワークシート](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx)をダウンロードして、Azure AD 認証に移行するアプリケーションと、移動はするが [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) を使用して管理するものを記録できます。

## <a name="phase-2-classify-apps-and-plan-pilot"></a>フェーズ 2:アプリを分類し、パイロットを計画する

アプリの移行を分類することは、重要な演習です。 すべてのアプリを同時に移行して切り替える必要はありません。 各アプリに関する情報を収集したら、最初に移行する必要があるアプリと、時間がかかる可能性があるものを合理化します。

### <a name="classify-in-scope-apps"></a>対象となるアプリを分類する

これについて考える 1 つの方法は、ビジネス上の重要度、使用状況、および有効期間の軸に沿うことです。これらはそれぞれ、複数の要因に依存しています。

### <a name="business-criticality"></a>ビジネス上の重要度

ビジネス上の重要度ではビジネスごとに異なるディメンションを使用しますが、考慮する必要がある 2 つの基準は、**特徴と機能** および **ユーザー プロファイル** です。 機能が重複しているか古いアプリよりも高いポイント値を、固有機能を持つアプリに割り当てます。

![特徴と機能およびユーザー プロファイルのスペクトラム図](media/migrating-application-authentication-to-azure-active-directory-3.jpg)

### <a name="usage"></a>使用法

**使用率の高い** アプリケーションが、使用率の低いアプリよりも高い値を受け取る必要があります。 外部、経営幹部、またはセキュリティ チーム ユーザーが存在するアプリにはより高い値を割り当てます。 移行ポートフォリオ内の各アプリについて、これらの評価を完了します。

![ユーザーのボリュームとユーザーの幅のスペクトラム図](media/migrating-application-authentication-to-azure-active-directory-4.jpg)

ビジネス上の重要度と使用状況の値を決定したら、**アプリケーションの有効期間** を決定し、優先順位のマトリックスを作成できます。 以下の、そのようなマトリックスの 1 つを参照してください。

![使用状況、予想される有効期間、ビジネス上の重要度の関係を示す三角形の図](media/migrating-application-authentication-to-azure-active-directory5.jpg)

### <a name="prioritize-apps-for-migration"></a>移行するアプリに優先度を付ける

組織のニーズに基づいて、優先度の最も低いアプリまたは優先度の最も高いアプリのいずれかを使用して、アプリの移行を開始することを選択できます。

Azure AD と ID サービスの使用経験がないと思われるシナリオでは、**優先度の最も低いアプリ** を最初に Azure AD に移行することを検討してください。 これにより、ビジネスへの影響が最小限に抑えられ、勢いをつけることができます。 これらのアプリを正常に移動し、利害関係者の信頼を得たら、他のアプリの移行を続けることができます。

明確な優先度がない場合は、最初に [Azure AD ギャラリー](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)にあり、複数の ID プロバイダー (ADFS または Okta) をサポートするアプリを移動することを検討する必要があります。これらは統合が容易であるためです。 これらのアプリは、組織内で **優先度の最も高いアプリ** である可能性があります。 SaaS アプリケーションを Azure AD と統合するのに役立つように、構成手順を示す一連の[チュートリアル](../saas-apps/tutorial-list.md)を作成しました。

アプリの移行期限がある場合、これらの優先度の最も高いアプリのバケットが主なワークロードになります。 期限をずらしてもコストは変わらないため、最終的に優先度の低いアプリを選択できます。 ライセンスを更新する必要がある場合でも、それはわずかなものです。

この分類に加え、移行の緊急度に応じて、アプリ所有者がそれぞれのアプリを移行するために関与する必要のある **移行スケジュール** を提出することも検討してください。 このプロセスの最後には、移行のために優先度が付けられたバケットのすべてのアプリケーションの一覧が用意できているはずです。

### <a name="document-your-apps"></a>アプリをドキュメント化する

まず、アプリケーションについての重要情報を収集します。 [アプリケーション検出ワークシート](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx)を使用すると、移行に関する決定を迅速に行うことができ、いつでもビジネス グループに対する推奨を受けることができます。

移行を決定するうえで重要な情報には、次のものがあります。

- **アプリ名** – ビジネスにおいて、このアプリは何と呼ばれていますか?

- **アプリの種類** – サードパーティの SaaS アプリですか? カスタムの基幹 Web アプリですか? API ですか?

- **ビジネス上の重要度** – その重要度は高いですか? 低いですか? それともその中間ですか?

- **ユーザー アクセス ボリューム** – このアプリにはすべてのユーザーまたはごく少数のユーザーがアクセスしますか?

- **予定されている有効期間** - このアプリの存続期間はどれくらいですか? 6 か月未満ですか? 2 年を超えますか?

- **現在の ID プロバイダー** - このアプリの主要な IdP は何ですか? あるいはローカル ストレージに依存していますか?

- **認証方法** – アプリはオープン標準を使用して認証されますか?

- **アプリ コードの更新を計画しているかどうか** - アプリは計画されている、またはアクティブな開発中ですか?

- **アプリをオンプレミスに保持する予定があるかどうか** - アプリをデータセンターに長期間保持しますか?

- **アプリが他のアプリまたは API に依存しているかどうか** – 現在、アプリから他のアプリまたは API を呼び出していますか?

- **アプリが Azure AD ギャラリーにあるかどうか** – 現在、アプリは既に [Azure AD ギャラリー](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)と統合されていますか?

後で役立つものの、すぐに移行を決定する必要はないその他のデータには、次のようなものがあります。

- **アプリの URL** – ユーザーはアプリにアクセスするためにどこに移動しますか?

- **アプリの説明** – アプリの機能についての簡単な説明は何ですか?

- **アプリの所有者** – 社内のアプリの主要な POC は誰ですか?

- **一般的なコメントまたは注意事項** – アプリまたはビジネスの所有権に関するその他の一般的な情報

アプリケーションを分類し、詳細を文書化したら、必ず、計画された移行戦略に対するビジネス所有者の同意を得てください。

### <a name="plan-a-pilot"></a>パイロットを計画する

パイロット用に選択したアプリでは、組織の主要な ID とセキュリティ要件を表す必要があります。また、アプリケーションの所有者からの明確な同意が必要です。 パイロットは通常、別個のテスト環境で実行されます。 デプロイの計画に関するページの「[パイロットのベスト プラクティス](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot)」を参照してください。

**外部のパートナーを忘れないでください。** 彼らが移行スケジュールとテストに参加していることをご確認ください。 最後に、問題が発生した場合に、ヘルプ デスクにアクセスする方法があることを確認します。

### <a name="plan-for-limitations"></a>制限事項について計画する

一部のアプリの移行は簡単ですが、サーバーまたはインスタンスが複数あることが原因で、時間がかかる可能性があるものもあります。 たとえば、カスタム サインイン ページが原因で SharePoint の移行には時間がかかることがあります。

多くの SaaS アプリ ベンダーは、SSO 接続の変更料を請求します。 彼らに確認し、これについて計画してください。

Azure AD には、注意する必要がある[サービスの制限と制約](../enterprise-users/directory-service-limits-restrictions.md)もあります。

### <a name="app-owner-sign-off"></a>アプリ所有者の承認

ビジネス クリティカルで一般的に使用されるアプリケーションでは、パイロット段階でアプリをテストするためにパイロット ユーザーのグループが必要になる場合があります。 運用前またはパイロット環境でアプリをテストしたら、そのアプリを移行する前に、アプリのビジネス所有者が確実にパフォーマンスについて承認し、すべてのユーザーが認証のために Azure AD を運用環境で使用するようにしてください。

### <a name="plan-the-security-posture"></a>セキュリティ体制を計画する

移行プロセスを開始する前に、企業の ID システム用に開発するセキュリティ体制を十分に検討してください。 これは、これらの重要な情報のセットを収集することに基づいています: **データにアクセスしている ID、デバイス、および場所。**

### <a name="identities-and-data"></a>ID とデータ

ほとんどの組織には、業界や組織内の職務によって異なる ID とデータ保護に関する特定の要件があります。 [条件付きアクセス ポリシー](../conditional-access/overview.md)および関連する機能の所定のセットを含む推奨事項については、「[ID とデバイスのアクセス構成](/microsoft-365/enterprise/microsoft-365-policies-configurations)」を参照してください。

この情報を使用して、Azure AD に統合されているすべてのサービスへのアクセスを保護できます。 これらの推奨事項は、Microsoft セキュリティ スコアと [Azure AD の ID スコア](../fundamentals/identity-secure-score.md)に準拠しています。 このスコアは、次のために役立ちます。

- ID セキュリティ体制を客観的に測定する

- ID セキュリティの強化を計画する

- 強化の成功を確認する

これは、[ID インフラストラクチャをセキュリティで保護するために 5 つの手順](../../security/fundamentals/steps-secure-identity.md)を実装する場合にも役立ちます。 組織の出発点としてガイダンスを使用し、組織固有の要件を満たすようにポリシーを調整します。

### <a name="who-is-accessing-your-data"></a>誰がデータにアクセスしているか

Azure AD でサポートされているアプリとリソースのユーザーには、次の 2 つの主なカテゴリがあります。

- **内部:** ID プロバイダー内にアカウントを持つ従業員、請負業者、およびベンダー。 これには、マネージャーまたはリーダーと他の従業員では規則が異なるピボットがさらに必要になる可能性があります。

- **外部:** [Azure AD B2B コラボレーション](../external-identities/what-is-b2b.md)を使用して通常のビジネスで組織とやりとりするベンダー、供給元、販売代理店、またはその他のビジネス パートナー。

これらのユーザーのグループを定義し、さまざまな方法でこれらのグループを設定することができます。 管理者がグループにメンバーを手動で追加する必要があるようにするか、あるいは自分でセルフサービス グループのメンバーシップを有効にできるようにするかを選択できます。 [動的グループ](../enterprise-users/groups-dynamic-membership.md)を使用して、指定された条件に基づいてメンバーをグループに自動的に追加する規則を確立できます。

外部ユーザーが顧客を参照する場合もあります。 [Azure AD B2C](../../active-directory-b2c/overview.md) という別の製品でカスタマー認証がサポートされています。 しかし、本書ではこれについて説明しません。

### <a name="devicelocation-used-to-access-data"></a>データへのアクセスに使用されるデバイスまたは場所

ユーザーがアプリへのアクセスに使用するデバイスと場所も重要です。 企業ネットワークに物理的に接続されているデバイスのほうが安全です。 ネットワークの外部からの VPN 経由の接続には、調査が必要になる場合があります。

![ユーザーの場所とデータ アクセスの関係を示す図](media/migrating-application-authentication-to-azure-active-directory-6.jpg)

リソース、ユーザー、およびデバイスのこれらの側面を考慮して、[Azure AD 条件付きアクセス](../conditional-access/overview.md)機能を使用することを選択できます。 条件付きアクセスはユーザーのアクセス許可を超えています。これは、ユーザーまたはグループの ID、ユーザーが接続されているネットワーク、使用しているデバイスとアプリケーション、およびアクセスしようとしているデータの種類などの要素の組み合わせに基づいています。 ユーザーに付与されたアクセス権は、このより広範な条件セットに適応します。

### <a name="exit-criteria"></a>終了基準

次のような場合、このフェーズは成功です。

- アプリを把握している
  - 移行する予定のアプリを完全に文書化した
  - ビジネス上の重要度、使用量、および有効期間に基づいて、アプリに優先度を付けた

- パイロットの要件を表すアプリを選択した

- 優先度付けと戦略について、ビジネス所有者の同意を得ている

- セキュリティ体制のニーズとその実装方法を理解している

## <a name="phase-3-plan-migration-and-testing"></a>フェーズ 3: 移行とテストを計画する

ビジネス上の同意を得たら、次の手順として、Azure AD 認証へのこれらのアプリの移行を開始します。

### <a name="migration-tools-and-guidance"></a>移行ツールとガイダンス

以下のツールとガイダンスを使用して、Azure AD にアプリケーションを移行するために必要となる正確な手順に従います。

- **一般的な移行ガイダンス** – [Azure AD アプリの移行ツールキット](./migration-resources.md)のホワイトペーパー、ツール、電子メール テンプレート、およびアプリケーションのアンケートを使用して、アプリの検出、分類、および移行を行います。

- **SaaS アプリケーション** – [数百の SaaS アプリ チュートリアル](../saas-apps/tutorial-list.md)の一覧と、完全な [Azure AD SSO デプロイ計画](https://aka.ms/ssodeploymentplan)を参照し、エンドツーエンド プロセスをご確認ください。

- **オンプレミスで実行するアプリケーション** – [Azure AD アプリケーション プロキシに関する](./application-proxy.md)詳細を確認し、完全な [Azure AD アプリケーション プロキシのデプロイ計画](https://aka.ms/AppProxyDPDownload)を使用して、すぐに作業を開始します。

- **開発中のアプリ** – 詳細な[統合](../develop/quickstart-register-app.md)と[登録](../develop/quickstart-register-app.md)のガイダンスを参照してください。

移行後に、ユーザーにデプロイが成功したことを知らせ、行う必要がある新しい手順について通知することができます。

### <a name="plan-testing"></a>テストを計画する

移行プロセス時に、アプリには通常のデプロイ中に使用されるテスト環境が既に存在する場合があります。 移行のテストにこの環境を引き続き使用することができます。 テスト環境を現在使用できない場合は、アプリケーションのアーキテクチャに応じて、Azure App Service または Azure Virtual Machines を使用して設定できることがあります。 アプリの構成を開発するときに使用するテスト Azure AD テナントを別に設定することもできます。 このテナントはクリーンな状態で開始され、どのシステムとも同期するように構成されません。

テスト ユーザーでログインすることで各アプリをテストし、すべての機能が移行前と同じであることを確認できます。 テスト中に、ユーザーが [MFA](/azure/active-directory/authentication/howto-mfa-userstates) または [SSPR](../authentication/tutorial-enable-sspr.md) の設定を更新する必要があることがわかった場合、または移行中にこの機能を追加する場合は、必ず、エンド ユーザーのコミュニケーション計画にそれを追加してください。 [MFA](https://aka.ms/mfatemplates) と [SSPR](https://aka.ms/ssprtemplates) のエンド ユーザー通信テンプレートを参照してください。

アプリを移行したら、[Azure portal](https://aad.portal.azure.com/) に移動して、移行が成功したかどうかをテストします。 次の手順に従ってください。

- **[エンタープライズ アプリケーション] &gt; [すべてのアプリケーション]** の順に選択し、一覧から対象のアプリを見つけます。

- **[管理] &gt; [ユーザーとグループ]** の順に選択し、アプリに少なくとも 1 人のユーザーまたは 1 つのグループを割り当てます。

- **[管理] &gt; [条件付きアクセス]** の順に選択します。 ポリシーの一覧を調べて、[条件付きアクセス ポリシー](../conditional-access/overview.md)によってアプリケーションへのアクセスがブロックされないことを確認します。

アプリの構成方法に応じて、SSO が正常に機能することを確認します。

| 認証の種類      | テスト                                             |
| ------------------------ | --------------------------------------------------- |
| **OAuth/OpenID Connect** | **[エンタープライズ アプリケーション] &gt; [アクセス許可]** の順に選択し、アプリのユーザー設定において組織内で使用されるアプリケーションに確実に同意しているようにします。 |
| **SAML ベースの SSO** | **[シングル サインオン]** の下にある [[SAML 設定のテスト]](./debug-saml-sso-issues.md) ボタンを使用します。 |
| **パスワードベースの SSO** | [マイ アプリによるセキュリティで保護されたサインイン拡張機能](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)をダウンロードしてインストールします。 この拡張機能は、SSO プロセスを使用する必要がある組織の任意のクラウド アプリを開始する場合に役立ちます。 |

|  **[アプリケーション プロキシ](./application-proxy.md)** | コネクタが実行されていて、アプリケーションに割り当てられていることを確認します。 詳細については、[アプリケーション プロキシのトラブルシューティング ガイド](./application-proxy-troubleshoot.md)に関する記事をご覧ください。 |

### <a name="troubleshoot"></a>トラブルシューティング

問題が発生した場合は、[アプリのトラブルシューティング ガイド](../app-provisioning/isv-automatic-provisioning-multi-tenant-apps.md)のヘルプを参照してください。 トラブルシューティングの記事を確認することもできます。「[SAML ベースのシングル サインオンで構成されたアプリへのサインインに関する問題](/troubleshoot/azure/active-directory/troubleshoot-sign-in-saml-based-apps)」を参照してください。

### <a name="plan-rollback"></a>ロールバックを計画する

移行に失敗した場合は、ロールバックとテストを行うことをお勧めします。 移行に関する問題を軽減するために行うことができる手順を以下に示します。

- アプリの既存の構成の **スクリーンショットを取得** します。 もう一度アプリを再構成する必要があるかどうかを確認できます。

- クラウド認証に関する問題が発生した場合に、**レガシ認証へのリンクを提供する** ことも検討できます。

- 移行を完了する前に、以前の ID プロバイダーで **既存の構成を変更しないでください**。

- まず、**複数の IdP をサポートするアプリ** を移行します。 問題が発生した場合は、いつでも優先される IdP の構成に変更できます。

- アプリのエクスペリエンスに、 **[フィードバック] ボタン** または問題が発生したときの **ヘルプ デスク** へのポインターがあることを確認します。

### <a name="exit-criteria"></a>終了基準

次のような場合、このフェーズは成功です。

- 各アプリの移行方法を決定した

- 移行ツールを確認した

- テスト環境とグループを含む、テストを計画した

- ロールバックを計画した

## <a name="phase-4-plan-management-and-insights"></a>フェーズ 4: 管理と分析情報について計画する

アプリが移行されたら、確実に次のようにする必要があります。

- ユーザーが安全にアクセスして管理できる

- 使用状況とアプリの正常性に関する適切な分析情報を得ることができる

組織に応じて、次の操作を行うことをお勧めします。

### <a name="manage-your-users-app-access"></a>ユーザーのアプリへのアクセスを管理する

アプリを移行したら、さまざまな方法でユーザーのエクスペリエンスを向上させることができます

**アプリを検出できるようにする**

[MyApps](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) ポータル エクスペリエンスを **ユーザーに示します**。 ここで、すべてのクラウドベースのアプリ、[Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) を使用して利用できるアプリ、およびこれらのアプリにアクセスする権限がある場合は[アプリケーション プロキシ](./application-proxy.md)を使用しているアプリにアクセスできます。


次のようにして、ユーザーにアプリを検出する方法を説明できます。

- [既存のシングル サインオン](./view-applications-portal.md)機能を使用して、**ユーザーを任意のアプリにリンクする**


- アプリに対して [アプリケーションのセルフサービス アクセス](./manage-self-service-access.md)を有効にし、**キュレーションしたアプリをユーザーが追加できるようにする**

- [エンドユーザーに対してアプリケーションを非表示にし](./hide-application-from-user-portal.md) (既定の Microsoft アプリまたはその他のアプリ)、**必要なアプリを見つけやすくする**

### <a name="make-apps-accessible"></a>アプリにアクセスできるようにする

**ユーザーが自分のモバイル デバイスからアプリにアクセスできるようにします**。 ユーザーは、[iOS](./hide-application-from-user-portal.md) 7.0 以降または [Android](./hide-application-from-user-portal.md) デバイス上で Intune で管理されたブラウザーを使用して、MyApps ポータルにアクセスできます。

ユーザーは次のようにして、**Intune で管理されているブラウザー** をダウンロードできます。

- **Android デバイスの場合は**、[Google Play ストア](https://play.google.com/store/apps/details?id=com.microsoft.intune)から

- **Apple デバイスの場合は**、[Apple App Store から](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8)。または [iOS 用のマイ アプリ モバイル アプリ](https://apps.apple.com/us/app/my-apps-azure-active-directory/id824048653)をダウンロードできます

**ユーザーがブラウザー拡張機能からアプリを開けるようにします。**

ユーザーは、[Chrome](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl)、[FireFox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)、または [Microsoft Edge](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) で[マイ アプリによるセキュリティで保護されたサインイン拡張機能をダウンロード](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab)でき、ブラウザー バーから直接アプリを起動して、次のことを行うことができます。

- **アプリを検索して、最近使用したアプリを表示する**

- 適切な外部 URL に、[アプリケーション プロキシ](./application-proxy.md)で構成した **内部 URL を自動的に変換** する。 これで、ユーザーはどこにいるかに関係なく、使い慣れたリンクを使用できるようになります。

**ユーザーが Office.com からアプリを開けるようにします。**

ユーザーは [Office.com](https://www.office.com/) に移動して **対象のアプリを検索し、最近使用したアプリを表示する** ことができます。これは、作業場から直接行うことができます。

### <a name="secure-app-access"></a>アプリへのアクセスをセキュリティで保護する

Azure AD によって、移行されたアプリを管理するための一元的なアクセスの場所が提供されます。 [Azure portal](https://portal.azure.com/) に移動して、次の機能を有効にします。

- **アプリへのユーザー アクセスをセキュリティで保護する。** デバイスの状態や場所などに基づいてアプリケーションへのユーザー アクセスをセキュリティで保護するには、[条件付きアクセスポリシー](../conditional-access/overview.md)または [Identity Protection](../identity-protection/overview-identity-protection.md) を有効にします。

- **自動プロビジョニング。** ユーザーがアクセスする必要があるさまざまなサードパーティ製の SaaS アプリを使用して、[ユーザーの自動プロビジョニング](../app-provisioning/user-provisioning.md)を設定します。 これには、ユーザー ID の作成に加えて、状態または役割が変化したときのユーザー ID のメンテナンスおよび削除が含まれます。

- **ユーザー アクセス** **管理を委任する**。 必要に応じて、ご利用のアプリに対してアプリケーションのセルフサービス アクセスを有効にし、"*それらのアプリへのアクセスを承認するビジネス承認者を割り当てます*"。 アプリのコレクションに割り当てられたグループには、[セルフサービス グループ管理](../enterprise-users/groups-self-service-management.md)を使用します。

- **管理者アクセスを委任する。** **ディレクトリ ロール** を使用して、管理者ロール (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション開発者など) をユーザーに割り当てます。

### <a name="audit-and-gain-insights-of-your-apps"></a>アプリを監査し、分析情報を得る

[Azure portal](https://portal.azure.com/) を使用して、一元化された場所からすべてのアプリを監査することもできます。

- **[エンタープライズ アプリケーション] の [監査] を使用して **アプリを監査** したり、[Azure AD Reporting API](../reports-monitoring/concept-reporting-api.md) から同じ情報にアクセスして、お気に入りのツールに統合したりします。

- OAuth または OpenID Connect を使用するアプリの場合は、 **[エンタープライズ アプリケーション] の [アクセス許可]** を使用して、**アプリのアクセス許可を表示します**。

- **[エンタープライズ アプリケーション] の [サインイン]** を使用して、**サインインの分析情報を取得します**。[Azure AD Reporting API](../reports-monitoring/concept-reporting-api.md) から同じ情報にアクセスします。

- [Azure AD Power BI コンテンツ パック](../reports-monitoring/howto-use-azure-monitor-workbooks.md)から **アプリの使用状況を視覚化する**

### <a name="exit-criteria"></a>終了基準

次のような場合、このフェーズは成功です。

- ユーザーにセキュリティで保護されたアプリへのアクセスを提供している

- 移行されたアプリを監査して分析情報を得るために管理している

### <a name="do-even-more-with-deployment-plans"></a>デプロイ計画でさらに多くのことを行う

デプロイ計画では、アプリの移行シナリオを含む、Azure AD ソリューションのビジネス価値、計画、実装手順、および管理について説明します。 デプロイおよび Azure AD 機能から価値を得る作業を開始するのに必要なすべてをまとめます。 デプロイ ガイドには、Microsoft が推奨するベスト プラクティス、エンドユーザーのコミュニケーション、計画ガイド、実装手順、テスト ケースなどの内容が含まれています。

使用できる[デプロイ計画](../fundamentals/active-directory-deployment-plans.md)は多数ありますが、常にさらに多くのもの策定しています。

### <a name="contact-support"></a>サポートにお問い合せください

サポート チケットを作成または追跡したり、正常性を監視したりする場合は、次のサポート リンクを参照してください。

- **Azure サポート:** [Microsoft サポート](https://azure.microsoft.com/support)にお問い合わせいただき、任意の Azure のチケットを開くことができます

Microsoft との Enterprise Agreement に応じて、デプロイの問題を特定します。

- **FastTrack**: Enterprise Mobility and Security (EMS) または Azure AD Premium ライセンスを購入した場合は、[FastTrack プログラム](/enterprise-mobility-security/solutions/enterprise-mobility-fasttrack-program)からデプロイのサポートを受ける資格があります。

- **製品エンジニアリング チームに参加する:** 数百万ものユーザーが存在する大規模な顧客デプロイで作業している場合は、Microsoft アカウント チームまたはクラウド ソリューション アーキテクトからサポートを受ける権利があります。 プロジェクトのデプロイの複雑さに基づいて、[Azure ID 製品エンジニアリング チーム](https://aad.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/solutionProviders)と直接連携することができます。

- **Azure AD ID ブログ:** ID エンジニアリング チームによって直接提供されるすべての最新の製品発表、詳細、およびロードマップ情報を常に把握するために、[Azure AD ID ブログ](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/bg-p/Identity)にサブスクライブします。
