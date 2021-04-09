---
title: FileUpload UI 要素
description: Azure Portal の Microsoft.Common.FileUpload UI 要素について説明します。 管理対象アプリケーションをデプロイするとき、ユーザーがファイルをアップロードできるようにします。
author: tfitzmac
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: tomfitz
ms.openlocfilehash: c4eb3972be85ac7e291380f64127c20424b0d0f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87004284"
---
# <a name="microsoftcommonfileupload-ui-element"></a>Microsoft.Common.FileUpload UI 要素

アップロードするファイルを 1 つまたは複数指定できるコントロールです。

## <a name="ui-sample"></a>UI サンプル

![Microsoft.Common.FileUpload](./media/managed-application-elements/microsoft-common-fileupload.png)

## <a name="schema"></a>スキーマ

```json
{
  "name": "element1",
  "type": "Microsoft.Common.FileUpload",
  "label": "Some file upload",
  "toolTip": "",
  "constraints": {
    "required": true,
    "accept": ".doc,.docx,.xml,application/msword"
  },
  "options": {
    "multiple": false,
    "uploadMode": "file",
    "openMode": "text",
    "encoding": "UTF-8"
  },
  "visible": true
}
```

## <a name="sample-output"></a>サンプル出力

options.multiple が false で options.uploadMode が file である場合、出力にはファイルの内容が JSON 文字列として含まれます。

```json
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."
```

options.multiple が true で options.uploadMode が file である場合、出力にはファイルの内容が JSON 配列として含まれます。

```json
[
  "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",
  "Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
  "Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.",
  "Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
]
```

options.multiple が false で options.uploadMode が url である場合、出力には URL が JSON 文字列として含まれます。

```json
"https://myaccount.blob.core.windows.net/pictures/profile.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
```

options.multiple が true で options.uploadMode が url である場合、出力には URL の一覧が JSON 配列として含まれます。
```json
[
  "https://myaccount.blob.core.windows.net/pictures/profile1.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile2.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile3.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
]
```

CreateUiDefinition をテストする際、一部のブラウザー (Google Chrome など) では、Microsoft.Common.FileUpload 要素によって生成された URL がブラウザー コンソールで切り詰められます。 完全な URL をコピーするには、個々のリンクを右クリックしなければならない場合があります。

## <a name="remarks"></a>解説

- `constraints.accept` は、ブラウザーのファイル ダイアログに表示されるファイルの種類を指定します。 [HTML5 の仕様](https://html.spec.whatwg.org/multipage/input.html#attr-input-accept)を参照して、使用できる値を確認します。 既定値は **null** です。
- `options.multiple` が **true** に設定されている場合、ユーザーはブラウザーのファイル ダイアログ内の複数のファイルを選択できます。 既定値は **false** です。
- この要素は、`options.uploadMode` の値に基づいた 2 つのモードでのファイルのアップロードをサポートしています。 **file** を指定すると、出力にファイルの内容が BLOB として含まれます。 **url** を指定すると、ファイルは一時的な場所にアップロードされ、出力には BLOB の URL が含まれます。 一時的な BLOB は 24 時間後に消去されます。 既定値は **file** です。
- アップロードされたファイルは保護されます。 出力 URL には、デプロイ中にファイルにアクセスするための [SAS トークン](../../storage/common/storage-sas-overview.md?toc=/azure/storage/blobs/toc.json)が含まれています。
- `options.openMode` の値はファイルの読み取り方法を決定します。 ファイルがプレーン テキストである場合は **text** を指定し、そうでない場合は **binary** を指定します。 既定値は **text** です。
- `options.uploadMode` が **file** に設定され、`options.openMode` が **binary** に設定されている場合、出力は Base64 でエンコードされます。
- `options.encoding` はファイルを読み取る際に使用するエンコード方法を指定します。 既定値は **UTF-8** であり、これは `options.openMode` が **text** に設定されている場合にのみ使用されます。

## <a name="next-steps"></a>次のステップ

* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
