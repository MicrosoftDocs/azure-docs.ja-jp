---
title: パフォーマンスのベスト プラクティスと構成ガイドライン - Azure SQL Edge
description: Azure SQL Edge のパフォーマンスのベスト プラクティスと構成ガイドラインについて説明します。
keywords: SQL Edge、データ保有
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 02f22883a0989714d8b74f778cacf1ba2c65d0b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392012"
---
# <a name="performance-best-practices-and-configuration-guidelines"></a>パフォーマンスのベスト プラクティスと構成ガイドライン

Azure SQL Edge には、SQL Edge デプロイのパフォーマンスを向上させるために使用できる機能がいくつか用意されています。 この記事では、パフォーマンスを最大化するためのベスト プラクティスと推奨事項について説明します。 

## <a name="best-practices"></a>ベスト プラクティス 

### <a name="configure-multiple-tempdb-data-files"></a>複数の tempdb データ ファイルを構成する

既定では、Azure SQL Edge により、コンテナーの初期化の一部として tempdb データ ファイルが 1 つだけ作成されます。 デプロイ後に複数の tempdb データ ファイルを作成することを検討することをお勧めします。 詳細については、「[Tempdb データベースを SQL Server に割り当て時の競合を減らすための推奨事項](https://support.microsoft.com/help/2154845/recommendations-to-reduce-allocation-contention-in-sql-server-tempdb-d)」を参照してください。

### <a name="use-clustered-columnstore-indexes-where-possible"></a>可能な場合は、クラスター化列ストア インデックスを使用する

IoT および Edge デバイスにより、大量のデータが生成される傾向があり、これは通常、分析のために一定の時間枠で集計されます。 個々のデータ行が分析に使用されることはほとんどありません。 列ストア インデックスは、このような大規模なデータセットの格納とクエリに最適です。 このインデックスは列ベースのデータ ストレージとクエリ処理を使用して、従来の行指向ストレージと比較して最大 10 倍のクエリ パフォーマンスを実現します。 また、非圧縮データのサイズと比較して最大で 10 倍のデータ圧縮を実現できます。 詳細については、[列ストア インデックス](/sql/relational-databases/indexes/columnstore-indexes-overview)に関する記事をご覧ください

さらに、データの挿入とデータの削除に関する列ストアの最適化によって、データ ストリーミングやデータ保有など、Azure SQL Edge の他の機能が向上します。 

### <a name="simple-recovery-model"></a>単純復旧モデル

ストレージはエッジ デバイスで制限される可能性があるため、既定では、Azure SQL Edge のすべてのユーザー データベースによって単純復旧モデルが使用されます。 単純復旧モデルによって、必要な領域が少なくなるように、ログ領域が自動的に再利用されるため、トランザクション ログ領域の管理は基本的に必要ありません。 使用可能なストレージが制限されるエッジ デバイスでは、これが役に立つ場合があります。 単純復旧モデルとその他の使用可能な復旧モデルの詳細については、[復旧モデル](/sql/relational-databases/backup-restore/recovery-models-sql-server)に関する記事をご覧ください

ログ配布やポイントインタイム リストアなど、トランザクション ログ バックアップを必要とする操作は、単純復旧モデルでサポートされていません。  

## <a name="advanced-configuration"></a>詳細な構成 

### <a name="setting-memory-limits"></a>メモリ制限の設定

Azure SQL Edge によって、バッファー プールに対して最大 64 GB のメモリがサポートされますが、SQL Edge コンテナー内で実行されるサテライト プロセスで追加のメモリが必要になる場合があります。 メモリが制限される小さなエッジ デバイスでは、docker ホストとその他のエッジ プロセスやモジュールが正常に機能できるように、SQL Edge コンテナーで使用できるメモリを制限することをお勧めします。 SQL Edge で使用可能なメモリの合計は、次のメカニズムを使用して制御できます。 

- SQL Edge コンテナーで使用可能なメモリを制限する:SQL Edge コンテナーで使用できるメモリの合計は、コンテナー実行構成オプション `--memory` を使用して制限できます。 SQL Edge コンテナーで使用可能なメモリの制限の詳細については、「[Runtime options with Memory, CPUs, and GPUs](https://docs.docker.com/config/containers/resource_constraints/)」 (メモリ、CPU、GPU を含むランタイム オプション) を参照してください。

- コンテナー内の SQL プロセスで使用可能なメモリを制限する:既定では、コンテナー内の SQL プロセスによって、使用可能な物理 RAM の 80% のみが使用されます。 ほとんどのデプロイでは、既定の構成で十分です。 しかしながら、SQL Edge コンテナー内で実行されるデータ ストリーミングと ONNX プロセスには、追加のメモリが必要になる場合があります。 このようなシナリオでは、mssql conf ファイルの `memory.memorylimitmb` 設定を使用して、SQL プロセスで使用可能なメモリを制御できます。 詳細については、「[mssql.conf ファイルを使用して構成する](configure.md#configure-by-using-an-mssqlconf-file)」をご覧ください。

メモリ制限を設定するときは、この値を低く設定しすぎないようにご注意ください。 SQL プロセスに十分なメモリを設定しないと、SQL のパフォーマンスに重大な影響を与える可能性があります。

### <a name="delayed-durability"></a>遅延持続性

Azure SQL Edge のトランザクションは、完全持続性、SQL Server の既定値、または遅延持続性 (低速コミットとも呼ばれます) のいずれかになります。

完全持続性トランザクションのコミットは同期的であり、トランザクションのログ レコードがディスクに書き込まれてからコミットが正常完了として報告され、制御がクライアントに返されます。 遅延持続性トランザクションのコミットは非同期的であり、トランザクションのログ レコードがディスクに書き込まれる前にコミットが正常完了として報告されます。 トランザクションを持続可能にするためには、トランザクション ログ エントリをディスクに書き込む必要があります。 遅延持続性トランザクションは、トランザクション ログ エントリがディスクにフラッシュされる時点で持続的になります。 

**多少のデータ損失** が許容されるデプロイや、低速のストレージを使用するエッジ デバイスでは、遅延持続性を使用してデータ インジェストとデータ保有に基づくクリーンアップを最適化できます。 詳しくは、「[トランザクションの持続性の制御](/sql/relational-databases/logs/control-transaction-durability)」をご覧ください。


### <a name="linux-os-configurations"></a>Linux OS の構成 

SQL インストールで最適なパフォーマンスを得るには、次の [Linux オペレーティング システムの構成](/sql/linux/sql-server-linux-performance-best-practices#linux-os-configuration)設定を使用することを検討してください。