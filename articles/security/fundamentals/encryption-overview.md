---
title: Azure の暗号化の概要 | Microsoft Docs
description: Azure での暗号化オプションについて説明します。 保存データの暗号化、転送中のデータの暗号化、Azure Key Vault を使用したキー管理についての情報をご確認ください。
services: security
author: msmbaldwin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: d7d438b369c863660a032f101e466b6fadf639fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98879716"
---
# <a name="azure-encryption-overview"></a>Azure の暗号化の概要

この記事では、Microsoft Azure の暗号化の概要について説明します。 保存データの暗号化、転送中のデータの暗号化、Azure Key Vault を使用したキー管理など、暗号化の主な項目について説明します。 各セクションには、より詳細な情報へのリンクが含まれています。

## <a name="encryption-of-data-at-rest"></a>保存データの暗号化

保存データには、物理メディア上の永続的ストレージに存在する、あらゆるデジタル形式の情報が含まれます。 これには、磁気メディアまたは光学メディア上のファイル、アーカイブされたデータ、データのバックアップなどが含まれます。 Microsoft Azure では、さまざまなニーズに応えるために、ファイル、ディスク、BLOB、テーブル ストレージなど、多様なデータ ストレージ ソリューションをご用意しています。 Microsoft は、[Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md)、[Azure Cosmos DB](../../data-factory/introduction.md)、Azure Data Lake を保護する暗号化の機能も提供しています。

保存されているデータの暗号化は、サービスとしてのソフトウェア (SaaS)、サービスとしてのプラットフォーム (PaaS)、サービスとしてのインフラストラクチャ (IaaS) の各クラウド モデルのサービスで使用できます。 この記事では、Azure の暗号化オプションを使用する際に役立つリソースを提供し、その概要について説明します。

Azure で保存データを暗号化する方法について詳しくは、「[Azure Data Encryption-at-Rest](encryption-atrest.md)」をご覧ください。

## <a name="azure-encryption-models"></a>Azure の暗号化モデル

Azure では、サービスが管理するキー、Key Vault でユーザーが管理するキー、またはユーザーが制御するハードウェア上でユーザーが管理するキーを使用したサーバー側暗号化など、さまざまな暗号化モデルがサポートされています。 クライアント側暗号化を使用すると、オンプレミスまたは別のセキュリティで保護された場所で、キーを管理したり格納したりできます。

### <a name="client-side-encryption"></a>クライアント側暗号化

クライアント側暗号化は Azure の外部で実行されます。 次の情報が含まれます。

- ユーザーのデータ センターで実行中のアプリケーション、またはサービス アプリケーションによって暗号化されたデータ
- Azure が受信するときに既に暗号化されているデータ

クライアント側暗号化では、クラウド サービス プロバイダーには暗号化キーへのアクセス権がないため、このデータの暗号化を解除できません。 ユーザーがキーを完全に制御して保持します。

### <a name="server-side-encryption"></a>サーバー側暗号化

3 つのサーバー側暗号化モデルにはそれぞれ異なるキー管理の特徴があり、ユーザーの要件によっていずれかを選択できます。

- **サービスが管理するキー**: オーバーヘッドを軽減しながら、利便性を損なわずに制御できます。

- **ユーザーが管理するキー**: Bring Your Own Key (BYOK) などでキーを制御したり、新しいキーを生成したりできます。

- **ユーザーが制御するハードウェア上でサービスが管理するキー**: Microsoft の管理外の独自のリポジトリでキーを管理できます。 このような特徴は Host Your Own Key (HYOK) と呼ばれます。 ただし、構成が複雑になるため、ほとんどの Azure サービスではこのモデルはサポートされていません。

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Windows 仮想マシンと Linux 仮想マシンは [Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md) を使用して保護できます。この暗号化サービスは、[Windows BitLocker](/previous-versions/windows/it-pro/windows-vista/cc766295(v=ws.10)) テクノロジと Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) を使用してオペレーティング システム ディスクとデータ ディスクの両方をフル ボリューム暗号化で保護します。

暗号化キーとシークレットは、[Azure Key Vault](../../key-vault/general/overview.md) サブスクリプションで保護されます。 Azure Backup サービスを使うことにより、キー暗号化キー (KEK) 構成を使う暗号化された仮想マシン (VM) をバックアップしたり復元したりできます。

