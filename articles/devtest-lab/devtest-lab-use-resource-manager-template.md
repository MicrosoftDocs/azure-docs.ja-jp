---
title: "仮想マシンの Azure Resource Manager テンプレートを表示し使用する | Microsoft Docs"
description: "仮想マシンから Azure Resource Manager テンプレートを使用して他の VM を作成する方法を説明します"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: a759d9ce-655c-4ac6-8834-cb29dd7d30dd
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: tarcher
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: 12cdb61667f77215c894800d5c439235e767a26b
ms.contentlocale: ja-jp
ms.lasthandoff: 07/06/2017


---

# <a name="use-a-virtual-machines-azure-resource-manager-template"></a>仮想マシンの Azure Resource Manager テンプレートを使用する

[Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) を介して DevTest Labs で仮想マシン (VM) を作成すると、VM を保存する前に Azure Resource Manager テンプレートを表示できます。 このテンプレートは、同じ設定で追加のラボ VM を作成するベースとして使用できます。

この記事では、VM 作成時に Resource Manager テンプレートを表示する方法と、あとからそれをデプロイして同じ VM を自動作成する方法について説明します。

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>マルチ VM と単一 VM の Resource Manager テンプレートの比較
Resource Manager テンプレートを使用して DevTest Labs で VM を作成する方法は 2 つあります。Microsoft.DevTestLab/labs/virtualmachines リソースのプロビジョニング、またはMicrosoft.Commpute/virtualmachines リソースのプロビジョニングです。 それぞれ、異なるシナリオで使用され、異なるアクセス許可が必要です。

- Microsoft.DevTestLab/labs/virtualmachines というリソースの種類 (テンプレートの “resource” プロパティで宣言される) を使用する Resource Manager テンプレートは、個々のラボ VM をプロビジョニングできます。 その後、各 VM は DevTest Labs 仮想マシンの一覧に 1 つの項目として表示されます。

   ![DevTest Labs 仮想マシンの一覧に 1 つの項目として表示される VM の一覧](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   この種類の Resource Manager テンプレートは、Azure PowerShell コマンド **New-AzureRmResourceGroupDeployment** によって、または Azure CLI コマンド **az group deployment create** によってプロビジョニングできます。 これには管理者のアクセス許可が必要なので、DevTest Labs のユーザー ロールに割り当てられているユーザーはデプロイを実行できません。 

- Microsoft.Compute/virtualmachines というリソースの種類を使用する Resource Manager テンプレートは、複数の VM を、DevTest Labs 仮想マシンの一覧における 1 つの環境としてプロビジョニングできます。

   ![DevTest Labs 仮想マシンの一覧に 1 つの項目として表示される VM の一覧](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   同じ環境にある VM を一緒に管理でき、同じライフ サイクルを共有します。 管理者がこのようにラボを構成していれば、DevTest Labs のユーザー ロールを割り当てられているユーザーは、これらのテンプレートを使用して環境を作成できます。

これ以降、この記事では Mirosoft.DevTestLab/labs/virtualmachines を使用する Resource Manager テンプレートについて説明します。 これらは、ラボ管理者がラボ VM 作成 (たとえば、要求可能な VM) またはゴールデン イメージ生成 (たとえば、イメージの出荷) を自動化するのに使用されます。

「[Azure Resource Manager テンプレートを作成するためのベスト プラクティス](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices)」では、信頼性が高く使いやすい Azure Resource Manager テンプレートを作成するのに役立つ多くのガイドラインと推奨事項を紹介しています。

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>仮想マシンの Resource Manager テンプレートを表示し保存する
1. 「[Azure DevTest Labs のラボに最初の VM を作成する](devtest-lab-create-first-vm.md)」の手順に従い、仮想マシンの作成を始めます。
1. 仮想マシンに必要な情報を入力し、この VM に必要なすべてのアイテムを追加します。
1. [構成の設定] ウィンドウの下部で、**[View ARM template]\(ARM テンプレートの表示\)** を選択します。

   ![[View ARM template]\(ARM テンプレートの表示\) ボタン](./media/devtest-lab-use-arm-template/devtestlab-lab-view-rm-template.png)
1. 後で別の仮想マシンの作成に使用するために、Resource Manager テンプレートをコピーして保存します。

   ![後から使用するために保存する Resource Manager テンプレート](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

Resource Manager テンプレートを保存したら、使用する前に、テンプレートのパラメーター セクションを更新する必要があります。 実際の Resource Manager テンプレートの外部で、パラメーターだけをカスタマイズする parameter.json を作成できます。 

![JSON ファイルを使用してパラメーターをカスタマイズする](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

## <a name="deploy-a-resource-manager-template-to-create-a-vm"></a>Resource Manager テンプレートをデプロイして VM を作成する
Resource Manager テンプレートを保存して必要に応じてカスタマイズしたら、VM の作成を自動化するのに使用できます。 「[Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)」では、Azure PowerShell と Resource Manager テンプレートを使用して Azure にリソースをデプロイする方法を説明しています。 「[Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)」では、Azure CLI と Resource Manager テンプレートを使用して Azure にリソースをデプロイする方法について説明しています。

> [!NOTE]
> ラボ所有者権限を持つユーザーだけが、Azure PowerShell を使用して Resource Manager テンプレートから VM を作成できます。 Resource Manager テンプレートを使用して VM の作成を自動化する場合でユーザー権限しか持っていない場合は、[CLI で **az lab vm create** コマンド](https://docs.microsoft.com/cli/azure/lab/vm#create) を使用します。

### <a name="next-steps"></a>次のステップ
* [Resource Manager テンプレートを使用してマルチ VM 環境を作成する](devtest-lab-create-environment-from-arm.md)方法を確認します。
* [パブリックの DevTest Labs GitHub レポジトリ](https://github.com/Azure/azure-quickstart-templates)から、DevTest Labs 自動化のためのクイックスタートの Resource Manager テンプレートをもっと探します。

