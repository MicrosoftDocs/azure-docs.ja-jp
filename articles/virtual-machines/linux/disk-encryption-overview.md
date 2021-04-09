---
title: Linux VM に対する Azure Disk Encryption を有効にする
description: この記事では、Linux VM で Microsoft Azure Disk Encryption を有効にする手順を説明します。
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 81c026893c3185c6c9f960cdb6acb2d0c2d49cc4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104580353"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Linux VM に対する Azure Disk Encryption 

Azure Disk Encryption は、データを保護して、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。 Linux の [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 機能を使用して、Azure 仮想マシン (VM) の OS とデータ ディスクにボリューム暗号化が提供されます。これは、ディスク暗号化キーとシークレットを制御および管理できるように、[Azure Key Vault](../../key-vault/index.yml) に統合されています。

Azure Disk Encryption は、Virtual Machines と同じように、ゾーン回復性を備えています。 詳細については、「[Availability Zones をサポートする Azure サービス](../../availability-zones/az-region.md)」を参照してください。

[Azure Security Center](../../security-center/index.yml) を使用している場合、暗号化されていない VM があると警告を受け取ります。 アラートは高重要度として表示され、このような VM は暗号化することをお勧めします。

![Azure Security Center のディスク暗号化アラート](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - これまで Azure AD で Azure Disk Encryption を使用して VM を暗号化していた場合は、引き続きこのオプションを使用して VM を暗号化する必要があります。 詳細については、「[Azure AD での Azure Disk Encryption (以前のリリース)](disk-encryption-overview-aad.md)」を参照してください。 
> - 特定の推奨事項により、データ、ネットワーク、またはコンピューティング リソースの使用量が増え、その結果、ライセンスまたはサブスクリプション コストの追加が必要になる可能性があります。 サポートされているリージョンにおいて Azure でリソースを作成するための有効なアクティブ Azure サブスクリプションが必要です。

「[クイックスタート: Azure CLI で Linux VM を作成して暗号化する](disk-encryption-cli-quickstart.md)」または「[クイックスタート: Azure PowerShell で Linux VM を作成して暗号化する](disk-encryption-powershell-quickstart.md)」では、Linux 用 Azure Disk Encryption の基礎について数分で学習できます。

## <a name="supported-vms-and-operating-systems"></a>サポートされている VM とオペレーティング システム

### <a name="supported-vms"></a>サポート対象の VM

Linux VM は、[さまざまなサイズ](../sizes.md)で利用できます。 Azure Disk Encryption は、第 1 世代と第 2 世代の VM でサポートされています。 Azure Disk Encryption は、Premium Storage を使用した VM でも利用できます。

「[ローカル一時ディスクを持たない Azure VM のサイズ](../azure-vms-no-temp-disk.md)」を参照してください。

また、Azure Disk Encryption は、[Basic、A シリーズ VM](https://azure.microsoft.com/pricing/details/virtual-machines/series/) または次の最小メモリ要件を満たしていない仮想マシンでは利用できません。

| 仮想マシン | 最小メモリ要件 |
|--|--|
| Linux VM (データ ボリュームのみを暗号化する場合)| 2 GB |
| Linux VM (データ ボリュームと OS ボリュームの両方を暗号化し、なおかつルート (/) ファイル システムの使用量が 4 GB 以下の場合) | 8 GB |
| Linux VM (データ ボリュームと OS ボリュームの両方を暗号化し、なおかつルート (/) ファイル システムの使用量が 4 GB を超える場合) | ルート ファイル システムの使用量 * 2。 たとえば、ルート ファイル システムの使用量が 16 GB の場合、32 GB 以上の RAM が必要となります。 |

Linux 仮想マシンの OS ディスクの暗号化プロセスが完了すると、より少ないメモリで VM を実行するように構成できます。

例外の詳細については、「[Azure Disk Encryption:サポートされていないシナリオ](disk-encryption-linux.md#unsupported-scenarios)に関する記事を参照してください。

### <a name="supported-operating-systems"></a>サポートされるオペレーティング システム

Azure Disk Encryption は [Azure での動作が保証された一部の Linux ディストリビューション](endorsed-distros.md)でサポートされています。Azure での動作が保証された Linux ディストリビューションはそれ自体があらゆる Linux サーバー ディストリビューションの一部となります。

![Azure Disk Encryption をサポートする Linux サーバー ディストリビューションのベン図](./media/disk-encryption/ade-supported-distros.png)

Azure での動作が保証されていない Linux サーバー ディストリビューションでは Azure Disk Encryption がサポートされておらず、動作が保証されているディストリビューションの中でも、次のディストリビューションとバージョンだけで Azure Disk Encryption がサポートされています。


| Publisher | プラン | SKU | URN | 暗号化がサポートされているボリュームの種類 |
| --- | --- |--- | --- |
| Canonical | Ubuntu | 18.04-LTS | Canonical:UbuntuServer:18.04-LTS:latest | OS とデータ ディスク |
| Canonical | Ubuntu 18.04 | 18.04-DAILY-LTS | Canonical:UbuntuServer:18.04-DAILY-LTS:latest | OS とデータ ディスク |
| Canonical | Ubuntu 16.04 | 16.04-DAILY-LTS | Canonical:UbuntuServer:16.04-DAILY-LTS:latest | OS とデータ ディスク |
| Canonical | Ubuntu 14.04.5</br>[カーネルが 4.15 以降に調整されている Azure](disk-encryption-troubleshooting.md) | 14.04.5-LTS | Canonical:UbuntuServer:14.04.5-LTS:latest | OS とデータ ディスク |
| Canonical | Ubuntu 14.04.5</br>[カーネルが 4.15 以降に調整されている Azure](disk-encryption-troubleshooting.md) | 14.04.5-DAILY-LTS | Canonical:UbuntuServer:14.04.5-DAILY-LTS:latest | OS とデータ ディスク |
| RedHat | RHEL 8-LVM | 8-LVM | RedHat:RHEL:8-LVM:latest | OS とデータ ディスク (後述する注を参照してください) |
| RedHat | RHEL 8.2 | 8.2 | RedHat:RHEL:8.2:latest | OS とデータ ディスク (後述する注を参照してください) |
| RedHat | RHEL 8.1 | 8.1 | RedHat:RHEL:8.1:latest | OS とデータ ディスク (後述する注を参照してください) |
| RedHat | RHEL 7-LVM | 7-LVM | RedHat:RHEL:7-LVM:7.8.2020111201 | OS とデータ ディスク (後述する注を参照してください) |
| RedHat | RHEL 7.8 | 7.8 | RedHat:RHEL:7.8:latest | OS とデータ ディスク (後述する注を参照してください) |
| RedHat | RHEL 7.7 | 7.7 | RedHat:RHEL:7.7:latest | OS とデータ ディスク (後述する注を参照してください) |
| RedHat | RHEL 7.6 | 7.6 | RedHat:RHEL:7.6:latest | OS とデータ ディスク (後述する注を参照してください) |
| RedHat | RHEL 7.5 | 7.5 | RedHat:RHEL:7.5:latest | OS とデータ ディスク (後述する注を参照してください) |
| RedHat | RHEL 7.4 | 7.4 | RedHat:RHEL:7.4:latest | OS とデータ ディスク (後述する注を参照してください) |
| RedHat | RHEL 7.3 | 7.3 | RedHat:RHEL:7.3:latest | OS とデータ ディスク (後述する注を参照してください) |
| RedHat | RHEL 7.2 | 7.2 | RedHat:RHEL:7.2:latest | OS とデータ ディスク (後述する注を参照してください) |
| RedHat | RHEL 6.8 | 6.8 | RedHat:RHEL:6.8:latest | データ ディスク (後述する注を参照してください) |
| RedHat | RHEL 6.7 | 6.7 | RedHat:RHEL:6.7:latest | データ ディスク (後述する注を参照してください) |
| OpenLogic | CentOS 8-LVM | 8-LVM | OpenLogic:CentOS-LVM:8-LVM:latest | OS とデータ ディスク |
| OpenLogic | CentOS 8.2 | 8_2 | OpenLogic:CentOS:8_2:latest | OS とデータ ディスク |
| OpenLogic | CentOS 8.1 | 8_1 | OpenLogic:CentOS:8_1:latest | OS とデータ ディスク |
| OpenLogic | CentOS 7-LVM | 7-LVM | OpenLogic:CentOS-LVM:7-LVM:7.8.2020111100 | OS とデータ ディスク |
| OpenLogic | CentOS 7.8 | 7.8 | OpenLogic:CentOS:7_8:latest | OS とデータ ディスク |
| OpenLogic | CentOS 7.7 | 7.7 | OpenLogic:CentOS:7.7:latest | OS とデータ ディスク |
| OpenLogic | CentOS 7.6 | 7.6 | OpenLogic:CentOS:7.6:latest | OS とデータ ディスク |
| OpenLogic | CentOS 7.5 | 7.5 | OpenLogic:CentOS:7.5:latest | OS とデータ ディスク |
| OpenLogic | CentOS 7.4 | 7.4 | OpenLogic:CentOS:7.4:latest | OS とデータ ディスク |
| OpenLogic | CentOS 7.3 | 7.3 | OpenLogic:CentOS:7.3:latest | OS とデータ ディスク |
| OpenLogic | CentOS 7.2n | 7.2n | OpenLogic:CentOS:7.2n:latest | OS とデータ ディスク |
| OpenLogic | CentOS 7.1 | 7.1 | OpenLogic:CentOS:7.1:latest | データ ディスクのみ |
| OpenLogic | CentOS 7.0 | 7.0 | OpenLogic:CentOS:7.0:latest | データ ディスクのみ |
| OpenLogic | CentOS 6.8 | 6.8 | OpenLogic:CentOS:6.8:latest | データ ディスクのみ |
| SUSE | openSUSE 42.3 | 42.3 | SUSE:openSUSE-Leap:42.3:latest | データ ディスクのみ |
| SUSE | SLES 12-SP4 | 12-SP4 | SUSE:SLES:12-SP4:latest | データ ディスクのみ |
| SUSE | SLES HPC 12-SP3 | 12-SP3 | SUSE:SLES-HPC:12-SP3:latest | データ ディスクのみ |

> [!NOTE]
> RHEL7 の従量課金制イメージについては、RHEL OS とデータ ディスクに新しい Azure Disk Encryption の実装がサポートされます。  
>
> ADE は、RHEL のサブスクリプション持ち込み Gold Image でもサポートされています。ただし、サブスクリプションが登録された **後** でのみサポートされます。 詳細については、「[Azure での Red Hat Enterprise Linux のサブスクリプション持ち込み Gold Image](../workloads/redhat/byos.md#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images)」を参照してください。

## <a name="additional-vm-requirements"></a>追加の VM 要件

Azure Disk Encryption では、dm-crypt モジュールと vfat モジュールがシステムに存在している必要があります。 vfat を既定のイメージから削除したり無効にしたりすると、システムはキー ボリュームを読み取って、その後のリブートでディスクのロックを解除するために必要なキーを取得できなくなります。 vfat モジュールをシステムから削除する、またはデータ ドライブに OS マウントポイント/フォルダーの拡張を強制するシステム強化手順は、Azure Disk Encryption とは互換性がありません。 

暗号化を有効にする前に、暗号化するデータ ディスクを /etc/fstab に正しく登録する必要があります。 エントリを作成するときに "nofail" オプションを使用して、永続的なブロック デバイス名を選択します (暗号化を実行した後は特に、再起動時に "/dev/sdX" 形式のデバイス名が同じディスクに関連付けられていない可能性があります。この動作の詳細については、「[Linux VM デバイス名の変更のトラブルシューティング](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems)」を参照してください)。

/etc/fstab 設定がマウントに合わせて正しく構成されていることを確認します。 これらの設定を構成するには、mount -a コマンドを実行するか、VM を再起動してその方法での再マウントをトリガーします。 完了したら、lsblk コマンドの出力を調べて、ドライブがまだマウントされていることを確認します。 

- 暗号化を有効にする前に /etc/fstab ファイルによってドライブが適切にマウントされない場合、Azure Disk Encryption Azure でそれを適切にマウントできません。
- Azure Disk Encryption プロセスは、暗号化プロセスの一部として、/etc/fstab から独自の構成ファイルにマウント情報を移動します。 データ ドライブの暗号化が完了した後、/etc/fstab からそのエントリがなくなっても気にする必要はありません。
- マウントされたデータ ディスクに書き込みを行う可能性のあるサービスとプロセスは、暗号化を開始する前にすべて停止し、それらを無効にして、再起動後に自動的に再開することのないようにしてください。 それが原因でそれらのパーティション上のファイルが開いたままになっていると、暗号化手順で再マウントが阻害され、暗号化に失敗します。 
- 再起動後、新しく暗号化されたディスクが Azure Disk Encryption プロセスによってマウントされる処理には時間がかかります。 再起動後すぐには利用できません。 このプロセスでは、他のプロセスがアクセスできるようになる前に、暗号化されたドライブを起動し、ロックを解除し、マウントする時間が必要です。 システムの特性によっては、再起動後に 1 分以上かかることがあります。

データ ディスクをマウントし、必要な /etc/fstab エントリを作成するために使用できるコマンドの例は、次のとおりです。

```bash
UUID0="$(blkid -s UUID -o value /dev/sda1)"
UUID1="$(blkid -s UUID -o value /dev/sda2)"
mkdir /data0
mkdir /data1
echo "UUID=$UUID0 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "UUID=$UUID1 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```
## <a name="networking-requirements"></a>ネットワーク要件

Azure Disk Encryption 機能を有効にするには、Linux VM で次のネットワーク エンドポイントの構成要件が満たされている必要があります。
  - お使いのキー コンテナーに接続するためのトークンを取得するには、Linux VM が Azure Active Directory エンドポイント \[login.microsoftonline.com\] に接続できる必要があります。
  - 暗号化キーをお使いのキー コンテナーに書き込むには、Linux VM がキー コンテナー エンドポイントに接続できる必要があります。
  - Linux VM では、Azure 拡張リポジトリがホストされている Azure ストレージ エンドポイントと、VHD ファイルがホストされている Azure ストレージ アカウントに接続できる必要があります。
  -  セキュリティ ポリシーで Azure VM からインターネットへのアクセスが制限されている場合は、上記の URI を解決し、IP への送信接続を許可するための特定のルールを構成することができます。 詳細については、「[ファイアウォールの内側にある Azure Key Vault へのアクセス](../../key-vault/general/access-behind-firewall.md)」を参照してください。  

## <a name="encryption-key-storage-requirements"></a>暗号化キーのストレージ要件  

Azure Disk Encryption では、ディスク暗号化キーとシークレットを制御および管理するために、Azure Key Vault が必要です。 ご利用のキー コンテナーと VM は、同じ Azure リージョンおよびサブスクリプションに存在している必要があります。

詳細については、「[Azure Disk Encryption 用のキー コンテナーの作成と構成](disk-encryption-key-vault.md)」をご覧ください。

## <a name="terminology"></a>用語

次の表では、Azure Disk Encryption のドキュメントで使用される一般的な用語の一部を定義します。

| 用語 | 定義 |
| --- | --- |
| Azure Key Vault | Key Vault は、Federal Information Processing Standards (FIPS) に照らして検証されたハードウェア セキュリティ モジュールに基づく、暗号化キー管理サービスです。 これらの標準は、暗号化キーと機密性の高いシークレットを保護するために役立ちます。 詳細については、[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) のドキュメントと「[Azure Disk Encryption 用のキー コンテナーの作成と構成](disk-encryption-key-vault.md)」をご覧ください。 |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) は、コマンド ラインから Azure リソースを管理できるように最適化されています。|
| DM-Crypt |[DM-Crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) は、Linux VM でディスク暗号化を有効にするために使用される Linux ベースの透過的なディスク暗号化サブシステムです。 |
| キー暗号化キー (KEK) | シークレットを保護またはラップするために使用できる非対称キー (RSA 2048) です。 ハードウェア セキュリティ モジュール (HSM) で保護されたキーまたはソフトウェアで保護されたキーを指定できます。 詳細については、[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) のドキュメントと「[Azure Disk Encryption 用のキー コンテナーの作成と構成](disk-encryption-key-vault.md)」をご覧ください。 |
| PowerShell コマンドレット | 詳しくは、[Azure PowerShell コマンドレット](/powershell/azure/)に関するページをご覧ください。 |


## <a name="next-steps"></a>次のステップ

- [クイック スタート - Azure CLI を使用して Linux VM を作成、暗号化する](disk-encryption-cli-quickstart.md)
- [クイック スタート - Azure PowerShell を使用して Linux VM を作成、暗号化する](disk-encryption-powershell-quickstart.md) 
- [Linux VM での Azure Disk Encryption シナリオ](disk-encryption-linux.md)
- [Azure Disk Encryption の前提条件の CLI スクリプト](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption の前提条件の PowerShell スクリプト](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Azure Disk Encryption 用のキー コンテナーの作成と構成](disk-encryption-key-vault.md)