### <a name="azure-storage-service-encryption"></a>Azure Storage Service Encryption

Azure Blob Storage および Azure ファイル共有の保存データは、サーバー側とクライアント側の両方のシナリオで暗号化できます。

[Azure Storage Service Encryption](../../storage/common/storage-service-encryption.md) (SSE) によって、データが保存される前にデータを自動で暗号化したり、データを取得するときに自動で暗号化を解除したりすることができます このプロセスは、ユーザーからは見えないところで実行されます。 Storage Service Encryption では 256 ビットの [Advanced Encryption Standard (AES) 暗号化](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) が使われます。これは、使用可能な最も強力なブロック暗号の 1 つです。 AES は、暗号化、復号化、キー管理を透過的に処理します。

### <a name="client-side-encryption-of-azure-blobs"></a>Azure Blob のクライアント側暗号化

Azure BLOB のクライアント側暗号化は、さまざまな方法で実行できます。

.NET NuGet パッケージ用の Azure Storage Client Library を使用すると、データを Azure Storage にアップロードする前にクライアント アプリケーション内で暗号化できます。

.NET NuGet パッケージ用の Azure Storage Client Library の詳細とダウンロードについては、「[Windows Azure Storage 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage)」をご覧ください。

Key Vault でクライアント側暗号化を使用すると、Azure Storage クライアント SDK によって生成される 1 回限りの対称コンテンツ暗号化キー (CEK) を使用してデータが暗号化されます。 CEK は、対称キーまたは非対称キーのいずれかのペアであるキー暗号化キー (KEK) を使用して暗号化されます。 CEK はローカルで管理するか、Key Vault で格納できます。 その後、暗号化されたデータは Azure Storage にアップロードされます。

Key Vault のクライアント側暗号化の詳細について確認し、手順に従って作業を開始するには、[チュートリアル: Key Vault を使用した Azure Storage 内の BLOB の暗号化と復号化](../../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md)に関する記事をご覧ください。

