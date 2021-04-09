---
title: StorageBlobSelector UI 要素
description: Azure Portal の Microsoft.Storage.StorageBlobSelector UI 要素について説明します。
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 1085b70df67a3f16a7f7f8c5ae85c9ab271b62ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92754295"
---
# <a name="microsoftstoragestorageblobselector-ui-element"></a>Microsoft.Storage.StorageBlobSelector UI 要素

Azure ストレージ アカウントから BLOB を選択するためのコントロールです。

## <a name="ui-sample"></a>UI サンプル

ユーザーには、使用可能なストレージ BLOB を参照するためのオプションが表示されます。

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-browse.png" alt-text="Microsoft.Storage.StorageBlobSelector - 参照":::

**[参照]** を選択した後、ユーザーはストレージ アカウントを選択できます。

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-select.png" alt-text="Microsoft.Storage.StorageBlobSelector - ストレージを選択する":::

ユーザーは、ストレージ アカウント内のコンテナーを確認し、1 つを選択できます。

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-containers.png" alt-text="Microsoft.Storage.StorageBlobSelector - コンテナーを選択する":::

ユーザーは、コンテナーからファイルを選択できます。

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-file.png" alt-text="Microsoft.Storage.StorageBlobSelector - ファイル":::

コントロールが更新され、選択したファイルの名前が表示されます。

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-result.png" alt-text="Microsoft.Storage.StorageBlobSelector - 選択したファイルが表示される":::

## <a name="schema"></a>スキーマ

```json
{
  "name": "storageBlobSelection",
  "type": "Microsoft.Storage.StorageBlobSelector",
  "visible": true,
  "toolTip": "Select storage blob",
  "label": "Package (.zip, .cspkg)",
  "options": {
    "text": "Select Package"
  },
  "constraints": {
    "allowedFileExtensions": [ "zip", "cspkg" ]
  }
}
```

## <a name="sample-output"></a>サンプル出力

```json
{
  "blobName": "test.zip",
  "sasUri": "https://azstorageaccnt1.blob.core.windows.net/container1/test.zip?sp=r&se=2020-10-10T07:46:22Z&sv=2019-12-12&sr=b&sig=X4EL8ZsRmiP1TVxkVfTcGyMj2sHg1zCbFBXsDmnNOyg%3D"
}

```

## <a name="remarks"></a>解説

**constraints.allowedFileExtensions** プロパティでは、許可されているファイルの種類を指定します。

## <a name="next-steps"></a>次のステップ

* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
