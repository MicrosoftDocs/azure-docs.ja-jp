---
title: 秘密度ラベルをデータに自動的に適用する
description: 秘密度ラベルを作成し、スキャン中にデータに自動的に適用する方法について説明します。
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/09/2021
ms.openlocfilehash: 37ac292acc76c681ea38b2ae881ff8cd2ae5ec3c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102502448"
---
# <a name="automatically-label-your-data-in-azure-purview"></a>Azure Purview でデータに自動的にラベルを付ける

この記事では、Microsoft Information Protection (MIP) の秘密度ラベルを作成し、Azure Purview の Azure 資産に自動的に適用する方法について説明します。

## <a name="what-are-sensitivity-labels"></a>秘密度ラベルとは 

組織内のユーザーは、業務を完了するために組織の内外で他のユーザーと共同作業を行います。 データは常にクラウドにとどまっているわけではなく、多くの場合、デバイス、アプリ、サービスなどにどこでもローミングされます。 

データをローミングする場合は、組織のビジネス ポリシーとコンプライアンス ポリシーを満たす安全で保護された方法で行う必要があります。

秘密度ラベルを適用すると、組織内の特定の機密データの性質を指定できます。 たとえば、特定のプロジェクト名は組織内で非常に機密性が高いが、同じ用語が他の組織にとっては機密ではない場合があります。 

### <a name="sensitivity-labels-in-azure-purview"></a>Azure Purview の秘密度ラベル

Purview では、分類はサブジェクト タグに似ており、スキャン時にデータ資産内で見つかった特定の種類のデータをマークおよび識別するために使用されます。

Purview では、Microsoft 365 と同じ分類 (機密情報の種類とも呼ばれます) が使用されます。  MIP の秘密度ラベルは、Microsoft 365 セキュリティ/コンプライアンス センター (SCC) で作成されます。 これにより、Azure Purview の資産全体で既存の秘密度ラベルを拡張できます。

**分類** は、直接照合されます。たとえば、社会保障番号には、**社会保障番号** の分類があります。 

これに対して、**秘密度ラベル** は、1 つまたは複数の分類と条件が一緒に検出された場合に適用されます。 このコンテキストにおける [条件](/microsoft-365/compliance/apply-sensitivity-label-automatically)とは、*別の分類との近さ* や *信頼度* など、非構造化データに対して定義できるすべてのパラメーターを指します。 

Azure Purview で秘密度ラベルを使用すると、ファイルおよびデータベースの列にラベルを自動的に適用できます。

詳細については、次を参照してください。

