---
title: 回復性があるアクセス制御管理戦略を作成する - Azure Active Directory
description: このドキュメントでは、予期されていない中断の間のロックアウトのリスクを軽減する回復性を提供するために、組織で採用する必要がある戦略に関するガイダンスを示します
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 12/19/2018
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 831ba47ea4e999219a6d8cf34cb5fb0fdcd1ead8
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594953"
---
# <a name="create-a-resilient-access-control-management-strategy-with-azure-active-directory"></a>Azure Active Directory で回復性があるアクセス制御管理戦略を作成する

>[!NOTE]
> このドキュメントに含まれる情報は、取り上げている問題についての、公開時点における Microsoft Corporation の見解を表します。 Microsoft は変化する市場状況に対応する必要があるため、これを Microsoft による確約と解釈しないでください。Microsoft は、記載されている情報の公開後の正確さを保証できません。

多要素認証 (MFA) や 1 つのネットワークの場所などの単一のアクセス制御に依存して IT システムをセキュリティ保護している組織では、その単一のアクセス制御が使用不能になったり誤って構成されたりした場合に、アプリやリソースへのアクセス障害の影響を受けやすくなります。 たとえば、自然災害によって、広範囲の通信インフラストラクチャや企業ネットワークを使用できなくなる可能性があります。 このようなが中断のため、エンド ユーザーや管理者がサインインできなくなることがあります。

このドキュメントでは、次のようなシナリオで、予期されていない中断の間のロックアウトのリスクを軽減する回復性を提供するために、組織で採用する必要がある戦略に関するガイダンスを示します。

 1. 組織では、リスク軽減戦略またはコンティンジェンシー計画を実装することで、**中断の前に**ロックアウトのリスクを軽減する回復性を向上させることができます。
 2. 組織では、リスク軽減戦略とコンティンジェンシー計画を導入することで、**中断の間に**選択したアプリとリソースへのアクセスを続けることができます。
 3. 組織では、**中断の後**、実装されているコンティンジェンシーをロールバックする前に、ログなどの情報が保持されるようにする必要があります。
 4. 防止戦略や代替計画を実装していない組織でも、中断に対処する**緊急オプション**を実装できる場合があります。

## <a name="key-guidance"></a>重要なガイダンス

このドキュメントで重要なことは次の 4 つです。

* 緊急アクセス用アカウントを使用して、管理者のロックアウトを回避する。
* ユーザーごとの MFA ではなく条件付きアクセス (CA) を使用して、MFA を実装する。
* 複数の条件付きアクセス (CA) 制御を使用して、ユーザーのロックアウトを軽減する。
* 複数の認証方法または同等の手段をユーザーごとにプロビジョニングして、ユーザーのロックアウトを軽減する。

## <a name="before-a-disruption"></a>中断する前

発生する可能性があるアクセス制御の問題への対応において、組織が主眼にする必要があるのは、実際の中断を軽減することです。 軽減策には、実際のイベントに対する計画に加えて、アクセス制御と運用が中断の間に影響を受けないようにする戦略の実装が含まれます。

### <a name="why-do-you-need-resilient-access-control"></a>回復性があるアクセス制御が必要な理由

 ID は、アプリとリソースにアクセスするユーザーのコントロール プレーンです。 ID システムでは、どのユーザーが、どのような条件の下で (アクセス制御や認証の要件など)、アプリケーションにアクセスできるかが制御されます。 不測の事態のため、ユーザーの認証に対して 1 つ以上の認証要件またはアクセス制御要件を使用できないと、次の問題の一方または両方が組織に発生する可能性があります。

* **管理者のロックアウト:** 管理者は、テナントまたはサービスを管理できません。
* **ユーザーのロックアウト:** ユーザーは、アプリまたはリソースにアクセスできません。

### <a name="administrator-lockout-contingency"></a>管理者ロックアウトのコンティンジェンシー

