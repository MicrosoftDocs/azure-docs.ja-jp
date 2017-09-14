---
title: "Azure の暗号化の概要 | Microsoft Docs"
description: "Azure でのさまざまな暗号化オプションの詳細について"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 08/18/2017
ms.author: barclayn
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: 752568747ab96bc0a9c7fc5f24ff28c3bce4e09f
ms.contentlocale: ja-jp
ms.lasthandoff: 08/23/2017

---
# <a name="azure-encryption-overview"></a>Azure の暗号化の概要

この記事では、Microsoft Azure の暗号化の概要について説明します。 保存データの暗号化、移動中のデータの暗号化、Key Vault を使用したキー管理など、暗号化の主な項目について説明します。 各セクションには、より詳細な情報へのリンクが含まれています。

## <a name="encryption-of-data-at-rest"></a>保存データの暗号化

保存データには、物理メディア上の永続的ストレージに存在する、あらゆるデジタル形式の情報が含まれます。 これには、磁気メディアまたは光学メディア上のファイル、アーカイブされたデータ、データのバックアップなどが含まれます。 Microsoft Azure では、さまざまなニーズに応えるために、ファイル、ディスク、BLOB、テーブル ストレージなど、多様なデータ ストレージ ソリューションをご用意しています。 Microsoft は、[Azure SQL Database](../sql-database/sql-database-technical-overview.md)、[CosmosDB](../cosmos-db/introduction.md)、Azure Data Lake を保護する暗号化の機能も提供しています。

保存されているデータの暗号化は、Azure のサービスとしてのソフトウェア (SaaS)、サービスとしてのプラットフォーム (PaaS)、サービスとしてのインフラストラクチャ (IaaS) クラウド モデルのサービスで使用できます。 このドキュメントでは、Azure の暗号化オプションを使用する際に役立つリソースを提供し、その概要について説明します。

Azure で保存データを暗号化する方法の詳細については、「[Azure Data Encryption-at-Rest](azure-security-encryption-atrest.md)」というタイトルのドキュメントをご覧ください。

## <a name="azure-encryption-models"></a>Azure の暗号化モデル

Azure では、サービスが管理するキー、Azure Key Vault でユーザーが管理するキー、またはユーザーが制御するハードウェア上でユーザーが管理するキーを使用したサーバー側暗号化など、さまざまな暗号化モデルがサポートされています。 クライアント側暗号化を使用すると、オンプレミスまたは別のセキュリティで保護された場所で、キーを管理したり格納したりできます。

### <a name="client-side-encryption"></a>クライアント側暗号化

クライアント側暗号化は Azure の外部で実行されます。 クライアント側暗号化には次のものが含まれます。

- ユーザーのデータ センターで実行中のアプリケーション、またはサービス アプリケーションによって暗号化されたデータ
- Azure が受信するときに既に暗号化されているデータ

クライアント側暗号化では、クラウド サービス プロバイダーには暗号化キーへのアクセス権がないため、このデータの暗号化を解除できません。 ユーザーがキーを完全に制御して保持します。

### <a name="server-side-encryption"></a>サーバー側暗号化

3 つのサーバー側暗号化モデルにはそれぞれ異なるキー管理の特徴があり、ユーザーの要件によっていずれかを選択できます。

- **サービスが管理するキー**を使用すれば、オーバーヘッドを軽減しながら、利便性を損なわずに制御できます。

- **ユーザーが管理するキー**を使用すれば、独自のキーを指定する (BYOK)、または新しいキーを生成するなどのキーの制御が可能になります。

