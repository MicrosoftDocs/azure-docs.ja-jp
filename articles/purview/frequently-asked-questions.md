---
title: よく寄せられる質問 (FAQ)
description: この記事では、Azure Purview についてよく寄せられる質問に回答します。
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 94b765cbcbdd81505b08052845207ee1d93a28d9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101667806"
---
# <a name="frequently-asked-questions-faq-about-azure-purview"></a>Azure Purview に関してよく寄せられる質問 (FAQ)

## <a name="overview"></a>概要

多くの組織では、自社データの全体像を把握できていません。 どのようなデータがあるか、データはどこにあるか、関連するデータを検索してアクセスするにはどうしたらよいかを理解することは困難です。 データには系列、分類、包括的なメタデータなどのコンテキストが不足しているため、ビジネス ユーザーは適切なデータを検索し、そのデータを適切に使用することが難しくなります。 その結果、収集されたデータのごく一部しかビジネスの意思決定に使用されていません。 最後に、データのセキュリティ上の問題を特定することと、機密データを保護することには整合性がありません。 特にデータの機敏性を確保しているときは、継続的な時間と労力が必要になります。

Azure Purview は、データ ガバナンス ソリューションです。 これは、顧客が自社のすべてのデータの使用を制御しながら、それに関する深い知識を習得するのに役立ちます。 Azure Purview を使用すれば、組織はデータを検出し、キュレーションします。 自社のデータ資産に関する分析情報を取得し、データへのアクセスを一元的に管理します。

## <a name="purpose-of-this-faq"></a>この FAQ の目的

この FAQ では、顧客や現場のチームからよく寄せられる一般的な質問に回答します。 これは、Azure Purview および関連するソリューション (Azure Data Catalog (ADC) Gen 2 (非推奨) や Azure Information Protection など) に関する質問を明確にすることを目的としています。

### <a name="what-are-the-source-types-available-for-metadata-scanning-and-classification"></a>メタデータのスキャンと分類に使用できるソースの種類は何ですか?

|Azure|Azure 以外|
|---------|---------|
|Azure Blob Storage|Power BI|
|Azure Synapse Analytics (SQL DW)|SQL Server |
|Azure Cosmos DB|Teradata (2020 年末まで利用可能)|
|Azure SQL Managed Instance|SAP ECC (2020 年末まで利用可能)|
|Azure Data Explorer|AP S/4 HANA (2020 年末まで利用可能)|
|Azure Data Lake Storage Gen1|Hive メタストア (2020 年末まで利用可能)|
|Azure Data Lake Storage Gen2|Amazon S3|
|Azure Files|--|
|Azure SQL Database|--|

### <a name="what-data-systemsprocessors-can-we-connect-and-get-lineage"></a>どのようなデータ システムまたはプロセッサに接続して、系列を取得できますか?

|データ システムまたはプロセッサ 
|---------
|Azure Data Factory: コピー アクティビティ、データ フロー アクティビティ 
|カスタム系列   
|Azure Data Share   
|Power BI    |
|SQL Server Integration Services  

### <a name="how-are-adc-gen-2-azure-information-protection-and-azure-purview-related"></a>ADC Gen 2、Azure Information Protection、Azure Purview はどのように関連していますか?

Azure Purview はもともと ADC Gen 2 として開始されましたが、以来ずっとその範囲が拡大されてきました。 現在、ADC Gen 2 の高度なカタログ機能を、Azure Information Protection のデータ分類、ラベル付け、コンプライアンス ポリシー適用機能と組み合わせて使用できるようになりました。 今日では、データ ガバナンスの幅広い業界定義により厳密に適合しています。

### <a name="what-happens-to-customers-using-adc-gen-1"></a>ADC Gen 1 を使用している顧客はどうなりますか?

Microsoft のカタログ ソリューション分野では、Azure Purview がすべての製品イノベーションの焦点となっています。 ADC Gen 1 は引き続きサポートされます。

### <a name="can-customers-have-multiple-azure-purview-accounts-in-the-same-subscription"></a>同じサブスクリプションに複数の Azure Purview アカウントを割り当てることはできますか?

はい。サブスクリプションとテナントごとに、多数の Azure Purview アカウントがサポートされています。

### <a name="can-i-run-adc-gen-1-and-azure-purview-in-parallel"></a>ADC Gen 1 と Azure Purview を並行で実行できますか?

はい。 どちらも独立したサービスです。

### <a name="how-do-i-migrate-existing-adc-gen-1-data-assets-to-azure-purview"></a>ADC Gen 1 の既存のデータ資産を Azure Purview に移行するにはどうすればよいですか?

Azure Purview の API を使用して ADC Gen 1 から抽出し、Azure Purview に取り込みます。 用語集については、CSV に基づいた一括ツールがサポートされています。

### <a name="how-do-i-encrypt-sensitive-data-for-sql-tables-using-azure-purview"></a>Azure Purview を使用して SQL テーブルの機密データを暗号化するにはどうすればよいですか?

データの暗号化は、データ ソース レベルで行われます。 Azure Purview では、メタデータのみを格納します。 データのプレビューは行われません。

### <a name="will-all-the-capabilities-of-adc-gen-2-exist-in-azure-purview"></a>Azure Purview には ADC Gen 2 のすべての機能が含まれていますか?

はい。

<!--## Is the data lineage feature available in Azure Purview?

Yes, but it's limited to the Azure Data Factory connector.

