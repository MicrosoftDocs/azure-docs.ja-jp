---
title: Cloud Foundry と Azure の統合方法 | Microsoft Docs
description: Cloud Foundry で Azure サービスを利用してエンタープライズ エクスペリエンスを強化する方法について説明します
services: virtual-machines-linux
documentationcenter: ''
author: ningk
manager: jeconnoc
editor: ''
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: ningk
ms.openlocfilehash: e341cc5beeb8e8362a848bb1e208ddf1dc773978
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2019
ms.locfileid: "71976797"
---
# <a name="integrate-cloud-foundry-with-azure"></a>Cloud Foundry と Azure を統合する

[Cloud Foundry](https://docs.cloudfoundry.org/) は、クラウド プロバイダーの IaaS プラットフォーム上で動作する PaaS プラットフォームです。 各クラウド プロバイダーに、一貫性のあるアプリケーション デプロイ エクスペリエンスを提供します。 また、エンタープライズ グレードの HA、スケーラビリティ、および低コストで、さまざまな Azure サービスと統合することもできます。
以下を含め、[Cloud Foundry には 6 つのサブシステム](https://docs.cloudfoundry.org/concepts/architecture/)があります。これらはオンラインで柔軟にスケールできます。ルーティング、認証、アプリケーション ライフ サイクル管理、サービス管理、メッセージング、および監視です。 各サブシステムについて、対応する Azure サービスを利用するように Cloud Foundry を構成することができます。 

![Azure 統合アーキテクチャでの Cloud Foundry](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1.高可用性とスケーラビリティ
### <a name="managed-disk"></a>マネージド ディスク
Bosh では、ディスクの作成および削除ルーチンに、Azure CPI (Cloud Provider Interface) を使用します。 既定では、アンマネージド ディスクが使用されます。 ユーザーがストレージ アカウントを手動で作成し、CF マニフェスト ファイルでアカウントを構成する必要があります。 これは、ストレージ アカウントあたりのディスクの数に制限があるためです。
[マネージド ディスク](https://azure.microsoft.com/services/managed-disks/)を使用できるようになり、管理されたセキュリティと信頼性の高いディスク ストレージが仮想マシンに提供されます。 ユーザーは、スケーリングと HA のためにストレージ アカウントを操作する必要がなくなりました。 Azure によって、ディスクが自動的に配置されます。 Azure CPI は、新しいデプロイか既存のデプロイかにかかわらず、CF デプロイ中にマネージド ディスクの作成または移行を処理します。 これは PCF 1.11 でサポートされています。 また、参考として、オープンソースの Cloud Foundry の[マネージド ディスク ガイダンス](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks)を調べることもできます。 
### <a name="availability-zone-"></a>可用性ゾーン *
クラウドネイティブ アプリケーション プラットフォームとして、Cloud Foundry は、[4 つのレベルの高可用性](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html)を持つように設計されています。 最初の 3 つのレベルのソフトウェア障害は CF システム自体で処理できますが、プラットフォームのフォールト トレランスはクラウド プロバイダーによって提供されます。 主要な CF コンポーネントは、クラウド プロバイダーのプラットフォーム HA ソリューションで保護する必要があります。 たとえば、GoRouter、Diego Brain、CF データベース、サービス タイルなどです。 既定では、データ センター内のクラスター間のフォールト トレランスには、[Azure 可用性セット](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets)が使用されます。
しかし、[Azure 可用性ゾーン](https://docs.microsoft.com/azure/availability-zones/az-overview )がリリースされたため、フォールト トレランスが次のレベルに引き上げられ、データ センター間の冗長性で待機時間が少なくなります。
Azure 可用性ゾーンは、VM のセットを 2 つ以上のデータ センターに配置することで HA を実現し、VM の各セットは他のセットと重複しています。 1 つのゾーンがダウンしても、他のセットはまだ利用することができ、災害からは隔離されています。
> [!NOTE] 
> Azure 可用性ゾーンは、まだ一部のリージョンでは提供されていません。最新の[サポートされているリージョンの一覧についてのお知らせ](https://docs.microsoft.com/azure/availability-zones/az-overview)を確認してください。 Open Source Cloud Foundry については、[Azure Availability Zone for open source Cloud Foundry guidance (オープン ソースの Cloud Foundry 用の Azure 可用性ゾーン ガイダンス)](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone) を確認してください。

## <a name="2-network-routing"></a>2.ネットワーク ルーティング
既定では、受信 CF API/アプリ要求に対して Azure 基本ロード バランサーが使用され、要求は Gorouter に転送されます。 Diego Brain、MySQL、ERT などの CF コンポーネントも、ロード バランサーを使用して HA のトラフィックを分散することができます。 また、Azure には完全に管理された負荷分散ソリューションのセットが用意されています。 TLS 終端 ("SSL オフロード") または HTTP/HTTPS 要求ごとのアプリケーション レイヤー処理が必要な場合は、Application Gateway を検討してください。 レイヤー 4 の高可用性とスケーラビリティの負荷分散については、標準ロード バランサーを検討してください。
### <a name="azure-application-gateway-"></a>Azure Application Gateway *
[Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) は、SSL オフロード、エンド ツー エンド SSL、Web アプリケーション ファイアウォール、Cookie ベースのセッション アフィニティなど、レイヤー 7 のさまざまな負荷分散機能を提供します。 [Application Gateway は、Open Source Cloud Foundry で構成](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway)することができます。 PCF については、POC テストの [PCF 2.1 リリース ノート](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway)を確認してください。

### <a name="azure-standard-load-balancer-"></a>Azure Standard Load Balancer *
Azure Load Balancer は、レイヤー 4 のロード バランサーです。 負荷分散セット内のサービスのインスタンス間でトラフィックを分散するために使用されます。 標準バージョンには、基本バージョンに加えて、[高度な機能](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)が用意されています。 例 1。 バックエンド プールの上限が、100 から 1,000 VM に上げられました。  2. エンドポイントで、1 つの可用性セットだけではなく複数の可用性セットをサポートできるようになりました。  手順 3. HA ポート、より豊富な監視データなど、追加の機能があります。 Azure 可用性ゾーンに移行する場合は、標準ロード バランサーが必要です。 新たにデプロイする場合は、Azure Standard Load Balancer で始めることをお勧めします。 

## <a name="3-authentication"></a>3.認証 
[Cloud Foundry User Account and Authentication](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) は、CF とそのさまざまなコンポーネントの中心的な ID 管理サービスです。 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) は、Microsoft が提供する、マルチテナントに対応したクラウドベースのディレクトリおよび ID 管理サービスです。 既定では、Cloud Foundry 認証には UAA が使用されます。 高度なオプションとして、UAA は外部ユーザー ストアとして Azure AD もサポートしています。 Azure AD ユーザーは、Cloud Foundry アカウントなしで、LDAP ID を使用して Cloud Foundry にアクセスすることができます。 PCF で UAA 用に Azure AD を構成するには、[こちらの手順](https://docs.pivotal.io/p-identity/1-6/azure/index.html)に従ってください。

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4.Cloud Foundry ランタイム システムのデータ ストレージ
Cloud Foundry には、Azure blobstore または Azure MySQL/PostgreSQL サービスをアプリケーション ランタイム システム ストレージ用に使用するための広範な拡張性があります。
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>Cloud Foundry Cloud Controller blobstore 用の Azure Blobstore
Cloud Controller blobstore は、buildpack、droplet、パッケージ、およびリソース プール用の重要なデータ ストアです。 既定では、Cloud Controller blobstore には NFS サーバーが使用されます。 単一障害点を回避するには、外部ストアとして Azure Blob Storage を使用します。 背景については、[Cloud Foundry のドキュメント](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html)を参照してください。また、[Pivotal Cloud Foundry のオプション](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html)も参照してください。

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>Cloud Foundry Elastic Run Time データベースとしての MySQL/PostgreSQL *
CF Elastic Runtime には、次の 2 つの主要なシステム データベースが必要です。
#### <a name="ccdb"></a>CCDB 
Cloud Controller データベース。  Cloud Controller は、クライアントがシステムにアクセスするための REST API エンドポイントを提供します。 CCDB は、Cloud controller のために、組織、スペース、サービス、ユーザー ロールなどのテーブルを格納します。
#### <a name="uaadb"></a>UAADB 
User Account and Authentication 用のデータベース。 ユーザー認証に関連するデータ (暗号化されたユーザー名とパスワードなど) を格納します。

既定では、ローカルのシステム データベース (MySQL) を使用することができます。 HA とスケーリングのために、Azure で管理されている MySQL または PostgreSQL サービスを利用してください。 Open Source Cloud Foundry で CCDB、UAADB、およびその他のシステム データベース用に Azure MySQL/PostgreSQL を有効にする手順については、[こちら](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service)を参照してください。

## <a name="5-open-service-broker"></a>5.Open Service Broker
Azure のサービス ブローカーは、Azure サービスへのアプリケーションのアクセスを管理するために、一貫性のあるインターフェイスを提供します。 新しい [Open Service Broker for Azure プロジェクト](https://github.com/Azure/open-service-broker-azure)は、Cloud Foundry、OpenShift、および Kubernetes にわたってアプリケーションにサービスを提供するために、単一の単純な方法を提供します。 PCF でのデプロイの手順については、[Azure Open Service Broker for PCF タイル](https://pivotal.io/platform/services-marketplace/data-management/microsoft-azure)を参照してください。

## <a name="6-metrics-and-logging"></a>6.メトリックとログ
Azure Log Analytics Nozzle は Cloud Foundry コンポーネントであり、[Cloud Foundry loggregator firehose](https://docs.cloudfoundry.org/loggregator/architecture.html) から [Azure Monitor ログ](https://azure.microsoft.com/services/log-analytics/)にメトリックを転送します。 Nozzle を使用すると、複数のデプロイにわたる CF のシステム正常性とパフォーマンスのメトリックを収集、表示、および分析することができます。
Open Source と Pivotal の両方の Cloud Foundry 環境に Azure Log Analytics Nozzle をデプロイし、Azure Monitor ログ コンソールからデータにアクセスする方法を確認するには、[こちら](https://docs.microsoft.com/azure/cloudfoundry/cloudfoundry-oms-nozzle)をクリックしてください。 
> [!NOTE]
> PCF 2.0 からは、既定では VM の BOSH 正常性メトリックが Loggregator Firehose に転送され、Azure Monitor ログ コンソールに統合されます。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="7-cost-saving"></a>7.コストの削減
### <a name="cost-saving-for-devtest-environments"></a>開発/テスト環境でのコスト削減
#### <a name="b-series-"></a>B シリーズ: *
Pivotal Cloud Foundry の運用環境では、F および D VM シリーズが一般的に推奨されていましたが、新しい "バースト対応の" [B シリーズ](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/)が選択肢に加わりました。 Web サーバー、小規模なデータベース、開発とテスト環境など、CPU が常時最大限のパフォーマンスを発揮している必要のないワークロードでは、B シリーズのバースト可能な VM が最適です。 このようなワークロードでは通常、負荷の急増に対応できることがパフォーマンスの要件となります。 0\.05 米国ドル/時間 (F1) と比較して、0.012 米国ドル/時間 (B1) です。詳細については、[VM サイズ](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general)と[料金](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)の完全な一覧を参照してください。 
#### <a name="managed-standard-disk"></a>マネージド Standard ディスク: 
運用環境で信頼性の高いパフォーマンスを得るには、Premium ディスクが推奨されていました。  [マネージド ディスク](https://azure.microsoft.com/services/managed-disks/)により、Standard ストレージでも同様の信頼性が得られるようになりました。ただし、パフォーマンスは異なります。 開発/テスト環境や重要度が低い環境のようなパフォーマンスが重視されないワークロードの場合、マネージド Standard ディスクは低コストな代替オプションになります。  
### <a name="cost-saving-in-general"></a>一般的なコスト削減 
#### <a name="significant-vm-cost-saving-with-azure-reservations"></a>Azure の予約による大幅な VM コスト削減: 
現在、すべての CF VM は、環境が通常は無期限に稼働状態であっても、"オンデマンド" 料金を使用して課金されます。 しかし、1 年または 3 年の期間で VM 容量を予約し、45 から 65% の割引を受けることができるようになりました。 割引は、環境を変更せずに、課金システムに適用されます。 詳細については、[Azure の予約のしくみ](https://azure.microsoft.com/pricing/reserved-vm-instances/)に関するページを参照してください。 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>より小さいサイズのマネージド Premium ディスク: 
マネージド ディスクでは、Premium と Standard の両方のディスクで、P4 (32 GB) や P6 (64 GB) などのより小さいディスク サイズがサポートされています。 ワークロードが小さい場合は、標準の Premium ディスクをマネージド Premium ディスクに移行して、コストを節約することができます。
#### <a name="use-azure-first-party-services"></a>Azure ファースト パーティ サービスの使用: 
Azure のファースト パーティ サービスの利点を活用すると、前のセクションで説明した HA と信頼性の他に、長期的な管理コストも削減することができます。 

Pivotal は、PCF ユーザー向けに [Small Footprint ERT](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html) の提供を開始しました。コンポーネントは、4 つの VM だけに配置され、最大で 2,500 のアプリケーション インスタンスを実行します。 評価版は、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry) を通じて利用できるようになりました。

## <a name="next-steps"></a>次の手順
Azure の統合機能は、Pivotal Cloud Foundry で利用できるようになる前に、まずは [Open Source Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/) で利用することができます。 \* のマークが付いている機能は、PCF ではまだ利用できません。 Azure Stack との Cloud Foundry 統合も、このドキュメントでは説明されていません。
\* のマークが付いている機能の PCF サポート、または Azure Stack との Cloud Foundry 統合の最新の状況については、Pivotal および Microsoft のアカウント マネージャーに問い合わせてください。 

