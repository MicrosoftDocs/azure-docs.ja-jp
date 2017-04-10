---
title: "Azure Virtual Machines で使用される Azure のセキュリティ機能 | Microsoft Docs"
description: " Azure Virtual Machines で使用できる Azure のコア セキュリティ機能の概要について説明します。 Azure VM は、その VM を実行する物理的なハードウェアを購入して維持する手間を省き、仮想化がもたらす柔軟性を提供します。 "
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 70d22ae8d7f67f1e12a233e004f1eb749927e7d8
ms.lasthandoff: 03/25/2017


---
# <a name="azure-virtual-machines-security-overview"></a>Azure 仮想マシンのセキュリティの概要
Azure 仮想マシンを使用すると、さまざまなコンピューティング ソリューションを俊敏にデプロイできます。 Microsoft Windows、Linux、Microsoft SQL Server、Oracle、IBM、SAP、Azure BizTalk Services に対応しており、ほぼすべてのオペレーティング システムですべてのワークロード、すべての言語をデプロイできます。

Azure Virtual Machine は、仮想マシンを実行する物理的なハードウェアを購入して維持する手間を省き、仮想化がもたらす柔軟性を提供します。  高度にセキュリティ保護されたデータセンターでデータが保護されているという安心感を持って、アプリケーションを構築してデプロイできます。

Azure によって、セキュリティが強化され、コンプライアンスに準拠した、以下を可能にするソリューションを構築できます。

* 仮想マシンのウイルスやマルウェアからの保護
* 機密データ情報の暗号化
* ネットワーク トラフィックのセキュリティ保護
* 脅威の特定と検出
* コンプラアイアンス要件の充足

この記事の目的は、仮想マシンで使用できる Azure のコア セキュリティ機能の概要を説明することです。 それぞれの詳細について説明する記事へのリンクも用意されているため、さらに詳しく学習できます。  

この記事では、次の Azure 仮想マシンのコア セキュリティ機能について説明します。

* マルウェア対策
* ハードウェア セキュリティ モジュール
* 仮想マシン ディスクの暗号化
* 仮想マシンのバックアップ
* Azure Site Recovery
* 仮想ネットワーク
* セキュリティ ポリシーの管理とレポート
* コンプライアンス

## <a name="antimalware"></a>マルウェア対策
Azure では、Microsoft、Symantec、Trend Micro、McAfee、Kaspersky などのセキュリティ ベンダーが提供するマルウェア対策ソフトウェアを利用できます。これにより、悪意のあるファイルやアドウェアなどの脅威から仮想マシンを保護できます。 パートナー ソリューションに関する記事については、後述の「詳細情報:」を参照してください。

Azure Cloud Services および 仮想マシン に対する Microsoft マルウェア対策は、ウイルス、スパイウェアなどの悪意のあるソフトウェアの特定や駆除に役立つリアルタイムの保護機能です。  Microsoft マルウェア対策は、既知の悪意あるまたは望ましくないソフトウェアが Azure システム上に自動でインストールまたは実行されそうになった場合に、構成可能なアラートを提供します。

Microsoft マルウェア対策は、アプリケーションおよびテナント環境のための単一エージェント ソリューションであり、ユーザーの介入なしにバック グラウンドで実行されるように設計されています。 アプリケーションのワークロードのニーズに基づいて、マルウェア対策監視など、基本的な既定のセキュリティまたは高度なカスタム構成で、保護をデプロイできます。

Microsoft マルウェア対策をデプロイして有効にすると、次のコア機能を使用できるようになります。

* リアルタイム保護 - Cloud Services および仮想マシンでのアクティビティを監視し、マルウェアの実行を検出してブロックします。
* スケジュールに基づくスキャン - 特定対象のスキャンを定期的に実行し、マルウェアや活動量の多いプログラムを検出します。
* マルウェアの駆除 - 悪意のあるファイルの削除や検疫、悪意のあるレジストリ エントリのクリーンアップなど、検出されたマルウェアへの対処を自動的に行います。
* シグネチャの更新 - 最新の保護シグネチャ (ウイルスの定義) を自動的にインストールし、事前に定義された頻度で保護を最新の状態に更新します。
* マルウェア対策エンジンの更新 - Microsoft マルウェア対策エンジンを自動的に更新します。
* マルウェア対策プラットフォームの更新 - Microsoft マルウェア対策プラットフォームを自動的に更新します。
* アクティブ保護 - 検出された脅威および疑わしいリソースに関するテレメトリ メタデータを Azure に報告して迅速に対応し、Microsoft Active Protection System (MAPS) を使用して同期されたシグネチャをリアルタイムで配信できるようにします。
* サンプルのレポート - Microsoft マルウェア対策サービスにサンプルを提供および報告し、サービスの調整およびトラブルシューティングを可能にします。
* 除外 – パフォーマンスやその他の理由で、アプリケーションおよびサービスの管理者が特定のファイル、プロセス、ドライブを保護から除外できるようにします。
* マルウェア対策イベントの収集 - マルウェア対策サービスの状態、疑わしいアクティビティ、および実行された修復アクションをオペレーティング システムのイベント ログに記録し、顧客の Azure ストレージ アカウントにそれらを収集します。

