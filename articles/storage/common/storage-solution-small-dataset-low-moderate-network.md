---
title: 低速から中速のネットワーク帯域幅での、小規模なデータセットの Azure データ転送のオプション | Microsoft Docs
description: ご利用の環境に低速から中速のネットワーク帯域幅があり、小規模なデータセットを転送する予定の場合に、データ転送に関する Azure ソリューションを選択する方法について説明します。
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: alkohli
ms.openlocfilehash: f59d1e297ba4d7607d7abd07a78da4784f55d20f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023229"
---
# <a name="data-transfer-for-small-datasets-with-low-to-moderate-network-bandwidth"></a>低速から中速のネットワーク帯域幅での小規模なデータセットのデータ転送
 
この記事では、ご利用の環境に低速から中速のネットワーク帯域幅があり、小規模なデータセットを転送する予定の場合の、データ転送ソリューションの概要を示します。 また、この記事では、推奨されるデータ転送と、このシナリオのそれぞれの主要な機能のマトリックスについても説明します。

利用可能なすべてのデータ転送オプションの概要を理解するには、[Azure データ転送ソリューションの選択](storage-choose-data-transfer-solution.md)に関するページに移動します。

## <a name="scenario-description"></a>シナリオの説明

小規模なデータセットとは、データ サイズが GB から数 TB のものです。 低から中程度のネットワーク帯域幅とは、45 Mbps (データセンターでの T3 接続) から 1 Gbps を意味します。

- 数個のファイルだけを転送し、データ転送を自動化する必要がない場合は、グラフィカルなインターフェイスを備えたツールを検討してください。
- システム管理に慣れている場合は、コマン ドラインまたはプログラム/スクリプト ツールを検討してください。

## <a name="recommended-options"></a>推奨されるオプション

このシナリオで推奨されるオプションは次のとおりです。

- Azure Storage Explorer や Azure portal の Azure Storage などの **グラフィカル インターフェイス ツール**。 これらでは、データを表示して数個のファイルをすばやく転送する簡単な手段が提供されます。

    - **Azure Storage Explorer** - このクロスプラットフォーム ツールを使用すると、Azure ストレージ アカウントの内容を管理できます。 BLOB、ファイル、キュー、テーブル、Azure Cosmos DB のエンティティをアップロード、ダウンロード、および管理できます。 BLOB ストレージと共に使用して BLOB とフォルダーを管理できるだけでなく、ローカル ファイル システムと BLOB ストレージ間、またはストレージ アカウント間で BLOB をアップロードおよびダウンロードすることができます。
    - **Azure portal** - Azure portal の Azure Storage では、ファイルを調べて一度に 1 つずつ新しいファイルをアップロードするための、Web ベースのインターフェイスが提供されています。 ツールをインストールしたくない場合、またはファイルをすばやく探索するためや少数のファイルをアップロードするためだけにコマンドを発行したくない場合に適しています。

- AzCopy/PowerShell/Azure CLI や Azure Storage REST API などの **スクリプト/プログラム ツール**。

    - **AzCopy** - このコマンドライン ツールを使用すると、最適なパフォーマンスで Azure BLOB、Files、および Table ストレージとの間で相互に簡単にデータをコピーできます。 AzCopy はコンカレンシーと並列処理をサポートし、中断された場合にコピー操作を再開することができます。
    - **Azure PowerShell** - ユーザーがシステムの管理に慣れている場合は、Azure PowerShell の Azure Storage モジュールを使用してデータを転送します。
    - **Azure CLI** - このクロスプラットフォーム ツールは、Azure サービスを管理し、Azure Storage にデータをアップロードするために使用します。
    - **Azure Storage REST API/SDK** – アプリケーションの構築時に、Azure Storage REST API/SDK に対するアプリケーションを開発し、複数の言語で提供される Azure クライアント ライブラリを使用できます。


## <a name="comparison-of-key-capabilities"></a>主な機能の比較

次の表は、主な機能における相違点をまとめたものです。

| 機能 | Azure Storage Explorer | Azure portal | AzCopy<br>Azure PowerShell<br>Azure CLI | Azure Storage REST API または SDK |
|---------|------------------------|--------------|-----------------------------------------|---------------------------------|
| 可用性 | ダウンロードしてインストールする <br>スタンドアロン ツール | Azure portal の Web ベースの探索ツール | コマンド ライン ツール |.NET、Java、Python、JavaScript、C++、Go、Ruby、および PHP でのプログラミング可能なインターフェイス |
| グラフィカル インターフェイス | はい | はい | いいえ | いいえ |
| サポートされているプラットフォーム | Windows、Mac、Linux | Web ベース |Windows、Mac、Linux |すべてのプラットフォーム |
| 使用できる Blob Storage 操作<br>(対 BLOB およびフォルダー) | アップロード<br>ダウンロード<br>管理する | アップロード<br>ダウンロード<br>管理する |アップロード<br>ダウンロード<br>管理する | はい、カスタマイズ可能 |
| 使用できる Data Lake Gen1 ストレージ<br>操作 (対ファイルおよびフォルダー) | アップロード<br>ダウンロード<br>管理する | いいえ |アップロード<br>ダウンロード<br>管理する                   | いいえ |
| 使用できる File Storage 操作<br>(対ファイルおよびディレクトリ) | アップロード<br>ダウンロード<br>管理する | アップロード<br>ダウンロード<br>管理する   |アップロード<br>ダウンロード<br>管理する | はい、カスタマイズ可能 |
| 使用できる Table Storage 操作<br>(対テーブル) |管理する | いいえ |AzCopy v7 でのテーブル サポート |はい、カスタマイズ可能|
| 使用できる Queue Storage 操作 | 管理する | いいえ  |いいえ | はい、カスタマイズ可能|


## <a name="next-steps"></a>次のステップ

- [Azure Storage Explorer を使用してデータを転送する](../../machine-learning/team-data-science-process/move-data-to-azure-blob-using-azure-storage-explorer.md)方法を学習します。
- [AzCopy を使用してデータを転送する](./storage-use-azcopy-v10.md)