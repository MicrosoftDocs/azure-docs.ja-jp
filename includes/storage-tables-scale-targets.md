---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/20/2019
ms.author: tamram
ms.openlocfilehash: 5ab03b682dd0ed1dc7b198e89c86e7a74c6275cd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67457420"
---
| Resource | ターゲット |
|----------|---------------|
| 1 つのテーブルの最大サイズ | 500 TiB |
| テーブル エンティティの最大サイズ | 1 MiB |
| テーブル エンティティの最大プロパティ数 | 255。次の 3 つのシステム プロパティが含まれます。PartitionKey、RowKey、Timestamp |
| エンティティ内のプロパティ値の最大合計サイズ | 1 MiB |
| エンティティ内の個々のプロパティの最大合計サイズ | プロパティの型によって異なります。 詳細については、「[Table サービス データ モデルについて](/rest/api/storageservices/understanding-the-table-service-data-model)」の「**プロパティの型**」を参照してください。 |
| テーブルあたりの保存されるアクセス ポリシーの最大数 | 5 |
| ストレージ アカウントあたりの最大要求レート | 毎秒 20,000 トランザクション (エンティティ サイズは 1 KiB を想定) |
| 1 つのテーブル パーティションのターゲット スループット (1 KiB のエンティティ) | 毎秒最大 2,000 エンティティ |