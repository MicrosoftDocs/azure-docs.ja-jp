---
title: Azure Resource Manager テンプレートを使ってラボを作成または変更する
description: Azure Resource Manager テンプレートと PowerShell を使用して DevTest ラボでラボを自動的に作成または変更する方法を学習します
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 645f1d0717514d2c7e7b16844513327127e4e1a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87272642"
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Azure Resource Manager テンプレートと PowerShell を使ってラボを自動的に作成または変更する

DevTest ラボが提供する数多くの Azure Resource Manager テンプレートと PowerShell スクリプトは、新規ラボの作成や既存ラボの変更とこれらのリソースのデプロイを速く、自動的に行うのに役立ちます。

この記事では、これらのテンプレートとスクリプトを使用してラボの作成、変更、デプロイを自動化するプロセスを説明します。 この記事では、PowerShell を使用して DevTest ラボでいくつかの一般的タスクを実行する方法の詳細も紹介します。

## <a name="step-1-gather-your-templates-and-scripts"></a>ステップ 1: テンプレートとスクリプトを収集する
あらかじめ定義された [Azure Resource Manager テンプレート](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)と [PowerShell スクリプト](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts)が、当社が公開する [GitHub リポジトリ](https://github.com/Azure/azure-devtestlab)にあります。 これらをそのまま使用するか、ニーズに合わせてカスタマイズして、独自の[プライベート Git リポジトリ](devtest-lab-add-artifact-repo.md)に保管できます。

## <a name="step-2-modify-your-azure-resource-manager-template"></a>ステップ 2: Azure Resource Manager テンプレートの変更
これまでテンプレートを作成したことがない場合は、「[初めての Azure Resource Manager テンプレートを作成する](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)」の手順に従うことができます。

さらに、「[Azure Resource Manager テンプレートを作成するためのベスト プラクティス](../azure-resource-manager/templates/template-best-practices.md)」では、信頼性が高く使いやすい Azure Resource Manager テンプレートの作成に役立つ多くのガイドラインと推奨事項を紹介しています。 通常、紹介されているいずれかのアプローチや例のバリエーションを使用し、ニーズに応じてテンプレートを変更します。

## <a name="step-3-deploy-resources-with-powershell"></a>ステップ 3: PowerShell によりリソースをデプロイする
テンプレートやスクリプトをカスタマイズした後で、[Resource Manager テンプレートと Azure PowerShell を使用してリソースをデプロイ](../azure-resource-manager/templates/deploy-powershell.md)するために必要な手順に従ってください。 この記事では、Azure PowerShell と Azure Resource Manager テンプレートを使用してリソースを Azure にデプロイする場合の概要を説明します。


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>DevTest ラボで PowerShell を使用して実行できる一般的なタスク
PowerShell を使用して自動化できる一般的タスクが他にも多数あります。 ドキュメントの以下のセクションで、これらのタスクを実行するために必要な手順を概説します。

* [PowerShell を使用して VHD ファイルからカスタム イメージを作成する](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [PowerShell を使用してラボのストレージ アカウントに VHD ファイルをアップロードします](devtest-lab-upload-vhd-using-powershell.md)
* [PowerShell を使用して、ラボに外部ユーザーを追加します。](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [PowerShell を使用してラボ カスタム ロールを作成する](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>次のステップ
* カスタマイズしたテンプレートまたはスクリプトを保存するための[プライベート Git リポジトリ](devtest-lab-add-artifact-repo.md)を作成する方法を学習します。
* [Azure クイックスタート テンプレート ギャラリーから Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates)を検索します。
