---
title: Power Automate と Power Apps からロジック アプリを呼び出す
description: ロジック アプリをコネクタとしてエクスポートすることで、Microsoft Power Automate フローからロジック アプリを呼び出します。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: b402dab4c6e94a7634e11f0330b5379315e43abf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91762426"
---
# <a name="call-logic-apps-from-power-automate-and-power-apps"></a>Power Automate と Power Apps からロジック アプリを呼び出す

Microsoft Power Automate と Microsoft Power Apps からロジック アプリを呼び出すには、ロジック アプリをコネクタとしてエクスポートします。 Power Automate または Power Apps 環境でロジック アプリをカスタム コネクタとして公開すると、そこにあるフローからロジック アプリを呼び出すことができます。

Power Automate または Power の代わりに、Logic Apps にフローを移行する場合は、「[Power Automate からフローをエクスポートして Azure Logic Apps にデプロイする](export-from-microsoft-flow-logic-app-template.md)」を参照してください。

> [!NOTE]
> すべての Power Automate コネクタが Azure Logic Apps で使用できるわけではありません。 Azure Logic Apps に同等のコネクタを持つ Power Automate フローのみを移行できます。 たとえば、Button トリガー、Approval コネクタ、および Notification コネクタは Power Automate に固有です。 現時点では、Power Automate の OpenAPI ベースのフローは、ロジック アプリ テンプレートとしてエクスポートおよびデプロイではサポートされていません。
>
> * Logic Apps に同等のコネクタを持たない Power Automate コネクタを見つけるには、[Power Automate コネクタ](/connectors/connector-reference/connector-reference-powerautomate-connectors)に関する記事を参照してください。
>
> * Power Automate に同等のコネクタを持たない Logic Apps コネクタを見つけるには、[Logic Apps コネクタ](/connectors/connector-reference/connector-reference-powerautomate-connectors)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* Power Automate または Power Apps ライセンス。

* エクスポートする要求トリガーが含まれるロジック アプリ。

* ロジック アプリを呼び出す Power Automate または Power Apps のフロー。

## <a name="export-your-logic-app-as-a-custom-connector"></a>ロジック アプリをカスタム コネクタとしてエクスポートする

Power Automate または Power Apps からロジック アプリを呼び出すには、まず、ロジック アプリをカスタム コネクタとしてエクスポートする必要があります。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure portal の検索ボックスに、「`Logic Apps`」と入力します。 結果の **[サービス]** の下にある **[Logic Apps]** を選択します。

1. エクスポートするロジック アプリを選択します。

1. ロジック アプリのメニューで、 **[エクスポート]** を選択します。

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/export-logic-app.png" alt-text="Azure portal のロジック アプリのページのスクリーンショット。[エクスポート] ボタンが選択されたメニューが表示されています。":::

1. **[エクスポート]** ウィンドウで、 **[名前]** に、ロジック アプリへのカスタム コネクタの名前を入力します。 **[環境]** リストから、ロジック アプリの呼び出し元となる Power Automate または Power Apps 環境を選択します。 終了したら、 **[OK]** を選択します。

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/export-logic-app2.png" alt-text="カスタム コネクタ名と環境に必要なフィールドをが示されている、ロジック アプリの [エクスポート] ウィンドウのスクリーンショット。":::

1. ロジック アプリが正常にエクスポートされたことを確認するには、[通知] ウィンドウを確認します。

### <a name="exporting-errors"></a>エクスポート エラー

ここでは、ロジック アプリをカスタム コネクタとしてエクスポートするときに発生する可能性があるエラーと、推奨される解決策を示します。

* **環境を取得できませんでした。アカウントが Power Automate 用に構成されていることを確認してから、もう一度お試しください。** :Azure アカウントに Power Automate プランがあることを確認します。

