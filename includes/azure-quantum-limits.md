---
title: インクルード ファイル
description: インクルード ファイル
author: danielstocker
ms.service: azure-quantum
ms.topic: include
ms.date: 01/08/2021
ms.author: dasto
ms.openlocfilehash: 2106a48a583f120f8b4dde4eb32a30f1a1b1d85b
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948071"
---
### <a name="provider-limits--quota"></a>プロバイダーの制限とクォータ

Azure Quantum サービスでは、ファーストパーティとサードパーティの両方のサービス プロバイダーがサポートされています。 サードパーティ プロバイダーには、制限とクォータがあります。 ユーザーは、プロバイダー ブレードでサードパーティ プロバイダーを構成するときに、Azure portal でプランと制限を表示できます。 

Microsoft のファーストパーティ最適化ソリューション プロバイダーの公開済みクォータ制限を下に示します。 

#### <a name="learn--develop-sku"></a>Learn & Develop SKU

| リソース | 制限 |
| --- | --- |
| CPU ベースの同時実行ジョブ | 最大 5 つの同時実行ジョブ |
| FPGA ベースの同時実行ジョブ | 最大 2 つの同時実行ジョブ |
| CPU ベースのソルバー時間 | 20 時間/月  |
| FPGA ベースのソルバー時間 | 1 時間/月  |

Learn & Develop SKU を使用する場合、クォータ制限の上限の引き上げを要求することは **できません**。 代わりに、Performance at Scale SKU に切り替える必要があります。

#### <a name="performance-at-scale-sku"></a>Performance at Scale SKU

| リソース | 既定の制限 | 上限 |
| --- | --- | --- |
| CPU ベースの同時実行ジョブ | 最大 100 の同時実行ジョブ | 既定の制限と同じ |
| FPGA ベースの同時実行ジョブ | 最大 10 の同時実行ジョブ | 既定の制限と同じ |
| ソルバー時間 | 1,000 時間/月  | 最大 50,000 時間/月 |

上限の引き上げを要求する必要がある場合は、Azure サポートにお問い合わせください。 

詳細については、[Azure Quantum の価格に関するページ](https://aka.ms/AQ/Pricing)を参照してください。
サードパーティのオファリングの詳細については、Azure portal の該当するプロバイダーのページを参照してください。
