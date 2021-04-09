---
title: Azure Site Recovery を使用した Azure への VMware ディザスター リカバリーのソースを設定する
description: この記事では、Azure Site Recovery を使用して VMware VM を Azure にレプリケートするためのオンプレミスの環境を設定する方法について説明します。
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: afd3979690b8952c915a49099ee04b3d416031fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88189731"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Azure への VMware レプリケーションのソース環境を設定する

この記事では、VMware VM を Azure にレプリケートするためのオンプレミスのソース環境を設定する方法について説明します。 この記事には、レプリケーション シナリオを選択するための手順、オンプレミス コンピューターを Site Recovery の構成サーバーとして設定するための手順、オンプレミスの VM を自動的に検出するための手順が含まれます。

## <a name="prerequisites"></a>前提条件

この記事は、既に以下の操作を行っていることを前提としています。

- [Azure Site Recovery Deployment Planner](site-recovery-deployment-planner.md) を使用して、デプロイを計画した。 これにより、毎日のデータ変化率に基づいて十分な帯域幅を割り当て、必要な復旧ポイント目標 (RPO) を満たすことができます。
- [Azure Portal](https://portal.azure.com) で[リソースを設定する](tutorial-prepare-azure.md)。
- [オンプレミスの VMware 設定する](vmware-azure-tutorial-prepare-on-premises.md) (自動検出のための専用アカウントを含む)。

## <a name="choose-your-protection-goals"></a>保護の目標を選択する

1. **[Recovery Services コンテナー]** で、コンテナー名を選択します。 このシナリオでは、**ContosoVMVault** を使います。
2. **[作業の開始]** で、[Site Recovery] を選択します。 次に、 **[インフラストラクチャの準備]** を選択します。
3. **[保護の目標]**  >  **[マシンのある場所]** で、 **[オンプレミス]** を選びます。
4. **[マシンをどこにレプリケートしますか]** で、 **[To Azure]\(Azure\)** を選びます。
5. **[マシンは仮想化されていますか]** で、 **[はい (VMware vSphere ハイパーバイザー の場合)]** を選びます。 **[OK]** をクリックします。

## <a name="set-up-the-configuration-server"></a>構成サーバーを設定する

Open Virtualization Application (OVA) テンプレートを使用し、構成サーバーをオンプレミスの VMware VM として設定できます。 VMware VM にインストールされるコンポーネントについては、[こちら](./vmware-azure-architecture.md)をご覧ください。

1. 構成サーバー デプロイの[前提条件](vmware-azure-deploy-configuration-server.md#prerequisites)を確認します。
2. デプロイに必要な[容量を確認](vmware-azure-deploy-configuration-server.md#sizing-and-capacity-requirements)します。
3. OVA テンプレートを[ダウンロード](vmware-azure-deploy-configuration-server.md#download-the-template)して[インポート](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware)し、構成サーバーを実行するオンプレミス VMware VM を設定します。 テンプレートに付属するライセンスは評価版ライセンスとなり、180 日間有効です。 この期間が経過した後は、入手済みのライセンスを使用して Windows のライセンス認証をユーザーが行う必要があります。
4. VMware VM を有効にし、Recovery Services コンテナーに[登録](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services)します。

## <a name="azure-site-recovery-folder-exclusions-from-antivirus-program"></a>ウイルス対策プログラムからの Azure Site Recovery フォルダーの除外

### <a name="if-antivirus-software-is-active-on-source-machine"></a>ウイルス対策ソフトウェアがソース マシンでアクティブな場合

ソース マシンにアクティブなウイルス対策ソフトウェアがある場合、インストール フォルダーを除外する必要があります。 そのため、スムーズなレプリケーションのために、フォルダー *C:\ProgramData\ASR\agent* を除外します。

### <a name="if-antivirus-software-is-active-on-configuration-server"></a>ウイルス対策ソフトウェアが構成サーバーでアクティブな場合

スムーズなレプリケーションと接続の問題を回避するために、ウイルス対策ソフトウェアから次のフォルダーを除外します

- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\Program Files\Microsoft Azure Site Recovery Provider
- C:\Program Files\Microsoft Azure Site Recovery Configuration Manager 
- C:\Program Files\Microsoft Azure Site Recovery Error Collection Tool 
  - C:\thirdparty
  - C:\Temp
  - C:\strawberry
  - C:\ProgramData\MySQL
  - C:\Program Files (x86)\MySQL
  - C:\ProgramData\ASR
  - C:\ProgramData\Microsoft Azure Site Recovery
  - C:\ProgramData\ASRLogs
  - C:\ProgramData\ASRSetupLogs
  - C:\ProgramData\LogUploadServiceLogs
  - C:\inetpub
  - Site Recovery サーバーのインストール ディレクトリ。 次に例を示します。E:\Program Files (x86)\Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-scale-out-process-servermaster-target"></a>ウイルス対策ソフトウェアがスケールアウト プロセス サーバー/マスター ターゲットでアクティブな場合

ウイルス対策ソフトウェアから次のフォルダーを除外します

1. C:\Program Files\Microsoft Azure Recovery Services Agent
2. C:\ProgramData\ASR
3. C:\ProgramData\ASRLogs
4. C:\ProgramData\ASRSetupLogs
5. C:\ProgramData\LogUploadServiceLogs
6. C:\ProgramData\Microsoft Azure Site Recovery
7. Azure Site Recovery 負荷分散プロセス サーバーのインストール ディレクトリ。例: C:\Program Files (x86)\Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-the-linux-master-target"></a>ウイルス対策ソフトウェアが Linux マスター ターゲットで有効になっている場合

ウイルス対策ソフトウェアから次のフォルダーを除外します

1.  /usr/local/ASR
2.  /usr/local/InMage
3.  /var/log/vxlogs
4.  /var/log
5.  /var/log/ApplicationPolicyLogs
6.  /var/log/ASRsetuptelemetry
7.  /var/log/ASRsetuptelemetry_uploaded


## <a name="next-steps"></a>次のステップ
[ターゲット環境をセットアップする](./vmware-azure-set-up-target.md) 
