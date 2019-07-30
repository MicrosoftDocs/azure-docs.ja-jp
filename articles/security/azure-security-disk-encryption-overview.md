---
title: Azure Disk Encryption とは
description: この記事では、Azure Disk Encryption の概要を示します
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 58c5c8321e505fe2c1c7d19c58fe0d031b75b3e4
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67294859"
---
# <a name="azure-disk-encryption-overview"></a>Azure Disk Encryption の概要

Azure Disk Encryption は、データを保護して、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。 これは、Azure Virtual Machines (VM) の OS およびデータ ディスクのボリュームを暗号化するために、Windows の [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) 機能と Linux の [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 機能を使用します。 また、[Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) と統合されています。これにより、ディスク暗号化キーとシークレットを制御および管理するのが容易になり、Azure Storage で保存中の VM ディスクの全データが確実に暗号化されます。 Windows および Linux VM 用の Azure Disk Encryption は、すべての Azure パブリック リージョンおよび Azure Government リージョンの Standard VM と Azure Premium Storage を使用する VM で一般に提供されています。 

Azure Security Center を使用している場合、暗号化されていない VM があると警告を受け取ります。 アラートは高重要度として表示され、このような VM は暗号化することをお勧めします。

![Azure Security Center のディスク暗号化アラート](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> 特定の推奨事項により、データ、ネットワーク、またはコンピューティング リソースの使用量が増え、その結果、ライセンスまたはサブスクリプション コストの追加が必要になる可能性があります。


## <a name="encryption-scenarios"></a>暗号化のシナリオ

Azure Disk Encryption では、業界標準の暗号化テクノロジを使用して保存中の Azure VM をセキュリティで保護することで、組織のセキュリティおよびコンプライアンス要件に対処できます。 また、お客様が管理するキーとポリシー (BYOK) に従って VM が起動するように構成し、Key Vault 内のこれらのキーの使用を監査することもできます。

Azure Disk Encryption では、次の顧客シナリオがサポートされます。

* サポートされている Azure ギャラリー イメージから作成された新しい VM で暗号化を有効および無効にする。
* Azure で実行されている既存の VM で暗号化を有効および無効にする。
* 事前に暗号化した VHD と暗号化キーから作成された新しい Windows VM で暗号化を有効および無効にする。
* Windows 仮想マシン スケール セットで暗号化を有効および無効にする。
* Linux 仮想マシン スケール セットのデータ ドライブで暗号化を有効および無効にする。
* マネージド ディスク VM の暗号化を有効および無効にする。
* 既存の暗号化された Premium Storage VM および非 Premium Storage VM の暗号化設定を更新する。
* 暗号化された VM をバックアップおよび復元する。
* お客様が独自の暗号化キーを使用し、それらを Azure Key Vault に保存する、独自の暗号化の使用 (BYOE) および独自のキーの使用 (BYOK) シナリオ。

また、Microsoft Azure で有効になっている場合、VM に関する次のシナリオがサポートされます。

* Azure Key Vault との統合。
* [最小メモリ要件](azure-security-disk-encryption-prerequisites.md#supported-vm-sizes)を満たす [Standard レベルの VM](https://azure.microsoft.com/pricing/details/virtual-machines/)。 
* サポートされている Azure ギャラリー イメージの Windows および Linux VM、マネージド ディスク、およびスケール セット VM で暗号化を有効にする。
* Windows VM、スケール セット VM、およびマネージド ディスク VM の OS およびデータ ドライブで暗号化を無効にする。
* Linux VM、スケール セット VM、およびマネージド ディスク VM のデータ ドライブで暗号化を無効にする。
* Windows クライアント OS を実行している VM で暗号化を有効にする。
* ボリュームのマウント パスでの暗号化を有効にする。
* mdadm を使用してディスク ストライピング (RAID) が構成されている Linux VM で暗号化を有効にする。
* データ ディスクに LVM を使用している Linux VM で暗号化を有効にする。
* Linux VM の OS ディスクとデータ ディスクで暗号化を有効にする。

   > [!NOTE]
   > 一部の Linux ディストリビューションでは、OS ドライブの暗号化はサポートされていません。 詳しくは、[Azure Disk Encryption でサポートされるオペレーティング システム: Linux](azure-security-disk-encryption-prerequisites.md#linux) に関するページをご覧ください。
   
* Windows Server 2016 以降の Windows 記憶域スペースを使用して構成されている VM で暗号化を有効にする。
* キー暗号化キー (KEK) シナリオと非 KEK シナリオの両方で暗号化された VM をバックアップおよび復元する。

Azure Disk Encryption は、次のシナリオ、機能、およびテクノロジには対応していません。

* Basic レベルの VM または従来の VM の作成方法を使用して作成された VM を暗号化する。
* OS ドライブが暗号化されている場合に Linux VM の OS ドライブまたはデータ ドライブで暗号化を無効にする。
* Linux 仮想マシン スケール セットの OS ドライブを暗号化する。
* ソフトウェア ベースの RAID システムを使用して構成されている Windows VM を暗号化する。
* Linux VM のカスタム イメージを暗号化する。
* オンプレミスのキー管理システムとの統合。
* Azure Files (共有ファイル システム)。
* ネットワーク ファイル システム (NFS)。
* 動的ボリューム。

## <a name="encryption-features"></a>暗号化機能

Azure VM の Azure Disk Encryption を有効にしてデプロイすると、次の機能が有効になるように構成できます。

* ストレージに保存中のブート ボリュームを保護するために OS ボリュームを暗号化する。
* ストレージに保存中のデータ ボリュームを保護するためにデータ ボリュームを暗号化する。
* Windows VM の OS ドライブおよびデータ ドライブで暗号化を無効にする。
* Linux VM のデータ ドライブで暗号化を無効にする (OS ドライブが暗号化されていない場合のみ)。
* Azure Key Vault サブスクリプションの暗号化キーとシークレットを保護する。
* 暗号化された VM の暗号化状態をレポートする。
* VM からディスク暗号化構成設定を削除する。
* Azure Backup サービスを使用して、暗号化された VM をバックアップおよび復元する。

Windows および Linux 用の VM の Azure Disk Encryption には以下が含まれます。

* [Windows のディスク暗号化拡張機能](../virtual-machines/extensions/azure-disk-enc-windows.md)。
* [Linux のディスク暗号化拡張機能](../virtual-machines/extensions/azure-disk-enc-linux.md)。
* [PowerShell ディスク暗号化コマンドレット](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.2.0)。
* [Azure CLI ディスク暗号化コマンドレット](/cli/azure/vm/encryption?view=azure-cli-latest)。
* [Azure Resource Manager ディスク暗号化テンプレート](azure-security-disk-encryption-appendix.md#resource-manager-templates)。

> [!NOTE]
> Azure Disk Encryption による VM ディスクの暗号化による追加料金は発生しません。 暗号化キーの格納に使用するキー コンテナーには標準の [Key Vault 価格](https://azure.microsoft.com/pricing/details/key-vault/)が適用されます。 

## <a name="encryption-workflow"></a>暗号化のワークフロー

Windows および Linux VM のディスク暗号化を有効にするには、次の手順を実行します。

1. Azure Disk Encryption Resource Manager テンプレート、PowerShell コマンドレット、または Azure CLI を使用してディスク暗号化を有効にすることを選択し、暗号化の構成を指定します。

   * お客様が暗号化した VHD の場合は、暗号化した VHD をストレージ アカウントにアップロードし、暗号化キー マテリアルを Key Vault にアップロードします。 その後、暗号化構成を指定して新しい VM での暗号化を有効にします。
   * サポートされているギャラリー イメージから作成された新しい VM と、Azure で既に実行されている既存の VM については、VM での暗号化を有効にするための暗号化構成を指定します。

1. 暗号化キー マテリアル (Windows システムの場合は BitLocker 暗号化キー、Linux の場合はパスフレーズ) を Key Vault から読み取るためのアクセス権を Azure プラットフォームに付与し、VM での暗号化を有効にします。

1. Azure は、暗号化と Key Vault の構成を使用して VM サービス モデルを更新し、暗号化された VM をセットアップします。

   ![Azure での Microsoft マルウェア対策](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>暗号化解除のワークフロー
VM のディスク暗号化を無効にするには、次の手順概要に従います。

1. Azure で実行中の VM に対して暗号化を無効に (解除) するよう選択し、暗号化解除の構成を指定します。 Azure Disk Encryption Resource Manager テンプレート、PowerShell コマンドレット、または Azure CLI を使用して無効にすることができます。

   この手順によって、Windows VM で実行されている OS またはデータ ボリューム、もしくはその両方の暗号化が無効になります。 前のセクションで述べたように、Linux で OS ディスクの暗号化を無効にすることはサポートされていません。 OS ディスクが暗号化されていない限り、暗号化解除の手順は、Linux VM のデータ ドライブでのみ許可されます。

1. Azure によって VM サービス モデルが更新され、VM は暗号化解除済みとしてマークされます。 VM の内容は保存時に暗号化されなくなります。

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

   * お客様が暗号化した VHD の場合は、暗号化した VHD をストレージ アカウントにアップロードし、暗号化キー マテリアルを Key Vault にアップロードします。 その後、暗号化構成を指定して新しい VM での暗号化を有効にします。
   * Marketplace から作成された新しい VM と、Azure で既に実行されている既存の VM については、VM での暗号化を有効にするための暗号化構成を指定します。

1. 暗号化キー マテリアル (Windows システムの場合は BitLocker 暗号化キー、Linux の場合はパスフレーズ) を Key Vault から読み取るためのアクセス権を Azure プラットフォームに付与し、VM での暗号化を有効にします。

1. Key Vault に暗号化キー マテリアルを書き込むための Azure AD アプリケーション ID を指定します。 この手順によって、手順 2 で述べたシナリオについて、VM での暗号化が有効になります。

1. Azure は、暗号化と Key Vault の構成を使用して VM サービス モデルを更新し、暗号化された VM をセットアップします。


## <a name="terminology"></a>用語集
次の表では、Azure Disk Encryption のドキュメントで使用される一般的な用語の一部を定義します。

| 用語集 | 定義 |
| --- | --- |
| Azure AD | [Azure AD](https://azure.microsoft.com/documentation/services/active-directory/) アカウントは、Key Vault のシークレットの認証、格納、および取得を行うために使用されます。 |
| Azure Key Vault | Key Vault は、Federal Information Processing Standards (FIPS) に照らして検証されたハードウェア セキュリティ モジュールに基づく、暗号化キー管理サービスです。 これらの標準は、暗号化キーと機密性の高いシークレットを保護するために役立ちます。 詳しくは、[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) のドキュメントをご覧ください。 |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) は、Windows VM でディスク暗号化を有効にするために使用される、業界で認められた Windows ボリューム暗号化テクノロジです。 |
| BEK | BitLocker 暗号化キー (BEK) は、OS ブート ボリュームとデータ ボリュームの暗号化に使用されます。 BEK は、Key Vault でシークレットとして保護されます。 |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) は、コマンド ラインから Azure リソースを管理できるように最適化されています。|
| DM-Crypt |[DM-Crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) は、Linux VM でディスク暗号化を有効にするために使用される Linux ベースの透過的なディスク暗号化サブシステムです。 |
| キー暗号化キー (KEK) | シークレットを保護またはラップするために使用できる非対称キー (RSA 2048) です。 ハードウェア セキュリティ モジュール (HSM) で保護されたキーまたはソフトウェアで保護されたキーを指定できます。 詳しくは、[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) のドキュメントをご覧ください。 |
| PowerShell コマンドレット | 詳しくは、[Azure PowerShell コマンドレット](/powershell/azure/overview)に関するページをご覧ください。 |

## <a name="next-steps"></a>次の手順

最初に「[Azure Disk Encryption の前提条件](azure-security-disk-encryption-prerequisites.md)」をご覧ください。

