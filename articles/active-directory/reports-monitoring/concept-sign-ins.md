---
title: Azure Active Directory ポータルのサインイン アクティビティ レポート | Microsoft Docs
description: Azure Active Directory ポータルのサインイン アクティビティ レポートの概要
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/24/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d61962667953b20f4b542874e902411bb579b9c3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93122845"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory ポータルのサインイン アクティビティ レポート

Azure Active Directory (Azure AD) のレポート アーキテクチャは、次のコンポーネントで構成されます。

- **アクティビティ** 
    - **サインイン** – マネージド アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報。
    - **監査ログ** - [監査ログ](concept-audit-logs.md)は、ユーザーとグループの管理や、マネージド アプリケーションとディレクトリのアクティビティに関するシステム アクティビティ情報を提供します。
    - **プロビジョニング ログ** - [プロビジョニング ログ](./concept-provisioning-logs.md)によって、プロビジョニング サービスによるアクティビティの監視 (ServiceNow のグループや Workday からインポートされたユーザーの作成など) が可能になります。 
- **Security** 
    - **リスクの高いサインイン** - [リスクの高いサインイン](../identity-protection/overview-identity-protection.md)は、ユーザー アカウントの正当な所有者ではないユーザーによるサインイン試行の指標です。
    - **リスクのフラグ付きユーザー** - [リスクの高いユーザー](../identity-protection/overview-identity-protection.md)は、侵害された可能性があるユーザー アカウントの指標です。

この記事では、サインイン レポートの概要について説明します。

## <a name="prerequisites"></a>前提条件

### <a name="who-can-access-the-data"></a>誰がデータにアクセスできますか。

* セキュリティ管理者、セキュリティ閲覧者、グローバル閲覧者、およびレポート閲覧者ロールのユーザー
* グローバル管理者
* 任意のユーザー (非管理者) が自分のサインインにアクセス可能 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>サインイン アクティビティにアクセスするために必要な Azure AD ライセンスを教えてください。

サインイン アクティビティ レポートは、[Azure AD のすべてのエディション](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data)で使用でき、Microsoft Graph API を使用してアクセスすることもできます。

## <a name="sign-ins-report"></a>サインイン レポート

ユーザーのサインイン レポートは、次の質問に対する回答を提示します。

* ユーザーのサインインにどのようなパターンがあるか。
* 1 週間で何人のユーザーがサインインを行ったか。
* これらのサインインはどのような状態か。

