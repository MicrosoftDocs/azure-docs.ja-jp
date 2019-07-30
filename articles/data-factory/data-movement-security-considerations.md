---
title: Azure Data Factory のセキュリティに関する考慮事項 | Microsoft Docs
description: Azure Data Factory のデータ移動サービスがデータの保護を容易にするために使用する基本的なセキュリティ インフラストラクチャについて説明します。
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: abnarain
ms.openlocfilehash: 3dab2da2d54efe73b7b782800b190ea8aac2b5cd
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67460685"
---
#  <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>Azure Data Factory におけるデータ移動のセキュリティに関する考慮事項
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
>
> * [Version 1](v1/data-factory-data-movement-security-considerations.md)
> * [現在のバージョン](data-movement-security-considerations.md)

この記事では、Azure Data Factory のデータ移動サービスがデータの保護を容易にするために使用する基本的なセキュリティ インフラストラクチャについて説明します。 Data Factory 管理リソースは、Azure のセキュリティ インフラストラクチャ上に構築されており、Azure が提供する利用可能なすべてのセキュリティ対策を使用します。

Data Factory ソリューションでは、1 つ以上のデータ [パイプライン](concepts-pipelines-activities.md)を作成します。 パイプラインは、1 つのタスクを連携して実行するアクティビティの論理的なグループです。 これらのパイプラインは、データ ファクトリが作成されたリージョンに存在します。 

Data Factory を利用できるのは少数のリージョンだけですが、データ移動サービスはデータのコンプライアンス、効率性、ネットワーク送信コスト削減のために[グローバルに利用できます](concepts-integration-runtime.md#integration-runtime-location)。 

Azure Data Factory は、クラウド データ ストアのリンクされたサービス資格情報以外のデータを格納しません。その資格情報は証明書を使用して暗号化されます。 Azure Data Factory を使用すると、データドリブン ワークフローを作成し、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)間でのデータ移動と、他のリージョンまたはオンプレミスの環境にある[コンピューティング サービス](compute-linked-services.md)を使用したデータ処理を調整できます。 また、SDK と Azure Monitor を使用して、ワークフローを監視および管理することもできます。

Data Factory は、次の認証を取得しています。

