---
title: インクルード ファイル
description: インクルード ファイル
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 05/28/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 22bfc3c86605f4c2eed4c022919b3643f394ea95
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "67080925"
---
| **リソース** | **既定の制限** | **上限** |
| --- | --- | --- |
| サブスクリプションあたりのリージョンごとの Azure Batch アカウント数 | 1 ～ 3 |50 |
| Batch アカウントあたりの専用コア数 | 90 から 900 | サポートにお問い合せください |
| Batch アカウントあたりの優先順位の低いコア数 | 10 から 100 | サポートにお問い合せください |
| Batch アカウントあたりの **[アクティブな](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** ジョブおよびジョブ スケジュール (**完了した**ジョブには制限なし) | 100 から 300 | 1,000<sup>1</sup> |
| Batch アカウントあたりのプール数 | 20 から 100 | 500<sup>1</sup> |

> [!NOTE]
> 既定の制限は、Batch アカウントの作成に使用するサブスクリプションの種類によって異なります。 表示されるコア クォータは、Batch サービス モードの Batch アカウント用です。 [Batch アカウントのクォータを確認してください](../articles/batch/batch-quota-limit.md#view-batch-quotas)。

<sup>1</sup>この制限を超えた引き上げを要求する場合は、Azure サポートにお問い合わせください。
