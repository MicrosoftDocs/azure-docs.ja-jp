---
title: Azure Site Recovery での VMware ディザスター リカバリーのために構成サーバーを展開する | Microsoft Docs
description: この記事では、Azure Site Recovery での VMware ディザスター リカバリーのために構成サーバーを展開する方法について説明します
services: site-recovery
author: rajeswari-mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/06/2019
ms.author: ramamill
ms.openlocfilehash: c25ca8c27b84f34b025ec5abce00c8d8c70e5df6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "62125697"
---
# <a name="deploy-a-configuration-server"></a>構成サーバーをデプロイする

Azure への VMware 仮想マシンと物理サーバーのディザスター リカバリーに [Azure Site Recovery](site-recovery-overview.md) を使うときは、オンプレミスの構成サーバーを展開します。 構成サーバーは、オンプレミスの VMware と Azure の間の通信を調整します。 データのレプリケーションも管理します。 この記事では、VMware VM を Azure にレプリケートするときの構成サーバーの展開に必要な手順について説明します。 物理サーバーのレプリケーション用に構成サーバーを設定する必要がある場合は、[こちらの記事に従ってください](physical-azure-set-up-source.md)。

> [!TIP]
> Azure Site Recovery アーキテクチャの一部としての構成サーバーの役割については、[こちら](vmware-azure-architecture.md)を参照してください。

## <a name="deployment-of-configuration-server-through-ova-template"></a>OVA テンプレートを使用する構成サーバーのデプロイ

構成サーバーは、特定の最小限のハードウェアおよびサイズ要件に基づいて、高可用性 VMware VM として設定する必要があります。 便利で簡単なデプロイを実現するために、Site Recovery では、以下にリストされているすべての必須要件に準拠する構成サーバーを設定するためのダウンロード可能な OVA (Open Virtualization Application) テンプレートが提供されます。

## <a name="prerequisites"></a>前提条件

構成サーバーの最小限のハードウェア要件を次の表にまとめます。

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Azure Active Directory アクセス許可の要件

Azure Site Recovery サービスに構成サーバーを登録するには、AAD (Azure Active Directory) で設定された**次のいずれか**のアクセス許可を持つユーザーが必要です。