| **[CSA STAR 認証](https://www.microsoft.com/trustcenter/compliance/csa-star-certification)** |
| :----------------------------------------------------------- |
| **[ISO 20000-1:2011](https://www.microsoft.com/trustcenter/Compliance/ISO-20000-1)** |
| **[ISO 22301:2012](https://www.microsoft.com/trustcenter/compliance/iso-22301)** |
| **[ISO 27001:2013](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001)** |
| **[ISO 27017:2015](https://www.microsoft.com/trustcenter/compliance/iso-iec-27017)** |
| **[ISO 27018:2014](https://www.microsoft.com/trustcenter/compliance/iso-iec-27018)** |
| **[ISO 9001:2015](https://www.microsoft.com/trustcenter/compliance/iso-9001)** |
| **[SOC 1、2、3](https://www.microsoft.com/trustcenter/compliance/soc)** |
| **[HIPAA BAA](https://www.microsoft.com/trustcenter/compliance/hipaa)** |

Azure コンプライアンスと、Azure が独自のインフラストラクチャをセキュリティで保護するしくみについて興味がある場合は、[Microsoft Trust Center (セキュリティ センター)](https://microsoft.com/en-us/trustcenter/default.aspx) にアクセスしてください。 すべての Azure コンプライアンス認証の最新の一覧については、「 https://aka.ms/AzureCompliance 」を参照してください。

この記事では、次の 2 つのデータ移動シナリオでセキュリティに関する考慮事項を確認します。 

- **クラウド シナリオ**:このシナリオでは、ソースと移動先の両方にインターネットを通じてパブリックにアクセスできます。 これには、Azure Storage、Azure SQL Data Warehouse、Azure SQL Database、Azure Data Lake Store、Amazon S3、Amazon Redshift などの管理クラウド ストレージ サービス、Salesforce などの SaaS サービス、FTP や OData などの Web プロトコルが該当します。 サポートされているデータ ソースの完全な一覧については、「[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)」を参照してください。
- **ハイブリッド シナリオ**:このシナリオでは、ソースまたは移動先のどちらかが、ファイアウォールの内側またはオンプレミスの企業ネットワーク内にあります。 あるいは、データ ストアがプライベート ネットワークまたは仮想ネットワーク内 (ほとんどの場合はソース) にあり、パブリックにアクセスできません。 仮想マシンでホストされているデータベース サーバーもこのシナリオに該当します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>クラウド シナリオ

### <a name="securing-data-store-credentials"></a>データ ストアの資格情報の保護

- **Azure Data Factory で管理されたストアに暗号化された資格情報を格納します**。 Data Factory では、データ ストアの資格情報の保護を容易にするために、Microsoft が管理する証明書を使用して暗号化します。 証明書は、2 年ごとに交換されます (証明書の更新と資格情報の移行が行われます)。 暗号化された資格情報は、Azure Data Factory 管理サービスによって管理される Azure ストレージ アカウントに安全に格納されます。 Azure Storage のセキュリティの詳細については、「[Azure Storage のセキュリティの概要](../security/security-storage-overview.md)」を参照してください。
- **Azure Key Vault に資格情報を格納します**。 データ ストアの資格情報は、[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) に格納することもできます。 Data Factory は、アクティビティの実行中に資格情報を取得します。 詳細については、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」を参照してください。

### <a name="data-encryption-in-transit"></a>転送中のデータの暗号化
クラウド データ ストアが HTTPS または TLS をサポートしている場合、Data Factory のデータ移動サービスとクラウド データ ストア間のデータ転送はすべて、セキュリティで保護されたチャネル HTTPS または TLS を介して行われます。

> [!NOTE]
> データがデータベースとの間で転送中である間は、Azure SQL Database および Azure SQL Data Warehouse への接続をすべて (SSL/TLS を使用して) 暗号化する必要があります。 JSON を使用してパイプラインを作成する場合は、encryption プロパティを追加し、接続文字列で **true** に設定します。 Azure Storage では、接続文字列で **HTTPS** を使用できます。

> [!NOTE]
> Oracle からデータを移動している間、転送中の暗号化を有効にするには、次のいずれかのオプションを実行します。
> 1. Oracle サーバーで Oracle Advanced Security (OAS) に移動し、暗号化の設定を構成します。Triple-DES Encryption (3DES) と Advanced Encryption Standard (AES) がサポートされています。詳細については、[こちら](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759)をご覧ください。 ADF は暗号化方法を自動的にネゴシエートし、Oracle への接続を確立するときにユーザーが OAS で構成した方法を使用します。
> 2. ADF では、(リンクされたサービス内の) 接続文字列に EncryptionMethod=1 を追加できます。 これにより、暗号化方法として SSL/TLS が使用されます。 これを使用するには、Oracle サーバー側の OAS で非 SSL 暗号化の設定を無効にして、暗号化の競合を回避する必要があります。

> [!NOTE]
> 使用される TLS のバージョンは、1.2 です。

### <a name="data-encryption-at-rest"></a>保存データの暗号化
一部のデータ ストアは、保存データの暗号化をサポートしています。 そうしたデータ ストアに対してはデータ暗号化メカニズムを有効にすることをお勧めします。 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Azure SQL Data Warehouse の Transparent Data Encryption (TDE) を使用すると、保存データの暗号化と暗号化解除をリアルタイムで実行することにより、悪意のあるアクティビティの脅威から保護できます。 この動作はクライアントに対して透過的です。 詳細については、「[SQL Data Warehouse でのデータベース保護](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md)」をご覧ください。

#### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database では、Transparent Data Encryption (TDE) もサポートしています。TDE を使用すると、データの暗号化と暗号化解除をリアルタイムで実行することによって、悪意のあるアクティビティの脅威から保護できます。アプリケーションを変更する必要はありません。 この動作はクライアントに対して透過的です。 詳細については、「[Azure SQL Database と Data Warehouse の透過的なデータ暗号化](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)」をご覧ください。

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake Store では、アカウントに格納されているデータを暗号化することもできます。 暗号化を有効にすると、Data Lake Store によってデータの永続化の前の暗号化と取得前の暗号化解除が自動的に行われるので、データにアクセスするクライアントに対してこの動作が透過的になります。 詳細については、「[Azure Data Lake Store のセキュリティ](../data-lake-store/data-lake-store-security-overview.md)」をご覧ください。 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob Storage と Azure Table Storage
Azure Blob Storage と Azure Table Storage では、Storage Service Encryption (SSE) をサポートしています。SSE により、データをストレージに永続化する前の暗号化と取得前の暗号化解除が自動的に実行されます。 詳細については、「[保存データ向け Azure Storage Service Encryption](../storage/common/storage-service-encryption.md)」をご覧ください。

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 では、保存データのクライアント暗号化とサーバー暗号化の両方をサポートしています。 詳細については、「[暗号化を使用したデータの保護](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html)」をご覧ください。

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift では、保存データのクラスター暗号化をサポートしています。 詳細については、「[Amazon Redshift データベース暗号化](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html)」をご覧ください。 

#### <a name="salesforce"></a>Salesforce
Salesforce では、ファイル、添付ファイル、カスタム フィールドをすべて暗号化できる Shield Platform Encryption をサポートしています。 詳細については、「[Understanding the Web Server OAuth Authentication Flow (Web サーバーの OAuth 認証フローについて)](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm)」をご覧ください。  

## <a name="hybrid-scenarios"></a>ハイブリッド シナリオ
ハイブリッド シナリオでは、オンプレミス ネットワーク、仮想ネットワーク (Azure)、または仮想プライベート クラウド (Amazon) 内にセルフホステッド統合ランタイムをインストールする必要があります。 セルフホステッド統合ランタイムは、ローカルのデータ ストアにアクセスできる必要があります。 セルフホステッド統合ランタイムの詳細については、「[自己ホスト型統合ランタイムを作成し構成する方法](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)」を参照してください。 

![セルフホステッド統合ランタイム チャネル](media/data-movement-security-considerations/data-management-gateway-channels.png)

コマンド チャネルは、Data Factory のデータ移動サービスとセルフホステッド統合ランタイム間で通信できるようにします。 通信には、アクティビティに関連する情報が含まれます。 データ チャネルは、オンプレミス データ ストアとクラウド データ ストア間でデータを転送するために使用されます。    

### <a name="on-premises-data-store-credentials"></a>オンプレミス データ ストアの資格情報
オンプレミス データ ストアの資格情報は、常に暗号化され格納されます。 セルフホステッド統合ランタイム コンピューターのローカル、または Azure Data Factory で管理されている記憶域のいずれかに格納できます (クラウド ストア資格情報と同様)。 

- **資格情報をローカルに保存します**。 セルフホステッド統合ランタイムでローカルに資格情報を暗号化して格納したい場合、「[Azure Data Factory でオンプレミスのデータ ストアの資格情報を暗号化する](encrypt-credentials-self-hosted-integration-runtime.md)」の手順に従ってください。 このオプションは、すべてのコネクタでサポートされています。 セルフホステッド統合ランタイムは、Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) を使用して機密データと資格情報を暗号化します。 

   リンクされたサービスの資格情報およびリンクされたサービスの機密情報の暗号化には、**New-AzDataFactoryV2LinkedServiceEncryptedCredential** コマンドレットを使用してください。 それから (接続文字列の **EncryptedCredential** 要素と共に) 返される JSON を使用して、**Set-AzDataFactoryV2LinkedService** コマンドレットを使用してリンクされたサービスを作成できます。  

- **Azure Data Factory で管理されているストレージに格納します**。 接続文字列と資格情報をインラインで使用して **Set-AzDataFactoryV2LinkedService** コマンドレットを JSON で直接使用する場合、リンクされたサービスは暗号化され Azure Data Factory マネージド ストレージに格納されます。 それでも機密情報は証明書によって暗号化され、Microsoft がこれらの証明書を管理します。



#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>セルフホステッド統合ランタイム上のリンクされたサービスの暗号化時に使用されるポート
PowerShell では既定で、セキュリティで保護された通信にセルフホステッド統合ランタイムでコンピューター上のポート 8060 を使用します。 このポートは、必要に応じて変更できます。  

![ゲートウェイの HTTPS ポート](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>転送中の暗号化
すべてのデータ転送は、Azure サービスとの通信中に man-in-the-middle 攻撃を防止するために、セキュリティで保護されたチャネル HTTPS および TLS over TCP を介して行われます。

オンプレミス ネットワークと Azure 間の通信チャネルのセキュリティを強化するために、[IPSec VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) または [Azure ExpressRoute](../expressroute/expressroute-introduction.md) を使用することもできます。

Azure Virtual Network は、クラウド内のユーザーのネットワークを論理的に表したものです。 IPSec VPN (サイト間) または ExpressRoute (プライベート ピアリング) を設定して、オンプレミス ネットワークを仮想ネットワークに接続できます。    

次の表には、ハイブリッド データ移動のソースと移動先の場所の異なる組み合わせに基づいたネットワークとセルフホステッド統合ランタイムの構成の推奨事項がまとめてあります。

| source      | 宛先                              | ネットワーク構成                    | 統合ランタイムのセットアップ                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| オンプレミス | 仮想ネットワークにデプロイされた仮想マシンとクラウド サービス | IPSec VPN (ポイント対サイトまたはサイト間) | セルフホステッド統合ランタイムは、仮想ネットワークの Azure 仮想マシン上にインストールすることをお勧めします。  |
| オンプレミス | 仮想ネットワークにデプロイされた仮想マシンとクラウド サービス | ExpressRoute (プライベート ピアリング)           | セルフホステッド統合ランタイムは、仮想ネットワークの Azure 仮想マシン上にインストールすることをお勧めします。  |
| オンプレミス | パブリック エンドポイントが存在する Azure ベースのサービス | ExpressRoute (Microsoft ピアリング)            | セルフホステッド統合ランタイムは、オンプレミスまたは Azure 仮想マシンにインストールできます。 |

次の図は、オンプレミス データベースと Azure サービス間でのデータの移動に ExpressRoute および IPSec VPN (Azure Virtual Network を使用) を使用した場合のセルフホステッド統合ランタイムの使用方法を示しています。

**ExpressRoute**

![ExpressRoute とゲートウェイの使用](media/data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN**

![IPSec VPN とゲートウェイの使用](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a> ファイアウォール構成とホワイトリストに登録する IP アドレス

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>オンプレミス/プライベート ネットワークのファイアウォール要件  
企業では、組織の中央ルーターで企業ファイアウォールが実行されています。 セルフホステッド統合ランタイムがインストールされているローカル コンピューターで Windows ファイアウォールがデーモンとして実行されています。 

次の表には、企業ファイアウォールの送信ポートとドメインの要件を示しています。

| ドメイン名                  | 送信ポート | 説明                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | セルフホステッド統合ランタイムが Data Factory のデータ移動サービスに接続するために必要です。 |
| `*.frontend.clouddatahub.net` | 443            | セルフホステッド統合ランタイムが Data Factory サービスに接続するために必要です。 |
| `download.microsoft.com`    | 443            | セルフホステッド統合ランタイムが更新プログラムをダウンロードするために必要です。 自動更新を無効にした場合は、これをスキップできます。 |
| `*.core.windows.net`          | 443            | [ステージング コピー](copy-activity-performance.md#staged-copy)機能を使用する場合に、セルフホステッド統合ランタイムが Azure ストレージ アカウントに接続するために使用します。 |
| `*.database.windows.net`      | 1433           | (オプション) Azure SQL Database または Azure SQL Data Warehouse との間でコピーするときに必要です。 ステージング コピー機能を使用すると、ポート 1433 を開かずに Azure SQL Database または Azure SQL Data Warehouse にデータをコピーします。 |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | (オプション) Azure Data Lake Store との間でコピーするときに必要です。 |

> [!NOTE] 
> 各データ ソースで必要な場合は、企業ファイアウォール レベルでポートまたはホワイトリストに登録するドメインを管理する必要があります。 この表では、例として Azure SQL Database、Azure SQL Data Warehouse、Azure Data Lake Store のみを使用しています。   

次の表には、Windows ファイアウォールの受信ポートの要件があります。

| 受信ポート | 説明                              |
| ------------- | ---------------------------------------- |
| 8060 (TCP)    | 「[Azure Data Factory でオンプレミスのデータ ストアの資格情報を暗号化する](encrypt-credentials-self-hosted-integration-runtime.md)」に説明されているように PowerShell 暗号化コマンドレットと、セルフホステッド統合ランタイムのオンプレミス データ ストアに安全に資格情報を設定するために資格情報マネージャー アプリケーションで必要です。 |

![ゲートウェイのポートの要件](media/data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-whitelisting-in-data-stores"></a>データ ストアの IP 構成とホワイトリスト登録
クラウドの一部のデータ ストアでは、ストアにアクセスするコンピューターの IP アドレスをホワイトリストに登録する必要もあります。 セルフホステッド統合ランタイム コンピューターの IP アドレスがファイアウォールで適切にホワイトリストに登録または構成されていることを確認します。

以下のクラウド データ ストアでは、セルフホステッド統合ランタイム コンピューターの IP アドレスをホワイトリストに登録する必要があります。 これらのデータ ストアの中には、既定では、ホワイトリストに登録する必要がないものもあります。 

- [Azure SQL Database](../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../cosmos-db/firewall-support.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>よく寄せられる質問

**セルフホステッド統合ランタイムは別のデータ ファクトリと共有できますか?**

はい。 詳細については、[こちら](https://azure.microsoft.com/blog/sharing-a-self-hosted-integration-runtime-infrastructure-with-multiple-data-factories/)をご覧ください。

**セルフホステッド統合ランタイムの動作にはどのようなポートの要件がありますか?**

セルフホステッド統合ランタイムは、インターネットにアクセスするために HTTP ベースの接続を行います。 セルフホステッド統合ランタイムがこの接続を確立するには、送信ポート 443 を開く必要があります。 資格情報マネージャー アプリケーションのコンピューター レベル (企業のファイアウォール レベルではない) でのみ受信ポート 8060 を開きます。 Azure SQL Database または Azure SQL Data Warehouse をソースまたは移動先として使用する場合は、ポート 1433 も開く必要があります。 詳細については、[ファイアウォール構成とホワイトリストに登録する IP アドレス](#firewall-configurations-and-whitelisting-ip-address-of-gateway)に関するセクションを参照してください。 


## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティのパフォーマンスについては、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](copy-activity-performance.md)」を参照してください。

 
