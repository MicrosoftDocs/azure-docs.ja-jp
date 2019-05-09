---
title: Azure Resource Manager テンプレートと PowerShell を使ってラボを自動的に作成または変更する | Microsoft Docs
description: Azure Resource Manager テンプレートと PowerShell を使用して DevTest ラボでラボを自動的に作成または変更する方法を学習します
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: dad9944c-0b20-48be-ba80-8f4aa0950903
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: cb5a08730b47cb5df3116aa4a54554ef0ee6f260
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149752"
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Azure Resource Manager テンプレートと PowerShell を使ってラボを自動的に作成または変更する

DevTest ラボが提供する数多くの Azure Resource Manager テンプレートと PowerShell スクリプトは、新規ラボの作成や既存ラボの変更とこれらのリソースのデプロイを速く、自動的に行うのに役立ちます。

この記事では、これらのテンプレートとスクリプトを使用してラボの作成、変更、デプロイを自動化するプロセスを説明します。 この記事では、PowerShell を使用して DevTest ラボでいくつかの一般的タスクを実行する方法の詳細も紹介します。

## <a name="step-1-gather-your-templates-and-scripts"></a>ステップ 1:テンプレートとスクリプトを収集する
あらかじめ定義された [Azure Resource Manager テンプレート](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)と [PowerShell スクリプト](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts)が、当社が公開する [GitHub リポジトリ](https://github.com/Azure/azure-devtestlab)にあります。 これらをそのまま使用するか、ニーズに合わせてカスタマイズして、独自の[プライベート Git リポジトリ](devtest-lab-add-artifact-repo.md)に保管できます。

## <a name="step-2-modify-your-azure-resource-manager-template"></a>ステップ 2:Azure Resource Manager テンプレートの変更
これまでテンプレートを作成したことがない場合は、「[初めての Azure Resource Manager テンプレートを作成する](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)」の手順に従うことができます。

さらに、「[Azure Resource Manager テンプレートを作成するためのベスト プラクティス](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices)」では、信頼性が高く使いやすい Azure Resource Manager テンプレートの作成に役立つ多くのガイドラインと推奨事項を紹介しています。 通常、紹介されているいずれかのアプローチや例のバリエーションを使用し、ニーズに応じてテンプレートを変更します。

## <a name="step-3-deploy-resources-with-powershell"></a>ステップ 3:PowerShell によりリソースをデプロイする
テンプレートやスクリプトをカスタマイズした後で、[Resource Manager テンプレートと Azure PowerShell を使用してリソースをデプロイ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)するために必要な手順に従ってください。 この記事では、Azure PowerShell と Azure Resource Manager テンプレートを使用してリソースを Azure にデプロイする場合の概要を説明します。


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>DevTest ラボで PowerShell を使用して実行できる一般的なタスク
PowerShell を使用して自動化できる一般的タスクが他にも多数あります。 ドキュメントの以下のセクションで、これらのタスクを実行するために必要な手順を概説します。

* [PowerShell を使用して VHD ファイルからカスタム イメージを作成する](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [PowerShell を使用してラボのストレージ アカウントに VHD ファイルをアップロードする](devtest-lab-upload-vhd-using-powershell.md)
* [PowerShell を使用してラボに外部ユーザーを追加する](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [PowerShell を使用してラボ カスタム ロールを作成する](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>次の手順
* カスタマイズしたテンプレートまたはスクリプトを保存するための[プライベート Git リポジトリ](devtest-lab-add-artifact-repo.md)を作成する方法を学習します。
* [Azure クイックスタート テンプレート ギャラリーから Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates)を検索します。
