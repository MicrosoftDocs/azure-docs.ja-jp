---
title: Table Storage の概要 - Azure のオブジェクト ストレージ | Microsoft Docs
description: NoSQL データ ストアである Azure Table Storage を使用して構造化データをクラウドに格納します。
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.devlang: dotnet
ms.topic: overview
ms.date: 01/07/2021
ms.subservice: tables
ms.openlocfilehash: 292104651c6bb9b63e9d8cbe26fea2bf8c6acbf1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98027742"
---
# <a name="what-is-azure-table-storage-"></a>Azure Table Storage とは 

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Azure Table Storage は、非リレーショナル構造化データ (構造化された NoSQL データとも呼ばれます) をクラウド内に格納するサービスであり、スキーマレスのデザインによるキーおよび属性ストアを実現します。 Table Storage はスキーマがないため、アプリケーションの進化のニーズに合わせてデータを容易に修正できます。 Table Storage のデータ アクセスは、多くの種類のアプリケーションにとって高速でコスト効率に優れ、また一般に、従来の SQL と比較して、同様の容量のデータを低コストで保存することができます。

Table Storage を使用すると、Web アプリケーションのユーザー データ、アドレス帳、デバイス情報、サービスに必要なその他の種類のメタデータなど、柔軟なデータセットを格納できます。 ストレージ アカウントの容量の上限を超えない限り、テーブルには任意の数のエンティティを保存でき、ストレージ アカウントには任意の数のテーブルを含めることができます。

[!INCLUDE [storage-table-concepts-include](../../../includes/storage-table-concepts-include.md)]

## <a name="next-steps"></a>次のステップ

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md)は、Windows、macOS、Linux で Azure Storage のデータを視覚的に操作できる Microsoft 製の無料のスタンドアロン アプリです。

* [.Net での Azure Table Storage の概要](../../cosmos-db/tutorial-develop-table-dotnet.md)

* 利用可能な API の詳細については、Table service のリファレンス ドキュメントを参照してください。

    * [.NET 用ストレージ クライアント ライブラリ リファレンス](/dotnet/api/overview/azure/storage)

    * [REST API リファレンス](/rest/api/storageservices/)