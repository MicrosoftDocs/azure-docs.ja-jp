---
title: Azure Data Lake Storage (ADLS) Gen1 の登録とスキャン
description: このチュートリアルでは、Azure Data Lake Storage Gen1 からのデータをスキャンして Azure Purview に取り込む方法について説明します。
author: shsandeep123
ms.author: sandeepshah
ms.service: data-catalog
ms.subservice: data-catalog-gen2
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: a831681f892de9f6aae50fa9a2fcf71e883fe6ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97693716"
---
# <a name="register-and-scan-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1 の登録とスキャン

この記事では、Azure Purview にデータ ソースとして Azure Data Lake Storage Gen1 を登録し、そのスキャンを設定する方法について説明します。

> [!Note]
> Azure Data Lake Storage Gen2 の一般提供が開始されました。 今すぐ使用を開始することをお勧めします。 詳細については、[製品に関するページ](https://azure.microsoft.com/services/storage/data-lake-storage/)を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

Azure Data Lake Storage Gen1 データ ソースでは、以下の機能がサポートされています。

- Data Lake Storage Gen1 のメタデータと分類をキャプチャする **完全および増分スキャン**

- ADF コピーまたはデータフロー アクティビティのための、データ資産間の **データ系列**

## <a name="prerequisites"></a>前提条件

- データ ソースを登録する前に、Azure Purview アカウントを作成します。 Purview アカウントの作成の詳細については、[クイック スタート: Azure Purview アカウントの作成](create-catalog-portal.md)に関する記事を参照してください。
- Azure Purview データ ソース管理者である必要があります

## <a name="setting-up-authentication-for-a-scan"></a>スキャンの認証の設定

Azure Data Lake Storage Gen1 では、次の認証方法がサポートされています。

- マネージド ID
- サービス プリンシパル

### <a name="managed-identity-recommended"></a>マネージド ID (推奨)

使いやすさとセキュリティのために、Purview MSI を使用してデータ ストアに対する認証を行うことをお勧めします。

Data Catalog の MSI を使用してスキャンを設定するには、まず、データ ソースをスキャンするためのアクセス許可を Purview アカウントに付与する必要があります。 この手順を行って "*から*" スキャンを設定する必要があります。そうしないと、スキャンは失敗します。

#### <a name="adding-the-data-catalog-msi-to-an-azure-data-lake-storage-gen1-account"></a>Azure Data Lake Storage Gen1 アカウントへの Data Catalog MSI の追加

スキャンのアクセス許可を付与する対象に応じて、サブスクリプション、リソースグループ、またはリソースのレベルに Catalog の MSI を追加できます。

> [!Note]
> Azure リソースにマネージド ID を追加できるようにするには、サブスクリプションの所有者である必要があります。

1. [Azure portal](https://portal.azure.com) で、カタログによるスキャンを許可するサブスクリプション、リソース グループ、またはリソース (Azure Data Lake Storage Gen1 ストレージ アカウントなど) のいずれかを検索します。

2. **[概要]** をクリックして、 **[データ エクスプローラー]** を選択します。

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="アクセス制御を選択する":::

3. 上部ナビゲーションの **[追加]** をクリックします。

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="[アクセス]"::: をクリックします。

4. **[追加]** をクリックします。 [ユーザーまたはグループの選択] の選択肢の中から **Purview カタログ** を追加します。 **[読み取り]** と **[実行]** のアクセス許可を選択します。 下のスクリーンショットに示すように、[追加先] オプションで **[このフォルダーとすべての子]** を選択し、 **[OK]** をクリックします。
   :::image type="content" source="./media/register-scan-adls-gen1/managed-instance-authentication.png" alt-text="MSI 認証の詳細":::

5. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。

6. 最後に、サービス プリンシパルを使用して[新しい資格情報を作成](manage-credentials.md#create-a-new-credential)し、スキャンを設定します
> [!Note]
> データ ソースにカタログの MSI を追加したら、アクセス許可が反映されるまで最大 15 分間待機してから、スキャンを設定します。

約 15 分後には、リソースをスキャンできるようにするための適切なアクセス許可がカタログに付与されます。

### <a name="service-principal"></a>サービス プリンシパル

サービス プリンシパルを使用するには、まず、それを以下の手順に従って作成する必要があります。

1. [Azure Portal](https://portal.azure.com) に移動します。

2. 左側のメニューで、 **[Azure Active Directory]** を選択します。

3. **[アプリの登録]** を選択します。

4. **[+ 新しいアプリケーションの登録]** を選択します。

5. **アプリケーション** の名前 (サービス プリンシパル名) を入力します。

6. **[この組織のディレクトリ内のアカウントのみ]** を選択します。

7. **[リダイレクト URI]** で **[Web]** を選択し、必要な URL を入力します。これは実際の URL である必要はなく、機能しなくてもかまいません。

8. 次に、 **[登録]** を選択します。

9. 表示名とアプリケーション ID の両方から値をコピーします。

#### <a name="adding-the-data-catalog-service-principal-to-an-azure-data-lake-storage-gen1-account"></a>Azure Data Lake Storage Gen1 アカウントへの Data Catalog サービス プリンシパルの追加
1. [Azure portal](https://portal.azure.com) で、カタログによるスキャンを許可するサブスクリプション、リソース グループ、またはリソース (Azure Data Lake Storage Gen1 ストレージ アカウントなど) のいずれかを検索します。

2. **[概要]** をクリックして、 **[データ エクスプローラー]** を選択します。

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="アクセス制御を選択する":::

3. 上部ナビゲーションの **[追加]** をクリックします。

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="[アクセス]"::: をクリックします。

4. **[追加]** をクリックします。 [ユーザーまたはグループの選択] の選択肢の中から **サービス プリンシパル アプリケーション** を追加します。 **[読み取り]** と **[実行]** のアクセス許可を選択します。 下のスクリーンショットに示すように、[追加先] オプションで **[このフォルダーとすべての子]** を選択し、 **[OK]** をクリックします。
   :::image type="content" source="./media/register-scan-adls-gen1/service-principal-authentication.png" alt-text="サービス プリンシパルの認証の詳細":::

5. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。

6. 最後に、サービス プリンシパルを使用して[新しい資格情報を作成](manage-credentials.md#create-a-new-credential)し、スキャンを設定します。

## <a name="register-azure-data-lake-storage-gen1-data-source"></a>Azure Data Lake Storage Gen1 データ ソースを登録する

新しい ADLS Gen1 アカウントをデータ カタログに登録するには、次の手順を行います。

1. ご利用の Purview Data Catalog に移動します。
2. 左側のナビゲーションで **[ソース]** を選択します。
3. **[登録]** を選択します
4. **[ソースの登録]** で、 **[Azure Data Lake Storage Gen1]** を選択します。 
5. **[続行]** をクリックします。

[ソースの登録 (Azure Data Lake Storage Gen1)] 画面で、次の手順を行います。

1. データ ソースがカタログに表示されるときの **名前** を入力します。
2. サブスクリプションを選択して、ストレージ アカウントをフィルター処理します
3. Select a storage account (ストレージ アカウントを選択する)
4. コレクションを選択するか、新しいものを作成します (省略可能)
5. データ ソースの登録を終了します。

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>次のステップ

- [Azure Purview データ カタログを参照する](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)
