---
title: Azure の複数のソースをスキャンする方法
description: Azure Purview Data Catalog で Azure サブスクリプションまたはリソース グループ全体をスキャンする方法について説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 2/26/2021
ms.openlocfilehash: 098f62365971fd634001706ab99fd414a6b25056
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102123566"
---
# <a name="register-and-scan-azure-multiple-sources"></a>Azure の複数のソースを登録してスキャンする

この記事では、Azure の複数のソース (Azure サブスクリプションまたはリソース グループ) を Purview に登録し、それに対してスキャンを設定する方法について説明します。

## <a name="supported-capabilities"></a>サポートされる機能

Azure の複数のソースでは、Purview でサポートされるほとんどの種類の Azure リソースでメタデータおよびスキーマをキャプチャするためのスキャンがサポートされます。 また、システムおよびカスタムの分類規則に基づいてデータが自動的に分類されます。

## <a name="prerequisites"></a>前提条件

- データ ソースを登録する前に、Azure Purview アカウントを作成します。 Purview アカウントの作成の詳細については、[クイック スタート: Azure Purview アカウントの作成](create-catalog-portal.md)に関する記事を参照してください。
- Azure Purview データ ソース管理者である必要があります
- 以下のセクションの説明に従った認証の設定

### <a name="setting-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group"></a>サブスクリプションまたはリソース グループの下にあるリソースを列挙するための認証を設定する

1. Azure portal でサブスクリプションまたはリソース グループに移動します。  
1. 左側のナビゲーション メニューで  **[アクセス制御 (IAM)]**   を選択します。 
1. サブスクリプションまたはリソース グループにロールを追加するには、所有者またはユーザー アクセス管理者である必要があります。 *[+ 追加]* ボタンを選択します。 
1. **[閲覧者]** ロールを設定し、[入力の選択] ボックスに Azure Purview のアカウント名 (MSI を表すもの) を入力します。 *[保存]* をクリックして、ロールの割り当てを完了します。

### <a name="setting-up-authentication-to-scan-resources-under-a-subscription-or-resource-group"></a>サブスクリプションまたはリソース グループの下にあるリソースをスキャンするための認証を設定する

Azure の複数のソースの認証を設定するには、次の 2 つの方法があります。

- マネージド ID
- サービス プリンシパル

> [!NOTE]
> 登録してスキャンするサブスクリプションまたはリソース グループ内にある各リソースに対して認証を設定する必要があります。 Azure ストレージ リソースの種類 (Azure BLOB ストレージおよび Azure Data Lake Storage Gen2) については、ストレージ BLOB データ閲覧者として、サブスクリプションまたはリソース グループのレベルで MSI またはサービス プリンシパルを追加できるため簡単に行うことができます。その後、アクセス許可が、そのサブスクリプションまたはリソース グループ内の各ストレージ アカウントに継承されます。 他のすべてのリソースの種類については、各リソースに MSI またはサービス プリンシパルを適用するか、またはデバイスにスクリプトを適用する必要があります。 サブスクリプションまたはリソース グループ内のリソースの種類ごとにアクセス許可を追加する方法を次に示します。
    
- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Azure SQL Database マネージド インスタンス](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
 
## <a name="register-an-azure-multiple-source"></a>Azure の複数のソースを登録する

新しい Azure の複数のソースをデータ カタログに登録するには、次の手順を実行します。

1. Purview アカウントに移動します。
1. 左側のナビゲーションで **[ソース]** を選択します。
1. **[登録]** を選択します
1. **[ソースの登録]** で、 **[Azure (multiple)]\(Azure (複数)\)** を選択します。
1. **[続行]** を選択します。

   :::image type="content" source="media/register-scan-azure-multiple-sources/register-azure-multiple.png" alt-text="Azure の複数のソースを登録する":::

**[Register sources (Azure multiple)]\(ソースの登録 (Azure 複数)\)** 画面で、次の手順を実行します。

1. データ ソースがカタログに表示されるときの **[名前]** を入力します。 
1. 必要に応じて、フィルターを適用する **管理グループ** を選択します。
1. ドロップダウンで、**サブスクリプションを選択するか、指定したサブスクリプションの下の特定のリソースを選択** します。 登録スコープは、選択したサブスクリプションまたはリソース グループに設定されます。  
1. **コレクション** を選択するか、新しいものを作成します (省略可能)
1. データ ソースの登録を **[完了]** します。

   :::image type="content" source="media/register-scan-azure-multiple-sources/azure-multiple-source-setup.png" alt-text="Azure の複数のソースを設定する":::

## <a name="creating-and-running-a-scan"></a>スキャンを作成し、実行する

新しいスキャンを作成して実行するには、次の操作を行います。

1. **[ソース]** セクションに移動します。

1. 登録したデータ ソースを選択します。

1. [詳細の表示] をクリックし、 **[+ 新しいスキャン]** を選択するか、ソース タイルのスキャン クイック操作アイコンを使用します。

1. "*名前*" を入力し、このソース内でスキャンするすべての種類のリソースを選択します。

    1. *[すべて]* のままにしておくことができます (これには、そのサブスクリプションまたはリソース グループ内に現在存在しない可能性のある将来のリソースの種類が含まれます)。
    1. スキャンする **リソースの種類を具体的に選択** できます。 このオプションを選択した場合、将来スキャンが明示的に編集されない限り、このサブスクリプションまたはリソース グループ内に将来作成される可能性のあるリソースの種類はスキャンの対象に含まれません。
    
    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-scan.png" alt-text="Azure の複数のソースのスキャン":::

1. データ ソース内のリソースに接続する資格情報を選択します。 
    1. MSI として **親レベルの資格情報** を選択するか、特定のサービス プリンシパルの種類の資格情報を選択できます。これは、サブスクリプションまたはリソース グループの下にあるすべてのリソースの種類に対して使用することを選択できます。
    1. また、具体的に **リソースの種類を選択し、そのリソースの種類の別の資格情報を適用** することもできます。
    1. 各資格情報は、特定の種類の下にあるすべてのリソースの認証方法と見なされます。
    1. 上記のこの[セクション](#setting-up-authentication-to-scan-resources-under-a-subscription-or-resource-group)で説明したように、リソースを正常にスキャンするために、選択した認証をリソースに設定する必要があります。
1. 各種類では、すべてのリソースをスキャンするか、またはそのサブセットを名前でスキャンするかを選択できます。
    1. オプションを **[すべて]** のままにすると、その種類の将来のリソースも、将来のスキャンの実行でスキャンされます。
    1. 特定のストレージ アカウントまたは SQL データベースを選択した場合、将来スキャンが明示的に編集されない限り、このサブスクリプションまたはリソース グループ内に将来作成される、その種類のリソースはスキャンの対象に含まれません。
 
1.  **[続行]** をクリックして続行します。 サブスクリプションまたはリソース グループの閲覧者として Purview MSI を適用したかどうかを確認するために、Microsoft によってアクセスがテストされます。 エラー メッセージがスローされた場合は、[こちら](#setting-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group)の手順に従ってください。

1.  前の手順で選択したリソースの種類ごとに **スキャン ルール セット** を選択します。 また、スキャン ルール セットをインラインで作成することもできます。
  :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-rule-set.png" alt-text="Azure の複数スキャンのルール セットの選択":::

1. スキャン トリガーを選択します。 **[weekly/monthly]\(毎週/毎月\)** または **[1 回]** 、実行するようにスケジュールできます。

1. スキャンをレビューし、[保存] を選択して設定を完了します。   

## <a name="viewing-your-scans-and-scan-runs"></a>スキャンとスキャンの実行を確認する

1. [ソース] セクションの下にあるタイルで **[詳細の表示]** をクリックして、ソースの詳細を表示します。 

      :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-detail.png" alt-text="Azure の複数のソースの詳細"::: 

1. **[スキャンの詳細]** に移動して、スキャン実行の詳細を表示します。
    1. "*ステータス バー*" は、子リソースの実行中の状態に関する概要です。 これは、サブスクリプションまたはリソース グループ レベルで表示されます。
    1. 緑色は成功を意味し、赤色は失敗を意味します。 グレーは、スキャンがまだ進行中であることを意味します。
    1. 各スキャンをクリックすると、より詳細な情報を表示できます。

      :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-full-details.png" alt-text="Azure 複数スキャンの詳細":::

1. [ソースの詳細] ページの下部に、最近失敗したスキャン実行の概要が表示されます。 また、クリックして、これらの実行に関するより詳細な情報を表示することもできます。

## <a name="manage-your-scans---edit-delete-or-cancel"></a>スキャンを管理する - 編集、削除、またはキャンセル
スキャンを管理または削除するには、次の操作を行います。

- 管理センターに移動します。 [Sources and scanning]\(ソースとスキャン\) セクションで [データ ソース] を選択し、目的のデータ ソースを選択します。

- 管理するスキャンを選択します。 スキャンを編集するには、[編集] を選択します。

- スキャンを削除するには、[削除] を選択します。
- 実行中のスキャンもキャンセルできます。

## <a name="next-steps"></a>次のステップ

- [Azure Purview データ カタログを参照する](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)    
