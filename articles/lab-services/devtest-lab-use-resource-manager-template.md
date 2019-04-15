---
title: 仮想マシンの Azure Resource Manager テンプレートを表示し使用する | Microsoft Docs
description: 仮想マシンから Azure Resource Manager テンプレートを使用して他の VM を作成する方法を説明します
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a759d9ce-655c-4ac6-8834-cb29dd7d30dd
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: spelluru
ms.openlocfilehash: 533770d98b146dea01e91e1249115c4b5c074b3c
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648437"
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して仮想マシンを作成する 

[Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) を介して DevTest Labs で仮想マシン (VM) を作成すると、VM を保存する前に Azure Resource Manager テンプレートを表示できます。 このテンプレートは、同じ設定で追加のラボ VM を作成するベースとして使用できます。

この記事では、複数の VM と単一の VM の Resource Manager テンプレートについて説明します。また、VM の作成時にテンプレートを表示し、保存する方法を示します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>マルチ VM と単一 VM の Resource Manager テンプレートの比較
Resource Manager テンプレートを使用して DevTest Labs で VM を作成する方法は 2 つあります。Microsoft.DevTestLab/labs/virtualmachines リソースのプロビジョニング、またはMicrosoft.Compute/virtualmachines リソースのプロビジョニングです。 それぞれ、異なるシナリオで使用され、異なるアクセス許可が必要です。

- Microsoft.DevTestLab/labs/virtualmachines というリソースの種類 (テンプレートの “resource” プロパティで宣言される) を使用する Resource Manager テンプレートは、個々のラボ VM をプロビジョニングできます。 その後、各 VM は DevTest Labs 仮想マシンの一覧に 1 つの項目として表示されます。

   ![DevTest Labs 仮想マシンの一覧に 1 つの項目として表示される VM の一覧](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   この種類の Resource Manager テンプレートは、Azure PowerShell コマンド **New-AzResourceGroupDeployment** によって、または Azure CLI コマンド **az group deployment create** によってプロビジョニングできます。 これには管理者のアクセス許可が必要なので、DevTest Labs のユーザー ロールに割り当てられているユーザーはデプロイを実行できません。 

- Microsoft.Compute/virtualmachines というリソースの種類を使用する Resource Manager テンプレートは、複数の VM を、DevTest Labs 仮想マシンの一覧における 1 つの環境としてプロビジョニングできます。

   ![DevTest Labs 仮想マシンの一覧に 1 つの項目として表示される VM の一覧](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   同じ環境にある VM を一緒に管理でき、同じライフ サイクルを共有します。 管理者がこのようにラボを構成していれば、DevTest Labs のユーザー ロールを割り当てられているユーザーは、これらのテンプレートを使用して環境を作成できます。

これ以降、この記事では Microsoft.DevTestLab/labs/virtualmachines を使用する Resource Manager テンプレートについて説明します。 これらは、ラボ管理者がラボ VM 作成 (たとえば、要求可能な VM) またはゴールデン イメージ生成 (たとえば、イメージの出荷) を自動化するのに使用されます。

「[Azure Resource Manager テンプレートを作成するためのベスト プラクティス](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices)」では、信頼性が高く使いやすい Azure Resource Manager テンプレートを作成するのに役立つ多くのガイドラインと推奨事項を紹介しています。

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>仮想マシンの Resource Manager テンプレートを表示し保存する
1. 「[Azure DevTest Labs のラボに最初の VM を作成する](tutorial-create-custom-lab.md#add-a-vm-to-the-lab)」の手順に従い、仮想マシンの作成を始めます。
1. 仮想マシンに必要な情報を入力し、この VM に必要なすべてのアイテムを追加します。
1. [構成の設定] ウィンドウの下部で、**[View ARM template]\(ARM テンプレートの表示\)** を選択します。

   ![[View ARM template]\(ARM テンプレートの表示\) ボタン](./media/devtest-lab-use-arm-template/devtestlab-lab-view-rm-template.png)
1. 後で別の仮想マシンの作成に使用するために、Resource Manager テンプレートをコピーして保存します。

   ![後から使用するために保存する Resource Manager テンプレート](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

Resource Manager テンプレートを保存したら、使用する前に、テンプレートのパラメーター セクションを更新する必要があります。 実際の Resource Manager テンプレートの外部で、パラメーターだけをカスタマイズする parameter.json を作成できます。 

![JSON ファイルを使用してパラメーターをカスタマイズする](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

これで、Resource Manager テンプレートを使用して [VM を作成する](devtest-lab-create-environment-from-arm.md)準備ができました。

### <a name="next-steps"></a>次の手順
* [Resource Manager テンプレートを使用してマルチ VM 環境を作成する](devtest-lab-create-environment-from-arm.md)方法を確認します。
* [Resource Manager テンプレートをデプロイして VM を作成する](devtest-lab-create-environment-from-arm.md#automate-deployment-of-environments)
* [パブリックの DevTest Labs GitHub リポジトリ](https://github.com/Azure/azure-quickstart-templates)から、DevTest Labs 自動化のためのクイックスタートの Resource Manager テンプレートをもっと探します。