[[Azure portal]](https://portal.azure.com) メニューで **[Azure Active Directory]** を選択するか、任意のページから **[Azure Active Directory]** を検索して選択します。

![[Azure Active Directory] を選択する](./media/concept-sign-ins/select-azure-active-directory.png "Azure Active Directory")

**[監視]** で **[サインイン]** を選択して、[サインイン レポート](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)を開きます。

![スクリーンショットには、[監視] メニューから選択された [サインイン] が示されています。](./media/concept-sign-ins/monitoring-sign-ins-in-azure-active-directory.png "サインイン アクティビティ")

一部のサインイン レコードがポータルに表示されるまでに、最大 2 時間かかることがあります。

> [!IMPORTANT]
> サインイン レポートには、**対話型** のサインイン、つまりユーザーがユーザー名とパスワードを使用して手動で行うサインインのみが表示されます。 サービス間の認証のような対話型ではないサインインは、サインイン レポートに表示されません。 

サインイン ログには、次のものを示す既定のリスト ビューがあります。

- サインインの日付
- 関連するユーザー
- ユーザーがサインインしたアプリケーション
- サインインの状態
- リスク検出の状態
- 多要素認証 (MFA) の要件の状態

![スクリーンショットには、Office 365 SharePoint Online のサインインが示されています。](./media/concept-sign-ins/sign-in-activity.png "サインイン アクティビティ")

リスト ビューをカスタマイズするには、ツール バーの **[列]** をクリックします。

![スクリーンショットには、[サインイン] ページの [列] オプションが示されています。](./media/concept-sign-ins/19.png "サインイン アクティビティ")

**[列]** ダイアログでは、選択可能な属性にアクセスできます。 サインイン レポートでは、特定のサインイン要求に対して複数の値を持つフィールドを列として使用することはできません。 これは、認証の詳細、条件付きアクセス データ、ネットワークの場所などが該当します。   

![スクリーンショットには、属性を選択できる [列] ダイアログ ボックスが示されています。](./media/concept-sign-ins/columns.png "サインイン アクティビティ")

詳細な情報を取得するには、リスト ビューで項目を選択します。

![スクリーンショットには、詳細情報ビューが示されています。](./media/concept-sign-ins/basic-sign-in.png "サインイン アクティビティ")

> [!NOTE]
> すべてのサインイン レポートで、条件付きアクセス ポリシーのトラブルシューティングを実行できるようになりました。 サインイン レコードの **[条件付きアクセス]** タブをクリックして、条件付きアクセスの状態を確認し、サインインに適用されたポリシーの詳細と各ポリシーの結果を調べることができます。
> 詳細については、「[Frequently asked questions about CA information in all sign-ins](reports-faq.md#conditional-access)」 (すべてのサインインの CA 情報に関してよく寄せられる質問) を参照してください。



## <a name="filter-sign-in-activities"></a>サインイン アクティビティのフィルター処理

まず、報告されたデータを、自分に適したレベルまで絞り込みます。 次に、既定のフィルターとして [日付] フィールドを使用したサインイン データをフィルター処理します。 Azure AD では、さまざまな追加のフィルターを設定できます。

![スクリーンショットには、[フィルターの追加] オプションが示されています。](./media/concept-sign-ins/04.png "サインイン アクティビティ")

**要求 ID** - 確認したい要求の ID です。

**ユーザー** - 確認したいユーザーの名前またはそのユーザー プリンシパル名 (UPN) です。

**アプリケーション** - ターゲット アプリケーションの名前です。
 
**状態** - 確認したいサインインの状態です。

- Success

- 障害

- 中断


**IP アドレス** - テナントへの接続に使用されたデバイスの IP アドレスです。

**場所** - 接続が開始された場所です。

- City

- 都道府県

- 国/リージョン


**リソース** - サインインに使用されたサービスの名前です。


**リソース ID** - サインインに使用されたサービスの ID です。


**クライアント アプリ** - テナントへの接続に使用されたクライアント アプリの種類です。

![クライアント アプリ フィルター](./media/concept-sign-ins/client-app-filter.png)


|名前|先進認証|説明|
|---|:-:|---|
|認証済み SMTP| |電子メール メッセージを送信するために POP および IMAP のクライアントで使用されます。|
|自動検出| |Exchange Online でメールボックスを検索して接続するために Outlook および EAS のクライアントで使用されます。|
|Exchange ActiveSync| |このフィルターは、EAS プロトコルが試行されたすべてのサインイン試行を表示します。|
|Browser|![青のチェックマーク。](./media/concept-sign-ins/check.png)|Web ブラウザーを使用したユーザーのすべてのサインイン試行を表示します|
|Exchange ActiveSync| | Exchange ActiveSync を使用して Exchange Online に接続するクライアント アプリでのユーザーのすべてのサインイン試行を表示します|
|Exchange Online PowerShell| |リモート PowerShell を使用して Exchange Online に接続するために使用されます。 Exchange Online PowerShell の基本認証をブロックする場合は、Exchange Online PowerShell モジュールを使用して接続する必要があります。 手順については、「[多要素認証を使用して Exchange Online PowerShell に接続する](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)」を参照してください。|
|Exchange Web サービス| |Outlook、Outlook for Mac、およびサードパーティ製アプリによって使用されるプログラミング インターフェイスです。|
|IMAP4| |IMAP を使用して電子メールを取得する従来のメール クライアント。|
|MAPI over HTTP| |Outlook 2010 以降で使用されます。|
|モバイル アプリとデスクトップ クライアント|![青のチェックマーク。](./media/concept-sign-ins/check.png)|モバイル アプリとデスクトップ クライアントを使用したユーザーのすべてのサインイン試行を表示します。|
|オフライン アドレス帳| |Outlook によってダウンロードおよび使用されるアドレス一覧コレクションのコピーです。|
|Outlook Anywhere (RPC over HTTP)| |Outlook 2016 以降で使用されます。|
|Outlook サービス| |Windows 10 用のメール/カレンダー アプリで使用されます。|
|POP3| |POP3 を使用して電子メールを取得する従来のメール クライアント。|
|レポート Web サービス| |Exchange Online でレポート データを取得するために使用されます。|
|その他のクライアント| |クライアント アプリが含まれていない、または不明であるユーザーのサインインの試行をすべて表示します。|



**オペレーティング システム** - テナントへのサインオンに使用されたデバイスで実行されているオペレーティング システムです。 


**デバイス ブラウザー** - ブラウザーから接続が開始された場合、このフィールドを使用するとブラウザー名でフィルター処理できます。


**関連付け ID** - アクティビティの関連付け ID です。




**条件付きアクセス** - 適用されている条件付きアクセス規則の状態です

- **適用されていません**:サインイン中にポリシーがユーザーとアプリケーションに適用されていません。

- **成功**:サインイン中に 1 つ以上の条件付きアクセス ポリシーがユーザーとアプリケーションに適用されました (ただし、必ずしも他の条件が適用されたとは限りません)。 

- **失敗**:サインインによって少なくとも 1 つの条件付きアクセス ポリシーのユーザーとアプリケーションの条件が満たされたうえで、制御の許可が満たされていないか、またはアクセスをブロックするように設定されています。









## <a name="download-sign-in-activities"></a>サインイン アクティビティのダウンロード

**[ダウンロード]** オプションをクリックして、最新の 250,000 件のレコードを含む CSV または JSON ファイルを作成します。 Azure portal の外部で操作する場合は、まず[サインイン データをダウンロード](quickstart-download-sign-in-report.md)します。  

![ダウンロード](./media/concept-sign-ins/71.png "ダウンロード")

> [!IMPORTANT]
> ダウンロードできるレコードの数は、[Azure Active Directory レポートの保持ポリシー](reference-reports-data-retention.md)によって制限されます。  


## <a name="sign-ins-data-shortcuts"></a>サインイン データのショートカット

Azure AD と Azure portal には両方とも、サインイン データへの追加のエントリ ポイントが用意されています。

- ID のセキュリティ保護の概要
- ユーザー
- グループ
- エンタープライズ アプリケーション

### <a name="users-sign-ins-data-in-identity-security-protection"></a>ID セキュリティ保護のユーザーのサインイン データ

**[ID のセキュリティ保護]** の概要ページのユーザー サインイン グラフは、サインインの週単位の集計を示します。期間の既定値は 30 日です。

![スクリーンショットには、1 か月のサインインのグラフが表示されています。](./media/concept-sign-ins/06.png "サインイン アクティビティ")

サインイン グラフ内の日付をクリックすると、その日のサインイン アクティビティの概要が表示されます。

サインイン アクティビティ リストの各行には、次の情報が表示されます。

* サインインしたのはだれか。
* サインインの対象となったのはどのアプリケーションか。
* サインインの状態はどのような状態か。
* サインインの MFA 状態はどのような状態か。

項目をクリックすると、サインイン操作の詳細が表示されます。

- User ID
- User
- ユーザー名
- アプリケーション ID
- Application
- Client
- 場所
- IP アドレス
- Date
- MFA が必要
- サインインの状態

> [!NOTE]
> IP アドレスは、IP アドレスとそのアドレスを持つコンピューターの物理的な配置場所との間に明確な関連性がないような方法で発行されます。 IP アドレスのマッピングは、多くの場合、クライアント デバイスの実際の使用場所から遠く離れたところにある中央プールから、モバイル プロバイダーや VPN が IP アドレスを発行しているという事実によって、複雑になります。 現在の Azure AD レポートでは、IP アドレスの物理的な場所の変換は、トレース、レジストリ データ、逆引き参照、およびその他の情報に基づくベスト エフォートで対応されています。

**[ユーザー]** ページの **[アクティビティ]** セクションの **[サインイン]** をクリックすると、すべてのユーザー サインインの完全な概要が表示されます。

![スクリーンショットには、[サインイン] を選択できる [アクティビティ] セクションが示されています。](./media/concept-sign-ins/08.png "サインイン アクティビティ")

## <a name="usage-of-managed-applications"></a>マネージド アプリケーションの使用状況

アプリケーションを中心にしてサインイン データを表示すると、次のような疑問に答えることができます。

* アプリケーションをだれが使用しているか。
* 組織内の上位 3 つのアプリケーションはどれか。
* 最新のアプリケーションはどのように実行されるか。

このデータへのエントリ ポイントとなるのは、組織内の上位 3 つのアプリケーションです。 データは **[エンタープライズ アプリケーション]** の下の **[概要]** セクションにある、過去 30 日間のレポートに含まれています。

![スクリーンショットには、[概要] を選択できる場所が示されています。](./media/concept-sign-ins/10.png "サインイン アクティビティ")

アプリ使用状況グラフは、特定の期間の上位 3 つのアプリケーションのサインインを週単位で集計します。 期間の既定値は 30 日です。

![スクリーンショットには、1 か月間のアプリの使用状況が示されています。](./media/concept-sign-ins/graph-chart.png "サインイン アクティビティ")

必要に応じて、特定のアプリケーションにフォーカスを設定できます。

![Reporting](./media/concept-sign-ins/single-app-usage-graph.png "レポーティング")

アプリ使用状況グラフ内の日付をクリックすると、サインイン アクティビティの詳細な一覧が表示されます。

**[サインイン]** オプションを使用すると、アプリケーションへのすべてのサインイン イベントの完全な概要を表示できます。

## <a name="microsoft-365-activity-logs"></a>Microsoft 365 のアクティビティ ログ

Microsoft 365 のアクティビティ ログは、[Microsoft 365 管理センター](/office365/admin/admin-overview/about-the-admin-center)から確認できます。 Microsoft 365 アクティビティ ログと Azure AD アクティビティ ログで多くのディレクトリ リソースが共有される点について考えてみましょう。 Microsoft 365 のアクティビティ ログがすべて表示されるのは、Microsoft 365 管理センターだけです。 

また、[Office 365 Management API](/office/office-365-management-api/office-365-management-apis-overview) を使用すると、Microsoft 365 のアクティビティ ログにプログラムでアクセスすることもできます。

## <a name="next-steps"></a>次のステップ

* [サインイン アクティビティ レポートのエラー コード](reference-sign-ins-error-codes.md)
* [Azure AD のデータ保有ポリシー](reference-reports-data-retention.md)
* [Azure AD のレポート待機時間](reference-reports-latencies.md)