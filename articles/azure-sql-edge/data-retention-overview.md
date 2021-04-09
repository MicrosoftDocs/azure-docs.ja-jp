---
title: データ保持ポリシーの概要 - Azure SQL Edge
description: Azure SQL Edge でのデータ保持ポリシーについて説明します
keywords: SQL Edge, データ保持
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: bb059a946c03f41e5b65944eec67070f84ee6b08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90976339"
---
# <a name="data-retention-overview"></a>データ保有の概要

接続された IoT デバイスからのデータの収集と保管は、運用とビジネスの分析情報を得るために重要です。 ただし、これらのデバイスから送信されるデータの量のため、組織では保有するデータの量と粒度を慎重に計画することが重要になります。 すべてのデータをすべての粒度で保持することが理想的ですが、それが常に実用的であるとは限りません。 また、保持できるデータの量は、IoT デバイスまたは Edge デバイスで使用可能なストレージの量によって制限されます。 

Azure SQL Edge では、データベース管理者は SQL Edge データベースとその基になるテーブルに対するデータ保持ポリシーを定義できます。 データ保持ポリシーを定義すると、バックグラウンド システム タスクが実行されて、ユーザー テーブルから古いデータが消去されます。 

> [!Note]
> テーブルから消去されたデータは復旧できません。 消去されたデータを復旧できる唯一の方法は、古いバックアップからデータベースを復元することです。

クイック スタート:

- [データ保有ポリシーを有効または無効にする](data-retention-enable-disable.md)
- [アイテム保持ポリシーを使用して履歴データを管理する](data-retention-cleanup.md)

## <a name="how-data-retention-works"></a>データ保持のしくみ

データ保持を構成するには、DDL ステートメントを使用できます。 詳細については、「[データ保有ポリシーを有効または無効にする](data-retention-enable-disable.md)」を参照してください。 古いレコードを自動的に削除するには、最初に、データベースとそのデータベース内で消去するテーブルの両方で、データ保有を有効にする必要があります。 

テーブルに対してデータ保有を構成すると、バックグラウンド タスクが実行され、テーブル内の古いレコードが識別されて、それらのレコードが削除されます。 何らかの理由で、タスクの自動クリーンアップが実行されない場合、または削除に対応できない場合は、これらのテーブルに対して手動クリーンアップ操作を実行できます。 自動および手動のクリーンアップの詳細については、[自動と手動のクリーンアップ](data-retention-cleanup.md)に関するページを参照してください。

## <a name="limitations-and-restrictions"></a>制限事項と制約事項

- 有効にしたデータ保有は、データベースが完全バックアップから復元された場合、または再アタッチされた場合、自動的に無効にされます。 
- テンポラル履歴テーブルに対しては、データ保有を有効にできません
- データ保持フィルターの列を変更することはできません。 列を変更するには、テーブルのデータ保持を無効にします。  

## <a name="next-steps"></a>次の手順

- [SQL Edge での ONNX を使用した機械学習と人工知能](onnx-overview.md)。
- [IoT Edge を使用して SQL Edge でエンド ツー エンドの IoT ソリューションを構築する](tutorial-deploy-azure-resources.md)。
- [Azure SQL Edge でのデータ ストリーミング](stream-data.md)
