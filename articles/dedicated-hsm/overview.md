---
title: 専用 HSM とは - Azure Dedicated HSM | Microsoft Docs
description: Azure 内に暗号化キーの保管場所を提供する Azure サービス、Azure Dedicated HSM について説明します。
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc, seodec18
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: 6eb82400b2f598b4b54da4b238f4bce71d83de6b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "90970208"
---
# <a name="what-is-azure-dedicated-hsm"></a>Azure Dedicated HSM とは何か

Azure Dedicated HSM は、Azure 内に暗号化キーの保管場所を提供する Azure サービスです。 Dedicated HSM は、最も厳格なセキュリティ要件に適合しています。 これは、FIPS 140-2 レベル 3 検証済みデバイスを必要としていて、HSM アプライアンスを完全かつ独占的に制御する必要のあるお客様にとって、理想的なソリューションです。 

 HSM デバイスは、複数の Azure リージョン全体にグローバルにデプロイされます。 高可用性を確保するために、これらを一対のデバイスとして容易にプロビジョニングして構成することができます。 HSM デバイスを複数のリージョン全体にプロビジョニングすることで、リージョン レベルのフェールオーバーに対応することもできます。 Microsoft では、Gemalto の [SafeNet Luna Network HSM 7 (Model A790)](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/) アプライアンスを使用して Dedicated HSM サービスを提供しています。 このデバイスは、最高レベルのパフォーマンスと暗号統合オプションを備えています。 

プロビジョニングした HSM デバイスは、お客様の仮想ネットワークに直接接続されます。 また、ポイント対サイトまたはサイト間の VPN 接続を構成することで、オンプレミスのアプリケーションや管理ツールからもアクセスできます。 お客様には、HSM デバイスを構成して管理するためのソフトウェアとドキュメントが Gemalto のサポート ポータルを通じて提供されます。

## <a name="why-use-azure-dedicated-hsm"></a>Azure Dedicated HSM を使用する理由

### <a name="fips-140-2-level-3-compliance"></a>FIPS 140-2 レベル 3 準拠

