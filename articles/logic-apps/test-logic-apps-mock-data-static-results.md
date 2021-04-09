---
title: モック データを使用してロジック アプリをテストする
description: 運用環境に影響を与えずにモック データでロジック アプリをテストするための静的な結果を設定する
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/13/2019
ms.openlocfilehash: 711d753203aeaeba50cea692053a37fcab2e9c7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93027705"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>静的な結果を設定してモック データでロジック アプリをテストする

ロジック アプリをテストするときに、さまざまな理由からアプリ、サービス、およびシステムを実際に呼び出したり、それらにアクセスする準備ができていないことがあります。 通常、これらのシナリオでは、別の条件パスを実行したり、エラーを強制したり、特定のメッセージ応答本文を提供したり、いくつかの手順をスキップしてみることも必要になる場合があります。 ロジック アプリでアクションの静的な結果を設定することにより、そのアクションからの出力データをモックできます。 アクションで静的な結果を有効にしてもアクションは実行されませんが、代わりにモック データが返されます。

たとえば、Outlook 365 メール送信アクションの静的な結果を設定した場合、Logic Apps エンジンは、Outlook を呼び出して電子メールを送信するのではなく、静的な結果として指定したモック データを返すだけです。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、<a href="https://azure.microsoft.com/free/" target="_blank">無料の Azure アカウントにサインアップ</a>してください。

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

* 静的な結果を設定するロジック アプリ

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>静的な結果を設定する

1. まだ開いていない場合は、[Azure portal](https://portal.azure.com) で Logic Apps デザイナーのロジック アプリを開きます。

1. 静的な結果を設定するアクションで、次の手順に従います。 

   1. アクションの右上隅で、省略記号 ( *...* ) ボタンを選択し、たとえば次のように **設定な結果** を選択します。

      ![「静的な結果」>「静的な結果を有効にする」の順に選択する](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. **[静的な結果を有効にする]** を選択します。 必須 (*) プロパティには、アクションの応答として返す必要のあるモック出力値を指定します。

      たとえば、HTTP アクションの必須プロパティは次のとおりです。

      | プロパティ | 説明 |
      |----------|-------------|
      | **状態** | 返されるアクションの状態 |
      | **状態コード** | 返される特定の状態コード |
      | **ヘッダー** | 返されるヘッダーの内容 |
      |||

      ![[静的な結果を有効にする] を選択する](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      JavaScript Object Notation (JSON) 形式でモック データを入力するには、 **[JSON モードに切り替える]** (![[JSON モードに切り替える]を選択](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)) を選択します。

   1. 省略可能なプロパティについては、 **[省略可能なフィールドを選択する]** の一覧を開いて、モックするプロパティを選択します。

      ![省略可能なプロパティを選択する](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. 保存する準備ができたら、 **[完了]** を選択します。

   アクションの右上隅では、キャプション バーにテスト ビーカー アイコン (![静的な結果のアイコン](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)) が表示されるようになり、静的な結果が有効になっていることを示します。

   ![有効になった静的な結果を示すアイコン](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   モック データを使用する以前の実行を検索するには、このトピックで後述する「[静的な結果を使用した実行を見つける](#find-runs-mock-data)」を参照してください。

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>以前の出力を再利用する

モック出力として再利用できる出力を持つ以前の実行がロジック アプリにある場合、その実行の出力をコピーして貼り付けることができます。

1. まだ開いていない場合は、[Azure portal](https://portal.azure.com) で Logic Apps デザイナーのロジック アプリを開きます。

1. ロジック アプリのメイン メニューで、 **[概要]** を選択します。

1. **[実行の履歴]** セクションで、目的のロジック アプリの実行を選択します。

1. ロジック アプリのワークフローで、目的の出力を持つアクションを見つけて展開します。

1. **[未加工出力の表示]** リンクを選択します。

1. 完全な JavaScript Object Notation (JSON) オブジェクトか、出力セクションやヘッダー セクションなどの使用する特定のサブセクションのどちらかをコピーします。

1. [[静的な結果を設定する]](#set-up-static-results) でアクションに応じた **[静的な結果]** ボックスを開く手順に従います。

1. **[静的な結果]** ボックスが開いたら、次のどちらかの手順を選択します。

   * 完全な JSON オブジェクトを貼り付けるには、 **[JSON モードに切り替える]** (![[JSON モードに切り替える] を選択](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)) を選択します。

     ![完全なオブジェクトに対して [JSON モードに切り替える] を選択する](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * JSON セクションだけを貼り付けるには、たとえば次のように、そのセクションのラベルの横にある、そのセクションに応じた **[JSON モードに切り替える]** を選択します。

     ![出力に対して [JSON モードに切り替える] を選択する](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. JSON エディターで、以前にコピーした JSON を貼り付けます。

   ![JSON モード](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. 完了したら、 **[完了]** を選択します。 または、デザイナーに戻るには、 **[エディター モードを切り替える]** (![[エディター モードを切り替える] を選択](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png)) を選択します。

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>静的な結果を使用した実行を見つける

ロジック アプリの実行履歴から、アクションが静的な結果を使用した実行を特定します。 これらの実行を見つけるには、以下の手順に従います。

1. ロジック アプリのメイン メニューで、 **[概要]** を選択します。 

1. 右ウィンドウの **[実行の履歴]** の下で **[静的な結果]** 列を見つけます。 

   結果を伴ったアクションを含むどの実行でも、たとえば次のように、 **[静的な結果]** 列が **[有効]** に設定されています。

   ![[実行の履歴] - [静的な結果] 列](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. 静的な結果を使用したアクションを表示するには、 **[静的な結果]** 列が **[有効]** に設定されている目的の実行を選択します。

   静的な結果を使用したアクションには、たとえば次のように、テスト ビーカー (![静的な結果のアイコン](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)) アイコンが表示されます。

   ![[実行の履歴] - 静的な結果を使用したアクション](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>静的な結果を無効にする

静的な結果をオフにすると、前回の設定の値は破棄されなくなります。 そのため、次回静的な結果をオンにしたときに、以前の値を使用し続けることができます。

1. 静的な出力を無効にするアクションを探します。 アクションの右上隅で、テスト ビーカー アイコン (![静的な結果のアイコン](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)) を選択します。

   ![テスト ビーカー アイコンを選択できる HTTP アクションを示しているスクリーンショット。](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. **[静的な結果の無効化]**  >  **[完了]** の順に選択します。

   ![選択できる [静的な結果の無効化] オプションを示しているスクリーンショット。](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="reference"></a>リファレンス

基になるワークフロー定義でのこの設定の詳細については、「[Static results - Schema reference for Workflow Definition Language (静的結果 - ワークフロー定義言語のスキーマ参照)](../logic-apps/logic-apps-workflow-definition-language.md#static-results)」と「[runtimeConfiguration.staticResult - Runtime configuration settings (runtimeConfiguration.staticResult - ランタイム構成設定)](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-configuration-settings)」を参照してください

## <a name="next-steps"></a>次のステップ

* [Azure Logic Apps](../logic-apps/logic-apps-overview.md) について学習します