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
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81b7030d0f45222a1641e34b341885d2ecc051e3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67107663"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory ポータルのサインイン アクティビティ レポート

Azure Active Directory (Azure AD) のレポート アーキテクチャは、次のコンポーネントで構成されます。

- **アクティビティ** 
    - **サインイン** – マネージド アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報。
    - **監査ログ** - [監査ログ](concept-audit-logs.md)は、ユーザーとグループの管理や、マネージド アプリケーションとディレクトリのアクティビティに関するシステム アクティビティ情報を提供します。
- **セキュリティ** 
    - **リスクの高いサインイン** - [リスクの高いサインイン](concept-risky-sign-ins.md)は、ユーザー アカウントの正当な所有者ではない人によって行われた可能性があるサインイン試行の指標です。
    - **リスクのフラグ付きユーザー** - [リスクの高いユーザー](concept-user-at-risk.md)は、侵害された可能性があるユーザー アカウントの指標です。

このトピックでは、サインイン レポートの概要を説明します。

## <a name="prerequisites"></a>前提条件

### <a name="who-can-access-the-data"></a>誰がデータにアクセスできますか。
* セキュリティ管理者、セキュリティ閲覧者、レポート閲覧者ロールのユーザー
* グローバル管理者
* さらに、任意のユーザー (非管理者) が自分のサインインにアクセス可能 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>サインイン アクティビティにアクセスするために必要な Azure AD ライセンスを教えてください。
* すべてのサインイン アクティビティ レポートを閲覧するためには、ご利用のテナントに、Azure AD Premium ライセンスが関連付けられている必要があります。 Azure Active Directory エディションにアップグレードするには、「[Azure Active Directory Premium の概要](../fundamentals/active-directory-get-started-premium.md)」を参照してください。 アップグレード前の時点でアクティビティ データがまったく存在しなかった場合、Premium ライセンスへのアップグレード後、データがレポートに表示されるまでに数日かかります。

## <a name="sign-ins-report"></a>サインイン レポート

ユーザーのサインイン レポートは、次の質問に対する回答を提示します。

* ユーザーのサインインにどのようなパターンがあるか。
* 1 週間で何人のユーザーがサインインを行ったか。
* これらのサインインはどのような状態か。

