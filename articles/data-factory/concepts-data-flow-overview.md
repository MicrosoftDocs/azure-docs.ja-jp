---
title: Azure Data Factory の Mapping Data Flow の概要
description: Azure Data Factory の Mapping Data Flow の概要の説明
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 5064f47444b4ca6d9194ed66144938e6e3d51a4e
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732367"
---
# <a name="what-are-mapping-data-flows-in-azure-data-factory"></a>Azure Data Factory の Mapping Data Flow について

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Mapping Data Flow を使用すると、コードを記述しなくても、グラフィカルなデータ変換ロジックを作成できます。 生成されたデータ フローは、スケールアウトされた Azure Databricks クラスターを使用して、Azure Data Factory パイプライン内でアクティビティとして実行されます。

Azure Data Factory のデータ フローの目的は、コーディングなしで完全にビジュアルなエクスペリエンスを得られるようにすることです。 データ フローを実行クラスター上で実行することにより、データ処理がスケールアウトされます。 Azure Data Factory では、コード翻訳、パス最適化、およびデータ フローのジョブ実行のすべてが処理されます。

最初に、変換ロジックを対話的に検証できるようにするため、データ フローをデバッグ モードで作成します。 次に、実行するパイプラインにデータ フロー アクティビティを追加して、パイプライン デバッグでデータ フローをテストするか、またはパイプライン内で [Trigger Now]\(今すぐトリガー\) を使用して、パイプライン アクティビティからデータ フローをテストします。

続いて、データ フロー アクティビティを実行する Azure Data Factory のパイプラインを使用して、データ フローのアクティビティをスケジュールおよび監視します。

データ フロー デザイン サーフェス上にあるデバッグ モードのトグル スイッチにより、データ変換を対話的に作成できます。 デバッグ モードでは、データ フローを作成するためのデータ準備環境を利用できます。
