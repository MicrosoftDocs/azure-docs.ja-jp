---
title: Azure Sentinel のプレイブックを使用して脅威への対応を自動化する | Microsoft Docs
description: この記事では、Azure Sentinel での自動化について説明し、プレイブックを使用して脅威の防止と対応を自動化する方法を示します。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: yelevin
ms.openlocfilehash: 0158c9f5b9debf0c47978e816951e25634621645
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104608836"
---
# <a name="automate-threat-response-with-playbooks-in-azure-sentinel"></a>Azure Sentinel のプレイブックを使用して脅威への対応を自動化する

この記事では、Azure Sentinel プレイブックの概要と、それを使ってセキュリティ オーケストレーション、自動化、および対応 (SOAR: Security Orchestration, Automation and Response) 操作を実装し、時間とリソースを節約しながらより良い結果を実現する方法について説明します。

## <a name="what-is-a-playbook"></a>プレイブックとは

通常、SIEM または SOC チームには、セキュリティ アラートやインシデントが日常的に押し寄せています。その量のあまりの多さに、対応するスタッフは圧倒されています。 この結果、多くのアラートが無視され、多くのインシデントが調査されず、気付かずに行われる攻撃に対して組織は脆弱な状態のままになっていることが非常によくあります。

これらのアラートのほとんどではないにしても多くは、定義された特定の修復アクションで対処できる、繰り返し起こるパターンに従っています。

プレイブックは、Azure Sentinel からルーチンとして実行できるこれらの修復アクションのコレクションです。 プレイブックは、脅威への対応を自動化し、調整するのに役立ちます。これは、分析ルールまたは自動化ルールによってトリガーされたときに、それぞれ、手動で実行するように、または特定のアラートやインシデントに対応して自動的に実行されるように設定できます。

プレイブックはサブスクリプション レベルで作成および適用されますが、**プレイブック** タブ (新しい **オートメーション** ブレードにある) には、選択したすべてのサブスクリプションで利用可能なすべてのプレイブックが表示されます。

### <a name="azure-logic-apps-basic-concepts"></a>Azure Logic Apps の基本概念

Azure Sentinel のプレイブックは、エンタープライズ全体のシステムでタスクとワークフローをスケジュール、自動化、および調整するのに役立つクラウド サービスである [Azure Logic Apps](../logic-apps/logic-apps-overview.md) で作成されたワークフローに基づいています。 つまり、プレイブックでは Logic Apps の組み込みテンプレートにあるすべての機能とカスタマイズ性を活用できます。

> [!NOTE]
> Azure Logic Apps は別個のリソースのため、追加料金が適用される場合があります。 詳細については、[Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) の価格ページを参照してください。

Azure Logic Apps は、コネクタを使用して他のシステムやサービスと通信します。 コネクタといくつかの重要な属性に関する簡単な説明を次に示します。

- **マネージド コネクタ:** 特定の製品またはサービスに対する API 呼び出しに関連する一連のアクションとトリガー。 Azure Logic Apps では、Microsoft と Microsoft 以外の両方のサービスと通信するための多数のコネクタが提供されています。
  - [すべての Logic Apps コネクタとそのドキュメントの一覧](/connectors/connector-reference/)

- **カスタム コネクタ:** 事前構築コネクタとして使用できないサービスと通信する必要がある場合があります。 カスタム コネクタは、ユーザーがコネクタを作成 (および共有) し、独自のトリガーとアクションを定義するのを許可することで、このニーズに対応しています。
  - [独自のカスタム Logic Apps コネクタを作成する](/connectors/custom-connectors/create-logic-apps-connector)

- **Azure Sentinel コネクタ:** Azure Sentinel と対話するプレイブックを作成するには、Azure Sentinel コネクタを使用します。
  - [Azure Sentinel コネクタのドキュメント](/connectors/azuresentinel/)

