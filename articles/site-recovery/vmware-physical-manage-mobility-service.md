---
title: Azure Site Recovery を使用して VMware VM と物理サーバーをディザスター リカバリーするためにサーバーでモビリティ エージェントを管理する | Microsoft Docs
description: Azure Site Recovery を使用して VMware VM と物理サーバーを Azure にディザスター リカバリーするために、モビリティ サービス エージェントを管理します。
author: rajeswari-mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 69b8e1c533747d1bade69949911ea43f299f49e9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66117322"
---
# <a name="manage-mobility-agent-on-protected-machines"></a>保護されたマシン上のモビリティ エージェントを管理する

Azure への VMware VM と物理サーバーのディザスター リカバリーに Azure Site Recovery を使うときは、ご自身のサーバーでモビリティ エージェントを設定します。 モビリティ エージェントは、保護されたマシン、構成サーバー/スケールアウト プロセス サーバーの間の通信を調整し、データのレプリケーションを管理します。 この記事は、デプロイ後にモビリティ エージェントを管理するための一般的なタスクをまとめたものです。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Azure portal からモビリティ サービスを更新する

1. 保護されたマシン上のモビリティ サービスの更新を開始する前に、デプロイの一部である、構成サーバー、スケールアウト プロセス サーバー、マスター ターゲット サーバーを必ず更新します。
2. ポータルでコンテナーを開き、 **[レプリケートされたアイテム]** を開きます。
3. 構成サーバーが最新版の場合、"Site Recovery レプリケーション エージェントの新しい更新プログラムが利用可能です。 クリックしてインストールしてください" という通知が表示されます。

     ![[レプリケートされたアイテム] ウィンドウ](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. 通知をクリックし、 **[エージェントの更新]** でモビリティ サービスをアップグレードするマシンを選択します。 次に、 **[OK]** をクリックします

     ![[レプリケートされたアイテム] VM リスト](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. 選択したマシンごとに、モビリティ サービスの更新ジョブが開始されます。

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Windows サーバー上の PowerShell スクリプトを介してモビリティ サービスを更新する

次のスクリプトを使用して、PowerShell コマンドレットを介してサーバー上のモビリティ サービスをアップグレードします

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>モビリティ サービスのプッシュ インストールに使用されるアカウントを更新する

Site Recovery をデプロイしたとき、モビリティ サービスのプッシュ インストールを有効にする目的で、アカウントを指定しました。このアカウントは、マシンで複製が有効になっているとき、マシンにアクセスし、サービスをインストールするために Site Recovery プロセス サーバーによって使用されます。 このアカウントの資格情報を更新する場合、[こちらの手順](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)に従ってください。

## <a name="uninstall-mobility-service"></a>モビリティ サービスをアンインストールする

### <a name="on-a-windows-machine"></a>Windows マシンの場合

UI またはコマンド プロンプトからアンインストールします。

- **UI から**:マシンの [コントロール パネル] で **[プログラム]** を選択します。 **[Microsoft Azure Site Recovery Mobility Service/マスター ターゲット サーバー]**  > 、 **[アンインストール]** の順に選択します。
- **コマンド プロンプトから**:マシンの管理者としてコマンド プロンプト ウィンドウを開きます。 次のコマンドを実行します。 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>Linux マシンの場合
1. Linux マシンで**ルート** ユーザーとしてサインインします。
2. ターミナルで /user/local/ASR に移動します。
3. 次のコマンドを実行します。
    ```
    uninstall.sh -Y

## Install Site Recovery VSS provider on source machine

Azure Site Recovery VSS provider is required on the source machine to generate application consistency points. If the installation of the provider didn't succeed through push installation, follow the below given guidelines to install it manually.

1. Open admin cmd window.
2. Navigate to the mobility service installation location. (Eg - C:\Program Files (x86)\Microsoft Azure Site Recovery\agent)
3. Run the script InMageVSSProvider_Uninstall.cmd . This will uninstall the service if it already exists.
4. Run the script InMageVSSProvider_Install.cmd to install the VSS provider manually.

## Next steps

- [Set up disaster recovery for VMware VMs](vmware-azure-tutorial.md)
- [Set up disaster recovery for physical servers](physical-azure-disaster-recovery.md)
