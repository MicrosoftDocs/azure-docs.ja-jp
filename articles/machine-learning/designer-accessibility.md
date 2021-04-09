---
title: デザイナーでユーザー補助機能を使用する
titleSuffix: Azure Machine Learning
description: デザイナーで使用できるキーボード ショートカットとスクリーン リーダーのユーザー補助機能について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: peterlu
author: peterclu
ms.date: 01/09/2020
ms.custom: designer
ms.openlocfilehash: 86cb5260a59f864658fbb7ac1c1da2d943c6253e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "90893430"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer"></a>キーボードを使用して Azure Machine Learning デザイナーを使用する

キーボード ショートカットとスクリーン リーダーのユーザー補助機能を使用して Azure Machine Learning デザイナーを使用する方法について説明します。 Azure portal のすべての場所で使用できるキーボード ショートカットの一覧については、「[Azure Portal のキーボード ショートカット](../azure-portal/azure-portal-keyboard-shortcuts.md)」を参照してください。

このワークフローは、[ナレーター](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator) と [JAWS](https://www.freedomscientific.com/products/software/jaws/) でテストされていますが、他の標準的なスクリーン リーダーでも動作するはずです。

## <a name="navigate-the-pipeline-graph"></a>パイプライン グラフ内を移動する

パイプライン グラフは、入れ子になったリストとして構成されます。 外側のリストは、パイプライン グラフ内のすべてのモジュールが記述されたモジュール リストです。 内側のリストは、特定のモジュールのすべての接続が記述された接続リストです。  

1. モジュール リスト内でモジュールを切り替えるには、方向キーを使用します。
1. ターゲット モジュールの接続リストを開くには、Tab キーを使用します。
1. モジュールの接続ポートを切り替えるには、方向キーを使用します。
1. ターゲット モジュールに移動するには、"G" キーを使用します。

## <a name="edit-the-pipeline-graph"></a>パイプライン グラフを編集する

### <a name="add-a-module-to-the-graph"></a>グラフにモジュールを追加する

1. キャンバスからモジュール ツリーにフォーカスを切り替えるには、Ctrl + F6 キーを使用します。
1. モジュール ツリーで目的のモジュールを探すには、標準のツリー表示コントロールを使用します。

### <a name="edit-a-module"></a>モジュールを編集する

モジュールを別のモジュールに接続するには、次のようにします。

1. モジュール リスト内のターゲット モジュールで Ctrl + Shift + H キーを使用し、接続ヘルパーを開きます。
1. そのモジュールの接続ポートを編集します。

モジュールのプロパティを調整するには

1. ターゲット モジュールで Ctrl + Shift + E キーを使用し、モジュール プロパティを開きます。
1. そのモジュールのプロパティを編集します。

## <a name="navigation-shortcuts"></a>ナビゲーション ショートカット

| キー操作 | 説明 |
|-|-|
| Ctrl + F6 | キャンバスとモジュール ツリー間でフォーカスを切り替える |
| Ctrl + F1   | モジュール ツリーのノードにフォーカスがあるときに情報カードを開く |
| Ctrl + Shift + H | ノードにフォーカスがあるときに接続ヘルパーを開く |
| Ctrl + Shift + E | ノードにフォーカスがあるときにモジュールのプロパティを開く |
| Ctrl + G | パイプラインの実行が失敗したときに、最初の失敗したノードにフォーカスを移動する |

## <a name="action-shortcuts"></a>アクションのショートカット

次のショートカットをアクセス キーと共に使用します。 アクセス キーの詳細については、https://en.wikipedia.org/wiki/Access_key を参照してください。

| キー操作 | アクション |
|-|-|
| アクセス キー + R | 実行 |
| アクセス キー + P | 発行 |
| アクセス キー + C | 複製 |
| アクセス キー + D | 配置 |
| アクセス キー + I | 推論パイプラインを作成、更新する |
| アクセス キー + B | バッチ推論パイプラインを作成、更新する |
| アクセス キー + K | [Create inference pipeline]\(推論パイプラインの作成\) ドロップダウンを開く |
| アクセス キー + U | [Update inference pipeline]\(推論パイプラインの更新\) ドロップダウンを開く |
| アクセス キー + M | [その他] (...) ドロップダウンを開く |

## <a name="next-steps"></a>次のステップ

- [ハイコントラストの有効化またはテーマの変更](../azure-portal/set-preferences.md#choose-a-theme-or-enable-high-contrast)
- [Microsoft のユーザー補助関連のツール](https://www.microsoft.com/accessibility)
