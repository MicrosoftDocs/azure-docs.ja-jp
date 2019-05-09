---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: a8b4e3038bfa6a2e937de91804159e340ed13224
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554158"
---
| リソース | ターゲット |
|----------|---------------|
| 1 つのテーブルの最大サイズ | 500 TiB |
| テーブル エンティティの最大サイズ | 1 MiB |
| テーブル エンティティの最大プロパティ数 | 255。次の 3 つのシステム プロパティが含まれます。PartitionKey、RowKey、Timestamp |
| テーブルあたりの保存されるアクセス ポリシーの最大数 | 5 |
| ストレージ アカウントあたりの最大要求レート | 毎秒 20,000 トランザクション (エンティティ サイズは 1 KiB を想定) |
| 1 つのテーブル パーティションのターゲット スループット (1 KiB のエンティティ) | 毎秒最大 2,000 エンティティ |
