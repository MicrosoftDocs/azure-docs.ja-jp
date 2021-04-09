---
title: Azure DevTest Labs で入れ子になったテンプレート環境をデプロイする
description: 入れ子になった Azure Resource Manager テンプレートをデプロイして Azure DevTest Labs に環境を提供する方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 39002e286fafd4f813333a14ed86256a517897e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85481342"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>テスト環境向けに入れ子になったAzure Resource Manager テンプレートをデプロイする
入れ子になったデプロイを使用すると、メインの Azure Resource Manager テンプレート内から別の Resource Manager テンプレートを実行できます。 これを使用することにより、デプロイを、目的別に的を絞った一連のテンプレートに分解できます。 これには、テスト、再利用、読みやすさの点でメリットがあります。 [Azure リソースのデプロイ時のリンクされたテンプレートの使用](../azure-resource-manager/templates/linked-templates.md)に関する記事では、このソリューションの概要とコード サンプルが紹介されています。 この記事では、Azure DevTest Labs に固有の例を示します。 

## <a name="key-parameters"></a>キー パラメーター
ゼロから独自の Resource Manager テンプレートを作成することもできますが、Visual Studio で [Azure リソース グループ プロジェクト](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)を使用することをお勧めします。このプロジェクトを使用すると、簡単にテンプレートを開発してデバッグできます。 azuredeploy.json に入れ子になったデプロイ リソースを追加すると、Visual Studio によって、テンプレートをより柔軟にするいくつかの項目が追加されます。 これらの項目には、セカンダリ テンプレートとパラメーター ファイルを含むサブフォルダー、メイン テンプレート ファイル内の変数名、および新しいファイルの保存場所を表す 2 つのパラメーターが含まれます。 **_artifactsLocation** と **_artifactsLocationSasToken** は DevTest Labs によって使用されるキー パラメーターです。 

DevTest Labs が環境を使用するしくみになじみがない場合は、「[Azure Resource Manager テンプレートを使用してマルチ VM 環境と PaaS リソースを作成する](devtest-lab-create-environment-from-arm.md)」を参照してください。 テンプレートは、DevTest Labs のラボにリンクされているリポジトリに格納されます。 これらのテンプレートを使用して新しい環境を作成すると、ファイルがラボの Azure Storage コンテナーに移動されます。 入れ子になったファイルを識別してコピーできるようにするために、DevTest Labs は _artifactsLocation と _artifactsLocationSasToken パラメーターを識別し、サブフォルダーをストレージ コンテナーまでコピーします。 次に、その場所と Shared Access Signature (SaS) トークンを自動的にパラメーターに挿入します。 

## <a name="nested-deployment-example"></a>入れ子になったデプロイの例
入れ子になったデプロイの単純な例を次に示します。

```json

"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": {
    "_artifactsLocation": {
        "type": "string"
    },
    "_artifactsLocationSasToken": {
        "type": "securestring"
    }},
"variables": {
    "NestOneTemplateFolder": "nestedtemplates",
    "NestOneTemplateFileName": "NestOne.json",
    "NestOneTemplateParametersFileName": "NestOne.parameters.json"},
    "resources": [
    {
        "name": "NestOne",
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2016-09-01",
        "dependsOn": [ ],
        "properties": {
            "mode": "Incremental",
            "templateLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            },
            "parametersLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateParametersFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            }
        }    
    }],
"outputs": {}
```

このテンプレートが格納されているリポジトリ内のフォルダーに、**NestOne.json** ファイルおよび **NestOne.parameters.json** ファイルを含むサブフォルダー `nestedtemplates` があります。 **azuredeploy.json** には、成果物の場所、入れ子になったテンプレート のフォルダー、入れ子になったテンプレート ファイルの名前を使用して、テンプレートの URI が構築されています。 同様に、パラメーターの URI が、成果物の場所、入れ子になったテンプレートのフォルダー、および入れ子になったテンプレートのパラメーター ファイルを使用して構築されています。 

Visual Studio での同じプロジェクト構造の画像を次に示します。 

![Visual Studio でのプロジェクト構造](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

プライマリ フォルダーには他のフォルダーを追加できますが、これより深いレベルには追加できません。 

## <a name="next-steps"></a>次のステップ
環境の詳細については、次の記事を参照してください。 

- [Azure Resource Manager テンプレートを使用してマルチ VM 環境と PaaS リソースを作成する](devtest-lab-create-environment-from-arm.md)
- [Azure DevTest Labs でパブリック環境を構成して使用する](devtest-lab-configure-use-public-environments.md)
- [Azure DevTest Labs のラボの仮想ネットワークに環境を接続する](connect-environment-lab-virtual-network.md)