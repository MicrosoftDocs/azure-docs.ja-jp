---
title: Azure Data Factory におけるデータ移動のセキュリティに関する考慮事項
description: Azure Data Factory におけるデータ移動の保護について説明します。
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 467ba9f36dbcd44c5b8d87ee2f20d178d62d9732
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930809"
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Azure Data Factory - データ移動のセキュリティに関する考慮事項

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 プレビュー段階にある Data Factory サービスの現在のバージョンを使用している場合は、[Data Factory のデータ移動のセキュリティ上の考慮事項](../data-movement-security-considerations.md)に関する記事を参照してください。

## <a name="introduction"></a>はじめに
この記事では、Azure Data Factory のデータ移動サービスがデータを保護するために使用する基本的なセキュリティ インフラストラクチャについて説明します。 Azure Data Factory 管理リソースは、Azure のセキュリティ インフラストラクチャ上に構築されており、Azure が提供する利用可能なすべてのセキュリティ対策を使用します。

Data Factory ソリューションでは、1 つ以上のデータ [パイプライン](data-factory-create-pipelines.md)を作成します。 パイプラインは、1 つのタスクを連携して実行するアクティビティの論理的なグループです。 これらのパイプラインは、データ ファクトリが作成されたリージョンに存在します。 

Data Factory を利用できるリージョンが**米国西部**、**米国東部**、**北ヨーロッパ**のみであっても、データ移動サービスは[いくつかのリージョンでグローバル](data-factory-data-movement-activities.md#global)に利用できます。 Data Factory サービスでは、データ移動サービスがまだデプロイされていないリージョンを代替リージョンとして使用するようにサービスに明示的に指示しない限り、データが地域/リージョンを離れないことが保証されます。 

Azure Data Factory 自体は、クラウド データ ストアのリンクされたサービス資格情報以外のデータを格納しません。その資格情報は証明書を使用して暗号化されます。 Azure Data Factory を使用すると、データ主導型のワークフローを作成し、[サポートされているデータ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats)間でのデータ移動と、他のリージョンまたはオンプレミス環境にある[コンピューティング サービス](data-factory-compute-linked-services.md)を使用したデータ処理を調整できます。 また、プログラムと UI の両方のメカニズムを使用して、 [ワークフローを監視および管理](data-factory-monitor-manage-pipelines.md) することもできます。

Azure Data Factory を使用したデータ移動は、次の**認定を取得**しています。
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA)  
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018) 
-   [CSA STAR](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)
     
