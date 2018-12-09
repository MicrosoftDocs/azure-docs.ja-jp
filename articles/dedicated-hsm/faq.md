---
title: Azure 専用 HSM のよくあるご質問 | Microsoft Docs
description: Azure の専用 HSM は、FIPS 140-2 レベル 3 認定を満たしたキーの保管機能を Azure 内で実現します
services: dedicated-hsm
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: concepts
ms.custom: mvc
ms.date: 11/19/2018
ms.author: barclayn
ms.openlocfilehash: 3179fd3cf7a595b4491aec77e221aca3960c8ecb
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/26/2018
ms.locfileid: "52319041"
---
# <a name="frequently-asked-questions-faq"></a>よく寄せられる質問 (FAQ)

Microsoft Azure 専用 HSM についてよく寄せられる質問への回答を示します。

## <a name="the-basics"></a>基本操作

### <a name="q-what-is-a-hardware-security-module-hsm"></a>Q: ハードウェア セキュリティ モジュール (HSM) とは何ですか。

ハードウェア セキュリティ モジュール (HSM) は、暗号化キーの保護と管理に使用される物理コンピューティング デバイスです。 HSM に格納されたキーは、暗号操作に使用できます。 キー マテリアルは、改ざんの防止および改ざんの証明対策が取られたハードウェア モジュール内に、安全に保管されます。 HSM では、認証済みおよび承認済みのアプリケーション以外には、キーの使用を許可しません。 キー マテリアルが HSM による保護の境界の外に出ることは決してありません。

### <a name="q-what-is-azure-dedicated-hsm-offering"></a>Q: Azure 専用 HSM オファリングとは何ですか。

Azure 専用 HSMはクラウドベースのサービスであり、お客様の仮想ネットワークに直接接続された Azure データセンター内でホストされる HSM を提供します。 これらの HSM は、専用のネットワーク アプライアンス (Gemalto の SafeNet ネットワーク HSM 7 モデル A790) です。 お客様のプライベート IP アドレス空間に直接展開され、Microsoft には HSM の暗号化機能へのアクセス権はありません。 お客様のみが、これらのデバイスの完全な管理と暗号化の制御を行います。 お客様がデバイスの管理を担当して、ご自身のデバイスから直接、完全なアクティビティ ログを取得することが可能です。 専用 HSM は、お客様が FIPS 140-2 レベル 3、HIPAA、PCI-DSS、eIDAS、およびその他多くのコンプライアンスや規制要件を満たすのに役立ちます。

## <a name="what-hardware-is-used-for-dedicated-hsm"></a>専用 HSM には、どのようなハードウェアが使用されますか?

Azure 専用 HSM サービスを提供するために、Microsoft は Gemalto と提携しています。 使用される特定のデバイスは、[SafeNet Luna ネットワーク HSM 7 モデル A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/) です。 このデバイスでは、FIPS 140-2 レベル 3 の検証済みファームウェアを提供しているだけでなく、10 個のパーティションを使って低待機時間、高パフォーマンス、および高容量も実現しています。 

### <a name="q-what-is-an-hsm-used-for"></a>Q: HSM の使用目的は何ですか。

HSM を使用する目的は、SSL (Secure Socket Layer)、データの暗号化、PKI (公開キー基盤)、DRM (デジタル著作権管理)、ドキュメントの署名などの暗号化機能で使用される暗号化キーを格納することです。

### <a name="q-how-does-dedicated-hsm-work"></a>Q: 専用 HSM はどのように機能しますか。

お客様は、PowerShell またはコマンド ライン インターフェイスを使用して、特定のリージョンに HSM をプロビジョニングできます。 お客様は、どの仮想ネットワークに HSM を接続するかを指定し、プロビジョニングされた HSM は、お客様のプライベート IP アドレス空間内の割り当てられた IP アドレスにおいて、指定されたサブネット内で利用可能になります。 次に、お客様はアプライアンス管理用の SSH を使用して HSM に接続し、HSM クライアント接続の設定、HSM の初期化、パーティションの作成、ロール (パーティション担当者、暗号化担当者、暗号化ユーザーなど) の定義と割り当てを行うことができます。 その後、お客様は、Gemalto が提供する HSM クライアント ツール/SDK/ソフトウェアを使用して、アプリケーションから暗号操作を実行します。

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>Q: 専用 HSM サービスでは、どのようなソフトウェアが提供されますか。

