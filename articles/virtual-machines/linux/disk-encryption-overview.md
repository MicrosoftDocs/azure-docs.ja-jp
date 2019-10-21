---
title: Linux VM に対する Azure Disk Encryption を有効にする
description: この記事では、Linux VM で Microsoft Azure Disk Encryption を有効にする手順を説明します。
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 053c52d7d1a0282d72ad76408b77c96aa3b0e3e4
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174702"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Linux VM に対する Azure Disk Encryption 

Azure Disk Encryption は、データを保護して、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。 Linux の [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 機能を使用して、Azure 仮想マシン (VM) の OS とデータ ディスクにボリューム暗号化が提供されます。これは、ディスク暗号化キーとシークレットを制御および管理できるように、[Azure Key Vault](../../key-vault/index.yml) に統合されています。 

[Azure Security Center](../../security-center/index.yml) を使用している場合、暗号化されていない VM があると警告を受け取ります。 アラートは高重要度として表示され、このような VM は暗号化することをお勧めします。

![Azure Security Center のディスク暗号化アラート](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - これまで Azure AD で Azure Disk Encryption を使用して VM を暗号化していた場合は、引き続きこのオプションを使用して VM を暗号化する必要があります。 詳細については、「[Azure AD での Azure Disk Encryption (以前のリリース)](disk-encryption-overview-aad.md)」を参照してください。 
> - 特定の推奨事項により、データ、ネットワーク、またはコンピューティング リソースの使用量が増え、その結果、ライセンスまたはサブスクリプション コストの追加が必要になる可能性があります。 サポートされているリージョンにおいて Azure でリソースを作成するための有効なアクティブ Azure サブスクリプションが必要です。

「[クイックスタート: Azure CLI で Linux VM を作成して暗号化する](disk-encryption-cli-quickstart.md)」または「[クイックスタート: Azure PowerShell で Linux VM を作成して暗号化する](disk-encryption-powershell-quickstart.md)」では、Linux 用 Azure Disk Encryption の基礎について数分で学習できます。

## <a name="supported-vms-and-operating-systems"></a>サポートされている VM とオペレーティング システム

### <a name="supported-vm-sizes"></a>サポートされる VM のサイズ

Linux VM は、[さまざまなサイズ](sizes.md)で利用できます。 Azure Disk Encryption は、[Basic、A シリーズ VM](https://azure.microsoft.com/pricing/details/virtual-machines/series/) または次の最小メモリ要件を満たしていない仮想マシンでは利用できません。

| 仮想マシン | 最小メモリ要件 |
|--|--|
| Linux VM (データ ボリュームのみを暗号化する場合)| 2 GB |
| Linux VM (データ ボリュームと OS ボリュームの両方を暗号化し、なおかつルート (/) ファイル システムの使用量が 4 GB 以下の場合) | 8 GB |
| Linux VM (データ ボリュームと OS ボリュームの両方を暗号化し、なおかつルート (/) ファイル システムの使用量が 4 GB を超える場合) | ルート ファイル システムの使用量 * 2。 たとえば、ルート ファイル システムの使用量が 16 GB の場合、32 GB 以上の RAM が必要となります。 |

Linux 仮想マシンの OS ディスクの暗号化プロセスが完了すると、より少ないメモリで VM を実行するように構成できます。 

Azure Disk Encryption は、Premium Storage を使用した VM でも利用できます。 

### <a name="supported-operating-systems"></a>サポートされているオペレーティング システム

Azure Disk Encryption は [Azure での動作が保証された一部の Linux ディストリビューション](endorsed-distros.md)でサポートされています。Azure での動作が保証された Linux ディストリビューションはそれ自体があらゆる Linux サーバー ディストリビューションの一部となります。

![Azure Disk Encryption をサポートする Linux サーバー ディストリビューションのベン図](./media/disk-encryption/ade-supported-distros.png)

Azure での動作が保証されていない Linux サーバー ディストリビューションでは Azure Disk Encryption がサポートされておらず、動作が保証されているディストリビューションの中でも、次のディストリビューションとバージョンだけで Azure Disk Encryption がサポートされています。

| Linux ディストリビューション | Version | 暗号化がサポートされているボリュームの種類|
| --- | --- |--- |
| Ubuntu | 18.04| OS とデータ ディスク |
| Ubuntu | 16.04| OS とデータ ディスク |
| Ubuntu | 14.04.5</br>[カーネルが 4.15 以降に調整されている Azure](disk-encryption-troubleshooting.md) | OS とデータ ディスク |
| RHEL | 7.7 | OS とデータ ディスク (後述する注を参照してください) |
| RHEL | 7.6 | OS とデータ ディスク (後述する注を参照してください) |
| RHEL | 7.5 | OS とデータ ディスク (後述する注を参照してください) |
| RHEL | 7.4 | OS とデータ ディスク (後述する注を参照してください) |
| RHEL | 7.3 | OS とデータ ディスク (後述する注を参照してください) |
| RHEL | 7.2 | OS とデータ ディスク (後述する注を参照してください) |
| RHEL | 6.8 | データ ディスク (後述する注を参照してください) |
| RHEL | 6.7 | データ ディスク (後述する注を参照してください) |
| CentOS | 7.7 | OS とデータ ディスク |
| CentOS | 7.6 | OS とデータ ディスク |
| CentOS | 7.5 | OS とデータ ディスク |
| CentOS | 7.4 | OS とデータ ディスク |
| CentOS | 7.3 | OS とデータ ディスク |
| CentOS | 7.2n | OS とデータ ディスク |
| CentOS | 6.8 | データ ディスク |
| openSUSE | 42.3 | データ ディスク |
| SLES | 12-SP4 | データ ディスク |
| SLES | 12-SP3 | データ ディスク |

> [!NOTE]
> RHEL7 の従量課金制イメージについては、RHEL OS とデータ ディスクに新しい ADE の実装がサポートされます。 RHEL の BYOS (Bring-Your-Own-Subscription) イメージについては、現在 ADE はサポートされません。 

## <a name="additional-vm-requirements"></a>追加の VM 要件

Azure Disk Encryption では、dm-crypt モジュールと vfat モジュールがシステムに存在している必要があります。 vfat を既定のイメージから削除したり無効にしたりすると、システムはキー ボリュームを読み取って、その後のリブートでディスクのロックを解除するために必要なキーを取得できなくなります。 vfat モジュールをシステムから削除するシステム強化手順は、Azure Disk Encryption とは互換性がありません。 

暗号化を有効にする前に、暗号化するデータ ディスクを /etc/fstab に正しく登録する必要があります。 "/dev/sdX" 形式のデバイス名は、再起動後 (特に暗号化が適用された後) に同じディスクに関連付けられるとは限らないため、このエントリに永続的なブロック デバイス名を使用してください。 この動作の詳細については、次を参照してください:[Linux VM デバイス名の変更トラブルシューティング](troubleshoot-device-names-problems.md)

/etc/fstab 設定がマウントに合わせて正しく構成されていることを確認します。 これらの設定を構成するには、mount -a コマンドを実行するか、VM を再起動してその方法での再マウントをトリガーします。 完了したら、lsblk コマンドの出力を調べて、ドライブがまだマウントされていることを確認します。 
- 暗号化を有効にする前に /etc/fstab ファイルによってドライブが適切にマウントされない場合、Azure Disk Encryption Azure でそれを適切にマウントできません。
- Azure Disk Encryption プロセスは、暗号化プロセスの一部として、/etc/fstab から独自の構成ファイルにマウント情報を移動します。 データ ドライブの暗号化が完了した後、/etc/fstab からそのエントリがなくなっても気にする必要はありません。
- マウントされたデータ ディスクに書き込みを行う可能性のあるサービスとプロセスは、暗号化を開始する前にすべて停止し、それらを無効にして、再起動後に自動的に再開することのないようにしてください。 それが原因でそれらのパーティション上のファイルが開いたままになっていると、暗号化手順で再マウントが阻害され、暗号化に失敗します。 
- 再起動後、新しく暗号化されたディスクが Azure Disk Encryption プロセスによってマウントされる処理には時間がかかります。 再起動後すぐには利用できません。 このプロセスでは、他のプロセスがアクセスできるようになる前に、暗号化されたドライブを起動し、ロックを解除し、マウントする時間が必要です。 システムの特性によっては、再起動後に 1 分以上かかることがあります。

データ ディスクをマウントし、必要な /etc/fstab エントリを作成するために使用できるコマンドの例は、[Azure Disk Encryption の前提条件の CLI スクリプト](https://github.com/ejarvi/ade-cli-getting-started) (244-248 行) および [Azure Disk Encryption の前提条件の PowerShell スクリプト](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)で確認できます。 

## <a name="networking-requirements"></a>ネットワーク要件

Azure Disk Encryption 機能を有効にするには、Linux VM で次のネットワーク エンドポイントの構成要件が満たされている必要があります。
  - お使いのキー コンテナーに接続するためのトークンを取得するには、Linux VM が Azure Active Directory エンドポイント \[login.microsoftonline.com\] に接続できる必要があります。
  - 暗号化キーをお使いのキー コンテナーに書き込むには、Linux VM がキー コンテナー エンドポイントに接続できる必要があります。
  - Linux VM では、Azure 拡張リポジトリがホストされている Azure ストレージ エンドポイントと、VHD ファイルがホストされている Azure ストレージ アカウントに接続できる必要があります。
  -  セキュリティ ポリシーで Azure VM からインターネットへのアクセスが制限されている場合は、上記の URI を解決し、IP への送信接続を許可するための特定のルールを構成することができます。 詳細については、「[ファイアウォールの内側にある Azure Key Vault へのアクセス](../../key-vault/key-vault-access-behind-firewall.md)」を参照してください。  

## <a name="encryption-key-storage-requirements"></a>暗号化キーのストレージ要件  

Azure Disk Encryption では、ディスク暗号化キーとシークレットを制御および管理するために、Azure Key Vault が必要です。 お使いのキー コンテナーと VM は、同じ Azure リージョンおよびサブスクリプションに存在している必要があります。

詳しくは、「[Azure Disk Encryption 用のキー コンテナーの作成と構成](disk-encryption-key-vault.md)」をご覧ください。

## <a name="terminology"></a>用語集
次の表では、Azure Disk Encryption のドキュメントで使用される一般的な用語の一部を定義します。

| 用語集 | 定義 |
| --- | --- |
| Azure Key Vault | Key Vault は、Federal Information Processing Standards (FIPS) に照らして検証されたハードウェア セキュリティ モジュールに基づく、暗号化キー管理サービスです。 これらの標準は、暗号化キーと機密性の高いシークレットを保護するために役立ちます。 詳しくは、[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) のドキュメントと「[Azure Disk Encryption 用のキー コンテナーの作成と構成](disk-encryption-key-vault.md)」をご覧ください。 |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) は、コマンド ラインから Azure リソースを管理できるように最適化されています。|
| DM-Crypt |[DM-Crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) は、Linux VM でディスク暗号化を有効にするために使用される Linux ベースの透過的なディスク暗号化サブシステムです。 |
| キー暗号化キー (KEK) | シークレットを保護またはラップするために使用できる非対称キー (RSA 2048) です。 ハードウェア セキュリティ モジュール (HSM) で保護されたキーまたはソフトウェアで保護されたキーを指定できます。 詳しくは、[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) のドキュメントと「[Azure Disk Encryption 用のキー コンテナーの作成と構成](disk-encryption-key-vault.md)」をご覧ください。 |
| PowerShell コマンドレット | 詳しくは、[Azure PowerShell コマンドレット](/powershell/azure/overview)に関するページをご覧ください。 |


## <a name="next-steps"></a>次の手順

- [クイック スタート - Azure CLI を使用して Linux VM を作成、暗号化する](disk-encryption-cli-quickstart.md)
- [クイック スタート - Azure PowerShell を使用して Linux VM を作成、暗号化する](disk-encryption-powershell-quickstart.md)
- [Linux VM での Azure Disk Encryption シナリオ](disk-encryption-linux.md)
- [Azure Disk Encryption の前提条件の CLI スクリプト](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption の前提条件の PowerShell スクリプト](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Azure Disk Encryption 用のキー コンテナーの作成と構成](disk-encryption-key-vault.md)


