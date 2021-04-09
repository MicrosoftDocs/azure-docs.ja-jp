---
title: チュートリアル:Azure portal でのクエリの管理
description: このチュートリアルでは、Azure portal で Resource Graph クエリを作成し、新しいクエリを他のユーザーと共有します。
ms.date: 01/27/2021
ms.topic: tutorial
ms.openlocfilehash: 7240a843cfa041137b28284e396c8a4b62a81926
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98916681"
---
# <a name="tutorial-create-and-share-an-azure-resource-graph-query-in-the-azure-portal"></a>チュートリアル:Azure portal で Azure Resource Graph クエリを作成して共有する

Azure Resource Graph エクスプローラーを使うと、Azure portal で直接 Resource Graph クエリを保存できます。 クエリには次の 2 種類があります: "_プライベート_" と "_共有_"。 プライベート クエリは、自分の Azure portal の設定に保存されます。 一方、共有クエリは、Azure ロールベースのアクセス制御 (Azure RBAC) による管理とリソース ロックによる保護が可能な Azure Resource Manager リソースです。 どちらの種類のクエリも、保存時に暗号化されます。

お気に入りのクエリやよく使用するクエリを Azure portal に保存しておくと、探す時間を節約できます。 クエリを共有すると、一貫性と効率性というチームの目標を、繰り返しによって実現することができます。

このチュートリアルでは、次のタスクを実行します。

> [!div class="checklist"]
> - プライベート クエリを作成および削除する
> - 共有クエリを作成する
> - 共有クエリを見つける
> - 共有クエリを削除する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、Azure サブスクリプションが必要です。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。

## <a name="create-and-delete-a-private-query"></a>プライベート クエリを作成および削除する

プライベート クエリにアクセスしてそれを表示できるのは、クエリを作成したアカウントだけです。 それらは Azure portal のアカウントの設定に保存されるので、Azure portal の内部からのみ、作成、使用、削除することができます。 プライベート クエリは、Resource Manager のリソースではありません。 新しいプライベート クエリを作成するには、次の手順のようにします。

1. ポータルのメニューから、 **[すべてのサービス]** を選択するか、すべてのページの上部にある Azure 検索ボックスを使用します。 **[Resource Graph エクスプローラー]** を探して選択します。