- Microsoft 365 のドキュメントの「[秘密度ラベルの詳細](/microsoft-365/compliance/sensitivity-labels)」
- [自動ラベル付け規則とは](#what-are-auto-labeling-rules)
- [Azure Purview の秘密度ラベルでサポートされるデータ型](#supported-data-types-for-sensitivity-labels-in-azure-purview)
- [SQL データベースの列のラベル付け](#labeling-for-sql-database-columns)

#### <a name="what-are-auto-labeling-rules"></a>自動ラベル付け規則とは

データは絶えず増加して変化しています。 現在ラベルが付けられていないデータを追跡し、ラベルを手動で適用するアクションを実行するのは、面倒なだけでなく、不必要な頭痛の種でもあります。 

自動ラベル付け規則は、特定のラベルを適用するタイミングを指定する条件です。 これらの条件が満たされると、ラベルがデータに自動的に割り当てられ、一貫性のある秘密度ラベルが大規模なデータで維持されます。

ラベルを作成するときは、[ファイル](#define-auto-labeling-rules-for-files)と[データベースの列](#define-auto-labeling-rules-for-database-columns)の両方に対して自動ラベル付け規則を定義し、各データ スキャンでラベルが自動的に適用されるようにしてください。 

Purview でデータをスキャンすると、自動的に適用されたラベルを Purview Catalog と分析情報レポートに表示できます。
#### <a name="supported-data-types-for-sensitivity-labels-in-azure-purview"></a>Azure Purview の秘密度ラベルでサポートされるデータ型

Azure Purview では、次のデータ型に対して秘密度ラベルがサポートされます。

|データ型  |変換元  |
|---------|---------|
|ファイルの自動ラベル付け     |      - Azure Blob ストレージ  </br>- Azure Data Lake Storage Gen 1 および Gen 2  |
|データベースの列の自動ラベル付け     |  - SQL Server </br>- Azure SQL データベース </br>- Azure SQL Database Managed Instance   <br> - Azure Synapse  <br> - Azure Cosmos DB <br><br>詳細については、以下の「[SQL データベースの列のラベル付け](#labeling-for-sql-database-columns)」を参照してください。  |
| | |

#### <a name="labeling-for-sql-database-columns"></a>SQL データベースの列のラベル付け

Microsoft では、データベースの列に対する Purview のラベル付けに加えて、[SQL Server Management Studio (SSMS)](/sql/ssms/sql-server-management-studio-ssms) の SQL データ分類を使用した SQL データベースの列のラベル付けもサポートされています。 Purview ではグローバルの [MIP 秘密度ラベル](/microsoft-365/compliance/sensitivity-labels)が使用されますが、SSMS ではローカルに定義されているラベルのみが使用されます。

Purview でのラベル付けと SSMS でのラベル付けは、現在相互に連携していない別々のプロセスです。 したがって、SSMS で適用されるラベルは Purview には表示されず、その逆も同様です。 SQL データベースにラベルを付けるには、Azure Purview を使用することをお勧めします。これは、複数のプラットフォームに適用できるグローバルな MIP ラベルが使用されているからです。

詳しくは、「[SQL データの検出と分類](/sql/relational-databases/security/sql-data-discovery-and-classification)」のドキュメントをご覧ください。

## <a name="how-to-create-sensitivity-labels-in-microsoft-365"></a>Microsoft 365 で秘密度ラベルを作成する方法

秘密度ラベルがまだない場合は、それを作成して、Azure Purview で使用できるようにする必要があります。 既存の秘密度ラベルを変更して、Azure Purview で使用できるようにすることもできます。

詳細については、次を参照してください。

- [ライセンスの要件](#licensing-requirements)
- [Azure Purview への秘密度ラベルの拡張](#extending-sensitivity-labels-to-azure-purview)
- [新しい秘密度ラベルの作成または既存のラベルの変更](#creating-new-sensitivity-labels-or-modifying-existing-labels)
### <a name="licensing-requirements"></a>ライセンスの要件

MIP の秘密度ラベルは、Microsoft 365 セキュリティ/コンプライアンス センターで作成および管理されます。 Azure Purview で使用する秘密度ラベルを作成するには、アクティブな Microsoft 365 E5 ライセンスを保持している必要があります。

必要なライセンスをまだ保持していない場合は、[Microsoft 365 E5](https://www.microsoft.com/microsoft-365/business/compliance-solutions#midpagectaregion) の試用版にサインアップできます。

### <a name="extending-sensitivity-labels-to-azure-purview"></a>Azure Purview への秘密度ラベルの拡張

既定では、MIP の秘密度ラベルは Microsoft 365 の資産にのみ使用でき、ファイルや電子メールに適用できます。

Azure Purview の Azure 資産に MIP の秘密度ラベルを適用するには、ラベルの拡張について明示的に同意し、Purview で使用できるようにする特定のラベルを選択する必要があります。

Azure Purview で MIP の秘密度ラベルを拡張することにより、組織は、より幅広いデータ ソースで秘密度の検出、分類、および分析情報の取得を行うことができるようになり、コンプライアンスのリスクを最小限に抑えることができます。

> [!NOTE]
> Microsoft 365 と Azure Purview は別々のサービスであるため、異なるリージョンにデプロイされる可能性があります。 ラベル名とカスタムの機密情報の種類の名前は、お客様のデータと見なされ、データの機密性を保護し、GDPR 法を回避するために、既定で同じ地域の場所に保持されます。
>
> このため、ラベルとカスタムの機密情報の種類は、既定では Azure Purview に共有されず、Azure Purview での使用に同意する必要があります。

**秘密度ラベルを Purview に拡張するには:**

次の手順では、秘密度ラベルを Azure Purview で使用できるようにします。Azure Purview では、SQL 列、Azure Blob Storage 内のファイルなどの資産に秘密度ラベルを適用できます。

1. Microsoft 365 で、 **[Information Protection]** ページに移動します。 
1. **[Azure Purview の資産へのラベル付けを拡張する]** で、 **[有効にする]** ボタンを選択し、表示される確認ダイアログで **[はい]** を選択します。

例:

:::image type="content" source="media/create-sensitivity-label/extend-sensitivity-labels-to-purview-small.png" alt-text="[有効にする] を選択して、秘密度ラベルを Purview に拡張します" lightbox="media/create-sensitivity-label/extend-sensitivity-labels-to-purview.png":::
 
Azure Purview の資産にラベル付けを拡張すると、Purview で使用できるようにするラベルを選択できます。 詳細については、「[新しい秘密度ラベルの作成または既存のラベルの変更](#creating-new-sensitivity-labels-or-modifying-existing-labels)」を参照してください。
### <a name="creating-new-sensitivity-labels-or-modifying-existing-labels"></a>新しい秘密度ラベルの作成または既存のラベルの変更

Windows、macOS、iOS、および Android で Office アプリの秘密度ラベルを使用すると、ユーザーに対して 4 時間以内に新しいラベルが表示され、Web 上の Office では 1 時間以内に表示されます。 ただし、すべてのアプリとサービスに対して変更をレプリケートするには、最大 24 時間かかります。

> [!IMPORTANT]
> ユーザーへの影響が不明な場合は、ラベルを削除しないでください。 詳細については、Microsoft 365 のドキュメントの「[ラベルの解除と削除](/microsoft-365/compliance/create-sensitivity-labels#removing-and-deleting-labels)」を参照してください。
>

**新しい秘密度ラベルを作成または既存のラベルを変更するには**:

1. [Microsoft 365 セキュリティ/コンプライアンス センター](https://protection.office.com/homepage)を開きます。 

1. **[ソリューション]** で、 **[Information Protection]** を選択し、 **[ラベルの作成]** を選択します。 

    :::image type="content" source="media/create-sensitivity-label/create-sensitivity-label-full-small.png" alt-text="Microsoft 365 のセキュリティ/コンプライアンス センターで秘密度ラベルを作成します" lightbox="media/create-sensitivity-label/create-sensitivity-label-full.png":::

1. ラベルに名前を付けます。 その後、 **[Define the scope for this label]\(このラベルのスコープを定義\)** で、次を実行します。

    - すべての場合において、 **[Azure Purview assets]\(Azure Purview の資産\)** を選択します。
    - ファイルにラベルを付けるには、 **[ファイル & 電子メール]** も選択します。 このオプションは、データベース資産にのみラベルを付ける場合は必要ありません。 
    
    :::image type="content" source="media/create-sensitivity-label/create-label-scope-small.png" alt-text="Microsoft 365 セキュリティ/コンプライアンス センターでラベルを作成します" lightbox="media/create-sensitivity-label/create-label-scope.png":::

1. ウィザードの残りの指示に従って、ラベルの設定を行います。 

    特にファイルとデータベースの列の自動ラベル付け規則を定義します。

    - [ファイルの自動ラベル付け規則を定義する](#define-auto-labeling-rules-for-files)
    - [データベースの列の自動ラベル付け規則を定義する](#define-auto-labeling-rules-for-database-columns)

    ウィザードのオプションの詳細については、Microsoft 365 のドキュメントの「[機密ラベルでできること](/microsoft-365/compliance/sensitivity-labels#what-sensitivity-labels-can-do)」を参照してください。

1. 上記の手順を繰り返して、追加のラベルを作成します。 

    サブラベルを作成するには、親ラベル > **...**  >  **[その他のアクション]**  >  **[サブ ラベルの追加]** を選択します。

1. 既存のラベルを変更するには、 **[Information Protection]**  >  **[ラベル]** を参照して、ラベルを選択します。 

    次に、 **[ラベルの編集]** を選択して、ラベルの作成時に定義したすべての設定を使用して、**秘密度ラベルの編集** ウィザードを再度開きます。

    :::image type="content" source="media/create-sensitivity-label/edit-sensitivity-label-full-small.png" alt-text="既存の秘密度ラベルを編集します" lightbox="media/create-sensitivity-label/edit-sensitivity-label-full.png":::

1. すべてのラベルの作成が完了したら、ラベルの順序を表示し、必要に応じて順序を変更します。 

    ラベルの順序を変更するには、 **...** > **[その他のアクション]**  >  **[上へ移動]** または **[下へ移動]** を選択します。 

    詳細については、Microsoft 365 のドキュメントの「[ラベルの優先度 (順序の問題)](/microsoft-365/compliance/sensitivity-labels#label-priority-order-matters)」を参照してください。


「[データをスキャンしてラベルを自動的に適用する](#scan-your-data-to-apply-labels-automatically)」に進み、その後、次のトピックに進みます。

- [資産のラベルを表示する](#view-labels-on-assets)
- [分類と秘密度ラベルの分析情報レポートを表示する](#view-insight-reports-for-the-classifications-and-sensitivity-labels)

#### <a name="define-auto-labeling-rules-for-files"></a>ファイルの自動ラベル付け規則を定義する

ファイルの自動ラベル付け規則は、ウィザードでラベルを作成または編集するときに定義します。 

**[Office アプリの自動ラベル付け]** ページで、 **[Office アプリの自動ラベル付け]** を有効にして、データにラベルを自動的に適用する条件を定義します。

例:

:::image type="content" source="media/create-sensitivity-label/create-auto-labeling-rules-files-small.png" alt-text="Microsoft 365 セキュリティ/コンプライアンス センターでファイルの自動ラベル付け規則を定義します" lightbox="media/create-sensitivity-label/create-auto-labeling-rules-files.png":::
 
詳細については、Microsoft 365 のドキュメントの[データへの秘密度ラベルの自動適用](/microsoft-365/compliance/apply-sensitivity-label-automatically#how-to-configure-auto-labeling-for-office-apps)に関するページを参照してください。 

#### <a name="define-auto-labeling-rules-for-database-columns"></a>データベースの列の自動ラベル付け規則を定義する

データベースの列の自動ラベル付け規則は、ウィザードでラベルを作成または編集するときに定義します。 

**[Azure Purview の資産 (プレビュー)]** オプションで:

1. **[データベースの列の自動ラベル付け]** スライダーを選択します。

1. **[機密情報の種類の選択]** を選択して、ラベルに適用する機密情報の種類を選択します。

例:
        
:::image type="content" source="media/create-sensitivity-label/create-auto-labeling-rules-db-columns-small.png" alt-text="Microsoft 365 セキュリティ/コンプライアンス センターで SQL 列の自動ラベル付け規則を定義します" lightbox="media/create-sensitivity-label/create-auto-labeling-rules-db-columns.png":::

## <a name="scan-your-data-to-apply-labels-automatically"></a>データをスキャンしてラベルを自動的に適用する

Azure Purview でデータをスキャンすると、定義した自動ラベル付け規則に基づいて、作成したラベルが自動的に適用されます。 

Azure Purview のさまざまな資産でスキャンを設定する方法の詳細については、以下を参照してください。

|source  |リファレンス  |
|---------|---------|
|**Azure Blob Storage**     |[Azure Blob Storage の登録とスキャン](register-scan-azure-blob-storage-source.md)         |
|**Azure Data Lake Storage**     |[Azure Data Lake Storage Gen1 の登録とスキャン](register-scan-adls-gen1.md) </br>[Azure Data Lake Storage Gen2 の登録とスキャン](register-scan-adls-gen2.md)         |
|**Azure SQL Databases**|[Azure SQL Database の登録とスキャン](register-scan-azure-sql-database.md) </br>[Azure SQL Database Managed Instance の登録とスキャン](register-scan-azure-sql-database-managed-instance.md)|
| | |

## <a name="view-labels-on-assets"></a>資産のラベルを表示する

Microsoft 365 でラベルの自動ラベル付け規則を定義し、Azure Purview でデータをスキャンすると、ラベルが資産に自動的に適用されます。 

**Azure Purview Catalog の資産に適用されているラベルを表示するには:**

Azure Purview Catalog で、 **[ラベル]** フィルター オプションを使用して、特定のラベルを持つファイルのみを表示します。 例: 

:::image type="content" source="media/create-sensitivity-label/filter-search-results-small.png" alt-text="ラベルで資産を検索します" lightbox="media/create-sensitivity-label/filter-search-results.png":::

例:

:::image type="content" source="media/create-sensitivity-label/view-labeled-files-blob-storage-small.png" alt-text="Azure Blob Storage 内のファイルの秘密度ラベルを表示します" lightbox="media/create-sensitivity-label/view-labeled-files-blob-storage.png":::

## <a name="view-insight-reports-for-the-classifications-and-sensitivity-labels"></a>分類と秘密度ラベルの分析情報レポートを表示する

Azure Purview の分類されてラベル付けされたデータに関する分析情報を取得するには、**分類** と **秘密度ラベル** のレポートを使用します。

> [!div class="nextstepaction"]
> [分類の分析情報](./classification-insights.md)

> [!div class="nextstepaction"]
> [秘密度ラベルの分析情報](sensitivity-insights.md)