* **現在のロジック アプリはエクスポートできません。エクスポートするには、要求トリガーのあるロジック アプリを選択します。** :ロジック アプリが [要求トリガー](./logic-apps-workflow-actions-triggers.md#request-trigger)で開始されていることを確認します。

## <a name="connect-to-your-logic-app-from-power-automate"></a>Power Automate からロジック アプリに接続する

Power Automate フローでエクスポートしたロジック アプリに接続するには、次の手順に従います。

1. [Power Automate](https://flow.microsoft.com) にサインインします。

1. **[Power Automate]** のホーム ページにあるメニューで、 **[マイ フロー]** を選択します。

1. **[フロー]** ページで、ロジック アプリに接続するフローを選択します。

1. フロー ページのメニューから、 **[編集]** を選択します。

1. フロー エディターで **[&#43; 新しいステップ]** を選択します。

1. **[アクションを選択する]** の検索ボックスに、自分のロジック アプリ コネクタの名前を入力します。 環境内のカスタム コネクタのみを表示する必要がある場合には、 **[カスタム]** タブを選択して結果をフィルター処理します。

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/power-automate-custom-connector-action.png" alt-text="Power Automate のフロー エディターのスクリーンショット。カスタム コネクタと使用可能なアクションに新しいステップが追加されているところが示されています。":::

1. ロジック アプリ コネクタで実行するアクションを選択します。 

1. アクションによってロジック アプリ コネクタに渡される情報を指定します。

1. 変更を保存するには、Power Automate のエディター メニューの **[保存]** を選択します。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Logic Apps サービスで、エクスポートしたロジック アプリを見つけます。

1. ロジック アプリが、Power Automate フローで指定したとおりに動作することを確認します。

## <a name="delete-logic-app-connector-from-power-automate"></a>Power Automate からロジック アプリ コネクタを削除する

1. [Power Automate](https://flow.microsoft.com) にサインインします。

1. **Power Automate** のホーム ページで、メニューから **[データ]** &gt; **[カスタム コネクタ]** の順に選択します。

1. 一覧からカスタム コネクタを見つけて、省略記号 ( **...** ) ボタン &gt; **[削除]** の順に選択します。

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/delete-custom-connector.png" alt-text="ロジック アプリのカスタム コネクタ管理ボタンが表示されている、Power Automate の [カスタム コネクタ] ページのスクリーンショット。":::

1. 削除を確定するには、 **[OK]** を選択します。

## <a name="connect-to-your-logic-app-from-power-apps"></a>Power Apps からロジック アプリに接続する

Power Apps フローでエクスポートしたロジック アプリに接続するには、次の手順に従います。

1. [Power Apps](https://powerapps.microsoft.com/) にサインインします。

1. **Power Apps** のホーム ページで、メニューから **[フロー]** を選択します。

1. **[フロー]** ページで、ロジック アプリに接続するフローを選択します。

1. フローのページで、フローのメニューにある **[編集]** を選択します。

1. フロー エディターで **[&#43; 新しいステップ]** ボタンを選択します。

1. 新しいステップの **[アクションを選択する]** で、検索ボックスにロジック アプリ コネクタの名前を入力します。 環境内のカスタム コネクタのみを表示する必要がある場合には、 **[カスタム]** タブを使用して結果をフィルター処理します。

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/power-apps-custom-connector-action.png" alt-text="Power Apps のフロー エディターのスクリーンショット。カスタム コネクタと使用可能なアクションに新しいステップが追加されているところが示されています。":::

1. コネクタで実行するアクションを選択します。 

1. アクションによってどの情報をロジック アプリ コネクタに渡すかを構成します。

1. Power Apps のエディター メニューにある **[保存]** を選択して変更を保存します。 

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Logic Apps サービスで、エクスポートしたロジック アプリを見つけます。

1. ロジック アプリが Power Apps のフローで指定したとおりに機能していることを確認します。

## <a name="delete-logic-app-connector-from-power-apps"></a>Power Apps からロジック アプリ コネクタを削除する

1. [Power Apps](https://powerapps.microsoft.com) にサインインします。

1. **Power Apps** のホーム ページで、メニューから **[データ]** &gt; **[カスタム コネクタ]** の順に選択します。

1. 一覧からカスタム コネクタを見つけて、省略記号 ( **...** ) ボタン &gt; **[削除]** の順に選択します。

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/delete-custom-connector.png" alt-text="ロジック アプリのカスタム コネクタ管理ボタンが表示されている、Power Apps の [カスタム コネクタ] ページのスクリーンショット。":::

1. 削除を確定するには、 **[OK]** を選択します。

## <a name="next-steps"></a>次のステップ

* [Azure Logic Apps のコネクタ](../connectors/apis-list.md)の詳細について学習します。
* [Azure Logic Apps](../logic-apps/logic-apps-overview.md) について学習します
