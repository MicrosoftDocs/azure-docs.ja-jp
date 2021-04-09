---
title: Azure Purview を使用した Synapse でのデータの検出、接続、探索
description: Synapse でデータを検出、接続、探索する方法に関するガイド
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 12/16/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: a3ed7979584787627c97cfec3bb2e19c147f7152
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98872666"
---
# <a name="discover-connect-and-explore-data-in-synapse-using-azure-purview"></a>Azure Purview を使用した Synapse でのデータの検出、接続、探索 

このドキュメントでは、Azure Purview アカウントを Synapse に登録するときに実行できる操作の種類について説明します。 

## <a name="prerequisites"></a>前提条件 

- [Azure Purview アカウント](../../purview/create-catalog-portal.md) 
- [Synapse ワークスペース](../quickstart-create-workspace.md) 
- [Azure Purview アカウントを Synapse に接続する](quickstart-connect-azure-purview.md) 

## <a name="using-azure-purview-in-synapse"></a>Synapse での Azure Purview の使用 

Synapse で Azure Purview を使用するには、その Purview アカウントにアクセスできる必要があります。 Synapse では、Purview のアクセス許可をパススルーします。 たとえば、キュレーター アクセス許可ロールがある場合は、Azure Purview によってスキャンされたメタデータを編集できます。 

### <a name="data-discovery-search-datasets"></a>データ検出: データセットの検索 

Azure Purview によって登録およびスキャンされたデータを検出するには、Synapse ワークスペースの上部中央にある検索バーを使用します。 すべての組織データを検索するために、必ず Azure Purview を選択してください。 

[![Azure Purview の資産の検索](./media/purview-access.png)](./media/purview-access.png#lightbox)

## <a name="azure-purview-actions"></a>Azure Purview のアクション 

Synapse で使用できる Azure Purview の機能を次に示します。 
- メタデータの **概要**。 
- 分類、用語集の用語、データ型、説明を含むメタデータの **スキーマ** を表示、編集する。 
- 依存関係を理解し、影響分析を実行するために **系列** を表示する。 詳細については、[系列](../../purview/catalog-lineage-user-guide.md)に関する記事をご覧ください。
- データセットの所有者または専門家を確認するために、**連絡先** を表示、編集する。 
- 特定のデータセットの階層的依存関係を理解するための **関連項目**。 このエクスペリエンスは、データ階層を参照する際に役立ちます。

## <a name="actions-that-you-can-perform-over-datasets-with-synapse-resources"></a>Synapse リソースを使用してデータセットに対して実行できるアクション 

### <a name="connect-data-to-synapse"></a>Synapse へのデータの接続 

- Synapse への **新しいリンク サービス** を作成できます。 このアクションは、データを Synapse にコピーしたり、データ ハブに配置したりするために必要となります (ADLSg2 などのサポートされているデータ ソースの場合)。 
- ファイル、フォルダー、テーブルなどのオブジェクトについては、**新しい統合データセット** を直接作成し、既存のリンク サービス (既に作成されている場合) を活用できます。 

既存のリンク サービスまたは統合データセットがあるかどうかはまだ推測できません。 

###  <a name="develop-in-synapse"></a>Synapse での開発 

実行できるアクションは、**新しい SQL スクリプト**、**新しい Notebook**、**新しいデータ フロー** の 3 つです。 

**新しい SQL スクリプト** では、サポートの種類に応じて、次のことができます。 
- データの構造を理解するために、上位 100 行を表示する 
- Synapse SQL データベースから外部テーブルを作成する 
- Synapse SQL データベースにデータを読み込む 
 
**新しい Notebook** では、次のことができます。 
- Spark DataFrame にデータを読み込む 
- Spark テーブルを作成する (Parquet 形式でこれを行うと、サーバーレス SQL プール テーブルも作成されます) 
 
**新しいデータ フロー** では、データ フロー パイプラインのソースとして使用できる統合データセットを作成できます。 データ フローは、データ変換を実行するためのコード不要の開発者向け機能です。 Synapse でのデータ フローの使用方法の詳細については、[こちら](../quickstart-data-flow.md)をご覧ください。

##  <a name="nextsteps"></a>次のステップ 

- [Azure Purview での Azure Synapse 資産の登録とスキャン](../../purview/register-scan-azure-synapse-analytics.md)
- [Azure Purview Data Catalog でデータを検索する方法](../../purview/how-to-search-catalog.md)
