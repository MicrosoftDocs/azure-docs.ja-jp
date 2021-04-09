---
title: Azure Site Recovery を使用して Azure VM のディザスター リカバリーを設定するためのチュートリアル
description: このチュートリアルでは、Site Recovery サービスを使用して別の Azure リージョンへの Azure VM のディザスター リカバリーを設定します。
ms.topic: tutorial
ms.date: 11/03/2020
ms.custom: mvc
ms.openlocfilehash: 473a264ef497cab4bd4f88372600161b33178099
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97656871"
---
# <a name="tutorial-set-up-disaster-recovery-for-azure-vms"></a>チュートリアル:Azure VM のディザスター リカバリーを設定する

このチュートリアルでは、[Azure Site Recovery](site-recovery-overview.md) を使用して Azure VM のディザスター リカバリーを設定する方法について説明します。 この記事では、次のことについて説明します。

> [!div class="checklist"]
> * Azure の設定とアクセス許可を確認する
> * レプリケートする VM を準備する
> * Recovery Services コンテナーを作成する
> * VM のレプリケーションを有効にする

VM のレプリケーションを有効にしてディザスター リカバリーを設定すると、Site Recovery Mobility Service 拡張機能が VM にインストールされ、Azure Site Recovery に登録されます。 レプリケーション中、VM ディスクへの書き込みは、ソース リージョンのキャッシュ ストレージ アカウントに送信されます。 データは、そこからターゲット リージョンに送信され、そのデータから復旧ポイントが生成されます。 ディザスター リカバリーの過程で VM をフェールオーバーすると、復旧ポイントを使用してターゲット リージョンに VM が復元されます。

> [!NOTE]
> チュートリアルの手順では、最も単純な既定の設定を使用しています。 カスタマイズした設定で Azure VM ディザスター リカバリーを設定したい場合は、[こちらの記事](azure-to-azure-how-to-enable-replication.md)をご覧ください。

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを始める前に:

