---
title: "Azure Site Recovery でセカンダリ Azure リージョンへの Azure VM のディザスター リカバリーを設定する (プレビュー)"
description: "Azure Site Recovery サービスを使用して、別の Azure リージョンへの Azure VM のディザスター リカバリーを設定する方法について説明します。"
services: site-recovery
author: rajani-janaki-ram
manager: carmonm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/18/2017
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: 9a92bf402863fd1ee688fba7c20d3a2cd3e299da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-disaster-recovery-for-azure-vms-to-a-secondary-azure-region-preview"></a>セカンダリ Azure リージョンへの Azure VM のディザスター リカバリーを設定する (プレビュー)

[Azure Site Recovery](site-recovery-overview.md) サービスは、オンプレミス マシンと Azure 仮想マシン (VM) のレプリケーション、フェールオーバー、フェールバックを管理し、調整することでディザスター リカバリー戦略に貢献します。

このチュートリアルでは、セカンダリ Azure リージョンへの Azure VM のディザスター リカバリーを設定する方法について説明します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Recovery Services コンテナーを作成する
> * ターゲット リソースの設定を確認する
> * VM の発信アクセスを設定する
> * VM のレプリケーションを有効にする

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

- [シナリオのアーキテクチャとコンポーネント](concepts-azure-to-azure-architecture.md)を理解している。
- すべてのコンポーネントの[サポート要件](site-recovery-support-matrix-azure-to-azure.md)を確認する。

## <a name="create-a-vault"></a>コンテナーの作成

任意のリージョン (ソース リージョンを除く) にコンテナーを作成します。

