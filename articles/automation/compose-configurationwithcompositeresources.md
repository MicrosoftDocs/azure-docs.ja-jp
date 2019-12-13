---
title: Azure Automation State Configuration (DSC) で複合リソースを使用して DSC 構成を作成する
description: Azure Automation State Configuration (DSC) で複合リソースを使用して構成を作成する方法について説明します
keywords: powershell dsc, Desired State Configuration, powershell dsc azure, 複合リソース
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 08/21/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e9552adad1f15422eb60c34f2548324aeb03ee74
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849566"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-dsc-using-composite-resources"></a>Azure Automation State Configuration (DSC) で複合リソースを使用して DSC 構成を作成する

リソースを複数の Desired State Configuration (DSC) 構成で管理する必要がある場合に最適な方法は、[複合リソース](/powershell/scripting/dsc/resources/authoringresourcecomposite)を使用することです。 複合リソースは、別の構成内の DSC リソースとして使用されている、入れ子になったパラメーター化された構成です。 これにより、基礎となる複合リソース (パラメーター化された構成) を個別に管理および構築しながら、複雑な構成を作成することができます。

Azure Automation により、[複合リソースのインポートとコンパイル](automation-dsc-compile.md#compiling-configurations-in-azure-automation-that-contain-composite-resources)が可能になります。
複合リソースが Automation アカウントにインポートされると、 **[状態の構成 (DSC)]** ページで **[構成の作成]** エクスペリエンスを使用できるようになります。

## <a name="composing-a-configuration-from-composite-resources"></a>複合リソースから構成を作成する

Azure portal で複合リソースから作成された構成を割り当てるには、まずそれを作成する必要があります。 これを行うには、 **[状態の構成 (DSC)]** ページの **[構成]** または **[コンパイル済み構成]** のいずれかのタブで **[構成の作成]** を使用します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 左側の **[すべてのリソース]** をクリックし、使用している Automation アカウントの名前をクリックします。
1. **[Automation アカウント]** ページで、 **[構成管理]** の **[状態の構成 (DSC)]** を選択します。
1. **[状態の構成 (DSC)]** ページで、 **[構成]** または **[コンパイル済み構成]** のいずれかのタブをクリックしてから、ページ上部のメニューで **[構成の作成]** をクリックします。
1. **[基本]** のステップでは、新しい構成名 (必須) を指定して、新しい構成に含める各複合リソースの行の任意の場所をクリックし、 **[次へ]** をクリックするか、 **[ソース コード]** のステップをクリックします。 以下のステップでは、**PSExecutionPolicy** と **RenameAndDomainJoin** の複合リソースが選択されています。
   ![[構成の作成] ページの [基本] ステップのスクリーンショット](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. **[ソース コード]** ステップでは、選択した複合リソースの作成された構成がどのようになっているかが示されます。 すべてのパラメーターのマージと、それらが複合リソースにどのように渡されるかを確認することができます。 新しいソース コードの確認が終ったら、 **[次へ]** をクリックするか、 **[パラメーター]** ステップをクリックします。
   ![[構成の作成] ページの [ソース コード] ステップのスクリーンショット](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. **[パラメーター]** ステップでは、各複合リソースが持っているパラメーターを指定できるように、それらが公開されます。 パラメーターに説明がある場合は、パラメーター フィールドの横に表示されます。 フィールドが **PSCredential** 型パラメーターの場合、構成のためのドロップダウンには現在の Automation アカウント内の**資格情報**オブジェクトのリストが提供されます。 **[+ 資格情報の追加]** オプションも使用できます。 すべての必須パラメーターを指定したら、 **[Save and compile]\(保存してコンパイル\)** をクリックします。
   ![[構成の作成] ページの [パラメーター] ステップのスクリーンショット](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

新しい構成が保存されると、その構成がコンパイルのために送信されます。 コンパイル ジョブの状態は、他のインポートされた構成と同じように表示できます。 詳細については、「[コンパイル ジョブを表示する](automation-dsc-getting-started.md#viewing-a-compilation-job)」を参照してください。

コンパイルが正常に完了すると、新しい構成が **[コンパイル済み構成]** タブに表示されます。構成がこのタブに表示されたら、「[別のノード構成にノードを再割り当てする](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration)」の手順に従って、その構成をマネージド ノードに割り当てることができます。

## <a name="next-steps"></a>次の手順

- 使用を開始するには、「[Azure Automation State Configuration の使用](automation-dsc-getting-started.md)」をご覧ください。
- ノードをオンボードにする方法は、「[Azure Automation State Configuration による管理のためのマシンのオンボード](automation-dsc-onboarding.md)」をご覧ください。
- DSC 構成をコンパイルしてターゲット ノードに割り当てることができるようにする方法の詳細については、「[Azure Automation State Configuration での構成のコンパイル](automation-dsc-compile.md)」をご覧ください。
- PowerShell コマンドレットのリファレンスについては、[Azure Automation State Configuration のコマンドレット](/powershell/module/azurerm.automation/#automation)に関するページをご覧ください。
- 料金情報については、[Azure Automation State Configuration の価格](https://azure.microsoft.com/pricing/details/automation/)に関するページをご覧ください。
- 継続的なデプロイ パイプラインで Azure Automation State Configuration を使う例については、「[Automation State Configuration と Chocolatey を使用した仮想マシンへの継続的なデプロイ](automation-dsc-cd-chocolatey.md)」をご覧ください。