<!-- ## How can I scan SQL Server on-premises? 

Use the self-host integration runtime capability. !-->

<!--### What is the difference between classification in Azure SQL Database and classification in Azure Purview?

|Azure SQL DB classification  |Azure Purview classification  |
|---------|---------|
|Classification is based on SQL metadata from system catalogs. |Classification is based on Azure Purview's sampling technique by using the system-defined or custom-defined regex pattern.|
|Custom classification is supported.     |Custom classification is supported.         |
|Doesn't use Microsoft 365 system classifiers out of the box.    | Uses Microsoft 365 system classifiers out of the box.        |
-->

### <a name="whats-the-difference-between-a-glossary-and-classification"></a>用語集と分類の違いは何ですか?

用語集では、データの非技術系またはビジネス ユーザー (データ コンシューマーとも呼ばれる) が従うべき名前付け規則が使用されています。 こうした種類のユーザーは、Azure Purview を使用し、ビジネスの使用状況に基づいて特定の種類のデータを検索するビジネス アナリストまたはデータ サイエンティストです。 たとえば、サプライ チェーン アナリストは、"*SKU の種類*" や "*出荷の詳細*" という用語の検索が必要になる場合があります。 用語集でこうした用語を検索すると、関連データを見つけることができます。
分類は、テーブル、列、またはファイル レベルでデータ資産に適用されるタグであり、これによって資産内に存在するデータを識別できます。 分類は、検出されたデータの種類に基づいて、自動または手動で適用できます。 通常、分類タグを使用すると、資産に機密データが含まれているかどうかや、どのような種類の機密データが含まれているかを特定できます。

### <a name="does-azure-purview-scan-and-classify-emails-pdfs-etc-in-my-sharepoint-and-onedrive"></a>Azure Purview では、SharePoint や OneDrive 内の電子メール、PDF などをスキャンして分類しますか?

オンプレミスの SharePoint のサイトおよびライブラリのスキャンは、Azure Information Protection スキャナーを通じて提供されます。 このスキャナーは、次の SKU を含む顧客の Microsoft 365 サブスクリプションで使用できます。AIP P1、EMS E3、M365 E3。 これらの SKU のいずれかをお持ちの場合は、Azure Information Protection スキャナーの使用を開始するための適切な権利があるはずです。

<!--### What is the difference between classifications and sensitivity labels in Azure Purview?

Azure Purview's data governance solution is based on the Apache Atlas framework. As defined by Atlas, classification is a way to identify the contents of an asset (table or file) or an entity (table column or structured file). This classification becomes a metadata property that allows Azure Purview to understand the data within each asset and govern and protect them.

Sensitivity labels are a Microsoft 365 concept that resembles classification at the asset level. You create a label with a collection of classifications applied at the asset or entity level.

Atlas-centric customers will see no real distinction between classifications and labels. To these customers, everything is a classification and labels aren't needed.

Security-focused customers will see a distinction between classification and labeling, but only because in Microsoft 365 the classifications aren't exposed directly to the user; only labels are visible. So, similar to Atlas, Office 365 security customers don't need to deal with both entities.
-->

### <a name="what-is-the-compute-used-for-the-scan"></a>スキャンに使用されるコンピューティングは何ですか?
Microsoft によって管理されるスキャン インフラストラクチャがあります。 サポートされているほとんどの Azure または AWS リソースでは、スキャン インフラストラクチャをデプロイする必要はありません。

### <a name="is-there-a-way-to-provision-azure-purview-via-azure-resource-manager-arm-template--cli--powershell"></a>Azure Resource Manager (ARM) のテンプレート、CLI、または PowerShell を使用して Azure Purview をプロビジョニングする方法はありますか?

はい、ARM テンプレートを使用できます

<!--### Does Azure Purview support guest users in AAD?-->

### <a name="im-already-using-atlas-can-i-easily-move-to-azure-purview"></a>Atlas を既に使用していますが、Azure Purview に簡単に移行できますか?

Azure Purview は、Atlas API と互換性があります。 Atlas から移行する場合は、まず Azure Purview を使用してデータ ソースをスキャンすることをお勧めします。 お使いのアカウントで資産を利用できるようになったら、同様の Atlas API を使用して、資産を更新したりカスタム系列を追加したりするなどの統合を行うことができます。 Azure Purview では、Azure Search を使用するように Search API を変更します。これにより、高度な検索を使用できるようになります。

### <a name="can-i-create-multiple-catalogs-in-my-tenant"></a>自分のテナントに複数のカタログを作成できますか?

はい。サブスクリプションとテナントごとに複数の Azure Purview アカウントを作成できます。 これらの制限については、[Azure Purview を使用したリソースのクォータの管理と引き上げ](how-to-manage-quotas.md)に関するページで確認できます。

複数のアカウントが必要な場合と必要ない場合のその他の推奨事項については、「[Azure Purview のデプロイのベストプラクティス](deployment-best-practices.md)」をご覧ください。

### <a name="can-i-register-multiple-tenants-within-a-single-azure-purview-account"></a>1 つの Azure Purview アカウント内に複数のテナントを登録できますか?

いいえ。現在、別のテナントのデータ ソースをスキャンするには、そのテナントに別の Azure Purview アカウントを作成する必要があります。

### <a name="does-azure-purview-support-column-level-lineage"></a>Azure Purview では列レベルの系列をサポートしていますか?

はい。Azure Purview では列レベルの系列をサポートしています。