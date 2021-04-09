---
title: スキャン用の資格情報を作成して管理する
description: Azure Purview で資格情報を作成して管理する手順について説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: 3802d25ebd8f21ab5b8991a66ceb6650f2f276a9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103461710"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>Azure Purview でのソース認証用の資格情報

この記事では、Azure Purview で資格情報を作成する方法について説明します。 保存した資格情報を使用すると、保存された認証情報を簡単に再利用し、データ ソース スキャンに適用できます。

## <a name="prerequisites"></a>前提条件

- Azure Key Vault。 作成方法については、「[クイック スタート: Azure portal を使用してキー コンテナーを作成する](../key-vault/general/quick-create-portal.md)」を参照してください。

## <a name="introduction"></a>はじめに

資格情報は、Azure Purview で、登録済みのデータ ソースに対する認証に使用できる認証情報です。 資格情報オブジェクトは、さまざまな種類の認証シナリオ (ユーザー名とパスワードを必要とする基本認証など) のために作成できます。 資格情報では、選択した認証方法の種類に基づいて、認証に必要な特定の情報がキャプチャされます。 資格情報の作成プロセス中は、機密性の高い認証情報を取得するために Azure Key Vault の既存のシークレットが使用されます。

## <a name="use-purview-managed-identity-to-set-up-scans"></a>Purview マネージド ID を使用したスキャンの設定

Purview マネージド ID を使用してスキャンを設定する場合は、資格情報を明示的に作成し、キー コンテナーを Purview にリンクしてそれらを格納する必要はありません。 Purview マネージド ID を追加してデータ ソースのスキャンを利用できるようにする詳細な手順については、以下のデータ ソース固有の認証に関するセクションをご覧ください。

- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Azure SQL Database マネージド インスタンス](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>Azure Purview アカウントに Azure Key Vault の接続を作成する

資格情報を作成する前に、まず既存の Azure Key Vault インスタンスの 1 つまたは複数を Azure Purview アカウントに関連付けます。

1. [Azure portal](https://portal.azure.com) で、Azure Purview アカウントを選択して Azure Purview Studio を開きます。 Azure Purview Studio の **[管理センター]** に移動してから、**資格情報** に移動します。

2. **[資格情報]** ページで、 **[Key Vault 接続の管理]** を選択します。

   :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="Azure Key Vault 接続を管理する":::

3. [Key Vault 接続の管理] ページで **[+ 新規]** を選択します。

4. 必要な情報を入力して、 **[作成]** を選択します。

5. この例に示すように、Key Vault が Azure Purview アカウントに正常に関連付けられていることを確認します。

   :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="確認のために Azure Key Vault 接続を表示します。":::

## <a name="grant-the-purview-managed-identity-access-to-your-azure-key-vault"></a>Purview マネージド ID に Azure Key Vault へのアクセス権を付与する

1. お使いの Azure Key Vault に移動します。

2. **[アクセス ポリシー]** ページを選択します。

3. **[アクセス ポリシーの追加]** を選択します。

   :::image type="content" source="media/manage-credentials/add-msi-to-akv.png" alt-text="AKV への Purview MSI の追加":::

4. **[シークレットのアクセス許可]** ドロップダウンで、 **[取得]** および **[一覧]** のアクセス許可を選択します。

5. **[プリンシパルの選択]** で、Purview マネージド ID を選択します。 Purview インスタンス名 **または** マネージ ID アプリケーション ID を使用して、Purview MSI を検索できます。 現在、複合 ID (マネージド ID 名 + アプリケーション ID) はサポートされていません。

   :::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="アクセス ポリシーの追加":::

6. **[追加]** を選択します。

7. **[保存]** を選択してアクセス ポリシーを保存します。

   :::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="アクセス ポリシーの保存":::

## <a name="create-a-new-credential"></a>新しい資格情報を作成する

これらの資格情報の種類が Purview でサポートされています。

- 基本認証:**パスワード** をシークレットとしてキー コンテナーに追加します。
- サービス プリンシパル:**サービス プリンシパルのキー** をシークレットとしてキー コンテナーに追加します。
- SQL 認証:**パスワード** をシークレットとしてキー コンテナーに追加します。
- アカウント キー:**アカウント キー** をシークレットとしてキー コンテナーに追加します。
- ロール ARN: Amazon S3 データ ソースの場合は、AWS に **ロール ARN** を追加します。 

詳細については、「[Key Vault にシークレットを追加する](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault)」および「[Purview 用の新しい AWS ロールを作成する](register-scan-amazon-s3.md#create-a-new-aws-role-for-purview)」を参照してください。

シークレットをキー コンテナーに格納した後、次のようにします。

1. Azure Purview で、[資格情報] ページに移動します。

2. **[+ 新規]** を選択して、新しい資格情報を作成します。

3. 必要な情報を入力します。 **[認証方法]** と、シークレットの選択元となる **[Key Vault インスタンス]** を選択します。

4. すべての詳細の入力が完了したら、 **[作成]** を選択します。

   :::image type="content" source="media/manage-credentials/new-credential.png" alt-text="新しい資格情報":::

5. 新しい資格情報がリスト ビューに表示され、使用する準備ができていることを確認します。

   :::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="資格情報の表示":::

## <a name="manage-your-key-vault-connections"></a>キー コンテナーの接続を管理する

1. Key Vault 接続を名前で検索します

   :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="Key Vault の検索":::

2. 1 つ以上の Key Vault 接続を削除します

   :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="キー コンテナーを削除する":::

## <a name="manage-your-credentials"></a>資格情報を管理する

1. 資格情報を名前で検索します。
  
2. 既存の資格情報を選択して更新します。

3. 1 つ以上の資格情報を削除します。

## <a name="next-steps"></a>次のステップ

[スキャン ルール セットを作成する](create-a-scan-rule-set.md)
