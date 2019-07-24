---
title: Azure SQL Database のリソース制限 - マネージド インスタンス| Microsoft Docs
description: この記事では、マネージド インスタンスに対する Azure SQL Database のリソース制限の概要を示します。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp
manager: craigg
ms.date: 02/27/2019
ms.openlocfilehash: 09ab154494ad3e1276239e36068255c2042358c5
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58223820"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Azure SQL Database Managed Instance のリソース制限の概要

この記事では、Azure SQL Database Managed Instance のリソース制限の概要を示し、既定のリージョン サブスクリプション制限を引き上げるための要求を作成する方法について説明します。

> [!NOTE]
> その他の Managed Instance の制限については、「[仮想コアベースの購入モデル](sql-database-managed-instance.md#vcore-based-purchasing-model)」と「[マネージド インスタンスのサービス階層](sql-database-managed-instance.md#managed-instance-service-tiers)」をご覧ください。 サポートされている機能と T-SQL ステートメントの違いについては、[機能の違い](sql-database-features.md)と [T-SQL ステートメントのサポート](sql-database-managed-instance-transact-sql-information.md)に関するページをご覧ください。

## <a name="instance-level-resource-limits"></a>インスタンス レベルのリソース制限

Managed Instance には、基本のインフラストラクチャとアーキテクチャによって異なる特性とリソース制限があります。 制限は、ハードウェアの世代とサービス レベルに依存します。

### <a name="hardware-generation-characteristics"></a>ハードウェアの世代の特性

Azure SQL Database Managed Instance は、2 つのハードウェアの世代 (Gen4 と Gen5) でデプロイできます。 ハードウェアの世代には、次の表に示したさまざまな特性があります。

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| ハードウェア | Intel E5-2673 v3 (Haswell) 2.4 GHz プロセッサ、接続されている SSD 仮想コア = 1 PP (物理コア) | Intel E5-2673 v4 (Broadwell) 2.3-GHz プロセッサ、高速 NVMe SSD、仮想コア=1 LP (ハイパー スレッド) |
| Compute | 8、16、24 の仮想コア | 8、16、24、32、40、64、80 の仮想コア |
| メモリ | 仮想コアあたり 7 GB | 仮想コアあたり 5.1 GB |
| インメモリ OLTP メモリ | 仮想コアあたり 3 GB | 仮想コアあたり 2.6 GB |
| 最大ストレージ容量 (汎用) |  8 TB | 8 TB |
| 最大ストレージ容量 (Business Critical) | 1 TB (テラバイト) | コアの数に応じて 1 TB、2 TB 、または 4 TB |

### <a name="service-tier-characteristics"></a>サービス レベルの特性

Managed Instance には、General Purpose と Business Critical の 2 つのサービス レベルがあります。 これらのレベルは、次の表に示すように、別の機能を提供します。

| **機能** | **汎用** | **Business Critical** |
| --- | --- | --- |
| 仮想コアの数\* | Gen4: 8、16、24<br/>Gen5: 8、16、24、32、40、64、80 | Gen4: 8、16、24、32 <br/> Gen5: 8、16、24、32、40、64、80 |
| メモリ | Gen4:56 ～ 168 GB<br/>Gen5:40.8 ～ 408 GB<br/>\*仮想コアの数に比例 | Gen4:56 ～ 168 GB <br/> Gen5:40.8 ～ 408 GB<br/>\*仮想コアの数に比例 |
| 最大ストレージ サイズ | 8 TB | Gen4:1 TB (テラバイト) <br/> Gen5: <br/>- 8、16 仮想コアの場合は 1 TB<br/>- 24 仮想コアの場合は 2 TB<br/>- 32、40、64、80 仮想コアの場合は 4 TB |
| データベースあたりの最大ストレージ容量 | インスタンスごとの最大ストレージ サイズによって決まります | インスタンスごとの最大ストレージ サイズによって決まります |
| インスタンスごとの最大データベース数 | 100 | 100 |
| インスタンスごとの最大データベース ファイル数 | 最大 280 | データベースあたり 32,767 ファイル |
| データ/ログの IOPS (概算) | ファイルあたり 500 ～ 7,500<br/>\*[ファイル サイズによって異なる](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)| 11 ～ 110 K (仮想コアあたり 1,375) |
| ログ スループット | インスタンスあたり 22 MB/秒 | 仮想コアあたり 3 MB/秒<br/>インスタンスあたり最大 48 MB/秒|
| データ スループット (概算) | ファイルあたり 100 ～ 250 MB/秒<br/>\*[ファイル サイズによって異なる](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes) | 仮想コアあたり 24 ～ 48 MB/秒 |
| IO 待機時間 (概算) | 5 ～ 10 ms | 1 ～ 2 ms |
| 最大 tempDB サイズ | 192 ～ 1,920 GB (仮想コアあたり 24 GB) | 制約なし - 最大インスタンス ストレージ サイズにより制限される |

**注**:

- ユーザー データベースとシステム データベースのデータ ファイルおよびログ ファイルのサイズはどちらも、最大ストレージ サイズの制限と比較されるインスタンス ストレージ サイズに含まれます。 データベースによって使用される合計領域を確認するには、<a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> システム ビューを使用します。 エラー ログは保持されず、サイズには含まれません。 バックアップは、ストレージ サイズに含まれません。
- スループットと IOPS も、Managed Instance によって明示的に制限されないページ サイズに依存します。

## <a name="supported-regions"></a>サポートされているリージョン

Managed Instance は、[サポートされているリージョン](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)のみで作成できます。 現在サポートされていないリージョンで Managed Instance を作成したい場合は、[Azure portal 経由でサポート要求を送信](#obtaining-a-larger-quota-for-sql-managed-instance)できます。

## <a name="supported-subscription-types"></a>サポートされているサブスクリプションの種類

Managed Instance は現在、次の種類のサブスクリプションのみでデプロイをサポートしています。

- [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [クラウド サービス プロバイダー (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [開発テスト用の従量課金制プラン](https://azure.microsoft.com/offers/ms-azr-0023p/)

> [!NOTE]
> この制限は一時的です。 今後、新しいサブスクリプションの種類が有効になる予定です。

## <a name="regional-resource-limitations"></a>リージョンのリソース制限

サポートされているサブスクリプションの種類には、リージョンごとのリソース数の制限を組み入れることができます。 Managed Instance には、サブスクリプションの種類に応じて、Azure リージョンごとに 2 つの既定の制限があります。

- **サブネットの制限**: マネージド インスタンスが単一リージョンにデプロイされているサブネットの最大数。
- **インスタンス数の制限**: 単一リージョンにデプロイできるインスタンスの最大数。

> [!Note]
> これらの制限は既定の設定であり、技術的な制限ではありません。 現在のリージョンでさらに多くの Managed Instance が必要な場合、[Azure portal でサポート リクエスト](#obtaining-a-larger-quota-for-sql-managed-instance)を特別に作成して、これらの制限をオンデマンドで引き上げることができます。 サポート要求を送信せずに、代わりに、別の Azure リージョンに新しい Managed Instance を作成することも可能です。

次の表は、サポートされているサブスクリプションの既定のリージョン制限を示しています。

|サブスクリプションの種類| Managed Instance のサブネットの最大数 | インスタンスの最大数 |GP マネージド インスタンスの最大数*|BC マネージド インスタンスの最大数*|
| :---| :--- | :--- |:--- |:--- |
|従量課金制|1*|4*|4*|1*|
|CSP |1*|4*|4*|1*|
|開発テスト用の従量課金制プラン|1*|4*|4*|1*|
|Enterprise Dev/Test|1*|4*|4*|1*|
|EA|3**|12**|12**|3**|

\* サブネット内の "インスタンス ユニット" の総数が 4 を超えないように、1 つのサブネット内に 1 BC または 4 GP インスタンスのどちらかをデプロイできます。

** 別のサービス レベルにインスタンスがない場合、1 つのサービス レベルにインスタンスの最大数が適用されます。 GP インスタンスと BC インスタンスを同じサブネット内で共存させることを計画している場合、許可される組み合わせの参考として、次のセクションを利用してください。 単純なルールとしては、サブネットの総数を 3 以内、インスタンス ユニットの総数を 12 以内にする必要があります。



> [!IMPORTANT]
> デプロイを計画するときは、Business Critical (BC) インスタンス は (冗長性が追加されているため) 一般的に、General Purpose (GP) インスタンスの 4 倍の大きさの容量を消費することを考慮してください。 そのため、計算では、1 GP インスタンス = 1 インスタンス ユニット、1 BC インスタンス = 4 インスタンス ユニットとなります。 既定の制限に対する消費量分析を簡素化するために、Managed Instance がデプロイされているリージョン内のすべてのサブネットのインスタンス ユニットを集計して、その結果をサブスクリプションの種類のインスタンス ユニットの制限と比較します。

## <a name="strategies-for-deploying-mixed-general-purpose-and-business-critical-instances"></a>General Purpose と Business Critical インスタンスを共存させてデプロイする場合の戦略

[Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) サブスクリプションでは、GP と BC インスタンスを共存させることが可能です。 ただし、サブネット内でのインスタンスの配置に関して、いくつかの制約があります。

> [!Note]
> [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/)と[クラウド サービス プロバイダー (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources) のサブスクリプションの種類では、1 つの Business Critical インスタンスまたは最大 4 つの General Purpose インスタンスを保持できます。

以下の例は、サブネットが空ではなく、GP と BC が共存しているサービス レベルでのデプロイの事例を示しています。

|サブネットの数|サブネット 1|サブネット 2|サブネット 3|
|:---|:---|:---|:---|
|1|1 BC、最大 8 GP<br>2 BC、最大 4 GP|該当なし| 該当なし|
|2|0 BC、最大 4 GP|1 BC、最大 4 GP<br>2 BC、0 GP|該当なし|
|2|1 BC、0 GP|0 BC、最大 8 GP<br>1 BC、最大 4 GP|該当なし|
|2|2 BC、0 GP|0 BC、最大 4 GP|該当なし|
|3|1 BC、0 GP|1 BC、0 GP|0 BC、最大 4 GP|
|3|1 BC、0 GP|0 BC、最大 4 GP|0 BC、最大 4 GP|

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>SQL Managed Instance でのより大きなクォータの取得

現在のリージョンでより多くの Managed Instance が必要な場合、Azure portal を使用してクォータを拡張するためのサポート要求を送信できます。
より大きなクォータを取得するプロセスを開始するには、次の手順を実行します。

1. **[ヘルプとサポート]** を開き、**[新しいサポート要求]** をクリックします。

   ![ヘルプとサポート](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. 新しいサポート要求の [基本] タブで、次の手順を実行します。
   - **[問題の種類]** で、**[サービスとサブスクリプションの制限 (クォータ)]** を選択します。
   - **[サブスクリプション]** で、ご使用のサブスクリプションを選択します。
   - **[クォータの種類]** で、**[SQL Database Managed Instance]** を選択します。
   - **[サポート プラン]** で、お使いのサポート プランを選択します。

     ![[問題の種類] クォータ](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. **[次へ]** をクリックします。
4. 新しいサポート要求の [問題] タブで、次の手順を実行します。
   - **[重大度]** で、問題の重大度を選択します。
   - **[詳細]** で、エラー メッセージなど、問題に関する追加情報を指定します。
   - **[ファイルのアップロード]** で、より詳細な情報を含むファイル (最大 4 MB) を添付します。

     ![問題の詳細](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > 有効な要求には、次の情報を含める必要があります。
     > - サブスクリプションの制限を引き上げる必要があるリージョン
     > - クオータが増加した後の既存のサブネットで、サービス レベルごとに必要なインスタンス数 (既存のサブネットのいずれかを拡張する必要がある場合)
     > - 必要な新しいサブネット数と、新しいサブネット内でのサービス レベルごとのインスタンス総数 (新しいサブネットにマネージド インスタンスをデプロイする必要がある場合)

5. **[次へ]** をクリックします。
6. 新しいサポート要求の [連絡先情報] タブで、希望する連絡方法 (電子メールまたは電話) と連絡先の詳細を入力します。
7. **Create** をクリックしてください。

## <a name="next-steps"></a>次の手順

- Managed Instance の詳細については、「[Managed Instance とは](sql-database-managed-instance.md)」を参照してください。
- 料金情報については、[Azure SQL Database Managed Instance の価格](https://azure.microsoft.com/pricing/details/sql-database/managed/)に関するページを参照してください。
- 最初の Managed Instance を作成する方法については、[クイック スタート ガイド](sql-database-managed-instance-get-started.md)を参照してください。
