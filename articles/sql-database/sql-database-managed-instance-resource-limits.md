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
ms.reviewer: carlrab, jovanpop, sachinp, sstein
manager: craigg
ms.date: 06/26/2019
ms.openlocfilehash: f4e19b916553912e36f2c3beee3f6a518b244e4d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706999"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Azure SQL Database マネージド インスタンスのリソース制限の概要

この記事では、Azure SQL Database マネージド インスタンスのリソース制限の概要を示し、これらの制限の引き上げを要求する方法について説明します。

> [!NOTE]
> サポートされている機能と T-SQL ステートメントの違いについては、[機能の違い](sql-database-features.md)と [T-SQL ステートメントのサポート](sql-database-managed-instance-transact-sql-information.md)に関するページをご覧ください。

## <a name="instance-level-resource-limits"></a>インスタンス レベルのリソース制限

マネージド インスタンスには、基本のインフラストラクチャとアーキテクチャによって異なる特性とリソース制限があります。 制限は、ハードウェアの世代とサービス レベルに依存します。

### <a name="hardware-generation-characteristics"></a>ハードウェアの世代の特性

Azure SQL Database マネージド インスタンスは、2 つのハードウェアの世代 (Gen4 と Gen5) でデプロイできます。 ハードウェアの世代には、次の表に示したさまざまな特性があります。

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| ハードウェア | Intel E5-2673 v3 (Haswell) 2.4 GHz プロセッサ、接続されている SSD 仮想コア = 1 PP (物理コア) | Intel E5-2673 v4 (Broadwell) 2.3-GHz プロセッサ、高速 NVMe SSD、仮想コア=1 LP (ハイパー スレッド) |
| 仮想コアの数 | 8、16、24 の仮想コア | 4、8、16、24、32、40、64、80 の仮想コア |
| 最大メモリ (メモリ/コア比) | 仮想コアあたり 7 GB<br/>メモリ量を増やすには、仮想コアを追加します。 | 仮想コアあたり 5.1 GB<br/>メモリ量を増やすには、仮想コアを追加します。 |
| 最大インメモリ OLTP メモリ | インスタンスの制限:仮想コアあたり 3 GB<br/>データベースの制限:<br/> - 8 コア:データベースあたり 8 GB<br/> - 16 コア:データベースあたり 20 GB<br/> - 24 コア:データベースあたり 36 GB | インスタンスの制限:仮想コアあたり 2.5 GB<br/>データベースの制限:<br/> - 8 コア:データベースあたり 13 GB<br/> - 16 コア:データベースあたり 32 GB |
| インスタンスの予約済み最大ストレージ (汎用) |  8 TB | 8 TB |
| インスタンスの予約済み最大ストレージ (Business Critical) | 1 TB (テラバイト) | コアの数に応じて 1 TB、2 TB 、または 4 TB |

> [!IMPORTANT]
> 新しい Gen4 データベースは、AustraliaEast リージョンでサポートされなくなりました。

### <a name="service-tier-characteristics"></a>サービス レベルの特性

マネージド インスタンスには 2 つのサービス レベルがあります。General Purpose と Business Critical です。 これらのレベルは、次の表に示すように、別の機能を提供します。