[Azure portal](https://portal.azure.com) の **[Azure Active Directory]** ブレードの **[アクティビティ]** セクションで **[サインイン]** を選択して、サインイン レポートにアクセスできます。 サインイン レコードによっては、ポータルに表示されるまでに最大 2 時間かかるものもあることに注意してください。

![サインイン アクティビティ](./media/concept-sign-ins/61.png "サインイン アクティビティ")

> [!IMPORTANT]
> サインイン レポートには、**対話型**のサインイン、つまりユーザーがユーザー名とパスワードを使用して手動で行うサインインのみが表示されます。 サービス間の認証のような対話型ではないサインインは、サインイン レポートに表示されません。 

サインイン ログには、次のものを示す既定のリスト ビューがあります。

- サインインの日付
- 関連するユーザー
- ユーザーがサインインしたアプリケーション
- サインインの状態
- リスク検出の状態
- 多要素認証 (MFA) の要件の状態

![サインイン アクティビティ](./media/concept-sign-ins/01.png "サインイン アクティビティ")

リスト ビューをカスタマイズするには、ツール バーの **[列]** をクリックします。

![サインイン アクティビティ](./media/concept-sign-ins/19.png "サインイン アクティビティ")

これで、追加のフィールドの表示または既に表示されているフィールドの削除ができます。

![サインイン アクティビティ](./media/concept-sign-ins/02.png "サインイン アクティビティ")

詳細な情報を取得するには、リスト ビューで項目を選択します。

![サインイン アクティビティ](./media/concept-sign-ins/03.png "サインイン アクティビティ")

> [!NOTE]
> すべてのサインイン レポートで、条件付きアクセス ポリシーのトラブルシューティングを実行できるようになりました。 サインイン レコードの **[条件付きアクセス]** タブをクリックして、条件付きアクセスの状態を確認し、サインインに適用されたポリシーの詳細と各ポリシーの結果を調べることができます。
> 詳細については、「[Frequently asked questions about CA information in all sign-ins](reports-faq.md#conditional-access)」 (すべてのサインインの CA 情報に関してよく寄せられる質問) を参照してください。

![サインイン アクティビティ](./media/concept-sign-ins/ConditionalAccess.png "サインイン アクティビティ")


## <a name="filter-sign-in-activities"></a>サインイン アクティビティのフィルター処理

報告されるデータを有用なものだけに絞り込むために、次の既定のフィールドを使用してサインイン データをフィルター処理できます。

- User
- Application
- サインインの状態
- 条件付きアクセス
- Date

![サインイン アクティビティ](./media/concept-sign-ins/04.png "サインイン アクティビティ")

**[ユーザー]** フィルターでは、確認したいユーザーの名前またはそのユーザー プリンシパル名 (UPN) を指定できます。

**[アプリケーション]** フィルターでは、確認したいアプリケーションの名前を指定できます。

**[サインイン状態]** フィルターでは、次のいずれかを選択できます。

- All
- Success
- 失敗

**条件付きアクセス** フィルターでは、次に示すサインインの CA ポリシーの状態を選択できます。

- All
- 未適用
- Success
- 失敗

**[日付]** フィルターでは、返されるデータの期間を定義できます。  
次のいずれかの値になります。

- 1 か月
- 7 日
- 24 時間
- カスタム期間

カスタムの期間を選択すると、開始時刻と終了時刻を構成できます。

サインイン ビューにフィールドを追加すると、これらのフィールドがフィルターの一覧に自動的に追加されます。 たとえば、 **[クライアント アプリ]** フィールドを一覧に追加した場合、次のフィルターを設定できるもう 1 つのフィルター オプションが表示されます。  
![サインイン アクティビティ](./media/concept-sign-ins/12.png "サインイン アクティビティ")

- **ブラウザー**  
    このフィルターは、サインイン試行がブラウザー フローを使用して行われたすべてのイベントを表示します。
- **Exchange ActiveSync (サポート対象)**  
    このフィルターは、iOS、Android、Windows Phone のようなサポートされるプラットフォームから Exchange ActiveSync (EAS) プロトコルが試行されているすべてのサインイン試行を表示します。
- **Exchange ActiveSync (サポート対象外)**  
    このフィルターは、Linux ディストリビューションのようなサポートされないプラットフォームから EAS プロトコルが試行されているすべてのサインイン試行を表示します。
- **モバイル アプリとデスクトップ クライアント**。このフィルターは、ブラウザー フローを使用していなかったすべてのサインイン試行を表示します。 これは、任意のプロトコルを使用する任意のプラットフォームからのモバイル アプリの場合や、Windows や MacOS での Office のようなデスクトップ クライアントの場合があります。
  
- **その他のクライアント**
    - **IMAP**  
        IMAP を使用して電子メールを取得する従来のメール クライアント。
    - **MAPI**  
        Office 2013。ADAL が有効になっており、MAPI が使用されています。
    - **以前のバージョンの Office クライアント**  
        ADAL が有効になっておらず、MAPI が使用されている既定の構成の Office 2013。あるいは、ADAL が無効になっている Office 2016。
    - **POP**  
        POP3 を使用して電子メールを取得する従来のメール クライアント。
    - **SMTP**  
        SMTP を使用して電子メールを送信する従来のメール クライアント。

## <a name="download-sign-in-activities"></a>サインイン アクティビティのダウンロード

Azure portal の外部で操作する場合は、[サインイン データをダウンロード](quickstart-download-sign-in-report.md)できます。 **[ダウンロード]** をクリックすると、最新の 250,000 件のレコードを含む CSV または JSON ファイルを作成することができます。  

![ダウンロード](./media/concept-sign-ins/71.png "ダウンロード")

> [!IMPORTANT]
> ダウンロードできるレコードの数は、[Azure Active Directory レポートの保持ポリシー](reference-reports-data-retention.md)によって制限されます。  


## <a name="sign-ins-data-shortcuts"></a>サインイン データのショートカット

Azure AD の他にも、Azure portal にはサインイン データに対する追加のエントリ ポイントが用意されています。

- ID のセキュリティ保護の概要
- ユーザー
- グループ
- エンタープライズ アプリケーション

### <a name="users-sign-ins-data-in-identity-security-protection"></a>ID セキュリティ保護のユーザーのサインイン データ

**[ID のセキュリティ保護]** の概要ページのユーザー サインイン グラフは、特定期間内のすべてのユーザーのサインインについて、週単位の集計を示します。 期間の既定値は 30 日です。

![サインイン アクティビティ](./media/concept-sign-ins/06.png "サインイン アクティビティ")

サインイン グラフ内の日付をクリックすると、その日のサインイン アクティビティの概要が表示されます。

サインイン アクティビティ リストの各行には、次の情報が表示されます。

* サインインしたのはだれか。
* サインインの対象となったのはどのアプリケーションか。
* サインインの状態はどのような状態か。
* サインインの MFA 状態はどのような状態か。

項目をクリックすると、サインイン操作の詳細が表示されます。

- ユーザー ID
- User
- ユーザー名
- アプリケーション ID
- Application
- Client
- Location
- IP アドレス
- Date
- MFA が必要
- サインインの状態

> [!NOTE]
> IP アドレスは、IP アドレスとそのアドレスを持つコンピューターの物理的な配置場所との間に明確な関連性がないような方法で発行されます。 IP アドレスのマッピングは、多くの場合、クライアント デバイスの実際の使用場所から遠く離れたところにある中央プールから、モバイル プロバイダーや VPN が IP アドレスを発行しているという事実によって、複雑になります。 現在の Azure AD レポートでは、IP アドレスの物理的な場所の変換は、トレース、レジストリ データ、逆引き参照、およびその他の情報に基づくベスト エフォートで対応されています。

**[ユーザー]** ページの **[アクティビティ]** セクションの **[サインイン]** をクリックすると、すべてのユーザー サインインの完全な概要が表示されます。

![サインイン アクティビティ](./media/concept-sign-ins/08.png "サインイン アクティビティ")

## <a name="usage-of-managed-applications"></a>マネージド アプリケーションの使用状況

アプリケーションを中心にしてサインイン データを表示すると、次のような疑問に答えることができます。

* アプリケーションをだれが使用しているか。
* 組織内の上位 3 つのアプリケーションはどれか。
* 最近ロールアウトしたアプリケーションは、 どのような状況か。

このデータへのエントリ ポイントは、 **[エンタープライズ アプリケーション]** の **[概要]** セクションにある過去 30 日間のレポートに示される、組織内の上位 3 つのアプリケーションです。

![サインイン アクティビティ](./media/concept-sign-ins/10.png "サインイン アクティビティ")

アプリ使用状況グラフは、特定の期間の上位 3 つのアプリへのサインインを週ごとに集計します。 期間の既定値は 30 日です。

![サインイン アクティビティ](./media/concept-sign-ins/47.png "サインイン アクティビティ")

必要に応じて、特定のアプリケーションにフォーカスを設定できます。

![レポート](./media/concept-sign-ins/single_spp_usage_graph.png "レポート")

アプリ使用状況グラフ内の日付をクリックすると、サインイン アクティビティの詳細な一覧が表示されます。

**[サインイン]** オプションを使用すると、アプリケーションへのすべてのサインイン イベントの完全な概要を表示できます。

![サインイン アクティビティ](./media/concept-sign-ins/11.png "サインイン アクティビティ")

## <a name="office-365-activity-logs"></a>Office 365 のアクティビティ ログ

Office 365 のアクティビティ ログは、[Microsoft 365 管理センター](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center)から確認できます。 Office 365 のアクティビティ ログと Azure AD のアクティビティ ログでは多くのディレクトリ リソースが共有されていますが、Office 365 のアクティビティ ログがすべて表示されるのは、Microsoft 365 管理センターのみです。 

[Office 365 Management API シリーズ](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview)を使用すると、Office 365 のアクティビティ ログにプログラムでアクセスすることもできます。

## <a name="next-steps"></a>次の手順

* [サインイン アクティビティ レポートのエラー コード](reference-sign-ins-error-codes.md)
* [Azure AD のデータ保有ポリシー](reference-reports-data-retention.md)
* [Azure AD のレポート待機時間](reference-reports-latencies.md)

