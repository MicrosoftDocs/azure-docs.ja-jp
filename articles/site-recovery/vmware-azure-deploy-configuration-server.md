---
title: Azure Site Recovery で構成サーバーをデプロイする
description: この記事では、Azure Site Recovery での VMware ディザスター リカバリーのために構成サーバーを展開する方法について説明します
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/15/2019
ms.author: ramamill
ms.openlocfilehash: a8ceb3df68ebe42f83c70ed62327bf59c0dfc225
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92359817"
---
# <a name="deploy-a-configuration-server"></a>構成サーバーをデプロイする

Azure への VMware 仮想マシンと物理サーバーのディザスター リカバリーに [Azure Site Recovery](site-recovery-overview.md) を使うときは、オンプレミスの構成サーバーを展開します。 構成サーバーは、オンプレミスの VMware と Azure の間の通信を調整します。 データのレプリケーションも管理します。 この記事では、VMware VM を Azure にレプリケートするときの構成サーバーの展開に必要な手順について説明します。 物理サーバーのレプリケーション用に構成サーバーを設定する必要がある場合は、「[物理サーバーの Azure へのディザスター リカバリーのために構成サーバーを設定する](physical-azure-set-up-source.md)」を参照してください。

> [!TIP]
> Azure Site Recovery アーキテクチャの一部としての構成サーバーの役割については、「[VMware から Azure へのディザスター リカバリー アーキテクチャ](vmware-azure-architecture.md)」を参照してください。

## <a name="deploy-a-configuration-server-through-an-ova-template"></a>OVA テンプレートを使用して構成サーバーをデプロイする

構成サーバーは、特定の最小限のハードウェアおよびサイズ要件に基づいて、高可用性 VMware VM として設定する必要があります。 便利で簡単なデプロイを実現するために、Site Recovery では、ここに記載されているすべての必須要件に準拠する構成サーバーを設定するためのダウンロード可能な Open Virtualization Application (OVA) テンプレートが提供されます。

## <a name="prerequisites"></a>前提条件

構成サーバーの最小限のハードウェア要件を以下のセクションにまとめます。

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Azure Active Directory アクセス許可の要件

Azure Site Recovery サービスに構成サーバーを登録するには、Azure Active Directory (Azure AD) で設定された次のいずれかのアクセス許可を持つユーザーが必要です。

