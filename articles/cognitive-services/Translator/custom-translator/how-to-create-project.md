---
title: プロジェクトを作成する方法 - Custom Translator
titleSuffix: Azure Cognitive Services
description: この記事では、Azure Cognitive Services Custom Translator でプロジェクトを作成および管理する方法について説明します。
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 9e3aa52323f44e6c1407fe2a542e40ee06370043
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895800"
---
# <a name="create-a-project"></a>プロジェクトの作成

プロジェクトは、モデル、ドキュメント、およびテストのコンテナーです。 各プロジェクトには、正しい言語ペアを持ち、そのワークスペースにアップロードされたすべてのドキュメントが自動的に追加されます。

プロジェクトの作成は、モデル構築の第一歩です。

## <a name="create-a-project"></a>プロジェクトを作成する:

1.  [Custom Translator](https://portal.customtranslator.azure.ai) ポータルで、[プロジェクトの作成] をクリックします。

    ![Create project](media/how-to/how-to-create-project.png)

2.  プロジェクトに関する以下の詳細をダイアログに入力します。

    a.  プロジェクト名 (必須):一意でわかりやすい名前をプロジェクトに付けます。 タイトルに言語を含める必要はありません。

    b.  説明:プロジェクトに関する簡単な概要。 この説明は、Custom Translator または結果のカスタム システムの動作に影響はありませんが、異なるプロジェクトを区別するために役立ちます。

    c.  言語ペア (必須):翻訳元と翻訳先の言語を選択します。

    d.  カテゴリ (必須):プロジェクトに最適なカテゴリを選択します。 このカテゴリは、翻訳する予定のドキュメントの用語とスタイルを表します。

    e.  カテゴリの説明:このフィールドを使用して、作業対象の特定の分野または業界について詳しく説明します。 たとえば、カテゴリが医薬の場合は、手術や小児科などの特定のドキュメントを追加することができます。 この説明は、Custom Translator または結果のカスタム システムの動作に影響はありません。

    f.  プロジェクト ラベル:[プロジェクト ラベル](workspace-and-project.md#project-labels)で、言語ペアとカテゴリが同じプロジェクトを区別します。 同じ言語ペアと同じカテゴリの複数のプロジェクトを構築し、このようなプロジェクトに異なる CategoryID でアクセスする予定がある場合に *のみ*、ラベルを使用することをお勧めします。 カテゴリが 1 つのみのシステムを構築する場合は、このフィールドを使用しないでください。 プロジェクト ラベルは必須ではないため、言語ペアを区別するために役立ちません。 同じラベルを複数のプロジェクトに使用することができます。

    ![[プロジェクトの作成] ダイアログ](media/how-to/how-to-create-project-dialog.png)

3.  Create をクリックしてください。

## <a name="view-project-details"></a>プロジェクトの詳細を表示する

Custom Translator のランディング ページには、ワークスペースに含まれる最初の 10 個のプロジェクトが表示されます。 プロジェクト名、言語ペア、カテゴリ、状態、および BLEU スコアが表示されます。

プロジェクトを選択すると、プロジェクト ページに次のように表示されます。

- CategoryID:CategoryID は、WorkspaceID、プロジェクト ラベル、およびカテゴリ コードを連結して作成されます。 カスタムの翻訳を取得するには、Text Translator API と共に CategoryID を使用します。 コピーするには、コピー アイコンをクリックします。

- [Train]\(トレーニング\) ボタン:このボタンを使用して、[モデルのトレーニング](how-to-train-model.md)を開始します。

- [ドキュメントの追加] ボタン:このボタンを使用して、[ドキュメントをアップロード](how-to-upload-document.md)します。

- [Filter documents]\(ドキュメントのフィルター\) ボタン:このボタンを使用して、特定のドキュメントのフィルター処理と検索を行います。

    ![プロジェクトの詳細を表示する](media/how-to/how-to-view-project.png)

## <a name="next-steps"></a>次のステップ

- [プロジェクトの検索、編集、削除方法](how-to-search-edit-delete-projects.md)について説明します。
- [ドキュメントをアップロード](how-to-upload-document.md)して翻訳モデルを構築する方法について説明します。
