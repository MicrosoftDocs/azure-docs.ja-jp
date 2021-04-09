---
title: Azure Storage を使用した SQL Server のバックアップと復元の方法 | Microsoft Docs
description: SQL Server を Azure Storage に バックアップする方法について説明します。 SQL データベースを Azure Storage にバックアップする利点について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.subservice: backup
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mathoma
ms.openlocfilehash: 35fff49a53f5a0a9532fd0dff841356c5deaf3ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97724784"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Azure Storage を使用した SQL Server のバックアップと復元
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

SQL Server 2012 SP1 CU2 以降で、SQL Server データベースを Azure Blob Storage に直接書き込んでバックアップできるようになりました。 この機能を使用して、Azure Blob Storage との間でバックアップと復元を行います。 クラウドへのバックアップには、高い可用性、無制限の社外ストレージの geo レプリケーション、クラウドとの間でのデータ移行の容易さという利点があります。 Transact-SQL または SMO を使用して `BACKUP` または `RESTORE` ステートメントを発行できます。

## <a name="overview"></a>概要
SQL Server 2016 に導入された新しい機能である [ファイル スナップショット バックアップ](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) を使用して、ほぼ瞬時のバックアップと非常に簡単な復元を実行できます。

このトピックでは、SQL Server のバックアップに Azure Storage の使用をお勧めする理由を説明し、その後で関連するコンポーネントについて説明します。 この記事の最後に示すリソースから、このサービスを SQL Server のバックアップで使い始めるためのチュートリアルと追加情報にアクセスできます。

## <a name="benefits-of-using-azure-blob-storage-for-sql-server-backups"></a>SQL Server バックアップに Azure Blob storage を使用する利点
SQL Server をバックアップするときに直面する課題はいくつかあります。 ストレージの管理、ストレージ障害のリスク、社外ストレージへのアクセス、ハードウェアの構成がその課題として挙げられます。 これらの課題の多くは、Azure Blob Storage を使用して SQL Server バックアップを行うことによって解決されます。 次の利点を考慮してください。