詳細情報: 仮想マシンを保護するマルウェア対策ソフトウェアの詳細については、以下を参照してください。

* [Azure Cloud Services および Virtual Machines 向け Microsoft マルウェア対策](azure-security-antimalware.md)
* [Azure Virtual Machines へのマルウェア対策ソリューションのデプロイ](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Windows VM に Trend Micro Deep Security をサービスとしてインストールし、構成する方法](../virtual-machines/windows/classic/install-trend.md)
* [Windows VM に Symantec Endpoint Protection をインストールし、構成する方法](../virtual-machines/windows/classic/install-symantec.md)
* [New Antimalware Options for Protecting Azure Virtual Machines – McAfee Endpoint Protection (Azure Virtual Machines を保護するための新しいマルウェア対策オプション – McAfee Endpoint Protection)](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)
* [Azure Marketplace のセキュリティ ソリューション](https://azure.microsoft.com/marketplace/?term=security)

## <a name="hardware-security-module"></a>ハードウェア セキュリティ モジュール
暗号化と認証は、キー自体が保護されない限り、セキュリティを向上させません。 大切な秘密情報とキーを Azure Key Vault に格納して、それらの管理とセキュリティ保護をシンプルにできます。 Key Vault では、オプションとして、キーを保管するためのハードウェア セキュリティ モジュール (HSM) が提供されています。HSM は FIPS 140-2 レベル 2 標準に準拠しています。 バックアップまたは [Transparent Data Encryption](https://msdn.microsoft.com/library/bb934049.aspx) 用の SQL Server 暗号化キーに加えて、アプリケーションのすべてのキーや秘密情報を Key Vault に格納できます。 保護されたこれらのアイテムに対するアクセス許可とアクセスは、[Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/) を通して管理されます。

詳細情報:

* [Azure Key Vault とは](../key-vault/key-vault-whatis.md)
* [Azure Key Vault の概要](../key-vault/key-vault-get-started.md)
* [Azure Key Vault ブログ](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>仮想マシン ディスクの暗号化
Azure Disk Encryption は、Windows および Linux Azure 仮想マシン ディスクを暗号化できる新機能です。 Azure Disk Encryption では、Windows の業界標準である [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) 機能と Linux の [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) 機能を使用して、OS およびデータ ディスクのボリュームの暗号化を提供します。

ソリューションは Azure Key Vault と統合されています。これは、Key Vault サブスクリプションでディスク暗号化キーとシークレットを制御および管理し、Azure ストレージで保存中の仮想マシン ディスク内のすべてのデータを確実に暗号化する場合に役立ちます。

詳細情報:

* [Windows および Linux IaaS VM の Azure ディスク暗号化](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)
* [Linux および Windows 仮想マシン向け Azure Disk Encryption](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview-now-available/)
* [仮想マシンの暗号化](../security-center/security-center-disk-encryption.md)

## <a name="virtual-machine-backup"></a>仮想マシンのバックアップ
Azure Backup は、設備投資なしで、また最小限の運用コストでアプリケーション データを保護できる、スケーラブルなソリューションです。 アプリケーション エラーが発生するとデータが破損するおそれがあり、ヒューマン エラーが生じればアプリケーションにバグが生まれる危険があります。 Azure Backup により、Windows と Linux で実行されている仮想マシンが保護されます。

詳細情報:

* [Azure Backup とは](../backup/backup-introduction-to-azure-backup.md)
* [Azure Backup のラーニング パス](https://azure.microsoft.com/documentation/learning-paths/backup/)
* [Azure Backup サービス - FAQ](../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery
組織の BCDR 戦略において重要となるのは、計画済みおよび計画外の停止が発生した場合に企業のワークロードとアプリを継続して実行する方法を見極めることです。 Azure Site Recovery は、ワークロードとアプリのレプリケーション、フェールオーバー、および復旧を調整するため、1 次拠点がダウンした場合でも 2 次拠点からワークロードとアプリを利用できます。

Site Recovery:

* **BCDR 戦略の簡素化** - Site Recovery では、1 か所から複数のビジネス ワークロードとアプリのレプリケーション、フェールオーバー、および復旧を簡単に処理できます。 Site recovery はレプリケーションとフェールオーバーを調整しますが、アプリケーション データをインターセプトすることや、そのデータに関する情報を持つことはありません。
* **柔軟なレプリケーション機能の提供** - Site Recovery を使用すると、Hyper-V 仮想マシン、VMware 仮想マシン、および Windows または Linux の物理サーバーで実行されているワークロードをレプリケートできます。
* **簡単なフェールオーバーと復旧** - Site Recovery では、実稼働環境に影響を与えずに障害復旧の演習をサポートするテスト フェールオーバーを実行できます。 また、予期された停止の場合はデータ損失ゼロの計画されたフェールオーバーを実行し、予期しない停止の場合は (レプリケーションの頻度に応じた) 最小限のデータ損失で計画外のフェールオーバーを実行することもできます。 フェールオーバー後は、プライマリ サイトにフェールバックできます。 Site Recovery に用意されている復旧計画には、多層アプリケーションのフェールオーバーと復旧をカスタマイズできるように、スクリプトや Azure Automation ブックが含まれています。
* **セカンダリ データセンターの排除** - オンプレミスのセカンダリ サイトまたは Azure にレプリケートできます。 障害復旧のためのレプリケーション先として Azure を使用すると、セカンダリ サイトの管理に伴うコストと手間が削減されます。 レプリケートされたデータは Azure Storage に格納されます。
* **既存の BCDR テクノロジとの統合** - Site Recovery は、その他のアプリケーションの BCDR 機能と連携します。 たとえば、Site Recovery を使用すると、企業のワークロードの SQL Server バックエンドを保護できます。 これには、SQL Server AlwaysOn による可用性グループのフェールオーバーの管理のネイティブ サポートが含まれます。

詳細情報:

* [Azure Site Recovery とは](../site-recovery/site-recovery-overview.md)
* [Azure Site Recovery のしくみ](../site-recovery/site-recovery-components.md)
* [Azure Site Recovery によって保護されるワークロードの種類](../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>仮想ネットワーク
仮想マシンには、ネットワーク接続が必要です。 その要件に対応するため、Azure では、仮想マシンによる Azure Virtual Network への接続が必要となります。 Azure Virtual Network は、物理的な Azure ネットワーク ファブリック上に構築される論理的な構築物です。 各論理 Azure Virtual Network は、他のすべての Azure Virtual Network から分離されています。 この分離は、他の Microsoft Azure ユーザーによるデプロイ内のネットワーク トラフィックへのアクセスを防ぐ上で役立ちます。

詳細情報:

* [Azure のネットワーク セキュリティの概要](security-network-overview.md)
* [仮想ネットワークの概要](../virtual-network/virtual-networks-overview.md)
* [エンタープライズ シナリオ向けのネットワーク機能とパートナーシップ](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>セキュリティ ポリシーの管理とレポート
Azure Security Center は、脅威の回避、検出、対応に役立つサービスで、Azure リソースのセキュリティを高度に視覚化して制御できます。 これにより、Azure サブスクリプション全体に統合セキュリティの監視とポリシーの管理を提供し、気付かない可能性がある脅威を検出し、セキュリティ ソリューションの広範なエコシステムと連動します。

Azure Security Center は、仮想マシンのセキュリティの最適化と監視に役立つ次の機能を備えています。

* システム更新プログラムの適用、ACL エンドポイント、マルウェア対策の有効化、ネットワーク セキュリティ グループの有効化、ディスク暗号化の適用など、仮想マシンの [セキュリティに関する推奨事項](../security-center/security-center-recommendations.md) の提供。
* 仮想マシンの状態の監視

詳細情報:

* [Azure Security Center 入門](../security-center/security-center-intro.md)
* [Azure セキュリティ センターについてよく寄せられる質問](../security-center/security-center-faq.md)
* [Azure Security Center 計画および運用](../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>コンプライアンス
Azure Virtual Machines は、FISMA、FedRAMP、HIPAA、PCI DSS レベル 1、その他の主要なコンプライアンス プログラムの認定を受けています。 この認定により、Azure アプリケーションをコンプライアンス要件に準拠させ、広範に及ぶ国内および国際的な規制の要件にビジネスを対応させることが容易になります。

詳細情報:

* [Microsoft セキュリティ センター: コンプライアンス](https://www.microsoft.com/TrustCenter/Compliance/default.aspx)
* [信頼されるクラウド: Microsoft Azure のセキュリティ、プライバシー、コンプライアンス](http://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)

