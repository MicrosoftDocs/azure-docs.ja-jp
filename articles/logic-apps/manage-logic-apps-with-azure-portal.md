---
title: Azure portal でロジック アプリを管理する
description: Azure portal を使用して、ロジック アプリを編集、有効化、無効化、または削除します。
services: logic-apps
ms.suite: integration
author: lauradolan
ms.author: ladolan
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 07/20/2020
ms.openlocfilehash: d50f577a7170982be004cc8957114f79675fbc6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96006100"
---
# <a name="manage-logic-apps-in-the-azure-portal"></a>Azure portal でロジック アプリを管理する

ロジック アプリは、[Azure portal](https://portal.azure.com) または [Visual Studio](manage-logic-apps-with-visual-studio.md) を使用して管理できます。 この記事では、Azure portal で、ロジック アプリを編集、有効化、無効化、または削除する方法について説明します。 Azure Logic Apps を初めて使用する場合は、「[Azure Logic Apps とは](logic-apps-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* 既存のロジック アプリ。 Azure portal を使用してロジック アプリを作成する方法を学習するには、「[クイック スタート: Azure Logic Apps を使用して初めてのワークフローを作成する - Azure portal](quickstart-create-first-logic-app-workflow.md)」を参照してください。

<a name="find-logic-app"></a>

## <a name="find-your-logic-apps"></a>ロジック アプリを検索する

ロジック アプリを検索して開くには、次の手順を行います。

1. Azure アカウントで [Azure Portal](https://portal.azure.com) にサインインします。

1. Azure の検索バーに「`logic apps`」と入力して **[Logic Apps]** を選択します。

   ![Azure portal メニューのスクリーンショット。検索バーで Logic Apps が検索されて選択されている。](./media/manage-logic-apps-with-azure-portal/find-select-logic-apps.png)

1. **[Logic Apps]** ページで、管理するロジック アプリを検索して選びます。

   ロジック アプリの **[概要]** ペインが開いたら、次の方法で **[Logic Apps]** ページに表示される一覧をフィルター処理できます。

   * ロジック アプリを名前で検索する
   * ロジック アプリをサブスクリプション、リソース グループ、場所、タグでフィルター処理する
   * ロジック アプリをリソース グループ、種類、サブスクリプション、場所でグループ化する

## <a name="view-logic-app-properties"></a>ロジック アプリのプロパティを表示する

1. Azure portal で、[ご利用のロジック アプリを検索して開きます](#find-logic-app)。

1. ロジック アプリのメニューの **[設定]** の下で、 **[プロパティ]** を選択します。

1. **[プロパティ]** ペインで、ロジック アプリに関する次の情報を表示してコピーできます。

   * **名前**
   * **リソース ID**
   * **リソース グループ**
   * **場所**
   * **Type** 
   * **サブスクリプション名**
   * **サブスクリプション ID**
   * **アクセス エンドポイント**
   * **ランタイム発信 IP アドレス**
   * **アクセス エンドポイントの IP アドレス**
   * **コネクタ発信 IP アドレス**

## <a name="disable-or-enable-logic-apps"></a>ロジック アプリを無効または有効にする

Azure portal で、[1 つのロジック アプリ](#disable-enable-single-logic-app)を、または[複数のロジック アプリを同時に](#disable-or-enable-multiple-logic-apps)有効または無効にすることができます。 また、[Visual Studio でロジック アプリを有効または無効にする](manage-logic-apps-with-visual-studio.md#disable-or-enable-logic-app)ことができます。

ロジック アプリを無効にすると、次のようにワークフロー インスタンスと実行に影響します。

* 進行中および保留中のすべての実行は完了するまで引き続き実行されます。 実行の数により、このプロセスには時間がかかる場合があります。

* Logic Apps エンジンでは、新しいワークフロー インスタンスが作成または実行されません。

* トリガーは、次にその条件が満たされたときに起動されません。

* トリガーの状態には、ロジック アプリが停止したポイントが記憶されます。 そのため、ロジック アプリを再度有効にすると、前回の実行以降のすべての未処理の項目に対してトリガーが起動されます。

  前回の実行以降の未処理の項目に対してロジック アプリが起動しないようにするには、ロジック アプリを再度有効にする前に、トリガーの状態をクリアします。

  1. Azure portal で、[ご利用のロジック アプリを検索して開きます](#find-logic-app)。

  1. ロジック アプリのトリガーの任意の部分を編集します。

  1. 変更を保存します。 この手順により、トリガーの現在の状態がリセットされます。

  1. [ロジック アプリを有効にします](#disable-enable-single-logic-app)。

<a name="disable-enable-single-logic-app"></a>

### <a name="disable-or-enable-single-logic-app"></a>1 つのロジック アプリを無効または有効にする

1. Azure portal で、[ご利用のロジック アプリを検索して開きます](#find-logic-app)。

1. ロジック アプリのメニューで、 **[概要]** を選択します。 次のオプションから選択します。

   * ツールバーで、 **[Disable]\(無効化\)** を選択します。

     ![ロジック アプリのツール バーのスクリーンショット。[無効化] ボタンが選択された状態を示す。](./media/manage-logic-apps-with-azure-portal/disable-single-logic-app.png)

     ロジック アプリが既に無効になっている場合は、 **[有効化]** オプションのみが表示されます。

   * ツールバーで、 **[Enable]\(有効化\)** を選択します。

     ![ロジック アプリのツール バーのスクリーンショット。[有効化] ボタンが選択された状態を示す。](./media/manage-logic-apps-with-azure-portal/enable-single-logic-app.png)

     ロジック アプリが既に有効になっている場合は、 **[無効化]** オプションのみが表示されます。 

   Azure portal で、操作が成功したか失敗したかを確認する通知がメインの Azure ツールバーに表示されます。

   ![Azure portal のスクリーンショット。操作の状態を確認する通知を示す](./media/manage-logic-apps-with-azure-portal/operation-confirmation-notification.png)

<a name="disable-or-enable-multiple-logic-apps"></a>

### <a name="disable-or-enable-multiple-logic-apps"></a>複数のロジック アプリを無効または有効にする

1. Azure portal で、無効または無効にする[ロジック アプリを検索します](#find-logic-app)。

1. ロジック アプリが現在有効になっているか無効になっているかを確認するには、 **[Logic Apps]** ページで、そのロジック アプリの **[状態]** 列を確認します。 

   ![Azure portal の [Logic Apps] ページのスクリーンショット。[状態] 列でソートされたロジック アプリの一覧を示す。](./media/manage-logic-apps-with-azure-portal/view-logic-app-status.png)

   **[状態]** 列が表示されていない場合は、 **[Logic Apps]** ツール バーで、 **[プレビューを試す]** を選択します。

   ![Azure portal の [Logic Apps] ページのスクリーンショット。[プレビューを試す] ボタンが選択された状態を示す。](./media/manage-logic-apps-with-azure-portal/select-try-preview.png)

1. チェックボックスの列で、無効または有効にするロジック アプリを検索します。 ツール バーで、 **[無効化]** または **[有効化]** を選択します。

   ![Azure portal の [Logic Apps] ページのスクリーンショット。複数のロジック アプリ用の [有効化] ボタンと [無効化] ボタンを示す。](./media/manage-logic-apps-with-azure-portal/enable-disable-multiple-logic-apps.png)

1. 確認ボックスが表示されたら、 **[はい]** を選択して続行します。

   Azure portal で、操作が成功したか失敗したかを確認する通知がメインの Azure ツールバーに表示されます。

## <a name="delete-logic-apps"></a>ロジック アプリを削除する

Azure portal で、[1 つのロジック アプリを削除する](#delete-single-logic-app)か、または[複数のロジック アプリを同時に削除する](#delete-multiple-logic-apps)ことができます。 また、[Visual Studio でロジック アプリを削除する](manage-logic-apps-with-visual-studio.md#delete-your-logic-app)ことができます。

ロジック アプリを削除すると、次のようにワークフロー インスタンスに影響します。

* 進行中および保留中のすべての実行は完了するまで引き続き実行されます。 実行の数により、このプロセスには時間がかかる場合があります。

* Logic Apps エンジンでは、新しいワークフロー インスタンスが作成または実行されません。

> [!NOTE]
> 子ロジック アプリを削除して再作成する場合は、親ロジック アプリを再保存する必要があります。 再作成された子アプリのメタデータは異なります。
> 子を再作成した後に親ロジック アプリを再保存しないと、子ロジック アプリへの呼び出しは失敗し、"未承認" というエラーが表示されます。 この動作は、統合アカウントのアーティファクトを使用する親子ロジック アプリや、Azure Functions を呼び出す親子ロジック アプリに当てはまります。

<a name="delete-single-logic-app"></a>

### <a name="delete-single-logic-app"></a>1 つのロジック アプリを削除する

1. Azure portal で、[ご利用のロジック アプリを検索して開きます](#find-logic-app)。

1. ロジック アプリのメニューで、 **[概要]** を選択します。 ロジック アプリのツール バーで、 **[削除]** を選択します。

   ![ロジック アプリのツール バーのスクリーンショット。[削除] ボタンが選択された状態を示す。](./media/manage-logic-apps-with-azure-portal/delete-single-logic-app.png)

1. 確認ボックスが表示されたら、ロジック アプリ名を入力して、 **[削除]** を選択します。

   ![単一のロジック アプリの削除を確認するロジック アプリ プロンプトのスクリーンショット。](./media/manage-logic-apps-with-azure-portal/delete-confirmation-single-logic-app.png)

   Azure portal で、操作が成功したか失敗したかを確認する通知がメインの Azure ツールバーに表示されます。

<a name="delete-multiple-logic-apps"></a>

### <a name="delete-multiple-logic-apps"></a>複数のロジック アプリを削除する

1. Azure portal で、削除する[ロジック アプリを検索します](#find-logic-app)。

1. チェックボックスの列で、削除するロジック アプリを検索します。 ツールバーの **[削除]** を選択します。

   ![Azure portal の [Logic Apps] ページのスクリーンショット。リスト内の複数のロジック アプリを削除するように選択した状態を示す。](./media/manage-logic-apps-with-azure-portal/delete-multiple-logic-apps.png)

1. 確認ボックスが表示されたら、「`yes`」と入力して、 **[削除]** を選択します。

   ![複数のロジック アプリの削除を確認するロジック アプリ プロンプトのスクリーンショット。](./media/manage-logic-apps-with-azure-portal/delete-confirmation-multiple-logic-apps.png)

   Azure portal で、操作が成功したか失敗したかを確認する通知がメインの Azure ツールバーに表示されます。

<a name="manage-logic-app-versions"></a>

## <a name="manage-logic-app-versions"></a>ロジック アプリのバージョンを管理する

ロジック アプリのバージョン コントロールには、Azure portal を使用できます。 ロジック アプリのバージョン履歴を確認し、以前のバージョンのレベルを上げることができます。

<a name="find-version-history"></a>

### <a name="find-and-view-previous-versions"></a>以前のバージョンを検索して表示する

1. Azure portal で、[管理するロジック アプリを検索します](#find-logic-app)。

1. ロジック アプリのメニューで、 **[開発ツール]** の下にある **[バージョン]** を選択します。

   ![Azure portal のロジック アプリのスクリーンショット。[開発ツール] の [バージョン] ページが選択された状態を示す。](./media/manage-logic-apps-with-azure-portal/logic-apps-menu-versions.png)

1. 管理するロジック アプリの **バージョン** を一覧から選択します。 検索バーに **バージョン** ID を入力すると、一覧をフィルター処理できます。

1. **[履歴のバージョン]** ページで、以前のバージョンの詳細が読み取り専用モードで表示されます。 Logic Apps **デザイナー** モードか、**コード ビュー** モードのどちらかを選択できます。

   ![ロジック アプリの [履歴のバージョン] ページのスクリーンショット。コード ビューおよびデザイナー ビューのオプションを示す。](./media/manage-logic-apps-with-azure-portal/history-version.png)

<a name="promote-previous-versions"></a>

### <a name="promote-previous-versions"></a>以前のバージョンのレベルを上げる

1. ロジック アプリのバージョン履歴で、[レベルを上げるバージョンを見つけて選択します](#find-version-history)。

1. **[履歴のバージョン]** ページで、 **[レベル上げ]** を選択します。

   ![ロジック アプリのバージョン履歴のスクリーンショット。以前のバージョンのレベルを上げるボタンを示す。](./media/manage-logic-apps-with-azure-portal/promote-button.png)

1. 開いた **[Logic Apps デザイナー]** ページで、必要に応じてレベルを上げるバージョンを編集します。 **[デザイナー]** モードと **[コード ビュー]** モードを切り替えることができます。 **[パラメーター]** 、 **[テンプレート]** 、 **[コネクタ]** を更新することもできます。

   ![Logic Apps デザイナーのスクリーンショット。以前のバージョンのロジック アプリのレベルを上げるボタンを示す。](./media/manage-logic-apps-with-azure-portal/promote-page.png)

1. 更新内容を保存し、以前のバージョンのレベル上げを完了するには、 **[保存]** を選択します。 (または、変更を取り消すには、 **[破棄]** を選択します。) 

   [ロジック アプリのバージョン履歴をもう一度表示する](#find-version-history)と、レベルの上がったバージョンがリストの先頭に表示され、新しい識別子が含まれています。

## <a name="next-steps"></a>次のステップ

* [ロジック アプリの監視](monitor-logic-apps.md)