- **ユーザーが制御するハードウェア上でサービスが管理するキー**を使用すれば、Microsoft の管理外の独自のリポジトリでキーを管理できます。 これは Host Your Own Key (HYOK) と呼ばれます。 ただし、構成が複雑になるため、ほとんどの Azure サービスではこのモデルはサポートされていません。

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Windows 仮想マシンと Linux 仮想マシンは [Azure Disk Encryption](azure-security-disk-encryption.md) を使用して保護できます。この暗号化サービスは、[Windows BitLocker](https://technet.microsoft.com/library/cc766295(v=ws.10).aspx) テクノロジと Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) を使用してオペレーティング システム ディスクとデータ ディスクの両方をフル ボリューム暗号化で保護します。

暗号化キーとシークレットは、[Azure Key Vault](../key-vault/key-vault-whatis.md) サブスクリプションで保護されます。 Azure Backup サービスを使用して、KEK 構成で暗号化された VM をバックアップしたり復元したりできます。

### <a name="azure-storage-service-encryption"></a>Azure Storage サービスの暗号化

Azure ストレージ (BLOB とファイルの両方) の保存データは、サーバー側とクライアント側の両方のシナリオで暗号化できます。

[Azure Storage Service Encryption](../storage/storage-service-encryption.md) (SSE) によって、データが保存される前にデータを自動で暗号化したり、データを取得するときに自動で暗号化を解除したりすることが可能で、そのプロセスを完全に透過的に処理することができます。 Storage Service Encryption は、現在使用できる最強レベルのブロック暗号である 256 ビットの [AES 暗号化](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)を使用し、暗号化、暗号化の解除、キー管理を透過的に処理します。

### <a name="client-side-encryption-of-azure-blobs"></a>Azure Blob のクライアント側暗号化

Azure Blob のクライアント側暗号化は、さまざまな方法で実行できます。

.NET NuGet パッケージ用の Azure Storage Client Library を使用すると、データを Azure Storage にアップロードする前にクライアント アプリケーション内で暗号化できます。

.NET NuGet パッケージ用の Azure Storage Client Library の詳細とダウンロードについては、「[Windows Azure Storage 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage)」というタイトルのドキュメントをご覧ください。

Azure Key Vault でクライアント側暗号化を使用すると、Azure Storage クライアント SDK によって生成される 1 回限りの対称コンテンツ暗号化キー (CEK) を使用してデータが暗号化されます。 CEK は、対称キーまたは非対称キーのいずれかのペアであるキー暗号化キー (KEK) を使用して暗号化されます。 CEK はローカルで管理するか、Azure Key Vault で格納できます。 その後、暗号化されたデータは Azure Storage サービスにアップロードされます。

Azure Key Vault のクライアント側暗号化の詳細について確認し、手順に従って作業を開始するには、「[Tutorial: Encrypt and decrypt blobs in Microsoft Azure Storage using Azure Key Vault (チュートリアル: Azure Key Vault を使用して Microsoft Azure Storage で BLOB を暗号化および暗号化を解除する)](../storage/storage-encrypt-decrypt-blobs-key-vault.md)」というタイトルのドキュメントをご覧ください。

最後に、Java 用の Azure Storage Client Library を使用して、Azure Storage にデータをアップロードする前にクライアント側暗号化を実行したり、データをクライアントにダウンロードするときに暗号化を解除したりすることもできます。 また、このライブラリは [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) との統合にも役立ち、ストレージ アカウント キーの管理に利用できます。

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Azure SQL データベースを使用した保存データの暗号化

[Azure SQL Database](../sql-database/sql-database-technical-overview.md) は、リレーショナル データ、JSON、空間、XML などの構造をサポートする、Microsoft Azure における汎用リレーショナル データベース サービスです。 Azure SQL では、Transparent Data Encryption (TDE) の機能を使用したサーバー側暗号化と、Always Encrypted 機能を使用したクライアント側暗号化の両方がサポートされています。

#### <a name="transparent-data-encryption"></a>透過的なデータ暗号化

[TDE Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) は、[SQL Server](https://www.microsoft.com/sql-server/sql-server-2016)、[Azure SQL Database](../sql-database/sql-database-technical-overview.md)、[Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) のデータ ファイルをリアルタイムで暗号化するために使用されます。その際、復旧時の可用性のためにデータベースのブート レコードに格納されるデータベース暗号化キー (DEK) が使用されます。

TDE は、AES と 3DES の暗号化アルゴリズムを使用して、データとログ ファイルを保護します。 データベース ファイルの暗号化はページ レベルで実行されます。暗号化されたデータベース内のページは、ディスクに書き込まれる前に暗号化され、メモリに読み込まれるときに暗号化が解除されます。 現在、新しく作成された Azure SQL データベースでは、TDE は既定で有効になっています。

#### <a name="always-encrypted"></a>Always Encrypted

Azure SQL の [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) 機能を使用すれば、データを Azure SQL Database に保存する前にクライアント アプリケーション内で暗号化することができ、オンプレミス データベースの管理をサード パーティに委任したり、データを所有し閲覧できる人と、データは管理するがアクセスする必要はない人とを分離したままにしたりできます。

#### <a name="cellcolumn-level-encryption"></a>セル レベルまたは列レベルの暗号化

Azure SQL Database を使用すれば、Transact-SQL を使用してデータの列に対称暗号化を適用できます。 これは、異なる暗号化キーを使用してデータの特定の列または特定のセルを暗号化するのに使用できることから、[セル レベルの暗号化または列レベルの暗号化](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data) (CLE) と呼ばれます。 これにより、ページのデータを暗号化する TDE よりも詳細な暗号化が可能になります。

CLE には、対称キーまたは非対称キーのいずれかを、証明書の公開キーまたは 3DES を使用したパスフレーズとともに使用してデータを暗号化できる組み込み関数があります。

### <a name="cosmos-db-database-encryption"></a>Cosmos DB データベースの暗号化

[Azure Cosmos DB](../cosmos-db/database-encryption-at-rest.md) は、Microsoft のグローバル分散型マルチモデル データベースです。 Cosmos DB に保存された、不揮発性ストレージ (ソリッド ステート ドライブ) 内のユーザー データは既定で暗号化されます。この設定のオン、オフを切り替えることはできません。 保存時の暗号化は、セキュリティで保護されたキー ストレージ システム、暗号化ネットワーク、暗号化 API など、多くのセキュリティ テクノロジを使用して実装されています。 暗号化キーは Microsoft が管理し、Microsoft の社内ガイドラインに沿って交換されます。

### <a name="at-rest-encryption-in-azure-data-lake"></a>Azure Data Lake での保存されているときの暗号化

[Azure Data Lake](../data-lake-store/data-lake-store-encryption.md) は、要件やスキーマが正式に定義される前に 1 か所に収集されたあらゆる種類のデータの、エンタープライズ規模のリポジトリです。 Azure Data Lake Store では、保存データの透過的な暗号化が ”既定でオン” になっており、アカウント作成時に設定されます。 既定では Data Lake Store がユーザーの代わりにキーを管理しますが、ユーザー管理に切り替えるオプションがあります。

マスター暗号化キー (MEK)、データ暗号化キー (DEK)、ブロック暗号化キー (BEK) という 3 種類のキーが、データの暗号化と暗号化の解除に使用されます。 MEK は永続メディアに格納される DEK の暗号化に使用されます。BEK は DEK から派生したデータ ブロックです。 独自のキーを管理している場合は、MEK を交換できます。

## <a name="encryption-of-data-in-transit"></a>転送中のデータの暗号化

Azure では、データの機密性を維持しながらデータを別の場所に移動するメカニズムを多数ご用意しています。

### <a name="tlsssl-encryption-in-azure"></a>Azure での TLS または SSL 暗号化

Microsoft では、クラウド サービスとユーザーとの間でデータをやりとりする際に、[トランスポート層セキュリティ](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) プロトコルを使用してデータを保護します。 Microsoft のデータ センターは、Azure サービスに接続するクライアント システムとの TLS 接続をネゴシエートします。 TLS には、強力な認証、メッセージの機密性、整合性 (メッセージの改ざん、傍受、偽造の検出が有効)、相互運用性、アルゴリズムの柔軟性、デプロイと使用のしやすさといったメリットがあります。

[Perfect Forward Secrecy](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) は、顧客のクライアント システムと Microsoft のクラウド サービスとの間の接続を一意のキーで保護します。 接続には RSA ベースの 2,048 ビット長の暗号化キーも使用します。 この組み合わせにより、転送中のデータの傍受やデータへのアクセスを困難にしています。

### <a name="azure-storage-transactions"></a>Azure Storage トランザクション

Azure Portal で Azure Storage を操作する際は、すべてのトランザクションが HTTPS 経由で行われます。 HTTPS 経由で Storage REST API を使用して Azure Storage を操作することもできます。 ストレージ アカウントの [安全な転送が必須] を有効にすると、ストレージ アカウント内のオブジェクトにアクセスするための REST API を呼び出す際に HTTPS の使用を強制できます。

Azure Storage オブジェクトへのアクセス権を委任するときに使用できる Shared Access Signature ([SAS](../storage/storage-dotnet-shared-access-signature-part-1.md)) には、Shared Access Signature の使用時に HTTPS プロトコルのみを使用するよう指定できるオプションがあります。 これにより、SAS トークンを指定したリンクを送信するときに誰でも適切なプロトコルを使用できます。

Azure File Shares にアクセスするために使用される [SMB 3.0](https://technet.microsoft.com/library/dn551363(v=ws.11).aspx#BKMK_SMBEncryption) は暗号化をサポートしており、Windows Server 2012 R2、Windows 8、Windows 8.1、Windows 10 で使用できるため、リージョンをまたがるアクセスや、デスクトップ上のアクセスも許可されます。

クライアント側暗号化では、Azure Storage に送信する前にデータを暗号化するため、ネットワーク間を行き交う際にはデータは暗号化されています。

### <a name="smb-encryption-over-azure-virtual-networks"></a>Azure Virtual Network での SMB 暗号化 

Windows Server 2012 以降を実行している Azure VM の [SMB 3.0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) により、Azure Virtual Network 上の転送中データを暗号化して安全に転送し、改ざんや傍受などの攻撃からデータを保護できます。 管理者は、SMB 暗号化をサーバー全体で有効にするか、指定した一部のみで有効にするかを選択できます。

SMB 暗号化をサーバーの一部または全体でオンにすると、既定では暗号化された共有部分にアクセスできるのは 3 つの SMB クライアントのみになります。

## <a name="in-transit-encryption-in-azure-virtual-machines"></a>Azure Virtual Machines での転送中の暗号化

Windows を実行している Azure VM との間でやりとりされる転送中データは、接続の特性に応じてさまざまな方法で暗号化されます。

### <a name="rdp-sessions"></a>RDP セッション

Windows クライアント コンピューターの[リモート デスクトップ プロトコル](https://msdn.microsoft.com/library/aa383015(v=vs.85).aspx) (RDP)、または RDP クライアントがインストールされた Mac を使用して Azure VM に接続し、ログオンできます。 ネットワーク上の RDP セッション内の転送中データは TLS で保護できます。

リモート デスクトップを使用して、Azure の Linux VM に接続することもできます。

### <a name="secure-access-to-linux-vms-with-ssh"></a>SSH を使用して Linux VM に安全にアクセスする

[Secure Shell](../virtual-machines/linux/ssh-from-windows.md) (SSH) を使用して、Azure で起動している Linux VM に接続してリモート管理を行うことができます。 SSH は、セキュリティで保護されていない接続においてセキュリティで保護されたログインを可能にする、暗号化された接続プロトコルです。 SSH は、Azure でホストされる Linux VM の既定の接続プロトコルです。 認証に SSH キーを使用すると、ログイン時にパスワードを入力する必要がなくなります。 SSH では、認証に公開/秘密キー ペア (非対称暗号化) を使用します。

## <a name="azure-vpn-encryption"></a>Azure VPN の暗号化

セキュリティで保護されたトンネルを作成してネットワーク上に送信されるデータのプライバシーを保護する仮想プライベート ネットワークを通じて、Azure に接続できます。

### <a name="azure-vpn-gateway"></a>Azure VPN Gateway

[Azure VPN ゲートウェイ](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)は、仮想ネットワークとオンプレミスの場所との間で暗号化されたトラフィックをパブリック接続を使って送信するために、あるいは仮想ネットワーク間でトラフィックを送信するために使用できます。

サイト間 VPN は、転送の暗号化に [IPsec](https://en.wikipedia.org/wiki/IPsec) を使用します。 Azure VPN ゲートウェイは、既定の提案セットを使用します。 Azure の既定のポリシー セットではなく、特定の暗号アルゴリズムとキーの強度を使用したカスタムの IPsec/IKE ポリシーを使用するように Azure VPN ゲートウェイを構成できます。

### <a name="point-to-site-vpn"></a>ポイント対サイト VPN

ポイント対サイト VPN は、個々のクライアント コンピューターが Azure Virtual Network にアクセスできるようにします。 [Secure Socket トンネリング プロトコル](https://technet.microsoft.com/library/2007.06.cableguy.aspx) (SSTP) は VPN トンネルを作成するために使用され、ファイアウォールを通過できます (トンネルは HTTPS 接続として表示されます)。 ポイント対サイト接続で、自社の内部 PKI ルート CA を使用できます。

Azure Portal を使用して、証明書認証または PowerShell を使用した仮想ネットワークへのポイント対サイト VPN 接続を構成できます。

Azure VNet へのポイント対サイト VPN 接続の詳細については、次のドキュメントをご覧ください。「[証明書認証を使用した VNet へのポイント対サイト接続の構成: Azure Portal](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)」と

「[証明書認証を使用した VNet へのポイント対サイト接続の構成: PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)」

### <a name="site-to-site-vpn"></a>サイト間 VPN 

サイト間 VPN Gateway 接続は、IPsec/IKE (IKEv1 または IKEv2) VPN トンネルを介してオンプレミス ネットワークを Azure 仮想ネットワークに接続するために使用します。 この種類の接続では、外部接続用パブリック IP アドレスが割り当てられていてるオンプレミスの VPN デバイスが必要です。

Azure Portal、PowerShell、または Azure コマンド ライン インターフェイス (CLI) を使用して、仮想ネットワークへのサイト間 VPN 接続を構成できます。

詳細については次のドキュメントをご覧ください。

[Azure Portal でサイト間接続を作成する](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

[サイト間接続を作成する](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[CLI を使用したサイト間 VPN 接続を持つ仮想ネットワークの作成](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-azure-data-lake"></a>Azure Data Lake での転送中の暗号化

Data Lake Store では転送中のデータ (つまり移動中のデータ) も常に暗号化されます。 データは永続メディアに格納される前に暗号化されるうえ、転送中も HTTPS を使用して常にセキュリティ保護されます。 HTTPS は、Data Lake Store REST インターフェイスでサポートされる唯一のプロトコルです。

Azure Data Lake での転送中データの暗号化の詳細については、「[Azure Data Lake Store でのデータの暗号化](../data-lake-store/data-lake-store-encryption.md)」というタイトルのドキュメントをご覧ください。

## <a name="key-management-with-azure-key-vault"></a>Azure Key Vault のキー管理

キーを適切に保護して管理しなければ、暗号化する意味がありません。 Azure Key Vault は、クラウド サービスで使用される暗号化キーの管理とアクセス制御を行う、Microsoft が推奨するソリューションです。 キーへのアクセス権限は、サービスまたはユーザーの Azure Active Directory アカウントに割り当てることができます。

Azure Key Vault を使用すれば、組織はハードウェア セキュリティ モジュール (HSM) とキー管理ソフトウェアの構成、パッチ、メンテナンスを行う必要がありません。 Azure Key Vault を使用いただいている限りは、Microsoft がお客様のキーを確認したり、アプリケーションがそれらに直接アクセスすることもないため、完全にお客様によるキーの制御が可能になります。 キーを HSM にインポートしたり生成したりすることもできます。

## <a name="next-steps"></a>次のステップ

- [Azure セキュリティの概要](security-get-started-overview.md)
- [Azure のネットワーク セキュリティの概要](security-network-overview.md)
- [Azure のデータベース セキュリティの概要](azure-database-security-overview.md)
- [Azure 仮想マシンのセキュリティの概要](security-virtual-machines-overview.md)
- [保存データの暗号化](azure-security-encryption-atrest.md)
- [データ セキュリティと暗号化のベスト プラクティス](azure-security-data-encryption-best-practices.md)

