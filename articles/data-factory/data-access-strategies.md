---
title: データ アクセス戦略
description: Azure Data Factory で、静的 IP アドレス範囲がサポートされるようになりました。
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: edc773ec2db078b6c50b55c81ad6570758a3f5f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100389248"
---
# <a name="data-access-strategies"></a>データ アクセス戦略

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

組織の重要なセキュリティ目標は、そのデータ ストア (それがオンプレミスやクラウドまたは SaaS データ ストアであっても) をインターネット経由のランダム アクセスから保護することです。 

通常、クラウド データ ストアでは、次のメカニズムを使用してアクセスを制御します。
* 仮想ネットワークからプライベート エンドポイントへの Private Link が有効なデータ ソース
* IP アドレスによって接続を制限するファイアウォール規則
* ユーザーに ID の証明を求める認証メカニズム
* ユーザーによる特定のアクションとデータを制限する認証メカニズム

> [!TIP]
> [静的 IP アドレス範囲の導入](./azure-integration-runtime-ip-addresses.md)により、特定の Azure 統合ランタイム リージョンの IP 範囲のリストを許可できるようになったため、クラウド データ ストア内のすべての Azure IP アドレスを許可する必要がなくなりました。 これにより、データ ストアへのアクセスが許可されている IP アドレスを制限できます。

> [!NOTE] 
> IP アドレスの範囲は Azure Integration Runtime ではブロックされ、現在はデータ移動、パイプライン、および外部アクティビティにのみ使用されます。 マネージド仮想ネットワークが有効になっているデータフローと Azure Integration Runtime では、これらの IP 範囲は使用されなくなりました。 

これは多くのシナリオで機能するはずです。統合ランタイムごとに一意の静的 IP アドレスが望ましいことは理解していますが、現時点では、サーバーレスである Azure Integration Runtime を使用してこれを行うことはできません。 必要に応じて、いつでもセルフホステッド統合ランタイムを設定して、それで静的 IP を使用することができます。 

## <a name="data-access-strategies-through-azure-data-factory"></a>Azure Data Factory によるデータ アクセス戦略

