---
title: サービスの制限
titleSuffix: Azure Machine Learning
description: 容量計画で使用されるサービスの制限値と、Azure Machine Learning の要求および応答の上限について説明します。
services: machine-learning
author: andscho
ms.author: andscho
ms.reviewer: mldocs
ms.topic: reference
ms.service: machine-learning
ms.subservice: core
ms.date: 12/21/2020
ms.openlocfilehash: b675e72df4f128d0ce096b3ac398fab63c20557e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "97930805"
---
# <a name="service-limits-in-azure-machine-learning"></a>Azure Machine Learning のサービスの制限値

ここでは、Azure Machine Learning の基本的なクォータとスロットルのしきい値について説明します。 リソースのクォータを引き上げる方法については、[クォータの管理と引き上げ](how-to-manage-quotas.md)に関するページを参照してください。

## <a name="workspaces"></a>Workspaces
| 制限 | 値 |
| --- | --- |
| ワークスペース名 | 2 - 32 文字 |

## <a name="runs"></a>実行
| 制限 | 値 |
| --- | --- |
| ワークスペースごとに実行 | 1,000 万 |
| RunId/ParentRunId | 256 文字 |
| DataContainerId | 261 文字 |
| DisplayName |256 文字|
| 説明 |5,000 文字|
| プロパティの数 |50 |
| プロパティ キーの長さ |100 文字 |
| プロパティ値の長さ |1,000 文字 |
| タグの数 |50 |
| タグ キーの長さ |100 |
| タグ値の長さ |1,000 文字 |
| CancelUri / CompleteUri / DiagnosticsUri |1,000 文字 |
| エラー メッセージの長さ |3,000 文字 |
| 警告メッセージの長さ |300 文字 |
| 入力データセットの数 |200 |
| 出力データセットの数 |20 |


## <a name="metrics"></a>メトリック
| 制限 | 値 |
| --- | --- |
| 実行あたりのメトリック名の数 |50|
| メトリック名あたりのメトリック行数 |1,000 万|
| メトリック行あたりの列数 |15|
| メトリック列の名前の長さ |255 文字 |
| メトリック列の値の長さ |255 文字 |
| アップロードされたバッチあたりのメトリック行数 | 250 |

> [!NOTE]
> メトリック名に変数を書式設定しているために、実行あたりのメトリック名の上限値に達している場合は、1 列目を変数値、2 列目をメトリック値とした行メトリックを代わりに使用することを検討してください。

## <a name="artifacts"></a>Artifacts

| 制限 | 値 |
| --- | --- |
| 実行あたりの成果物の数 |1,000 万|
| 成果物パスの最大長 |5,000 文字 |

## <a name="limit-increases"></a>制限値を上げる
個々のワークスペースの制限値の一部は、[サポートに問い合わせて](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)増やすことができます。 

## <a name="next-steps"></a>次のステップ

- [Azure Machine Learning 環境の構成](how-to-configure-environment.md)
- リソースのクォータを引き上げる方法については、[リソースのクォータの管理と引き上げ](how-to-manage-quotas.md)に関するページを参照してください。

