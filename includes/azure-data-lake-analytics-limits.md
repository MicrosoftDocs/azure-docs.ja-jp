---
author: rothja
ms.service: data-lake-analytics
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 978131a6cdc27849ff1e9b922d37854f35ff374a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85838962"
---
Azure Data Lake Analytics により、分散インフラストラクチャや複雑なコードの管理という複雑なタスクも簡単に行うことができます。 リソースを動的にプロビジョニングするので、これを使用してエクサバイト規模のデータも分析できます。 ジョブが完了するとリソースが自動的に縮小されます。 使用した処理能力の分だけの支払いですみます。 保存するデータのサイズや使用するコンピューティングの量を増やしたり減らしたりする際に、コードを書き直す必要はありません。 サブスクリプションの既定の制限を引き上げるには、サポートにお問い合わせください。

| **リソース** | **制限** | **コメント** |
| --- | --- | --- |
| 同時ジョブの最大数 |20 | |
| アカウントあたりの Analytics ユニット (AU) の最大数 |250 | 20 個のジョブ全体で 250 AU を最大とする任意の組み合わせを使用します。 この制限を引き上げる場合は、Microsoft Support にご連絡ください。 |
| ジョブ送信の最大スクリプト サイズ | 3 MB | |
| サブスクリプションあたり、リージョンあたりの Data Lake Analytics アカウントの最大数 | 5 | この制限を引き上げる場合は、Microsoft Support にご連絡ください。 |
