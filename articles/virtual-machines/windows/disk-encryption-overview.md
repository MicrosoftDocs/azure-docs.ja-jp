---
title: Windows VM 用の Azure Disk Encryption を有効にする
description: この記事では、Windows VM 用の Microsoft Azure Disk Encryption を有効にする手順について説明します。
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/05/2019
ms.custom: seodec18
ms.openlocfilehash: 8e95f770a3335d66eae0a690e148c4d6ddc22d5c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555331"
---
# <a name="azure-disk-encryption-for-windows-vms"></a>Windows VM 用の Azure Disk Encryption

Azure Disk Encryption は、データを保護して、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。 Windows の [BitLocker](https://en.wikipedia.org/wiki/BitLocker) 機能を使用して、Azure 仮想マシン (VM) の OS とデータ ディスクにボリューム暗号化が提供されます。これは、ディスク暗号化キーとシークレットを制御および管理できるように、[Azure Key Vault](../../key-vault/index.yml) に統合されています。

Azure Disk Encryption は、Virtual Machines と同じように、ゾーン回復性を備えています。 詳細については、「[Availability Zones をサポートする Azure サービス](../../availability-zones/az-region.md)」を参照してください。

[Azure Security Center](../../security-center/index.yml) を使用している場合、暗号化されていない VM があると警告を受け取ります。 アラートは高重要度として表示され、このような VM は暗号化することをお勧めします。

![Azure Security Center のディスク暗号化アラート](../media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - これまで Azure AD で Azure Disk Encryption を使用して VM を暗号化していた場合は、引き続きこのオプションを使用して VM を暗号化する必要があります。 詳細については、「[Azure AD での Azure Disk Encryption (以前のリリース)](disk-encryption-overview-aad.md)」を参照してください。 
> - 特定の推奨事項により、データ、ネットワーク、またはコンピューティング リソースの使用量が増え、その結果、ライセンスまたはサブスクリプション コストの追加が必要になる可能性があります。 サポートされているリージョンにおいて Azure でリソースを作成するための有効なアクティブ Azure サブスクリプションが必要です。

[Azure CLI を使用した Windows VM の作成および暗号化のクイックスタート](disk-encryption-cli-quickstart.md)または[Azure PowerShell を使用した Windows VM の作成および暗号化のクイックスタート](disk-encryption-powershell-quickstart.md)のページでは、Windows 用 Azure Disk Encryption の基礎について数分で学習できます。

## <a name="supported-vms-and-operating-systems"></a>サポートされている VM とオペレーティング システム

### <a name="supported-vms"></a>サポート対象の VM

Windows VM は、[さまざまなサイズ](../sizes-general.md)で利用できます。 Azure Disk Encryption は、第 1 世代と第 2 世代の VM でサポートされています。 Azure Disk Encryption は、Premium Storage を使用した VM でも利用できます。

Azure Disk Encryption は、[Basic、A シリーズ VM](https://azure.microsoft.com/pricing/details/virtual-machines/series/)、またはメモリが 2 GB 未満の仮想マシンでは利用できません。  Azure Disk Encryption は、一時ディスクのない VM イメージ (Dv4、Dsv4、Ev4、および Esv4) でも使用できません。  「[ローカル一時ディスクを持たない Azure VM のサイズ](../azure-vms-no-temp-disk.md)」を参照してください。  例外の詳細については、「[Azure Disk Encryption:サポートされていないシナリオ](disk-encryption-windows.md#unsupported-scenarios)に関する記事を参照してください。

### <a name="supported-operating-systems"></a>サポートされるオペレーティング システム

- Windows クライアント: Windows 8 以降
- Windows Server: Windows Server 2008 R2 以降  
 
> [!NOTE]
> Windows Server 2008 R2 には、暗号化用に .NET Framework 4.5 をインストールする必要があります。Windows Update から、オプションの更新プログラムである Windows Server 2008 R2 x64 ベース システム用の Microsoft .NET Framework 4.5.2 ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)) を使用してインストールすることができます。  
>  
> Windows Server 2012 R2 Core と Windows Server 2016 Core には、暗号化用に VM 上に bdehdcfg コンポーネントをインストールする必要があります。


## <a name="networking-requirements"></a>ネットワーク要件
Azure Disk Encryption を有効にするには、VM が次のネットワーク エンドポイントの構成要件を満たす必要があります。
  - ご利用のキー コンテナーに接続するためのトークンを取得するには、Windows VM が Azure Active Directory エンドポイント \[login.microsoftonline.com\] に接続できる必要があります。
  - 暗号化キーをご利用のキー コンテナーに書き込むには、Windows VM がキー コンテナー エンドポイントに接続できる必要があります。
  - Windows VM は、Azure 拡張リポジトリをホストする Azure ストレージ エンドポイントと、VHD ファイルをホストする Azure ストレージ アカウントに接続できる必要があります。
  -  セキュリティ ポリシーで Azure VM からインターネットへのアクセスが制限されている場合は、上記の URI を解決し、IP への送信接続を許可するための特定のルールを構成することができます。 詳細については、「[ファイアウォールの内側にある Azure Key Vault へのアクセス](../../key-vault/general/access-behind-firewall.md)」を参照してください。    


## <a name="group-policy-requirements"></a>グループ ポリシーの要件

Azure Disk Encryption では、Windows VM に対して BitLocker 外部キー保護機能が使用されます。 ドメインに参加している VM の場合は、TPM 保護機能を適用するグループ ポリシーをプッシュしないでください。 "互換性のある TPM が装備されていない BitLocker を許可する" のグループ ポリシーについては、[BitLocker グループ ポリシー リファレンス](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)に関するページをご覧ください。

ドメインに参加済みであり、カスタム グループ ポリシーを使用する仮想マシンでの BitLocker ポリシーには、次の設定を含める必要があります。[[BitLocker 回復情報のユーザー記憶域を構成する] -> [256 ビットの回復キーを許可する]](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings)。 BitLocker のカスタム グループ ポリシー設定に互換性がない場合、Azure Disk Encryption は失敗します。 正しいポリシー設定がないマシンでは、新しいポリシーを適用し、新しいポリシーを強制的に更新して (gpupdate.exe /force)、再起動する処理が必要になる可能性があります。

BitLocker によって使用される AES-CBC アルゴリズムがドメイン レベル グループ ポリシーでブロックされると、Azure Disk Encryption は失敗します。

## <a name="encryption-key-storage-requirements"></a>暗号化キーのストレージ要件  

Azure Disk Encryption では、ディスク暗号化キーとシークレットを制御および管理するために、Azure Key Vault が必要です。 ご利用のキー コンテナーと VM は、同じ Azure リージョンおよびサブスクリプションに存在している必要があります。

詳細については、「[Azure Disk Encryption 用のキー コンテナーの作成と構成](disk-encryption-key-vault.md)」をご覧ください。

## <a name="terminology"></a>用語
次の表では、Azure Disk Encryption のドキュメントで使用される一般的な用語の一部を定義します。

| 用語 | 定義 |
| --- | --- |
| Azure Key Vault | Key Vault は、Federal Information Processing Standards (FIPS) に照らして検証されたハードウェア セキュリティ モジュールに基づく、暗号化キー管理サービスです。 これらの標準は、暗号化キーと機密性の高いシークレットを保護するために役立ちます。 詳細については、[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) のドキュメントと「[Azure Disk Encryption 用のキー コンテナーの作成と構成](disk-encryption-key-vault.md)」をご覧ください。 |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) は、コマンド ラインから Azure リソースを管理できるように最適化されています。|
| BitLocker |[BitLocker](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831713(v=ws.11)) は、Windows VM でディスク暗号化を有効にするために使用される、業界で認められた Windows ボリューム暗号化テクノロジです。 |
| キー暗号化キー (KEK) | シークレットを保護またはラップするために使用できる非対称キー (RSA 2048) です。 ハードウェア セキュリティ モジュール (HSM) で保護されたキーまたはソフトウェアで保護されたキーを指定できます。 詳細については、[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) のドキュメントと「[Azure Disk Encryption 用のキー コンテナーの作成と構成](disk-encryption-key-vault.md)」をご覧ください。 |
| PowerShell コマンドレット | 詳しくは、[Azure PowerShell コマンドレット](/powershell/azure/)に関するページをご覧ください。 |

## <a name="next-steps"></a>次のステップ

- [クイックスタート - Azure CLI を使用して Windows VM を作成、暗号化する](disk-encryption-cli-quickstart.md)
- [クイック スタート - Azure PowerShell を使用して Windows VM を作成、暗号化する](disk-encryption-powershell-quickstart.md)
- [Windows VM での Azure Disk Encryption シナリオ](disk-encryption-windows.md)
- [Azure Disk Encryption の前提条件の CLI スクリプト](https://github.com/ejarvi/ade-cli-getting-started) 
- [Azure Disk Encryption の前提条件の PowerShell スクリプト](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Azure Disk Encryption 用のキー コンテナーの作成と構成](disk-encryption-key-vault.md)