* **[Private Link](../private-link/private-link-overview.md)** - Azure Data Factory マネージド仮想ネットワーク内に Azure Integration Runtime を作成できます。それはプライベート エンドポイントを使用して、サポートされているデータ ストアに安全に接続します。 マネージド仮想ネットワークとデータ ソース間のトラフィックは Microsoft のバックボーン ネットワークを経由して送信され、パブリック ネットワークには公開されません。
* **[信頼できるサービス](../storage/common/storage-network-security.md#exceptions)** - Azure Storage (Blob、ADLS Gen2) では、限定された信頼できる Azure プラットフォーム サービスがストレージ アカウントに安全にアクセスできるようにするファイアウォール構成がサポートされています。 信頼できるサービスでは、マネージド ID の認証が適用されます。このため、マネージド ID を使用して承認されていない限り、他のデータ ファクトリはこのストレージに接続できません。 詳細については、 **[このブログ](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993)** を参照してください。 そのため、これは非常に安全であり、推奨されます。 
* **一意の静的 IP** - Data Factory コネクタの静的 IP を取得するには、セルフホステッド統合ランタイムを設定する必要があります。 このメカニズムにより、他のすべての IP アドレスからのアクセスを確実にブロックできるようになります。 
* **[静的 IP 範囲](./azure-integration-runtime-ip-addresses.md)** - Azure Integration Runtime の IP アドレスを使用して、ストレージ内にそれをリストすることを許可します (たとえば、S3、Salesforce など)。 これにより、データ ストアに接続できる IP アドレスが確実に制限されますが、認証規則や承認規則にも依存します。
* **[サービス タグ](../virtual-network/service-tags-overview.md)** - 指定された Azure サービス (Azure Data Factory など) からの IP アドレス プレフィックスのグループを表します。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。これにより、ネットワーク セキュリティ規則に対する頻繁な更新の複雑さを最小限に抑えられます。 これは、Virtual Network の IaaS でホストされるデータ ストアのデータ アクセスをフィルター処理する場合に便利です。
* **Azure サービスを許可する** - 一部のサービスでは、このオプションを選択すると、すべての Azure サービスに接続することを許可できます。 

Azure Integration Runtime とセルフホステッド統合ランタイムのデータ ストアでサポートされているネットワーク セキュリティ メカニズムの詳細については、次の 2 つの表を参照してください。  
* **Azure Integration Runtime**

    | データ ストア                  | データ ストアでサポートされているネットワーク セキュリティ メカニズム | Private Link     | 信頼できるサービス     | 静的 IP 範囲 | サービス タグ | Azure サービスの許可 |
    |------------------------------|-------------------------------------------------------------|---------------------|-----------------|--------------|----------------------|-----------------|
    | Azure PaaS データ ストア       | Azure Cosmos DB                                     | はい              | -                   | はい             | -            | はい                  |
    |                              | Azure Data Explorer                                 | -                | -                   | はい*            | はい*         | -                    |
    |                              | Azure Data Lake Gen1                                | -                | -                   | はい             | -            | はい                  |
    |                              | Azure Database for MariaDB、Azure Database for MySQL、Azure Database for PostgreSQL       | -                | -                   | はい             | -            | はい                  |
    |                              | Azure File Storage                                  | はい              | -                   | はい             | -            | .                    |
    |                              | Azure Storage (BLOB、ADLS Gen2)                     | はい              | はい (MSI 認証のみ) | はい             | -            | .                    |
    |                              | Azure SQL DB、Azure Synapse Analytics)、SQL   Ml  | はい (Azure SQL DB/DW のみ)        | -                   | ○             | -            | はい                  |
    |                              | Azure Key Vault (シークレットまたは接続文字列をフェッチするため) | はい      | ○                 | はい             | -            | -                    |
    | その他の PaaS/SaaS データ ストア | AWS S3、SalesForce、Google Cloud Storage など    | -                | -                   | はい             | -            | -                    |
    | Azure laaS                   | SQL Server、Oracle など                          | -                | -                   | はい             | はい          | -                    |
    | オンプレミスの laaS              | SQL Server、Oracle など                          | -                | -                   | はい             | -            | -                    |
    
    **Azure Data Explorer が仮想ネットワークに挿入され、IP 範囲を NSG またはファイアウォールに適用できる場合にのみ適用されます。* 

* **セルフホステッド統合ランタイム (Vnet またはオンプレミスで)**
    
    | データ ストア                  | データ ストアでサポートされているネットワーク セキュリティ メカニズム         | 静的 IP | 信頼済みサービス  |
    |--------------------------------|---------------------------------------------------------------|-----------|---------------------|
    | Azure PaaS データ ストア       | Azure Cosmos DB                                               | はい       | -                   |
    |                                | Azure Data Explorer                                           | -         | -                   |
    |                                | Azure Data Lake Gen1                                          | はい       | -                   |
    |                                | Azure Database for MariaDB、Azure Database for MySQL、Azure Database for PostgreSQL               | はい       | -                   |
    |                                | Azure File Storage                                            | はい       | -                   |
    |                                | Azure Storage (Blog、ADLS Gen2)                             | はい       | はい (MSI 認証のみ) |
    |                                | Azure SQL DB、Azure Synapse Analytics)、SQL   Ml          | はい       | -                   |
    |                                | Azure Key Vault (シークレットまたは接続文字列をフェッチするため) | はい       | はい                 |
    | その他の PaaS/SaaS データ ストア | AWS S3、SalesForce、Google Cloud Storage など              | はい       | -                   |
    | Azure laaS                     | SQL Server、Oracle など                                  | はい       | -                   |
    | オンプレミスの laaS              | SQL Server、Oracle など                                  | はい       | -                   |    

## <a name="next-steps"></a>次のステップ

詳細については、次の関連記事を参照してください。
* [サポートされているデータ ストア](./copy-activity-overview.md#supported-data-stores-and-formats)
* [Azure Key Vault の "信頼できるサービス"](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services)
* [Azure Storage の "信頼できる Microsoft サービス"](../storage/common/storage-network-security.md#trusted-microsoft-services)
* [Data Factory のマネージド ID](./data-factory-service-identity.md)