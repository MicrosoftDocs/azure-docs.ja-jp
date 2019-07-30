---
title: プロジェクトの検索、編集、削除方法 - Custom Translator
titleSuffix: Azure Cognitive Services
description: Custom Translator には、効率的な方法でプロジェクトを管理するさまざまな方法が用意されています。 複数のプロジェクトを作成し、条件に基づいて検索し、プロジェクトを編集することができます。 Custom Translator でプロジェクトを削除することもできます。
author: swmachan
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 3a19feb76b5f5e65f891dc9f4e9dbfbd642d15b6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447939"
---
# <a name="search-edit-and-delete-projects"></a>プロジェクトの検索、編集、削除

Custom Translator には、効率的な方法でプロジェクトを管理する複数の方法が用意されています。 多数のプロジェクトを作成し、条件に基づいて検索し、プロジェクトを編集することができます。 Custom Translator でプロジェクトを削除することもできます。  

## <a name="search-and-filter-projects"></a>プロパティの検索とフィルター処理

フィルター ツールを使用すると、さまざまなフィルター条件でプロジェクトを検索できます。 プロジェクト名、状態、ソース言語、ターゲット言語、プロジェクトのカテゴリなどのフィルター処理を行います。

1.  フィルター ボタンをクリックします。

    ![プロジェクトを検索する](media/how-to/how-to-search-project.png)

1.  プロジェクト名、状態、ソース言語、ターゲット言語、およびカテゴリの各フィールドのいずれか (またはすべて) を指定してフィルター処理できます。

2.  [適用] をクリックします。

    ![プロジェクトの検索のフィルター オプション](media/how-to/how-to-search-project-filters.png)

3.  フィルターをクリアしてすべてのプロジェクトを表示するには、[クリア] をタップします。


## <a name="edit-a-project"></a>プロジェクトを編集する

Custom Translator には、プロジェクトの名前と説明を編集する機能があります。 カテゴリ、ソース言語、ターゲット言語など、プロジェクトの他のメタデータは編集できません。 次にプロジェクトの編集手順を説明します。

1.  プロジェクトをマウスでポイントしたときに表示される鉛筆アイコンをクリックします。

    ![プロジェクトの編集](media/how-to/how-to-edit-project.png)

2.  このダイアログでは、プロジェクト名またはプロジェクトの説明を変更できますが、プロジェクトのラベル、カテゴリ、または言語ペアは変更できません。

    ![[プロジェクトの編集] ダイアログ](media/how-to/how-to-edit-project-dialog.png)

3.  フィルター ボタンをクリックします。

## <a name="delete-a-project"></a>プロジェクトを削除する

不要になったプロジェクトは削除できます。 次にプロジェクトの削除手順を説明します。

1. 任意のプロジェクト レコードをマウスでポイントし、ごみ箱アイコンをクリックします。

   ![プロジェクトの削除](media/how-to/how-to-delete-project.png)

2. 削除を確認します。 プロジェクトを削除すると、そのプロジェクト内で作成されたすべてのモデルが削除されます。 プロジェクトを削除してもドキュメントに影響はありません。

   ![削除の確認ダイアログ](media/how-to/how-to-delete-project-confirm.png)

## <a name="next-steps"></a>次の手順

- [ドキュメントをアップロード](how-to-upload-document.md)してカスタム翻訳モデルの構築を開始します。