| **機能** | **汎用** | **Business Critical** |
| --- | --- | --- |
| 仮想コアの数\* | Gen4: 8、16、24<br/>Gen5:4、8、16、24、32、40、64、80 | Gen4:8、16、24、32 <br/> Gen5:4、8、16、24、32、40、64、80 |
| 最大メモリ | Gen4:56 GB - 168 GB (7 GB/仮想コア)<br/>Gen5:40.8 GB - 408 GB (5.1 GB/仮想コア)<br/>メモリ量を増やすには、仮想コアを追加します。 | Gen4:56 GB - 168 GB (7 GB/仮想コア)<br/>Gen5:40.8 GB - 408 GB (5.1 GB/仮想コア)<br/>メモリ量を増やすには、仮想コアを追加します。 |
| インスタンスの予約済み最大ストレージ サイズ | - 4 仮想コアの場合は 2 TB (Gen5 のみ)<br/>- その他のサイズの場合は 8 TB | Gen4:1 TB (テラバイト) <br/> Gen5: <br/>- 4、8、16 仮想コアの場合は 1 TB<br/>- 24 仮想コアの場合は 2 TB<br/>- 32、40、64、80 仮想コアの場合は 4 TB |
| 最大データベース サイズ | インスタンスごとの最大ストレージ サイズによって決まります | インスタンスごとの最大ストレージ サイズによって決まります |
| インスタンスごとの最大データベース数 | 100 | 100 |
| インスタンスごとの最大データベース ファイル数 | 最大 280 | データベースあたり 32,767 ファイル |
| データ/ログの IOPS (概算) | ファイルあたり 500 ～ 7,500<br/>\*[IOPS を増やすには、ファイル サイズを大きくします](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)| 11 K - 110 K (1,375/仮想コア)<br/>IO パフォーマンスを向上させるには、仮想コアを追加します。 |
| ログ書き込みスループット制限 | 仮想コアあたり 3 MB/秒<br/>インスタンスあたり最大 22 MB/秒 | 仮想コアあたり 4 MB/秒<br/>インスタンスあたり最大 48 MB/秒|
| データ スループット (概算) | ファイルあたり 100 ～ 250 MB/秒<br/>\*[IO パフォーマンスを向上させるには、ファイル サイズを増やします](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes) | 該当なし |
| ストレージ IO 待機時間 (概算) | 5 ～ 10 ms | 1 ～ 2 ms |
| 最大 tempDB サイズ | 192 ～ 1,920 GB (仮想コアあたり 24 GB)<br/>tempdb 領域を増やすには、仮想コアを追加します。 | インスタンスの最大ストレージ サイズによって制限されます。 現在、termdb ログ ファイルのサイズは、仮想コアあたり 24 GB に制限されています。 |
| 最大セッション数 | 30000 | 30000 |

> [!NOTE]
> - ユーザー データベースとシステム データベースのデータ ファイルおよびログ ファイルのサイズはどちらも、最大ストレージ サイズの制限と比較されるインスタンス ストレージ サイズに含まれます。 データベースによって使用される合計領域を確認するには、<a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> システム ビューを使用します。 エラー ログは保持されず、サイズには含まれません。 バックアップは、ストレージ サイズに含まれません。
> - スループットと IOPS も、マネージド インスタンスによって明示的に制限されないページ サイズに依存します。

## <a name="supported-regions"></a>サポートされているリージョン

