---
title: Azure Purview での接続のトラブルシューティング
description: この記事では、Azure Purview で接続のトラブルシューティングを行う手順について説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: c176fcafe13749ba89c04b34854f036aa5aea516
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101677649"
---
# <a name="troubleshoot-your-connections-in-azure-purview"></a>Azure Purview での接続のトラブルシューティング

この記事では、Azure Purview でデータ ソースのスキャンを設定するときの、接続エラーをトラブルシューティングする方法について説明します。

## <a name="permission-the-credential-on-the-data-source"></a>データ ソースの資格情報のアクセス許可

スキャンの認証方法としてマネージド ID またはサービス プリンシパルを使用している場合は、これらの ID にデータ ソースへのアクセスを許可する必要があります。

ソースの種類ごとに固有の手順があります。

- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Cosmos DB](register-scan-azure-cosmos-database.md#setting-up-authentication-for-a-scan)
- [Azure Data Explorer](register-scan-azure-data-explorer.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Azure SQL Database マネージド インスタンス](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
- [SQL Server](register-scan-on-premises-sql-server.md#setting-up-authentication-for-a-scan)
- [Power BI](register-scan-power-bi-tenant.md)
- [Amazon S3](register-scan-amazon-s3.md#create-a-purview-credential-for-your-aws-bucket-scan)
## <a name="storing-your-credential-in-your-key-vault-and-using-the-right-secret-name-and-version"></a>キー コンテナーへの資格情報の格納と、適切なシークレット名およびバージョンの使用

資格情報を Azure Key Vault インスタンスに格納し、適切なシークレット名およびバージョンを使用する必要があります。

次の手順に従って、これを確認します。

1. お使いのキー コンテナーに移動します。
1. **[Settings]\(設定\)** > **[Secrets]\(シークレット\)** を選択します。
1. スキャンするデータ ソースに対する認証に使用するシークレットを選択します。
1. 使用するバージョンを選択し、 **[シークレット値を表示する]** をクリックして、パスワードまたはアカウント キーが正しいことを確認します。 

## <a name="verify-permissions-for-the-purview-managed-identity-on-your-azure-key-vault"></a>Azure Key Vault での、Purview が管理する ID のアクセス許可の確認

Azure Key Vault にアクセスするために、Purview が管理する ID に適切なアクセス許可が構成されていることを確認します。

これを確認するには、次の手順を実行します。

1. 使用するキー コンテナーに移動し、 **[アクセス ポリシー]** セクションに移動します。

1. Purview が管理する ID が *[現在のアクセス ポリシー]* セクションに表示され、少なくともシークレットに対する **Get** および **List** アクセス許可が付与されていることを確認します。

   :::image type="content" source="./media/troubleshoot-connections/verify-minimum-permissions.png" alt-text="Get と List の両方のアクセス許可についてドロップダウンの選択を示す画像":::

Purview が管理する ID が一覧に表示されない場合は、「[スキャン用の資格情報を作成して管理する](manage-credentials.md)」に記載されている手順に従って追加します。 

## <a name="next-steps"></a>次のステップ

- [Azure Purview データ カタログを参照する](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)
