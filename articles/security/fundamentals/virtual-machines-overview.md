---
title: Azure VM で使用されるセキュリティ機能
titleSuffix: Azure security
description: この記事では、Azure 仮想マシンで使用できる Azure のコア セキュリティ機能の概要について説明します。
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/2/2019
ms.author: terrylan
ms.openlocfilehash: 036a1d3f8cad4ba9cda03a9001cfec1bb7de86b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101715039"
---
# <a name="azure-virtual-machines-security-overview"></a>Azure 仮想マシンのセキュリティの概要
この記事では、仮想マシンで使用できるコアの Azure セキュリティ機能の概要を示します。

Azure Virtual Machines を使うと、さまざまなコンピューティング ソリューションを俊敏にデプロイできます。 このサービスは、Microsoft Windows、Linux、Microsoft SQL Server、Oracle、IBM、SAP、および Azure BizTalk Services をサポートします。 したがって、ほぼすべてのオペレーティング システム上に任意のワークロードと言語を展開できます。

Azure Virtual Machine は、仮想マシンを実行する物理的なハードウェアを購入して維持する手間を省き、仮想化がもたらす柔軟性を提供します。 高度にセキュリティ保護されたデータセンターでデータが保護されているという安心感を持って、アプリケーションを構築して展開できます。

Azure によって、セキュリティが強化され、コンプライアンスに準拠している、以下を可能にするソリューションを構築できます。

* 仮想マシンをウイルスやマルウェアから保護します。
* 機密データを暗号化します。
* ネットワーク トラフィックをセキュリティで保護します。
* 脅威を識別して検出します。
* コンプライアンス要件を満たします。  

## <a name="antimalware"></a>マルウェア対策

Azure では、Microsoft、Symantec、Trend Micro、Kaspersky などのセキュリティ ベンダーのマルウェア対策ソフトウェアを使用できます。 このソフトウェアは、悪意のあるファイル、アドウェア、他の脅威から仮想マシンを保護するのに役立ちます。

Azure Cloud Services および 仮想マシン に対する Microsoft マルウェア対策は、ウイルス、スパイウェアなどの悪意のあるソフトウェアの特定や駆除に役立つリアルタイムの保護機能です。  Azure の Microsoft マルウェア対策は、既知の悪意あるまたは望ましくないソフトウェアが Azure システム上に自動でインストールまたは実行されそうになった場合に、構成可能なアラートを提供します。

Azure の Microsoft マルウェア対策は、アプリケーションやテナント環境のための単一エージェント ソリューションです。 ユーザーの介入なしにバックグラウンドで実行するように設計されています。 アプリケーションのワークロードのニーズに基づいて、マルウェア対策監視など、基本的な既定のセキュリティまたは高度なカスタム構成で、保護をデプロイできます。

詳細については、[Azure 向け Microsoft Antimalware](antimalware.md) と利用可能なコア機能に関するページを参照してください。

仮想マシンを保護するマルウェア対策ソフトウェアの詳細については、以下を参照してください。