1. Azure Resource Graph エクスプローラー ページの **[クエリ 1]** タブで、次のクエリを入力します。

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

   **[クエリの実行]** を選択すると、下のウィンドウにクエリの結果が表示されます。

   このクエリについて詳しくは、[サンプル – 仮想マシンの数の取得 (OS の種類別)](../samples/starter.md#count-os) に関する記事をご覧ください。

1. **[保存]** または **[名前を付けて保存]** を選択します。 **[クエリの保存]** ウィンドウで、名前に「**Count VMs by OS**」と入力し、種類は **[プライベート クエリ]** のままにして、下部にある **[保存]** を選択します。 タブのタイトルが、 **[クエリ 1]** から **[Count VMs by OS]** に変わります。

1. Azure portal で Azure Resource Graph エクスプローラーから別の場所に移動し、再びエクスプローラーに戻ります。 保存したクエリが表示されなくなり、 **[クエリ 1]** タブに戻っていることに注意してください。

1. **[クエリを開く]** を選択します。 種類が **[プライベート クエリ]** であることを確認します。 保存した名前 "**Count VMs by OS**" が、 **[クエリ名]** の一覧に表示されるようになります。 保存したクエリのタイトル リンクを選択すると、そのクエリの名前の新しいタブに読み込まれます。

   > [!NOTE] 
   > 保存されているクエリを開き、タブにその名前が表示されている状態で、 **[保存]** ボタンを選択すると、行った変更でクエリが更新されます。 この開いているクエリから新しいクエリを作成して保存するには、 **[名前を付けて保存]** を選択して、新しいクエリを保存したときと同じようにします。

1. 保存したクエリを削除するには、 **[クエリを開く]** を再び選択し、 **[種類]** フィールドが **[プライベート クエリ]** に設定されていることを確認します。 保存されている `Count VMs by OS` クエリの行で、 **[削除]** (ごみ箱アイコン) を選択します。 確認ダイアログ ボックスで、 **[はい]** を選択してクエリの削除を完了します。
   次に、 **[クエリを開く]** ウィンドウを閉じます。

## <a name="create-a-shared-query"></a>共有クエリを作成する

プライベート クエリとは異なり、共有クエリは Resource Manager リソースです。 つまり、クエリをリソース グループに保存し、Azure RBAC で管理および制御し、リソース ロックを使用して保護さえできることを意味します。 適切なアクセス許可を持つすべてのユーザーが、リソースとしてそれを参照し、使用することができます。 新しい共有クエリを作成するには、次の手順のようにします。

1. ポータルのメニューから **[すべてのサービス]** を選択するか、すべてのページの上部にある Azure 検索ボックスを使用し、 **[Resource Graph エクスプローラー]** を探して選択します。

1. Azure Resource Graph エクスプローラー ページの **[クエリ 1]** タブで、次のクエリを入力します。

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```
    
   **[クエリの実行]** を選択すると、下のウィンドウにクエリの結果が表示されます。

   このクエリについて詳しくは、[サンプル – 仮想マシンの数の取得 (OS の種類別)](../samples/starter.md#count-os) に関する記事をご覧ください。

1. **[保存]** または **[名前を付けて保存]** を選択します。

   :::image type="content" source="../media/create-share-query/save-shared-query-buttons.png" alt-text="[保存] ボタンを使用して新しいクエリを保存する" border="false":::

1. **[クエリの保存]** ウィンドウで、名前に「**Count VMs by OS**」と入力します。

1. 種類を **[共有クエリ]** に変更し、[説明] に「**OS の種類別の仮想マシンの数**」と入力し、 **[サブスクリプション]** を設定してクエリ リソースを作成する場所を指定します。

1. **['resource-graph-queries' リソース グループに発行します]** チェック ボックスをオンのままにし、 **[リソース グループの場所]** を **[(米国) 米国中西部]** に設定します。

1. **[クエリの保存]** ウィンドウの下部にある **[保存]** を選択します。 タブのタイトルが、 **[クエリ 1]** から **[Count VMs by OS]** に変わります。 **resource-graph-queries** リソース グループを初めて使うときは、リソース グループが作成されるので、保存に予想以上の時間がかかります。
   
   :::image type="content" source="../media/create-share-query/save-shared-query-window.png" alt-text="新しいクエリを共有クエリとして保存する" border="false":::

   > [!NOTE] 
   > 既存のリソース グループの名前を指定して共有クエリをそこに保存する場合は、 **['resource-graph-queries' リソース グループに発行します]** チェック ボックスをオフにすることができます。 クエリに既定の名前のリソース グループを使用すると、共有クエリを探すのが容易になります。 また、そのリソース グループの目的もより明確になります。 ただし、既存のアクセス許可に基づくセキュリティ上の理由から、既存のリソース グループを選択することもあります。

1. Azure portal で Azure Resource Graph エクスプローラーから別の場所に移動し、再びエクスプローラーに戻ります。 保存したクエリが表示されなくなり、 **[クエリ 1]** タブに戻っていることに注意してください。

1. **[クエリを開く]** を選択します。 種類が **[共有クエリ]** に設定されていること、および **[サブスクリプション]** と **[リソース グループ]** の組み合わせが、クエリを保存した場所と一致していることを確認します。 保存した "**Count VMs by OS**" 項目が、 **[クエリ名]** の一覧に表示されるようになります。 保存したクエリのタイトル リンクを選択して、そのクエリの名前の新しいタブに読み込まれます。 共有クエリなので、タブのタイトルの横にアイコンが表示され、共有されていることが示されます。

   :::image type="content" source="../media/create-share-query/show-saved-shared-query.png" alt-text="アイコンの付いたクエリを表示する" border="false":::

   > [!NOTE] 
   > 保存されているクエリを開き、タブにその名前が表示されている状態では、 **[保存]** ボタンにより、行った変更でクエリが更新されます。 新しいクエリを作成して保存するには、 **[名前を付けて保存]** を選択して、新しいクエリを保存したときと同じようにします。

## <a name="discover-shared-queries"></a>共有クエリを見つける

共有クエリは Resource Manager リソースであるため、いくつかの方法で検索できます。

- Resource Graph Explorer で、 **[クエリを開く]** を選択し、種類を **[共有クエリ]** に設定します。
- Resource Graph クエリのポータル ページから。
- 共有クエリが保存されているリソース グループから。
- Resource Graph に対するクエリにより。

### <a name="view-resource-graph-queries"></a>Resource Graph クエリを表示する

Azure portal の Resource Graph クエリ ページには、ログインしているアカウントでアクセスできる共有クエリが表示されます。 このページでは、Resource Graph クエリの名前、サブスクリプション、リソース グループ、その他のプロパティを使用して、フィルター処理を行うことができます。 このインターフェイスを使用して、Resource Graph クエリのタグ付け、エクスポート、削除を行うこともできます。

いずれかのクエリを選択すると、Resource Graph クエリのページが開きます。 他の Resource Manager リソースと同様に、このページには、アクティビティ ログ、アクセス制御、タグと共に対話形式の概要が表示されます。 また、このページから直接リソース ロックを適用することもできます。

ポータルのメニューから Resource Graph クエリ ページに移動するには、 **[すべてのサービス]** を選択するか、すべてのページの上部にある Azure 検索ボックスを使用します。 **[Resource Graph エクスプローラー]** を探して選択します。

### <a name="list-resource-groups-resources"></a>リソース グループのリソースの一覧を表示する

Resource Graph クエリは、リソース グループの一部である他のリソースと共に一覧表示されます。
Resource Graph クエリを選択すると、そのクエリのページが開きます。 省略記号およびショートカット メニュー オプション (右クリックによって起動) は、Resource Graph クエリ ページと同じように動作します。

### <a name="query-resource-graph"></a>Resource Graph のクエリを実行する

Resource Graph のクエリを実行することで、Resource Graph クエリを見つけることができます。 次の Resource Graph クエリでは、種類 `Microsoft.ResourceGraph/queries` によって制限した後、`project` を使用して名前、変更日時、およびクエリ自体のみを一覧表示しています。

```kusto
Resources
| where type == "microsoft.resourcegraph/queries"
| project name, properties.timeModified, properties.query
```

## <a name="run-a-shared-query"></a>共有クエリを実行する

Resource Graph 共有クエリは、`{{shared-query-uri}}` 構文 (プレビュー) を使用して実行できます。 詳細については、[共有クエリの構文](../concepts/query-language.md#shared-query-syntax)に関するセクションを参照してください。

## <a name="delete-a-shared-query"></a>共有クエリを削除する

共有クエリが不要になった場合は、削除します。 共有クエリを削除すると、対応する Resource Manager リソースが削除されます。 結果のグラフがピン留めされたダッシュボードには、エラー メッセージが表示されるようになります。 そのエラー メッセージが表示されたら、 **[ダッシュボードから削除する]** ボタンを使用してダッシュボードをクリーンアップします。

共有クエリは、次のインターフェイスを使用して削除できます。
- Resource Graph クエリ一覧のページ
- Resource Graph クエリのページ
- Resource Graph エクスプローラーの **[クエリを開く]** ページ
- リソース グループ ページ

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルを終了するとき、作成したプライベート クエリと共有クエリがもう必要ない場合は削除してください。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、プライベート クエリと共有クエリを作成しました。 Resource Graph 言語の詳細については、クエリ言語の詳細のページに進んでください。

> [!div class="nextstepaction"]
> [クエリ言語に関する詳細情報を入手します](../concepts/query-language.md)