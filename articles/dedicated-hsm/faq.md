---
title: よく寄せられる質問 - Azure 専用 HSM | Microsoft Docs
description: 基本情報、相互運用性、高可用性、サポートなど、Azure 専用ハードウェア セキュリティ モジュールについてよく寄せられる質問の答えが見つかります。
services: dedicated-hsm
author: keithp
manager: rkarlin
tags: azure-resource-manager
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: keithp
ms.openlocfilehash: 1f349931fb479be3b3ba936b3d0a665427357f29
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97369040"
---
# <a name="frequently-asked-questions-faq"></a>よく寄せられる質問 (FAQ)

Microsoft Azure 専用 HSM についてよく寄せられる質問への回答を示します。

## <a name="the-basics"></a>基本操作

### <a name="q-what-is-a-hardware-security-module-hsm"></a>Q:ハードウェア セキュリティ モジュール (HSM) とは何ですか。

ハードウェア セキュリティ モジュール (HSM) は、暗号化キーの保護と管理に使用される物理コンピューティング デバイスです。 HSM に格納されたキーは、暗号操作に使用できます。 キー マテリアルは、改ざんの防止および改ざんの証明対策が取られたハードウェア モジュール内に、安全に保管されます。 HSM では、認証済みおよび承認済みのアプリケーション以外には、キーの使用を許可しません。 キー マテリアルが HSM による保護の境界の外に出ることは決してありません。

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>Q:Azure 専用 HSM オファリングとは何ですか。

Azure 専用 HSMはクラウドベースのサービスであり、お客様の仮想ネットワークに直接接続された Azure データセンター内でホストされる HSM を提供します。 これらの HSM は専用ネットワーク アプライアンス (Thales Network Luna HSM 7) です。 お客様のプライベート IP アドレス空間に直接展開され、Microsoft には HSM の暗号化機能へのアクセス権はありません。 お客様のみが、これらのデバイスの完全な管理と暗号化の制御を行います。 お客様がデバイスの管理を担当して、ご自身のデバイスから直接、完全なアクティビティ ログを取得することが可能です。 専用 HSM は、お客様が FIPS 140-2 レベル 3、HIPAA、PCI-DSS、eIDAS、およびその他多くのコンプライアンスや規制要件を満たすのに役立ちます。

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>Q:専用 HSM には、どのようなハードウェアが使用されますか?

Microsoft は Azure Dedicated HSM サービスを提供するために Thales と提携しています。 使用される具体的なデバイスは、[Thales Network Luna HSM 7](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) です。 このデバイスでは、FIPS 140-2 レベル 3 の検証済みファームウェアを提供しているだけでなく、10 個のパーティションを使って低待機時間、高パフォーマンス、および高容量も実現しています。 

### <a name="q-what-is-an-hsm-used-for"></a>Q:HSM は何に使用されますか。

HSM を使用する目的は、TLS (トランスポート層セキュリティ)、データの暗号化、PKI (公開キー基盤)、DRM (デジタル著作権管理)、ドキュメントの署名などの暗号化機能で使用される暗号化キーを格納することです。

### <a name="q-how-does-dedicated-hsm-work"></a>Q:専用 HSM はどのように動作しますか。

お客様は、PowerShell またはコマンド ライン インターフェイスを使用して、特定のリージョンに HSM をプロビジョニングできます。 お客様は、どの仮想ネットワークに HSM を接続するかを指定し、プロビジョニングされた HSM は、お客様のプライベート IP アドレス空間内の割り当てられた IP アドレスにおいて、指定されたサブネット内で利用可能になります。 次に、お客様はアプライアンス管理用の SSH を使用して HSM に接続し、HSM クライアント接続の設定、HSM の初期化、パーティションの作成、ロール (パーティション担当者、暗号化担当者、暗号化ユーザーなど) の定義と割り当てを行うことができます。 その後、お客様は、Thales が提供する HSM クライアント ツール/SDK/ソフトウェアを使用して、アプリケーションから暗号操作を実行します。

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>Q:専用 HSM サービスでは、どのようなソフトウェアが提供されますか。

