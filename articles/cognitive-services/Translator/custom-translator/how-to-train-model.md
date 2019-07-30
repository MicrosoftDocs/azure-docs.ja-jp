---
title: モデルをトレーニングする - Custom Translator
titleSuffix: Azure Cognitive Services
description: モデルのトレーニングは、翻訳モデルを構築するときに重要な手順です。 トレーニングは、そのトレーニング用に選択したドキュメントに基づいて行われます。
author: swmachan
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 8804285bf419bce5ca85cc5070cd47ce9a87392a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447956"
---
# <a name="train-a-model"></a>モデルをトレーニングする

モデルのトレーニングが翻訳モデルの構築に重要な手順である理由は、トレーニングがなければ、モデルを構築できないためです。 トレーニングは、そのトレーニング用に選択したドキュメントに基づいて行われます。

モデルをトレーニングするには:

1.  モデルを作成するプロジェクトを選択します。

2.  プロジェクトの [データ] タブには、プロジェクトの言語ペアに関連するすべてのドキュメントが表示されます。 モデルのトレーニングに使用するドキュメントを手動で選択します。 この画面では、ドキュメントのトレーニング、チューニング、およびテストを選択できます。 また、トレーニング セットを選択するだけで、Custom Translator でチューニングとテスト セットが自動的に作成されます。

    -  Document name (ドキュメント名): ドキュメントの名前。

    -  Pairing (ペアリング): このドキュメントが並列またはモノリンガル ドキュメントの場合。 モノリンガル ドキュメントは、現在、トレーニングではサポートされていません。

    -  ドキュメントの種類:トレーニング、チューニング、テスト、または辞書があります。

    -  Language pair (言語ペア): プロジェクトのソース言語とターゲット言語が表示されます。

    -  Source sentences (ソース文): ソース ファイルから抽出された文の数が表示されます。

    -  Target sentences (ターゲット文): ターゲット ファイルから抽出された文の数が表示されます。

    ![モデルをトレーニングする](media/how-to/how-to-train-model.png)

3.  [Train]\(トレーニング\) ボタンをクリックします。

4.  ダイアログで、モデルの名前を指定します。

5.  [Train model]\(モデルのトレーニング\) をクリックします。

    ![[Train model]\(モデルのトレーニング\) ダイアログ](media/how-to/how-to-train-model-2.png)

6.  Custom Translator からトレーニングが送信され、モデルのタブにトレーニングの状態が表示されます。

    ![[Train model]\(モデルのトレーニング\) ページ](media/how-to/how-to-train-model-3.png)

>[!Note]
>Custom Translator は、任意の 1 時点で、ワークスペース内の 10 個の同時実行トレーニングをサポートします。


## <a name="edit-a-model"></a>モデルを編集する

[Model Details]\(モデルの詳細\) ページの [Edit]\(編集\) リンクを使用してモデルを編集できます。

1.  鉛筆アイコンをクリックします。

    ![モデルの編集](media/how-to/how-to-edit-model.png)

2.  このダイアログで次を変更します。

    1.  Model Name (モデル名) (必須): モデルにわかりやすい名前を指定します。

        ![[Edit model]\(モデルの編集\) ダイアログ](media/how-to/how-to-edit-model-dialog.png)

3.  [保存] をクリックします。


## <a name="next-steps"></a>次の手順

- [モデルの詳細を表示する方法](how-to-view-model-details.md)について説明します。
