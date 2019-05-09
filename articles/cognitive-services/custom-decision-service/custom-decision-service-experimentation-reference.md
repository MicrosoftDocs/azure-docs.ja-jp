---
title: 実験 - Custom Decision Service
titlesuffix: Azure Cognitive Services
description: この記事は、Custom Decision Service による実験のガイドです。
services: cognitive-services
author: marco-rossi29
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: marossi
ms.openlocfilehash: b5f8c853218a1db53f4dd23e7254b35990a7132b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55870267"
---
# <a name="experimentation"></a>実験

[Contextual Bandits (CB)](https://www.microsoft.com/en-us/research/blog/contextual-bandit-breakthrough-enables-deeper-personalization/) の理論に従い、Custom Decision Service はコンテキストを繰り返し観察し、アクションを実行して、選択したアクションに対する報酬を観察します。 たとえばコンテンツのパーソナル化では、コンテキストはユーザーを記述し、アクションは候補のストーリーであり、報酬はユーザーが推奨されるストーリーを好んだ程度を測定します。

Custom Decision Service は、コンテキストからアクションにマップするときにポリシーを生成します。 特定のターゲット ポリシーについて、予想される報酬を知る必要があります。 報酬を推定する 1 つの方法は、ポリシーをオンラインで使用して、ポリシーにアクション (ユーザーにストーリーを勧める、など) を選択させることです。 しかし、そのようなオンライン評価は、次の 2 つの理由からコストが高くなる場合があります。

* ユーザーに対し、テストされていない実験的なポリシーを公開します。
* 複数のターゲット ポリシーを評価するようにスケーリングされません。

オフポリシー評価は、代替のパラダイムです。 ログ記録ポリシーに従うログが既存のオンライン システムにある場合、オフポリシー評価は新しいターゲット ポリシーの予想される報酬を見積もることができます。

ログ ファイルを使用することで、実験は推定される予想報酬が最も高いポリシーを検索しようとします。 ターゲット ポリシーは、[Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki) 引数によってパラメーター化されます。 既定のモードでは、スクリプトは `--base_command` に追加することによってさまざまな Vowpal Wabbit 引数をテストします。 このスクリプトは、次のアクションを実行します。

* 入力ファイルの最初の `--auto_lines` 行から機能の名前空間を自動的に検出します。
* ハイパーパラメーター (`learning rate`、`L1 regularization`、`power_t`) に対して最初のスイープを実行します。
* ポリシーの評価 `--cb_type` をテストします (逆傾向スコア (`ips`) または二重頑健 (`dr`))。 詳しくは、「[Contextual Bandit example](https://github.com/JohnLangford/vowpal_wabbit/wiki/Contextual-Bandit-Example)」(Contextual Bandit の例) をご覧ください。
* 限界をテストします。
* 二次インタラクション機能をテストします。
   * **ブルート フォース フェーズ**:`--q_bruteforce_terms` 個以下のペアですべての組み合わせをテストします。
   * **最長一致フェーズ**:`--q_greedy_stop` ラウンドに向上がなくなるまで、最善のペアを追加します。
* ハイパーパラメーター (`learning rate`、`L1 regularization`、`power_t`) に対して 2 番目のスイープを実行します。

これらのステップを制御するパラメーターには、いくつかの Vowpal Wabbit 引数が含まれます。
- 操作オプションの例:
  - 共有名前空間
  - アクション名前空間
  - 限界名前空間
  - 二次機能
- 更新ルール オプション
  - 学習速度
  - L1 正則化
  - t パワー値

上記の引数について詳しくは、「[Vowpal Wabbit command-line arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)」(Vowpal Wabbit コマンド ライン引数) をご覧ください。

## <a name="prerequisites"></a>前提条件
- Vowpal Wabbit:インストールされてパス上にあること。
  - Windows:[`.msi`インストーラー](https://github.com/eisber/vowpal_wabbit/releases)を使用します。
  - その他のプラットフォーム:[ソース コードを入手](https://github.com/JohnLangford/vowpal_wabbit/releases)します。
- Python 3:インストールされてパス上にあること。
- NumPy:任意のパッケージ マネージャーを使用します。
- *Microsoft/mwt ds*リポジトリ:[リポジトリを複製](https://github.com/Microsoft/mwt-ds)します。
- Decision Service JSON ログ ファイル:既定では、ベース コマンドに `--dsjson` が含まれ、入力データ ファイルの Decision Service JSON の解析を有効にします。 [この形式の例を入手します](https://github.com/JohnLangford/vowpal_wabbit/blob/master/test/train-sets/decisionservice.json)。

## <a name="usage"></a>使用法
`mwt-ds/DataScience` に移動し、次のコードに説明するように関連する引数を使用して `Experimentation.py` を実行します。

```cmd
python Experimentation.py [-h] -f FILE_PATH [-b BASE_COMMAND] [-p N_PROC]
                          [-s SHARED_NAMESPACES] [-a ACTION_NAMESPACES]
                          [-m MARGINAL_NAMESPACES] [--auto_lines AUTO_LINES]
                          [--only_hp] [-l LR_MIN_MAX_STEPS]
                          [-r REG_MIN_MAX_STEPS] [-t PT_MIN_MAX_STEPS]
                          [--q_bruteforce_terms Q_BRUTEFORCE_TERMS]
                          [--q_greedy_stop Q_GREEDY_STOP]
```

結果のログは、*mwt-ds/DataScience/experiments.csv* ファイルに追加されます。

### <a name="parameters"></a>parameters
| 入力 | 説明 | 既定値 |
| --- | --- | --- |
| `-h`、`--help` | ヘルプ メッセージを表示して終了します。 | |
| `-f FILE_PATH`、`--file_path FILE_PATH` | データ ファイルのパス (`.json` または `.json.gz` 形式 - 各行は `dsjson` です)。 | 必須 |  
| `-b BASE_COMMAND`、`--base_command BASE_COMMAND` | 基本の Vowpal Wabbit コマンド。  | `vw --cb_adf --dsjson -c` |  
| `-p N_PROC`、`--n_proc N_PROC` | 使用する並列処理の数。 | 論理プロセッサ |  
| `-s SHARED_NAMESPACES, --shared_namespaces SHARED_NAMESPACES` | 共有機能名前空間 (たとえば、`abc` は名前空間 `a`、`b`、`c` を意味します)。  | データ ファイルから自動検出 |  
| `-a ACTION_NAMESPACES, --action_namespaces ACTION_NAMESPACES` | アクション機能の名前空間。 | データ ファイルから自動検出 |  
| `-m MARGINAL_NAMESPACES, --marginal_namespaces MARGINAL_NAMESPACES` | 限界機能の名前空間。 | データ ファイルから自動検出 |  
| `--auto_lines AUTO_LINES` | 自動検出機能の名前空間をスキャンするデータ ファイルの行数。 | `100` |  
| `--only_hp` | ハイパーパラメーター (`learning rate`、`L1 regularization`、`power_t`) のみをスイープ。 | `False` |  
| `-l LR_MIN_MAX_STEPS`、`--lr_min_max_steps LR_MIN_MAX_STEPS` | 正の値 `min,max,steps` としての学習速度の範囲。 | `1e-5,0.5,4` |  
| `-r REG_MIN_MAX_STEPS`、`--reg_min_max_steps REG_MIN_MAX_STEPS` | 正の値 `min,max,steps` としての L1 正則化の範囲。 | `1e-9,0.1,5` |  
| `-t PT_MIN_MAX_STEPS`、`--pt_min_max_steps PT_MIN_MAX_STEPS` | 正の値 `min,max,step` としての Power_t の範囲。 | `1e-9,0.5,5` |  
| `--q_bruteforce_terms Q_BRUTEFORCE_TERMS` | ブルート フォース フェーズでテストする二次ペアの数。 | `2` |  
| `--q_greedy_stop Q_GREEDY_STOP` | 向上のないラウンド。その後、二次最長一致検索フェーズは停止されます。 | `3` |  

### <a name="examples"></a>例
プリセットの既定値を使用するには:
```cmd
python Experimentation.py -f D:\multiworld\data.json
```

同様に、Vowpal Wabbit も `.json.gz` ファイルを取り込むことができます。
```cmd
python Experimentation.py -f D:\multiworld\data.json.gz
```

ハイパーパラメーターのみをスイープするには (`learning rate`、`L1 regularization`、`power_t`、ステップ 2 の後に停止)。
```cmd
python Experimentation.py -f D:\multiworld\data.json --only_hp
```