Azure コンプライアンスと、Azure が独自のインフラストラクチャをセキュリティで保護するしくみについて興味がある場合は、 [Microsoft Trust Center (セキュリティ センター)](https://microsoft.com/en-us/trustcenter/default.aspx)にアクセスしてください。 

この記事では、次の 2 つのデータ移動シナリオでセキュリティに関する考慮事項を確認します。 

- **クラウド シナリオ** - このシナリオでは、ソースと移動先の両方にインターネットを通じてパブリックにアクセスできます。 これには、Azure Storage、Azure SQL Data Warehouse、Azure SQL Database、Azure Data Lake Store、Amazon S3、Amazon Redshift などの管理クラウド ストレージ サービス、Salesforce などの SaaS サービス、FTP や OData などの Web プロトコルが該当します。 サポートされているデータ ソースの完全な一覧は、[ここで](data-factory-data-movement-activities.md#supported-data-stores-and-formats)確認できます。
- **ハイブリッド シナリオ** - このシナリオでは、ソースと移動先のどちらかがファイアウォールの内側またはオンプレミスの企業ネットワーク内にあるか、データ ストアがプライベート ネットワーク/仮想ネットワーク内 (ほとんどの場合はソース) にあり、パブリックにアクセスできません。 仮想マシンでホストされているデータベース サーバーもこのシナリオに該当します。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>クラウド シナリオ
### <a name="securing-data-store-credentials"></a>データ ストアの資格情報の保護
Azure Data Factory では、データ ストアの資格情報を保護するために、**Microsoft が管理する証明書**を使用して**暗号化**します。 証明書は、**2 年**ごとに交換されます (証明書の更新と資格情報の移行が行われます)。 暗号化された資格情報は、**Azure Data Factory 管理サービスによって管理される Azure Storage** に安全に格納されます。 Azure Storage のセキュリティの詳細については、「[Azure Storage のセキュリティの概要](../../security/fundamentals/storage-overview.md)」を参照してください。

### <a name="data-encryption-in-transit"></a>転送中のデータの暗号化
クラウド データ ストアが HTTPS または TLS をサポートしている場合、Data Factory のデータ移動サービスとクラウド データ ストア間のデータ転送はすべて、セキュリティで保護されたチャネル HTTPS または TLS を介して行われます。

> [!NOTE]
> データがデータベースとの間で転送中である間は、常に **Azure SQL Database** および **Azure SQL Data Warehouse** への接続をすべて (SSL/TLS を使用して) 暗号化する必要があります。 JSON エディターを使用してパイプラインを作成する場合は、**encryption** プロパティを追加し、**接続文字列**で **true** に設定します。 [コピー ウィザード](data-factory-azure-copy-wizard.md)を使用すると、既定でこのプロパティが設定されます。 **Azure Storage** では、接続文字列で **HTTPS** を使用できます。

### <a name="data-encryption-at-rest"></a>保存データの暗号化
一部のデータ ストアは、保存データの暗号化をサポートしています。 そうしたデータ ストアに対してはデータ暗号化メカニズムを有効にすることをお勧めします。 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Azure SQL Data Warehouse の Transparent Data Encryption (TDE) を使用すると、保存データの暗号化と暗号化解除をリアルタイムで実行することによって、悪意のあるアクティビティの脅威から保護できます。 この動作はクライアントに対して透過的です。 詳細については、「[SQL Data Warehouse でのデータベース保護](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md)」をご覧ください。

#### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database では、Transparent Data Encryption (TDE) もサポートしています。TDE を使用すると、データの暗号化と暗号化解除をリアルタイムで実行することによって、悪意のあるアクティビティの脅威から保護できます。アプリケーションを変更する必要はありません。 この動作はクライアントに対して透過的です。 詳細については、「[Azure SQL Database での Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database)」をご覧ください。 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake Store では、アカウントに格納されているデータを暗号化することもできます。 暗号化を有効にすると、Data Lake Store によってデータの永続化の前の暗号化と取得前の暗号化解除が自動的に行われるので、データにアクセスするクライアントに対してこの動作が透過的になります。 詳細については、「[Azure Data Lake Store のセキュリティ](../../data-lake-store/data-lake-store-security-overview.md)」をご覧ください。 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob Storage と Azure Table Storage
Azure Blob Storage と Azure Table Storage では、Storage Service Encryption (SSE) をサポートしています。SSE により、データをストレージに永続化する前の暗号化と取得前の暗号化解除が自動的に実行されます。 詳細については、「[保存データ向け Azure Storage Service Encryption](../../storage/common/storage-service-encryption.md)」をご覧ください。

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 では、保存データのクライアント暗号化とサーバー暗号化の両方をサポートしています。 詳細については、「[暗号化を使用したデータの保護](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html)」をご覧ください。 現在、Data Factory では仮想プライベート クラウド (VPC) 内の Amazon S3 はサポートしていません。

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift では、保存データのクラスター暗号化をサポートしています。 詳細については、「[Amazon Redshift データベース暗号化](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html)」をご覧ください。 現在、Data Factory では VPC 内の Amazon Redshift はサポートしていません。 

#### <a name="salesforce"></a>Salesforce
Salesforce では、ファイル、添付ファイル、カスタム フィールドをすべて暗号化できる Shield Platform Encryption をサポートしています。 詳細については、「[Understanding the Web Server OAuth Authentication Flow (Web サーバーの OAuth 認証フローについて)](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm)」をご覧ください。  

## <a name="hybrid-scenarios-using-data-management-gateway"></a>ハイブリッド シナリオ (Data Management Gateway を使用)
ハイブリッド シナリオでは、オンプレミス ネットワーク、仮想ネットワーク (Azure)、または仮想プライベート クラウド (Amazon) 内に Data Management Gateway をインストールする必要があります。 ゲートウェイは、ローカル データ ストアにアクセスできる必要があります。 ゲートウェイの詳細については、「[Data Management Gateway](data-factory-data-management-gateway.md)」を参照してください。 

![Data Management Gateway のチャネル](media/data-factory-data-movement-security-considerations/data-management-gateway-channels.png)

**コマンド チャネル**は、Data Factory のデータ移動サービスと Data Management Gateway 間で通信できるようにします。 通信には、アクティビティに関連する情報が含まれます。 データ チャネルは、オンプレミス データ ストアとクラウド データ ストア間でデータを転送するために使用されます。    

### <a name="on-premises-data-store-credentials"></a>オンプレミス データ ストアの資格情報
オンプレミス データ ストアの資格情報は、ローカルに格納されます (クラウドには格納されません)。 この資格情報は、次の 3 つの異なる方法で設定できます。 

- Azure Portal またはコピー ウィザードから HTTPS 経由で**プレーンテキスト**を使用する (セキュリティが低下します)。 資格情報は、プレーンテキスト形式でオンプレミスのゲートウェイに渡されます。
- コピー ウィザードから **JavaScript 暗号化ライブラリを使用する**。
- **ClickOnce ベースの資格情報マネージャー アプリケーション**を使用する。 ClickOnce アプリケーションは、ゲートウェイにアクセスできるオンプレミスのコンピューターで実行され、データ ストアの資格情報を設定します。 このオプションと次のオプションが最も安全なオプションです。 既定では、資格情報マネージャー アプリケーションは、通信をセキュリティで保護するために、ゲートウェイ コンピューターのポート 8050 を使用します。  
- [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/New-azDataFactoryEncryptValue) PowerShell コマンドレットを使用して、資格情報を暗号化します。 コマンドレットはゲートウェイに構成されている証明書を使用して資格情報を暗号化します。 このコマンドレットから返された暗号化済みの資格情報を使用し、[New-AzDataFactoryLinkedService](/powershell/module/az.datafactory/new-azdatafactorylinkedservice) コマンドレットで使用する JSON ファイル内、またはポータルの Data Factory エディターの JSON スニペット内の **connectionString** の **EncryptedCredential** 要素にその資格情報を追加できます。 このオプションと ClickOnce アプリケーションが最も安全なオプションです。 

#### <a name="javascript-cryptography-library-based-encryption"></a>JavaScript 暗号化ライブラリ ベースの暗号化
[コピー ウィザード](data-factory-copy-wizard.md)から [JavaScript 暗号化ライブラリ](https://www.microsoft.com/download/details.aspx?id=52439)を使用して、データ ストアの資格情報を暗号化できます。 このオプションを選択した場合、コピー ウィザードがゲートウェイの公開キーを取得し、そのキーを使用してデータ ストアの資格情報を暗号化します。 資格情報はゲートウェイ コンピューターによって暗号化が解除され、Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) によって保護されます。

**サポートされているブラウザー:** IE8、IE9、IE10、IE11、Microsoft Edge、最新の Firefox、Chrome、Opera、Safari ブラウザー。 

#### <a name="click-once-credentials-manager-app"></a>ClickOnce 資格情報マネージャー アプリケーション
パイプラインの作成時に、Azure Portal またはコピー ウィザードから ClickOnce ベースの認証情報マネージャー アプリケーションを起動できます。 このアプリケーションでは、資格情報がプレーンテキストで転送されません。 既定では、資格情報マネージャー アプリは、通信をセキュリティで保護するために、ゲートウェイ コンピューターのポート **8050** を使用します。 このポートは、必要に応じて変更できます。  
  
![ゲートウェイの HTTPS ポート](media/data-factory-data-movement-security-considerations/https-port-for-gateway.png)

現在、Data Management Gateway では 1 つの**証明書**を使用します。 この証明書は、ゲートウェイのインストール時に作成されます (2016 年 11 月以降に作成された Data Management Gateway およびバージョン 2.4.xxxx.x 以降に適用されます)。 この証明書は独自の SSL または TLS 証明書に置き換えることができます。 この証明書は、ClickOnce 資格情報マネージャー アプリケーションがデータ ストアの資格情報を設定する際にゲートウェイ コンピューターに安全に接続するために使用します。 ゲートウェイ コンピューターで Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) を使用して、データ ストアの資格情報をオンプレミスに安全に格納します。 

> [!NOTE]
> 2016 年 11 月以前にインストールされた古いゲートウェイ (またはバージョン 2.3.xxxx.x 以前) では、暗号化されてクラウドに格納された資格情報を引き続き使用します。 ゲートウェイを最新バージョンにアップグレードしても、資格情報はオンプレミスのコンピューターに移行されません。    
  
| ゲートウェイのバージョン (作成時) | 資格情報の格納先 | 資格情報の暗号化/セキュリティ | 
| --------------------------------- | ------------------ | --------- |  
| 2\.3.xxxx.x 以前 | クラウド | 証明書を使用して暗号化されます (資格情報マネージャー アプリケーションで使用する証明書とは異なります) | 
| 2\.4.xxxx.x 以降 | オンプレミス | DPAPI を使用して保護されます | 
  

### <a name="encryption-in-transit"></a>転送中の暗号化
すべてのデータ転送は、Azure サービスとの通信中に man-in-the-middle 攻撃を防止するために、セキュリティで保護されたチャネル **HTTPS** および **TLS over TCP** を介して行われます。
 
オンプレミス ネットワークと Azure 間の通信チャネルのセキュリティを強化するために、[IPSec VPN](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) または [ExpressRoute](../../expressroute/expressroute-introduction.md) を使用することもできます。

仮想ネットワークは、クラウド内のユーザーのネットワークを論理的に表したものです。 IPSec VPN (サイト間) または ExpressRoute (プライベート ピアリング) を設定して、オンプレミス ネットワークを Azure 仮想ネットワーク (VNet) に接続できます。     

次の表には、ハイブリッド データ移動のソースと移動先の場所の異なる組み合わせに基づいたネットワークとゲートウェイの構成の推奨事項がまとめてあります。

| source | 宛先 | ネットワーク構成 | ゲートウェイの設定 |
| ------ | ----------- | --------------------- | ------------- | 
| オンプレミス | 仮想ネットワークにデプロイされた仮想マシンとクラウド サービス | IPSec VPN (ポイント対サイトまたはサイト間) | ゲートウェイはオンプレミスまたは VNet の Azure 仮想マシン (VM) にインストールできます | 
| オンプレミス | 仮想ネットワークにデプロイされた仮想マシンとクラウド サービス | ExpressRoute (プライベート ピアリング) | ゲートウェイはオンプレミスまたは VNet の Azure VM にインストールできます | 
| オンプレミス | パブリック エンドポイントが存在する Azure ベースのサービス | ExpressRoute (パブリック ピアリング) | ゲートウェイはオンプレミスにインストールする必要があります | 

次の図は、オンプレミス データベースと Azure サービス間でのデータの移動に ExpressRoute および IPSec VPN (Virtual Network を使用) を使用した場合の Data Management Gateway の使用方法を示しています。

**ExpressRoute:**
 
![ExpressRoute とゲートウェイの使用](media/data-factory-data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN:**

![IPSec VPN とゲートウェイの使用](media/data-factory-data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a>ゲートウェイのファイアウォール構成とホワイトリストに登録する IP アドレス

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>オンプレミス/プライベート ネットワークのファイアウォール要件  
企業では、組織の中央ルーターで**企業ファイアウォール**が実行されています。 また、ゲートウェイがインストールされているローカル コンピューターで **Windows ファイアウォール**がデーモンとして実行されています。 

次の表には、**企業ファイアウォール**の**送信ポート**とドメインの要件を示しています。

| ドメイン名 | 送信ポート | 説明 |
| ------------ | -------------- | ----------- | 
| `*.servicebus.windows.net` | 443、80 | ゲートウェイが Data Factory のデータ移動サービスに接続するために必要です。 |
| `*.core.windows.net` | 443 | [ステージング コピー](data-factory-copy-activity-performance.md#staged-copy)機能を使用する場合に、ゲートウェイが Azure ストレージ アカウントに接続するために使用します。 | 
| `*.frontend.clouddatahub.net` | 443 | ゲートウェイが Azure Data Factory サービスに接続するために必要です。 | 
| `*.database.windows.net` | 1433   | (オプション) 移動先が Azure SQL Database または Azure SQL Data Warehouse である場合に必要です。 ステージング コピー機能を使用すると、ポート 1433 を開かずに Azure SQL Database または Azure SQL Data Warehouse にデータをコピーします。 | 
| `*.azuredatalakestore.net` | 443 | (オプション) 移動先が Azure Data Lake Store である場合に必要です。 | 

> [!NOTE] 
> 各データ ソースで必要な場合は、企業ファイアウォール レベルでポート/ホワイトリストに登録するドメインを管理する必要があります。 この表では、例として Azure SQL Database、Azure SQL Data Warehouse、Azure Data Lake Store のみを使用しています。   

次の表には、**Windows ファイアウォール**の**受信ポート**の要件を示しています。

| 受信ポート | 説明 | 
| ------------- | ----------- | 
| 8050 (TCP) | 資格情報マネージャー アプリケーションがゲートウェイでオンプレミス データ ストアの資格情報を安全に設定するために必要です。 | 

![ゲートウェイのポートの要件](media/data-factory-data-movement-security-considerations/gateway-port-requirements.png)

#### <a name="ip-configurations-whitelisting-in-data-store"></a>データ ストアの IP 構成/ホワイトリスト登録
クラウドの一部のデータ ストアでは、アクセス元のコンピューターの IP アドレスをホワイトリストに登録する必要もあります。 ゲートウェイ コンピューターの IP アドレスがファイアウォールで適切にホワイトリストに登録/構成されていることを確認します。

以下のクラウド データ ストアでは、ゲートウェイ コンピューターの IP アドレスをホワイトリストに登録する必要があります。 これらのデータ ストアの中には、既定では、IP アドレスをホワイトリストに登録する必要がないものもあります。 

- [Azure SQL Database](../../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../../cosmos-db/firewall-support.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>よく寄せられる質問

**質問:** ゲートウェイを異なるデータ ファクトリ間で共有できますか?
**回答:** この機能はまだサポートされていません。 現在、この機能のサポートに積極的に取り組んでいます。

**質問:** ゲートウェイが動作するためのポートの要件は何ですか?
**回答:** ゲートウェイは、インターネットを開くために HTTP ベースの接続を確立します。 ゲートウェイがこの接続を確立するためには、**送信ポート 443 と 80** を開く必要があります。 資格情報マネージャー アプリケーションのコンピューター レベル (オンプレミスのファイアウォール レベルではない) でのみ**受信ポート 8050** を開きます。 Azure SQL Database または Azure SQL Data Warehouse をソースまたは移動先として使用する場合は、ポート **1433** も開く必要があります。 詳細については、[ファイアウォール構成とホワイトリストに登録する IP アドレス](#firewall-configurations-and-whitelisting-ip-address-of gateway)に関するセクションを参照してください。 

**質問:** ゲートウェイの証明書の要件は何ですか?
**回答:** 現行のゲートウェイには、資格情報マネージャー アプリケーションがデータ ストアの資格情報を安全に設定するために使用する証明書が必要です。 この証明書は、ゲートウェイの設定によって作成および構成された自己署名証明書です。 代わりに独自の TLS または SSL 証明書を使用できます。 詳細については、「[ClickOnce 資格情報マネージャー アプリケーション](#click-once-credentials-manager-app)」セクションを参照してください。 

## <a name="next-steps"></a>次の手順
コピー アクティビティのパフォーマンスについては、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」を参照してください。

 
