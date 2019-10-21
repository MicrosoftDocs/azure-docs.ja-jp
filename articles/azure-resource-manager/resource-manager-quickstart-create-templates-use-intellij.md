---
title: IntelliJ IDEA を使用した Azure Resource Manager テンプレートの作成とデプロイ | Microsoft Docs
description: IntelliJ IDEA を使用して最初の Azure Resource Manager テンプレートを作成する方法とテンプレートをデプロイする方法を説明します。
services: azure-resource-manager
documentationcenter: ''
author: yucwan
manager: ''
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: java
ms.date: 08/01/2019
ms.topic: quickstart
ms.author: yucwan
ms.openlocfilehash: d25a5720e3aaa8ad6306a72d2db8d3ca2e89861b
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169442"
---
# <a name="quickstart-create-and-deploy-azure-resource-manager-templates-by-using-the-intellij-idea"></a>クイック スタート:IntelliJ IDEA を使用した Azure Resource Manager テンプレートの作成とデプロイ

IntelliJ IDEA を使用して Resource Manager テンプレートを Azure にデプロイする方法と、IDE から直接テンプレートを編集および更新するプロセスについて説明します。 Resource Manager テンプレートとは、ソリューションに対してデプロイが必要なリソースを定義した JSON ファイルのことをいいます。 Azure ソリューションのデプロイと管理に関する概念について理解を深めるには、「[Azure Resource Manager の概要](resource-group-overview.md)」を参照してください。

![Resource Manager テンプレートのクイックスタート ポータル ダイアグラム](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

チュートリアルを完了した後、Azure Storage アカウントをデプロイします。 同じプロセスを使用して他の Azure リソースをデプロイすることができます。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition または Community Edition がインストールされていること
* [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053) がインストールされていること。詳細については、[IntelliJ のプラグイン管理ガイド](https://www.jetbrains.com/help/idea/managing-plugins.html)を参照してください
* Azure Toolkit for IntelliJ 用の自分の Azure アカウントに[サインイン](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-sign-in-instructions)していること

## <a name="deploy-a-quickstart-template"></a>クイックスタート テンプレートをデプロイする

ゼロからテンプレートを作成するのではなく、[Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/)からテンプレートを開きます。 Azure クイック スタート テンプレートは、Resource Manager テンプレートのリポジトリです。 このクイック スタートで使用されるテンプレートは、[Create a standard storage account](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create/) と呼ばれます。 これにより、Azure Storage アカウント リソースが定義されます。

1. [`azuredeploy.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) と [`azuredeploy.parameters.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json) を右クリックして、お使いのローカル コンピューターに保存します。

1. Azure Toolkit が適切にインストールされ、サインインしている場合は、IntelliJ IDEA のサイドバーに Azure Explorer が表示されます。 **[リソース管理]** を右クリックし、 **[デプロイの作成]** を選択します。

    ![Resource Manager テンプレートを右クリックしてデプロイを作成する](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-create-deployment-right-click.png)

1. **[デプロイ名]** 、 **[サブスクリプション]** 、 **[リソース グループ]** 、 **[リージョン]** を構成します。 ここでは、テンプレートを新しいリソース グループ `testRG` にデプロイします。 次に、 **[リソース テンプレート]** のパスに `azuredeploy.json` を選択し、 **[リソース パラメーター]** にダウンロードした `azuredeploy.parameters.json` を選択します。

    ![Resource Manager テンプレートでデプロイを作成するファイルを選択する](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-create-deployment-select-files.png)

1. [OK] をクリックすると、デプロイが開始されます。 デプロイが完了するまで、下部にある IntelliJ IDEA の**ステータ スバー**から進行状況を確認できます。

    ![Resource Manager テンプレートのデプロイの状態](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-create-deployment-status.png)

## <a name="browse-an-existing-deployment"></a>既存のデプロイの参照

1. デプロイが完了すると、新しいリソース グループ `testRG` と新しいデプロイが作成されたことを確認できます。 デプロイを右クリックすると、使用可能なアクションの一覧が表示されます。 **[プロパティの表示]** を選択します。

    ![Resource Manager テンプレートのデプロイの参照](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-deployment-browse.png)

1. タブ ビューが開き、デプロイの状態やテンプレートの構造などの有用なプロパティが表示されます。

    ![デプロイ プロパティを示す Resource Manager テンプレート](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-deployment-show-properties.png)

## <a name="edit-and-update-an-existing-deployment"></a>既存のデプロイを編集および更新する

1. 右クリック メニューまたは前の [プロパティの表示] ビューから **[Edit Deployment]\(デプロイの編集\)** を選択します。 別のタブ ビューが開き、Azure でデプロイするためのテンプレートとパラメーター ファイルが表示されます。 これらのファイルをローカルに保存するには、 **[Export Template File]\(テンプレート ファイルのエクスポート\)** または **[Export Parameter Files]\(パラメーター ファイルのエクスポート\)** をクリックします。

    ![Resource Manager テンプレートのデプロイの編集](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-edit-deployment.png)

1. このページで 2 つのファイルを編集し、変更を Azure にデプロイすることができます。 ここでは、パラメーター ファイルの **storageAccountType** の値を `Standard_LRS` から `Standard_GRS` に変更します。 次に、下部にある **[更新プログラムの展開]** をクリックし、更新プログラムを確認します。

    ![Resource Manager テンプレートのデプロイの編集](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-edit-deployment-update.png)

1. 更新プログラムのデプロイが完了したら、作成されたストレージ アカウントが `Standard_GRS` に変更されたことをポータルで確認できます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

1. Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。 これは、Azure portal または Azure CLI から行うことができます。 IntelliJ IDEA から Azure Explorer で、作成した**リソース グループ**を右クリックし、削除を選択します。

    ![IntelliJ IDEA から Azure Explorer のリソース グループを削除する](./media/resource-manager-quickstart-create-templates-use-intellij/delete-resource-group.png)

> [!NOTE]
> デプロイを削除しても、デプロイによって作成されたリソースは削除されないことに注意してください。 対応するリソース グループまたは特定のリソースが不要になった場合は、削除してください。

## <a name="next-steps"></a>次の手順

このクイックスタートの主な目的は、IntelliJ IDEA を使用して、Azure クイックスタート テンプレートから既存のテンプレートをデプロイすることです。 また、Azure で既存のデプロイを表示および更新する方法についても学習しました。 Azure クイック スタート テンプレートのテンプレートでは、必要なものすべてを得ることができないことがあります。 テンプレートの開発についてさらに学習するには、以下の新しい初心者向けチュートリアル シリーズを参照してください。

> [!div class="nextstepaction"]
> [初心者向けチュートリアル](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Azure デベロッパー センターで Java にアクセスする](https://docs.microsoft.com/azure/java)
