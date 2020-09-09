---
title: Azure Security Center のワークフローの自動化 | Microsoft Docs
description: Azure Security Center でワークフローを作成して自動化する方法について説明します。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: memildin
ms.openlocfilehash: ff358f3c90edfcdabb5a1225ad1efa122818b070
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89375263"
---
# <a name="workflow-automation"></a>ワークフローの自動化

すべてのセキュリティ プログラムには、インシデント対応のための複数のワークフローが含まれています。 これらのプロセスには、直接の利害関係者への通知、変更管理プロセスの開始、および特定の修復手順の適用が含まれます。 これらのプロシージャの手順をできるだけ多く自動化することがセキュリティの専門家によって推奨されています。 自動化によってオーバーヘッドが削減されます。 また、迅速かつ一貫した方法で、定義済みの要件に従ってプロセスの手順が実行されるようにすることで、セキュリティを向上させることもできます。

この記事では、Azure Security Center のワークフローの自動化機能について説明します。 この機能では、セキュリティ アラートと推奨事項が出されたときにロジック アプリをトリガーできます。 たとえば、アラートが発生したときに Security Center から特定のユーザーに電子メールが送信されるようにすることができます。 また、[Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) を使用してロジック アプリを作成する方法についても説明します。

> [!NOTE]
> サイドバーでプレイブック (プレビュー) ビューを以前に使用していた場合は、新しいワークフローの自動化のページに、拡張された機能と共に同じ機能が表示されます。



## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般公開|
|価格:|Free レベル|
|必要なロールとアクセス許可:|リソース グループ上での**セキュリティ管理者ロール**または**所有者**<br>ターゲット リソースに対する書き込みアクセス許可も必要になります<br><br>Azure Logic Apps のワークフローを操作するには、次のロジック アプリのロール/アクセス許可も必要です。<br> - [ロジック アプリのオペレーター](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator)のアクセス許可が必要。または、ロジック アプリの読み取り/トリガーのアクセス権 (このロールでは、ロジック アプリを作成したり編集したりすることはできません。既存のものを*実行*するだけです)<br> - [ロジック アプリの共同作成者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor)のアクセス許可は、ロジック アプリの作成と変更に必要です<br>ロジック アプリのコネクタを使用する場合は、それぞれのサービス (たとえば、Outlook/Teams/Slack のインスタンス) にサインインするために追加の資格情報が必要になることがあります。|
|クラウド:|![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![はい](./media/icons/yes-icon.png) China Gov、その他の Gov|
|||



## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>ロジック アプリを作成し、自動的に実行するタイミングを定義する 

1. Security Center のサイドバーで、 **[ワークフローの自動化]** を選択します。

    [![ワークフローの自動化の一覧](media/workflow-automation/list-of-workflow-automations.png)](media/workflow-automation/list-of-workflow-automations.png#lightbox)

    このページで、新しい自動化ルールを作成したり、既存のものを有効化、無効化、または削除したりすることができます。

1. 新しいワークフローを定義するには、 **[Add workflow automation]\(ワークフローの自動化の追加\)** をクリックします。 

    新しい自動化のオプションを含むウィンドウが表示されます。 ここで、以下を入力できます。
    1. 自動化の名前と説明。
    1. この自動ワークフローを開始するトリガー。 たとえば、"SQL" を含むセキュリティ警告が生成されたときにロジック アプリを実行することができます。
    1. トリガー条件が満たされたときに実行されるロジック アプリ。 

        [![ワークフローの自動化の一覧](media/workflow-automation/add-workflow.png)](media/workflow-automation/add-workflow.png#lightbox)

1. [Actions]\(アクション\) セクションで、 **[Create a new one]\(新規作成する\)** をクリックして、ロジック アプリの作成プロセスを開始します。

    Azure Logic Apps が表示されます。

    [![新しいロジック アプリの作成](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. 名前、リソース グループ、および場所を入力し、 **[Create]\(作成\)** をクリックします。

1. 新しいロジック アプリでは、セキュリティ カテゴリの組み込みの定義済みテンプレートから選択できます。 または、このプロセスがトリガーされたときに発生するイベントのカスタム フローを定義することもできます。

    ロジック アプリ デザイナーでは、Security Center コネクタからの次のトリガーがサポートされています。

    * **When an Azure Security Center Recommendation is created or triggered** (Azure Security Center の推奨事項が作成またはトリガーされたとき)
    * **When an Azure Security Center Alert is created or triggered** (Azure Security Center のアラートが作成またはトリガーされたとき) 
    
    > [!TIP]
    > トリガーをカスタマイズして、対象となる重大度レベルのアラートのみが関連付けられるようにすることができます。
    
    > [!NOTE]
    > レガシー トリガーの [When a response to an Azure Security Center alert is triggered]\(Azure Security Center アラートへの応答がトリガーされるとき\) を使用している場合、ロジック アプリはワークフローの自動化機能によって起動されません。 代わりに、前述のいずれかのトリガーを使用してください。 

    [![サンプル ロジック アプリ](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. ロジック アプリを定義したら、ワークフローの自動化の定義ウィンドウに戻ります ([Add workflow automation]\(ワークフローの自動化の追加\))。 **[Refresh]\(最新の情報に更新\)** をクリックして、新しいロジック アプリが選択可能であることを確認します。

    ![更新](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. ロジック アプリを選択し、自動化を保存します。 ロジック アプリ ドロップダウンには、Logic Apps とそれをサポートする前述の Security Center コネクタのみが表示されることにご注意ください。


## <a name="manually-trigger-a-logic-app"></a>ロジック アプリを手動でトリガーする

セキュリティのアラートまたは推奨事項を表示しているときに、ロジック アプリを手動で実行することもできます。

ロジック アプリを手動で実行するには、アラートまたは推奨事項を開き、 **[ロジック アプリのトリガー]** をクリックします。

[![ロジック アプリを手動でトリガーする](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)

## <a name="data-types-schemas"></a>データ型のスキーマ

ロジック アプリ インスタンスに渡されたセキュリティアラートまたはおすすめ候補イベントの未加工イベントスキーマを表示するには、[ワークフロー オートメーション データ型スキーマ](https://aka.ms/ASCAutomationSchemas) を参照してください。 これは、上記で説明した、 Security Center の組み込みロジック アプリ コネクタを使用していない場合に便利です。この場合には、ロジックアプリの汎用 HTTP コネクタを使用します。すなわち、イベントの JSON スキーマを使用して、必要に応じて手動で解析することができます。

## <a name="next-steps"></a>次のステップ

この記事では、ロジック アプリの作成、Security Center での実行の自動化、および手動実行について学習しました。 

その他の関連資料については、以下を参照してください。 

- [ワークフローの自動化を使用してセキュリティ対応を自動化する方法に関する Microsoft Learn モジュール](https://docs.microsoft.com/learn/modules/resolve-threats-with-azure-security-center/)
- [Azure Security Center でのセキュリティに関する推奨事項](security-center-recommendations.md)
- [Security alerts in Azure Security Center](security-center-alerts-overview.md)
- [Azure Logic Apps の概要](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [Logic Apps コネクタ](https://docs.microsoft.com/connectors/)
- [ワークフロー オートメーション データ型スキーマ](https://aka.ms/ASCAutomationSchemas)
