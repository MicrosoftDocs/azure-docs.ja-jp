---
title: Azure ストレージ テーブル設計のガイドライン | Microsoft Docs
description: 読み取り操作を効率的にサポートするように Azure テーブル サービスを設計します。
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: d056d29469ad9a60fceeee307aca3c0e1319283c
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55454523"
---
# <a name="guidelines-for-table-design"></a>テーブル設計のガイドライン

Azure ストレージ テーブル サービスで使用するテーブルの設計は、リレーショナル データベースの設計に関する考慮事項とは大きく異なります。 この記事では、読み取りと書き込みが効率的になるように Table service ソリューションを設計するためのガイドラインについて説明します。

## <a name="design-your-table-service-solution-to-be-read-efficient"></a>Table service ソリューションの読み込みが効率的になるように設計する

* ***読み込みが多いアプリケーションでクエリを実行するための設計*** テーブルを設計するときは、エンティティの更新方法について考える前に、実行するクエリ (特に、待機時間に影響を受けやすいもの) を検討してください。 これは通常、効率的でパフォーマンスの高いソリューションになります。  
* ***クエリでは、PartitionKey と RowKey の両方を指定します。*** *ポイント クエリ* これらは、最も効率的なテーブル サービスのクエリなどです。  
* ***エンティティの重複コピーを格納するか検討します。*** テーブル ストレージは安価であるため、クエリの効率を上げるため、(異なるキーを持つ) 同じエンティティを複数回格納することをご検討ください。  
* ***データの非正規化を検討します。*** テーブル ストレージは安価であるため、データの非正規化を検討してください。 たとえば、概要エンティティを格納すると、統計データ用クエリは単一のエンティティにアクセスするだけで済みます。  
* ***複合キーの値を使用します。*** 使用可能なキーは、**PartitionKey** と **RowKey** のみです。 たとえば、複合キーの値を使用してエンティティへの代替キー付きアクセス パスを有効にします。  
* ***クエリ プロジェクションを使用します。*** 必要なフィールドだけを選択するクエリを使用して、ネットワーク経由で転送するデータ量を削減できます。  

## <a name="design-your-table-service-solution-to-be-write-efficient"></a>Table service ソリューションの書き込みが効率的になるように設計する  

* ***ホット パーティションを作成しないでください。*** 任意の時点で、複数のパーティションで要求を分散できるキーを選択します。  
* ***トラフィックの急増を回避します。*** 適切な期間でトラフィックの流れをスムーズにし、トラフィックの急増を回避します。
* ***必ずしもエンティティの種類ごとに個別のテーブルを作成する必要はありません。*** 複数のエンティティ種類でアトミック トランザクションが必要なときに、同じテーブル内の同じパーティションにこれら複数のエンティティ種類を格納できます。
* ***実現する必要のある最大スループットを検討します。*** Table service のスケーラビリティ ターゲットを確認し、それを超えない設計にする必要があります。  

このガイドでは、これらの原則を実装した例を紹介します。 

## <a name="next-steps"></a>次の手順

- [テーブルの設計パターン](table-storage-design-patterns.md)
- [クエリに対応した設計](table-storage-design-for-query.md)
- [テーブル データを暗号化する](table-storage-design-encrypt-data.md)
- [データの変更に対応した設計](table-storage-design-for-modification.md)
