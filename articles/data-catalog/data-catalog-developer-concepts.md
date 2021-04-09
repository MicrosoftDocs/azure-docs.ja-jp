---
title: Azure Data Catalog 開発者の概念
description: Catalog REST API によって公開される Azure Data Catalog 概念モデルの主要な概念の概要を説明します。
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: ddeab4838feb07d1101993cab4ebc86581b4d8b1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674701"
---
# <a name="azure-data-catalog-developer-concepts"></a>Azure Data Catalog 開発者の概念

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Microsoft **Azure Data Catalog** は、データ ソース検出およびデータ ソース メタデータのクラウド ソーシングの機能を提供する、フル マネージドのクラウド サービスです。 開発者は、REST API を介してサービスを使用できます。 開発者が **Azure Data Catalog** を使いこなすには、サービスに実装されている概念を理解することが重要です。

## <a name="key-concepts"></a>主要な概念 
**Azure Data Catalog** の概念モデルは、**カタログ**、**ユーザー**、**資産**、**注釈** という 4 つの主要な概念に基づいています。

![Azure Data Catalog 概念モデルの図](./media/data-catalog-developer-concepts/concept2.png)

### <a name="catalog"></a>Catalog
**カタログ** は、組織が格納するすべてのメタデータの最上位レベルのコンテナーです。 Azure アカウントごとに許容される **カタログ** 1 つだけです。 カタログは Azure サブスクリプションに関連付けられますが、アカウントに複数のサブスクリプションが含まれる場合でも、所定の Azure アカウントに対して作成できる **カタログ** は 1 つだけです。

カタログには、 **ユーザー** と **資産** が含まれています。

### <a name="users"></a>ユーザー
ユーザーは、カタログで操作 (カタログの検索、項目の追加、編集、削除など) を実行する権限を持つセキュリティ プリンシパルです。

ユーザーが担うことができるロールには、さまざまなものがあります。 ロールの詳細については、ロールと承認に関するセクションを参照してください。

個々のユーザーとセキュリティ グループを追加できます。

Azure Data Catalog では、ID およびアクセス管理のために Azure Active Directory が使用されます。 各カタログのユーザーは、アカウントの Active Directory のメンバーである必要があります。

### <a name="assets"></a>アセット
**カタログ** にはデータ資産が含まれています。 **資産** は、カタログによって管理される粒度の単位です。

資産の粒度は、データ ソースによって異なります。 SQL Server または Oracle Database の資産には、テーブルまたはビューを指定できます。 SQL Server Analysis Services の資産には、メジャー、ディメンション、または主要業績評価指標 (KPI) を指定できます。 SQL Server Reporting Services の資産は、レポートになります。

**資産** は、カタログに追加またはカタログから削除する項目です。 これは、 **検索** で取得する結果の単位です。

**資産** は、その名前、場所、および種類と、詳細に説明する注釈で構成されています。

### <a name="annotations"></a>注釈
注釈は、資産に関するメタデータを表す項目です。

