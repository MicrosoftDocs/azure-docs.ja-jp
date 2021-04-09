---
title: Azure Monitor ブックのテキストの視覚化
description: すべての Azure Monitor ブックのテキストの視覚化について説明します。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: e8f2d9495484b781b26962c2946b5bada6c38b4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100600779"
---
# <a name="text-visualizations"></a>テキストの視覚化

ブックでは、作成者が自分のブックにテキスト ブロックを含めることができます。 テキストは、テレメトリ (ユーザーがデータやセクション見出しなどを解釈するのに役立つ情報) の人間による分析にすることができます。

[![Apdex のテキストの表のスクリーンショット](./media/workbooks-text-visualizations/apdex.png)](./media/workbooks-text-visualizations/apdex.png#lightbox)

テキストは、書式を完全に制御できるマークダウン コントロールを通じて追加されます。 これには、さまざまな見出しとフォント スタイル、ハイパーリンク、テーブルなどが含まれます。

編集モード:

![編集モードでのテキスト ステップのスクリーンショット。](./media/workbooks-text-visualizations/text-edit-mode.png)

プレビュー モード:

![[プレビュー] タブでの編集モードでのテキスト ステップのスクリーンショット。](./media/workbooks-text-visualizations/text-edit-mode-preview.png)

## <a name="add-a-text-control"></a>テキスト コントロールの追加

1. **[編集]** ツール バー項目をクリックして、ブックを編集モードに切り替えます。
2. **[テキストの追加]** リンクを使用して、テキスト コントロールをブックに追加します。
3. エディター フィールドでマークダウンを追加します。
4. *[テキストのスタイル]* オプションを使用して、プレーン マークダウンと、Azure portal 標準の情報/警告/成功/エラーのスタイル設定でラップされたマークダウンを切り替えます。
5. **[プレビュー]** タブを使用して、コンテンツの表示を確認します。 編集中のプレビューでは、コンテンツがスクロールバー領域内に表示され、そのサイズが制限されます。ただし、実行時には、マークダウン コンテンツは拡張され、スクロールバーなしで必要な領域内全体に表示されます。
6. **[編集が完了しました]** ボタンを選択して、ステップの編集を完了します。

> [!TIP]
> さまざまな書式設定オプションについては、こちらの「[マークダウン チートシート](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)」を参照してください。

## <a name="text-styles"></a>テキスト スタイル

テキスト ステップでは、次のテキスト スタイルを使用できます。

| Style     | 説明                                                                               |
|-----------|-------------------------------------------------------------------------------------------|
| `plain`   | 追加の書式設定は適用されません。                                                      |
| `info`    | ポータルの "情報" スタイル。`ℹ` または類似のアイコンと、通常は青の背景が使用されます。      |
| `error`   | ポータルの "エラー" スタイル。`❌` または類似のアイコンと、通常は赤の背景が使用されます。     |
| `success` | ポータルの "成功" スタイル。`✔` または類似のアイコンと、通常は緑の背景が使用されます。  |
| `upsell`  | ポータルの "アップセル" スタイル。`🚀` または類似のアイコンと、通常は紫の背景が使用されます。 |
| `warning` | ポータルの "警告" スタイル。`⚠` または類似のアイコンと、通常は青の背景が使用されます。   |

特定のスタイルを選択する代わりに、テキスト パラメーターをスタイルのソースとして選択することもできます。 パラメーター値は、上記のテキスト値のいずれかである必要があります。 値がない場合や値が認識されない場合は、`plain` スタイルとして扱われます。

情報スタイルの例:

![情報スタイルの表示を示すスクリーンショット。](./media/workbooks-text-visualizations/text-preview-info-style.png)

警告スタイルの例:

![警告スタイルの表示を示すスクリーンショット。](./media/workbooks-text-visualizations/text-warning-style.png)

## <a name="next-steps"></a>次の手順

* [ブックでグラフ](workbooks-chart-visualizations.md)を作成する方法について学習する。
* [ブックでグリッド](workbooks-grid-visualizations.md)を作成する方法について学習する。