管理者がテナントにアクセスできるようにするには、緊急アクセス用アカウントを作成する必要があります。 緊急アクセス用アカウント ("*非常時*" アカウントとも呼ばれます) を使用すると、通常の特権アカウントのアクセス手順を使用できないときに、Azure AD の構成にアクセスして管理できます。 [緊急アクセス用アカウントの推奨事項]( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)に従って、少なくとも 2 つの緊急アクセス用アカウントを作成する必要があります。

### <a name="mitigating-user-lockout"></a>ユーザー ロックアウトの軽減

 ユーザー ロックアウトのリスクを軽減するには、アプリやリソースへのアクセス方法をユーザーが選択できる複数の制御を備えた条件付きアクセス ポリシーを使用します。 たとえば、MFA でのサインイン、**または**、マネージド デバイスからのサインイン、**または**、企業ネットワークからのサインインをユーザーが選択できるようにすることで、いずれかのアクセス制御を利用できない場合でも、ユーザーには作業を続けるための他のオプションがあります。

#### <a name="microsoft-recommendations"></a>Microsoft の推奨事項

組織の既存の条件付きアクセス ポリシーに次のアクセス制御を組み込みます。

1. さまざまな通信チャネルに依存するユーザーごとに、複数の認証方法をプロビジョニングします (たとえば、Microsoft Authenticator アプリ (インターネット ベース)、OATH トークン (デバイス上で生成)、SMS (電話))。
2. Windows 10 デバイスに Windows Hello for Business を展開し、デバイスのサインインから直接 MFA 要件を満たします。
3. [Azure AD Hybrid Join](https://docs.microsoft.com/azure/active-directory/devices/overview) または [Microsoft Intune マネージド デバイス](https://docs.microsoft.com/intune/planning-guide)によって、信頼済みデバイスを使用します。 信頼済みデバイスを使用すると、信頼済みデバイス自体でポリシーの強力な認証要件を満たすことができ、ユーザーに MFA チャレンジを行う必要がないので、ユーザー エクスペリエンスが向上します。 その場合、新しいデバイスを登録するとき、および信頼されていないデバイスからアプリやリソースにアクセスするときに、MFA が必要になります。
4. 固定の MFA ポリシーの代わりに、ユーザーまたはサインインにリスクがあるときにアクセスを禁止する Azure AD Identity Protection のリスクに基づくポリシーを使用します。

>[!NOTE]
> リスクに基づくポリシーを使用するには、[Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) ライセンスが必要です。

次の例では、アプリやリソースにアクセスするユーザーに対して回復性があるアクセス制御を提供するために作成する必要があるポリシーについて説明します。 この例では、アクセス提供対象のユーザーを含むセキュリティ グループ **AppUsers**、コア管理者を含むグループ **CoreAdmins**、緊急アクセス用アカウントを含むグループ **EmergencyAccess** が必要です。
この例のポリシー セットでは、**AppUsers** で選択されているユーザーが、信頼済みデバイスから接続している場合、または MFA などの強力な認証を提供した場合に、選択されたアプリへのアクセス権をユーザーに付与します。 緊急アカウントとコア管理者は除外されます。

**CA 軽減ポリシー セット:**

* ポリシー 1:ターゲット グループ外のユーザーにアクセスをブロックする
  * ユーザーとグループ:すべてのユーザーを含める。 AppUsers、CoreAdmins、EmergencyAccess を除外する
  * クラウド アプリ:すべてのアプリを含める
  * 条件:(なし)
  * 許可の制御:ブロック
* ポリシー 2:MFA または信頼済みデバイスを要求して、AppUsers にアクセスを許可する。
  * ユーザーとグループ:AppUsers を含める。 CoreAdmins、EmergencyAccess を除外する
  * クラウド アプリ:すべてのアプリを含める
  * 条件:(なし)
  * 許可の制御:アクセスを許可する、多要素認証が必要、デバイスの準拠が必要。 複数の制御の場合:選択した制御のいずれかが必要。

### <a name="contingencies-for-user-lockout"></a>ユーザー ロックアウトのコンティンジェンシー

または、組織でコンティンジェンシー ポリシーを作成することもできます。 コンティンジェンシー ポリシーを作成するには、ビジネス継続性、運用コスト、財務コスト、セキュリティ リスクの間のトレードオフ条件を定義する必要があります。 たとえば、ユーザーのサブセット、アプリのサブセット、クライアントのサブセット、または場所のサブセットに対してのみ、コンティンジェンシー ポリシーをアクティブにする場合があります。 コンティンジェンシー ポリシーでは、軽減策が実装されていないときの中断中に、管理者とエンド ユーザーに対して、アプリとリソースへのアクセス権が付与されます。
中断中の危険な状態を理解することは、リスクを軽減するのに役立ち、計画プロセスの重要な一部です。 コンティンジェンシー計画を作成するには、まず、組織での次のビジネス要件を決定します。

1. 事前にミッション クリティカルなアプリを決定します。低いリスク/セキュリティ体制であっても、アクセス権を付与する必要があるアプリは何ですか。 そのようなアプリの一覧を作成し、すべてのアクセス制御が失われた場合であってもそれらのアプリの実行を継続する必要があることを、他の利害関係者 (ビジネス、セキュリティ、法律、リーダーシップ) が全員同意していることを確認します。 最終的に以下のカテゴリになる可能性があります。
   * **カテゴリ 1: ミッション クリティカルなアプリ**: 数分より長く使用不可能になることはできません。たとえば、組織の収益に直接影響するアプリなどです。
   * **カテゴリ 2: 重要なアプリ**: 数時間以内にアクセス可能になる必要があります。
   * **カテゴリ 3: 優先順位の低いアプリ**: 数日間停止しても耐えられます。
2. カテゴリ 1 と 2 のアプリについては、許可するアクセス レベルの種類を事前に計画することをお勧めします。
   * フル アクセスまたは制限されたセッション (ダウンロードの制限など) を許可しますか。
   * アプリ全体へのアクセスは許可せず、アプリの一部へのアクセスを許可しますか。
   * アクセス制御が復元されるまで、情報ワーカーのアクセスを許可し、管理者のアクセスをブロックしますか。
3. このようなアプリについては、意図的に開くアクセス手段と閉じるアクセス手段を計画することもお勧めします。
   * ブラウザーのみのアクセスは許可し、オフライン データを保存できるリッチ クライアントはブロックしますか。
   * 企業ネットワーク内のユーザーに対してのみアクセスを許可し、外部のユーザーはブロックしますか。
   * 中断中は、特定の国または地域からのアクセスのみを許可しますか。
   * 代わりのアクセス制御を使用できない場合に、コンティンジェンシー ポリシーに対するポリシーを失敗または成功させますか (特にミッション クリティカルなアプの場合)。

#### <a name="microsoft-recommendations"></a>Microsoft の推奨事項

コンティンジェンシー条件付きアクセス ポリシーは、Azure MFA、サード パーティの MFA、リスクに基づく制御、またはデバイスに基づく制御を省略する**無効化されたポリシー**です。 その場合、組織でコンティンジェンシー計画の有効化が決定されると、管理者はそのポリシーを有効にして、通常の制御に基づくポリシーを無効にできます。

>[!IMPORTANT]
> ユーザーにセキュリティを強制するポリシーを無効にすると、一時的であっても、コンティンジェンシー計画が実施されている間は、セキュリティ体制が低下します。

* 1 つの資格情報の種類または 1 つのアクセス制御メカニズムの中断によってアプリへのアクセスが影響を受ける場合は、フォールバック ポリシーのセットを構成します。 サード パーティの MFA プロバイダーを必要とするアクティブなポリシーに対するバックアップとして、制御としてのドメイン参加が必要なポリシーを無効化された状態で構成します。
* [パスワードのガイダンス](https://aka.ms/passwordguidance)に関するホワイト ペーパーでの方法に従って、MFA が必要ないときに、パスワードを推測する悪意のあるユーザーのリスクを軽減します。
* [Azure AD のセルフサービスのパスワード リセット (SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) および [Azure AD のパスワード保護](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy)を展開し、ユーザーが禁止されているありふれたパスワードや条件を使用しないようにします。
* 単にフル アクセスにフォールバックするのではなく、特定の認証レベルが満たされていない場合はアプリ内でアクセスを制限するポリシーを使用します。 例:
  * Exchange および SharePoint に制限されたセッション要求を送信するバックアップ ポリシーを構成します。
  * 組織で Microsoft Cloud App Security が使用されている場合は、フォールバックするポリシーで、MCAS を適用し、MCAS によって読み取り専用アクセスを許可してアップロードを許可しないことを検討します。
* 中断中にポリシーを簡単に見つけられるよう、ポリシーに名前を付けます。 ポリシー名には次の要素を含めます。
  * ポリシーの*ラベル番号*。
  * 表示するテキスト。このポリシーは緊急時のみを対象としています。 例: **ENABLE IN EMERGENCY**
  * 適用される*中断*。 例: **During MFA Disruption**
  * ポリシーをアクティブ化する順序を示す、*シーケンス番号*。
  * 適用される*アプリ*。
  * 適用される*コントロール*。
  * 必要な*条件*。
  
コンティンジェンシー ポリシーの場合、この名前付け基準は次のようになります。 

```
EMnnn - ENABLE IN EMERGENCY: [Disruption][i/n] - [Apps] - [Controls] [Conditions]
```

次の例をご覧ください。**例 A - ミッション クリティカルなコラボレーション アプリへのアクセスを復元するコンティンジェンシー CA ポリシー**は、企業の一般的なコンティンジェンシーです。 このシナリオの組織では、一般に、すべての Exchange Online と SharePoint Online に対して MFA が必要であり、このケースでの中断は顧客に対する MFA プロバイダーの停止です (Azure MFA、オンプレミス MFA プロバイダー、サード パーティ MFA にかかわらず)。 このポリシーでは、信頼された Windows デバイスからアプリに対する特定の対象ユーザーのアクセスを、信頼された企業ネットワークからアプリにアクセスしている場合にのみ許可することによって、この停止を軽減します。 また、緊急アカウントとコア管理者はこれらの制限から除外されます。 これにより、対象ユーザーは Exchange Online と SharePoint Online へのアクセスが許可されますが、その他のユーザーは障害のためにアプリにまだアクセスできません。 この例では、名前付きのネットワークの場所 **CorpNetwork**、対象ユーザーを含むセキュリティ グループ **ContingencyAccess**、コア管理者を宇ｆくむグループ **CoreAdmins**、緊急アクセス用管アカウントを含むグループ **EmergencyAccess** が必要です。 コンティンジェンシーでは、必要なアクセスを提供するために 4 つのポリシーが必要です。 

**例 A - ミッション クリティカルなコラボレーション アプリへのアクセスを復元するコンティンジェンシー CA ポリシー:**

* ポリシー 1:Exchange と SharePoint に対するドメイン参加済みデバイスが必要
  * 名前:EM001 - ENABLE IN EMERGENCY:MFA Disruption[1/4] - Exchange SharePoint - Require Hybrid Azure AD Join
  * ユーザーとグループ:ContingencyAccess を含める。 CoreAdmins、EmergencyAccess を除外する
  * クラウド アプリ:Exchange Online と SharePoint Online
  * 条件:Any
  * 許可の制御:ドメインへの参加が必要
  * 状態:Disabled
* ポリシー 2:Windows 以外のプラットフォームをブロック
  * 名前:EM002 - ENABLE IN EMERGENCY:MFA Disruption[2/4] - Exchange SharePoint - Block access except Windows
  * ユーザーとグループ:すべてのユーザーを含める。 CoreAdmins、EmergencyAccess を除外する
  * クラウド アプリ:Exchange Online と SharePoint Online
  * 条件:デバイス プラットフォームは、Windows 以外のすべてのプラットフォームを含む
  * 許可の制御:ブロック
  * 状態:Disabled
* ポリシー 3:CorpNetwork 以外のネットワークをブロック
  * 名前:EM003 - ENABLE IN EMERGENCY:MFA Disruption[3/4] - Exchange SharePoint - Block access except Corporate Network
  * ユーザーとグループ:すべてのユーザーを含める。 CoreAdmins、EmergencyAccess を除外する
  * クラウド アプリ:Exchange Online と SharePoint Online
  * 条件:場所は、CorpNetwork 以外のすべての場所を含む
  * 許可の制御:ブロック
  * 状態:Disabled
* ポリシー 4:EAS を明示的にブロックする
  * 名前:EM004 - ENABLE IN EMERGENCY:MFA Disruption[4/4] - Exchange - Block EAS for all users
  * ユーザーとグループ:すべてのユーザーを含める
  * クラウド アプリ:Exchange Online を含める
  * 条件:クライアント アプリ:Exchange Active Sync
  * 許可の制御:ブロック
  * 状態:Disabled

アクティブ化の順序:

1. 既存の MFA ポリシーから ContingencyAccess、CoreAdmins、EmergencyAccess を除外します。 ContingencyAccess に含まれるユーザーが SharePoint Online と Exchange Online にアクセスできることを確認します。
2. ポリシー 1 を有効にします:除外グループに含まれないドメイン参加済みデバイスのユーザーが、Exchange Online と SharePoint Online にアクセスできることを確認します。 除外グループに含まれるユーザーが、任意のデバイスから SharePoint Online と Exchange にアクセスできることを確認します。
3. ポリシー 2 を有効にします:除外グループに含まれないユーザーが自分のモバイル デバイスから SharePoint Online と Exchange Online にアクセスできないことを確認します。 除外グループに含まれるユーザーが、任意のデバイス (Windows/iOS/Android) から SharePoint と Exchange にアクセスできることを確認します。
4. ポリシー 3 を有効にします:除外グループに含まれないユーザーが、 ドメイン参加済みコンピューターであっても、企業ネットワーク外から SharePoint および Exchange にアクセスできないことを確認します。 除外グループに含まれるユーザーが、任意のネットワークから SharePoint と Exchange にアクセスできることを確認します。
5. ポリシー 4 を有効にします:すべてのユーザーが、モバイル デバイス上のネイティブ メール アプリケーションから Exchange Online にアクセスできないことを確認します。
6. SharePoint Online と Exchange Online に対する既存の MFA ポリシーを無効にします。

次の例、**例 B - Salesforce へのモバイル アクセスを許可するコンティンジェンシー CA ポリシー**では、ビジネス アプリのアクセスを復元します。 このシナリオの顧客は、通常、営業社員に対し、準拠しているモバイル デバイスから (Azure AD でシングル サインオンを構成された) Salesforce へのアクセスのみを許可する必要があります。 このケースでの中断はデバイスのコンプライアンスの評価に関する問題であり、販売チームが取引成立のために Salesforce にアクセスする必要がある微妙なときに障害が発生します。 以下のコンティンジェンシー ポリシーでは、取引を続けることができてビジネスが中断しないように、モバイル デバイスから Salesforce へのクリティカルなユーザー アクセスが許可されます。 この例では、**SalesforceContingency** にアクセスを維持する必要があるすべての営業社員が含まれ、**SalesAdmins** に Salesforce の必要な管理者が含まれます。

**例 B - コンティンジェンシー CA ポリシー:**

* ポリシー 1:SalesContingency チームに含まれないすべてのユーザーをブロックする
  * 名前:EM001 - ENABLE IN EMERGENCY:Device Compliance Disruption[1/2] - Salesforce - Block All users except SalesforceContingency
  * ユーザーとグループ:すべてのユーザーを含める。 SalesAdmins と SalesforceContingency を除外する
  * クラウド アプリ:Salesforce。
  * 条件:なし
  * 許可の制御:ブロック
  * 状態:Disabled
* ポリシー 2:(攻撃対象領域を減らすため) モバイル以外の任意のプラットフォームからのセールス チームをブロックする
  * 名前:EM002 - ENABLE IN EMERGENCY:Device Compliance Disruption[2/2] - Salesforce - Block All platforms except iOS and Android
  * ユーザーとグループ:SalesforceContingency を含める。 SalesAdmins を除外する
  * クラウド アプリ:Salesforce
  * 条件:デバイス プラットフォームは、iOS と Android 以外のすべてのプラットフォームを含む
  * 許可の制御:ブロック
  * 状態:Disabled

アクティブ化の順序:

1. Salesforce に対する既存のデバイス コンプライアンス ポリシーから SalesAdmins と SalesforceContingency を除外します。 SalesforceContingency グループに含まれるユーザーが Salesforce にアクセスできることを確認します。
2. ポリシー 1 を有効にします:SalesContingency に含まれないユーザーが Salesforce にアクセスできないことを確認します。 SalesAdmins と SalesforceContingency に含まれるユーザーが Salesforce にアクセスできることを確認します。
3. ポリシー 2 を有効にします:SalesContingency グループに含まれるユーザーが、Windows/Mac のラップトップからは Salesforce にアクセスできないが、自分のモバイル デバイスからはアクセスできることを確認します。 SalesAdmin が任意のデバイスから Salesforce にアクセスできることを確認します。
4. Salesforce に対する既存のデバイス コンプライアンス ポリシーを無効にします。

### <a name="deploy-password-hash-sync-even-if-you-are-federated-or-use-pass-through-authentication"></a>フェデレーションされている場合またはパススルー認証を使用している場合でも、パスワード ハッシュ同期を展開する

ユーザーのロックアウトは、次の条件が満たされる場合にも発生します。

- 組織で、パススルー認証またはフェデレーションによるハイブリッド ID ソリューションが使用されている。
- オンプレミスの ID システム (Active Directory、AD FS、依存コンポーネントなど) を使用できない。 
 
回復性を向上させるには、組織で[パスワード ハッシュ同期を有効にする](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)必要があります。そうすれば、オンプレミスの ID システムがダウンした場合は、[パスワード ハッシュ同期の使用に切り替える](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-user-signin)ことができます。

#### <a name="microsoft-recommendations"></a>Microsoft の推奨事項
 組織でフェデレーションまたはパススルー認証が使用されているかどうかにかかわらず、Azure AD Connect ウィザードを使用してパスワード ハッシュ同期を有効にします。

>[!IMPORTANT]
> パスワード ハッシュ同期を使用するために、ユーザーをフェデレーション認証からマネージド認証に変換する必要はありません。

## <a name="during-a-disruption"></a>中断の間

軽減計画を実装することを選択した場合、単一のアクセス制御の中断を自動的に回避することができます。 一方、コンティンジェンシー計画を作成することを選択した場合は、アクセス制御の中断の間に、コンティンジェンシー ポリシーをアクティブ化することができます。

1. 対象ユーザーに対して特定ネットワークから特定アプリへのアクセスを許可するコンティンジェンシー ポリシーを有効にします。
2. 通常の制御に基づくポリシーを無効にします。

### <a name="microsoft-recommendations"></a>Microsoft の推奨事項

中断の間に軽減とコンティンジェンシーのどちらを使用するかによっては、組織でパスワードだけによるアクセスが許可される可能性があります。 保護がないことは、慎重に検討すべき重大なセキュリティ リスクです。 組織では次のようにする必要があります。

1. 変更管理戦略の一環として、アクセス制御が完全に動作するようになったら直ちに、実装したコンティンジェンシーをロールバックできるように、すべての変更と以前の状態を文書化します。
2. MFA を無効にしている間は、悪意のあるユーザーがパスワード スプレーやフィッシング攻撃を使用してパスワードを取得しようとするものと想定します。 また、悪意のあるユーザーは、以前はどのリソースにもアクセスできなかったパスワードを既に持っていて、この期間中にアクセスを試みるかもしれません。 管理職などの重要なユーザーについては、MFA を無効にする前に、それらのユーザーのパスワードをリセットすることで、このリスクを軽減することができます。
3. すべてのサインイン アクティビティをアーカイブし、MFA が無効にされていた期間に誰がアクセスしたかを識別します。
4. この期間中に[報告されたすべてのリスク イベントをトリアージ](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins)します。

## <a name="after-a-disruption"></a>中断の後

中断の原因となったサービスが復元された後、アクティブ化されたコンティンジェンシー計画の一部として行われた変更を元に戻します。 

1. 通常のポリシーを有効にします
2. コンティンジェンシー ポリシーを無効にします。 
3. 中断中に行って文書化した他のすべての変更をロールバックします。
4. 緊急アクセス用アカウントを使用した場合は、緊急アクセス用アカウントの手順の一部として、忘れずに資格情報を再生成し、新しい資格情報の詳細を物理的にセキュリティ保護します。
5. 不審なアクティビティのため、中断後も引き続き[報告されたすべてのリスク イベントをトリアージ](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins)します。
6. [PowerShell を使用して](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0)一連のユーザーを対象に発行されたすべての更新トークンを取り消します。 すべての更新トークンの取り消しは中断中に使用された特権アカウントについて重要であり、そうすることで、強制的に再認証が行われ、復元されたポリシーの制御に対応します。

## <a name="emergency-options"></a>緊急時のオプション

 緊急事態が発生し、組織で以前に軽減策またはコンティンジェンシー計画を実施したことがない場合で、既に条件付きアクセス ポリシーを使用して MFA を強制しているときは、「[ユーザー ロックアウトのコンティンジェンシー](#contingencies-for-user-lockout)」セクションの推奨事項に従います。
組織でユーザーごとの MFA レガシ ポリシーを使用している場合は、次の代替手段を検討します。

1. 企業ネットワークに送信 IP アドレスがある場合は、それを信頼できる IP として追加し、企業ネットワークに対してのみ認証を有効にできます。
   1. 送信 IP アドレスのインベントリがない場合、または企業ネットワークの内部と外部でアクセスを有効にする必要があった場合は、0.0.0.0/1 と 128.0.0.0/1 を指定することにより、IPv4 アドレス空間全体を信頼できる IP アドレスとして追加できます。

>[!IMPORTANT]
 > アクセスのブロックを解除するために信頼できる IP アドレスの範囲を広げた場合、IP アドレスに関連するリスク イベント (たとえば、あり得ない移動や未知の場所) は生成されなくなります。

>[!NOTE]
 > Azure MFA に対する[信頼できる IP アドレス](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings)の構成は、[Azure AD Premium ライセンス](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-licensing)でのみ使用できます。

## <a name="learn-more"></a>詳細情報

* [Azure AD Authentication のドキュメント](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-iis)
* [Azure AD で緊急アクセス用管理者アカウントを管理する](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)
* [Azure Active Directory でネームド ロケーションを構成する](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)
  * [Set-MsolDomainFederationSettings](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)
* [ハイブリッド Azure Active Directory 参加済みデバイスの構成方法](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
* [Windows Hello for Business の展開ガイド](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide)
  * [パスワードのガイダンス - Microsoft Research](https://research.microsoft.com/pubs/265143/microsoft_password_guidance.pdf)
* [Azure Active Directory 条件付きアクセスの条件の概要](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions)
* [Azure Active Directory 条件付きアクセスによるアクセス制御の概要](https://docs.microsoft.com/azure/active-directory/conditional-access/controls)