* [Azure Virtual Machines へのマルウェア対策ソリューションのデプロイ](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Windows VM に Trend Micro Deep Security をサービスとしてインストールし、構成する方法](/previous-versions/azure/virtual-machines/extensions/trend)
* [Windows VM に Symantec Endpoint Protection をインストールし、構成する方法](../../virtual-machines/extensions/symantec.md)
* [Azure Marketplace のセキュリティ ソリューション](https://azure.microsoft.com/marketplace/?term=security)

さらに強力な保護のためには、[Windows Defender の Advanced Threat Protection](/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection) を使用することを検討してください。 Windows Defender の ATP を使用すると、以下が得られます。

* [攻撃対象領域の縮小](/windows/security/threat-protection/windows-defender-atp/overview-attack-surface-reduction)  
* [次世代の保護](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)  
* [エンドポイントの保護と対応](/windows/security/threat-protection/windows-defender-atp/overview-endpoint-detection-response)
* [調査と修復の自動化](/windows/security/threat-protection/windows-defender-atp/automated-investigations-windows-defender-advanced-threat-protection)
* [セキュリティ スコア](/windows/security/threat-protection/microsoft-defender-atp/tvm-microsoft-secure-score-devices)
* [高度な検出](/windows/security/threat-protection/windows-defender-atp/overview-hunting-windows-defender-advanced-threat-protection)
* [管理と API](/windows/security/threat-protection/windows-defender-atp/management-apis)
* [Microsoft の脅威保護](/windows/security/threat-protection/windows-defender-atp/threat-protection-integration)

詳細情報:

* [WDATP の使用を開始する](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)  
* [WDATP 機能の概要](/windows/security/threat-protection/windows-defender-atp/overview)  

## <a name="hardware-security-module"></a>ハードウェア セキュリティ モジュール

キーのセキュリティを高めると、暗号化と認証による保護を強化できます。 大切な秘密情報とキーを Azure Key Vault に格納して、それらの管理とセキュリティ保護をシンプルにできます。

Key Vault では、オプションとして、キーを保管するためのハードウェア セキュリティ モジュール (HSM) が提供されています。HSM は FIPS 140-2 レベル 2 標準に準拠しています。 バックアップまたは [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) 用の SQL Server 暗号化キーに加えて、アプリケーションのすべてのキーや秘密情報を Key Vault に格納できます。 保護されたこれらのアイテムに対するアクセス許可とアクセスは、[Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/) を通して管理されます。

詳細情報:

* [Azure Key Vault とは](../../key-vault/general/overview.md)
* [Azure Key Vault ブログ](/archive/blogs/kv/)

## <a name="virtual-machine-disk-encryption"></a>仮想マシン ディスクの暗号化

Azure Disk Encryption は、Windows および Linux 仮想マシン ディスクを暗号化する新機能です。 Azure Disk Encryption では、Windows の業界標準である [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) 機能と Linux の [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) 機能を使用して、OS およびデータ ディスクのボリュームの暗号化を提供します。

このソリューションは Azure Key Vault と統合されており、ディスクの暗号化キーとシークレットは Key Vault サブスクリプションで制御および管理できます。 仮想マシン ディスク内のすべてのデータが、Azure Storage での保存時に暗号化されることを保証します。

詳細情報:

* [IaaS VM の Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md)
* [クイック スタート: Azure PowerShell を使用して Windows IaaS VM を暗号化する](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)

## <a name="virtual-machine-backup"></a>仮想マシンのバックアップ

Azure Backup は、設備投資なしで、また最小限の運用コストでアプリケーション データを保護できる、スケーラブルなソリューションです。 アプリケーション エラーが発生するとデータが破損するおそれがあり、ヒューマン エラーが生じればアプリケーションにバグが生まれる危険があります。 Azure Backup により、Windows と Linux で実行されている仮想マシンが保護されます。

詳細情報:

* [Azure Backup とは](../../backup/backup-overview.md)
* [Azure Backup サービス FAQ](../../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery

組織の BCDR 戦略において重要となるのは、計画済みおよび計画外の停止が発生した場合に企業のワークロードとアプリを継続して実行する方法を見極めることです。 Azure Site Recovery は、ワークロードとアプリのレプリケーション、フェールオーバー、および復旧を調整するため、1 次拠点がダウンした場合でも 2 次拠点からワークロードとアプリを利用できます。

Site Recovery:

* **BCDR 戦略を簡素化**:Site Recovery では、1 か所から複数のビジネス ワークロードとアプリのレプリケーション、フェールオーバー、および復旧を簡単に処理できます。 Site Recovery はレプリケーションとフェールオーバーを調整しますが、アプリケーション データをインターセプトすることや、そのデータに関する情報を持つことはありません。
* **柔軟なレプリケーション機能の提供**:Site Recovery を使うことで、Hyper-V 仮想マシン、VMware 仮想マシン、および Windows または Linux の物理サーバーで実行されているワークロードをレプリケートできます。
* **フェールオーバーと復旧のサポート**:Site Recovery では、運用環境に影響を与えずにディザスター リカバリーの練習ができるよう、テスト フェールオーバーの機能が用意されています。 また、予期された停止の場合はデータ損失ゼロの計画されたフェールオーバーを実行し、予期しない停止の場合は (レプリケーションの頻度に応じた) 最小限のデータ損失で計画外のフェールオーバーを実行することもできます。 フェールオーバー後は、プライマリ サイトにフェールバックできます。 Site Recovery に用意されている復旧計画には、多層アプリケーションのフェールオーバーと復旧をカスタマイズできるように、スクリプトや Azure Automation ブックが含まれています。
* **セカンダリ データセンターを排除**:オンプレミスのセカンダリ サイトまたは Azure にレプリケートできます。 ディザスター リカバリーのためのレプリケーション先として Azure を使用すると、セカンダリ サイトの管理に伴うコストと手間が削減されます。 レプリケートされたデータは Azure Storage に格納されます。
* **既存の BCDR テクノロジとの統合**:Site Recovery は、その他のアプリケーションの BCDR 機能と連携します。 たとえば、Site Recovery を使用すると、企業のワークロードの SQL Server バックエンドを保護できます。 これには、SQL Server Always On による可用性グループのフェールオーバーの管理のネイティブ サポートが含まれます。

詳細情報:

* [Azure Site Recovery とは](../../site-recovery/site-recovery-overview.md)
* [Azure Site Recovery のしくみ](../../site-recovery/azure-to-azure-architecture.md)
* [Azure Site Recovery によって保護されるワークロードの種類](../../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>仮想ネットワーク

仮想マシンには、ネットワーク接続が必要です。 その要件に対応するため、Azure では、仮想マシンによる Azure 仮想ネットワークへの接続が必要となります。

Azure 仮想ネットワークは、物理的な Azure ネットワーク ファブリック上に構築される論理的な構築物です。 各論理 Azure 仮想ネットワークは、他のすべての Azure 仮想ネットワークから分離されています。 この分離は、他の Microsoft Azure ユーザーによるデプロイ内のネットワーク トラフィックへのアクセスを防ぐ上で役立ちます。

詳細情報:

* [Azure のネットワーク セキュリティの概要](network-overview.md)
* [仮想ネットワークの概要](../../virtual-network/virtual-networks-overview.md)
* [エンタープライズ シナリオ向けのネットワーク機能とパートナーシップ](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>セキュリティ ポリシーの管理とレポート

Azure Security Center は、脅威の防御、検出、対応を可能にする機能です。 Security Center により、Azure リソースのセキュリティの可視化を向上させ、コントロールすることができます。 Azure サブスクリプション間のセキュリティ監視とポリシー管理を総合的に提供します。 Security Center は、見つけにくい脅威の検出を支援すると共に、さまざまなセキュリティ ソリューションをまとめた広範なエコシステムとして機能します。

Security Center は、仮想マシンのセキュリティの最適化と監視に役立つ次の機能を備えています。

* 仮想マシンの[セキュリティに関する推奨事項](../../security-center/security-center-recommendations.md)の提供。 推奨事項の例としては、システム更新プログラムの適用、ACL エンドポイント、マルウェア対策の有効化、ネットワーク セキュリティ グループの有効化、ディスク暗号化の適用などがあります。
* 仮想マシンの状態の監視。

詳細情報:

* [Azure Security Center 入門](../../security-center/security-center-introduction.md)
* [Azure Security Center についてよく寄せられる質問](../../security-center/faq-general.md)
* [Azure Security Center 計画および運用](../../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>コンプライアンス

Azure Virtual Machines は、FISMA、FedRAMP、HIPAA、PCI DSS レベル 1、その他の主要なコンプライアンス プログラムの認定を受けています。 この認定により、Azure アプリケーションをコンプライアンス要件に準拠させ、広範に及ぶ国内および国際的な規制の要件にビジネスを対応させることが容易になります。

詳細情報:

* [Microsoft Trust Center:コンプライアンス](https://www.microsoft.com/en-us/trustcenter/compliance)
* [信頼できるクラウド:Microsoft Azure のセキュリティ、プライバシー、コンプライアンス](https://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)

## <a name="confidential-computing"></a>Confidential Computing

Confidential Computing は、技術的には仮想マシンのセキュリティの一部ではありません。仮想マシンのセキュリティのトピックは、"コンピューティング" のセキュリティの、高レベルのテーマに属します。 Confidential Computing は、カテゴリとしては、"コンピューティング" のセキュリティに属しています。

Confidential Computing により、データが効率的な処理のために必要な "クリアの状態" にあるとき、データは信頼できる実行環境 https://en.wikipedia.org/wiki/Trusted_execution_environment (TEE - エンクレーブとも呼ばれます) 内で確実に保護されるようになっています。その例を、下の図に示しています。  

TEE によって、外部からは、デバッガーを使用しても内部のデータや操作を見る方法がないようになっています。 それらは、承認されたコードのみがデータへのアクセスを許可されるようにさえしています。 コードが変更されたり改ざんされたりしている場合、操作は拒否され、環境が無効にされます。 TEE は、その内部でのコードの実行全体を通して、これらの保護を強制します。

詳細情報:

* [Azure Confidential Computing の概要](https://azure.microsoft.com/blog/introducing-azure-confidential-computing/)  
* [Azure Confidential Computing](https://azure.microsoft.com/blog/azure-confidential-computing/)  

## <a name="next-steps"></a>次のステップ

VM とオペレーティング システムの[セキュリティに関するベスト プラクティス](iaas.md)について説明します。