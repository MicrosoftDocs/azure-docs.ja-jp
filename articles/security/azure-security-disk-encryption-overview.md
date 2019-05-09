---
title: 概要 - IaaS VM のための Azure Disk Encryption | Microsoft Docs
description: この記事では、IaaS VM の Microsoft Azure Disk Encryption の概要を示します。
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/16/2019
ms.custom: seodec18
ms.openlocfilehash: 66d788aec83e3e57a49b063f2ca80484360f639d
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295285"
---
# <a name="azure-disk-encryption-for-iaas-vms"></a>IaaS VM の Azure Disk Encryption

Microsoft Azure では、利用者のデータのプライバシーとデータ主権の確保に積極的に取り組んでいます。 Azure を使用することで、利用者は、暗号化キーを暗号化し、制御し、管理すると共に、データのアクセスを制御および監査するための幅広い高度なテクノロジを通じて、Azure でホストされているデータを制御できます。 この制御により、Azure の利用者はビジネス ニーズに最適なソリューションを柔軟に選択することができます。 この記事では、次の技術ソリューションについて説明します。"Windows および Linux の IaaS 仮想マシン (VM) のための Azure Disk Encryption"。 この技術は、データを保護して、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]


## <a name="overview"></a>概要

Azure Disk Encryption は、Windows と Linux の IaaS VM のディスクを暗号化するのに役立つ機能です。 Disk Encryption では、Windows の業界標準である [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) 機能と Linux の [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 機能を利用して、OS とデータ ディスクのボリュームの暗号化が提供されます。 このソリューションは、ディスクの暗号化キーとシークレットを制御および管理できるように、[Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) と統合されています。 またこのソリューションでは、VM ディスク上のすべてのデータが、Azure Storage での保存時に暗号化されます。

Windows および Linux IaaS VM 用の Disk Encryption は、すべての Azure パブリック リージョンおよび Azure Government リージョンの Standard VM と Azure Premium Storage を使用する VM で一般に提供されています。 Disk Encryption の管理ソリューションを使用すると、次のビジネス ニーズに対応できます。

* 業界標準の暗号化テクノロジを使用して保存中の IaaS VM をセキュリティで保護し、組織のセキュリティおよびコンプライアンス要件に対処します。
* 顧客が管理するキーとポリシーを使用して IaaS VM が起動します。 お使いのキー コンテナーの使用状況を監査できます。

Azure Security Center を使用している場合、暗号化されていない VM があると警告を受け取ります。 アラートは高重要度として表示され、このような VM は暗号化することをお勧めします。

![Azure Security Center のディスク暗号化アラート](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> 特定の推奨事項により、データ、ネットワーク、またはコンピューティング リソースの使用量が増え、その結果、ライセンスまたはサブスクリプション コストの追加が必要になる可能性があります。


## <a name="encryption-scenarios"></a>暗号化のシナリオ

Disk Encryption ソリューションでは、次の顧客シナリオがサポートされています。

* 事前に暗号化した VHD と暗号化キーから作成された新しい Windows IaaS VM での暗号化を有効にする。
* サポートされている Azure ギャラリー イメージから作成された新しい IaaS VM での暗号化を有効にする。
* Azure で実行されている既存の IaaS VM での暗号化を有効にする。
* Windows 仮想マシン スケール セットでの暗号化を有効にする。
* Linux 仮想マシン スケール セットのデータ ドライブでの暗号化を有効にする。
* Windows IaaS VM での暗号化を無効にする。
* Linux IaaS VM でのデータ ドライブの暗号化を無効にする。
* Windows 仮想マシン スケール セットでの暗号化を無効にする。
* Linux 仮想マシン スケール セットのデータ ドライブでの暗号化を無効にする。
* マネージド ディスク VM の暗号化を有効にする。
* 既存の暗号化された Premium および Premium 以外のストレージ VM の暗号化設定を更新する。
* 暗号化された VM をバックアップおよび復元する。

このソリューションでは、Microsoft Azure で IaaS VM が有効になっている場合、IaaS VM の以下のシナリオがサポートされます。

* Azure Key Vault との統合。
* Standard レベルの VM:[A、D、DS、G、GS、F などの IaaS VM シリーズ](https://azure.microsoft.com/pricing/details/virtual-machines/)。 これらのレベルの [Linux VM](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) が最小メモリ要件 (7 GB) を満たしている必要があります。
* サポートされている Azure ギャラリー イメージの Windows および Linux IaaS VM、マネージド ディスク、およびスケール セット VM での暗号化を有効にする。
* Windows IaaS VM、スケール セット VM、およびマネージド ディスク VM の OS およびデータ ドライブでの暗号化を無効にする。
* Linux IaaS VM、スケール セット VM、およびマネージド ディスク VM のデータ ドライブでの暗号化を無効にする。
* Windows クライアント OS を実行している IaaS VM での暗号化を有効にする。
* ボリュームのマウント パスでの暗号化を有効にする。
* mdadm を使用してディスク ストライピング (RAID) が構成されている Linux VM の暗号化を有効にする。
* データ ディスクに LVM を使用している Linux VM で暗号化を有効にする。
* Linux VM の OS ディスクとデータ ディスクで暗号化を有効にする。

   > [!NOTE]
   > 一部の Linux ディストリビューションでは、OS ドライブの暗号化はサポートされていません。 詳しくは、「[Azure Disk Encryption に関する FAQ](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport)」をご覧ください。
   
* Windows Server 2016 以降の Windows 記憶域スペースを使用して構成されている VM の暗号化を有効にする。
* 既存の暗号化された Premium および Premium 以外のストレージ VM の暗号化設定を更新する。
* キー暗号化キー (KEK) シナリオと非 KEK シナリオ両方で暗号化された VM をバックアップおよび復元する。
* すべての Azure パブリック リージョンと Azure Government リージョンをサポートする。

このソリューションでは、以下のシナリオ、機能、およびテクノロジはサポートされていません。

* Basic レベルの IaaS VM。
* Linux IaaS VM での OS ドライブの暗号化を無効にする。
* OS ドライブが Linux IaaS VM で暗号化されている場合にデータ ドライブの暗号化を無効にする。
* Linux 仮想マシン スケール セットの OS ドライブの暗号化。
* 従来の VM の作成方法を使用して作成された IaaS VM。
* Linux IaaS VM で顧客カスタム イメージの暗号化を有効にする。
* オンプレミスのキー管理システムとの統合。
* Azure Files (共有ファイル システム)。
* ネットワーク ファイル システム (NFS)。
* 動的ボリューム。
* ソフトウェア ベースの RAID システムを使用して構成されている Windows VM。

## <a name="encryption-features"></a>暗号化機能

Azure IaaS VM 用の Disk Encryption を有効にしてデプロイすると、提供される構成に応じて、以下の機能が有効になります。

* ストレージに保存中のブート ボリュームを保護するために OS ボリュームを暗号化する。
* ストレージに保存中のデータ ボリュームを保護するためにデータ ボリュームを暗号化する。
* Windows IaaS VM での OS およびデータ ドライブの暗号化を無効にする。
* Linux IaaS VM のデータ ドライブでの暗号化を無効にする (OS ドライブが暗号化されていない場合のみ)。
* Azure Key Vault サブスクリプションの暗号化キーとシークレットを保護する。
* 暗号化された IaaS VM の暗号化状態をレポートする。
* IaaS VM からディスク暗号化構成設定を削除する。
* 暗号化された VM を Azure Backup サービスでバックアップおよび復元する。

Windows および Linux IaaS VM の Azure Disk Encryption ソリューションには、以下のものが含まれています。

* Windows のディスク暗号化拡張機能
* Linux のディスク暗号化拡張機能
* PowerShell ディスク暗号化コマンドレット。
* Azure CLI ディスク暗号化コマンドレット。
* Azure Resource Manager ディスク暗号化テンプレート。

Azure Disk Encryption ソリューションは、Windows または Linux OS を実行している IaaS VM でサポートされます。 サポートされているオペレーティング システムについて詳しくは、[前提条件](azure-security-disk-encryption-prerequisites.md)に関する記事をご覧ください。

> [!NOTE]
> Azure Disk Encryption による VM ディスクの暗号化による追加料金は発生しません。 暗号化キーの格納に使用するキー コンテナーには標準の [Key Vault 価格](https://azure.microsoft.com/pricing/details/key-vault/)が適用されます。 


## <a name="encryption-workflow"></a>暗号化のワークフロー

Windows および Linux VM のディスク暗号化を有効にするには、次の手順を実行します。

1. 「[暗号化のシナリオ](#encryption-scenarios)」セクションに列記されているシナリオから、暗号化のシナリオを選択します。

1. Azure Disk Encryption Resource Manager テンプレート、PowerShell コマンドレット、または Azure CLI を使用してディスク暗号化を有効にすることを選択し、暗号化の構成を指定します。

   * お客様が暗号化した VHD の場合は、暗号化した VHD をストレージ アカウントにアップロードし、暗号化キー マテリアルを Key Vault にアップロードします。 その後、暗号化構成を指定して新しい IaaS VM での暗号化を有効にします。
   * Marketplace から作成された新しい VM と、Azure で既に実行されている既存の VM については、IaaS VM での暗号化を有効にするための暗号化構成を指定します。

1. 暗号化キー マテリアル (Windows システムの場合は BitLocker 暗号化キー、Linux の場合はパスフレーズ) を Key Vault から読み取るためのアクセス権を Azure プラットフォームに付与し、IaaS VM での暗号化を有効にします。

1. Azure は、暗号化と Key Vault の構成を使用して VM サービス モデルを更新し、暗号化された VM をセットアップします。

   ![Azure での Microsoft マルウェア対策](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>暗号化解除のワークフロー
IaaS VM のディスク暗号化を無効にするには、以下の手順に従います。

1. Azure で実行中の IaaS VM に対して暗号化を無効に (解除) するよう選択し、暗号化解除の構成を指定します。 Azure Disk Encryption Resource Manager テンプレート、PowerShell コマンドレット、または Azure CLI を使用して無効にすることができます。

   この手順によって、Windows IaaS VM で実行されている OS またはデータ ボリューム、もしくはその両方の暗号化が無効になります。 前のセクションで述べたように、Linux で OS ディスクの暗号化を無効にすることはサポートされていません。 OS ディスクが暗号化されていない限り、暗号化解除の手順は、Linux VM のデータ ドライブでのみ許可されます。

1. Azure によって VM サービス モデルが更新され、IaaS VM は暗号化解除済みとしてマークされます。 VM の内容は保存時に暗号化されなくなります。

   > [!NOTE]
   > 暗号化を無効にしても、Key Vault や暗号化キー マテリアル (Windows システムの場合は BitLocker 暗号化キー、Linux の場合はパスフレーズ) は削除されません。
   >
   > Linux 用の OS ディスク暗号化の無効化は、サポートされていません。 暗号化解除の手順は、Linux VM のデータ ドライブでのみ許可されます。
   >
   > OS ドライブが暗号化されている場合、Linux のデータ ディスクの暗号化を無効にする機能はサポートされていません。


## <a name="encryption-workflow-previous-release"></a>暗号化のワークフロー (以前のリリース)

Azure Disk Encryption の新しいリリースでは、VM ディスク暗号化を有効にするために Azure Active Directory (Azure AD) アプリケーション パラメーターを指定する必要はありません。 新しいリリースでは、暗号化を有効にする手順の途中で、Azure AD の資格情報を指定する必要がなくなりました。 すべての新しい VM は、新しいリリースを使用して、Azure AD アプリケーション パラメーターを指定せずに暗号化する必要があります。 Azure AD アプリケーション パラメーターで既に暗号化された VM はまだサポートされていますが、Azure AD の構文を使用して保持し続ける必要があります。 Windows および Linux VM のディスク暗号化 (以前のリリース) を有効にするには、次の手順に従います。

1. 「[暗号化のシナリオ](#encryption-scenarios)」セクションに列記されているシナリオから、暗号化のシナリオを選択します。

1. Azure Disk Encryption Resource Manager テンプレート、PowerShell コマンドレット、または Azure CLI を使用してディスク暗号化を有効にすることを選択し、暗号化の構成を指定します。

   * お客様が暗号化した VHD の場合は、暗号化した VHD をストレージ アカウントにアップロードし、暗号化キー マテリアルを Key Vault にアップロードします。 その後、暗号化構成を指定して新しい IaaS VM での暗号化を有効にします。
   * Marketplace から作成された新しい VM と、Azure で既に実行されている既存の VM については、IaaS VM での暗号化を有効にするための暗号化構成を指定します。

1. 暗号化キー マテリアル (Windows システムの場合は BitLocker 暗号化キー、Linux の場合はパスフレーズ) を Key Vault から読み取るためのアクセス権を Azure プラットフォームに付与し、IaaS VM での暗号化を有効にします。

1. Key Vault に暗号化キー マテリアルを書き込むための Azure AD アプリケーション ID を指定します。 これにより、手順 2 で述べたシナリオについて、IaaS VM での暗号化が有効になります。

1. Azure は、暗号化と Key Vault の構成を使用して VM サービス モデルを更新し、暗号化された VM をセットアップします。


## <a name="terminology"></a>用語集
次の表では、この技術で使用される一般的な用語の一部を定義します。

| 用語集 | 定義 |
| --- | --- |
| Azure AD | [Azure AD](https://azure.microsoft.com/documentation/services/active-directory/) アカウントは、Key Vault のシークレットの認証、格納、および取得を行うために使用されます。 |
| Azure Key Vault | Key Vault は、Federal Information Processing Standards (FIPS) に照らして検証されたハードウェア セキュリティ モジュールに基づく、暗号化キー管理サービスです。 これらの標準は、暗号化キーと機密性の高いシークレットを保護するために役立ちます。 詳しくは、[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) のドキュメントをご覧ください。 |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) は、Windows IaaS VM でディスク暗号化を有効にするために使用される業界公認の Windows ボリューム暗号化テクノロジです。 |
| BEK | BitLocker 暗号化キー (BEK) は、OS ブート ボリュームとデータ ボリュームの暗号化に使用されます。 BEK は、Key Vault でシークレットとして保護されます。 |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) は、コマンド ラインから Azure リソースを管理できるように最適化されています。|
| DM-Crypt |[DM Crypt](https://en.wikipedia.org/wiki/Dm-crypt) は、Linux IaaS VM でディスク暗号化を有効にするために使用される Linux ベースの透過的なディスク暗号化サブシステムです。 |
| KEK | キー暗号化キー (KEK) は、シークレットを保護またはラップするために使用できる非対称キー (RSA 2048) です。 ハードウェア セキュリティ モジュール (HSM) で保護されたキーまたはソフトウェアで保護されたキーを指定できます。 詳しくは、[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) のドキュメントをご覧ください。 |
| PowerShell コマンドレット | 詳しくは、[Azure PowerShell コマンドレット](/powershell/azure/overview)に関するページをご覧ください。 |

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [Azure Disk Encryption の前提条件](azure-security-disk-encryption-prerequisites.md)
