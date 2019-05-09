---
title: 文のペアリングとアライン - Custom Translator
titleSuffix: Azure Cognitive Services
description: トレーニングの実行時に、並列ドキュメントに存在する文はペアリングまたはアラインされます。 Custom Translator では、文とその文の翻訳を読み取ることで、一度に 1 文ずつ翻訳が学習されます。 次に、2 つの文に含まれる単語とフレーズが相互にアラインされます。
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 8d2933f6a3bbab792acb708f2a59cad4eb2cabf7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57777160"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>並列ドキュメントの文のペアリングとアライン

トレーニング時に、並列ドキュメントに存在する文はペアリングまたはアラインされます。 Custom Translator では、各データ セットの [Aligned Sentences]\(アライン済みの文\) として、ペアリングできる文の数がレポートされます。

## <a name="pairing-and-alignment-process"></a>ペアリングとアラインのプロセス

Custom Translator では、一度に 1 文ずつ、文の翻訳が学習されます。 ソースから文が読み取られ、次にターゲットからその文の翻訳が読み取られます。 次に、2 つの文に含まれる単語とフレーズが相互にアラインされます。 このプロセスによって、ある文の単語とフレーズから、その文の翻訳に含まれる同義の単語とフレーズへのマップを作成できるようになります。 アラインでは、相互の翻訳である文に対してシステムが確実にトレーニングされるように試行します。

## <a name="pre-aligned-documents"></a>事前にアラインされたドキュメント

並列ドキュメントがあることがわかっている場合は、事前にアラインされたテキスト ファイルを提供して、文のアラインを上書きすることができます。 両方のドキュメントのすべての文をテキスト ファイルに抽出し、1 行に 1 文を構成し、`.align` の拡張子でアップロードすることができます。 `.align` の拡張子で、文のアラインをスキップする必要があることを Custom Translator に指示します。

最適な結果を得るために、ファイルの 1 行に 1 文ずつを含めます。 不適切なアライン結果になるため、文中には改行文字を入れないでください。

## <a name="suggested-minimum-number-of-extracted-and-aligned-sentences"></a>抽出された文とアラインされた文の推奨最小数

トレーニングが成功するために各データ セットに必要な抽出された文とアラインされた文の最小数を次の表に示します。 抽出された文の推奨最小数は、アラインされた文の推奨最小数よりもはるかに高くなっています。これは、文のアラインで、抽出された文をすべて適切にアラインできない場合を考慮しているためです。

| データ セット   | 抽出された文の推奨最小数 | アラインされた文の推奨最小数 | アラインされた文の最大数 |
|------------|--------------------------------------------|------------------------------------------|--------------------------------|
| トレーニング   | 10,000                                     | 2,000                                    | 上限なし                 |
| チューニング     | 2,000                                      | 500                                      | 2,500                          |
| テスト    | 2,000                                      | 500                                      | 2,500                          |
| Dictionary | 0                                          | 0                                        | 上限なし                 |

## <a name="next-steps"></a>次の手順

- Custom Translator で[辞書](what-is-dictionary.md)を使用する方法について説明します。