1. アプリケーションを作成するには、アプリケーション開発者のロールがユーザーに必要です。
    - 確認するには、Azure portal にサインインします。</br>
    - **[Azure Active Directory]**  >  **[Roles and administrators]\(ロールと管理者\)** に移動します。</br>
    - アプリケーション開発者のロールがユーザーに割り当てられていることを確認します。 割り当てられていない場合、このアクセス許可を持つユーザーを使用するか、[アクセス許可の有効化を管理者](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md#assign-roles)に要求します。
    
2. アプリケーション開発者のロールを割り当てることができない場合は、ID を作成するユーザーに対して **[ユーザーはアプリケーションを登録できる]** フラグが **[はい]** に設定されていることを確認します。 これらのアクセス許可を有効にするには、次の手順を実行します。
    - Azure portal にサインインします。
    - **[Azure Active Directory]**  >  **[ユーザー設定]** に移動します。
    - **[アプリの登録]** の **[ユーザーはアプリケーションを登録できる]** で、 **[はい]** を選択します。

      ![Azure AD_application_permission](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> Active Directory フェデレーション サービス (AD FS) は *サポートされていません*。 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) で管理されているアカウントを使用します。

## <a name="download-the-template"></a>テンプレートをダウンロードする

1. コンテナーで、 **[インフラストラクチャの準備]**  >  **[ソース]** の順に移動します。
2. **[ソースの準備]** で **[+ 構成サーバー]** を選びます。
3. **[サーバーの追加]** で、 **[サーバーの種類]** に **[VMware の構成サーバー]** が表示されていることを確認します。
4. 構成サーバー用の OVA テンプレートをダウンロードします。

   > [!TIP]
   >構成サーバー テンプレートの最新バージョンは、[Microsoft ダウンロード センター](https://aka.ms/asrconfigurationserver)から直接ダウンロードすることもできます。

> [!NOTE]
> OVA テンプレートに付属するライセンスは、180 日間有効な評価版ライセンスです。 この期間が終了したら、ライセンスを入手する必要があります。

## <a name="import-the-template-in-vmware"></a>VMware にテンプレートをインポートする

1. VMWare vSphere Client を使って、VMware vCenter サーバーまたは vSphere ESXi ホストにサインインします。
2. **[File]\(ファイル\)** メニューの **[Deploy OVF Template]\(OVF テンプレートのデプロイ\)** を選択し、 **[Deploy OVF Template]\(OVF テンプレートのデプロイ\)** ウィザードを起動します。

     ![OVF テンプレートのデプロイ](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. **[Select source]\(ソースの選択\)** で、ダウンロードした OVF の場所を入力します。
4. **[Review details]\(詳細の確認\)** で、 **[Next]\(次へ\)** を選択します。
5. **[Select name and folder]\(名前とフォルダーの選択\)** および **[Select configuration]\(構成の選択\)** は、既定の設定のままにします。
6. **[Select storage]\(ストレージの選択\)** で、パフォーマンスを最大にするために、 **[Select virtual disk format]\(仮想ディスクの形式の選択\)** の **[Thick Provision Eager Zeroed]\(シック プロビジョニング Eager Zeroed\)** を選びます。 シン プロビジョニングのオプションの使用は、構成サーバーのパフォーマンスに影響を及ぼす場合があります。
7. ウィザードの残りのページでは、既定の設定をそのまま使います。
8. **[Ready to complete]\(完了の準備\)** で、次の操作を行います。

    * 既定の設定で VM をセットアップするには、 **[Power on after deployment]\(デプロイ後に電源をオンにする\)**  >  **[Finish]\(完了\)** の順に選びます。
    * 追加のネットワーク インターフェイスを追加するには、 **[Power on after deployment]\(デプロイ後に電源をオンにする\)** をオフにし、 **[Finish]\(完了\)** を選びます。 既定でデプロイされる構成サーバー テンプレートの NIC は 1 つだけですが、 デプロイ後にさらに NIC を追加することができます。

> [!IMPORTANT]
> デプロイ後は、メモリ、コア数、CPU 制限などのリソース構成を変更したり、構成サーバーにインストールされているサービスやファイルを変更または削除したりしないでください。 そのような変更は、Azure サービスへの構成サーバーの登録や、構成サーバーのパフォーマンスに影響を及ぼします。

## <a name="add-an-additional-adapter"></a>さらにアダプターを追加する

> [!NOTE]
> フェールオーバー時、ソース コンピューターの IP アドレスを維持し、後でオンプレミスにフォールバックする予定の場合、NIC が 2 つ必要です。 1 つの NIC がソース コンピューターに接続され、もう 1 つの NIC が Azure 接続に使用されます。

構成サーバーにさらに NIC を追加する場合は、サーバーをコンテナーに登録する前に追加します。 登録後のアダプターの追加はサポートされていません。

1. vSphere Client インベントリで VM を右クリックし、 **[Edit Settings]\(設定の編集\)** を選びます。
2. **[Hardware]\(ハードウェア\)** で、 **[Add]\(追加\)**  >  **[Ethernet Adapter]\(イーサネット アダプター\)** の順に選択します。 **[次へ]** を選択します。
3. アダプターの種類およびネットワークを選びます。
4. VM がオンになったときに仮想 NIC を接続するには、 **[Connect at power- on]\(電源をオンにしたときに接続する\)** を選択します。 次に、 **[Next]\(次へ\)**  >  **[Finish]\(完了\)**  > **OK** の順に選びます。

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>構成サーバーを Azure Site Recovery サービスに登録する

1. VMWare vSphere Client のコンソールで、VM をオンにします。
2. VM が Windows Server 2016 のインストール エクスペリエンスで起動します。 使用許諾契約書に同意し、管理者パスワードを入力します。
3. インストールの完了後に、管理者として VM にサインインします。
4. 初めてサインインすると、数秒後に Azure Site Recovery 構成ツールが起動します。
5. 構成サーバーを Site Recovery に登録するために使う名前を入力します。 **[次へ]** を選択します。
6. このツールは、VM が Azure に接続できることを確認します。 接続が確立された後、 **[サインイン]** を選択して、自分の Azure サブスクリプションにサインインします。</br>
    a. この資格情報は、構成サーバーを登録するコンテナーにアクセスできる必要があります。</br>
    b. 選択したユーザー アカウントが、Azure でアプリケーションを作成するアクセス許可を持っていることを確認します。 必要なアクセス許可を有効にするには、「[Azure Active Directory アクセス許可の要件](#azure-active-directory-permission-requirements)」のガイドラインに従ってください。
7. ツールがいくつかの構成タスクを実行した後、再起動されます。
8. 再度マシンにサインインします。 構成サーバーの管理ウィザードが数秒後に自動的に起動します。

### <a name="configure-settings"></a>設定の構成

1. 構成サーバー管理ウィザードで、 **[接続の設定]** を選びます。 ドロップダウン ボックスから、最初に、モビリティ サービスの検出とソース マシンへのプッシュ インストールのために組み込みプロセス サーバーが使用する NIC を選択します。 次に、構成サーバーが Azure との接続に使用する NIC を選択します。 **[保存]** を選択します。 構成後、この設定を変更することはできません。 構成サーバーの IP アドレスは変更しないでください。 構成サーバーに割り当てられている IP が DHCP IP ではなく静的 IP であることを確認してください。
2. **[Select Recovery Services vault]\(Recovery Services コンテナーの選択\)** で、「[構成サーバーを Azure Site Recovery サービスに登録する](#register-the-configuration-server-with-azure-site-recovery-services)」の手順 6 で使用した資格情報で Microsoft Azure にサインインします。
3. サインインした後、Azure サブスクリプションと、関連するリソース グループおよびコンテナーを選びます。

    > [!NOTE]
    > 登録後に Recovery Services コンテナーを変更する柔軟性はありません。
    > Recovery Services コンテナーを変更するには、構成サーバーと現在のコンテナーの関連付けを解除する必要があり、それにより、構成サーバーの下で保護されているすべての仮想マシンのレプリケーションが停止されます。 詳細については、「[VMware VM のディザスター リカバリー用の構成サーバーを管理する](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault)」を参照してください。

4. **[Install third-party software]\(サードパーティ製ソフトウェアのインストール\)** で、次の手順を実行します。

    |シナリオ   |実行する手順  |
    |---------|---------|
    |MySQL を手動でダウンロードしてインストールできますか?     |  はい。 MySQL アプリケーションをダウンロードし、**C:\Temp\ASRSetup** フォルダー内に配置してから手動でインストールします。 使用条件に同意し、 **[ダウンロードしてインストール]** を選択すると、ポータルに *[Already installed]\(インストール済み\)* と表示されます。 次の手順に進むことができます。       |
    |オンラインでの MySQL のダウンロードを回避することはできますか?     |   はい。 MySQL インストーラー アプリケーションを **C:\Temp\ASRSetup** フォルダーに配置します。 使用条件に同意し、 **[ダウンロードしてインストール]** を選択すると、追加したインストーラーを使用してポータルによってアプリケーションがインストールされます。 インストールが終了したら、次の手順に進みます。    |
    |Azure Site Recovery を使用して MySQL をダウンロードおよびインストールしたい    |  使用許諾契約書に同意し、 **[ダウンロードしてインストール]** を選択します。 インストールが終了したら、次の手順に進みます。       |

5. **[アプライアンス構成の検証]** で、続行する前に前提条件が検証されます。
6. **[Configure vCenter Server/vSphere ESXi server]\(vCenter Server/vSphere ESXi サーバーの構成\)** で、レプリケートする VM が存在している vCenter サーバーまたは vSphere ホストの FQDN または IP アドレスを入力します。 サーバーがリッスンするポートを入力します。 コンテナーで VMware サーバーに使うフレンドリ名を入力します。
7. VMware サーバーに接続するために構成サーバーによって使われる資格情報を入力します。 Site Recovery はこれらの資格情報を使って、レプリケーションに利用できる VMware VM を自動的に検出します。 **[追加]**  >  **[続行]** を選択します。 ここに入力した資格情報はローカルに保存されます。
8. **[Configure virtual machine credentials]\(仮想マシンの資格情報の構成\)** で、レプリケーション時にモビリティ サービスを自動的にインストールするために、仮想マシンのユーザー名とパスワードを入力します。 **Windows** マシンの場合、アカウントには、レプリケートするマシンに対するローカル管理者特権が必要です。 **Linux** の場合は、ルート アカウントの詳細を指定します。
9. **[構成の確定]** を選択して、登録を完了します。
10. 登録が終了したら、Azure portal を開き、構成サーバーと VMware サーバーが、 **[Recovery Services Vault]\(Recovery Services コンテナー\)**  >  **[管理]**  >  **[Site Recovery Infrastructure]\(Site Recovery インフラストラクチャ\)**  >  **[Configuration Servers]\(構成サーバー\)** に表示されていることを確認します。

## <a name="upgrade-the-configuration-server"></a>構成サーバーをアップグレードする

構成サーバーを最新バージョンにアップグレードするには、「[VMware VM のディザスター リカバリー用の構成サーバーを管理する](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)」を参照してください。 すべての Site Recovery コンポーネントをアップグレードする方法については、「[Site Recovery のサービスの更新プログラム](service-updates-how-to.md)」を参照してください。

## <a name="manage-the-configuration-server"></a>構成サーバーの管理

進行中のレプリケーションの中断を回避するには、構成サーバーがコンテナーに登録された後に構成サーバーの IP アドレスが変更されないようにします。 一般的な構成サーバー管理タスクの詳細については、「[VMware VM のディザスター リカバリー用の構成サーバーを管理する](vmware-azure-manage-configuration-server.md)」を参照してください。

## <a name="troubleshoot-deployment-issues"></a>デプロイに関する問題のトラブルシューティング

デプロイや接続の問題を解決するには、[トラブルシューティング記事](vmware-azure-troubleshoot-configuration-server.md)を参照してください。

## <a name="faqs"></a>FAQ

* OVF を使用してデプロイされた構成サーバーで提供されているライセンスはどれくらいの期間有効ですか? ライセンスを再アクティブ化しないとどうなりますか?

    OVA テンプレートに付属するライセンスは、180 日間有効な評価版ライセンスです。 有効期限が切れる前に、ライセンスをアクティブ化する必要があります。 そうしないと、構成サーバーが頻繁にシャットダウンされ、レプリケーション アクティビティの妨げになる可能性があります。 詳細については、「[VMware VM のディザスター リカバリー用の構成サーバーを管理する](vmware-azure-manage-configuration-server.md#update-windows-license)」を参照してください。

* 構成サーバーがインストールされている VM をさまざまな目的で使用することはできますか?

    いいえ。 VM は構成サーバーの目的のみに使用してください。 ディザスター リカバリーを効率的に管理するため、必ず、[前提条件](#prerequisites)に記述されているすべての仕様に従ってください。
* 構成サーバーに既に登録されているコンテナーを、新しく作成されたコンテナーに切り替えることはできますか?

    いいえ。 コンテナーは、構成サーバーに登録した後に変更することはできません。
* 物理マシンと仮想マシンの両方を保護するために同じ構成サーバーを使用することはできますか?

    はい。 物理マシンと仮想マシンをレプリケートするために同じ構成サーバーを使用することができます。 ただし、物理マシンは VMware VM にしかフェールバックできません。
* 構成サーバーの目的は何ですか? また、どこで使用されるのですか?

    構成サーバーとその機能の詳細については、[VMware から Azure へのレプリケーションのアーキテクチャ](vmware-azure-architecture.md)に関するページを参照してください。
* 構成サーバーの最新バージョンはどこで入手できますか?

    ポータルを使用して構成サーバーをアップグレードする手順については、[構成サーバーのアップグレード](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)を参照してください。 すべての Site Recovery コンポーネントをアップグレードする方法については、「[Site Recovery のサービスの更新プログラム](./service-updates-how-to.md)」を参照してください。
* 構成サーバーのパスフレーズはどこでダウンロードできますか?

    パスフレーズをダウンロードするには、「[VMware VM のディザスター リカバリー用の構成サーバーを管理する](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase)」を参照してください。
* パスフレーズは変更できますか?

    いいえ。 構成サーバーのパスフレーズは変更しないでください。 パスフレーズを変更すると、保護されているマシンのレプリケーションが中断されて、重大な正常性状態につながります。
* コンテナー登録キーはどこでダウンロードできますか?

    **[Recovery Services Vault]\(Recovery Services コンテナー\)** で、 **[管理]**  >  **[Site Recovery Infrastructure]\(Site Recovery インフラストラクチャ\)**  >  **[Configuration Servers]\(構成サーバー\)** を選択します。 **[サーバー]** で **[登録キーのダウンロード]** を選択して、コンテナーの資格情報ファイルをダウンロードします。
* 既存の構成サーバーを複製してレプリケーション オーケストレーションに使用することはできますか?

    いいえ。 複製された構成サーバー コンポーネントの使用はサポートされていません。 スケールアウト プロセス サーバーの複製も、サポートされていないシナリオです。 Site Recovery コンポーネントを複製すると、進行中のレプリケーションに影響します。

* 構成サーバーの IP を変更することはできますか?

    いいえ。 構成サーバーの IP アドレスは変更しないでください。 構成サーバーに割り当てられているすべての IP が DHCP IP ではなく静的 IP であることを確認してください。
* Azure で構成サーバーを設定できますか?

    v-Center との直接の通信経路を使って、またデータ転送の待機時間を最小限に抑えるために、オンプレミス環境で構成サーバーを設定してください。 [フェールバックの目的](vmware-azure-manage-configuration-server.md#failback-requirements)で、構成サーバーのスケジュールされたバックアップを作成することができます。

* 構成サーバーまたはスケールアウト プロセス サーバーでキャッシュ ドライバーを変更できますか。

    いいえ。セットアップの完了後はキャッシュ ドライバーを変更できません。

構成サーバーに関するその他の FAQ については、[構成サーバーに関するよくある質問](vmware-azure-common-questions.md#configuration-server)を参照してください。

## <a name="next-steps"></a>次のステップ

Azure への [VMware VM](vmware-azure-tutorial.md) のディザスター リカバリーを設定する。