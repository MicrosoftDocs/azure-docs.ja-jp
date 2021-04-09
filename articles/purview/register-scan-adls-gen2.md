---
title: Azure Data Lake Storage (ADLS) Gen2 の登録とスキャン
description: このチュートリアルでは、Azure Data Lake Storage Gen2 をスキャンする方法について説明します。
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 4b7f71b5405708cc1988fafa5ca9c4628fe0d80b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98882401"
---
# <a name="register-and-scan-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 の登録とスキャン

この記事では、Azure Purview にデータ ソースとして Azure Data Lake Storage Gen2 を登録し、スキャンを設定する方法について説明します。

## <a name="supported-capabilities"></a>サポートされる機能

Azure Data Lake Storage Gen2 データ ソースでは、以下の機能がサポートされています。

- Data Lake Storage Gen2 のメタデータと分類をキャプチャする **完全および増分スキャン**

- ADF コピーまたはデータフロー アクティビティのための、データ資産間の **データ系列**

## <a name="prerequisites"></a>前提条件

データ ソースを登録する前に、Azure Purview アカウントを作成します。 Purview アカウントの作成の詳細については、[クイック スタート: Azure Purview アカウントの作成](create-catalog-portal.md)に関するページを参照してください。

### <a name="setting-up-authentication-for-a-scan"></a>スキャンの認証の設定

Azure Data Lake Storage Gen2 では、次の認証方法がサポートされています。

- マネージド ID
- サービス プリンシパル
- アカウント キー

#### <a name="managed-identity-recommended"></a>マネージド ID (推奨)

**マネージド ID** を選択する場合、接続を設定するには、まず、データ ソースをスキャンするためのアクセス許可を Purview アカウントに付与する必要があります。

1. ADLS Gen2 ストレージ アカウントに移動します。
1. 左側のナビゲーション メニューから **[アクセス制御 (IAM)]** を選択します。 
1. **[+ 追加]** を選択します。
1. **[ロール]** に **[ストレージ BLOB データ リーダー]** を設定し、 **[選択]** 入力ボックスに Azure Purview アカウント名を入力します。 次に、 **[保存]** を選択して、このロールの割り当てを Purview アカウントに付与します。

> [!Note]
> 詳細については、「[Azure Active Directory を使用して BLOB とキューへのアクセスを承認する](../storage/common/storage-auth-aad.md)」の手順を参照してください

#### <a name="account-key"></a>アカウント キー

選択した認証方法が **アカウント キー** の場合は、アクセス キーを取得して、キー コンテナーに格納する必要があります。

1. ADLS Gen2 ストレージ アカウントに移動します
1. **[設定] > [アクセス キー]** を選択します
1. "*キー*" をコピーし、次の手順のためにどこかに保存します
1. お使いのキー コンテナーに移動する
1. **[設定] > [シークレット]** の順に選択します。
1. **[+ 生成/インポート]** を選択し、 **[名前]** と *[値]* にストレージ アカウントの **キー** を入力します
1. **[作成]** を選択して完了します。
1. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。
1. 最後に、キーを使用して[新しい資格情報を作成](manage-credentials.md#create-a-new-credential)し、スキャンを設定します

#### <a name="service-principal"></a>サービス プリンシパル

サービス プリンシパルを使用するには、既存のものを使用するか、新しく作成することができます。 

> [!Note]
> 新しいサービス プリンシパルを作成する必要がある場合は、次の手順に従います。
> 1. [Azure Portal](https://portal.azure.com) に移動します。
> 1. 左側のメニューで、 **[Azure Active Directory]** を選択します。
> 1. **[アプリの登録]** を選択します。
> 1. **[+ 新しいアプリケーションの登録]** を選択します。
> 1. **アプリケーション** の名前 (サービス プリンシパル名) を入力します。
> 1. **[この組織のディレクトリ内のアカウントのみ]** を選択します。
> 1. [リダイレクト URI] には **[Web]** を選択し、必要な URL を入力します。これは、実際の URL でなくてもよく、機能しなくてもかまいません。
> 1. 次に、 **[登録]** を選択します。

サービス プリンシパルのアプリケーション ID とシークレットを取得する必要があります。

1. [Azure portal](https://portal.azure.com) でサービス プリンシパルに移動します
1. **[概要]** から **[アプリケーション (クライアント) ID]** 、 **[証明書とシークレット]** から **[クライアント シークレット]** の値をコピーします。
1. お使いのキー コンテナーに移動する
1. **[設定] > [シークレット]** の順に選択します。
1. **[生成/インポート]** を選択し、サービス プリンシパルの **クライアント シークレット** として任意の **名前** と **値** を入力します
1. **[作成]** を選択して完了します。
1. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。
1. 最後に、サービス プリンシパルを使用して[新しい資格情報を作成](manage-credentials.md#create-a-new-credential)し、スキャンを設定します

##### <a name="granting-the-service-principal-access-to-your-adls-gen2-account"></a>ADLS Gen2 アカウントへのアクセス権をサービス プリンシパルに付与する

1. ストレージ アカウントに移動します。
1. 左側のナビゲーション メニューから **[アクセス制御 (IAM)]** を選択します。 
1. **[+ 追加]** を選択します。
1. **[ロール]** に **[ストレージ BLOB データ リーダー]** を設定し、 **[選択]** 入力ボックスにサービス プリンシパル名またはオブジェクト ID を入力します。 次に、 **[保存]** を選択して、このロールの割り当てをサービス プリンシパルに付与します。
### <a name="firewall-settings"></a>ファイアウォールの設定

> [!NOTE]
> ストレージ アカウントに対してファイアウォールが有効になっている場合は、スキャンを設定するときに、 **[マネージド ID]** の認証方法を使用する必要があります。

1. [Azure portal](https://portal.azure.com) で ASLS Gen2 ストレージ アカウントに移動します
1. **[設定] > [ネットワーク]** に移動します
1. **[許可するアクセス元]** の **[選択されたネットワーク]** を選択します
1. **[例外]** セクションで、 **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** を選択し、 **[保存]** を選択します

:::image type="content" source="./media/register-scan-adls-gen2/firewall-setting.png" alt-text="ファイアウォールの設定を示すスクリーンショット":::

## <a name="register-azure-data-lake-storage-gen2-data-source"></a>Azure Data Lake Storage Gen2 を登録する

新しい ADLS Gen2 アカウントをデータ カタログに登録するには、次の手順を行います。

1. Purview アカウントに移動します
2. 左側のナビゲーションで **[ソース]** を選択します。
3. **[登録]** を選択します
4. **[ソースの登録]** で、 **[Azure Data Lake Storage Gen2]** を選択します
5. **[続行]** を選択します

**[ソースの登録 (Azure Data Lake Storage Gen2)]** 画面で、次の手順を行います。

1. データ ソースがカタログに表示されるときの **名前** を入力します。
2. サブスクリプションを選択して、ストレージ アカウントをフィルター処理します
3. Select a storage account (ストレージ アカウントを選択する)
4. コレクションを選択するか、新しいものを作成します (省略可能)
5. **[完了]** を選択して、データ ソースを登録します。

:::image type="content" source="media/register-scan-adls-gen2/register-sources.png" alt-text="ソースの登録のオプション" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>次のステップ

- [Azure Purview データ カタログを参照する](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)