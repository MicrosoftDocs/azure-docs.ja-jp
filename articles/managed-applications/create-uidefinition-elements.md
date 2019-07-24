---
title: Azure の UI 定義作成要素 | Microsoft Docs
description: Azure Portal の UI 定義を作成するときに使う要素について説明します。
services: managed-applications
documentationcenter: na
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2018
ms.author: tomfitz
ms.openlocfilehash: 895b5cfb121347f9400d1aa315fef05d9088c55c
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2018
ms.locfileid: "46366473"
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
| defaultValue | いいえ  | 要素の既定値。 要素の種類によっては複合的な既定値がサポートされるため、この値はオブジェクトになる場合があります。 |
| toolTip | いいえ  | 要素のヒントに表示するテキスト。 `label` と同様、要素によっては複数のヒント文字列がサポートされます。 マークダウン構文を使ってインライン リンクを埋め込むことができます。
| constraints | いいえ  | 要素の検証動作をカスタマイズする目的で使う 1 つまたは複数のプロパティ。 constraints でサポートされるプロパティは要素の種類によって異なります。 要素の種類によっては検証動作のカスタマイズがサポートされていません。その場合、constraints プロパティは存在しません。 |
| options | いいえ  | 要素の動作をカスタマイズする補足的なプロパティ。 `constraints` と同様、サポートされるプロパティは要素の種類によって異なります。 |
| visible | いいえ  | 要素が表示されるかどうかを示します。 `true` の場合、要素と該当する子要素が表示されます。 既定値は `true` です。 このプロパティの値を動的に制御するには[論理関数](create-uidefinition-functions.md#logical-functions)を使います。

## <a name="elements"></a>要素

それぞれの要素のドキュメントには、UI サンプル、スキーマ、要素の (通常、検証およびサポートされるカスタマイズに関連した) 動作についての備考、サンプル出力が記載されています。

- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft.Common.InfoBox](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft.Common.TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>次の手順
UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
