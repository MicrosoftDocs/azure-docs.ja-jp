---
title: Azure DevTest Labs と Azure Resource Manager テンプレートを使用してラボを作成する
description: このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して Azure DevTest Labs のラボを作成します。 ラボの管理者は、ラボを設定し、ラボに VM を作成して、ポリシーを構成します。
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/26/2020
ms.openlocfilehash: 2b825b4d4485f401199556b6faaef0017f583cc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91461191"
---
# <a name="quickstart-set-up-a-lab-by-using-azure-devtest-labs-arm-template"></a>クイック スタート:Azure DevTest Labs の ARM テンプレートを使用してラボを設定する
このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、Windows Server 2019 Datacenter VM を含むラボを作成します。 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

このクイックスタートでは、以下の操作を実行します。

> [!div class="checklist"]
> * テンプレートを確認する 
> * テンプレートのデプロイ
> * テンプレートを検証する
> * リソースをクリーンアップする

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-dtl-create-lab-windows-vm%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-dtl-create-lab-windows-vm/)からのものです。

:::code language="json" source="~/quickstart-templates/101-dtl-create-lab-windows-vm/azuredeploy.json":::

このテンプレートに定義されているリソースは次のとおりです。

- [**Microsoft.DevTestLab/labs**](/azure/templates/microsoft.devtestlab/labs)
- [**Microsoft.DevTestLab labs/virtualnetworks**](/azure/templates/microsoft.devtestlab/labs/virtualnetworks)
- [**Microsoft.DevTestLab labs/virtualmachines**](/azure/templates/microsoft.devtestlab/labs/virtualmachines)

テンプレートのその他のサンプルについては、「[Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devtestlab)」をご覧ください。

## <a name="deploy-the-template"></a>テンプレートのデプロイ
デプロイメントを自動的に実行するには、次のボタンをクリックします。 

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-dtl-create-lab-windows-vm%2Fazuredeploy.json)

1. 後でクリーンアップしやすいように **新しいリソース グループ** を作成します。
1. リソース グループの **場所** を選びます。 
1. **ラボの名前** を入力します。 
1. **VM の名前** を入力します。 
1. VM にアクセスできる **ユーザーの名前** を入力します。 
1. **[パスワード]** に、そのユーザーのパスワードを入力します。 
1. **[上記の使用条件に同意する]** を選択します。 
1. **[購入]** を選択します。

    :::image type="content" source="./media/create-lab-windows-vm-template/deploy-template-page.png" alt-text="[テンプレートのデプロイ] ページ":::

## <a name="validate-the-deployment"></a>デプロイの検証
1. 上部にある **[通知]** を選択して、デプロイの状態を確認し、 **[デプロイは進行中です]** リンクをクリックします。

    :::image type="content" source="./media/create-lab-windows-vm-template/deployment-notification.png" alt-text="デプロイの通知":::
2. **デプロイの概要** ページで、デプロイが完了するまで待ちます。 この工程 (特に VM の作成) は、完了までにしばらく時間がかかります。 次に、 **[リソース グループに移動]** または **リソース グループの名前** を選択します (下図参照)。 

    :::image type="content" source="./media/create-lab-windows-vm-template/navigate-resource-group.png" alt-text="リソース グループに移動する":::
3. **リソース グループ** ページで、リソース グループ内のリソースを一覧表示します。 リソースに `DevTest Lab` タイプのラボが表示されていることを確認します。 仮想ネットワークや仮想マシンなど、リソース グループ内の依存リソースも表示されます。 

    :::image type="content" source="./media/create-lab-windows-vm-template/resource-group-home-page.png" alt-text="リソース グループのホーム ページ":::
4. リソースの一覧から目的のラボを選択して、そのラボのホーム ページを表示します。 **[自分の仮想マシン]** の一覧に Windows Server 2019 Datacenter VM が表示されていることを確認します。 次の画像では、 **[基本]** セクションが最小化されています。 

    :::image type="content" source="./media/create-lab-windows-vm-template/lab-home-page.png" alt-text="ラボのホーム ページ":::

    > [!IMPORTANT] 
    > このページ開いたままにしておいてください。Azure でラボと VM を実行するコストが課金されないよう、次のセクションの手順に従ってリソースをクリーンアップします。 次のチュートリアルに進んでラボの VM へのアクセス テストを行う場合は、そのチュートリアルを終えてからリソースをクリーンアップしてください。 

## <a name="clean-up-resources"></a>リソースをクリーンアップする

1. まず、リソース グループを削除できるようにラボを削除します。 ラボが含まれているとリソース グループを削除できません。 ラボを削除するには、ツール バーの **[削除]** を選択します。 

    :::image type="content" source="./media/create-lab-windows-vm-template/delete-lab-button.png" alt-text="ラボの削除ボタン":::
 2. 確認ページで **ラボ名** を入力し、 **[削除]** を選択します。 
 3. ラボが削除されるまで待ちます。 **ベル** のアイコンを選択すると、削除操作からの通知が表示されます。 このプロセスには、ある程度時間がかかります。 ラボが削除されたのを確認して、階層リンク メニューの **[リソース グループ]** を選択します。 
 
    :::image type="content" source="./media/create-lab-windows-vm-template/confirm-lab-deletion.png" alt-text="VM が削除されたことを通知で確認する":::
 1. **[リソース グループ]** ページで、ツール バーの **[リソース グループの削除]** を選択します。 確認ページで **リソース グループ名** を入力し、 **[削除]** を選択します。 通知をチェックして、リソース グループが削除されたことを確認します。
 
    :::image type="content" source="./media/create-lab-windows-vm-template/delete-resource-group-button.png" alt-text="[リソース グループの削除] ボタン":::

## <a name="next-steps"></a>次のステップ
このクイックスタートでは、VM を備えたラボを作成しました。 ラボにアクセスする方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [チュートリアル:ラボにアクセスする](tutorial-use-custom-lab.md)
