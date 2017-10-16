---
title: "Azure Data Factory のセキュリティに関する考慮事項 | Microsoft Docs"
description: "Azure Data Factory のデータ移動サービスがデータを保護するために使用する基本的なセキュリティ インフラストラクチャについて説明します。"
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2017
ms.author: abnarain
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: 00673cac31eb25bb8dd8228d6a36617c3812f5db
ms.contentlocale: ja-jp
ms.lasthandoff: 09/26/2017

---

# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Azure Data Factory - データ移動のセキュリティに関する考慮事項
この記事では、Azure Data Factory のデータ移動サービスがデータを保護するために使用する基本的なセキュリティ インフラストラクチャについて説明します。 Azure Data Factory 管理リソースは、Azure のセキュリティ インフラストラクチャ上に構築されており、Azure が提供する利用可能なすべてのセキュリティ対策を使用します。

Data Factory ソリューションでは、1 つ以上のデータ [パイプライン](concepts-pipelines-activities.md)を作成します。 パイプラインは、1 つのタスクを連携して実行するアクティビティの論理的なグループです。 これらのパイプラインは、データ ファクトリが作成されたリージョンに存在します。 

Data Factory を利用できるリージョンが**米国東部**と**米国東部 2** (バージョン 2 プレビュー) のみであっても、データ移動サービスは[いくつかのリージョンでグローバル](concepts-integration-runtime.md#azure-ir)に利用できます。 Data Factory サービスでは、データ移動サービスがまだデプロイされていないリージョンを代替リージョンとして使用するようにサービスに明示的に指示しない限り、データが地域/リージョンを離れないことが保証されます。 

Azure Data Factory 自体は、クラウド データ ストアのリンクされたサービス資格情報以外のデータを格納しません。その資格情報は証明書を使用して暗号化されます。 Azure Data Factory を使用すると、データ主導型のワークフローを作成し、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)間でのデータ移動と、他のリージョンまたはオンプレミス環境にある[コンピューティング サービス](compute-linked-services.md)を使用したデータ処理を調整できます。 また、SDK と Azure Monitor を使用して、ワークフローを監視および管理することもできます。

Azure Data Factory を使用したデータ移動は、次の**認定を取得**しています。
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA) 
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018)
-   [CSA STAR](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)

