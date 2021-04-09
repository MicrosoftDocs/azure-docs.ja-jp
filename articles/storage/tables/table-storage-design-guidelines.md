---
title: Azure ストレージ テーブル設計のガイドライン | Microsoft Docs
description: 読み書きの操作を効率的にサポートするように Azure Storage Table service を設計するためのガイドラインについて説明します。
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: f84707e454a8b1f5d5947478fe65108a142a9757
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88236320"
---
# <a name="guidelines-for-table-design"></a>テーブル設計のガイドライン

Azure Storage Table service で使用するテーブルの設計は、リレーショナル データベースの設計に関する考慮事項とは大きく異なります。 この記事では、読み取りと書き込みが効率的になるように Table service ソリューションを設計するためのガイドラインについて説明します。

## <a name="design-your-table-service-solution-to-be-read-efficient"></a>Table service ソリューションの読み込みが効率的になるように設計する

* ***読み込みが多いアプリケーションでクエリを実行するための設計*** テーブルを設計するときは、エンティティの更新方法について考える前に、実行するクエリ (特に、待機時間に影響を受けやすいもの) を検討してください。 これは通常、効率的でパフォーマンスの高いソリューションになります。  
* ***クエリでは、PartitionKey と RowKey の両方を指定します。** これらのような_ _ポイント クエリ*は、最も効率的なテーブル サービス クエリです。  
* ***エンティティの重複コピーを格納するか検討します。*** テーブル ストレージは安価であるため、クエリの効率を上げるために、(異なるキーを持つ) 同じエンティティを複数回格納することをご検討ください。  
* ***データの非正規化を検討します。*** テーブル ストレージは安価であるため、データの非正規化を検討してください。 たとえば、概要エンティティを格納すると、統計データ用クエリは単一のエンティティにアクセスするだけで済みます。  
* ***複合キーの値を使用します。** _ 持っているキーは _ *PartitionKey** と **RowKey** だけです。 たとえば、複合キーの値を使用してエンティティへの代替キー付きアクセス パスを有効にします。  
* ***クエリ プロジェクションを使用します。*** 必要なフィールドだけを選択するクエリを使用して、ネットワーク経由で転送するデータ量を削減できます。  

## <a name="design-your-table-service-solution-to-be-write-efficient"></a>Table service ソリューションの書き込みが効率的になるように設計する  

* ***ホット パーティションを作成しないでください。*** 任意の時点で、複数のパーティションで要求を分散できるキーを選択します。  
* ***トラフィックの急増を回避します。*** 適切な期間でトラフィックの流れをスムーズにし、トラフィックの急増を回避します。
* ***必ずしもエンティティの種類ごとに個別のテーブルを作成する必要はありません。*** 複数のエンティティ種類でアトミック トランザクションが必要なときに、同じテーブル内の同じパーティションにこれら複数のエンティティ種類を格納できます。
* ***実現する必要のある最大スループットを検討します。*** Table service のスケーラビリティ ターゲットを確認し、それを超えない設計にする必要があります。  

このガイドでは、これらの原則を実装した例を紹介します。 

## <a name="next-steps"></a>次のステップ

- [テーブルの設計パターン](table-storage-design-patterns.md)
- [クエリに対応した設計](table-storage-design-for-query.md)
- [テーブル データを暗号化する](table-storage-design-encrypt-data.md)
- [データの変更に対応した設計](table-storage-design-for-modification.md)
