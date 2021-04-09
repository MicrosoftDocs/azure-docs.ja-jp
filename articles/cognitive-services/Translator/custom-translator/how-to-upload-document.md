---
title: ドキュメントをアップロードする方法 - Custom Translator
titleSuffix: Azure Cognitive Services
description: ドキュメントのアップロード機能では、並列ドキュメント (一方がオリジナルで他方が翻訳である 2 つのドキュメント) がサービスにアップロードされます。
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 83cce31d3eee9b5cca7b54e996e41a270fec0030
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98896396"
---
# <a name="upload-a-document"></a>ドキュメントのアップロード

[Custom Translator](https://portal.customtranslator.azure.ai) では、並列ドキュメントをアップロードして翻訳モデルをトレーニングすることができます。 [並列ドキュメント](what-are-parallel-documents.md)は、一方がもう一方の翻訳であるドキュメントのペアです。 ペアの一方のドキュメントにはソース言語の文が含まれ、もう一方のドキュメントにはターゲット言語に翻訳されたこれらの文が含まれています。

ドキュメントをアップロードする前に、[ドキュメント形式と名前付け規則に関するガイダンス](document-formats-naming-convention.md)を検討して、使用するファイル形式が Custom Translator 内でサポートされていることを確認します。

## <a name="how-to-upload-document"></a>ドキュメントのアップロード方法

[Custom Translator](https://portal.customtranslator.azure.ai) ポータルから、[ドキュメント] タブをクリックしてドキュメント ページに移動します。

![ドキュメントのアップロード リンク](media/how-to/how-to-upload-1.png)


1.  ドキュメント ページの [ファイルのアップロード] ボタンをクリックします。

    ![ドキュメントのアップロード ページ](media/how-to/how-to-upload-2.png)

2.  ダイアログに次の情報を入力します。

    a.  ドキュメントの種類:

    -  トレーニング: これらのドキュメントはトレーニング セットに使用されます。
    -  チューニング: これらのドキュメントはチューニング セットに使用されます。
    -  テスト: これらのドキュメントはテスト セットに使用されます。
    -  Phrase Dictionary (句辞書): これらのドキュメントは句辞書に使用されます。
    -  Sentence Dictionary (文辞書): これらのドキュメントは文辞書に使用されます

    b.  言語ペア

    c.  Override document if exists (存在する場合はドキュメントを上書きする): 既存のドキュメントを同じ名前で上書きする場合は、このチェック ボックスをオンにします。

    d.  関連するセクションに並列データまたは複合データを入力します。

    -  Parallel data (並列データ):
        -  ソース ファイル: ローカル コンピューターからソース言語ファイルを選択します。
        -  ターゲット ファイル: ローカル コンピューターからターゲット言語ファイルを選択します。
        -  Document name (ドキュメント名): 並列ファイルをアップロードする場合にのみ使用されます。

    - Combo data (複合データ):
        -  Combo File (複合ファイル): ローカル コンピューターから複合ファイルを選択します。 複合ファイルには、ソース言語とターゲット言語の両方の文があります。 複合ファイルには[名前付け規則](document-formats-naming-convention.md)が重要です。

    e.  [アップロード] をクリックします

    ![ドキュメントのアップロード ダイアログ](media/how-to/how-to-upload-dialog.png)

3.  この段階では、ドキュメントを処理し、文の抽出を試行しています。 [View upload Progress]\(アップロードの進行状況の表示\) をクリックすると、処理中のドキュメントの状態を確認できます。

    ![ドキュメントのアップロードの処理中ダイアログ](media/how-to/how-to-upload-processing-dialog.png)

4.  このページには、アップロード中の各ファイルの状態とエラーが表示されます。 [Upload history]\(アップロード履歴\) タブをクリックすると、いつでも過去のアップロードの状態を確認できます。

    ![ドキュメントのアップロードの履歴ダイアログ](media/how-to/how-to-upload-document-history.png)


## <a name="view-upload-history"></a>アップロード履歴の表示

[Upload history]\(アップロード履歴\) ページでは、ドキュメントの種類、言語ペア、アップロードの状態など、すべてのドキュメントのアップロードに関する詳細情報の履歴を表示できます。

1. [Custom Translator](https://portal.customtranslator.azure.ai) ポータルから [Upload History]\(アップロード履歴\) タブをクリックして履歴を表示します。

    ![[Upload history]\(アップロード履歴\) タブ](media/how-to/how-to-upload-history-1.png)

2. このページには、過去のすべてのアップロードの状態が表示されます。 最近のアップロードから古いアップロードの順に表示されます。 アップロードごとに、ドキュメント名、アップロードの状態、アップロード日、アップロードされたファイル数、アップロードされたファイルの種類、ファイルの言語ペアが表示されます。

    ![[Upload history]\(アップロード履歴\) ページ](media/how-to/how-to-document-history-2.png)

3. 任意のアップロード履歴レコードをクリックします。 アップロード履歴の詳細ページには、アップロードの一部としてアップロードされたファイル、アップロードされたファイルの状態、ファイルの言語、エラー メッセージ (アップロードにエラーがある場合) が表示されます。

## <a name="next-steps"></a>次のステップ

- [ドキュメントの詳細ページ](how-to-view-document-details.md)を使用して、抽出された文の一覧を確認します。
- [モデルをトレーニングする方法](how-to-train-model.md)。