1. [Azure Portal](https://portal.azure.com) > **Recovery Services** にサインインします。
2. **[新規]** > **[監視 + 管理]** > **[Backup and Site Recovery]\(バックアップと Site Recovery\)** の順にクリックします。
3. **[名前]**に、コンテナーを識別するフレンドリ名を入力します。 複数のサブスクリプションがある場合は、適切なものを選択します。
4. リソース グループを作成するか、既存のリソース グループを選択します。 Azure リージョンを指定します。 サポートされているリージョンを確認するには、[Azure Site Recovery の価格の詳細](https://azure.microsoft.com/pricing/details/site-recovery/)に関するページでご利用可能な地域をご覧ください。
5. ダッシュボードからコンテナーにすばやくアクセスするには、**[ダッシュボードにピン留めする]**、**[作成]** の順にクリックします。

   ![新しいコンテナー](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   新しいコンテナーは、**[ダッシュボード]** の **[すべてのリソース]** と、メインの **[Recovery Services コンテナー]** ページに追加されます。

## <a name="verify-target-resources"></a>ターゲット リソースの確認

1. Azure サブスクリプションによって、ディザスター リカバリーに使用されるターゲット リージョンに VM を作成できることを確認します。 サポートに連絡して、必要なクォータを有効にしてください。

2. サブスクリプションに、ソース VM と一致するサイズの VM をサポートするための十分なリソースがあることを確認します。 Site Recovery によって、ターゲット VM に対して同じサイズまたは最も近いサイズが選択されます。

## <a name="configure-outbound-network-connectivity"></a>発信ネットワーク接続の構成

Site Recovery が期待どおりに動作するには、レプリケートしたい VM から、送信ネットワークの接続に変更を加える必要があります。

- Site Recovery では、ネットワーク接続を制御するための認証プロキシの使用をサポートしていません。
- 認証プロキシがある場合、レプリケーションを有効にできません。

### <a name="outbound-connectivity-for-urls"></a>URL に対する送信接続

送信接続を制御するために URL ベースのファイアウォール プロキシを使用している場合、Site Recovery によって使用される次の URL へのアクセスを許可します。

| **URL** | **詳細** |
| ------- | ----------- |
| *.blob.core.windows.net | ソース リージョンのキャッシュ ストレージ アカウントに、VM からデータが書き込まれるよう許可します。 |
| login.microsoftonline.com | Site Recovery サービス URL に対する承認と認証を提供します。 |
| *.hypervrecoverymanager.windowsazure.com | VM と Site Recovery サービスの通信を許可します。 |
| *.servicebus.windows.net | VM による Site Recovery の監視および診断データの書き込みを許可します。 |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>IP アドレス範囲に対する送信接続

IP ベースのファイアウォール、プロキシ、または NSG ルールを使用して送信接続を制御している場合は、次の IP アドレス範囲をホワイトリストに登録する必要があります。 次のリンクから範囲の一覧をダウンロードしてください。

  - [Microsoft Azure データセンターの IP 範囲](http://www.microsoft.com/en-us/download/details.aspx?id=41653)
  - [ドイツの Windows Azure データセンターの IP 範囲](http://www.microsoft.com/en-us/download/details.aspx?id=54770)
  - [中国の Windows Azure データセンターの IP 範囲](http://www.microsoft.com/en-us/download/details.aspx?id=42064)
  - [Office 365 URL および IP アドレス範囲](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [Site Recovery サービス エンドポイントの IP アドレス](https://aka.ms/site-recovery-public-ips)

この一覧を使用して、ご使用のネットワークでネットワーク アクセス制御を構成します。 こちらの[スクリプト](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702)を使用して、必要な NSG ルールを作成することができます。

## <a name="verify-azure-vm-certificates"></a>Azure VM の証明書の確認

レプリケートする Windows または Linux VM に、最新のルート証明書がすべて存在することを確認します。 最新のルート証明書がない場合、セキュリティの制約のため、VM を Site Recovery に登録できません。

- Windows VM については、最新のすべての Windows 更新プログラムを VM にインストールして、すべての信頼されたルート証明書をマシンに用意します。 接続されていない環境の場合は、組織の標準の Windows Update プロセスおよび証明書更新プロセスに従ってください。

- Linux VM の場合は、Linux ディストリビューターから提供されるガイダンスに従って、VM で最新の信頼されたルート証明書と証明書失効リストを取得します。

## <a name="set-permissions-on-the-account"></a>アカウントのアクセス許可の設定

Azure Site Recovery には、Site Recovery の管理操作を制御するための組み込みロールが 3 つ用意されています。

- **Site Recovery 共同作成者** - このロールには、Recovery Services コンテナーでの Azure Site Recovery の操作の管理に必要なすべてのアクセス許可があります。 ただし、このロールを持つユーザーは、Recovery Services コンテナーの作成や削除、または他のユーザーへのアクセス権の割り当てを行うことはできません。 このロールは、アプリケーションまたは組織全体のディザスター リカバリーを有効にして管理できる、ディザスター リカバリー管理者に最も適しています。

- **Site Recovery オペレーター** - この役割には、フェールオーバーとフェールバックの操作を実行して管理するアクセス許可があります。 このロールのユーザーは、レプリケーションの有効化または無効化、コンテナーの作成または削除、新しいインフラストラクチャの登録、他のユーザーへのアクセス権の割り当てを行うことはできません。 このロールは、アプリケーション所有者および IT 管理者から指示されて、仮想マシンまたはアプリケーションをフェールオーバーできる、ディザスター リカバリー オペレーターに最も適しています。 障害が解決された後、DR オペレーターは仮想マシンを再び保護してフェールバックを行うことができます。

- **Site Recovery 閲覧者** - このロールには、すべての Site Recovery 管理操作を見るアクセス許可があります。 このロールは、保護の現在の状態を監視し、サポート チケットを発行できる、IT 監視担当役員に最も適しています。

[Azure RBAC の組み込みのロール](../active-directory/role-based-access-built-in-roles.md)について説明します。

## <a name="enable-replication"></a>Enable replication

### <a name="select-the-source"></a>ソースを選択する

1. [Recovery Services コンテナー] で、[コンテナー名] > **[+ レプリケート]** の順にクリックします。
2. **[ソース]** で **[Azure - プレビュー]** を選択します。
3. **[ソースの場所]** で、現在 VM が実行されているソースの Azure リージョンを選択します。
4. VM の **Azure 仮想マシンのデプロイ モデル**として、**[Resource Manager]** または **[クラシック]** を選択します。
5. Resource Manager VM の場合は**ソース リソース グループ**を、クラシック VM の場合は**クラウド サービス**を選択します。
6. **[OK]** をクリックして設定を保存します。

### <a name="select-the-vms"></a>VM を選択する

Site Recovery は、サブスクリプションとリソース グループ/クラウド サービスに関連付けられている VM の一覧を取得します。

1. **[仮想マシン]** で、レプリケートする VM を選択します。
2. **[OK]**をクリックします。

### <a name="configure-replication-settings"></a>レプリケーションの設定を構成する

Site Recovery では、ターゲット リージョンの既定の設定とレプリケーション ポリシーが作成されます。 設定は、要件に基づいて変更できます。

![設定を構成する](./media/azure-to-azure-tutorial-enable-replication/settings.png)

- **ターゲットの場所**: ディザスター リカバリーに使用するターゲット リージョン。 ターゲットの場所が Site Recovery コンテナーの場所と一致していることをお勧めします。

- **ターゲット リソース グループ**: フェールオーバー後、Azure VM を保持する、ターゲット リージョンのリソース グループ。 既定では、Site Recovery は "asr" というサフィックスを持つターゲット リージョンに、新しいリソース グループを作成します。

- **ターゲット仮想ネットワーク**: フェールオーバー後、VM が配置される、ターゲット リージョンのネットワーク。
  既定では、Site Recovery は "asr" というサフィックスを持つターゲット リージョンに、新しい仮想ネットワーク (およびサブネット) を作成します。

- **ストレージ アカウントのキャッシュ**: Site Recovery では、ソース リージョンにストレージ アカウントを使用します。 ソース VM への変更は、ターゲットの場所にレプリケートする前に、このアカウントに送信されます。

- **ターゲット ストレージ アカウント**: 既定では、Site Recovery はターゲット リージョンに新しいストレージ アカウントを作成して、ソース VM のストレージ アカウントをミラーします。

- **ターゲットの可用性セット:** 既定では、Site Recovery は "asr" サフィックスを付けて、新しい可用性セットをターゲット リージョンに作成します。

- **レプリケーション ポリシー名** : ポリシーの名前。

- **復旧ポイントのリテンション期間**: 既定では、Site Recovery は 24 時間の復旧ポイントを保持します。 1 ～ 72 時間の値を構成できます。

- **アプリ整合性スナップショットの頻度**: 既定では、Site Recovery はアプリ整合性スナップショットを 4 時間ごとに取得します。 1 ～ 12 時間の任意の値を構成できます。 アプリ整合性スナップショットは、VM 内のアプリケーション データのポイントインタイム スナップショットです。 ボリューム シャドウ コピー サービス (VSS) によって、スナップショットの作成時、VM 上のアプリの整合性が維持されます。

### <a name="track-replication-status"></a>レプリケーションの状態を追跡する

1. **[設定]** で、**[更新]** をクリックして最新の状態を取得します。

2. **保護の有効化**ジョブの進行状況は、**[設定]** > **[ジョブ]** > **[Site Recovery ジョブ]** で追跡できます。

3. **[設定]** > **[レプリケートされたアイテム]** では、VM の状態と初期レプリケーションの進行状況を確認できます。 VM の設定をドリルダウンするには、その VM をクリックします。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure VM のディザスター リカバリーを構成しました。 次の手順では、構成をテストします。

> [!div class="nextstepaction"]
> [ディザスター リカバリー訓練を実行する](azure-to-azure-tutorial-dr-drill.md)