1. ユーザーは、アプリケーションを作成するために、"アプリケーション開発者" ロールを持つ必要があります。
   1. 確認するには、Azure portal にサインインする</br>
   1. [Azure Active Directory] > [ロールと管理者] の順に移動する</br>
   1. "アプリケーション開発者" ロールがユーザーに割り当てられているかどうかを確認します。 割り当てられていない場合、このアクセス許可を持つユーザーを使用するか、[アクセス許可の有効化を管理者](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal#assign-roles)に要求します。
    
1. "アプリケーション開発者" ロールを割り当てることができない場合は、ID を作成するユーザーで、[ユーザーはアプリケーションを登録できる] が [はい] に設定されていることを確認します。 上記のアクセス許可を有効にするには、次の手順を実行します。
   1. Azure Portal にサインインする
   1. [Azure Active Directory]、[ユーザー設定] の順に移動する
   1. [アプリの登録] で、[ユーザーはアプリケーションを登録できる] を [はい] に設定する必要があります。

      ![AAD_application_permission](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> Active Directory フェデレーション サービス (AD FS) は**サポート**されていません。 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) で管理されているアカウントを使用してください。

## <a name="capacity-planning"></a>容量計画

構成サーバーのサイズ要件は、潜在的なデータ変更率に依存します。 次の表を参考にしてください。

| **CPU** | **メモリ** | **キャッシュ ディスク サイズ** | **データの変更率** | **保護されたマシン** |
| --- | --- | --- | --- | --- |
| 8 vCPU (2 ソケット * 4 コア \@ 2.5 GHz) |16 GB |300 GB |500 GB 以下 |100 台未満のマシンをレプリケートします。 |
| 12 vCPU (2 ソケット * 6 コア \@ 2.5 GHz) |18 GB |600 GB |500 GB ～ 1 TB |100 ～ 150 台のマシンをレプリケートします。 |
| 16 vCPU (2 ソケット * 8 コア \@ 2.5 GHz) |32 GB |1 TB (テラバイト) |1 TB ～ 2 TB |150 ～ 200 台のマシンをレプリケートします。 |

複数の VMware VM をレプリケートする場合は、[容量計画に関する考慮事項](site-recovery-plan-capacity-vmware.md)を参照してください。 VMware のレプリケーションの場合は [Deployment Planner ツール](site-recovery-deployment-planner.md)を実行します。

## <a name="download-the-template"></a>テンプレートをダウンロードする

1. コンテナーで、 **[インフラストラクチャの準備]**  >  **[ソース]** の順に移動します。
2. **[ソースの準備]** で **[+ 構成サーバー]** を選びます。
3. **[サーバーの追加]** で、 **[サーバーの種類]** に **[VMware の構成サーバー]** が表示されていることを確認します。
4. 構成サーバー用の Open Virtualization Application (OVA) テンプレートをダウンロードします。

   > [!TIP]
   >構成サーバー テンプレートの最新バージョンは、[Microsoft ダウンロード センター](https://aka.ms/asrconfigurationserver)から直接ダウンロードすることもできます。

> [!NOTE]
> OVA テンプレートに付属するライセンスは、180 日間有効な評価版ライセンスです。 この期間が経過した後は、入手済みのライセンスを使用して Windows のライセンス認証をユーザーが行う必要があります。

## <a name="import-the-template-in-vmware"></a>VMware にテンプレートをインポートする

1. VMWare vSphere Client を使って、VMware vCenter サーバーまたは vSphere ESXi ホストにサインインします。
2. **[File]\(ファイル\)** メニューの **[Deploy OVF Template]\(OVF テンプレートのデプロイ\)** を選び、Deploy OVF Template (OVF テンプレートのデプロイ) ウィザードを起動します。

     ![OVF テンプレート](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. **[Select source]\(ソースの選択\)** で、ダウンロードした OVF の場所を入力します。
4. **[Review details]\(詳細の確認\)** で、 **[Next]\(次へ\)** を選択します。
5. **[Select name and folder]\(名前とフォルダーの選択\)** および **[Select configuration]\(構成の選択\)** は、既定の設定のままにします。
6. **[Select storage]\(ストレージの選択\)** で、パフォーマンスを最大にするために、 **[Select virtual disk format]\(仮想ディスクの形式の選択\)** の **[Thick Provision Eager Zeroed]\(シック プロビジョニング Eager Zeroed\)** を選びます。 シン プロビジョニングのオプションの使用は、構成サーバーのパフォーマンスに影響を及ぼす場合があります。
7. ウィザードの残りのページでは、既定の設定をそのまま使います。
8. **[Ready to complete]\(完了の準備\)** で、次の操作を行います。

    * 既定の設定で VM をセットアップするには、 **[Power on after deployment]\(デプロイ後に電源をオンにする\)**  >  **[Finish]\(完了\)** の順に選びます。

    * 追加のネットワーク インターフェイスを追加するには、 **[Power on after deployment]\(デプロイ後に電源をオンにする\)** をオフにし、 **[Finish]\(完了\)** を選びます。 既定でデプロイされる構成サーバー テンプレートの NIC は 1 つだけですが、 デプロイ後にさらに NIC を追加することができます。

> [!IMPORTANT]
> デプロイ後は、リソースの構成 (CPU/メモリ/コアの制限) を変更したり、構成サーバーにインストールされているサービスやファイルを変更または削除したりしないでください。 これは、Azure サービスへの構成サーバーの登録と構成サーバーのパフォーマンスに影響を及ぼします。

## <a name="add-an-additional-adapter"></a>さらにアダプターを追加する

構成サーバーにさらに NIC を追加する場合は、サーバーをコンテナーに登録する前に追加します。 登録後のアダプターの追加はサポートされていません。

1. vSphere Client インベントリで VM を右クリックし、 **[Edit Settings]\(設定の編集\)** を選びます。
2. **[Hardware]\(ハードウェア\)** で、 **[Add]\(追加\)**  >  **[Ethernet Adapter]\(イーサネット アダプター\)** の順に選択します。 次に、 **[次へ]** を選択します。
3. アダプターの種類およびネットワークを選びます。
4. VM がオンになったときに仮想 NIC を接続するには、 **[Connect at power- on]\(電源をオンにしたときに接続する\)** を選択します。 次に、 **[Next]\(次へ\)**  >  **[Finish]\(完了\)**  > **OK** の順に選びます。

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>構成サーバーを Azure Site Recovery サービスに登録する

1. VMWare vSphere Client のコンソールで、VM をオンにします。
2. VM が Windows Server 2016 のインストール エクスペリエンスで起動します。 使用許諾契約書に同意し、管理者パスワードを入力します。
3. インストールの完了後に、管理者として VM にサインインします。
4. 初めてサインインすると、数秒後に Azure Site Recovery 構成ツールが起動します。
5. 構成サーバーを Site Recovery に登録するために使う名前を入力します。 次に、 **[次へ]** を選択します。
6. このツールは、VM が Azure に接続できることを確認します。 接続が確立された後、 **[サインイン]** を選択して、自分の Azure サブスクリプションにサインインします。</br>
    a. この資格情報は、構成サーバーを登録するコンテナーにアクセスできる必要があります。</br>
    b. 選択したユーザー アカウントが Azure でアプリケーションを作成するアクセス許可を持っていることを確認します。 必要なアクセス許可を有効にするには、[ここ](#azure-active-directory-permission-requirements)に指定されているガイドラインに従ってください。
7. ツールがいくつかの構成タスクを実行した後、再起動されます。
8. 再度マシンにサインインします。 構成サーバーの管理ウィザードが数秒後に**自動的に**起動します。

### <a name="configure-settings"></a>設定を構成する

1. 構成サーバー管理ウィザードで、 **[接続の設定]** を選びます。 ドロップダウンから、最初に、モビリティ サービスの検出とソース マシンへのプッシュ インストールのために組み込みプロセス サーバーが使用する NIC を選択し、次に、構成サーバーが Azure との接続に使用する NIC を選択します。 次に、 **[保存]** を選択します。 構成後、この設定を変更することはできません。 構成サーバーの IP アドレスは変更しないことを強くお勧めします。 構成サーバーに割り当てられている IP は、DHCP IP ではなく確実に静的 IP にします。
2. **[Recovery Services コンテナーを選択する]** で、**手順 6** の「[構成サーバーを Azure Site Recovery サービスに登録する](#register-the-configuration-server-with-azure-site-recovery-services)」で使用されている資格情報を使用して Microsoft Azure にサインインします。
3. サインインした後、Azure サブスクリプションと、関連するリソース グループおよびコンテナーを選びます。

    > [!NOTE]
    > 登録後に Recovery Services コンテナーを変更する柔軟性はありません。
    > Recovery Services コンテナーを変更するには、現在のコンテナーとの構成サーバーの関連付けの解除が必要になります。それにより構成サーバーの下で保護されているすべての仮想マシンのレプリケーションが停止されます。 [詳細情報](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault)。

4. **[サードパーティ製ソフトウェアのインストール]** で、

    |シナリオ   |実行する手順  |
    |---------|---------|
    |MySQL を手動でダウンロードしてインストールすることはできますか?     |  はい。 MySQL アプリケーションをダウンロードし、**C:\Temp\ASRSetup** フォルダーに配置してから手動でインストールします。 これで、使用条件に同意し、 **[ダウンロードしてインストール]** をクリックすると、ポータルに *[インストール済み]* と示されます。 次の手順に進むことができます。       |
    |オンラインでの MySQL のダウンロードを回避することはできますか?     |   はい。 MySQL インストーラー アプリケーションを **C:\Temp\ASRSetup** フォルダーに配置します。 使用条件に同意し、 **[ダウンロードしてインストール]** をクリックすると、ポータルでは追加されたインストーラーが使用され、アプリケーションがインストールされます。 インストール後に次の手順に進むことができます。    |
    |Azure Site Recovery を使用して、MySQL をダウンロードし、インストールしたい     |  ライセンス契約の内容に同意し、 **[ダウンロードしてインストール]** をクリックします。 これで、インストール後に次の手順に進むことができます。       |

5. **[アプライアンス構成の検証]** で、続行する前に前提条件が検証されます。
6. **[Configure vCenter Server/vSphere ESXi server]\(vCenter Server/vSphere ESXi サーバーの構成\)** で、レプリケートする VM が存在している vCenter サーバーまたは vSphere ホストの FQDN または IP アドレスを入力します。 サーバーがリッスンするポートを入力します。 コンテナーで VMware サーバーに使うフレンドリ名を入力します。
7. VMware サーバーに接続するために構成サーバーによって使われる資格情報を入力します。 Site Recovery はこれらの資格情報を使って、レプリケーションに利用できる VMware VM を自動的に検出します。 **[追加]** 、 **[続行]** の順に選択します。 ここに入力した資格情報はローカルに保存されます。
8. **[仮想マシンの資格情報の構成]** で、レプリケーション時にモビリティ サービスを自動的にインストールするために、仮想マシンのユーザー名とパスワードを入力します。 **Windows** マシンの場合、アカウントには、レプリケートするマシンに対するローカル管理者特権が必要です。 **Linux** の場合は、ルート アカウントの詳細を指定します。
9. **[構成の確定]** を選択して、登録を完了します。
10. 登録が終了したら、Azure portal を開き、構成サーバーと VMware サーバーが、 **[Recovery Services コンテナー]**  >  **[管理]**  >  **[Site Recovery インフラストラクチャ]**  >  **[構成サーバー]** にリストされていることを確認します。

## <a name="upgrade-the-configuration-server"></a>構成サーバーをアップグレードする

構成サーバーを最新バージョンにアップグレードするには、こちらの[手順](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)に従ってください。 すべての Site Recovery コンポーネントをアップグレードする方法の詳細な手順については、[こちら](service-updates-how-to.md)をクリックしてください。

## <a name="manage-the-configuration-server"></a>構成サーバーの管理

進行中のレプリケーションの中断を回避するには、構成サーバーがコンテナーに登録された後に構成サーバーの IP アドレスが変更されないようにします。 構成サーバーの一般的な管理タスクの詳細については、[こちら](vmware-azure-manage-configuration-server.md)を参照してください。

## <a name="faq"></a>FAQ

1. OVF を使用してデプロイされた構成サーバーで提供されているライセンスはどれくらいの期間有効ですか? ライセンスを再アクティブ化しないとどうなりますか?

    OVA テンプレートに付属するライセンスは、180 日間有効な評価版ライセンスです。 有効期限が切れる前に、ライセンスをアクティブ化する必要があります。 そうしないと、構成サーバーが頻繁にシャットダウンされ、それによりレプリケーション アクティビティが妨げられることがあります。

2. 構成サーバーがインストールされている VM をさまざまな目的で使用することはできますか?

    **いいえ**。VM は構成サーバー専用にすることをお勧めします。 ディザスター リカバリーを効率的に管理するため、必ず、[前提条件](#prerequisites)に記述されているすべての仕様に従ってください。
3. 構成サーバーに既に登録されているコンテナーを、新しく作成されたコンテナーに切り替えることはできますか?

    **いいえ**。コンテナーは、構成サーバーに登録した後に変更することはできません。
4. 物理マシンと仮想マシンの両方を保護するために同じ構成サーバーを使用することはできますか?

    **はい**。物理マシンと仮想マシンをレプリケートするために同じ構成サーバーを使用することができます。 ただし、物理マシンをフェールバックできるのは、VMware VM にのみです。
5. 構成サーバーの目的は何ですか? また、どこで使用されるのですか?

    構成サーバーとその機能の詳細については、[Azure レプリケーション アーキテクチャに対する VMware ](vmware-azure-architecture.md)を参照してください。
6. 構成サーバーの最新バージョンはどこで入手できますか?

    ポータルを使用して構成サーバーをアップグレードする手順については、[構成サーバーのアップグレード](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)を参照してください。 すべての Site Recovery コンポーネントをアップグレードする方法の詳細な手順については、[こちら](https://aka.ms/asr_how_to_upgrade)を参照してください。
7. 構成サーバーのパスフレーズはどこでダウンロードできますか?

    パスフレーズをダウンロードする場合は、[こちらの記事](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase)を参照してください。
8. パスフレーズは変更できますか?

    **いいえ**、構成サーバーの**パスフレーズは変更しないことを強くお勧めします**。 パスフレーズ変更すると、保護されているマシンのレプリケーションが中断されて、重大な正常性状態につながります。
9. コンテナー登録キーはどこでダウンロードできますか?

    **[Recovery Services コンテナー]** で、 **[管理]**  >  **[Site Recovery インフラストラクチャ]**  >  **[構成サーバー]** の順に移動します。 [サーバー] で **[登録キーのダウンロード]** を選択して、コンテナーの資格情報ファイルをダウンロードします。
10. 既存の構成サーバーを複製してレプリケーション オーケストレーションに使用することはできますか?

    **いいえ**。複製された Configuration Server コンポーネントは使用できません。 スケール アウト プロセス サーバーの複製のシナリオもサポートされていません。 Site Recovery コンポーネントを複製すると、進行中のレプリケーションに影響します。

11. 構成サーバーの IP を変更することはできますか?

    **いいえ**、構成サーバーの IP アドレスは変更しないことを強くお勧めします。 構成サーバーに割り当てられているすべての IP は、DHCP IP ではなく確実に静的 IP にします。
12. Azure で構成サーバーを設定できますか?

    v-Center との直接の通信経路を使ってオンプレミス環境の構成サーバーを設定することと、データ転送の待機時間を最小限に抑えることをお勧めします。 [フェールバックの目的](vmware-azure-manage-configuration-server.md#failback-requirements)で、構成サーバーのスケジュールされたバックアップを作成することができます。

構成サーバーのよくあるご質問については、[構成サーバーについてよく寄せられる質問のドキュメント](vmware-azure-common-questions.md#configuration-server)を参照してください。

## <a name="troubleshoot-deployment-issues"></a>デプロイに関する問題のトラブルシューティング

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>次の手順

Azure への [VMware VM](vmware-azure-tutorial.md) のディザスター リカバリーを設定する。
