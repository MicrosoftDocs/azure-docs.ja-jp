---
title: ポータル ペインの CreateUiDefinition.json ファイル
description: Azure portal のユーザー インターフェイス定義を作成する方法について説明します。 Azure Managed Applications の定義時に使用されます。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: 327fa1d7eb73d8e65bb4f81c1dff0fe2bec2913b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "89319571"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>Azure マネージド アプリケーションの作成エクスペリエンスのための CreateUiDefinition.json

このドキュメントでは、**createUiDefinition.json** ファイルの主要な概念について説明します。 このファイルは、マネージド アプリケーションを作成する際にユーザー インターフェイスを定義するために Azure portal で使用されます。

テンプレートは次のとおりです

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
        "config": {
            "isWizard": false,
            "basics": { }
        },
        "basics": [ ],
        "steps": [ ],
        "outputs": { },
        "resourceTypes": [ ]
    }
}
```

`CreateUiDefinition` には常に 3 つのプロパティが含まれます。

* handler
* version
* parameters

handler は常に `Microsoft.Azure.CreateUIDef` とする必要があります。また、サポートされている最新のバージョンは `0.1.2-preview` です。

parameters プロパティのスキーマは、指定した handler と version の組み合わせによって異なります。 マネージド アプリケーションでサポートされているプロパティは `config`、`basics`、`steps`、`outputs` です。 `config` は、`basics` ステップの既定の動作をオーバーライドする必要がある場合にのみ使用します。 basics プロパティと steps プロパティには、Azure Portal に表示される "[要素](create-uidefinition-elements.md)" (テキスト ボックス、ドロップダウンなど) が指定されます。 outputs プロパティは、指定された要素の出力値を Azure Resource Manager テンプレートのパラメーターに対してマップする目的で使われます。

`$schema` は、指定することが推奨されますが、必須ではありません。 指定する場合は、`$schema` の URI 内のバージョンと `version` の値とを一致させる必要があります。

JSON エディターを使用して createUiDefinition 定義を作成した後、[createUiDefinition 定義サンドボックス](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade)でテストしてそれをプレビューできます。 このサンドボックスの詳細については、[Azure Managed Applications のポータル インターフェイスのテスト](test-createuidefinition.md)に関するページを参照してください。

## <a name="config"></a>Config

`config` プロパティは省略可能です。 それを使用して基本ステップの既定動作をオーバーライドするか、ステップバイステップのウィザードとしてインターフェイスを設定します。 `config` が使用される場合、それは **createUiDefinition.json** ファイルの `parameters` セクションの最初のプロパティです。 利用可能なプロパティの例を次に示します。

```json
"config": {
    "isWizard": false,
    "basics": {
        "description": "Customized description with **markdown**, see [more](https://www.microsoft.com).",
        "subscription": {
            "constraints": {
                "validations": [
                    {
                        "isValid": "[expression for checking]",
                        "message": "Please select a valid subscription."
                    },
                    {
                        "permission": "<Resource Provider>/<Action>",
                        "message": "Must have correct permission to complete this step."
                    }
                ]
            },
            "resourceProviders": [
                "<Resource Provider>"
            ]
        },
        "resourceGroup": {
            "constraints": {
                "validations": [
                    {
                        "isValid": "[expression for checking]",
                        "message": "Please select a valid resource group."
                    }
                ]
            },
            "allowExisting": true
        },
        "location": {
            "label": "Custom label for location",
            "toolTip": "provide a useful tooltip",
            "resourceTypes": [
                "Microsoft.Compute/virtualMachines"
            ],
            "allowedValues": [
                "eastus",
                "westus2"
            ],
            "visible": true
        }
    }
},
```

### <a name="wizard"></a>ウィザード

`isWizard` プロパティでは、次のステップに進む前に各ステップの検証に成功することを要求できます。 `isWizard` プロパティが指定されていない場合、既定値は **false** であり、ステップバイステップの検証は必要ありません。

`isWizard` が有効になっている場合、**true** に設定すると、 **[基本]** タブが利用できて、他のタブはすべて無効になります。 **[次へ]** ボタンが選択されているとき、タブの検証に成功したか失敗したかがタブのアイコンで示されます。 タブの必須フィールドに入力し、 **[次へ]** ボタンを押して確認すると、次のタブに移動します。すべてのタブで検証に成功したら、 **[確認と作成]** ページに進み、 **[作成]** ボタンを選択してデプロイを開始できます。

:::image type="content" source="./media/create-uidefinition-overview/tab-wizard.png" alt-text="タブ ウィザード":::

### <a name="override-basics"></a>基本をオーバーライドする

基本構成では、基本ステップをカスタマイズできます。

`description` には、リソースの説明として、マークダウンを使用した文字列を指定します。 複数行形式やリンクがサポートされます。

`subscription` 要素と `resourceGroup` 要素を使用すると、検証を追加で指定することができます。 検証を指定するための構文は、[テキスト ボックス](microsoft-common-textbox.md)のカスタム検証と同じです。 サブスクリプションまたはリソース グループに対する `permission` の検証を指定することもできます。  

subscription コントロールには、一連のリソース プロバイダーの名前空間を指定できます。 たとえば、**Microsoft.Compute** を指定できます。 このリソース プロバイダーをサポートしないサブスクリプションをユーザーが選択すると、エラー メッセージが表示されます。 そのサブスクリプションにリソース プロバイダーが登録されていない場合や、リソース プロバイダーを登録するためのアクセス許可がユーザーにない場合に、エラーが発生します。  

リソース グループ コントロールには、`allowExisting` のオプションがあります。 `true` の場合、ユーザーは、既にリソースが存在するリソース グループを選択することができます。 ソリューション テンプレートで、新しい (空の) リソース グループを選択するよう既定の動作でユーザーに要求する場合、このフラグが最適です。 その他のシナリオでは、ほとんどの場合、このプロパティを指定する必要はありません。  

`location` には、場所のコントロールに関してオーバーライドしたいプロパティを指定します。 オーバーライドされていないプロパティはすべて、その既定値に設定されます。 `resourceTypes` には、リソースの種類の完全修飾名を含んだ文字列の配列を指定できます。 location のオプションは、そのリソースの種類をサポートするリージョンのみに制限されます。 `allowedValues`  には、リージョンの文字列の配列を指定できます。 ドロップダウンには、それらのリージョンのみが表示されます。 `allowedValues`  と  `resourceTypes` の両方を設定できます。 その結果は、両方のリストの積集合となります。 最後に、`visible` プロパティを使用して、条件付きで、または完全に、場所のドロップダウンを無効にすることができます。  

## <a name="basics"></a>基本

**basics** ステップは、Azure portal がファイルを解析するときに生成される最初のステップです。 basics ステップでは、サブスクリプション、リソース グループ、デプロイの場所をユーザーが既定で選択できます。

:::image type="content" source="./media/create-uidefinition-overview/basics.png" alt-text="basics の既定値":::

このセクションには、他にも要素を追加できます。 可能な場合、デプロイ全体のパラメーター (クラスターの名前、管理者の資格情報など) を照会する要素を追加します。

次の例は、既定の要素に追加されたテキスト ボックスを示しています。

```json
"basics": [
    {
        "name": "textBox1",
        "type": "Microsoft.Common.TextBox",
        "label": "Textbox on basics",
        "defaultValue": "my text value",
        "toolTip": "",
        "visible": true
    }
]
```

## <a name="steps"></a>手順

basics の後に表示するステップ (0 個以上) は steps プロパティに追加します。 各ステップに少なくとも 1 つの要素を記述します。 デプロイするアプリケーションのロールまたはレベルごとにステップを追加することを検討してください。 たとえば、マスター ノードの入力用のステップと、クラスター内のワーカー ノード用のステップを追加します。

```json
"steps": [
    {
        "name": "demoConfig",
        "label": "Configuration settings",
        "elements": [
          ui-elements-needed-to-create-the-instance
        ]
    }
]
```

## <a name="outputs"></a>出力

`outputs` プロパティは、Azure Portal が `basics` と `steps` の要素を Azure Resource Manager デプロイ テンプレートのパラメーターにマッピングする際に使われます。 このディクショナリのキーはテンプレート パラメーターの名前で、ディクショナリの値は、参照された要素の出力オブジェクトのプロパティです。

マネージド アプリケーションのリソース名を設定するには、`applicationResourceName` という名前の値を outputs プロパティに含める必要があります。 この値を設定しない場合は、アプリケーションによって名前に GUID が割り当てられます。 ユーザーに名前を要求するテキスト ボックスをユーザー インターフェイスに追加できます。

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="resource-types"></a>リソースの種類

使用可能な場所にフィルターを適用して、デプロイするリソースの種類がサポートされている場所に限定するには、リソースの種類の配列を指定します。 リソースの種類を複数指定した場合、そのすべての種類のリソースをサポートする場所のみが返されます。 このプロパティは省略可能です。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
        "resourceTypes": ["Microsoft.Compute/disks"],
        "basics": [
          ...
```  

## <a name="functions"></a>関数

CreateUiDefinition には、要素の入力と出力を操作するための[関数](create-uidefinition-functions.md)と、条件文などの機能が用意されています。 これらの関数は、構文と機能の両方において、Azure Resource Manager のテンプレートの関数に似ています。

## <a name="next-steps"></a>次のステップ

createUiDefinition.json ファイルには、それ自体に単純なスキーマが存在します。 その実際の深さは、すべてのサポートされている要素と関数に由来します。 これらの項目については、次の記事で詳しく説明します。

- [要素](create-uidefinition-elements.md)
- [関数](create-uidefinition-functions.md)

CreateUiDefinition の現在の JSON スキーマは、`https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json` から入手できます。

ユーザー インターフェイス ファイルの例については、[createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json) を参照してください。