Microsoft によってプロビジョニングされると、Thales から HSM デバイスのすべてのソフトウェアが提供されます。 ソフトウェアは、[Thales Customer Support Portal](https://supportportal.gemalto.com/csm/) で入手できます。 専用 HSM サービスを使用しているお客様は、Thales サポートへの登録が求められ、関連するソフトウェアへのアクセスとダウンロードが可能な顧客 ID を保有します。 サポートされるクライアント ソフトウェアはバージョン 7.2 であり、FIPS 140-2 レベル 3 の検証済みファームウェア バージョン 7.0.3 と互換性があります。 

### <a name="q-what-extra-costs-may-be-incurred-with-dedicated-hsm-service"></a>Q:専用 HSM サービスで追加コストが発生する可能性がありますか。

専用 HSM サービスを使用したときに、次の項目で追加コストが発生します。 
* 専用のオンプレミスのバックアップ デバイスは、専用 HSM サービスで使用できますが、これには追加コストが発生するため、Thales から直接供給を受ける必要があります。
* 専用 HSM には 10 個のパーティション ライセンスが用意されています。 お客様がさらに多くのパーティションを必要とする場合、Thales から直接供給される追加ライセンスに対して追加料金が発生します。
* 専用 HSM には、ネットワーク インフラストラクチャ (VNET、VPN Gateway など) と、デバイス構成用の仮想マシンなどのリソースが必要です。 これらの追加リソースには追加コストが発生し、専用 HSM サービスの価格には含まれません。

### <a name="q-does-azure-dedicated-hsm-offer-password-based-and-ped-based-authentication"></a>Q:Azure 専用 HSM は、パスワード ベースの認証と PED ベースの認証を提供していますか。

現時点では、Azure 専用 HSM はパスワード ベースの認証でのみ HSM を提供しています。

### <a name="q-will-azure-dedicated-hsm-host-my-hsms-for-me"></a>Q:Azure 専用 HSM はユーザーの HSM をホストしますか。

Microsoft は、専用 HSM サービス経由で Thales Network Luna HSM 7 のみを提供しており、ユーザーが提供するデバイスをホストすることはできません。

### <a name="q-does-azure-dedicated-hsm-support-payment-pineft-features"></a>Q:Azure Dedicated HSM は、支払い (PIN/EFT) 機能をサポートしますか。

Azure Dedicated HSM サービスでは、Thales Network Luna HSM 7 デバイスが使用されます。 これらのデバイスは、支払い HSM 固有の機能 (PIN や EFT など) または認定をサポートしていません。 Azure 専用 HSM サービスでの今後の支払い HSM のサポートを希望する場合は、Microsoft アカウント担当者までその旨をお知らせください。

### <a name="q-which-azure-regions-is-dedicated-hsm-available-in"></a>Q:どの Azure リージョンで Dedicated HSM を使用できますか。

2019 年 3 月下旬の時点では、Dedicated HSM は次に示す 14 のリージョンで使用できます。 追加のリージョンについては計画中であり、お客様の Microsoft アカウント担当者を介して検討することができます。

* 米国東部
* 米国東部 2
* 米国西部
* 米国西部 2
* 米国中南部
* 東南アジア
* 東アジア
* インド中部
* インド南部
* 東日本
* 西日本
* 北ヨーロッパ
* 西ヨーロッパ
* 英国南部
* 英国西部
* カナダ中部
* カナダ東部
* オーストラリア東部
* オーストラリア南東部
* スイス北部
* スイス西部
* US Gov バージニア州
* US Gov テキサス

## <a name="interoperability"></a>相互運用性

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>Q:自分のアプリケーションで専用 HSM に接続するにはどのようにしたらよいですか。

お客様は、Thales が提供する HSM クライアント ツール/SDK/ソフトウェアを使用して、お使いのアプリケーションから暗号操作を実行します。 ソフトウェアは、[Thales Customer Support Portal](https://supportportal.gemalto.com/csm/) で入手できます。 専用 HSM サービスを使用しているお客様は、Thales サポートへの登録が求められ、関連するソフトウェアへのアクセスとダウンロードが可能な顧客 ID を保有します。

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>Q:アプリケーションはリージョン内の、またはリージョンにまたがる異なる VNET から専用 HSM に接続できますか。

はい。リージョン内では [VNET ピアリング](../virtual-network/virtual-network-peering-overview.md)を使用して、仮想ネットワーク間に接続を確立する必要があります。 リージョン間の接続には、[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) を使用する必要があります。

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>Q:専用 HSM をオンプレミスの HSM と同期できますか。

はい。専用 HSM とオンプレミス HSM を同期することが可能です。 オンプレミス ネットワークとの接続を確立するために、[ポイント ツー ポイント VPN またはポイント対サイト](../vpn-gateway/vpn-gateway-about-vpngateways.md)接続を使用できます。

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>Q:専用 HSM 内に格納されたキーを使用して、他の Azure サービスで使用されるデータを暗号化できますか。

いいえ。 Azure 専用 HSM は、お使いの仮想ネットワーク内からのみアクセス可能です。

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>Q:既存のオンプレミスの HSM から専用 HSM にキーをインポートできますか。

はい (オンプレミスの Thales Network Luna HSM 7 HSM がある場合)。 複数の方法があります。 [Thales HSM のドキュメント](https://thalesdocs.com/gphsm/luna/7.2/docs/network/Content/Home_network.htm)をご覧ください。

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>Q:専用 HSM クライアント ソフトウェアでは、どのようなオペレーティング システムがサポートされますか。

* Windows、Linux、Solaris、AIX、HP-UX、FreeBSD
* 仮想: VMware、Hyper-V、Xen、KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>Q:複数の HSM の複数のパーティションで高可用性構成を作成するようにクライアント アプリケーションを構成するにはどのようにしたらよいですか。

高可用性を得るには、各 HSM からのパーティションを使用するように、HSM クライアント アプリケーションの構成を設定する必要があります。 Thales の HSM クライアント ソフトウェアに関するドキュメントをご覧ください。

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>Q:専用 HSM では、どのような認証メカニズムがサポートされますか。

Azure 専用 HSM では SafeNet ネットワーク HSM 7 アプライアンス (モデル A790) を使用すると共に、パスワードベースの認証をサポートしています。

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>Q:専用 HSM では、どのような SDK、API、クライアント ソフトウェアを使用できますか。

PKCS#11、Java (JCA/JCE)、Microsoft CAPI、CNG、OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>Q:キーを Luna 5/6 HSM から Azure 専用 HSM にインポートまたは移行できますか。

はい。 適切な Thales の移行ガイドについては、Thales の担当者にお問い合わせください。 

## <a name="using-your-hsm"></a>ご自身の HSM を使用する

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>Q:Azure Key Vault または Azure 専用 HSM のどちらを使用するかをどのように判断したらよいですか。

HSM を使用する Azure オンプレミス アプリケーションに移行している企業では、Azure 専用 HSM が適切な選択です。 専用 HSM は、変更を最小限に抑えてアプリケーションを移行するオプションを提供します。 Azure VM または Web App で実行されるアプリケーションのコード内で、暗号操作が行われている場合は、専用 HSM を使用できます。 一般に、HSM をキー ストアとしてサポートし、IaaS (サービスとしてのインフラストラクチャ) モデルで実行される市販のソフトウェアでは、専用 HSM を使用できます。キーのない TLS に対するアプリケーション ゲートウェイまたはトラフィック マネージャー、ADCS (Active Directory 証明書サービス)、類似の PKI ツール、ドキュメント署名に使用されるツール/アプリケーション、コード署名、EKM (拡張可能キー管理) プロバイダーを使用する HSM においてマスター キーを使った TDE (Transparent Database Encryption) によって構成されている SQL Server (IaaS) などがあります。 "クラウド生まれ" のアプリケーションや、お客様のデータが PaaS (サービスとしてのプラットフォーム) または SaaS (サービスとしてのソフトウェア) シナリオで処理される暗号保存のシナリオでは、Azure Key Vault が適しています。このシナリオには、Office 365 カスタマー キー、Azure Information Protection、Azure Disk Encryption、カスタマー マネージド キーを使用した Azure Data Lake Store 暗号化、カスタマー マネージド キーを使用した Azure Storage 暗号化、およびカスタマー マネージド キーを使用した Azure SQL などがあります。

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>Q:Azure 専用 HSM にはどのような使用シナリオが最適ですか。

Azure 専用 HSM は、移行のシナリオに最適です。 つまり、既に HSM を使用している Azure にオンプレミス アプリケーションを移行する場合です。 これにより、アプリケーションへの変更を最小限に抑えて Azure に移行するという、問題が生じにくいオプションが提供されます。 Azure VM または Web App で実行されるアプリケーションのコード内で暗号操作が行われている場合、専用 HSM を使用できます。 一般に、HSM をキー ストアとしてサポートし、IaaS (サービスとしてのインフラストラクチャ) モデルで実行される市販のソフトウェアでは、専用 HSM を使用できます。次のような例があります。

* キーのない TLS に対するアプリケーション ゲートウェイまたはトラフィック マネージャー
* ADCS (Active Directory 証明書サービス)
* 類似の PKI ツール
* ドキュメント署名に使用されるツール/アプリケーション
* コード署名
* EKM (拡張可能キー管理) プロバイダーを使用する HSM において、マスター キーを使った TDE (Transparent Database Encryption) によって構成されている SQL Server (IaaS)

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>Q:専用 HSM は、Office 365 カスタマー キー、Azure Information Protection、Azure Data Lake Store、Disk Encryption、Azure Storage 暗号化、Azure SQL TDE で使用できますか。

いいえ。 専用 HSM は、お客様のプライベート IP アドレス空間に直接プロビジョニングされるため、他の Azure または Microsoft サービスからアクセスすることはできません。

## <a name="administration-access-and-control"></a>管理、アクセス、および制御

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-"></a>Q:ユーザーは、専用 HSM を使用して HSM を完全に排他的に制御できますか。

はい。 各 HSM アプライアンスは完全に単一のお客様専用であり、一度プロビジョニングされて管理者パスワードが変更されると、他のユーザーには管理制御権がありません。

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>Q:Microsoft はユーザーの HSM に対してどのようなレベルのアクセス権を持っていますか。

Microsoft は HSM に対して、管理制御または暗号制御を行うことはできません。 Microsoft では、温度やコンポーネントの正常性などの基本のテレメトリを取得するために、シリアル ポート接続経由で監視レベルのアクセス権を保持しています。 これにより、Microsoft では、正常性の問題に関する事前通知を提供しています。 必要に応じて、お客様はこのアカウントを無効にできます。

### <a name="q-what-is-the-tenant-admin-account-microsoft-uses-i-am-used-to-the-admin-user-being-admin-on-thales-luna-hsms"></a>Q:Microsoft で使われている "tenant admin" アカウントとは何ですか。Thales Luna HSM で管理者ユーザーは "admin" であるのに慣れています。

HSM デバイスには、既定の管理者ユーザーと通常の既定のパスワードが付属しています。 Microsoft は、デバイスがプール内でお客様によるプロビジョニングを待っている間、既定のパスワードが使用されるのを望みませんでした。 これは、弊社の厳密なセキュリティ要件を満たしません。 このため、プロビジョニング時に破棄される強力なパスワードが設定されています。 また、プロビジョニング時には、"tenant admin" という名前の管理者ロールで、新しいユーザーが作成されます。 このユーザーには既定のパスワードが設定されており、お客様は、新しくプロビジョニングされたデバイスに初めてログインするときに、最初のアクションとしてこれを変更します。 このプロセスにより、高度なセキュリティが確保され、お客様に対する唯一の管理制御という約束が維持されます。 お客様がそのアカウントの使用を希望される場合、"tenant admin" ユーザーを使って管理者ユーザーのパスワードをリセットできることに注意する必要があります。 

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>Q:Microsoft または Microsoft の担当者は、ユーザーの専用 HSM 内のキーにアクセスできますか。

いいえ。 お客様に割り当てられている専用 HSM に格納されたキーに対しては、Microsoft はアクセス権を保持しません。

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>Q:自分に割り当てられた HSM 上のソフトウェア/ファームウェアをアップグレードできますか。

別のファームウェア バージョンの特定の機能が必要な場合、お客様には、ソフトウェア/ファームウェアのアップグレードを含めた完全な管理制御権があります。 変更を加える前に、HSMRequest@microsoft.com に連絡して、アップグレードについて Microsoft にお問い合わせください  

### <a name="q-how-do-i-manage-dedicated-hsm"></a>Q:専用 HSM はどのように管理したらよいですか。

SSH を使ってアクセスすることで、専用 HSM を管理できます。

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>Q:専用 HSM 上のパーティションはどのように管理したらよいですか。

HSM およびパーティションの管理には、Thales の HSM クライアント ソフトウェアが使用されます。

### <a name="q-how-do-i-monitor-my-hsm"></a>Q:自分の HSM を監視するにはどのようにしたらよいですか。

お客様は、syslog および SNMP 経由で HSM アクティビテ ログへのフル アクセス権を保持しています。 HSM からログまたはイベントを受信するには、お客様が syslog サーバーまたは SNMP サーバーをセットアップする必要があります。

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>Q:専用 HSM からすべての HSM 操作のフル アクセス ログを取得できますか。

はい。 HSM アプライアンスから syslog サーバーにログを送信することができます。

## <a name="high-availability"></a>高可用性

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>Q:同じリージョン内で、または複数のリージョンにまたがって高可用性を構成できますか。

はい。 高可用性の構成とセットアップは、Thales が提供する HSM クライアント ソフトウェアで実行されます。 同一リージョン内またはリージョン間での同じ VNET または別の VNET からの HSM や、サイト対サイトまたはポイント ツー ポイントの VPN を使用して VNET に接続されているオンプレミス HSM は、同じ高可用性の構成に追加できます。 これによってキー マテリアルのみが同期し、ロールなど特定の構成項目は同期されないことに注意する必要があります。

### <a name="q-can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>Q:自分のオンプレミス ネットワークから Azure 専用 HSM を使った高可用性グループに、HSM を追加することはできますか。

はい。 HSM が SafeNet Luna ネットワーク HSM 7 の高可用性の要件を満たしている必要があります。

### <a name="q-can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>Q:オンプレミス ネットワークから Azure 専用 HSM を使った高可用性グループに、Luna 5/6 HSM を追加することはできますか。

いいえ。

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>Q:1 つの単一アプリケーションから同じ高可用性構成にいくつの HSM を追加できますか。

メンバー数が 16 の HA グループに対して非常に厳しいテストを行い、すばらしい結果が出ています。

## <a name="support"></a>サポート

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>Q:専用 HSM サービスの SLA とはどういうものですか。

専用 HSM サービスで保証されている特定の稼働時間はありません。 Microsoft では、デバイスへのネットワーク レベルのアクセスを保証しているため、標準の Azure ネットワーク SLA が適用されます。

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>Q:Azure 専用 HSM で使用される HSM はどのように保護されていますか。

Azure データセンターにおいて、物理上および手続き上のセキュリティ制御が詳細に行わています。 さらに、専用 HSM は、データセンター内でもアクセスがより制限されたエリアでホストされています。 これらのエリアでは、セキュリティ強化のために詳細な物理アクセス制御やビデオ カメラによる監視が行われています。

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>Q:セキュリティ違反やハードウェアの改ざんが発生した場合はどうなりますか。

専用 HSM サービスでは、Thales Network Luna HSM 7 アプライアンスが使用されます。 これらのアプライアンスでは、物理的および論理的な改ざんの検出をサポートしています。 改ざんが行われた場合、HSM は自動的にゼロ埋めされます。

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>Q:自分の専用 HSM 内のキーがエラーや悪意のある内部関係者の攻撃のために失われていないことを確認するにはどのようにしたらよいですか。

オンプレミス HSM バックアップ デバイスを使用して、ディザスター リカバリーのために HSM の日常的な定期バックアップを実行することを強くお勧めします。 HSM バックアップ デバイスに接続されているオンプレミス ワークステーションに対しては、ピアツーピアまたはサイト間のVPN 接続を使用する必要があります。

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>Q:専用 HSM のサポートを受けるにはどのようにしたらよいですか。

サポートはマイクロソフトと Thales の両方で提供されます。  ハードウェアまたはネットワーク アクセスの問題がある場合は、Microsoft にサポート リクエストを出してください。HSM 構成、ソフトウェア、およびアプリケーション開発の問題がある場合は、Thales にサポート要求を出してください。 問題が不明な場合は、マイクロソフトにサポート要求を出してください。その後、必要に応じて Thales が対応できます。 

### <a name="q-how-do-i-get-the-client-software-documentation-and-access-to-integration-guidance-for-the-thales-network-luna-hsm-7"></a>Q:Thales Network Luna HSM 7 のクライアント ソフトウェア、ドキュメント、統合ガイダンスへのアクセスを取得するにはどうすればよいですか。

サービスに登録すると、Thales カスタマー サポート ポータルに登録するための Thales カスタマー ID が提供されます。 これによって、すべてのソフトウェアとドキュメントにアクセスできるようになり、Thales に直接サポート リクエストを出せるようになります。

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-thales-who-is-responsible-for-upgradingpatching-osfirmware"></a>Q:セキュリティの脆弱性が検出され、Thales によって修正プログラムがリリースされた場合は、だれが OS/ファームウェアのアップグレードまたはプログラム修正に責任を負いますか。

Microsoft では、お客様に割り当てられている HSM に接続することはできません。 お客様が HSM をアップグレードして、修正プログラムを適用する必要があります。

### <a name="q-what-if-i-need-to-reboot-my-hsm"></a>Q:HSM を再起動する必要が生じたらどうすればよいですか。

HSM にはコマンド ラインの再起動オプションがありますが、再起動が断続的に応答を停止する問題が発生しているため、安全に再起動できるように、Microsoft にサポート リクエストを送信してデバイスの物理的な再起動を依頼することをお勧めします。 

## <a name="cryptography-and-standards"></a>暗号化と標準

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>Q:最も重要なデータの暗号化キーを専用 HSM に格納することは安全ですか。

はい。専用 HSM では、FIPS 140-2 レベル 3 の検証済み HSM を使用する Thales Network Luna HSM 7 アプライアンスをプロビジョニングします。 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>Q:専用 HSM では、どのような暗号化キーとアルゴリズムがサポートされていますか。

専用 HSM サービスでは、Thales Network Luna HSM 7 アプライアンスがプロビジョニングされます。 これにより、次に示すような幅広い暗号化キーの種類とアルゴリズムがサポートされます。フル スイート B のサポート

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
  * キー派生: SP 800-108 カウンター モード
  * キー ラッピング: SP 800-38F
  * 乱数生成: FIPS 140-2 承認済み DRBG (SP 800-90 CTR モード)、BSI DRG.4 に準拠

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>Q:専用 HSM は FIPS 140-2 レベル 3 が検証されていますか。

はい。 専用 HSM サービスでは、FIPS 140-2 レベル 3 の検証済み HSM を使用する Thales Network Luna HSM 7 アプライアンスをプロビジョニングします。

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>Q:FIPS 140-2 レベル 3 の検証済みモードで専用 HSM を稼働させていることを確認するには何を行う必要がありますか。

専用 HSM サービスでは、Thales Network Luna HSM 7 アプライアンスがプロビジョニングされます。 これらのアプライアンスでは、FIPS 140-2 レベル 3 の検証済み HSM を使用します。 また、展開されている既定の構成、オペレーティング システム、およびファームウェアも、FIPS 検証済みになっています。 FIPS 140-2 レベル 3 に準拠するために、何らかの操作を行う必要はありません。

### <a name="q-how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>Q:HSM のプロビジョニングが解除されたときに、すべてのキー マテリアルが消去されていることをユーザーが確認するには、どうしたらよいですか?

プロビジョニング解除を要求する前に、Thales が提供する HSM クライアント ツールを使用して、お客様が HSM をゼロ埋めしておく必要があります。

## <a name="performance-and-scale"></a>パフォーマンスとスケール

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>Q:専用 HSM では、1 秒あたり何回の暗号化操作がサポートされていますか。

専用 HSM では、Thales Network Luna HSM 7 HSM がプロビジョニングされます。 一部の操作の最大パフォーマンスの概要を以下に示します。 

* RSA-2048: 1 秒あたり 10,000 トランザクション
* ECC P256: 1 秒あたり 20,000 トランザクション
* AES-GCM: 1 秒あたり 17,000 トランザクション

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>Q:専用 HSM では、いくつのパーティションを作成できますか。

使用される SafeNet Luna HSM 7 モデル A790 には、サービス料金に 10 個のパーティションのライセンスが含まれます。 デバイスのパーティションの制限は 100 個です。この制限を上回るパーティションを追加すると、余分なライセンス コストが発生し、デバイスに新しいライセンス ファイルのインストールする必要があります。

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>Q:専用 HSM では、いくつのキーをサポートできますか。

キーの最大数は使用可能なメモリの機能です。 使用中の SafeNet Luna 7 モデル A790 の目盛りは 32MB です。 非対称キーを使用している場合は、次の数がキーのペアにも適用されます。

* RSA-2048 - 19,000
* ECC-P256 - 91,000

キー生成のテンプレートに設定された特定のキー属性とパーティション数に応じて、キャパシティは変化します。
