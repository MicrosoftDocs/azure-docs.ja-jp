---
title: Azure portal のプロビジョニング ログの概要 (プレビュー) | Microsoft Docs
description: Azure portal を通じて Azure Active Directory のプロビジョニング ログ レポートの概要を取得します。
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
ms.date: 1/29/2021
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d8c4876faf9ebc2619309aa0095a8ffe1e9e93d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102500548"
---
# <a name="overview-of-provisioning-logs-in-the-azure-portal-preview"></a>Azure portal のプロビジョニング ログの概要 (プレビュー)

Azure Active Directory (Azure AD) のレポート アーキテクチャは、次のコンポーネントで構成されます。

- アクティビティ: 
    - **サインイン**:マネージド アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報。
    - [[監査ログ]](concept-audit-logs.md) :ユーザーとグループの管理、マネージド アプリケーション、ディレクトリ アクティビティに関するシステム アクティビティ情報。
    - **プロビジョニング ログ**:Azure AD プロビジョニング サービスによってプロビジョニングされたユーザー、グループ、ロールに関するシステム アクティビティ。 

- セキュリティ: 
    - **危険なサインイン**:[危険なサインイン](../identity-protection/overview-identity-protection.md)は、ユーザー アカウントの正当な所有者ではない人によってサインインが試行された可能性があることを示す指標です。
    - **リスクのフラグ付きユーザー**:[リスクの高いユーザー](../identity-protection/overview-identity-protection.md)は、侵害された可能性があるユーザー アカウントを示す指標です。

このトピックでは、プロビジョニング ログの概要を説明します。 このログでは、次のような質問に対する回答が提供されます。 

* ServiceNow で正常に作成されたグループ
* Adobe から正常に削除されたユーザー
* Active Directory で正常に作成された Workday のユーザー 

## <a name="prerequisites"></a>前提条件

これらのユーザーは、プロビジョニング ログのデータにアクセスできます。

