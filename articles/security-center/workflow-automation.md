---
title: Azure Security Center のワークフローの自動化 | Microsoft Docs
description: Azure Security Center でワークフローを作成して自動化する方法について説明します。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: 6268ff6cfb3d3e856edcd8f84af930d52f4cf9d3
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096175"
---
# <a name="automate-responses-to-security-center-triggers"></a>Security Center のトリガーへの応答を自動化する

すべてのセキュリティ プログラムには、インシデント対応のための複数のワークフローが含まれています。 これらのプロセスには、直接の利害関係者への通知、変更管理プロセスの開始、および特定の修復手順の適用が含まれます。 これらのプロシージャの手順をできるだけ多く自動化することがセキュリティの専門家によって推奨されています。 自動化によってオーバーヘッドが削減されます。 また、迅速かつ一貫した方法で、定義済みの要件に従ってプロセスの手順が実行されるようにすることで、セキュリティを向上させることもできます。

この記事では、Azure Security Center のワークフローの自動化機能について説明します。 この機能を使用すると、セキュリティ アラートや推奨事項、および規制コンプライアンスへの変更があったときに Logic Apps をトリガーできます。 たとえば、アラートが発生したときに Security Center から特定のユーザーに電子メールが送信されるようにすることができます。 また、[Azure Logic Apps](../logic-apps/logic-apps-overview.md) を使用してロジック アプリを作成する方法についても説明します。


## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般提供 (GA)|
|価格:|Free|
|必要なロールとアクセス許可:|リソース グループ上での **セキュリティ管理者ロール** または **所有者**<br>ターゲット リソースに対する書き込みアクセス許可も必要になります<br><br>Azure Logic Apps のワークフローを操作するには、次のロジック アプリのロール/アクセス許可も必要です。<br> - [ロジック アプリのオペレーター](../role-based-access-control/built-in-roles.md#logic-app-operator)のアクセス許可が必要。または、ロジック アプリの読み取り/トリガーのアクセス権 (このロールでは、ロジック アプリを作成したり編集したりすることはできません。既存のものを *実行* するだけです)<br> - [ロジック アプリの共同作成者](../role-based-access-control/built-in-roles.md#logic-app-contributor)のアクセス許可は、ロジック アプリの作成と変更に必要です<br>ロジック アプリのコネクタを使用する場合は、それぞれのサービス (たとえば、Outlook/Teams/Slack のインスタンス) にサインインするために追加の資格情報が必要になることがあります。|
|クラウド:|![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![Yes](./media/icons/yes-icon.png) ナショナル/ソブリン (US Gov、China Gov、その他の Gov)|
|||



## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>ロジック アプリを作成し、自動的に実行するタイミングを定義する 

1. Security Center のサイドバーで、 **[ワークフローの自動化]** を選択します。

    :::image type="content" source="./media/workflow-automation/list-of-workflow-automations.png" alt-text="ワークフローの自動化の一覧":::

    このページで、新しい自動化ルールを作成したり、既存のものを有効化、無効化、または削除したりすることができます。

1. 新しいワークフローを定義するには、 **[Add workflow automation]\(ワークフローの自動化の追加\)** をクリックします。 

    新しい自動化のオプションを含むウィンドウが表示されます。 ここで、以下を入力できます。
    1. 自動化の名前と説明。
    1. この自動ワークフローを開始するトリガー。 たとえば、"SQL" を含むセキュリティ警告が生成されたときにロジック アプリを実行することができます。

        > [!NOTE]
        > たとえば、**SQL データベースの脆弱性評価の結果を修復する必要がある** ことを示す "サブの推奨事項" という推奨事項がトリガーに含まれている場合は、セキュリティに関する新しい結果が得られるたびにロジック アプリがトリガーされることはありません。親の推奨事項の状態が変更された場合のみトリガーされます。

    1. トリガー条件が満たされたときに実行されるロジック アプリ。 

        :::image type="content" source="./media/workflow-automation/add-workflow.png" alt-text="ワークフロー自動化ウィンドウの追加":::

1. [Actions]\(アクション\) セクションで、 **[Create a new one]\(新規作成する\)** をクリックして、ロジック アプリの作成プロセスを開始します。

    Azure Logic Apps が表示されます。

    [![新しいロジック アプリの作成](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. 名前、リソース グループ、および場所を入力し、 **[Create]\(作成\)** をクリックします。

1. 新しいロジック アプリでは、セキュリティ カテゴリの組み込みの定義済みテンプレートから選択できます。 または、このプロセスがトリガーされたときに発生するイベントのカスタム フローを定義することもできます。

    > [!TIP]
    > ロジック アプリでは、パラメーターは、独自のフィールドではなく、文字列の一部としてコネクタに含まれることがあります。 パラメーターを抽出する方法の例については、「[Azure Security Center のワークフロー自動化の構築時にロジック アプリのパラメーターを操作する](https://techcommunity.microsoft.com/t5/azure-security-center/working-with-logic-app-parameters-while-building-azure-security/ba-p/1342121)」の手順 #14 を参照してください。

    ロジック アプリ デザイナーでは、Security Center の次のトリガーがサポートされています。

    - **Azure Security Center 推奨事項が作成またはトリガーされたとき** - ロジック アプリが非推奨または置換された推奨事項に依存している場合、自動化の動作は停止し、トリガーの更新が必要となります。 推奨事項の変更を追跡するには、[Azure Security Center リリース ノート](release-notes.md)に関するページを参照してください。

    - **Azure Security Center アラートが作成またはトリガーされたとき** - トリガーをカスタマイズして、対象となる重大度レベルのアラートのみが関連付けられるようにすることができます。
    
    - **Security Center の規制コンプライアンス評価が作成またはトリガーされたとき** - 規制コンプライアンス評価の更新に基づくトリガーの自動化。

    > [!NOTE]
    > レガシ トリガーである [Azure Security Center アラートへの応答がトリガーされるとき] を使用している場合、ロジック アプリはワークフローの自動化機能では起動されません。 代わりに、前述のいずれかのトリガーを使用してください。 

    [![サンプル ロジック アプリ](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. ロジック アプリを定義したら、ワークフローの自動化の定義ウィンドウに戻ります ([ワークフロー自動化の追加])。 **[Refresh]\(最新の情報に更新\)** をクリックして、新しいロジック アプリが選択可能であることを確認します。

    ![更新](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. ロジック アプリを選択し、自動化を保存します。 ロジック アプリ ドロップダウンには、Logic Apps とそれをサポートする前述の Security Center コネクタのみが表示されることにご注意ください。


## <a name="manually-trigger-a-logic-app"></a>ロジック アプリを手動でトリガーする

セキュリティのアラートまたは推奨事項を表示しているときに、ロジック アプリを手動で実行することもできます。

ロジック アプリを手動で実行するには、アラートまたは推奨事項を開き、 **[ロジック アプリのトリガー]** をクリックします。

[![ロジック アプリを手動でトリガーする](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)


## <a name="configure-workflow-automation-at-scale-using-the-supplied-policies"></a>提供されているポリシーを使用してワークフローの自動化を大規模に構成する

組織の監視とインシデント対応プロセスを自動化すると、セキュリティ インシデントの調査と軽減にかかる時間を大幅に短縮できます。

組織全体に自動化の構成をデプロイするには、後述の提供されている Azure Policy の "DeployIfNotExist" ポリシーを使用して、ワークフローの自動化手順を作成して構成します。

[ワークフローの自動化テンプレート](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)を使用して開始するには、こちらを参照してください。

これらのポリシーを実装するには、次の手順に従います。

1. 次の表から、適用するポリシーを選択します。

    |目標  |ポリシー  |ポリシー ID  |
    |---------|---------|---------|
    |セキュリティ アラートのワークフローの自動化|[Azure Security Center アラートに対してワークフローの自動化をデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
    |セキュリティに関する推奨事項のワークフローの自動化|[Azure Security Center 推奨事項に対してワークフローの自動化をデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
    |規制コンプライアンスの変化に関するワークフローの自動化|[Azure Security Center の法令遵守のためにワークフローの自動化をデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
    ||||

    > [!TIP]
    > これらは、Azure Policy を検索して見つけることもできます。
    > 1. Azure Policy を開きます。
    > :::image type="content" source="./media/continuous-export/opening-azure-policy.png" alt-text="Azure Policy へのアクセス":::
    > 2. Azure Policy のメニューから **[定義]** を選択し、名前で検索します。 

1. 関連する Azure Policy ページで、 **[割り当てる]** を選択します。
    :::image type="content" source="./media/workflow-automation/export-policy-assign.png" alt-text="Azure Policy の割り当て":::

1. 各タブを開き、必要に応じてパラメーターを設定します。
    1. **[基本]** タブで、ポリシーのスコープを設定します。 集中管理を使用するには、ワークフローの自動化の構成を使用するサブスクリプションが含まれている管理グループにポリシーを割り当てます。 
    1. **[パラメーター]** タブで、リソース グループとデータ型の詳細を設定します。 
        > [!TIP]
        > 各パラメーターには、使用可能なオプションを説明するツールヒントがあります。
        >
        > Azure Policy のパラメーター タブ (1) には、Security Center のワークフローの自動化ページ (2) に似た構成オプションへのアクセスが提供されています。
        > :::image type="content" source="./media/workflow-automation/azure-policy-next-to-workflow-automation.png" alt-text="ワークフローの自動化のパラメーターを Azure Policy と比較する" lightbox="./media/workflow-automation/azure-policy-next-to-workflow-automation.png":::

    1. 必要に応じて、この割り当てを既存のサブスクリプションに適用するには、 **[修復]** タブを開き、修復タスクを作成するためのオプションを選択します。

1. 概要ページを確認し、 **[作成]** を選択します。


## <a name="data-types-schemas"></a>データ型のスキーマ

ロジック アプリ インスタンスに渡されたセキュリティアラートまたはおすすめ候補イベントの未加工イベントスキーマを表示するには、[ワークフロー オートメーション データ型スキーマ](https://aka.ms/ASCAutomationSchemas) を参照してください。 これは、上記で説明した、 Security Center の組み込みロジック アプリ コネクタを使用していない場合に便利です。この場合には、ロジックアプリの汎用 HTTP コネクタを使用します。すなわち、イベントの JSON スキーマを使用して、必要に応じて手動で解析することができます。


## <a name="faq-for-workflow-automation"></a>ワークフローの自動化に関する FAQ

### <a name="does-workflow-automation-support-any-business-continuity-or-disaster-recovery-bcdr-scenarios"></a>ワークフローの自動化では、ビジネス継続性またはディザスター リカバリー (BCDR) のシナリオはサポートされていますか。

ターゲット リソースに停止やその他の障害が発生する BCDR シナリオに対して環境を準備する場合は、Azure Event Hubs、Log Analytics ワークスペース、ロジック アプリのガイドラインに従ってバックアップを確立することで、データの損失を防ぐ必要があります。

アクティブな自動化ごとに、同じ (無効な) 自動化を作成し、別の場所に保存することをお勧めします。 障害が発生した場合は、これらのバックアップの自動化を有効にし、通常の操作を継続することができます。

詳細については、「[Azure Logic Apps の事業継続とディザスター リカバリー](../logic-apps/business-continuity-disaster-recovery-guidance.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

この記事では、ロジック アプリの作成、Security Center での実行の自動化、および手動実行について学習しました。

関連資料については、以下を参照してください。 

- [ワークフローの自動化を使用してセキュリティ対応を自動化する方法に関する Microsoft Learn モジュール](/learn/modules/resolve-threats-with-azure-security-center/)
- [Azure Security Center でのセキュリティに関する推奨事項](security-center-recommendations.md)
- [Security alerts in Azure Security Center](security-center-alerts-overview.md)
- [Azure Logic Apps の概要](../logic-apps/logic-apps-overview.md)
- [Azure Logic Apps のコネクタ](../connectors/apis-list.md)
- [ワークフロー オートメーション データ型スキーマ](https://aka.ms/ASCAutomationSchemas)