多くの組織には、暗号化キーの保管に関して [FIPS 140-2 レベル 3](https://csrc.nist.gov/publications/detail/fips/140/2/final) 要件への準拠を規定した、厳しい業界規制が存在します。 Microsoft のマルチテナント Azure Key Vault サービスが現在対応しているのは、FIPS 140-2 レベル 2 認定のみです。 Azure Dedicated HSM は、金融サービス業や行政機関など、FIPS 140-2 レベル 3 要件への準拠が求められる組織の実際のニーズを満たします。

### <a name="single-tenant-devices"></a>シングルテナント デバイス

Microsoft のお客様の多くは、暗号の保管デバイスのシングル テナントに関する要件を抱えています。 Azure Dedicated HSM サービスでは、世界中に分散されている Microsoft のいずれかのデータセンターから物理デバイスをプロビジョニングすることができます。 お客様へのプロビジョニング後、そのデバイスにアクセスできるのは、そのお客様だけです。

### <a name="full-administrative-control"></a>完全な管理制御

多くのお客様は、完全な管理制御や、管理目的での単独アクセスを求めています。 デバイスのプロビジョニング後、そのデバイスに管理レベルまたはアプリケーション レベルでアクセスできるのはそのお客様だけです。

 お客様がデバイスへの初回アクセス時にパスワードを変更すると、その後は Microsoft による管理制御は行えなくなります。 その時点からお客様は、完全な管理制御とアプリケーション管理能力を有する真のシングル テナントとなります。 ただしテレメトリについては、シリアル ポート接続を通じた (管理者ロールではなく) モニター レベルのアクセス権を Microsoft が保持します。 これにより、温度、電源の正常性、ファンの正常性などのハードウェア モニターがカバーされます。 
 
 この監視は必要であればお客様が自由に無効化できます。 ただし、これを無効にした場合、Microsoft からプロアクティブな正常性アラートは通知されません。

### <a name="high-performance"></a>高性能

Gemalto デバイスがこのサービスに選ばれたのには、さまざまな理由があります。 幅広い暗号アルゴリズムのサポート、さまざまなオペレーティング システムのサポート、広範な API のサポートが提供されます。 デプロイされた特定のモデルは、RSA-2048 の場合に毎秒 10,000 操作という優れたパフォーマンスを発揮します。 一意のアプリケーションのインスタンスについて、10 個のパーティションを使用することができます。 短い待ち時間、大容量、高スループットのデバイスです。

### <a name="unique-cloud-based-offering"></a>他にはないクラウドベースのオファリング

Microsoft は、一部のお客様に固有のニーズを把握しています。 FIPS 140-2 レベル 3 検証済みの専用 HSM サービスを新しいお客様に提供し、クラウドベースとオンプレミスのアプリケーション統合をこれほど広範に実現するクラウド プロバイダーは他にありません。

## <a name="is-azure-dedicated-hsm-right-for-you"></a>Azure の専用 HSM が適しているお客様

Azure Dedicated HSM は、特定の種類の大規模な組織に固有の要件に対応する特殊なサービスです。 そのため、大部分の Azure のお客様には、このサービスの使用プロファイルが適さないと考えられます。 多くのお客様が、Azure Key Vault サービスの方が適しており、コスト効果に優れていると感じるものと思われます。 ご自分の要件に適しているかどうかを判断しやすいように、次の基準を特定しました。

### <a name="best-fit"></a>最適

Azure Dedicated HSM は、HSM デバイスに直接かつ単独でアクセスすることが求められる "リフトアンドシフト" のシナリオに最適です。 以下に例を示します。

- オンプレミスから Azure Virtual Machines にアプリケーションを移行する
- AWS Cloud HSM Classic サービスを使用するアプリケーションを Amazon AWS EC2 から仮想マシンに移行する (Amazon は、このサービスを新規顧客に提供していません)
- Azure Virtual Machines で市販のソフトウェアを実行している (Apache/Ngnix SSL Offload、Oracle TDE、ADCS など) 

### <a name="not-a-fit"></a>不適

Azure Dedicated HSM は、次のようなシナリオには適していません。お客様によって管理されるキーを使った暗号化をサポートする Microsoft クラウド サービス (Azure Information Protection、Azure Disk Encryption、Azure Data Lake Store、Azure Storage、Azure SQL Database、Office 365 のカスタマー キーなど) は、Azure Dedicated HSM とは統合されません。

### <a name="it-depends"></a>場合による

Azure Dedicated HSM が適しているかどうかは、発生し得る複雑な要件の組み合わせや、妥協できる点とできない点によって左右されます。 その例が FIPS 140-2 レベル 3 要件です。 この要件は共通のもので、現在のところ Dedicated HSM がこれを満たす唯一の選択肢となります。 こうした義務付けられている要件が該当しないようであれば、たいていは、Azure Key Vault か Dedicated HSM のどちらかを選ぶことになります。 意思決定を行う前にお客様自身の要件を評価してください。

次のような状況に該当する場合は、選択肢を比較検討する必要があります。 

- お客様の Azure 仮想マシンで実行中の新しいコード
- Azure 仮想マシンにおける SQL Server TDE
- Azure Storage のクライアント側暗号化
- SQL Server と Azure SQL DB の Always Encrypted

## <a name="next-steps"></a>次の手順

これは非常に特殊なサービスです。 そのため、このドキュメント セットの主要な概念について、価格、サポート、サービス レベル アグリーメントを含めて十分に理解しておくことをお勧めします。 

既存の仮想ネットワーク環境に HSM をプロビジョニングする際には、[Gemalto 統合ガイド](https://safenet.gemalto.com/partners/microsoft/)が役立ちます。 また、デプロイ アーキテクチャの設定方法を決める際に役立つ攻略ガイドも用意されています。

* [高可用性](high-availability.md)
* [物理的なセキュリティ](physical-security.md)
* [ネットワーク](networking.md)
* [サポート可能性](supportability.md)
* [Monitoring](monitoring.md)
