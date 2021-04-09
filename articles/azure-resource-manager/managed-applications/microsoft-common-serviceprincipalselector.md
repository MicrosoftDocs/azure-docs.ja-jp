---
title: ServicePrincipalSelector UI 要素
description: Azure portal 用の Microsoft.Common.ServicePrincipalSelector UI 要素について説明します。 アプリケーションを選択するコントロールと、パスワードまたは証明書の拇印を入力するテキスト ボックスを提供します。
author: tfitzmac
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: tomfitz
ms.openlocfilehash: 2fdbbad467d8c762db485fc7935e9cef78313fd0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96184452"
---
# <a name="microsoftcommonserviceprincipalselector-ui-element"></a>Microsoft.Common.ServicePrincipalSelector UI 要素

ユーザーが既存の[サービス プリンシパル](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)を選択したり、新しいアプリケーションを登録したりできるようにするコントロールです。 **[新規作成]** を選択したときに、新しいアプリケーションを登録する手順に従います。 既存のアプリケーションを選択すると、コントロールにより、パスワードまたは証明書の拇印を入力するテキスト ボックスが提供されます。

## <a name="ui-samples"></a>UI サンプル

既定のアプリケーションを使用するか、新しいアプリケーションを作成するか、既存のアプリケーションを使用することができます。

### <a name="use-default-application-or-create-new"></a>既定のアプリケーションを使用するか、新規に作成する

既定のビューは `defaultValue` プロパティの値で決まります。また、 **[Service Principal Type]\(サービス プリンシパルの種類\)** は **[新規作成]** に設定されています。 `principalId` プロパティに有効なグローバル一意識別子 (GUID) が含まれている場合、コントロールでは、アプリケーションの `objectId` を検索します。 ユーザーがコントロールから選択しない場合は、既定値が適用されます。

新しいアプリケーションを登録する場合は、 **[Change selection]\(選択内容の変更\)** を選択すると、 **[アプリケーションの登録]** ダイアログ ボックスが表示されます。 **名前**、**サポートされているアカウントの種類** を入力して、 **[登録]** ボタンを選択します。

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-default.png" alt-text="Microsoft.Common.ServicePrincipalSelector の初期表示。":::

新しいアプリケーションを登録したら、 **[認証の種類]** を使用して、パスワードまたは証明書の拇印を入力します。

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-authenticate.png" alt-text="Microsoft.Common.ServicePrincipalSelector の認証。":::

### <a name="use-existing-application"></a>既存のアプリケーションを使用する

既存のアプリケーションを使用するには、 **[既存のものを選択]** を選び、 **[選択の作成]** を選択します。 アプリケーションの名前を検索するには **[アプリケーションの選択]** ダイアログ ボックスを使用します。 結果からアプリケーションを選択し、 **[選択]** ボタンを選択します。 アプリケーションを選択すると、コントロールに、パスワードまたは証明書の拇印を入力するための **[認証の種類]** が表示されます。

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-existing.png" alt-text="Microsoft.Common.ServicePrincipalSelector の既存のアプリケーションの選択。":::

## <a name="schema"></a>スキーマ

```json
{
  "name": "ServicePrincipal",
  "type": "Microsoft.Common.ServicePrincipalSelector",
  "label": {
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type",
    "sectionHeader": "Service Principal"
  },
  "toolTip": {
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type"
  },
  "defaultValue": {
    "principalId": "<default guid>",
    "name": "(New) default App Id"
  },
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideCertificate": false
  },
  "visible": true
}
```

## <a name="remarks"></a>解説

- 必須のプロパティは次のとおりです。
  - `name`
  - `type`
  - `label`
  - `defaultValue`:既定の `principalId` と `name` を指定します。

- 省略可能なプロパティは次のとおりです。
  - `toolTip`:各ラベルにヒントの `infoBalloon` をアタッチします。
  - `visible`:コントロールを非表示にするか、表示します。
  - `options`:証明書の拇印オプションを使用可能にする必要があるかどうかを指定します。
  - `constraints`:パスワード検証用の正規表現による制約。

## <a name="example"></a>例

`Microsoft.Common.ServicePrincipalSelector` コントロールの例を次に示します。 `defaultValue` プロパティでは、既定のアプリケーション識別子である GUID のプレースホルダーとして、`principalId` が `<default guid>` に設定されています。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "SPNcontrol",
        "label": "SPNcontrol",
        "elements": [
          {
            "name": "ServicePrincipal",
            "type": "Microsoft.Common.ServicePrincipalSelector",
            "label": {
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type",
              "sectionHeader": "Service Principal"
            },
            "toolTip": {
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type"
            },
            "defaultValue": {
              "principalId": "<default guid>",
              "name": "(New) default App Id"
            },
            "constraints": {
              "required": true,
              "regex": "^[a-zA-Z0-9]{8,}$",
              "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
            },
            "options": {
              "hideCertificate": false
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "appId": "[steps('SPNcontrol').ServicePrincipal.appId]",
      "objectId": "[steps('SPNcontrol').ServicePrincipal.objectId]",
      "password": "[steps('SPNcontrol').ServicePrincipal.password]",
      "certificateThumbprint": "[steps('SPNcontrol').ServicePrincipal.certificateThumbprint]",
      "newOrExisting": "[steps('SPNcontrol').ServicePrincipal.newOrExisting]",
      "authenticationType": "[steps('SPNcontrol').ServicePrincipal.authenticationType]"
    }
  }
}
```

## <a name="example-output"></a>出力例

`appId` は、ユーザーが選択または作成したアプリケーション登録の ID です。 `objectId` は、選択したアプリケーション登録のために構成されたサービス プリンシパルのオブジェクト ID の配列です。

コントロールから何も選択されていないときの `newOrExisting` プロパティの値は、**new** です。

```json
{
  "appId": {
    "value": "<default guid>"
  },
  "objectId": {
    "value": ["<default guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "new"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

**[新規作成]** を選択するか、コントロールから既存のアプリケーションを選択したときの `newOrExisting` プロパティの値は、**existing** です。

```json
{
  "appId": {
    "value": "<guid>"
  },
  "objectId": {
    "value": ["<guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "existing"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

## <a name="next-steps"></a>次のステップ

- UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
- UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。