マネージド インスタンスは、[サポートされているリージョン](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)のみで作成できます。 現在サポートされていないリージョンでマネージド インスタンスを作成するには、[Azure portal 経由でサポート要求を送信](#obtaining-a-larger-quota-for-sql-managed-instance)できます。

## <a name="supported-subscription-types"></a>サポートされているサブスクリプションの種類

マネージド インスタンスは現在、次の種類のサブスクリプションのみでデプロイをサポートしています。

- [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [クラウド サービス プロバイダー (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [開発テスト用の従量課金制プラン](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio サブスクライバー向けの毎月の Azure クレジット付きサブスクリプション](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>リージョンのリソース制限

サポートされているサブスクリプションの種類には、リージョンごとのリソース数の制限を組み入れることができます。 マネージド インスタンスには、サブスクリプションの種類に応じて、Azure リージョンごとに 2 つの既定の制限があります。

- **サブネットの制限**: マネージド インスタンスが単一リージョンにデプロイされているサブネットの最大数。
- **仮想コアの制限**:1 つのリージョン内のすべてのインスタンスにデプロイできる仮想コアの最大数。

> [!Note]
> これらの制限は既定の設定であり、技術的な制限ではありません。 現在のリージョンでさらに多くのマネージド インスタンスが必要な場合、[Azure portal でサポート要求](#obtaining-a-larger-quota-for-sql-managed-instance)を特別に作成して、これらの制限をオンデマンドで引き上げることができます。 サポート要求を送信せずに、代わりに、別の Azure リージョンに新しいマネージド インスタンスを作成することも可能です。

次の表は、サポートされているサブスクリプションの既定のリージョン制限を示しています。

|サブスクリプションの種類| マネージド インスタンスのサブネットの最大数 | 最大仮想コア ユニット数* |
| :---| :--- | :--- |
|従量課金制|3|320|
|CSP |8 (リージョンによっては 15**)|960 (リージョンによっては 1440**)|
|開発テスト用の従量課金制プラン|3|320|
|Enterprise Dev/Test|3|320|
|EA|8 (リージョンによっては 15**)|960 (リージョンによっては 1440**)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional および MSDN Platforms|2|32|

\* デプロイを計画するときは、(冗長性が高くなるため) Business Critical (BC) 仮想コアで General Purpose (GP) 仮想コアの 4 倍を超える容量が消費されることを考慮します。 そのため、ご自分の計算では、1 GP 仮想コア = 1 仮想コア ユニット、1 BC 仮想コア = 4 仮想コア ユニットとします。 既定の制限に対する消費量分析を簡素化するために、マネージド インスタンスがデプロイされているリージョン内のすべてのサブネットの仮想コア ユニットを集計して、その結果をサブスクリプションの種類のインスタンス ユニットの制限と比較します。 「**最大仮想コア ユニット数**」の制限は、リージョン内の各サブスクリプションに適用されます。 個々のサブネットあたりの制限はありませんが、複数のサブネット全体のデプロイされたすべての仮想コアの合計は、「**最大仮想コア ユニット数**」以下である必要があります。

\*\* 次のリージョンには、より大きなサブネットと仮想コアの制限があります。オーストラリア東部、米国東部、米国東部 2、北ヨーロッパ、米国中南部、東南アジア、英国南部、西ヨーロッパ、米国西部 2。

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>SQL マネージド インスタンスでのより大きなクォータの取得

現在のリージョンでより多くのマネージド インスタンスが必要な場合、Azure portal を使用してクォータを拡張するためのサポート要求を送信します。
より大きなクォータを取得するプロセスを開始するには、次の手順を実行します。

1. **[ヘルプとサポート]** を開き、 **[新しいサポート要求]** をクリックします。

   ![ヘルプとサポート](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. 新しいサポート要求の [基本] タブで、次の手順を実行します。
   - **[問題の種類]** で、 **[サービスとサブスクリプションの制限 (クォータ)]** を選択します。
   - **[サブスクリプション]** で、ご使用のサブスクリプションを選択します。
   - **[クォータの種類]** で、 **[SQL Database Managed Instance]** を選択します。
   - **[サポート プラン]** で、お使いのサポート プランを選択します。

     ![[問題の種類] クォータ](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. **[次へ]** をクリックします。
4. 新しいサポート リクエストの **[問題]** タブで、次の手順を実行します。
   - **[重大度]** で、問題の重大度を選択します。
   - **[詳細]** で、エラー メッセージなど、問題に関する追加情報を指定します。
   - **[ファイルのアップロード]** で、より詳細な情報を含むファイル (最大 4 MB) を添付します。

     ![問題の詳細](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > 有効な要求には、次の情報を含める必要があります。
     > - サブスクリプションの制限を引き上げる必要があるリージョン。
     > - クォータが増加した後の既存のサブネットで、サービス レベルごとに必要な仮想コア数 (既存のサブネットのいずれかを拡張する必要がある場合)。
     > - 必要な新しいサブネット数と、新しいサブネット内でのサービス レベルあたりの仮想コア合計 (新しいサブネットにマネージド インスタンスをデプロイする必要がある場合)

5. **[次へ]** をクリックします。
6. 新しいサポート要求の [連絡先情報] タブで、希望する連絡方法 (電子メールまたは電話) と連絡先の詳細を入力します。
7. **Create** をクリックしてください。

## <a name="next-steps"></a>次の手順

- マネージド インスタンスの詳細については、[マネージド インスタンスとは何か](sql-database-managed-instance.md)に関する記事を参照してください。
- 料金情報については、[SQL Database マネージド インスタンスの価格](https://azure.microsoft.com/pricing/details/sql-database/managed/)に関するページを参照してください。
- 最初のマネージド インスタンスを作成する方法については、[クイック スタート ガイド](sql-database-managed-instance-get-started.md)を参照してください。