- [サポート対象リージョンを確認](azure-to-azure-support-matrix.md#region-support)します。 Azure VM のディザスター リカバリーは、同じ地理クラスターにある 2 つのリージョン間で設定できます。
- 少なくとも 1 つの Azure VM が必要です。 [Windows](azure-to-azure-support-matrix.md#windows) VM または [Linux](azure-to-azure-support-matrix.md#replicated-machines---linux-file-systemguest-storage) VM がサポートされていることを確認してください。
- VM の[コンピューティング](azure-to-azure-support-matrix.md#replicated-machines---compute-settings)、[ストレージ](azure-to-azure-support-matrix.md#replicated-machines---storage)、[ネットワーク](azure-to-azure-support-matrix.md#replicated-machines---networking)の要件を確認します。
- このチュートリアルでは、VM が暗号化されていないことを前提としています。 暗号化された VM のディザスター リカバリーを設定したい場合は、[こちらの記事に従ってください](azure-to-azure-how-to-enable-replication-ade-vms.md)。

## <a name="check-azure-settings"></a>Azure の設定を確認する

アクセス許可とターゲット リージョンの設定とを確認します。

### <a name="check-permissions"></a>アクセス許可を確認する

ご利用の Azure アカウントには、Recovery Services コンテナーを作成するためのアクセス許可と、ターゲット リージョンに VM を作成するためのアクセス許可が必要です。

- 無料の Azure サブスクリプションを作成したばかりであれば、ご自身がアカウントの管理者になっているため、それ以上のアクションは不要です。
- 管理者でなければ、必要なアクセス許可の割り当てを管理者に依頼します。
    - **コンテナーを作成する**: サブスクリプションの管理者または所有者のアクセス許可。 
    - **コンテナー内の Site Recovery の操作を管理する**: "*Site Recovery 共同作成者*" 組み込み Azure ロール。
    - **ターゲット リージョンに Azure VM を作成する**: 組み込みの "*仮想マシン共同作成者*" ロール。または次の作業を行うための個別のアクセス許可。
        - 選択した仮想ネットワーク内に VM を作成する。
        - Azure Storage アカウントに書き込む。
        - Azure マネージド ディスクに書き込む。

### <a name="verify-target-settings"></a>ターゲットの設定を確認する

ディザスター リカバリー中は、ソース リージョンからフェールオーバーしたときに、ターゲット リージョンに VM が作成されます。 

ご利用のサブスクリプションに、ターゲット リージョンに十分なリソースが存在することを確認します。 ソース リージョン内の VM と同じサイズの VM を作成できることが必要です。 ディザスター リカバリーを設定すると、Site Recovery によって、ターゲット VM に対して同じサイズまたは最も近いサイズが選択されます。


## <a name="prepare-vms"></a>VM を準備する

VM にアウトバウンド接続と最新のルート証明書があることを確認します。 


### <a name="set-up-vm-connectivity"></a>VM の接続を設定する

レプリケートする VM には、アウトバウンド ネットワーク接続が必要です。

> [!NOTE]
> Site Recovery では、ネットワーク接続を制御するための認証プロキシの使用をサポートしていません。

#### <a name="outbound-connectivity-for-urls"></a>URL に対する送信接続

アウトバウンド接続を制御するために URL ベースのファイアウォール プロキシを使用している場合、以下の URL へのアクセスを許可してください。

| **名前**                  | **商用**                               | **政府**                                 | **説明** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| ストレージ                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | ソース リージョンのキャッシュ ストレージ アカウントに、VM からデータが書き込まれるよう許可します。 |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Site Recovery サービス URL に対する承認と認証を提供します。 |
| レプリケーション               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | VM と Site Recovery サービスの通信を許可します。 |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | VM による Site Recovery の監視および診断データの書き込みを許可します。 |

#### <a name="outbound-connectivity-for-ip-address-ranges"></a>IP アドレス範囲に対する送信接続

ネットワーク セキュリティ グループ (NSG) を使用して接続を制御している場合は、ポート 443 への HTTPS アウトバウンドを許可するサービスタグ ベースの NSG ルールを作成します。対象となる[サービス タグ](../virtual-network/service-tags-overview.md#available-service-tags) (IP アドレスのグループ) は次のとおりです。

**Tag** | **許可** 
--- | ---
Storage タグ  |VM からキャッシュ ストレージ アカウントへのデータの書き込みを許可します。   
Azure AD タグ | Azure AD に対応するすべての IP アドレスへのアクセスを許可します。   
EventsHub タグ | Site Recovery 監視へのアクセスを許可します。  
AzureSiteRecovery タグ | 任意のリージョンの Site Recovery Service へのアクセスを許可します。   
GuestAndHybridManagement タグ | レプリケーションが有効な VM 上で稼働している Site Recovery モビリティ エージェントを自動的にアップグレードしたい場合に使用します。

必要なタグとタグ付けの例については、[詳しい情報](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)をご覧ください。

### <a name="verify-vm-certificates"></a>VM の証明書の確認

VM に最新のルート証明書が存在することを確認します。 存在しない場合、セキュリティの制約上、その VM を Site Recovery に登録することはできません。

- **Windows VM**:最新のすべての Windows 更新プログラムを VM にインストールして、すべての信頼されたルート証明書をマシンに用意します。 非接続環境では、自社の標準プロセスに従って、Windows Update および証明書の更新を実施してください。
- **Linux VM**:Linux ディストリビューターから提供されるガイダンスに従って、最新の信頼されたルート証明書と証明書失効リスト (CRL) を取得します。

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

VM のレプリケート元のソース リージョンを除く任意のリージョンに Recovery Services コンテナーを作成します。

1. [Azure portal](https://portal.azure.com) にサインインします。
2. 検索ボックスに、「*recovery*」と入力します。 **[サービス]** の **[Recovery Services コンテナー]** を選択します。

    ![Recovery Services コンテナーの検索](./media/azure-to-azure-tutorial-enable-replication/search.png)

3. **[Recovery Services コンテナー]** で、 **[追加]** を選択します。
4. **[Create Recovery Services vault]\(Recovery Services コンテナーの作成\)**  >  **[基本]** で、コンテナーを作成するサブスクリプションを選択します。
5. **[リソース グループ]** で、コンテナーの既存のリソース グループを選択するか、新しいリソース グループを作成します。
6. **[コンテナーの名前]** に、コンテナーを識別するフレンドリ名を指定します。
7. **[リージョン]** で、コンテナーの配置先となる Azure リージョンを選択します。 [サポート対象のリージョンを確認](https://azure.microsoft.com/pricing/details/site-recovery/)してください。
8. **[Review + create]\(レビュー + 作成\)** を選択します。

   ![コンテナーの新規作成ページのコンテナー設定](./media/azure-to-azure-tutorial-enable-replication/vault-basics.png)

9. **[確認および作成]** で、 **[作成]** を選択します。

10. コンテナーのデプロイが開始されます。 [通知] で進行状況を把握してください。
11. コンテナーがデプロイされたら、すぐに参照できるよう、 **[ダッシュボードにピン留めする]** を選択して保存します。 **[リソースに移動]** を選択して新しいコンテナーを開きます。 
    
    ![デプロイ後にコンテナーを開くためのボタンとダッシュボードにピン留めするためのボタン](./media/azure-to-azure-tutorial-enable-replication/vault-deploy.png)

### <a name="enable-site-recovery"></a>Site Recovery を有効にする

コンテナーの設定で、 **[Enable Site Recovery]\(Site Recovery を有効にする\)** を選択します。

![コンテナーで Site Recovery を有効にするためのオプション](./media/azure-to-azure-tutorial-enable-replication/enable-site-recovery.png)

## <a name="enable-replication"></a>レプリケーションを有効にする

ソースの設定を選択して、VM のレプリケーションを有効にします。 

### <a name="select-source-settings"></a>ソースの設定を選択する

1. コンテナーの **[Site Recovery]** ページで、 **[Azure 仮想マシン]** の **[レプリケーションを有効にする]** を選択します。

    ![Azure VM のレプリケーションを有効にするためのオプション](./media/azure-to-azure-tutorial-enable-replication/enable-replication.png)

2. **[ソース]** >  **[ソースの場所]** で、現在 VM が実行されているソース Azure リージョンを選択します。
3. **[Azure 仮想マシンのデプロイ モデル]** の設定は、既定の **[Resource Manager]** のままにします。
4. **[ソース サブスクリプション]** で、VM が実行されているサブスクリプションを選択します。 コンテナーと同じ Azure Active Directory (AD) テナントにあれば、どのサブスクリプションを選択してもかまいません。
5. **[ソース リソース グループ]** で、VM が存在するリソース グループを選択します。
6. **[可用性ゾーン間のディザスター リカバリーを行いますか?]** の設定は、既定の **[いいえ]** のままにします。

     ![Set up source](./media/azure-to-azure-tutorial-enable-replication/source.png)

7. **[次へ]** を選択します。

### <a name="select-the-vms"></a>VM を選択する

選択したサブスクリプションとリソース グループに関連付けられている VM が Site Recovery によって取得されます。

1. **[仮想マシン]** で、ディザスター リカバリーを有効にする VM を選択します。

     ![レプリケーションの対象となる VM を選択するためのページ](./media/azure-to-azure-tutorial-enable-replication/select-vm.png)

2. **[次へ]** を選択します。

### <a name="review-replication-settings"></a>レプリケーションの設定を確認する

1. **[レプリケーションの設定]** で設定を確認します。 Site Recovery によって、ターゲット リージョンの既定の設定とポリシーが作成されます。 このチュートリアルでは、その目的上、既定の設定を使用します。
2. **[レプリケーションを有効にする]** を選択します。

    ![設定をカスタマイズしてレプリケーションを有効にするためのページ](./media/azure-to-azure-tutorial-enable-replication/enable-vm-replication.png)   

3. [通知] で、レプリケーションの進行状況を追跡します。

     ![通知での進行状況の追跡](./media/azure-to-azure-tutorial-enable-replication/notification.png) ![通知でのレプリケーションの成功の追跡](./media/azure-to-azure-tutorial-enable-replication/notification-success.png)

4. 有効にした VM は、コンテナーの **[レプリケートされたアイテム]** ページに表示されます。

    ![[レプリケートされたアイテム] ページに表示された VM](./media/azure-to-azure-tutorial-enable-replication/replicated-items.png)


## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure VM のディザスター リカバリーを有効にしました。 今度は、訓練を実施して、想定したとおりにフェールオーバーが機能することを確認しましょう。

> [!div class="nextstepaction"]
> [ディザスター リカバリーのテストを実行する](azure-to-azure-tutorial-dr-drill.md)