Azure コンプライアンスと、Azure が独自のインフラストラクチャをセキュリティで保護するしくみについて興味がある場合は、 [Microsoft Trust Center (セキュリティ センター)](https://www.microsoft.com/TrustCenter/default.aspx)にアクセスしてください。 

この記事では、次の 2 つのデータ移動シナリオでセキュリティに関する考慮事項を確認します。 

- **クラウド シナリオ** - このシナリオでは、ソースと移動先の両方にインターネットを通じてパブリックにアクセスできます。 これには、Azure Storage、Azure SQL Data Warehouse、Azure SQL Database、Azure Data Lake Store、Amazon S3、Amazon Redshift などの管理クラウド ストレージ サービス、Salesforce などの SaaS サービス、FTP や OData などの Web プロトコルが該当します。 サポートされているデータ ソースの完全な一覧は、[ここで](copy-activity-overview.md#supported-data-stores-and-formats)確認できます。
- **ハイブリッド シナリオ** - このシナリオでは、ソースと移動先のどちらかがファイアウォールの内側またはオンプレミスの企業ネットワーク内にあるか、データ ストアがプライベート ネットワーク/仮想ネットワーク内 (ほとんどの場合はソース) にあり、パブリックにアクセスできません。 仮想マシンでホストされているデータベース サーバーもこのシナリオに該当します。

## <a name="cloud-scenarios"></a>クラウド シナリオ
###<a name="securing-data-store-credentials"></a>データ ストアの資格情報の保護
- Azure Data Factory で管理されたストアに暗号化された資格情報を格納します。

   Azure Data Factory では、データ ストアの資格情報を保護するために、**Microsoft が管理する証明書**を使用して**暗号化**します。 証明書は、**2 年**ごとに交換されます (証明書の更新と資格情報の移行が行われます)。 暗号化された資格情報は、**Azure Data Factory 管理サービスによって管理される Azure Storage** に安全に格納されます。 Azure Storage のセキュリティの詳細については、「[Azure Storage のセキュリティの概要](../security/security-storage-overview.md)」を参照してください。
- Azure Key Vault への資格情報の格納 

   [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) にデータ ストアの資格情報を格納するよう選択して、Azure Data Factory にアクティビティの実行中にそれを取得させることができます。 詳細については、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」を参照してください。

   > [!NOTE]
   > 現時点では、[Dynamics コネクタ](connector-dynamics-crm-office-365.md)でのみこの機能がサポートされます。 

### <a name="data-encryption-in-transit"></a>転送中のデータの暗号化
クラウド データ ストアが HTTPS または TLS をサポートしている場合、Data Factory のデータ移動サービスとクラウド データ ストア間のデータ転送はすべて、セキュリティで保護されたチャネル HTTPS または TLS を介して行われます。

> [!NOTE]
> データがデータベースとの間で転送中である間は、常に **Azure SQL Database** および **Azure SQL Data Warehouse** への接続をすべて (SSL/TLS を使用して) 暗号化する必要があります。 JSON を使用してパイプラインを作成する場合は、**encryption** プロパティを追加し、**接続文字列**で **true** に設定します。 **Azure Storage** では、接続文字列で **HTTPS** を使用できます。

### <a name="data-encryption-at-rest"></a>保存データの暗号化
一部のデータ ストアは、保存データの暗号化をサポートしています。 そうしたデータ ストアに対してはデータ暗号化メカニズムを有効にすることをお勧めします。 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Azure SQL Data Warehouse の Transparent Data Encryption (TDE) を使用すると、保存データの暗号化と暗号化解除をリアルタイムで実行することによって、悪意のあるアクティビティの脅威から保護できます。 この動作はクライアントに対して透過的です。 詳細については、「[SQL Data Warehouse でのデータベース保護](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md)」をご覧ください。

#### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database では、Transparent Data Encryption (TDE) もサポートしています。TDE を使用すると、データの暗号化と暗号化解除をリアルタイムで実行することによって、悪意のあるアクティビティの脅威から保護できます。アプリケーションを変更する必要はありません。 この動作はクライアントに対して透過的です。 詳細については、「[Azure SQL Database での Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database)」をご覧ください。 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake Store では、アカウントに格納されているデータを暗号化することもできます。 暗号化を有効にすると、Data Lake Store によってデータの永続化の前の暗号化と取得前の暗号化解除が自動的に行われるので、データにアクセスするクライアントに対してこの動作が透過的になります。 詳細については、「[Azure Data Lake Store のセキュリティ](../data-lake-store/data-lake-store-security-overview.md)」をご覧ください。 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob Storage と Azure Table Storage
Azure Blob Storage と Azure Table Storage では、Storage Service Encryption (SSE) をサポートしています。SSE により、データをストレージに永続化する前の暗号化と取得前の暗号化解除が自動的に実行されます。 詳細については、「[保存データ向け Azure Storage Service Encryption](../storage/common/storage-service-encryption.md)」をご覧ください。

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 では、保存データのクライアント暗号化とサーバー暗号化の両方をサポートしています。 詳細については、「[暗号化を使用したデータの保護](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html)」をご覧ください。 現在、Data Factory では仮想プライベート クラウド (VPC) 内の Amazon S3 はサポートしていません。

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift では、保存データのクラスター暗号化をサポートしています。 詳細については、「[Amazon Redshift データベース暗号化](http://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html)」をご覧ください。 現在、Data Factory では VPC 内の Amazon Redshift はサポートしていません。 

#### <a name="salesforce"></a>Salesforce
Salesforce では、ファイル、添付ファイル、カスタム フィールドをすべて暗号化できる Shield Platform Encryption をサポートしています。 詳細については、「[Understanding the Web Server OAuth Authentication Flow (Web サーバーの OAuth 認証フローについて)](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm)」をご覧ください。  

## <a name="hybrid-scenarios-using-self-hosted-integration-runtime"></a>ハイブリッド シナリオ (セルフホステッド統合ランタイムの使用)
ハイブリッド シナリオでは、オンプレミス ネットワーク、仮想ネットワーク (Azure)、または仮想プライベート クラウド (Amazon) 内にセルフホステッド統合ランタイムをインストールする必要があります。 セルフホステッド統合ランタイムは、ローカルのデータ ストアにアクセスできる必要があります。 セルフホステッド統合ランタイムの詳細については、「[セルフホステッド統合ランタイム](create-self-hosted-integration-runtime.md)」を参照してください。 

![セルフホステッド統合ランタイム チャネル](media/data-movement-security-considerations/data-management-gateway-channels.png)

**コマンド チャネル**は、Data Factory のデータ移動サービスとセルフホステッド統合ランタイム間で通信できるようにします。 通信には、アクティビティに関連する情報が含まれます。 データ チャネルは、オンプレミス データ ストアとクラウド データ ストア間でデータを転送するために使用されます。    

### <a name="on-premises-data-store-credentials"></a>オンプレミス データ ストアの資格情報
オンプレミス データ ストアの資格情報は、常に暗号化され格納されます。 セルフホステッド統合ランタイム コンピューターのローカル、または Azure Data Factory で管理されている記憶域のいずれかに格納できます (クラウド ストア資格情報と同様)。 

1. **資格情報はローカルに保存**するよう選択できます。 セルフホステッド統合ランタイムでローカルに資格情報を暗号化して格納したい場合、[セルフホステッド統合ランタイムで資格情報を暗号化する方法](encrypt-credentials-self-hosted-integration-runtime.md)の手順に従ってください。 このオプションは、すべてのコネクタでサポートされています。 セルフホステッド統合ランタイムは、Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) を使用して機密情報/資格情報を暗号化します。 

   リンクされたサービスの資格情報の暗号化またはリンクされたサービスの機密情報の暗号化には、**New-AzureRmDataFactoryV2LinkedServiceEncryptCredential** コマンドレットを使用してください。 それから JSON から返されるものを (**connectionString** の **EncryptedCredential** 要素と共に) 使用して、**Set-AzureRmDataFactoryV2LinkedSevrice** コマンドレットを使用してリンクされたサービスを作成できます。  

2. 前述の手順の説明に従って **New-AzureRmDataFactoryV2LinkedServiceEncryptCredential** コマンドレットを使用せず、接続文字列/資格情報をインラインで使用して **Set-AzureRmDataFactoryV2LinkedSevrice** コマンドレットを JSON で直接使用する場合、リンクされたサービスは **Azure Data Factory によって管理されるストレージで暗号化され格納**されます。 それでも機密情報は証明書によって暗号化され、これらの証明書は Microsoft によって管理されます。



#### <a name="ports-used-during-encrypting-linked-service-on-self-hosted-integration-runtime"></a>セルフホステッド統合ランタイム上のリンクされたサービスの暗号化時に使用されるポート
PowerShell は既定で、セキュリティで保護された通信にセルフホステッド統合ランタイムでコンピューター上のポート **8050** を使用します。 このポートは、必要に応じて変更できます。  

![ゲートウェイの HTTPS ポート](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>転送中の暗号化
すべてのデータ転送は、Azure サービスとの通信中に man-in-the-middle 攻撃を防止するために、セキュリティで保護されたチャネル **HTTPS** および **TLS over TCP** を介して行われます。

オンプレミス ネットワークと Azure 間の通信チャネルのセキュリティを強化するために、[IPSec VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) または [ExpressRoute](../expressroute/expressroute-introduction.md) を使用することもできます。

仮想ネットワークは、クラウド内のユーザーのネットワークを論理的に表したものです。 IPSec VPN (サイト間) または ExpressRoute (プライベート ピアリング) を設定して、オンプレミス ネットワークを Azure 仮想ネットワーク (VNet) に接続できます。     

次の表には、ハイブリッド データ移動のソースと移動先の場所の異なる組み合わせに基づいたネットワークとセルフホステッド統合ランタイムの構成の推奨事項がまとめてあります。

| ソース      | 変換先                              | ネットワーク構成                    | 統合ランタイムのセットアップ                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| オンプレミスの | 仮想ネットワークにデプロイされた仮想マシンとクラウド サービス | IPSec VPN (ポイント対サイトまたはサイト間) | セルフホステッド統合ランタイムはオンプレミスまたは VNet の Azure 仮想マシン (VM) にインストールできます |
| オンプレミスの | 仮想ネットワークにデプロイされた仮想マシンとクラウド サービス | ExpressRoute (プライベート ピアリング)           | セルフホステッド統合ランタイムはオンプレミスまたは VNet の Azure VM にインストールできます |
| オンプレミスの | パブリック エンドポイントが存在する Azure ベースのサービス | ExpressRoute (パブリック ピアリング)            | セルフホステッド統合ランタイムはオンプレミスにインストールする必要があります |

次の図は、オンプレミス データベースと Azure サービス間でのデータの移動に ExpressRoute および IPSec VPN (Virtual Network を使用) を使用した場合のセルフホステッド統合ランタイムの使用方法を示しています。

**ExpressRoute:**

![ExpressRoute とゲートウェイの使用](media/data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN:**

![IPSec VPN とゲートウェイの使用](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-self-hosted-integration-runtime"></a>ファイアウォール構成とホワイトリストに登録する IP アドレス (セルフホステッド統合ランタイム)

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>オンプレミス/プライベート ネットワークのファイアウォール要件  
企業では、組織の中央ルーターで**企業ファイアウォール**が実行されています。 また、セルフホステッド統合ランタイムがインストールされているローカル コンピューターで **Windows ファイアウォール**がデーモンとして実行されています。 

次の表には、**企業ファイアウォール**の**送信ポート**とドメインの要件を示しています。

| ドメイン名                  | 送信ポート | Description                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443、80        | セルフホステッド統合ランタイムが Data Factory のデータ移動サービスに接続するために必要です。 |
| `*.core.windows.net`          | 443            | [ステージング コピー](copy-activity-performance.md#staged-copy)機能を使用する場合に、セルフホステッド統合ランタイムが Azure Storage アカウントに接続するために使用します。 |
| `*.frontend.clouddatahub.net` | 443            | セルフホステッド統合ランタイムが Azure Data Factory サービスに接続するために必要です。 |
| `*.database.windows.net`      | 1433           | (オプション) 移動先が Azure SQL Database または Azure SQL Data Warehouse である場合に必要です。 ステージング コピー機能を使用すると、ポート 1433 を開かずに Azure SQL Database または Azure SQL Data Warehouse にデータをコピーします。 |
| `*.azuredatalakestore.net`    | 443            | (オプション) 移動先が Azure Data Lake Store である場合に必要です。 |

> [!NOTE] 
> 各データ ソースで必要な場合は、企業ファイアウォール レベルでポート/ホワイトリストに登録するドメインを管理する必要があります。 この表では、例として Azure SQL Database、Azure SQL Data Warehouse、Azure Data Lake Store のみを使用しています。   

次の表には、**Windows ファイアウォール**の**受信ポート**の要件があります。

| 受信ポート | 説明                              |
| ------------- | ---------------------------------------- |
| 8050 (TCP)    | [セルフホステッド統合ランタイム](encrypt-credentials-self-hosted-integration-runtime.md)/資格情報マネージャー アプリケーションでの資格情報の暗号化の説明に従って、セルフホステッド統合ランタイムのオンプレミス データ ストアに安全に資格情報を設定するために、PowerShell 暗号化コマンドレットで必要です。 |

![ゲートウェイのポートの要件](media\data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurationswhitelisting-in-data-store"></a>データ ストアの IP 構成/ホワイトリスト登録
クラウドの一部のデータ ストアでは、アクセス元のコンピューターの IP アドレスをホワイトリストに登録する必要もあります。 セルフホステッド統合ランタイム コンピューターの IP アドレスがファイアウォールで適切にホワイトリストに登録/構成されていることを確認します。

以下のクラウド データ ストアでは、セルフホステッド統合ランタイム コンピューターの IP アドレスをホワイトリストに登録する必要があります。 これらのデータ ストアの中には、既定では、IP アドレスをホワイトリストに登録する必要がないものもあります。 

- [Azure SQL Database](../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md#create-a-server-level-firewall-rule-in-the-azure-portal)
- [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../cosmos-db/firewall-support.md)
- [Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>よく寄せられる質問

**質問:** セルフホステッド統合ランタイムは別のデータ ファクトリと共有できますか?
**回答:** この機能はまだサポートされていません。 現在、この機能のサポートに積極的に取り組んでいます。

**質問:** セルフホステッド統合ランタイムの動作にはどのようなポートの要件がありますか?
**回答:** セルフホステッド統合ランタイムは、インターネットを開くために HTTP ベースの接続を行います。 セルフホステッド統合ランタイムがこの接続を確立するには、**送信ポート 443 と 80** を開く必要があります。 資格情報マネージャー アプリケーションのコンピューター レベル (オンプレミスのファイアウォール レベルではない) でのみ**受信ポート 8050** を開きます。 Azure SQL Database または Azure SQL Data Warehouse をソースまたは移動先として使用する場合は、ポート **1433** も開く必要があります。 詳細については、[ファイアウォール構成とホワイトリストに登録する IP アドレス](#firewall-configurations-and-whitelisting-ip-address-of gateway)に関するセクションを参照してください。 


## <a name="next-steps"></a>次のステップ
コピー アクティビティのパフォーマンスについては、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](copy-activity-performance.md)」を参照してください。

 