* アプリケーションの所有者 (所有するアプリケーションのログ)
* セキュリティ管理者、セキュリティ閲覧者、レポート閲覧者、セキュリティ オペレーター、アプリケーション管理者、クラウド アプリケーション管理者のロールであるユーザー
* [provisioningLogs アクセス許可](../roles/custom-enterprise-app-permissions.md#full-list-of-permissions)を持つカスタム ロールのユーザー
* グローバル管理者


プロビジョニング アクティビティ レポートを閲覧するには、ご利用のテナントに、Azure AD Premium ライセンスが関連付けられている必要があります。 Azure AD エディションをアップグレードするには、「[Azure Active Directory Premium の概要](../fundamentals/active-directory-get-started-premium.md)」を参照してください。 


## <a name="ways-of-interacting-with-the-provisioning-logs"></a>プロビジョニング ログとの対話方法 
お客様は、次の 4 つの方法を使用してプロビジョニング ログと対話できます。

- 次のセクションの説明に従って、Azure portal からログにアクセスします。
- プロビジョニング ログを [Azure Monitor](../app-provisioning/application-provisioning-log-analytics.md) にストリーミングします。 この方法を使用すると、データ保持期間を延長でき、カスタムのダッシュボード、アラート、クエリを作成できます。
- [Microsoft Graph API](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta) のクエリを実行して、プロビジョニング ログを見つけます。
- プロビジョニング ログを CSV または JSON ファイルとしてダウンロードします。

## <a name="access-the-logs-from-the-azure-portal"></a>Azure portal からログにアクセスする
[Azure portal](https://portal.azure.com) の **[Azure Active Directory]** ウィンドウの **[監視]** セクションで **[プロビジョニング ログ]** を選択して、プロビジョニング ログにアクセスできます。 プロビジョニング レコードによっては、ポータルに表示されるまでに最大 2 時間かかるものもあります。

![プロビジョニング ログにアクセスするための選択を示すスクリーンショット。](./media/concept-provisioning-logs/access-provisioning-logs.png "プロビジョニング ログ")


プロビジョニング ログには、次のものを示す既定のリスト ビューがあります。

- ID
- アクション
- ソース システム
- ターゲット システム
- 状態
- 日付


![プロビジョニング ログ内の既定の列を示すスクリーンショット。](./media/concept-provisioning-logs/default-columns.png "既定の列")

ツール バーの **[列]** を選択することで、リスト ビューをカスタマイズできます。

![列をカスタマイズするためのボタンを示すスクリーンショット。](./media/concept-provisioning-logs/column-chooser.png "列の選択")

この領域で、追加のフィールドの表示または既に表示されているフィールドの削除ができます。

![選択可能な列を示すスクリーンショット。いくつかの列が選択されている。](./media/concept-provisioning-logs/available-columns.png "使用可能な列")

詳細な情報を取得するには、リスト ビューで項目を選択します。

![詳細情報を示すスクリーンショット。](./media/concept-provisioning-logs/steps.png "Assert")


## <a name="filter-provisioning-activities"></a>プロビジョニング アクティビティのフィルター処理

プロビジョニング データをフィルター処理できます。 いくつかのフィルター値は、テナントに基づいて動的に設定されます。 たとえば、テナントに "作成" イベントがない場合は、 **[作成]** のフィルター オプションはありません。

既定のビューでは、次のフィルターを選択できます。

- **ID**
- **日付**
- **状態**
- **操作**


![フィルター値を示すスクリーンショット。](./media/concept-provisioning-logs/default-filter.png "Assert")

**ID** フィルターを使用すると、関心のある名前または ID を指定できます。 この ID は、ユーザー、グループ、ロール、またはその他のオブジェクトの場合があります。 

オブジェクトの名前または ID で検索できます。 ID はシナリオによって異なります。 たとえば、Azure AD から SalesForce にオブジェクトをプロビジョニングする場合、ソース ID は Azure AD 内のユーザーのオブジェクト ID です。 ターゲット ID は、Salesforce のユーザーの ID です。 Workday から Active Directory にプロビジョニングする場合、ソース ID は Workday ワーカーの従業員 ID です。 

> [!NOTE]
> ユーザーの名前が必ずしも **ID** 列に存在するとは限りません。 常に 1 つの ID が存在します。 


**[日付]** フィルターでは、返されるデータの期間を定義できます。 次のいずれかの値になります。

- 1 か月
- 7 日
- 30 日
- 24 時間
- カスタム期間

カスタムの期間を選択すると、開始日と終了日を構成できます。

**[状態]** フィルターでは、次のいずれかを選択できます。

- **すべて**
- **Success**
- **障害**
- **Skipped**

**[アクション]** フィルターでは、これらのアクションをフィルター処理できます。

- **作成** 
- **アップデート**
- **削除**
- **無効化**
- **その他**

既定のビューのフィルターに加えて、次のフィルターを設定できます。

![フィルターとして追加できるフィールドを示すスクリーンショット。](./media/concept-provisioning-logs/add-filter.png "フィールドを選択する")

- **ジョブ ID**:プロビジョニングを有効にした各アプリケーションに、一意のジョブ ID が関連付けられます。   

- **サイクル ID**:サイクル ID は、プロビジョニング サイクルを一意に識別します。 この ID を製品サポートと共有して、このイベントが発生したサイクルを調べることができます。

- **変更 ID**:変更 ID は、プロビジョニング イベントの一意の識別子です。 この ID を製品サポートと共有して、プロビジョニング イベントを調べることができます。   

- **ソース システム**:ID のプロビジョニング元となる場所を指定できます。 たとえば、Azure AD から ServiceNow にオブジェクトをプロビジョニングする場合、ソース システムは Azure AD です。 

- **ターゲット システム**:ID のプロビジョニング先となる場所を指定できます。 たとえば、Azure AD から ServiceNow にオブジェクトをプロビジョニングする場合、ターゲット システムは ServiceNow です。 

- **アプリケーション**:特定の文字列が含まれている表示名を持つアプリケーションのレコードのみを表示できます。

## <a name="provisioning-details"></a>プロビジョニングの詳細 

プロビジョニング リスト ビューで項目を選択すると、この項目の詳細が表示されます。 詳細は次のタブにグループ化されています。

![プロビジョニングの詳細を含む 4 つのタブを示すスクリーンショット。](./media/concept-provisioning-logs/provisioning-tabs.png "タブ")

- **手順**:オブジェクトをプロビジョニングするために実行される手順の概要が示されます。 オブジェクトのプロビジョニングは次の 4 つの手順で構成されます。
  
  1. オブジェクトをインポートする。
  1. オブジェクトがスコープ内にあるかどうかを判断する。
  1. ソースとターゲットの間でオブジェクトを一致させる。
  1. オブジェクト (作成、更新、削除、または無効化) をプロビジョニングする。

  ![[手順] タブのプロビジョニング手順を示すスクリーンショット。](./media/concept-provisioning-logs/steps.png "Assert")

- **トラブルシューティングと推奨事項**:エラー コードと理由が表示されます。 エラー情報は、障害が発生した場合にのみ利用できます。

- **変更されたプロパティ**:古い値と新しい値が表示されます。 古い値が存在しない場合、その列は空白になります。

- **概要**:ソースとターゲットのシステムのオブジェクトの発生内容の概要と識別子が表示されます。

## <a name="download-logs-as-csv-or-json"></a>ログを CSV または JSON としてダウンロードする

Azure portal のログに移動して **[ダウンロード]** を選択することで、プロビジョニング ログを後で使用するためにダウンロードできます。 ファイルは、選択したフィルター条件に基づいてフィルター処理されます。 ダウンロードのサイズと時間を削減するために、フィルターはできるだけ具体的に指定してください。 

CSV のダウンロードには、次の 3 つのファイルが含まれます。

* **ProvisioningLogs**:プロビジョニングの手順と変更されたプロパティを除く、すべてのログをダウンロードします。
* **ProvisioningLogs_ProvisioningSteps**:プロビジョニングの手順と変更 ID を含みます。 変更 ID を使用すると、イベントを他の 2 つのファイルと結合できます。
* **ProvisioningLogs_ModifiedProperties**:変更された属性と変更 ID を含みます。 変更 ID を使用すると、イベントを他の 2 つのファイルと結合できます。

#### <a name="open-the-json-file"></a>JSON ファイルを開く
JSON ファイルを開くには、[Microsoft Visual Studio Code](https://aka.ms/vscode) などのテキスト エディターを使用します。 Visual Studio Code では構文の強調表示があるため、ファイルが読み取りやすくなります。 [Microsoft Edge](https://aka.ms/msedge) などのブラウザーを使用して、編集不可の形式で JSON ファイルを開くこともできます。 

#### <a name="prettify-the-json-file"></a>JSON ファイルを読みやすくする
JSON ファイルは、ダウンロードのサイズを削減するために、縮小された形式でダウンロードされます。 この形式では、ペイロードが読みにくくなる場合があります。 ファイルを読みやすくするための 2 つの方法を確認してください。

- [Visual Studio Code を使用して JSON を書式設定](https://code.visualstudio.com/docs/languages/json#_formatting)する。

- PowerShell を使用して JSON を書式設定する。 このスクリプトによって、JSON はタブとスペースを含む形式で出力されます。 

  ` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

  `$JSONContent | ConvertTo-Json > <PATH TO OUTPUT THE JSON FILE>`

#### <a name="parse-the-json-file"></a>JSON ファイルを解析する

ここでは、PowerShell を使用して JSON ファイルを操作するためのサンプル コマンドをいくつか紹介します。 使い慣れた任意のプログラミング言語を使用できます。  

まず、このコマンドを実行して [JSON ファイルを読み取ります](/powershell/module/microsoft.powershell.utility/convertfrom-json)。

` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

これで、自分のシナリオに従ってデータを解析できます。 いくつかの例を次に示します。 

- JSON ファイル内のすべてのジョブ ID を出力します。

  `foreach ($provitem in $JSONContent) { $provitem.jobId }`

- アクションが "create" であったイベントのすべての変更 ID を出力します。

  `foreach ($provitem in $JSONContent) { `
  `   if ($provItem.action -eq 'Create') {`
  `       $provitem.changeId `
  `   }`
  `}`

## <a name="what-you-should-know"></a>知っておくべきこと

レポートのプロビジョニングに関するヒントと考慮事項をここに示します。

- 報告されたプロビジョニング データは、Premium Edition の場合は 30 日間、Free Edition の場合は 7 日間、Azure portal に保存されます。 30 日を過ぎても保持する場合は、プロビジョニング ログを [Log Analytics](../app-provisioning/application-provisioning-log-analytics.md) に発行できます。 

- 変更 ID 属性は、一意の識別子として使用できます。 これは、製品サポートとやり取りするときなどに便利です。

- スコープに含まれていないユーザーの場合、スキップされたイベントが表示されることがあります。 同期スコープがすべてのユーザーとグループに設定されているときは特にこれが予想されます。 このサービスでは、スコープ外であっても、テナント内のすべてのオブジェクトが評価されます。 

- プロビジョニング ログは現在、政府のクラウドで利用できません。 プロビジョニング ログにアクセスできない場合は、一時的な回避策として監査ログを使用してください。 

- プロビジョニング ログには、ロールのインポートは表示されません (AWS、Salesforce、Zendesk に適用されます)。 ロールのインポートのログは、監査ログで確認できます。 

## <a name="error-codes"></a>エラー コード

次の表を利用して、プロビジョニング ログに記録されているエラーの解決方法をより深く理解してください。 記載されていないエラー コードがあった場合は、このページの下部にあるリンクを使用してフィードバックをお送りください。 

|エラー コード|説明|
|---|---|
|Conflict、EntryConflict|Azure AD またはアプリケーションで、競合している属性値を修正してください。 または、競合しているユーザー アカウントが一致し、取って代わられたと思われるかどうか、一致する属性の構成を確認します。 一致する属性の構成の詳細については、この[ドキュメント](../app-provisioning/customize-application-attributes.md)を確認してください。|
|TooManyRequests|ターゲット アプリは過負荷になり多すぎる要求を受け取っているため、ユーザーを更新するこの試行を拒否しました。 行うことは何もありません。 この試行は自動的に中止されます。 Microsoft もこの問題について通知されています。|
|InternalServerError |ターゲット アプリから予期しないエラーが返されました。 ターゲット アプリケーションに関するサービスの問題によって、その動作が妨げられている可能性があります。 この試行は 40 分で自動的に中止されます。|
|InsufficientRights、MethodNotAllowed、NotPermitted、Unauthorized| Azure AD はターゲット アプリケーションによって認証されましたが、更新を実行する権限がありませんでした。 ターゲット アプリケーションによって提供された手順と、それぞれのアプリケーションの[チュートリアル](../saas-apps/tutorial-list.md)を確認してください。|
|UnprocessableEntity|ターゲット アプリケーションが予期しない応答を返しました。 ターゲット アプリケーションの構成が正しくないか、ターゲット アプリケーションに関するサービスの問題によって、その動作が妨げられている可能性があります。|
|WebExceptionProtocolError |ターゲット アプリケーションへの接続時に HTTP プロトコル エラーが発生しました。 することはありません。 この試行は 40 分で自動的に中止されます。|
|InvalidAnchor|プロビジョニング サービスによって以前作成または照合されたユーザーは存在しなくなりました。 そのユーザーが存在することを確認してください。 すべてのユーザーの新たな照合を強制するには、Microsoft Graph API を使用して[ジョブを再起動](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta)します。 <br><br>プロビジョニングを再開すると初期サイクルがトリガーされ、完了するまで時間がかかる場合があります。 また、プロビジョニングを再開することで、プロビジョニング サービスによって操作に使用されるキャッシュも削除されます。 これは、テナント内のすべてのユーザーとグループを再評価する必要があること、および特定のプロビジョニング イベントが削除される可能性があることを意味します。|
|NotImplemented | ターゲット アプリから予期しない応答が返されました。 アプリの構成が正しくないか、ターゲット アプリに関するサービスの問題によって、その動作が妨げられている可能性があります。 ターゲット アプリケーションによって提供された手順と、それぞれのアプリケーションの[チュートリアル](../saas-apps/tutorial-list.md)を確認してください。 |
|MandatoryFieldsMissing、MissingValues |必須の値がないため、ユーザーを作成できませんでした。 ソース レコード内の不足している属性値を修正するか、一致する属性の構成を調べて、必須フィールドが省略されていないことを確認してください。 一致する属性の構成に関する[詳細を確認](../app-provisioning/customize-application-attributes.md)してください。|
|SchemaAttributeNotFound |ターゲット アプリケーションに存在しない属性が指定されたため、操作を実行できませんでした。 属性のカスタマイズに関する[ドキュメント](../app-provisioning/customize-application-attributes.md)を参照し、構成が正しいことを確認してください。|
|InternalError |Azure AD プロビジョニング サービス内で内部サービス エラーが発生しました。 することはありません。 この試行は 40 分で自動的に再試行されます。|
|InvalidDomain |属性値に無効なドメイン名が含まれているため、操作を実行できませんでした。 ユーザーのドメイン名を更新するか、ターゲット アプリケーションの許可リストにそのドメイン名を追加します。 |
|タイムアウト |ターゲット アプリケーションが応答するのに時間がかかりすぎたため、操作を完了できませんでした。 することはありません。 この試行は 40 分で自動的に再試行されます。|
|LicenseLimitExceeded|このユーザーが使用できるライセンスがないため、ターゲット アプリケーションでユーザーを作成できませんでした。 ターゲット アプリケーションの追加ライセンスを購入してください。 または、ユーザーの割り当てと属性マッピングの構成を調べて、正しい属性で正しいユーザーが割り当てられていることを確認してください。|
|DuplicateTargetEntries  |ターゲット アプリケーションの複数のユーザーに、構成済みの一致する属性があると検出されたため、操作を完了できませんでした。 重複しているユーザーをターゲット アプリケーションから削除するか、[属性マッピングを再構成](../app-provisioning/customize-application-attributes.md)してください。|
|DuplicateSourceEntries | 複数のユーザーに、構成済みの一致する属性があると検出されたため、操作を完了できませんでした。 重複しているユーザーを削除するか、[属性マッピングを再構成](../app-provisioning/customize-application-attributes.md)してください。|
|ImportSkipped | システムでは、各ユーザーが評価されるときに、ソース システムからのそのユーザーのインポートが試みられます。 このエラーが発生するのは一般的に、インポートされるユーザーが、属性マッピングで定義されている一致するプロパティを持っていない場合です。 一致する属性のユーザー オブジェクトに値が指定されていない場合、システムではスコープ、一致、またはエクスポートの変更を評価することはできません。 このエラーが発生したとしても、ユーザーのスコープをまだ評価していないため、このユーザーがスコープ内にあることを示すものではないことに注意してください。|
|EntrySynchronizationSkipped | プロビジョニング サービスによってソース システムに対するクエリが正常に実行され、ユーザーが識別されました。 ユーザーに対してこれ以上の操作は行われず、スキップされました。 ユーザーがスコープ外にあるか、ユーザーがターゲット システムに既に存在していて、それ以上の変更は必要でない可能性があります。|
|SystemForCrossDomainIdentityManagementMultipleEntriesInResponse| ユーザーまたはグループを取得する GET 要求に対し、複数のユーザーまたはグループが応答で受信されました。 システムでは、1 つのユーザーまたはグループだけを応答で受信することが想定されています。 [たとえば](../app-provisioning/use-scim-to-provision-users-and-groups.md#get-group)、グループを取得するための GET 要求を実行してメンバーを除外するフィルターを指定したときに、クロスドメイン ID 管理システム (SCIM) エンドポイントからメンバーが返された場合、このエラーを受け取ります。|

## <a name="next-steps"></a>次のステップ

* [ユーザー プロビジョニングの状態を確認する](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)
* [Azure AD ギャラリー アプリケーションへのユーザー プロビジョニングの構成に関する問題](../app-provisioning/application-provisioning-config-problem.md)
* [プロビジョニング ログの Graph API](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta)