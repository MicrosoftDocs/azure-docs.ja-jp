---
title: UI 定義要素を作成する
description: Azure Portal の UI 定義を作成するときに使う要素について説明します。
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 3e8bdd85c112d90fd5e10f6e65e48ca6b3f71b6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94410744"
---
# <a name="createuidefinition-elements"></a>CreateUiDefinition の要素

この記事では、CreateUiDefinition でサポートされるすべての要素のスキーマとプロパティについて説明します。

## <a name="schema"></a>スキーマ

ほとんどの要素のスキーマは、次のようになっています。

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "my value",
  "toolTip": "Provide a descriptive name.",
  "constraints": {},
  "options": {},
  "visible": true
}
```

| プロパティ | 必須 | 説明 |
| -------- | -------- | ----------- |
| name | はい | 要素の特定のインスタンスを参照するための内部的な識別子。 要素名が最もよく使用されるのは `outputs` で、指定した要素の出力値がテンプレートのパラメーターにマッピングされます。 これを使って要素の出力値を別の要素の `defaultValue` にバインドすることもできます。 |
| type | はい | レンダリングする要素の UI コントロール。 サポートされる型の一覧については、「[要素](#elements)」を参照してください。 |
| label | はい | 要素の表示テキスト。 要素の種類によっては複数のラベルが含まれるため、この値は複数の文字列を含んだオブジェクトになる場合があります。 |
| defaultValue | いいえ | 要素の既定値。 要素の種類によっては複合的な既定値がサポートされるため、この値はオブジェクトになる場合があります。 |
| toolTip | いいえ | 要素のヒントに表示するテキスト。 `label` と同様、要素によっては複数のヒント文字列がサポートされます。 マークダウン構文を使ってインライン リンクを埋め込むことができます。
| constraints | いいえ | 要素の検証動作をカスタマイズする目的で使う 1 つまたは複数のプロパティ。 constraints でサポートされるプロパティは要素の種類によって異なります。 要素の種類によっては検証動作のカスタマイズがサポートされていません。その場合、constraints プロパティは存在しません。 |
| options | いいえ | 要素の動作をカスタマイズする補足的なプロパティ。 `constraints` と同様、サポートされるプロパティは要素の種類によって異なります。 |
| visible | いいえ | 要素が表示されるかどうかを示します。 `true` の場合、要素と該当する子要素が表示されます。 既定値は `true` です。 このプロパティの値を動的に制御するには[論理関数](create-uidefinition-functions.md#logical-functions)を使います。

## <a name="elements"></a>要素

それぞれの要素のドキュメントには、UI サンプル、スキーマ、要素の (通常、検証およびサポートされるカスタマイズに関連した) 動作についての備考、サンプル出力が記載されています。

- [Microsoft.Common.CheckBox](microsoft-common-checkbox.md)
- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft.Common.EditableGrid](microsoft-common-editablegrid.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft.Common.InfoBox](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft.Common.ServicePrincipalSelector](microsoft-common-serviceprincipalselector.md)
- [Microsoft.Common.Slider](microsoft-common-slider.md)
- [Microsoft.Common.TagsByResource](microsoft-common-tagsbyresource.md)
- [Microsoft.Common.TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft.KeyVault.KeyVaultCertificateSelector](microsoft-keyvault-keyvaultcertificateselector.md)
- [Microsoft.managedidentity.IdentitySelector](microsoft-managedidentity-identityselector.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Solutions.ArmApiControl](microsoft-solutions-armapicontrol.md)
- [Microsoft.Solutions.ResourceSelector](microsoft-solutions-resourceselector.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)
- [Microsoft.Storage.StorageBlobSelector](microsoft-storage-storageblobselector.md)

## <a name="next-steps"></a>次のステップ

UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