Microsoft によってプロビジョニングされると、Gemalto から HSM デバイスのすべてのソフトウェアが提供されます。 ソフトウェアは、[Gemalto Customer Support Portal](https://supportportal.gemalto.com/csm/) で入手できます。 専用 HSM サービスを使用しているお客様は、Gemalto サポートへの登録が求められ、関連するソフトウェアへのアクセスとダウンロードが可能な顧客 ID を保有します。 サポートされるクライアント ソフトウェアはバージョン 7.2 であり、FIPS 140-2 レベル 3 の検証済みファームウェア バージョン 7.0.3 と互換性があります。 

## <a name="interoperability"></a>相互運用性

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>Q: 自分のアプリケーションから専用 HSM へは、どのように接続すればよいですか。

お客様は、Gemalto が提供する HSM クライアント ツール/SDK/ソフトウェアを使用して、お使いのアプリケーションから暗号操作を実行します。 ソフトウェアは、[Gemalto Customer Support Portal](https://supportportal.gemalto.com/csm/) で入手できます。 専用 HSM サービスを使用しているお客様は、Gemalto サポートへの登録が求められ、関連するソフトウェアへのアクセスとダウンロードが可能な顧客 ID を保有します。

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>Q: アプリケーションは、リージョン内またはリージョン間の異なる VNET から専用 HSM に接続できますか。

はい。リージョン内では [VNET ピアリング](../virtual-network/virtual-network-peering-overview.md)を使用して、仮想ネットワーク間に接続を確立する必要があります。 リージョン間の接続には、[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) を使用する必要があります。

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>Q: 専用 HSM をオンプレミス HSM と同期することはできますか。

はい。専用 HSM とオンプレミス HSM を同期することが可能です。 オンプレミス ネットワークとの接続を確立するために、[ポイント ツー ポイント VPN またはポイント対サイト](../vpn-gateway/vpn-gateway-about-vpngateways.md)接続を使用できます。

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>Q: 専用 HSM に格納されたキーを使って、他の Azure サービスから使用されるデータを暗号化することはできますか。

いいえ。 Azure 専用 HSM は、お使いの仮想ネットワーク内からのみアクセス可能です。

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>Q: 既存のオンプレミス HSM から専用 HSM へキーをインポートすることはできますか。

はい。オンプレミス Gemalto SafeNet HSM をお持ちの場合は可能です。 複数の方法があります。 Gemalto HSM のドキュメントを参照してください。

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>Q: 専用 HSM クライアント ソフトウェアでは、どのようなオぺレーティング システムがサポートされますか。

* Windows、Linux、Solaris、AIX、HP-UX、FreeBSD
* 仮想: VMware、hyperv、Xen、KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>Q: 複数の HSM から複数のパーティションを使って高可用性の構成を作成するには、自分のクライアント アプリケーションをどのように構成すればよいですか。

高可用性を得るには、各 HSM からのパーティションを使用するように、HSM クライアント アプリケーションの構成を設定する必要があります。 Gemalto の HSM クライアント ソフトウェアに関するドキュメントを参照してください。

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>Q: 専用 HSM では、どのような認証メカニズムがサポートされますか。

Azure 専用 HSM では SafeNet ネットワーク HSM 7 アプライアンス (モデル A790) を使用すると共に、パスワードベースの認証をサポートしています。

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>Q: 専用 HSM と共に使用するには、どの SDK、API、クライアント ソフトウェアが利用可能ですか。

PKCS#11、Java (JCA/JCE)、Microsoft CAPI、CNG、OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>Q: Luna 5/6 HSM から Azure 専用 HSM へキーをインポートまたは移行できますか。

はい。 Gemalto の移行ガイドを参照してください。 

## <a name="using-your-hsm"></a>ご自身の HSM を使用する

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>Q: Azure Key Vault または Azure 専用 HSM のどちらを使用するかを、どのように決めればよいですか。

HSM を使用する Azure オンプレミス アプリケーションに移行している企業では、Azure 専用 HSM が適切な選択です。 専用 HSM は、変更を最小限に抑えてアプリケーションを移行するオプションを提供します。 Azure VM または Web App で実行されるアプリケーションのコード内で、暗号操作が行われている場合は、専用 HSM を使用できます。 一般に、HSM をキー ストアとしてサポートし、IaaS (サービスとしてのインフラストラクチャ) モデルで実行される市販のソフトウェアでは、専用 HSM を使用できます。キーのない SSL に対するアプリケーション ゲートウェイまたはトラフィック マネージャー、ADCS (Active Directory 証明書サービス)、類似の PKI ツール、ドキュメント署名に使用されるツール/アプリケーション、コード署名、EKM (拡張可能キー管理) プロバイダーを使用する HSM においてマスター キーを使った TDE (Transparent Database Encryption) によって構成されている SQL Server (IaaS) などがあります。 "クラウド内で構築された" アプリケーションや、お客様のデータが PaaS (platform as a service) または SaaS (Software as a service) シナリオで処理される暗号保存のシナリオでは、Azure Key Vault が適切です。このシナリオには、Office 365 カスタマー キー、Azure Information Protection、Azure Disk Encryption、顧客管理キーを使用した Azure Data Lake Store 暗号化、顧客管理キーを使用した Azure Storage 暗号化、および顧客管理キーを使用した Azure SQL などがあります。

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>Q: Azure 専用 HSM には、どのような使用シナリオが最適ですか。

Azure 専用 HSM は、移行のシナリオに最適です。 つまり、既に HSM を使用している Azure にオンプレミス アプリケーションを移行する場合です。 これにより、アプリケーションへの変更を最小限に抑えて Azure に移行するという、問題が生じにくいオプションが提供されます。 Azure VM または Web App で実行されるアプリケーションのコード内で暗号操作が行われている場合、専用 HSM を使用できます。 一般に、HSM をキー ストアとしてサポートし、IaaS (サービスとしてのインフラストラクチャ) モデルで実行される市販のソフトウェアでは、専用 HSM を使用できます。次のような例があります。

* キーのない SSL に対するアプリケーション ゲートウェイまたはトラフィック マネージャー
* ADCS (Active Directory 証明書サービス)
* 類似の PKI ツール
* ドキュメント署名に使用されるツール/アプリケーション
* コード署名
* EKM (拡張可能キー管理) プロバイダーを使用する HSM において、マスター キーを使った TDE (Transparent Database Encryption) によって構成されている SQL Server (IaaS)

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>Q: 専用 HSM は、Office 365 カスタマー キー、Azure Information Protection、Azure Data Lake Store、Disk Encryption、Azure Storage 暗号化、Azure SQL TDE と共に使用できますか。

いいえ。 専用 HSM は、お客様のプライベート IP アドレス空間に直接プロビジョニングされるため、他の Azure または Microsoft サービスからアクセスすることはできません。

## <a name="administration-access-and-control"></a>管理、アクセス、および制御

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-hsms"></a>Q: ユーザーは専用 HSM を使って、HSM による完全な排他的制御を得ることはできますか。

はい。 各 HSM アプライアンスは完全に単一のお客様専用であり、一度プロビジョニングされて管理者パスワードが変更されると、他のユーザーには管理制御権がありません。

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>Q: Microsoft はユーザーの HSM に対して、どのようなアクセス レベルを保持していますか。

Microsoft は HSM に対して、管理制御または暗号制御を行うことはできません。 Microsoft では、温度やコンポーネントの正常性などの基本のテレメトリを取得するために、シリアル ポート接続経由で監視レベルのアクセス権を保持しています。 これにより、Microsoft では、正常性の問題に関する事前通知を提供しています。 必要に応じて、お客様はこのアカウントを無効にできます。

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>Q: Microsoft または Microsoft の担当者は、ユーザーの専用 HSM 内のキーにアクセスできますか。

いいえ。 お客様に割り当てられている専用 HSM に格納されたキーに対しては、Microsoft はアクセス権を保持しません。

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>Q: 自分に割り当てられている HSM 上のソフトウェア/ファームウェアをアップグレードしてもよいですか。

最善のサポートを受けるために、HSM 上のソフトウェア/ファームウェアをアップグレードしないことを、Microsoft では強くお勧めしています。 ただし、別のファームウェア バージョンの特定の機能が必要な場合、お客様には、ソフトウェア/ファームウェアのアップグレードを含めた完全な管理制御権があります。 変更を加える前に、これにより FIPS 検証済みステータスが影響を受ける可能性があることなど、影響範囲を理解しておく必要があります。 

### <a name="q-how-do-i-manage-dedicated-hsm"></a>専用 HSM はどのように管理すればよいですか。

SSH を使ってアクセスすることで、専用 HSM を管理できます。

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>Q: 専用 HSM 上では、パーティションをどのように管理すればよいですか。

HSM およびパーティションの管理には、Gemalto のHSM クライアント ソフトウェアが使用されます。

### <a name="q-how-do-i-monitor-my-hsm"></a>Q: 自分の HSM を監視するにはどうしたらよいですか。

お客様は、syslog および SNMP 経由で HSM アクティビテ ログへのフル アクセス権を保持しています。 HSM からログまたはイベントを受信するには、お客様が syslog サーバーまたは SNMP サーバーをセットアップする必要があります。

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>Q: 専用 HSM からすべての HSM 操作のフル アクセス ログを取得することはできますか。

はい。 HSM アプライアンスから syslog サーバーにログを送信することができます。

## <a name="high-availability"></a>高可用性

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>Q: 同じリージョン内または複数のリージョン間で、高可用性を構成することは可能ですか。

はい。 高可用性の構成とセットアップは、Gemalto が提供する HSM クライアント ソフトウェアで実行されます。 同一リージョン内またはリージョン間での同じ VNET または別の VNET からの HSM や、サイト対サイトまたはポイント ツー ポイントの VPN を使用して VNET に接続されているオンプレミス HSM は、同じ高可用性の構成に追加できます。

### <a name="can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>自分のオンプレミス ネットワークから Azure 専用 HSM を使った高可用性グループに、HSM を追加することはできますか。

はい。 HSM が SafeNet Luna ネットワーク HSM 7 の高可用性の要件を満たしている必要があります。

### <a name="can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>オンプレミス ネットワークから Azure 専用 HSM を使った高可用性グループに、Luna 5/6 HSM を追加することはできますか。

いいえ。

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>Q: 単一アプリケーションから同じ高可用性の構成に追加できる HSM の数はいくつですか。

16.

## <a name="support"></a>サポート

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>Q: 専用 HSM サービスの SLA は何ですか。

現時点では、専用 HSM サービスに対して提供されている SLA はありません。 Microsoft では、デバイスへのネットワーク レベルのアクセスを保証しているため、標準の Azure ネットワーク SLA が適用されます。

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>Q: Azure 専用 HSM に使用されている HSM は、どのように保護されていますか。

Azure データセンターにおいて、物理上および手続き上のセキュリティ制御が詳細に行わています。 さらに、専用 HSM は、データセンター内でもアクセスがより制限されたエリアでホストされています。 これらのエリアでは、セキュリティ強化のために詳細な物理アクセス制御やビデオ カメラによる監視が行われています。

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>Q: セキュリティ違反やハードウェア改ざんの事案が発生した場合は、どうなりますか。

専用 HSM サービスでは、SafeNet ネットワーク HSM 7 アプライアンスを使用しています。 これらのアプライアンスでは、物理的および論理的な改ざんの検出をサポートしています。 改ざんが行われた場合、HSM は自動的にゼロ埋めされます。

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>Q: エラーや内部関係者による悪意の攻撃によって自分の専用 HSM 内のキーが損なわれていないことを確かめるには、どうしたらよいですか。

オンプレミス HSM バックアップ デバイスを使用して、ディザスター リカバリーのために HSM の日常的な定期バックアップを実行することを強くお勧めします。 HSM バックアップ デバイスに接続されているオンプレミス ワークステーションに対しては、ピアツーピアまたはサイト間のVPN 接続を使用する必要があります。

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>Q: 専用 HSM のサポートを受けるにはどうすればよいですか。

他のすべての Azure サービスのサポートを受ける場合と同じ方法です。 必要な場合、Azure サポート チームは状況に応じて Gemalto サポートに連携します。

### <a name="q-how-do-i-get-access-to-dedicated-hsm-client-software-documentation-firmware-images"></a>Q: 専用 HSM クライアントのソフトウェア、ドキュメント、ファームウェア イメージへのアクセスを取得するには、どうすればよいですか。

HSM クライアント ソフトウェア、ドキュメント、OS/ファームウェア イメージへのアクセスを取得するには、お客様が直接、Gemalto と連携する必要があります。

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-gemalto-who-is-responsible-for-upgradingpatching-osfirmware"></a>Q: セキュリティの脆弱性が検出され、Gemalto によって修正プログラムがリリースされている場合、OS/ファームウェアのアップグレードや修正プログラムの適用は誰が行いますか。

Microsoft では、お客様に割り当てられている HSM に接続することはできません。 お客様が HSM をアップグレードして、修正プログラムを適用する必要があります。

## <a name="cryptography-and-standards"></a>暗号化と標準

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>Q: 専用 HSM 内に最重要データの暗号化キーを格納することは、安全ですか。

はい。専用 HSM では、FIPS 140-2 レベル 3 の検証済み HSM を使用する SafeNet ネットワーク HSM 7 アプライアンスをプロビジョニングします。 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>Q: 専用 HSM では、どのような暗号化キーとアルゴリズムがサポートされていますか。

専用 HSM サービスでは、SafeNet ネットワーク HSM 7 アプライアンスをプロビジョニングします。 このアプライアンスでは、フル スイート B のサポートなど、暗号化キーの種類とアルゴリズムを幅広くサポートしています。

* 非対称:
  * RSA
  * DSA
  * Diffie-Hellman
  * 楕円曲線
  * 名前付き、ユーザー定義、Brainpool 曲線、KCDSA による暗号化 (ECDSA、ECDH、Ed25519、ECIES)
* 対称:
  * AES-GCM
  * Triple DES
  * DES
  * ARIA、SEED
  * RC2
  * RC4
  * RC5
  * CAST
  * ハッシュ/Message Digest/HMAC: SHA-1、SHA-2、SM3
  * キー派生: SP800-108 カウンター モード
  * キー ラッピング: SP800-38F
  * 乱数生成: BSI DRG.4 に準拠している、FIPS 140-2 承認済み DRBG (SP 800-90 CTR モード)

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>Q: 専用 HSM では FIPS 140-2 レベル 3 を検証済みですか。

はい。 専用 HSM サービスでは、FIPS 140-2 レベル 3 の検証済み HSM を使用する SafeNet ネットワーク HSM 7 アプライアンスをプロビジョニングします。

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>Q: FIPS 140-2 レベル 3 の検証済みモードで専用 HSM を稼働させていることを確認するには、どうすればよいですか。

専用 HSM サービスでは、SafeNet Luna ネットワーク HSM 7 アプライアンスをプロビジョニングします。 これらのアプライアンスでは、FIPS 140-2 レベル 3 の検証済み HSM を使用します。 また、展開されている既定の構成、オペレーティング システム、およびファームウェアも、FIPS 検証済みになっています。 FIPS 140-2 レベル 3 に準拠するために、何らかの操作を行う必要はありません。

### <a name="how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>HSM のプロビジョニングが解除されたときに、すべてのキー マテリアルが消去されていることをユーザーが確認するには、どうしたらよいですか?

プロビジョニング解除を要求する前に、Gemalto が提供する HSM クライアント ツールを使用して、お客様が HSM をゼロ埋めしておく必要があります。

## <a name="performance-and-scale"></a>パフォーマンスとスケール

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>Q: 専用 HSM では、1 秒あたり何回の暗号操作がサポートされていますか。

専用 HSM では、SafeNet ネットワーク HSM 7 アプライアンス (モデル A790) をプロビジョニングします。 一部の操作の最大パフォーマンスの概要を以下に示します。 

* RSA-2048: 1 秒あたり 10,000 回のトランザクション
* ECC P256: 1 秒あたり 20,000 回のトランザクション
* AES-GCM: 1 秒あたり 17,000 回のトランザクション

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>Q: 専用 HSM 内に作成できるパーティション数はいくつですか。

使用される HSM の指定モデルに基づいて、10 個のパーティションが利用可能です。

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>Q: 専用 HSM でサポートできるキーの数はいくつですか。

キーの最大数を以下に示します。 非対称キーを使用している場合は、これらの数がキーのペアにも適用されます。

* RSA-2048 - 19,000
* ECC-P256 - 91,000
* AES-256 - 218,000

キー生成のテンプレートに設定された特定のキー属性とパーティション数に応じて、キャパシティは変化します。