最後に、Java 用の Azure Storage Client Library を使用して、Azure Storage にデータをアップロードする前にクライアント側暗号化を実行したり、データをクライアントにダウンロードするときに暗号化を解除したりすることもできます。 また、このライブラリは [Key Vault](https://azure.microsoft.com/services/key-vault/) との統合にも役立ち、ストレージ アカウント キーの管理に利用できます。

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Azure SQL Database を使用した保存データの暗号化

[Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md) は、リレーショナル データ、JSON、空間、XML などの構造をサポートする、Azure における汎用リレーショナル データベース サービスです。 SQL Database では、Transparent Data Encryption (TDE) の機能を使用したサーバー側暗号化と、Always Encrypted 機能を使用したクライアント側暗号化の両方がサポートされています。

#### <a name="transparent-data-encryption"></a>透過的なデータ暗号化

[TDE](/sql/relational-databases/security/encryption/transparent-data-encryption-tde) は、[SQL Server](https://www.microsoft.com/sql-server/sql-server-2016)、[Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md)、[Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) のデータ ファイルをリアルタイムで暗号化するために使用されます。その際、復旧時の可用性のためにデータベースのブート レコードに格納されるデータベース暗号化キー (DEK) が使用されます。

TDE は、AES と Triple Data Encryption Standard (3DES) の暗号化アルゴリズムを使用して、データとログ ファイルを保護します。 データベース ファイルの暗号化は、ページ レベルで実行されます。 暗号化されたデータベース内のページは、ディスクに書き込まれる前に暗号化され、メモリに読み込まれるときに暗号化が解除されます。 現在、新しく作成された Azure SQL データベースでは、TDE は既定で有効になっています。

#### <a name="always-encrypted-feature"></a>Always Encrypted 機能

Azure SQL の [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) 機能を使うと、クライアント アプリケーション内のデータを、Azure SQL Database に格納される前に暗号化することができます。 また、オンプレミス データベースの管理をサード パーティに委任することができ、データを所有して見ることができるユーザーと、データを管理するがデータにアクセスする必要のないユーザーの分離を維持できます。

#### <a name="cell-level-or-column-level-encryption"></a>セル レベルまたは列レベルの暗号化

Azure SQL Database では、Transact-SQL を使用してデータの列に対称暗号化を適用できます。 この方法は、異なる暗号化キーを使用してデータの特定の列または特定のセルを暗号化するのに使用できることから、[セル レベルの暗号化または列レベルの暗号化](/sql/relational-databases/security/encryption/encrypt-a-column-of-data) (CLE) と呼ばれます。 これにより、ページのデータを暗号化する TDE よりも詳細な暗号化が可能になります。

CLE には、対称キーまたは非対称キーのいずれかを、証明書の公開キーまたは 3DES を使用したパスフレーズとともに使用してデータを暗号化できる組み込み関数があります。

### <a name="cosmos-db-database-encryption"></a>Cosmos DB データベースの暗号化

[Azure Cosmos DB](../../cosmos-db/database-encryption-at-rest.md) は、Microsoft のグローバル分散型マルチモデル データベースです。 Cosmos DB に保存された、不揮発性ストレージ (ソリッド ステート ドライブ) 内のユーザー データは既定で暗号化されます。 この設定のオン、オフを切り替えることはできません。 保存時の暗号化は、セキュリティで保護されたキー ストレージ システム、暗号化ネットワーク、暗号化 API など、多くのセキュリティ テクノロジを使用して実装されています。 暗号化キーは Microsoft が管理し、Microsoft の社内ガイドラインに沿って交換されます。

### <a name="at-rest-encryption-in-data-lake"></a>Data Lake での保存されているときの暗号化

[Azure Data Lake](../../data-lake-store/data-lake-store-encryption.md) は、要件やスキーマが正式に定義される前に 1 か所に収集されたあらゆる種類のデータの、エンタープライズ規模のリポジトリです。 Data Lake Store では、保存データの透過的な暗号化が ”既定でオン” になっており、アカウント作成時に設定されます。 既定では Azure Data Lake Store がユーザーの代わりにキーを管理しますが、ユーザー管理に切り替えるオプションがあります。

マスター暗号化キー (MEK)、データ暗号化キー (DEK)、ブロック暗号化キー (BEK) という 3 種類のキーが、データの暗号化と暗号化の解除に使用されます。 MEK は永続メディアに格納される DEK の暗号化に使用されます。BEK は DEK から派生したデータ ブロックです。 独自のキーを管理している場合は、MEK を交換できます。

## <a name="encryption-of-data-in-transit"></a>転送中のデータの暗号化

Azure では、データの機密性を維持しながらデータを別の場所に移動するメカニズムを多数ご用意しています。

### <a name="data-link-layer-encryption-in-azure"></a>Azure のデータリンク層の暗号化

Azure のお客様のトラフィックが、Microsoft (または Microsoft の代理) によって制御されない物理的境界の外で、データセンター間を移動するときは常に、基礎となるネットワーク ハードウェアから [IEEE 802.1AE MAC Security Standards](https://1.ieee802.org/security/802-1ae/) (別名 MACsec) を使用したデータリンク層の暗号化が適用されます。 パケットは送信前にデバイス上で暗号化され、復号され、物理的な "中間者" 攻撃、スヌーピング攻撃、盗聴攻撃を防ぎます。 このテクノロジはネットワーク ハードウェア自体に統合されているため、測定可能なリンクの待機時間を増やすことなく、ネットワーク ハードウェア上でライン レート暗号化を提供します。 この MACsec 暗号化は、リージョン内またはリージョン間を移動するすべての Azure トラフィックに対して、既定でオンになります。お客様側で必要な操作はありません。 

### <a name="tls-encryption-in-azure"></a>Azure の TLS 暗号化

Microsoft を利用する顧客は、クラウド サービスと顧客の間をデータが移動するとき、[トランスポート層セキュリティ](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) プロトコルを使用してデータを保護できます。 Microsoft のデータ センターは、Azure サービスに接続するクライアント システムとの TLS 接続をネゴシエートします。 TLS には、強力な認証、メッセージの機密性、整合性 (メッセージの改ざん、傍受、偽造の検出が有効)、相互運用性、アルゴリズムの柔軟性、デプロイと使用のしやすさといったメリットがあります。

[Perfect Forward Secrecy](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) は、顧客のクライアント システムと Microsoft クラウド サービスとの間の接続を一意のキーで保護します。 接続には RSA ベースの 2,048 ビット長の暗号化キーも使用します。 この組み合わせにより、転送中のデータの傍受やデータへのアクセスを困難にしています。

### <a name="azure-storage-transactions"></a>Azure Storage トランザクション

Azure Portal で Azure Storage を操作する際は、すべてのトランザクションが HTTPS 経由で行われます。 HTTPS 経由で Storage REST API を使用して Azure Storage を操作することもできます。 ストレージ アカウントに対して必要な安全な転送を有効にすると、ストレージ アカウント内のオブジェクトにアクセスするための REST API を呼び出す際に HTTPS の使用を強制できます。

Azure Storage オブジェクトへのアクセス権を委任するときに使用できる Shared Access Signature ([SAS](../../storage/common/storage-sas-overview.md)) には、Shared Access Signature の使用時に HTTPS プロトコルのみを使用するよう指定できるオプションがあります。 この方法により、SAS トークンを指定したリンクを送信するときに誰でも適切なプロトコルを使用できます。

Azure File 共有にアクセスするために使用される [SMB 3.0](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn551363(v=ws.11)#BKMK_SMBEncryption) は暗号化をサポートしており、Windows Server 2012 R2、Windows 8、Windows 8.1、Windows 10 で使用できます。 リージョンをまたがるアクセスや、デスクトップ上のアクセスも許可されます。

クライアント側暗号化では、Azure Storage インスタンスに送信する前にデータを暗号化するため、ネットワーク間を行き交う際にはデータは暗号化されています。

### <a name="smb-encryption-over-azure-virtual-networks"></a>Azure 仮想ネットワークでの SMB 暗号化 

Windows Server 2012 以降を実行する VM で [SMB 3.0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) を使うことにより、Azure 仮想ネットワークを転送中のデータを暗号化して、データの転送を安全にできます。 データを暗号化すると、改ざんや傍受攻撃から保護することができます。 管理者は、SMB 暗号化をサーバー全体で有効にするか、指定した一部のみで有効にするかを選択できます。

SMB 暗号化をサーバーの一部または全体でオンにすると、既定では暗号化された共有部分にアクセスできるのは SMB 3.0 クライアントのみになります。

## <a name="in-transit-encryption-in-vms"></a>VM での転送中の暗号化

Windows を実行している VM との間でやり取りされる転送中データは、接続の特性に応じてさまざまな方法で暗号化できます。

### <a name="rdp-sessions"></a>RDP セッション

Windows クライアント コンピューターの[リモート デスクトップ プロトコル](/windows/win32/termserv/remote-desktop-protocol) (RDP)、または RDP クライアントがインストールされた Mac を使用して、VM に接続し、サインインすることができます。 ネットワーク上の RDP セッション内の転送中データは TLS で保護できます。

リモート デスクトップを使用して、Azure の Linux VM に接続することもできます。

### <a name="secure-access-to-linux-vms-with-ssh"></a>SSH を使用して Linux VM に安全にアクセスする

[Secure Shell](../../virtual-machines/linux/ssh-from-windows.md) (SSH) を使用して、Azure で起動している Linux VM に接続してリモート管理を行うことができます。 SSH は、セキュリティで保護されていない接続においてセキュリティで保護されたサインインを可能にする、暗号化された接続プロトコルです。 SSH は、Azure でホストされる Linux VM の既定の接続プロトコルです。 認証に SSH キーを使用すると、サインイン時にパスワードを入力する必要がなくなります。 SSH では、認証に公開/秘密キー ペア (非対称暗号化) を使用します。

## <a name="azure-vpn-encryption"></a>Azure VPN の暗号化

セキュリティで保護されたトンネルを作成してネットワーク上に送信されるデータのプライバシーを保護する仮想プライベート ネットワークを通じて、Azure に接続できます。

### <a name="azure-vpn-gateways"></a>Azure VPN Gateway

[Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) を使い、仮想ネットワークとオンプレミスの場所との間で暗号化されたトラフィックをパブリック接続を使って送信したり、仮想ネットワーク間でトラフィックを送信したりすることができます。

サイト間 VPN は、転送の暗号化に [IPsec](https://en.wikipedia.org/wiki/IPsec) を使用します。 Azure VPN ゲートウェイは、既定の提案セットを使用します。 Azure の既定のポリシー セットではなく、特定の暗号アルゴリズムとキーの強度を使用したカスタムの IPsec/IKE ポリシーを使用するように Azure VPN ゲートウェイを構成できます。

### <a name="point-to-site-vpns"></a>ポイント対サイト VPN

ポイント対サイト VPN は、個々のクライアント コンピューターが Azure 仮想ネットワークにアクセスできるようにします。 VPN トンネルを作成するには、[Secure Socket Tunneling Protocol (SSTP)](/previous-versions/technet-magazine/cc162322(v=msdn.10)) が使われます。 VPN トンネルはファイアウォールを横断できます (トンネルは HTTPS 接続として認識されます)。 独自の内部公開キー基盤 (PKI) ルート証明機関 (CA) をポイント対サイト接続に使用できます。

Azure Portal を使用して、証明書認証または PowerShell を使用した仮想ネットワークへのポイント対サイト VPN 接続を構成できます。

Azure 仮想ネットワークへのポイント対サイト VPN 接続に関して詳しくは、以下をご覧ください。

[証明書認証を使用した仮想ネットワークへのポイント対サイト接続の構成: Azure portal](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) 

[証明書認証を使用した仮想ネットワークへのポイント対サイト接続の構成: PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpns"></a>サイト間 VPN 

サイト間 VPN ゲートウェイ接続は、IPsec/IKE (IKEv1 または IKEv2) VPN トンネルを介してオンプレミス ネットワークを Azure 仮想ネットワークに接続するために使用します。 この種類の接続では、外部接続用パブリック IP アドレスが割り当てられていてるオンプレミスの VPN デバイスが必要です。

Azure Portal、PowerShell、または Azure CLI を使用して、仮想ネットワークへのサイト間 VPN 接続を構成できます。

詳細については、次を参照してください。

[Azure Portal でサイト間接続を作成する](../../vpn-gateway/tutorial-site-to-site-portal.md)

[PowerShell でサイト間接続を作成する](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[CLI を使用したサイト間 VPN 接続を持つ仮想ネットワークの作成](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-data-lake"></a>Data Lake での転送中の暗号化

Data Lake Store では転送中のデータ (つまり移動中のデータ) も常に暗号化されます。 データは永続メディアに格納される前に暗号化されるうえ、転送中も HTTPS を使用して常にセキュリティ保護されます。 HTTPS は、Data Lake Store REST インターフェイスでサポートされる唯一のプロトコルです。

Data Lake での転送中データの暗号化の詳細については、「[Azure Data Lake Store でのデータの暗号化](../../data-lake-store/data-lake-store-encryption.md)」をご覧ください。

## <a name="key-management-with-key-vault"></a>Key Vault のキー管理

キーを適切に保護して管理しなければ、暗号化する意味がありません。 Key Vault は、クラウド サービスで使用される暗号化キーの管理とアクセス制御を行う、Microsoft が推奨するソリューションです。 キーへのアクセス権限は、サービスまたはユーザーの Azure Active Directory アカウントに割り当てることができます。

Key Vault を使用すれば、組織はハードウェア セキュリティ モジュール (HSM) とキー管理ソフトウェアの構成、パッチ、メンテナンスを行う必要がありません。 Key Vault を使うと、制御を維持できます。 Microsoft がお客様のキーを確認したり、アプリケーションがお客様のキーに直接アクセスしたりすることはありません。 キーを HSM にインポートしたり生成したりすることもできます。

## <a name="next-steps"></a>次のステップ

- [Azure セキュリティの概要](./overview.md)
- [Azure のネットワーク セキュリティの概要](network-overview.md)
- [Azure のデータベース セキュリティの概要](../../azure-sql/database/security-overview.md)
- [Azure 仮想マシンのセキュリティの概要](virtual-machines-overview.md)
- [保存データの暗号化](encryption-atrest.md)
- [データ セキュリティと暗号化のベスト プラクティス](data-encryption-best-practices.md)