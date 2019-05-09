---
title: インクルード ファイル
description: インクルード ファイル
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 10/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6b48dbfc33890df12209c3a242d812ad2103e07a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553791"
---
| **リソース** | **既定の制限** | **上限** |
| --- | --- | --- |
| サブスクリプションあたりのリージョンごとの Azure Batch アカウント数 | 1 ～ 3 |50 |
| Batch アカウントあたりの専用コア数 | 10 から 100 | N/A<sup>1</sup> |
| Batch アカウントあたりの優先順位の低いコア数 | 10 から 100 | N/A<sup>2</sup> |
| Batch アカウントあたりのアクティブなジョブ数とジョブ スケジュール数<sup>3</sup> | 100 から 300 | 1,000<sup>4</sup> |
| Batch アカウントあたりのプール数 | 20 から 100 | 500<sup>4</sup> |

> [!NOTE]
> 既定の制限は、Batch アカウントの作成に使用するサブスクリプションの種類によって異なります。 表示されるコア クォータは、Batch サービス モードの Batch アカウント用です。 [Batch アカウントのクォータを確認してください](../articles/batch/batch-quota-limit.md#view-batch-quotas)。 

<sup>1</sup>Batch アカウントあたりの専用コア数は増やすことができますが、最大数は指定されていません。 このオプション値を増やすには、Azure サポートにお問い合わせください。

<sup>2</sup>Batch アカウントあたりの優先順位の低いコア数は増やすことができますが、最大数は指定されていません。 このオプション値を増やすには、Azure サポートにお問い合わせください。

<sup>3</sup>完了したジョブとジョブ スケジュールに制限はありません。

<sup>4</sup>この制限を超えた引き上げを要求する場合は、Azure サポートにお問い合わせください。