- **トリガー:** プレイブックを開始するコネクタ コンポーネント。 これには、トリガーされたときにプレイブックで受信されることが想定されているスキーマが定義されています。 現在、Azure Sentinel コネクタには次の 2 つのトリガーがあります。
  - [アラート トリガー](/connectors/azuresentinel/#triggers): プレイブックでは、アラートを入力として受け取ります。
  - [インシデント トリガー](/connectors/azuresentinel/#triggers): プレイブックでは、含まれているすべてのアラートとエンティティと共に、インシデントを入力として受け取ります。

    > [!IMPORTANT]
    >
    > - プレイブックの **インシデント トリガー** 機能は、現在、**プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

- **アクション:** アクションは、トリガー後に発生するすべてのステップです。 それらは並列に、または複合条件のマトリックスに、連続的に配列できます。

- **動的フィールド:** トリガーとアクションの出力スキーマによって決定され、実際の出力によって設定される一時フィールド。これは、その後のアクションで使用できます。

### <a name="permissions-required"></a>必要なアクセス許可

 SecOps チームが Azure Sentinel でセキュリティ オーケストレーション、自動化、および対応 (SOAR) 操作を使用 (つまりプレイブックを作成して実行) できるようにするには、セキュリティ運用チームの特定のメンバーまたはチーム全体に Azure ロールを割り当てることができます。 次に、使用可能なさまざまなロールと、それらを割り当てる必要があるタスクについて説明します。

#### <a name="azure-roles-for-logic-apps"></a>Logic Apps 用の Azure ロール

- **ロジック アプリの共同作成者** を使用すると、ロジック アプリを管理し、プレイブックを実行できます。ただし、それらへのアクセスを変更することはできません (そのためには、**所有者** ロールが必要です)。
- **ロジック アプリのオペレーター** を使用すると、ロジック アプリの読み取り、有効化、無効化を行うことができます。ただし、編集および更新はできません。

#### <a name="azure-roles-for-sentinel"></a>Sentinel 用の Azure ロール

- **Azure Sentinel 共同作成者** ロールを使用すると、プレイブックを分析ルールにアタッチできます。
- **Azure Sentinel レスポンダー** ロールを使用すると、プレイブックを手動で実行できます。
- **Azure Sentinel Automation 共同作成者** を使用すると、自動化ルールでプレイブックを実行できます。 他の目的では使用できません。

#### <a name="learn-more"></a>詳細情報

- [Azure Logic Apps の Azure ロールに関する詳細情報](../logic-apps/logic-apps-securing-a-logic-app.md#access-to-logic-app-operations)。
- [Azure Sentinel の Azure ロールに関する詳細情報](roles.md)。

## <a name="steps-for-creating-a-playbook"></a>プレイブックの作成手順

- [自動化シナリオを定義する](#use-cases-for-playbooks)。

- [Azure ロジック アプリを作成する](tutorial-respond-threats-playbook.md)。

- [ロジック アプリをテストする](#run-a-playbook-manually-on-an-alert)。

- プレイブックを[自動化ルール](#incident-creation-automated-response)または[分析ルール](#alert-creation-automated-response)にアタッチするか、[必要に応じて手動で実行する](#run-a-playbook-manually-on-an-alert)。

### <a name="use-cases-for-playbooks"></a>プレイブックのユース ケース

Azure Logic Apps プラットフォームでは何百ものアクションとトリガーが提供されているため、ほとんどすべての自動化シナリオを作成できます。 Azure Sentinel では、次の SOC シナリオから開始することを推奨しています。

#### <a name="enrichment"></a>エンリッチメント

**データを収集し、よりスマートな意思決定を行うためにインシデントにアタッチします。**

次に例を示します。

Azure Sentinel インシデントが、IP アドレス エンティティを生成する分析ルールによってアラートから作成されました。

このインシデントによって、次の手順でプレイブックを実行する自動化ルールがトリガーされます。

- [新しい Azure Sentinel インシデントが作成されたとき](/connectors/azuresentinel/#triggers)に開始します。 インシデントで表されるエンティティが、インシデント トリガーの動的フィールドに格納されます。

- 各 IP アドレスについて、外部の脅威インテリジェンス プロバイダー ([Virus Total](https://www.virustotal.com/) など) に対してクエリを実行し、さらに多くのデータを取得します。

- 返されたデータと分析情報をインシデントのコメントとして追加します。

#### <a name="bi-directional-sync"></a>双方向の同期

**プレイブックを使用して、Azure Sentinel インシデントを他のチケット システムと同期できます。**

次に例を示します。

すべてのインシデント作成用の自動化ルールを作成し、ServiceNow でチケットを開くプレイブックをアタッチします。

- [新しい Azure Sentinel インシデントが作成されたとき](/connectors/azuresentinel/#triggers)に開始します。

- [ServiceNow](/connectors/service-now/) で新しいチケットを作成します。

- インシデント名、重要なフィールド、および Azure Sentinel インシデントへの URL をチケットに含めて、簡単にピボットできるようにします。

#### <a name="orchestration"></a>オーケストレーション

**SOC チャット プラットフォームを使用して、インシデント キューの制御を強化します。**

次に例を示します。

Azure Sentinel インシデントが、ユーザー名と IP アドレス エンティティを生成する分析ルールによってアラートから作成されました。

このインシデントによって、次の手順でプレイブックを実行する自動化ルールがトリガーされます。

- [新しい Azure Sentinel インシデントが作成されたとき](/connectors/azuresentinel/#triggers)に開始します。

- セキュリティ アナリストがインシデントを認識できるように、[Microsoft Teams](/connectors/teams/) または [Slack](/connectors/slack/) のセキュリティ オペレーション チャネルにメッセージを送信します。

- シニア ネットワーク管理者とセキュリティ管理者に、アラートのすべての情報をメールで送信します。このメール メッセージには、**ブロック** するか **無視** するかをユーザーが選択できるボタンが含まれます。

- 管理者からの応答を受信するまで待ってから、実行を続けます。

- 管理者が **ブロック** を選択した場合は、アラートに含まれている IP アドレスをブロックするコマンドをファイアウォールに、そしてそのユーザーを無効にする別のものを Azure AD に送信します。

#### <a name="response"></a>Response

**人間への依存を最小限に抑えて、直ちに脅威に対応します。**

2 つの例を挙げます。

**例 1:** [Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) によって検出された、侵害されたユーザーを示す分析ルールに対応します。

   - [新しい Azure Sentinel インシデントが作成されたとき](/connectors/azuresentinel/#triggers)に開始します。

   - 侵害された疑いがあるインシデント内の各ユーザー エンティティに対して、次を実行します。

     - Teams メッセージを該当するユーザーに送信して、ユーザーが不審な行動を取ったことに関する確認を要求します。

     - Azure AD Identity Protection を確認して、[ユーザーが侵害された状態であることを確認](/connectors/azureadip/#confirm-a-risky-user-as-compromised)します。 Azure AD Identity Protection では、ユーザーに **危険** のラベルを付け、既に構成されている強制ポリシーを適用します。たとえば、次回のサインイン時に MFA を使用するようにユーザーに要求します。

        > [!NOTE]
        > プレイブックでは、ユーザーに対する強制アクションを開始することも、強制ポリシーの構成を開始することもありません。 必要に応じて、既に定義されているポリシーを適用するように Azure AD Identity Protection に指示するだけです。 すべての強制は、Azure AD Identity Protection に定義されている適切なポリシーに完全に依存します。

**例 2:** [Microsoft Defender for Endpoint](/windows/security/threat-protection/) によって検出された、侵害されたマシンを示す分析ルールに対応します。

   - [新しい Azure Sentinel インシデントが作成されたとき](/connectors/azuresentinel/#triggers)に開始します。

   - Azure Sentinel の **[Entities - Get Hosts]\(エンティティ - ホストの取得\)** アクションを使用して、インシデント エンティティに含まれている疑わしいマシンを解析します。

   - Microsoft Defender for Endpoint に対して、アラート内の[マシンを分離する](/connectors/wdatp/#actions---isolate-machine)コマンドを発行します。

## <a name="how-to-run-a-playbook"></a>プレイブックの実行方法

プレイブックは、**手動** でも **自動** でも実行できます。

手動で実行する場合、アラートを受け取ったときに、選択したアラートへの応答としてプレイブックをオンデマンドで実行することを選択できます。 この機能が現在サポートされているのはアラートに対してのみであり、インシデントは対象外です。

自動的に実行する場合、(アラートの場合は) 分析ルールに自動応答として、(インシデントの場合は) 自動化ルールにアクションとして設定します。 自動化ルールの詳細情報については、[こちら](automate-incident-handling-with-automation-rules.md)をご覧ください。

### <a name="set-an-automated-response"></a>自動応答を設定する

セキュリティ運用チームは、繰り返し発生するタイプのインシデントやアラートに対する所定の対応を完全に自動化することにより、作業負荷を大幅に削減できます。これにより、一般的でないインシデントやアラートに集中し、パターンの分析や脅威ハンティングなどを行うことができます。

自動応答を設定すると、分析ルールがトリガーされるたびに、アラートの作成に加えて、ルールによって作成されたアラートを入力として受け取るプレイブックが実行されます。

アラートによってインシデントが作成された場合は、インシデントによって自動化ルールがトリガーされます。それにより、アラートによって作成されたインシデントを入力として受け取るプレイブックが実行されます。

#### <a name="alert-creation-automated-response"></a>アラート作成の自動応答

アラートの作成によってトリガーされ、アラートを入力として受け取るプレイブックの場合 (その最初の手順は、「Azure Sentinel アラートがトリガーされたとき」です)、プレイブックを分析ルールにアタッチします。

1. 自動応答を定義するアラートを生成する[分析ルール](tutorial-detect-threats-custom.md)を編集します。

1. **[自動応答]** タブの **[アラートの自動化]** で、アラートが作成されたときにこの分析ルールによってトリガーされる 1 つ以上のプレイブックを選択します。

#### <a name="incident-creation-automated-response"></a>インシデント作成の自動応答

インシデントの作成によってトリガーされ、インシデントを入力として受け取るプレイブックの場合 (その最初の手順は、「Azure Sentinel インシデントがトリガーされたとき」です)、自動化ルールを作成し、そこに **[プレイブックの実行]** アクションを定義します。 これは、2 つの方法で実行できます。

- 自動応答を定義するインシデントを生成する分析ルールを編集します。 **[自動応答]** タブの **[インシデントの自動化]** で、自動化ルールを作成します。 これにより、この分析ルール専用の自動応答が作成されます。

- **[自動化]** ブレードの **[自動化ルール]** タブから、新しい自動化ルールを作成し、適切な条件と必要なアクションを指定します。 この自動化ルールは、指定された条件を満たすすべての分析ルールに適用されます。

    > [!NOTE]
    > 自動化ルールからプレイブックを実行するために、Azure Sentinel では、それを行うために特別に承認されたサービス アカウントが使用されます。 (ユーザー アカウントではなく) このアカウントを使用すると、サービスのセキュリティ レベルが向上し、自動化ルール API で CI/CD のユース ケースをサポートできるようになります。
    >
    > このアカウントには、プレイブックが存在するリソース グループに対する明示的なアクセス許可が付与される必要があります。 その時点で、すべての自動化ルールでそのリソース グループ内のすべてのプレイブックを実行できるようになります。
    >
    > **[プレイブックの実行]** アクションを自動化ルールに追加すると、プレイブックのドロップダウン リストが表示されます。 Azure Sentinel がアクセス許可を持たないプレイブックは、使用不可として (「淡色表示」で) 表示されます。 **[Manage playbook permissions]\(プレイブックのアクセス許可の管理\)** リンクを選択すると、その場で Azure Sentinel にアクセス許可を付与できます。
    >
    > マルチテナント ([Lighthouse](extend-sentinel-across-workspaces-tenants.md#managing-workspaces-across-tenants-using-azure-lighthouse)) のシナリオでは、プレイブックを呼び出す自動化ルールが別のテナントにある場合でも、プレイブックが存在するテナントに対するアクセス許可を定義する必要があります。 そのためには、プレイブックのリソース グループに対して **所有者** アクセス許可を持っている必要があります。

[自動化ルールを作成するための完全な手順](tutorial-respond-threats-playbook.md#respond-to-incidents)に関する記事を参照してください。

### <a name="run-a-playbook-manually-on-an-alert"></a>アラートに対して手動でプレイブックを実行する

手動によるトリガーは、Azure Sentinel ポータルの次のブレードから利用できます。

- **[インシデント]** ビューで、特定のインシデントを選択し、その **[アラート]** タブを開き、アラートを選択します。

- **[調査]** で、特定のアラートを選択します。

1. 選択したアラートの **[プレイブックの表示]** をクリックします。 「**Azure Sentinel アラートがトリガーされたとき**」から始まる、アクセス許可を持っているすべてのプレイブックの一覧が表示されます。

1. 特定のプレイブックの行で **[実行]** をクリックしてトリガーします。

1. **[実行]** タブを選択すると、このアラートに対してプレイブックが実行されたすべての回の一覧が表示されます。 完了したばかりの実行がこの一覧に表示されるまでには数秒かかることがあります。

1. 特定の実行をクリックすると、Logic Apps で完全な実行ログが開きます。

### <a name="run-a-playbook-manually-on-an-incident"></a>インシデントに対して手動でプレイブックを実行する

まだサポートされていません。 <!--make this a note instead? -->

## <a name="manage-your-playbooks"></a>プレイブックを管理する

**[プレイブック]** タブには、現在 Azure に表示されているサブスクリプションによってフィルター処理された、アクセス権を持つすべてのプレイブックの一覧が表示されます。 サブスクリプション フィルターは、グローバル ページ ヘッダーの **[ディレクトリ + サブスクリプション]** メニューから使用できます。

プレイブック名をクリックすると、Logic Apps 内のプレイブックのメイン ページが表示されます。 **[状態]** 列は、有効になっているか無効になっているかを示しています。

**[トリガーの種類]** は、このプレイブックを開始する Logic Apps トリガーを表します。

| トリガーの種類 | プレイブック内のコンポーネントの種類を示す |
|-|-|
| **Azure Sentinel のインシデントまたはアラート** | プレイブックは、Sentinel トリガー (アラート、インシデント) のいずれかによって開始されます |
| **Azure Sentinel アクションの使用** | プレイブックは Sentinel 以外のトリガーで開始されますが、Azure Sentinel アクションが使用されます |
| **その他** | プレイブックには、Sentinel コンポーネントは含まれていません |
| **初期化されていない** | プレイブックは作成されていますが、コンポーネント (トリガーまたはアクション) は含まれていません。 |
|

プレイブックの [ロジック アプリ] ページでは、実行されたすべての回のログ、および結果 (成功または失敗、その他の詳細) など、プレイブックに関する詳細情報を確認できます。 適切なアクセス許可がある場合は、Logic Apps デザイナーを入力し、プレイブックを直接編集することもできます。

### <a name="api-connections"></a>API 接続

API 接続は、Logic Apps を他のサービスに接続するために使用されます。 Logic Apps コネクタに対する新しい認証が行われるたびに、**API 接続** の種類の新しいリソースが作成され、サービスへのアクセスを構成しているときに提供された情報が含まれます。

すべての API 接続を表示するには、Azure portal のヘッダー検索ボックスに「*API 接続*」と入力します。 次の列に注意してください。

- 表示名 - 接続を作成するたびに指定する "わかりやすい" 名前。
- 状態 - 接続の状態 (エラー、接続済み) を示します。
- リソース グループ - API 接続は、プレイブック (Logic Apps) リソースのリソース グループに作成されます。

API 接続を表示するもう 1 つの方法は、 **[すべてのリソース]** ブレードにアクセスし、 *[API 接続]* の種類でフィルター処理することです。 この方法を使用すると、一度に複数の接続の選択、タグ付け、削除を実行できます。

既存の接続の承認を変更するには、接続リソースを入力し、 **[API 接続の編集]** を選択します。

## <a name="next-steps"></a>次のステップ

- [チュートリアル: プレイブックを使用して Azure Sentinel で脅威への対応を自動化する](tutorial-respond-threats-playbook.md)
