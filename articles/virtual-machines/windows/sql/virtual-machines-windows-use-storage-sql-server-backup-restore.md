---
title: Azure Storage を使用した SQL Server のバックアップと復元の方法 | Microsoft Docs
description: SQL Server を Azure Storage に バックアップする方法について説明します。 SQL データベースを Azure Storage にバックアップする利点について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: mikeraymsft
manager: craigg
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mikeray
ms.openlocfilehash: 1b6660a1565b3c119cc1dec0823870c7dd5bd24f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "61477146"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Azure Storage を使用した SQL Server のバックアップと復元
## <a name="overview"></a>概要
SQL Server 2012 SP1 CU2 以降で、SQL Server のバックアップを Azure BLOB ストレージ サービスに直接書き込めるようになりました。 この機能を使用すると、オンプレミスの SQL Server データベースまたは Azure 仮想マシンの SQL Server データベースを使用する Azure Blob service との間でバックアップおよび復元できます。 クラウドへのバックアップには、高い可用性、無制限の社外ストレージのgeo レプリケーション、クラウドとの間でのデータ移行の容易さという利点があります。 Transact-SQL または SMO を使用して BACKUP または RESTORE ステートメントを発行できます。

SQL Server 2016 に導入された新しい機能である [ファイル スナップショット バックアップ](https://msdn.microsoft.com/library/mt169363.aspx) を使用して、ほぼ瞬時のバックアップと非常に簡単な復元を実行できます。

このトピックでは、SQL のバックアップに Azure Storage の使用をお勧めする理由を説明し、その後で関連するコンポーネントについて説明します。 この記事の最後に示すリソースから、このサービスを SQL Server のバックアップで使い始めるためのチュートリアルと追加情報にアクセスできます。

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>SQL Server のバックアップに Azure BLOB サービスを使用する利点
SQL Server をバックアップするときに直面する課題はいくつかあります。 ストレージの管理、ストレージ障害のリスク、社外ストレージへのアクセス、ハードウェアの構成がその課題として挙げられます。 このような課題の多くは、SQL Server のバックアップに Azure BLOB ストア サービスを使用することで対処できます。 次の利点を考慮してください。

* **使いやすさ**:Azure BLOB にバックアップを保存することで、社外ストレージへのアクセスが柔軟かつ簡単に行え、便利になります。 SQL Server のバックアップ用に社外ストレージを作成するのは、 **BACKUP TO URL** 構文を使用するように既存のスクリプトやジョブを変更するだけの簡単さです。 社外ストレージは通常、運用データベースから十分に離れた場所に設置して、一度の災害によって社外ストレージと運用データベースの両方が影響を受けないようにする必要があります。 [Azure BLOB の geo レプリケーション](../../../storage/common/storage-redundancy.md)を利用することで、リージョン全体に被害が及ぶような災害に対する防御措置を強化できます。
* **バックアップ アーカイブ**:Azure Blob Storage サービスでは、バックアップ アーカイブによく利用されているテープに代わる、優れた手法が提供されます。 テープ ストレージでは、場合によって社外設備への物理的な搬送やメディア保護の対策が必要です。 Azure Blob Storage へのバックアップの保存では、可用性と持続性に優れた高速アーカイブが可能です。
* **ハードウェアの管理**:Azure サービスではハードウェア管理のオーバーヘッドはありません。 Azure サービスのハードウェア管理では、ハードウェア障害に対する冗長性実現と保護のためにgeo レプリケーションが行われます。
* **無制限のストレージ**:Azure BLOB への直接バックアップを有効にすると、実質的に無制限のストレージにアクセスできます。 代わりに、Azure の仮想マシンのディスクにバックアップすると、マシンのサイズに基づく制限があります。 バックアップ用に Azure 仮想マシンにアタッチできるディスクの数には制限があります。 この制限はインスタンス サイズが XL の場合は 16 ディスクです。インスタンス サイズが小さくなるほど、このディスク数は少なくなります。
* **バックアップの可用性**:Azure BLOB に格納されたバックアップはいつでもどこでも使用でき、データベースのアタッチ/デタッチまたは VHD のダウンロードやアタッチの必要なく、オンプレミスの SQL Server、または Azure 仮想マシンで実行されている別の SQL Server に復元するために簡単にアクセスできます。
* **コスト**: 使用するサービスに対してのみ課金されます。 社外ストレージやバックアップ アーカイブ用にこのサービスを選択することで、高い費用対効果を得られます。 詳細については、[Azure 料金計算ツール](https://go.microsoft.com/fwlink/?LinkId=277060 "料金計算ツール")および[Azure の料金に関する記事](https://go.microsoft.com/fwlink/?LinkId=277059 "料金に関する記事")をご覧ください。
* **ストレージ スナップショット**:データベース ファイルが Azure BLOB に格納されていて、SQL Server 2016 を使用している場合は、[ファイル スナップショット バックアップ](https://msdn.microsoft.com/library/mt169363.aspx)を使用して、ほぼ瞬時のバックアップと非常に簡単な復元を実行できます。

詳細については、「 [Azure BLOB ストレージ サービスを使用した SQL Server のバックアップと復元](https://go.microsoft.com/fwlink/?LinkId=271617)」を参照してください。

次の 2 つのセクションで、必要な SQL Server コンポーネントを含め、Azure BLOB ストレージ サービスについて紹介します。 重要なのは、それらのコンポーネントとその相互作用を理解して、Azure Blob Storage サービスでバックアップまたは復元を正しく利用することです。

## <a name="azure-blob-storage-service-components"></a>Azure Blob Storage サービスのコンポーネント
Azure BLOB ストレージ サービスにバックアップする際に、次の Azure コンポーネントを使用します。

| コンポーネント | 説明 |
| --- | --- |
| **ストレージ アカウント** |ストレージ アカウントはすべてのストレージ サービスの出発点となります。 Azure Blob Storage サービスにアクセスするには、まず Azure のストレージ アカウントを作成します。 Azure BLOB ストレージ サービスの詳細については、「 [How to use the Azure Blob Storage Service (Azure BLOB ストレージ サービスを使用する方法)](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **コンテナー** |コンテナーは一連の BLOB のグループ化に使用され、格納できる BLOB の数に制限はありません。 SQL Server のバックアップを Azure Blob service に書き込むには、少なくとも root コンテナーが作成されている必要があります。 |
| **BLOB** |任意の種類およびサイズのファイルです。 BLOB は、次の URL 形式を使用してアドレスを指定できます。**https://[ストレージ アカウント].blob.core.windows.net/[コンテナー]/[BLOB]** ページ BLOB の詳細については、「[ブロック BLOB およびページ BLOB について](https://msdn.microsoft.com/library/azure/ee691964.aspx)」をご覧ください。 |

## <a name="sql-server-components"></a>SQL Server のコンポーネント
Azure BLOB ストレージ サービスにバックアップする際に、次の SQL Server コンポーネントを使用します。

| コンポーネント | 説明 |
| --- | --- |
| **URL** |URL は一意のバックアップ ファイルへの Uniform Resource Identifier (URI) を参照します。 URL を使用して SQL Server バックアップ ファイルの場所と名前を指定します。 URL は、コンテナーだけでなく実際の BLOB を参照する必要があります。 BLOB が存在しない場合は作成されます。 既存の BLOB を指定した場合、> WITH FORMAT オプションを指定していないと、BACKUP は失敗します。 BACKUP コマンドで URL を指定する例を次に示します。**http[s]://[ストレージ アカウント].blob.core.windows.net/[コンテナー]/[ファイル名.bak]** HTTPS は必須ではありませんが、使用することをお勧めします。 |
| **資格情報** |Azure BLOB ストレージ サービスに対する接続と認証に必要な情報は資格情報として保存されます。  SQL Server から Azure BLOB に対してバックアップを書き込んだり復元したりするには、SQL Server 資格情報を作成する必要があります。 詳細については、 [SQL Server 資格情報](https://msdn.microsoft.com/library/ms189522.aspx)に関するページを参照してください。 |

> [!NOTE]
> SQL Server 2016 は、ブロック BLOB をサポートするように更新されました。 詳しくは、「[チュートリアル:Azure Blob Storage サービスと SQL Server 2016 データベースの使用](https://msdn.microsoft.com/library/dn466438.aspx)」をご覧ください。
> 
> 

## <a name="next-steps"></a>次の手順
1. まだお持ちでない場合は、Azure アカウントを作成します。 Azure を評価する場合は、 [無料試用版](https://azure.microsoft.com/free/)を検討してください。
2. 次に、ストレージ アカウントの作成と復元の実行の手順について説明する次のいずれかのチュートリアルを確認します。
   
   * **SQL Server 2014**:[Microsoft Azure Blob Storage サービスへの SQL Server 2014 のバックアップと復元](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx)。
   * **SQL Server 2016**:[チュートリアル: Azure Blob Storage サービスと SQL Server 2016 データベースの使用](https://msdn.microsoft.com/library/dn466438.aspx)
3. [Microsoft Azure BLOB ストレージ サービスを使用した SQL Server のバックアップと復元](https://msdn.microsoft.com/library/jj919148.aspx)に関する記事などのその他のドキュメントを確認してください。

問題がある場合は、「 [SQL Server Backup to URL に関するベスト プラクティスとトラブルシューティング](https://msdn.microsoft.com/library/jj919149.aspx)」をご覧ください。

その他の SQL Server のバックアップと復元のオプションについては、「[Azure Virtual Machines における SQL Server のバックアップと復元](virtual-machines-windows-sql-backup-recovery.md)」をご覧ください。