* **使いやすさ**:Azure BLOB にバックアップを保存することで、社外ストレージへのアクセスが柔軟かつ簡単に行え、便利になります。 SQL Server のバックアップ用に社外ストレージを作成するのは、 **BACKUP TO URL** 構文を使用するように既存のスクリプトやジョブを変更するだけの簡単さです。 オフサイト ストレージは、通常、災害発生時にオフサイトと運用データベース両方の場所に影響しないように、運用データベースの場所から十分に離れた場所に設置する必要があります。 [Azure BLOB の geo レプリケーション](../../../storage/common/storage-redundancy.md)を利用することで、リージョン全体に被害が及ぶような災害に対する防御措置を強化できます。
* **バックアップ アーカイブ**:Azure Blob Storage では、バックアップ アーカイブによく利用されているテープに代わる、優れた手法が提供されます。 テープ ストレージでは、オフサイトの施設への物理的な輸送手段とメディアを保護する手段が必要になります。 Azure Blob Storage へのバックアップの保存では、可用性と持続性に優れた高速アーカイブが可能です。
* **ハードウェアの管理**:Azure サービスではハードウェア管理のオーバーヘッドはありません。 Azure サービスのハードウェア管理では、ハードウェア障害に対する冗長性実現と保護のためにgeo レプリケーションが行われます。
* **無制限のストレージ**:Azure BLOB への直接バックアップを有効にすると、実質的に無制限のストレージにアクセスできます。 代わりに、Azure の仮想マシンのディスクにバックアップすると、マシンのサイズに基づく制限があります。 バックアップ用に Azure 仮想マシンにアタッチできるディスクの数には制限があります。 ディスク数の上限は、XL インスタンスでは 16 台、サイズの小さいインスタンスではこれより少なくなります。
* **バックアップの可用性**:Azure BLOB に格納されたバックアップはいつでもどこでも使用でき、SQL Server インスタンスへのリストアを行うために簡単にアクセスできます。その際に、データベースのアタッチ/デタッチや、VHD のダウンロードとアタッチを行う必要はありません。
* **コスト**: 使用するサービスにのみ料金がかかります。 オフサイトのバックアップ アーカイブ オプションとして、優れたコスト効果を得ることができます。 詳細については、[Azure 料金計算ツール](https://go.microsoft.com/fwlink/?LinkId=277060 "料金計算ツール")と [Azure の料金に関する記事](https://go.microsoft.com/fwlink/?LinkId=277059 "価格に関する記事")をご覧ください。
* **ストレージ スナップショット**:データベース ファイルが Azure BLOB に格納されていて、SQL Server 2016 を使用している場合は、[ファイル スナップショット バックアップ](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure)を使用して、ほぼ瞬時のバックアップと非常に簡単な復元を実行できます。

詳細については、[Azure Blob Storage を使用した SQL Server のバックアップと復元](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)に関するページを参照してください。

次の 2 つのセクションで、必要な SQL Server コンポーネントを含め、Azure Blob Storage について紹介します。 重要なのは、それらのコンポーネントとその相互作用を理解して、Azure Blob Storage でバックアップまたは復元を正しく利用することです。

## <a name="azure-blob-storage-components"></a>Azure Blob storage のコンポーネント
Azure Blob Storage にバックアップする際に、次の Azure コンポーネントを使用します。

| コンポーネント | 説明 |
| --- | --- |
| **ストレージ アカウント** |ストレージ アカウントは、すべてのストレージ サービスの開始点となります。 Azure Blob Storage にアクセスするには、まず Azure Storage アカウントを作成します。 Azure Blob Storage の詳細については、[Azure Blob Storage の使用方法](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/)に関するページを参照してください。 |
| **コンテナー** |コンテナーは一連の BLOB のグループ化に使用され、格納できる BLOB の数に制限はありません。 SQL Server のバックアップを Azure Blob Storage に書き込むには、少なくとも root コンテナーが作成されている必要があります。 |
| **BLOB** |任意の種類とサイズのファイルです。 BLOB は、次の URL 形式を使用してアドレス指定できます: `https://<storageaccount>.blob.core.windows.net/<container>/<blob>`。 ページ BLOB の詳細については、「[ブロック BLOB およびページ BLOB について](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs)」をご覧ください。 |

## <a name="sql-server-components"></a>SQL Server のコンポーネント
Azure Blob Storage にバックアップする際に、次の SQL Server コンポーネントを使用します。

| コンポーネント | 説明 |
| --- | --- |
| **URL** |一意なバックアップ ファイルの Uniform Resource Identifier (URI) を示します。 URL では、SQL Server バックアップ ファイルの場所と名前を指定します。 URL は、コンテナーだけでなく、実際の BLOB を参照している必要があります。 BLOB が存在しない場合は Azure により作成されます。 既存の BLOB を指定した場合、`WITH FORMAT` オプションを指定していないと、バックアップ コマンドは失敗します。 BACKUP コマンドで URL を指定する例を次に示します。`https://<storageaccount>.blob.core.windows.net/<container>/<FILENAME.bak>`<br><br> HTTPS は必須ではありませんが、使用することをお勧めします。 |
| **資格情報** |Azure Blob Storage に対する接続と認証に必要な情報は資格情報として保存されます。 SQL Server から Azure BLOB に対してバックアップを書き込んだり復元したりするには、SQL Server 資格情報を作成する必要があります。 詳細については、 [SQL Server 資格情報](/sql/t-sql/statements/create-credential-transact-sql)に関するページを参照してください。 |

> [!NOTE]
> SQL Server 2016 は、ブロック BLOB をサポートするように更新されました。 詳しくは、「[チュートリアル:Microsoft Azure Blob Storage と SQL Server 2016 データベースの使用](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016)」を参照してください。
> 

## <a name="next-steps"></a>次のステップ

1. まだお持ちでない場合は、Azure アカウントを作成します。 Azure を評価する場合は、 [無料試用版](https://azure.microsoft.com/free/)を検討してください。
2. 次に、ストレージ アカウントの作成と復元の実行の手順について説明する次のいずれかのチュートリアルを確認します。
   
   * **SQL Server 2014**:[チュートリアル: Microsoft Azure Blob Storage への SQL Server 2014 のバックアップと復元](/previous-versions/sql/2014/relational-databases/backup-restore/sql-server-backup-to-url)。
   * **SQL Server 2016**:[チュートリアル: Microsoft Azure Blob Storage と SQL Server 2016 データベースの使用](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016)
3. [Microsoft Azure Blob Storage を使用した SQL Server のバックアップと復元](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)に関する記事などのその他のドキュメントを確認してください。

問題がある場合は、「 [SQL Server Backup to URL に関するベスト プラクティスとトラブルシューティング](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting)」をご覧ください。

その他の SQL Server のバックアップと復元のオプションについては、「[Azure Virtual Machines における SQL Server のバックアップと復元](backup-restore.md)」を参照してください。