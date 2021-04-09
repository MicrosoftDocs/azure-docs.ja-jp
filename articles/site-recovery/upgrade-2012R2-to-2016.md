---
title: Windows Server/System Center VMM 2012 R2 を Windows Server 2016-Azure Site Recovery にアップグレードする
description: Azure Site Recovery を使用して構成された Windows Server 2012 R2 ホストと SCVMM 2012 R2 を Windows Server 2016 と SCVMM 2016 にアップグレードする方法について説明します。
services: site-recovery
author: Sharmistha-Rai
manager: gaggupta
ms.topic: conceptual
ms.service: site-recovery
ms.date: 12/03/2018
ms.author: sharrai
ms.openlocfilehash: b9869ae7dfbf5afd6b8d3b870a2ad4e56fd54c1a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91250070"
---
# <a name="upgrade-windows-server-serversystem-center-2012-r2-vmm-to-windows-servervmm-2016"></a>Windows Server Server/System Center 2012 R2 VMM を Windows Server/VMM 2016 にアップグレードする 

この記事では、Azure Site Recovery を使用して構成された Windows Server 2012 R2 ホストと SCVMM 2012 R2 を Windows Server 2016 と SCVMM 2016 にアップグレードする方法について説明します

Site Recovery は、事業継続とディザスター リカバリー (BCDR) 戦略に貢献します。 想定内または想定外の障害が発生したときに、このサービスを使って、VM ワークロードが引き続き利用可能な状態を維持できるようにします。

> [!IMPORTANT]
> Azure Site Recovery によるレプリケーション用に既に構成されている Windows Server 2012 R2 ホストをアップグレードする場合は、このドキュメントに記載されている手順に従う必要があります。 アップグレードに他の方法を選択した場合は、サポートされていない状態になったり、フェールオーバーを実行する機能またはレプリケーションが中断したりする可能性があります。


この記事では、お客様の環境にある以下の構成をアップグレードする方法について説明します。

> [!div class="checklist"]
> * **SCVMM によって管理されていない Windows Server 2012 R2 ホスト** 
> * **スタンドアロンの SCVMM 2012 R2 サーバーによって管理されている Windows Server 2012 R2 ホスト** 
> * **高可用性の SCVMM 2012 R2 サーバーによって管理されている Windows Server 2012 R2 ホスト**


## <a name="prerequisites--factors-to-consider"></a>前提条件と検討するべき要素

アップグレードする前に、次の点に注意してください。

- SCVMM によって管理されていない Windows Server 2012 R2 ホストがあり、それがスタンドアロン環境のセットアップである場合、アップグレードを実行しようとすると、レプリケーションが中断されます。
- 最初に、SCVMM 2012 R2 のインストール中に "*キーを Active Directory の分散キー管理に格納しない*" ことを選択した場合、アップグレードは正常に完了しません。

- System Center 2012 R2 の VMM を使用している場合は、 

    - VMM でデータベース情報を確認します。 **[VMM コンソール]**  ->  **[設定]**  ->  **[標準]**  ->  **[データベース接続]**
    - System Center Virtual Machine Manager エージェント サービスのために使用されているサービス アカウントを確認します
    - VMM データベースのバックアップがあることを確認します。
    - 関係する SCVMM サーバーのデータベース名をメモします。 これは、 **[VMM コンソール]**  ->  **[設定]**  ->  **[標準]**  ->  **[データベース接続]** の順に移動して行えます
    - 2012R2 のプライマリ VMM サーバーと復旧 VMM サーバーの両方の VMM ID をメモしておきます。 VMM ID は、レジストリ "HKLM:\SOFTWARE\Microsoft\Microsoft System Center Virtual Machine Manager Server\Setup" で確認できます。
    - クラスターに追加する新しい SCVMM の名前が以前と同じであることを確認します。 

- どちらも SCVMM によって管理されているお客様の 2 つのサイト間でレプリケートしている場合は、プライマリ側をアップグレードする前に、必ず復旧側を先にアップグレードします。
  > [!WARNING]
  > SCVMM 2012 R2 のアップグレード中に、[分散キー管理] で **暗号化キーを Active Directory に格納する** ことを選択します。 サービス アカウントおよび分散キー管理の設定は、慎重に選択します。 お客様の選択によっては、テンプレート内のパスワードなど、暗号化されたデータがアップグレード後に使用できなくなり、Azure Site Recovery によるレプリケーションに影響が及ぶ可能性があります

