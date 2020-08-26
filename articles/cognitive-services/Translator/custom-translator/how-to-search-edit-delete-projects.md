---
title: プロジェクトの検索、編集、削除方法 - Custom Translator
titleSuffix: Azure Cognitive Services
description: Custom Translator には、効率的な方法でプロジェクトを管理するさまざまな方法が用意されています。 複数のプロジェクトを作成し、条件に基づいて検索し、プロジェクトを編集することができます。 Custom Translator でプロジェクトを削除することもできます。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 4c48a8a18a3ad0487b4c3840847c7f5edbb6d29f
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510378"
---
# <a name="search-edit-and-delete-projects"></a>プロジェクトの検索、編集、削除

Custom Translator には、効率的な方法でプロジェクトを管理する複数の方法が用意されています。 多数のプロジェクトを作成し、条件に基づいて検索し、プロジェクトを編集することができます。 Custom Translator でプロジェクトを削除することもできます。  

## <a name="search-and-filter-projects"></a>プロパティの検索とフィルター処理

フィルター ツールを使用すると、さまざまなフィルター条件でプロジェクトを検索できます。 プロジェクト名、状態、ソース言語、ターゲット言語、プロジェクトのカテゴリなどのフィルター処理を行います。

1. フィルター ボタンをクリックします。

    ![プロジェクトを検索する](media/how-to/how-to-search-project.png)

2. プロジェクト名、ソース言語、ターゲット言語、カテゴリ、およびプロジェクト可用性の各フィールドのいずれか (またはすべて) を指定してフィルター処理できます。

3. [適用] をクリックします。

    ![プロジェクトの検索のフィルター オプション](media/how-to/how-to-search-project-filters.png)

4. フィルターをクリアしてすべてのプロジェクトを表示するには、[クリア] をタップします。

## <a name="edit-a-project"></a>プロジェクトを編集する

Custom Translator には、プロジェクトの名前と説明を編集する機能があります。 カテゴリ、ソース言語、ターゲット言語など、プロジェクトの他のメタデータは編集できません。 次にプロジェクトの編集手順を説明します。

1. プロジェクトをマウスでポイントしたときに表示される鉛筆アイコンをクリックします。

    ![プロジェクトの編集](media/how-to/how-to-edit-project.png)

2. デプロイされたモデルがない場合は、このダイアログで、プロジェクト名、プロジェクトの説明、カテゴリの説明、およびプロジェクト ラベルを変更できます。 いったんプロジェクトが作成されると、カテゴリや言語ペアを変更することはできません。

    ![[プロジェクトの編集] ダイアログ](media/how-to/how-to-edit-project-dialog.png)

3. [保存] ボタンをクリックします。

## <a name="delete-a-project"></a>プロジェクトを削除する

不要になったプロジェクトは削除できます。 プロジェクトに、デプロイ済み、トレーニング送信済み、データ処理中、デプロイ中など、アクティブな状態のモデルが存在しないようにします。そうでないと、削除操作が失敗します。 次にプロジェクトの削除手順を説明します。

1. 任意のプロジェクト レコードをマウスでポイントし、ごみ箱アイコンをクリックします。

   ![プロジェクトの削除](media/how-to/how-to-delete-project.png)

2. 削除を確認します。 プロジェクトを削除すると、そのプロジェクト内で作成されたすべてのモデルが削除されます。 プロジェクトを削除してもドキュメントに影響はありません。

   ![削除の確認ダイアログ](media/how-to/how-to-delete-project-confirm.png)

## <a name="next-steps"></a>次のステップ

- [ドキュメントをアップロード](how-to-upload-document.md)してカスタム翻訳モデルの構築を開始します。
