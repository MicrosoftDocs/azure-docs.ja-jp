---
title: Azure Migrate での移行に向けた Windows Server 2003 サーバーの準備
description: Azure Migrate での移行に向けて Windows Server 2003 サーバーを準備する方法について説明します。
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: 5e33742d59972d491c1efb8d0f1453c1226d4625
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86103944"
---
# <a name="prepare-windows-server-2003-machines-for-migration"></a>移行に向けた Windows Server 2003 コンピューターの準備

この記事では、Azure への移行のために Windows Server 2003 を実行するコンピューターを準備する方法について説明します。 

- エージェントレスの移行を使用して [Hyper-V VM](tutorial-migrate-hyper-v.md) と [VMware VM](tutorial-migrate-vmware.md) を Azure に移行できます。
- 移行後に Azure VM に接続するには、Hyper-V 統合サービスを Azure VM にインストールする必要があります。 既定では、Windows Server 2003 コンピューターにはインストールされていません。
- Hyper-V 統合サービスをインストールするための直接ダウンロード リンクはありません。そのため、次の手順を実行する必要があります。
    - Hyper-V VM にインストールされていない場合は、Hyper-V ロールを持つ Windows Server 2012 R2/Windows Server 2012 を実行しているコンピューターに統合サービスのインストール ファイルを抽出し、そのインストーラーを Windows Server 2003 コンピューターにコピーします。 インストール ファイルは、Windows Server 2016 を実行しているコンピューターでは使用できません。
    - VMware VM の場合は、移行後に Azure VM が起動したときに統合サービスをインストールするスタートアップ タスクを作成します。


## <a name="install-on-hyper-v-vms"></a>Hyper-V VM にインストールする

移行の前に、Hyper-V 統合サービスがインストールされているかどうかを確認し、必要に応じてインストールします。

1. インストールされているかどうかを確認するには、[次の手順](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#turn-an-integration-service-on-or-off-using-hyper-v-manager)に従います。
2. インストールされていない場合は、Hyper-V ロールを持つ Windows Server 2012 R2/Windows Server 2012 を実行しているコンピューターにサインインします。
3. インストール ファイル **C:\Windows\System32\vmguest.iso** を探し、ファイルをマウントします。
2. インストール フォルダーを Windows Server 2003 コンピューターにコピーし、統合サービスをインストールします。
4. インストール後は、統合サービスで既定の設定をそのまま使用できます。 

## <a name="install-on-vmware-vms"></a>VMware VM にインストールする

1. Hyper-V ロールを持つ Windows Server 2012 R2/Windows Server 2012 を実行しているコンピューターにサインインします。
2. インストール ファイル **C:\Windows\System32\vmguest.iso** を探し、ファイルをマウントします。
3. インストール フォルダーを VMware VM にコピーします。
4. VM のコマンド ラインで ```gpedit.msc``` を実行します。
5. **[コンピューターの構成]**  >  **[Windows の設定]**  >  **[スクリプト (スタートアップ/シャットダウン)]** を開きます。
6. **[スタートアップ]**  >  **[追加]**  >  **[スクリプト名]** で、setup.exe のアドレスを入力します。
7. Azure への移行後、Azure VM の初回起動時にスクリプトが実行されます。
8. Azure VM を手動で再起動します。 ブート診断には、再起動が必要であることを示すポップアップが表示されます。
9. スクリプトが実行され、Hyper-V 統合サービスが Azure VM にインストールされたら、スタートアップからスクリプトを削除できます。
10. インストール後は、統合サービスで既定の設定をそのまま使用できます。 

## <a name="next-steps"></a>次のステップ

- [VMware](migrate-support-matrix-vmware-migration.md) と [Hyper-V](migrate-support-matrix-hyper-v-migration.md) の VM の移行要件を確認します。
- [VMware](server-migrate-overview.md) と [Hyper-V](tutorial-migrate-hyper-v.md) の VM を移行します。