注釈の例としては、説明、タグ、スキーマ、ドキュメントなどがあります。資産の型と注釈の型の完全な一覧については、「[資産オブジェクト モデル](#asset-object-model)」セクションを参照してください。

## <a name="crowdsourcing-annotations-and-user-perspective-multiplicity-of-opinion"></a>注釈のクラウド ソーシングとユーザーの観点 (意見の多重度)
Azure Data Catalog の重要な側面は、システム内のメタデータのクラウドソーシングをどのようにサポートするかということです。 1 つの意見しか存在せず、最後の書き込みを優先する wiki アプローチとは異なり、Azure Data Catalog モデルでは、システム内に複数の意見が共存できます。

このアプローチは、所定の資産に対してさまざまなユーザーがさまざまな観点を持ちうるという、企業データの現実世界を反映しています。

* データベース管理者は、サービス レベル アグリーメント、または ETL の一括操作に使用可能な処理ウィンドウに関する情報を提供する場合があります
* データ スチュワードは、資産が適用されるビジネス プロセス、またはビジネスで資産に適用した分類に関する情報を提供する場合があります
* 財務アナリストは、期末レポート タスク中のデータの使用方法に関する情報を提供する場合があります

この例をサポートするために、DBA、データ スチュワード、アナリストの各ユーザーは、カタログに登録されている単一のテーブルに説明を追加できます。 システムですべての説明が管理され、Azure Data Catalog ポータルにすべての説明が表示されます。

このパターンはオブジェクト モデルのほとんどの項目に適用されます。JSON ペイロードのオブジェクト型が、多くの場合、シングルトンが想定されるプロパティの配列であるのはこのためです。

たとえば、資産下で、ルートは説明オブジェクトの配列です。 配列プロパティは、“descriptions” という名前です。 説明オブジェクトには、description という 1 つのプロパティがあります。 パターンとしては、description を入力する各ユーザーは、そのユーザーが指定した値に対して作成された説明オブジェクトを取得します。

その後、UX は組み合わせの表示方法を選択できます。 表示には 3 つの異なるパターンがあります。

* 最も単純なパターンは、「すべて表示」です。 このパターンでは、すべてのオブジェクトがリスト ビューに表示されます。 このパターンは、Azure Data Catalog ポータル UX が説明に対して使用します。
* もう 1 つのパターンは、「統合」です。 このパターンでは、さまざまなユーザーからのすべての値がマージされ、重複部分が削除されます。 Azure Data Catalog ポータル UX でのこのパターンの例には、タグやエキスパート プロパティがあります。
* 3 つ目のパターンは、「最後の書き込みを優先」です。 このパターンでは、最後に入力された最新の値のみが表示されます。 このパターンの例には、friendlyName があります。

## <a name="asset-object-model"></a>資産オブジェクト モデル
「主要な概念」セクションで説明したように、 **Azure Data Catalog** オブジェクト モデルには、資産または注釈を指定できる項目が含まれます。 項目には、省略可能または必須のプロパティがあります。 一部のプロパティは、すべての項目に適用されます。 一部のプロパティは、すべての資産に適用されます。 一部のプロパティは、特定の資産の型のみに適用されます。

### <a name="system-properties"></a>システム プロパティ
<table><tr><td><b>プロパティ名</b></td><td><b>[データ型]</b></td><td><b>コメント</b></td></tr><tr><td>timestamp</td><td>DateTime</td><td>項目が最後に変更された日時。 このフィールドは、項目が挿入されたときと、項目が更新されるたびに、サーバーによって生成されます。 公開操作の入力時に、このプロパティの値は無視されます。</td></tr><tr><td>ID</td><td>Uri</td><td>項目の絶対 URL (読み取り専用)。 項目に対する一意のアドレス可能 URI です。  公開操作の入力時に、このプロパティの値は無視されます。</td></tr><tr><td>type</td><td>String</td><td>資産の型 (読み取り専用)。</td></tr><tr><td>etag</td><td>String</td><td>カタログ内の項目を更新する操作を実行するときに、オプティミスティック コンカレンシーに使用できる項目のバージョンに対応する文字列。 *" を使用して任意の値と照合することができます。</td></tr></table>

### <a name="common-properties"></a>共通プロパティ
これらのプロパティは、すべてのルート資産の型とすべての注釈の型に適用されます。

<table>
<tr><td><b>プロパティ名</b></td><td><b>[データ型]</b></td><td><b>コメント</b></td></tr>
<tr><td>fromSourceSystem</td><td>Boolean</td><td>項目のデータがソース システム (SQL Server データベース、Oracle Database など) から派生したのか、またはユーザーによって作成されたのかを示します。</td></tr>
</table>

### <a name="common-root-properties"></a>共通のルート プロパティ
<p>
これらのプロパティは、すべてのルート資産の型に適用されます。

<table><tr><td><b>プロパティ名</b></td><td><b>[データ型]</b></td><td><b>コメント</b></td></tr><tr><td>name</td><td>String</td><td>データ ソースの場所の情報から派生した名前。</td></tr><tr><td>dsl</td><td>DataSourceLocation</td><td>データ ソースを一意に説明するもので、資産の識別子の 1 つです  (デュアル ID のセクションを参照してください)。  dsl の構造は、プロトコルとソースの種類によって異なります。</td></tr><tr><td>dataSource</td><td>DataSourceInfo</td><td>資産の型の詳細な説明。</td></tr><tr><td>lastRegisteredBy</td><td>SecurityPrincipal</td><td>この資産を最後に登録したユーザーを説明します。  ユーザーの一意の ID (upn) と、表示名 (lastName および firstName) の両方が含まれています。</td></tr><tr><td>containerID</td><td>String</td><td>データ ソースのコンテナーの資産の ID です。 このプロパティは、コンテナー型ではサポートされていません。</td></tr></table>

### <a name="common-non-singleton-annotation-properties"></a>シングルトン以外の注釈の共通プロパティ
これらのプロパティは、シングルトン以外のすべての注釈の型に適用されます (1 つの資産に対して複数の注釈を適用できます)。

<table>
<tr><td><b>プロパティ名</b></td><td><b>[データ型]</b></td><td><b>コメント</b></td></tr>
<tr><td>key</td><td>String</td><td>現在のコレクション内で注釈を一意に識別するユーザー指定キー。 キーの長さは 256 文字を超えてはなりません。</td></tr>
</table>

### <a name="root-asset-types"></a>ルート資産の型
ルート資産の型は、カタログに登録できるデータ資産のさまざまな種類を表す型です。 ルート型ごとにビューがあり、ビューに含まれる資産と注釈が説明されています。 ビュー名は、REST API を使用して資産を発行するときに、対応する {view_name} url セグメントで使用する必要があります。

<table><tr><td><b>資産の種類 (ビュー名)</b></td><td><b>[追加のプロパティ]</b></td><td><b>[データ型]</b></td><td><b>使用できる注釈</b></td><td><b>コメント</b></td></tr><tr><td>Table ("tables")</td><td></td><td></td><td>説明<p>FriendlyName<p>タグ<p>スキーマ<p>ColumnDescription<p>ColumnTag<p> Expert<p>プレビュー<p>AccessInstruction<p>TableDataProfile<p>ColumnDataProfile<p>ColumnDataClassification<p>ドキュメント<p></td><td>テーブルは、表形式のデータを表します。  例: SQL テーブル、SQL ビュー、Analysis Services 表形式テーブル、Analysis Services 多次元ディメンション、Oracle テーブルなど。   </td></tr><tr><td>Measure ("measures")</td><td></td><td></td><td>説明<p>FriendlyName<p>タグ<p>Expert<p>AccessInstruction<p>ドキュメント<p></td><td>この型は、Analysis Services のメジャーを表します。</td></tr><tr><td></td><td>measure</td><td>列</td><td></td><td>メジャーを説明するメタデータ。</td></tr><tr><td></td><td>isCalculated </td><td>Boolean</td><td></td><td>メジャーが計算されるかどうかを指定します。</td></tr><tr><td></td><td>measureGroup</td><td>String</td><td></td><td>メジャーの物理的なコンテナー。</td></tr><td>KPI ("kpis")</td><td></td><td></td><td>説明<p>FriendlyName<p>タグ<p>Expert<p>AccessInstruction<p>ドキュメント</td><td></td></tr><tr><td></td><td>measureGroup</td><td>String</td><td></td><td>メジャーの物理的なコンテナー。</td></tr><tr><td></td><td>goalExpression</td><td>String</td><td></td><td>KPI のターゲット値を返す MDX 数値式または計算。</td></tr><tr><td></td><td>valueExpression</td><td>String</td><td></td><td>KPI の実際の値を返す MDX 数値式。</td></tr><tr><td></td><td>statusExpression</td><td>String</td><td></td><td>特定時点の KPI の状態を示す MDX 式。</td></tr><tr><td></td><td>trendExpression</td><td>String</td><td></td><td>一定期間にわたり、KPI の値を評価する MDX 式。 傾向は、特定のビジネス コンテキストで役に立つ任意の時間ベースの条件です。</td>
<tr><td>Report ("reports")</td><td></td><td></td><td>説明<p>FriendlyName<p>タグ<p>Expert<p>AccessInstruction<p>ドキュメント<p></td><td>この型は、SQL Server Reporting Services のレポートを表します。 </td></tr><tr><td></td><td>assetCreatedDate</td><td>String</td><td></td><td></td></tr><tr><td></td><td>assetCreatedBy</td><td>String</td><td></td><td></td></tr><tr><td></td><td>assetModifiedDate</td><td>String</td><td></td><td></td></tr><tr><td></td><td>assetModifiedBy</td><td>String</td><td></td><td></td></tr><tr><td>Container ("containers")</td><td></td><td></td><td>説明<p>FriendlyName<p>タグ<p>Expert<p>AccessInstruction<p>ドキュメント<p></td><td>この型は、SQL データベース、Azure BLOB コンテナー、Analysis Services モデルなど、その他の資産のコンテナーを表します。</td></tr></table>

### <a name="annotation-types"></a>注釈の型
注釈の型は、カタログ内で他の型に割り当てることのできるメタデータの種類を表します。

<table>
<tr><td><b>注釈の種類 (入れ子になったビュー名)</b></td><td><b>[追加のプロパティ]</b></td><td><b>[データ型]</b></td><td><b>コメント</b></td></tr>

<tr><td>Description ("descriptions")</td><td></td><td></td><td>このプロパティには、資産の説明が含まれています。 システムの各ユーザーは、独自の説明を追加できます。  そのユーザーだけが説明オブジェクトを編集できます   (管理者および資産の所有者は、Description オブジェクトを削除できますが、編集することはできません)。 システムでは、ユーザーの説明を個別に管理します。  したがって、資産ごとに説明の配列が存在します (場合により、データ ソースから派生した情報を含む説明。加えて、資産に関する知識を提供した各ユーザーの説明)。</td></tr>
<tr><td></td><td>description</td><td>string</td><td>資産の簡単な説明 (2 ～ 3 行)</td></tr>

<tr><td>Tag ("tags")</td><td></td><td></td><td>このプロパティで、資産のタグを定義します。 システムの各ユーザーは、資産に対して複数のタグを追加できます。  Tag オブジェクトを作成したユーザーのみがそれらのオブジェクトを編集できます  (管理者および資産の所有者は、Tag オブジェクトを削除できますが、編集することはできません)。 システムでは、ユーザーのタグを個別に管理します。  したがって、各資産に Tag オブジェクトの配列があります。</td></tr>
<tr><td></td><td>tag</td><td>string</td><td>資産を説明するタグ。</td></tr>

<tr><td>FriendlyName ("friendlyName")</td><td></td><td></td><td>このプロパティには、資産のフレンドリ名が含まれています。 FriendlyName はシングルトンの注釈です。1 つの FriendlyName のみを資産に追加できます。  FriendlyName オブジェクトを作成したユーザーのみがこのオブジェクトを編集できます (管理者および資産の所有者は、FriendlyName オブジェクトを削除できますが、編集することはできません)。 システムでは、ユーザーのフレンドリ名を個別に管理します。</td></tr>
<tr><td></td><td>friendlyName</td><td>string</td><td>資産のフレンドリ名。</td></tr>

<tr><td>Schema ("schema")</td><td></td><td></td><td>スキーマは、データの構造を説明します。  属性 (列、属性、フィールドなど) の名前と型、およびその他のメタデータを一覧表示します。  この情報はすべて、データ ソースから派生します。  スキーマはシングルトンの注釈です。1 つのスキーマのみを資産に追加できます。</td></tr>
<tr><td></td><td>列</td><td>Column[]</td><td>列オブジェクトの配列。 データ ソースから派生した情報を含む列を説明します。</td></tr>

<tr><td>ColumnDescription ("columnDescriptions")</td><td></td><td></td><td>このプロパティには、列の説明が含まれています。  システムの各ユーザーは、複数の列に対して独自の説明 (1 列ごとに最大 1 つの説明) を追加できます。 ColumnDescription オブジェクトを作成したユーザーのみがそれらのオブジェクトを編集できます  (管理者および資産の所有者は、ColumnDescription オブジェクトを削除できますが、編集することはできません)。 システムは、ユーザーの列の説明を個別に管理します。  したがって、資産ごとに ColumnDescription オブジェクトの配列が存在します (場合により、データ ソースから派生した情報を含む説明に加えて、列に関する知識を提供した各ユーザーの 1 列あたり 1 つの説明)。  ColumnDescription はスキーマに疎結合されているため、同期から除外することができます。ColumnDescription は、スキーマに存在しなくなった列を表す場合があります。  説明とスキーマの同期を保つのは、ライターの責任です。データ ソースは列の説明情報を持つこともできます。これらは、ツールの実行時に作成される追加の ColumnDescription オブジェクトです。</td></tr>
<tr><td></td><td>columnName</td><td>String</td><td>この説明が参照する列の名前。</td></tr>
<tr><td></td><td>description</td><td>String</td><td>列の簡単な説明 (2 ～ 3 行)。</td></tr>

<tr><td>ColumnTag ("columnTags")</td><td></td><td></td><td>このプロパティには、列のタグが含まれています。 システムの各ユーザーは、特定の 1 つの列に対して複数のタグを追加できます。複数の列に対して複数のタグを追加することもできます。 ColumnTag オブジェクトを作成したユーザーのみがそれらのオブジェクトを編集できます (管理者および資産の所有者は、ColumnTag オブジェクトを削除できますが、編集することはできません)。 システムは、ユーザーの列のタグを個別に管理します。  したがって、各資産に ColumnTag オブジェクトの配列があります。  ColumnTag はスキーマに疎結合されているため、同期から除外することができます。ColumnTag は、スキーマに存在しなくなった列を表す場合があります。  列のタグとスキーマの同期を保つのは、ライターの責任です。</td></tr>
<tr><td></td><td>columnName</td><td>String</td><td>このタグが参照する列の名前。</td></tr>
<tr><td></td><td>タグ</td><td>String</td><td>列を説明するタグ。</td></tr>

<tr><td>Expert ("experts")</td><td></td><td></td><td>このプロパティには、データ セット内のエキスパートと見なされるユーザーが含まれています。 説明の一覧を表示すると、エキスパートの意見 (説明) が UX の一番上に表示されます。 各ユーザーは、独自のエキスパートを指定できます。 そのユーザーだけがエキスパートのオブジェクトを編集できます (管理者および資産の所有者は、Expert オブジェクトを削除できますが、編集することはできません)。</td></tr>
<tr><td></td><td>expert</td><td>SecurityPrincipal</td><td></td></tr>

<tr><td>Preview ("previews")</td><td></td><td></td><td>プレビューには、資産のデータの上位 20 行のスナップショットが含まれています。 プレビューは、一部の資産の型に対してのみ意味を持ちます (テーブルに対しては意味を持ちますが、メジャーに対しては意味を持ちません)。</td></tr>
<tr><td></td><td>preview</td><td>object[]</td><td>列を表すオブジェクトの配列。  各オブジェクトには、列へのプロパティ マッピングが、行に対するその列の値と共に含まれます。</td></tr>

<tr><td>AccessInstruction ("accessInstructions")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>mimeType</td><td>string</td><td>コンテンツの MIME の種類。</td></tr>
<tr><td></td><td>コンテンツ</td><td>string</td><td>このデータ資産にアクセスする方法の手順。 コンテンツには、URL、電子メール アドレス、一連の手順が考えられます。</td></tr>

<tr><td>TableDataProfile ("tableDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>numberOfRows</td></td><td>INT</td><td>データ セット内の行の数</td></tr>
<tr><td></td><td>size</td><td>long</td><td>データ セットのバイト単位のサイズ。  </td></tr>
<tr><td></td><td>schemaModifiedTime</td><td>string</td><td>スキーマが最後に変更された日時</td></tr>
<tr><td></td><td>dataModifiedTime</td><td>string</td><td>データ セットが最後に変更された (データが追加、変更、または削除された) 日時</td></tr>

<tr><td>ColumnsDataProfile ("columnsDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>列</td></td><td>ColumnDataProfile[]</td><td>列のデータ プロファイルの配列。</td></tr>

<tr><td>ColumnDataClassification ("columnDataClassifications")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName</td><td>String</td><td>この分類が参照する列の名前。</td></tr>
<tr><td></td><td>分類</td><td>String</td><td>この列のデータの分類。</td></tr>

<tr><td>Documentation ("documentation")</td><td></td><td></td><td>特定の資産には 1 つのドキュメントしか関連付けることができません。</td></tr>
<tr><td></td><td>mimeType</td><td>string</td><td>コンテンツの MIME の種類。</td></tr>
<tr><td></td><td>コンテンツ</td><td>string</td><td>ドキュメントのコンテンツ。</td></tr>

</table>

### <a name="common-types"></a>共通の型
共通の型は、プロパティの型として使用できますが、項目ではありません。

<table>
<tr><td><b>共通の型</b></td><td><b>Properties</b></td><td><b>[データ型]</b></td><td><b>コメント</b></td></tr>
<tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr>
<tr><td></td><td>sourceType</td><td>string</td><td>データ ソースの種類について説明します。  例: SQL Server、Oracle Database など。  </td></tr>
<tr><td></td><td>objectType</td><td>string</td><td>データ ソース内のオブジェクトの種類について説明します。 例: SQL Server のテーブル、ビューなど。</td></tr>

<tr><td>DataSourceLocation</td><td></td><td></td><td></td></tr>
<tr><td></td><td>protocol</td><td>string</td><td>必須。 データ ソースとの通信に使用されるプロトコルについて説明します。 たとえば、`tds` (SQL Server の場合) や `oracle` (Oracle の場合) のように指定します。現在サポートされているプロトコルの一覧については、[データ ソース参照の仕様の DSL 構造体](data-catalog-dsr.md)を参照してください。</td></tr>
<tr><td></td><td>address</td><td>ディクショナリ&lt;文字列、オブジェクト&gt;</td><td>必須。 アドレスは、参照対象データ ソースの識別に使用されるプロトコルに固有の一連のデータです。 特定のプロトコルを対象にしたアドレス データ。つまり、プロトコルが不明な場合は意味がありません。</td></tr>
<tr><td></td><td>認証</td><td>string</td><td>省略可能。 データ ソースとの通信に使用される認証スキーマ。 例: windows、oauth など。</td></tr>
<tr><td></td><td>connectionProperties</td><td>ディクショナリ&lt;文字列、オブジェクト&gt;</td><td>省略可能。 データ ソースに接続する方法の追加情報。</td></tr>

<tr><td>SecurityPrincipal</td><td></td><td></td><td>バックエンドでは公開時に Azure Active Directory に対して指定されたプロパティの検証は実行されません。</td></tr>
<tr><td></td><td>upn</td><td>string</td><td>ユーザーの一意の電子メール アドレス。 objectId が指定されていない場合や、"lastRegisteredBy" プロパティのコンテキストでは、この値を指定する必要があります。それ以外の場合は、任意で指定できます。</td></tr>
<tr><td></td><td>objectId</td><td>Guid</td><td>ユーザーまたはセキュリティ グループの Azure Active Directory ID。 省略可能。 upn が指定されていない場合には、この値を指定する必要があります。それ以外の場合は、任意で指定できます。</td></tr>
<tr><td></td><td>firstName</td><td>string</td><td>ユーザーの名前 (表示用)。 省略可能。 "lastRegisteredBy" プロパティのコンテキストでのみ有効です。 "roles"、"permissions"、および "experts" のセキュリティ プリンシパルを指定するときに、この値を指定することはできません。</td></tr>
<tr><td></td><td>lastName</td><td>string</td><td>ユーザーの姓 (表示用)。 省略可能。 "lastRegisteredBy" プロパティのコンテキストでのみ有効です。 "roles"、"permissions"、および "experts" のセキュリティ プリンシパルを指定するときに、この値を指定することはできません。</td></tr>

<tr><td>列</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>string</td><td>列または属性の名前。</td></tr>
<tr><td></td><td>type</td><td>string</td><td>列または属性のデータ型。 使用可能な型は、資産の sourceType データによって異なります。  型のサブセットのみがサポートされます。</td></tr>
<tr><td></td><td>maxLength</td><td>INT</td><td>列または属性に使用できる最大長。 データ ソースから派生します。 一部のソースの種類のみに適用されます。</td></tr>
<tr><td></td><td>精度</td><td>byte</td><td>列または属性の有効桁数。 データ ソースから派生します。 一部のソースの種類のみに適用されます。</td></tr>
<tr><td></td><td>isNullable</td><td>Boolean</td><td>列が null 値を含むことができるかどうか。 データ ソースから派生します。 一部のソースの種類のみに適用されます。</td></tr>
<tr><td></td><td>expression</td><td>string</td><td>値が計算列である場合、このフィールドには値を表す式が含まれています。 データ ソースから派生します。 一部のソースの種類のみに適用されます。</td></tr>

<tr><td>ColumnDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName </td><td>string</td><td>列の名前</td></tr>
<tr><td></td><td>type </td><td>string</td><td>列の種類</td></tr>
<tr><td></td><td>分 </td><td>string</td><td>データ セット内の最小値</td></tr>
<tr><td></td><td>最大 </td><td>string</td><td>データ セット内の最大値</td></tr>
<tr><td></td><td>avg </td><td>double</td><td>データ セット内の平均値</td></tr>
<tr><td></td><td>stdev </td><td>double</td><td>データ セットの標準偏差</td></tr>
<tr><td></td><td>nullCount </td><td>INT</td><td>データ セット内の null 値の数</td></tr>
<tr><td></td><td>distinctCount  </td><td>INT</td><td>データ セット内の異なる値の数</td></tr>
</table>

## <a name="asset-identity"></a>資産 ID
Azure Data Catalog は、DataSourceLocation "dsl" プロパティの "address" プロパティ バッグから "protocol" プロパティと ID プロパティを使用して、Catalog 内の資産のアドレス指定に使用される資産の ID を生成します。
たとえば、表形式データ ストリーム (TDS) プロトコルには、"server"、"database"、"schema"、"object" の ID プロパティがあります。 プロトコルと ID プロパティの組み合わせを使用して、SQL Server テーブル資産の ID を生成します。
Azure Data Catalog には、組み込みのデータ ソース プロトコルがいくつかあります (「 [Azure Data Catalog でサポートされるデータ ソース](data-catalog-dsr.md)」を参照してください)。
サポートされるプロトコル セットは、プログラムで拡張できます (Data Catalog REST API リファレンスを参照してください)。 Catalog の管理者は、カスタム データ ソース プロトコルを登録できます。 次の表は、カスタム プロトコルの登録に必要なプロトコルの説明です。

### <a name="custom-data-source-protocol-specification"></a>カスタム データ ソース プロトコルの仕様
<table>
<tr><td><b>Type</b></td><td><b>Properties</b></td><td><b>[データ型]</b></td><td><b>コメント</b></td></tr>

<tr><td>DataSourceProtocol</td><td></td><td></td><td></td></tr>
<tr><td></td><td>namespace</td><td>string</td><td>プロトコルの名前空間。 名前空間の長さは 1 ～ 255 文字で、ドット (.) で区切られた 1 つ以上の空ではない部分を含める必要があります。 各部分の長さは 1 ～ 255 文字で、先頭には英字を指定し、英数字のみを使用する必要があります。</td></tr>
<tr><td></td><td>name</td><td>string</td><td>プロトコルの名前。 名前の長さは 1 ～ 255 文字で、先頭には英字を指定し、英数字とダッシュ (-) 文字のみを使用する必要があります。</td></tr>
<tr><td></td><td>identityProperties</td><td>DataSourceProtocolIdentityProperty[]</td><td>ID プロパティの一覧。プロパティは 1 個以上 20 個以下にする必要があります。 たとえば、"server"、"database"、"schema"、"object" は "tds" プロトコルの ID プロパティです。</td></tr>
<tr><td></td><td>identitySets</td><td>DataSourceProtocolIdentitySet[]</td><td>ID セットの一覧。 有効な資産の ID を示す ID プロパティのセットを定義します。 1 個以上 20 個以下のセット数を含める必要があります。 例: {"server"、"database"、"schema"、"object"} は、TDS プロトコルの ID セットです。TDS には、SQL Server Table 資産の ID が定義されています。</td></tr>

<tr><td>DataSourceProtocolIdentityProperty</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>string</td><td>プロパティの名前。 名前の長さは 1 ～ 100 文字で、先頭には英字を指定し、英数字のみを使用する必要があります。</td></tr>
<tr><td></td><td>type</td><td>string</td><td>プロパティの型。 サポートされる値: "bool"、boolean"、"byte"、"guid"、"int"、"integer"、"long"、"string"、"url"</td></tr>
<tr><td></td><td>ignoreCase</td><td>[bool]</td><td>プロパティ値を使用するときに大文字と小文字の区別を無視するかどうかを指定します。 "string" 型のプロパティにのみ指定できます。 既定値は false です。</td></tr>
<tr><td></td><td>urlPathSegmentsIgnoreCase</td><td>bool[]</td><td>URL のパスの各セグメントで大文字と小文字の区別を無視するかどうかを指定します。 "url" 型のプロパティにのみ指定できます。 既定値は [false] です。</td></tr>

<tr><td>DataSourceProtocolIdentitySet</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>string</td><td>ID セットの名前。</td></tr>
<tr><td></td><td>properties</td><td>string[]</td><td>この ID セットに含まれる ID プロパティの一覧。 重複を含めることはできません。 ID セットから参照されている各プロパティは、プロトコルの "identityProperties" の一覧で定義されている必要があります。</td></tr>

</table>

## <a name="roles-and-authorization"></a>ロールと承認
Microsoft Azure Data Catalog は、資産と注釈の CRUD 操作に対する承認機能を提供します。

Azure Data Catalog では、次の 2 つの承認機構が使用されます。

* ロール ベースの承認
* アクセス許可ベースの承認

### <a name="roles"></a>ロール
**管理者**、**所有者**、**共同作成者** の 3 つのロールがあります。  各ロールは、次の表に示すスコープと権限を持ちます。

<table><tr><td><b>ロール</b></td><td><b>スコープ</b></td><td><b>権限</b></td></tr><tr><td>管理者</td><td>カタログ (カタログ内のすべての資産と注釈)</td><td>Read Delete ViewRoles

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>所有者</td><td>各資産 (ルート項目)</td><td>Read Delete ViewRoles

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Contributor</td><td>個別の各資産および注釈</td><td>Read Update Delete ViewRoles Note: 項目に対する共同作成者の読み取り権限が取り消されると、すべての権限が取り消されます</td></tr></table>

> [!NOTE]
> **Read**、**Update**、**Delete**、**ViewRoles** 権限はいずれの項目 (資産または注釈) にも適用されますが、**TakeOwnership**、**ChangeOwnership**、**ChangeVisibility**、**ViewPermissions** はルート資産のみに適用されます。
> 
> **Delete** 権限は、項目およびその下にあるサブ項目または単一項目に適用されます。 たとえば、資産を削除すると、その資産のすべての注釈も削除されます。
> 

### <a name="permissions"></a>アクセス許可
アクセス許可は、アクセス制御エントリの一覧です。 各アクセス制御エントリによって、権限のセットがセキュリティ プリンシパルに割り当てられます。 アクセス許可は、資産 (つまり、ルート項目) に対してのみ指定でき、資産およびすべてのサブ項目に適用されます。

**Azure Data Catalog** プレビュー中は、資産の可視性を制限するシナリオを有効にするために、アクセス許可の一覧で **Read** 権限のみがサポートされています。

既定では、アクセス許可で可視性がプリンシパルのセットに限定されていない限り、認証されたすべてのユーザーがカタログ内のすべての項目に対して **Read** 権限を持ちます。

## <a name="rest-api"></a>REST API
**PUT** および **POST** の "項目の表示" 要求を使用して、ロールおよびアクセス許可を制御することができます。項目のペイロードに加えて、**roles** と **permissions** の 2 つのシステム プロパティを指定できます。

> [!NOTE]
> **permissions** は、ルート項目のみに適用されます。
> 
> **所有者** ロールは、ルート項目のみに適用されます。
> 
> 既定では、カタログで項目が作成されると、その **共同作成者** が、現在認証されているユーザーに設定されます。 すべてのユーザーが項目を更新できるようにする必要がある場合は、項目を最初に公開するときに、**roles** プロパティで **共同作成者** を &lt;Everyone&gt; (特別なセキュリティ プリンシパル) に設定する必要があります (次の例を参照)。 **共同作成者** は変更することができず、項目の有効期間中同じままになります (**管理者** または **所有者** であっても、**共同作成者** を変更する権限がありません)。 **共同作成者** の明示的な設定でサポートされている唯一の値は &lt;Everyone&gt; です。つまり、**共同作成者** には、項目を作成したユーザーまたは &lt;Everyone&gt; のみを指定できます。
> 

### <a name="examples"></a>例
**項目を公開するときに、共同作成者を &lt;Everyone&gt; に設定します。**
特別なセキュリティ プリンシパル &lt;Everyone&gt; の objectId は、"00000000-0000-0000-0000-000000000201" です。
  **POST** https:\//api.azuredatacatalog.com/catalogs/default/views/tables/?api-version=2016-03-30

> [!NOTE]
> HTTP クライアント実装の中には、サーバーからの 302 に対する応答として要求を自動的に再発行するものもありますが、通常は要求から Authorization ヘッダーが削除されます。 Authorization ヘッダーは Azure Data Catalog への要求に必要であるため、Azure Data Catalog で指定されたリダイレクト場所に要求を再発行する際は、Authorization ヘッダーが引き続き提供されるようにする必要があります。 以下のサンプル コードは、.NET HttpWebRequest オブジェクトを使用してこれを実現する方法を示しています。
> 

**本文**
```json
    {
        "roles": [
            {
                "role": "Contributor",
                "members": [
                    {
                        "objectId": "00000000-0000-0000-0000-000000000201"
                    }
                ]
            }
        ]
    }
```

  **所有者を割り当て、既存のルート項目の表示を制限する**:**PUT** https:\//api.azuredatacatalog.com/catalogs/default/views/tables/042297b0...1be45ecd462a?api-version=2016-03-30

```json
    {
        "roles": [
            {
                "role": "Owner",
                "members": [
                    {
                        "objectId": "c4159539-846a-45af-bdfb-58efd3772b43",
                        "upn": "user1@contoso.com"
                    },
                    {
                        "objectId": "fdabd95b-7c56-47d6-a6ba-a7c5f264533f",
                        "upn": "user2@contoso.com"
                    }
                ]
            }
        ],
        "permissions": [
            {
                "principal": {
                    "objectId": "27b9a0eb-bb71-4297-9f1f-c462dab7192a",
                    "upn": "user3@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            },
            {
                "principal": {
                    "objectId": "4c8bc8ce-225c-4fcf-b09a-047030baab31",
                    "upn": "user4@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            }
        ]
    }
```

> [!NOTE]
> PUT では、本文に項目のペイロードを指定する必要はありません。 PUT は、ロールまたはアクセス許可だけを更新するために使用できます。
> 

## <a name="next-steps"></a>次のステップ
[Azure Data Catalog REST API リファレンス](/rest/api/datacatalog/)
