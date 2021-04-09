---
title: モデルをトレーニングする - Custom Translator
titleSuffix: Azure Cognitive Services
description: モデルのトレーニングは、翻訳モデルを構築するときに重要な手順です。 トレーニングは、そのトレーニング用に選択したドキュメントに基づいて行われます。
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 5c17f842b93fb99a6a06b94f84ae26126794b776
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896428"
---
# <a name="train-a-model"></a>モデルをトレーニングする

モデルのトレーニングは、翻訳モデルを構築するための最初の最も重要な手順です。これがなければ、モデルを構築できません。 トレーニングは、そのトレーニング用に選択したドキュメントに基づいて行われます。 ドキュメントの種類が "トレーニング" のドキュメントを選択する場合は、並列文の最小要件が 10,000 である点に注意してください。 ドキュメントを選択すると、トレーニング文の合計数がガイドとして表示されます。 この要件は、モデルのトレーニングにドキュメントの種類が辞書のドキュメントのみを選択する場合には適用されません。

モデルをトレーニングするには:

1. モデルを作成するプロジェクトを選択します。

2. プロジェクトの [データ] タブには、プロジェクトの言語ペアに関連するすべてのドキュメントが表示されます。 モデルのトレーニングに使用するドキュメントを手動で選択します。 この画面では、ドキュメントのトレーニング、チューニング、およびテストを選択できます。 また、トレーニング セットを選択するだけで、Custom Translator でチューニングとテスト セットが自動的に作成されます。

    - Document name (ドキュメント名): ドキュメントの名前。

    - Pairing (ペアリング): このドキュメントが並列またはモノリンガル ドキュメントの場合。 モノリンガル ドキュメントは、現在、トレーニングではサポートされていません。

    - Document type (ドキュメントの種類): トレーニング、チューニング、テスト、または辞書があります。

    - Language pair (言語ペア): プロジェクトのソース言語とターゲット言語が表示されます。

    - Source sentences (ソース文): ソース ファイルから抽出された文の数が表示されます。

    - Target sentences (ターゲット文): ターゲット ファイルから抽出された文の数が表示されます。

    ![モデルのトレーニング](media/how-to/how-to-train-model.png)

3. "Create model" (モデルの作成) ボタンをクリックします。

4. ダイアログで、モデルの名前を指定します。 既定では、"Create model" (モデルの作成) ボタンをクリックしたときにトレーニング パイプラインが開始されるように "Train immediately" (今すぐトレーニングを開始する) が選択されています。 "Save as draft" (下書きとして保存) を選択すると、モデルのメタデータが作成され、モデルのトレーニングは開始されずに下書きの状態になります。 後でトレーニングを行うには、下書き状態のモデルを手動で選択する必要があります。

5. "Create model" (モデルの作成) ボタンをクリックします。

    ![[Train model]\(モデルのトレーニング\) ダイアログ](media/how-to/how-to-train-model-2.png)

6. カスタム翻訳ツールからトレーニングが送信され、モデルのタブにトレーニングの状態が表示されます。

    ![[Train model]\(モデルのトレーニング\) ページ](media/how-to/how-to-train-model-3.png)

>[!Note]
>Custom Translator は、任意の 1 時点で、ワークスペース内の 10 個の同時実行トレーニングをサポートします。

## <a name="modify-a-model-name"></a>モデル名を変更する

モデル名は、モデルの詳細ページから変更できます。

1. [プロジェクト] ページで、モデルが存在するプロジェクトの名前をクリックします。
2. [モデル] タブをクリックします。
3. モデル名をクリックして、モデルの詳細を表示します。
4. 鉛筆アイコンをクリックします。

    ![モデルの編集](media/how-to/how-to-edit-model.png)

5. ダイアログで、モデル名を変更し、モデルにわかりやすい名前を付けます。

    ![[Edit model]\(モデルの編集\) ダイアログ](media/how-to/how-to-edit-model-dialog.png)

6. [保存] をクリックします。

## <a name="next-steps"></a>次のステップ

- [モデルの詳細を表示する方法](how-to-view-model-details.md)について説明します。