> [!IMPORTANT]
> 詳細な SCVMM ドキュメントの[前提条件](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#requirements-and-limitations)を参照してください

## <a name="windows-server-2012-r2-hosts-which-arent-managed-by-scvmm"></a>SCVMM によって管理されていない Windows Server 2012 R2 ホスト 
以下で説明されている各手順は、この[チュートリアル](./hyper-v-prepare-on-premises-tutorial.md)に従って実行された [Hyper-V ホストから Azure へ](./hyper-v-azure-architecture.md)のユーザー構成に適用されます

> [!WARNING]
> 前提条件で説明したとおり、これらの手順はクラスター環境シナリオだけに適用され、スタンドアロンの Hyper-V ホスト構成には適用されません。

1. [クラスターのローリング アップグレード](/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process)の手順に従って、 クラスターのローリング アップグレード プロセスを実行します。
2. クラスターに導入された新しい Windows Server 2016 ホストごとに、[こちら] に記載されている手順に従って、Azure Site Recovery から Windows Server 2012 R2 ホストの参照を削除します。 これは、クラスターからのドレインおよび無効化を選択したホストである必要があります。
3. すべての仮想マシンに対して *Update-VMVersion* コマンドが実行されたら、アップグレードは完了です。 
4. [こちら](./hyper-v-azure-tutorial.md#set-up-the-source-environment)に記載されている手順を使用して、新しい Windows Server 2016 ホストを Azure Site Recovery に登録します。 Hyper-V サイトは既にアクティブになっているので、新しいホストをクラスターに登録するだけで済むことに注意してください。 
5.  Azure portal に移動し、[Recovery Services] で、レプリケートされた正常性状態を確認します

## <a name="upgrade-windows-server-2012-r2-hosts-managed-by-stand-alone-scvmm-2012-r2-server"></a>スタンドアロンの SCVMM 2012 R2 サーバーによって管理されている Windows Server 2012 R2 ホストのアップグレード
お客様の Windows Sever 2012 R2 ホストをアップグレードする前に、SCVMM 2012 R2 を SCVMM 2016 にアップグレードする必要があります。 以下の手順に従ってください。

**スタンドアロンの SCVMM 2012 R2 を SCVMM 2016 にアップグレードする**

1.  [コントロール パネル]、[プログラム]、[プログラムと機能]、[Microsoft Azure Site Recovery] の順に移動し、[アンインストール] をクリックして、ASR プロバイダーをアンインストールします
2. [SCVMM のデータベースを保持し、オペレーティング システムをアップグレードします](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#back-up-and-upgrade-the-operating-system)
3. **[プログラムの追加と削除]** で、 **[VMM]**  >  **[アンインストール]** の順に選択します。 b. **[機能の削除]** を選択し、 **[VMM 管理サーバー] と [VMM コンソール]** を選択します。 c. **[データベースのオプション]** で **[データベースの保持]** を選択します。 d. 概要を確認し、 **[アンインストール]** をクリックします。

4. [VMM 2016 をインストールします](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#install-vmm-2016)
5. SCVMM を起動し、 **[ファブリック]** タブで各ホストの状態を確認します。 **[最新の情報に更新]** をクリックして、最新の状態を取得します。 状態として "要注意" と表示されるはずです。 
17. SCVMM に最新の [Microsoft Azure Site Recovery プロバイダー](https://aka.ms/downloaddra)をインストールします。
16. クラスターの各ホストに最新の [Microsoft Azure Recovery Service (MARS) エージェント](https://aka.ms/latestmarsagent)をインストールします。 最新の情報に更新して、SCVMM がホストを正常に照会できることを確認します。

**Windows Server 2012 R2 ホストを Windows Server 2016 にアップグレードする**

1. クラスターのローリング アップグレード プロセスを実行するには、[こちら](/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process)に記載されている手順に従います。 
2. クラスターに新しいホストを追加した後、SCVMM コンソールからホストを更新して、この更新されたホストに VMM エージェントをインストールします。
3. *Update-VMVersion* を実行して、仮想マシンの VM バージョンを更新します。 
4.  Azure portal に移動し、[Recovery Services コンテナー] で仮想マシンのレプリケートされた正常性状態を確認します。 

## <a name="upgrade-windows-server-2012-r2-hosts-are-managed-by-highly-available-scvmm-2012-r2-server"></a>高可用性の SCVMM 2012 R2 サーバーによって管理されている Windows Server 2012 R2 ホストのアップグレード
お客様の Windows Sever 2012 R2 ホストをアップグレードする前に、SCVMM 2012 R2 を SCVMM 2016 にアップグレードする必要があります。 Azure Site Recovery を使用して構成された SCVMM 2012 R2 サーバーのアップグレードでは、次のアップグレード モードがサポートされています。それは、追加の VMM サーバーを使用しない混合モードと、追加の VMM サーバーを使用する混合モードです。

**SCVMM 2012 R2 を SCVMM 2016 にアップグレードする**

1.  [コントロール パネル]、[プログラム]、[プログラムと機能]、[Microsoft Azure Site Recovery] の順に移動し、[アンインストール] をクリックして、ASR プロバイダーをアンインストールします
2. お客様が実行したいアップグレードのモードに応じて、[こちら](/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#upgrade-a-standalone-vmm-server)に記載されている手順に従います。
3. SCVMM コンソールを起動し、 **[ファブリック]** タブで各ホストの状態を確認します。 **[最新の情報に更新]** をクリックして、最新の状態を取得します。 状態として "要注意" と表示されるはずです。
4. SCVMM に最新の [Microsoft Azure Site Recovery プロバイダー](https://aka.ms/downloaddra)をインストールします。
5. クラスターの各ホストで、最新の [Microsoft Azure Recovery Service (MARS) エージェント](https://aka.ms/latestmarsagent)に更新します。 最新の情報に更新して、SCVMM がホストを正常に照会できることを確認します。


**Windows Server 2012 R2 ホストを Windows Server 2016 にアップグレードする**

1. クラスターのローリング アップグレード プロセスを実行するには、[こちら](/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process)に記載されている手順に従います。
2. クラスターに新しいホストを追加した後、SCVMM コンソールからホストを更新して、この更新されたホストに VMM エージェントをインストールします。
3. *Update-VMVersion* を実行して、仮想マシンの VM バージョンを更新します。 
4.  Azure portal に移動し、[Recovery Services コンテナー] で仮想マシンのレプリケートされた正常性状態を確認します。 

## <a name="next-steps"></a>次のステップ
ホストのアップグレードが実行されたら、[テスト フェールオーバー](tutorial-dr-drill-azure.md)を実行して、レプリケーションの正常性とディザスター リカバリーの状態をテストできます。

