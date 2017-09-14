---
title: "Azure マネージ アプリケーションの CredentialsCombo UI 要素 | Microsoft Docs"
description: "Azure マネージ アプリケーションの Microsoft.Compute.CredentialsCombo UI 要素について説明します。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/12/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: 254f383ee6f7cb9f7051fa135d85319a22c3c369
ms.contentlocale: ja-jp
ms.lasthandoff: 05/13/2017

---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Microsoft.Compute.CredentialsCombo UI 要素
Windows と Linux のパスワードと SSH 公開キーの検証が組み込まれているコントロールのグループです。 この要素は、[Azure マネージ アプリケーションを作成する](managed-application-publishing.md)ときに使用します。

## <a name="ui-sample"></a>UI サンプル
![Microsoft.Compute.CredentialsCombo](./media/managed-application-elements/microsoft.compute.credentialscombo.png)

## <a name="schema"></a>スキーマ
`osPlatform` が **Windows** の場合、次のスキーマが使用されます。
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": {
    "password": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{8,}$",
    "customValidationMessage": "The password must contain at least 8 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false
  },
  "osPlatform": "Windows",
  "visible": true
}
```

`osPlatform` が **Linux** の場合、次のスキーマが使用されます。
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "authenticationType": "Authentication type",
    "password": "Password",
    "confirmPassword": "Confirm password",
    "sshPublicKey": "SSH public key"
  },
  "toolTip": {
    "authenticationType": "",
    "password": "",
    "sshPublicKey": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{8,}$",
    "customValidationMessage": "The password must contain at least 8 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false,
    "hidePassword": false
  },
  "osPlatform": "Linux",
  "visible": true
}
```

## <a name="remarks"></a>解説
- `osPlatform` は指定する必要があり、**Windows** と **Linux** のいずれかを使用できます。
- `constraints.required` が **true** に設定されている場合、パスワードまたは SSH 公開キーのテキスト ボックスには、正常に検証を完了できる値を含める必要があります。 既定値は **true**です。
- `options.hideConfirmation` が **true** に設定されている場合、ユーザーのパスワードを確認するための 2 つ目のテキスト ボックスは表示されません。 既定値は **false** です。
- `options.hidePassword` が **true** に設定されている場合、パスワード認証を使用するオプションは表示されません。 これは `osPlatform` が **Linux** であるときのみ使用できます。 既定値は **false** です。
- `customPasswordRegex` プロパティを使用して、許可されたパスワードに対する追加の制約を実装できます。 `customValidationMessage` 内の文字列は、パスワードのカスタム検証が失敗したときに表示されます。 これらのプロパティの既定値は両方とも **null** です。

## <a name="sample-output"></a>サンプル出力
`osPlatform` が **Windows** の場合、またはユーザーが SSH 公開キーではなくパスワードを指定した場合、出力は次のようになります。

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rd",
}
```

ユーザーが SSH 公開キーを指定した場合、出力は次のようになります。
```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="next-steps"></a>次のステップ
* マネージ アプリケーションの概要については、「[Azure マネージ アプリケーションの概要](managed-application-overview.md)」を参照してください。
* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](managed-application-createuidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](managed-application-createuidefinition-elements.md)」を参照してください。

