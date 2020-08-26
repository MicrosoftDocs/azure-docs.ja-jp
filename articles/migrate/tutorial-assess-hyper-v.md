---
title: Azure Migrate を使用して Azure に移行するために Hyper-V VM を評価する | Microsoft Docs
description: Azure Migrate Server Assessment を使用して Azure に移行するためにオンプレミスの Hyper-V VM を評価する方法について説明します。
ms.topic: tutorial
ms.date: 06/03/2020
ms.custom: mvc
ms.openlocfilehash: 4c95916bf5f92f8a82b9dfae50aa311891857e7a
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2020
ms.locfileid: "86114246"
---
# <a name="assess-hyper-v-vms-with-azure-migrate-server-assessment"></a>Azure Migrate Server Assessment を使用して Hyper-V VM を評価する

この記事では、[Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) ツールを使用してオンプレミスの Hyper-V VM を評価する方法について説明します。


これはシリーズの 2 番目のチュートリアルであり、Hyper-V VM を評価して Azure に移行する方法を示しています。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Migrate プロジェクトを設定する。
> * Azure Migrate アプライアンスを設定および登録する。
> * オンプレミス VM の継続的な検出を開始する。
> * 検出された VM をグループ化し、グループを評価する。
> * 評価を確認する。

> [!NOTE]
> チュートリアルでは、概念実証をすばやく設定できるように、シナリオの最も簡単なデプロイ パスを示します。 チュートリアルではできるだけ既定のオプションを使用しており、使用可能な設定とパスをすべて示しているわけではありません。 詳細な手順については、ハウツー記事を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。


## <a name="prerequisites"></a>前提条件

- このシリーズの最初のチュートリアルを[完了](tutorial-prepare-hyper-v.md)します。 そうしないと、このチュートリアルの手順はうまくいきません。
- 最初のチュートリアルでは、以下のことを行ったはずです。
    - Azure Migrate と連携するように [Azure を準備](tutorial-prepare-hyper-v.md#prepare-azure)します。
    - [Hyper-V のホストと VM の評価を準備](tutorial-prepare-hyper-v.md#prepare-for-assessment)します。
    - Hyper-V の評価用の Azure Migrate アプライアンスをデプロイするために必要なものを[確認](tutorial-prepare-hyper-v.md#prepare-for-appliance-deployment)します。

## <a name="set-up-an-azure-migrate-project"></a>Azure Migrate プロジェクトを設定する

1. Azure portal の **[すべてのサービス]** で、**Azure Migrate** を検索します。
2. 検索結果で、 **[Azure Migrate]** を選択します。
3. **[概要]** の **[サーバーの検出、評価、移行]** で、 **[サーバーの評価と移行]** をクリックします。

    ![サーバーの検出と評価](./media/tutorial-assess-hyper-v/assess-migrate.png)

4. **[作業の開始]** で、 **[ツールの追加]** をクリックします。
5. **[移行プロジェクト]** タブで、自分の Azure サブスクリプションを選択し、リソース グループがない場合は作成します。
6. **[プロジェクトの詳細]** で、プロジェクト名と、プロジェクトを作成したいリージョンを指定します。 [パブリック](migrate-support-matrix.md#supported-geographies-public-cloud)と [Government クラウド](migrate-support-matrix.md#supported-geographies-azure-government)でサポートされている地域を確認してください。

    - プロジェクトのリージョンは、オンプレミスの VM から収集されたメタデータを格納するためにのみ使用されます。
    - VM を移行するときに、別の Azure ターゲット リージョンを選択できます。 移行ターゲットとしては、すべての Azure リージョンがサポートされています。

    ![Azure Migrate プロジェクトの作成](./media/tutorial-assess-hyper-v/migrate-project.png)

7. **[次へ]** をクリックします。
8. **[評価ツールの選択]** で、次を選択します: **[Azure Migrate: Server Assessment]**  >  **[次へ]** 。

    ![Azure Migrate プロジェクトの作成](./media/tutorial-assess-hyper-v/assessment-tool.png)

9. **[移行ツールの選択]** で、 **[今は移行ツールの追加をスキップします]**  >  **[次へ]** の順に選択します。
10. **[ツールの確認と追加]** で設定を確認し、 **[ツールの追加]** をクリックします。
11. Azure Migrate プロジェクトがデプロイされるまで数分待ちます。 プロジェクトのページが表示されます。 プロジェクトが表示されない場合は、Azure Migrate ダッシュボードの **[サーバー]** からアクセスできます。

## <a name="set-up-the-azure-migrate-appliance"></a>Azure Migrate アプライアンスを設定する


Azure Migrate:Server Assessment では、軽量の Azure Migrate アプライアンスが使用されます。 このアプライアンスは VM の検出を実行し、VM のメタデータとパフォーマンス データを Azure Migrate に送信します。 このアプライアンスは、さまざまな方法で設定できます。

- ダウンロードした Hyper-V VHD を使用して Hyper-V VM 上に設定できます。 このチュートリアルでは、この方法を使用します。
- PowerShell インストーラー スクリプトを使用して Hyper-V VM 上または物理マシン上に設定します。 VHD を使用して VM を設定できない場合や、Azure Government をご利用の場合は、[この方法](deploy-appliance-script.md)を使用してください。

アプライアンスの作成後、Azure Migrate:Server Assessment に接続できることを確認し、最初の構成を行い、Azure Migrate プロジェクトに登録します。

### <a name="download-the-vhd"></a>VHD をダウンロードする

アプライアンス用の圧縮された VHD テンプレートをダウンロードします。

1. **[移行の目標]**  >  **[サーバー]**  >  **[Azure Migrate: Server Assessment]** で、 **[検出]** をクリックします。
2. **[マシンの検出]**  >  **[マシンは仮想化されていますか?]** で、 **[はい。Hyper-V を使用します]** をクリックします。
3. **[ダウンロード]** をクリックして、VHD ファイルをダウンロードします。

    ![VM をダウンロードする](./media/tutorial-assess-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>セキュリティを確認する

圧縮されたファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたマシンで、管理者用のコマンド ウィンドウを開きます。

2. 次の PowerShell コマンドを実行して、ZIP ファイルのハッシュを生成します
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - 使用例: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v1.19.06.27.zip -Algorithm SHA256```

3.  最新のアプライアンス バージョンとハッシュ値を確認します。

    - Azure パブリック クラウドの場合:

        **シナリオ** | **ダウンロード** | **SHA256**
        --- | --- | ---
        Hyper-V (8.93 GB) | [最新バージョン](https://aka.ms/migrate/appliance/hyperv) |  572be425ea0aca69a9aa8658c950bc319b2bdbeb93b440577264500091c846a1

    - Azure Government の場合:

        **シナリオ*** | **ダウンロード** | **SHA256**
        --- | --- | ---
        Hyper-V (63.1 MB) | [最新バージョン](https://go.microsoft.com/fwlink/?linkid=2120200&clcid=0x409) |  2c5e73a1e5525d4fae468934408e43ab55ff397b7da200b92121972e683f9aa3


### <a name="create-the-appliance-vm"></a>アプライアンス VM を作成する

ダウンロードしたファイルをインポートし、VM を作成します。

1. アプライアンス VM の配置先となる Hyper-V ホストに zip 圧縮済みの VHD ファイルをダウンロードしたら、その ZIP ファイルを展開します。
    - 展開先で、**AzureMigrateAppliance_VersionNumber** というフォルダーにファイルが解凍されます。
    - このフォルダーにも、**AzureMigrateAppliance_VersionNumber** というサブフォルダーが格納されています。
    - このサブフォルダーには、さらに **Snapshots**、**Virtual Hard Disks**、**Virtual Machines** という 3 つのサブフォルダーが格納されています。

2. Hyper-V マネージャーを開きます。 **[アクション]** で **[仮想マシンのインポート]** をクリックします。

    ![VHD をデプロイする](./media/tutorial-assess-hyper-v/deploy-vhd.png)

2. 仮想マシンのインポート ウィザードの **[開始する前に]** で、 **[次へ]** をクリックします。
3. **[フォルダーの検索]** で、 **[Virtual Machines]\(仮想マシン\)** フォルダーを選択します。 続けて、 **[次へ]** をクリックします。
1. **[仮想マシンを選択します]** で、 **[次へ]** をクリックします。
2. **[インポートの種類の選択]** で、 **[仮想マシンをコピーする (新しい一意な ID を作成する)]** をクリックします。 続けて、 **[次へ]** をクリックします。
3. **[移動先の選択]** は、既定の設定のままにします。 **[次へ]** をクリックします。
4. **[保存フォルダー]** は、既定の設定のままにします。 **[次へ]** をクリックします。
5. **[ネットワークの選択]** で、VM によって使用される仮想スイッチを指定します。 このスイッチには、Azure にデータを送信するためのインターネット接続が必要です。 仮想スイッチの作成に関するページを[参照](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines)してください。
6. **[概要]** で、設定を確認します。 **[完了]** をクリックします。
7. Hyper-V マネージャーの **[仮想マシン]** で、VM を起動します。


## <a name="verify-appliance-access-to-azure"></a>アプライアンスによる Azure へのアクセスを確認する

[パブリック](migrate-appliance.md#public-cloud-urls) クラウドと[政府機関向け](migrate-appliance.md#government-cloud-urls)クラウドの Azure URL にアプライアンス VM から接続できることを確認します。

### <a name="configure-the-appliance"></a>アプライアンスを構成する

アプライアンスを初めて設定します。

> [!NOTE]
> ダウンロードした VHD でなく、[PowerShell スクリプト](deploy-appliance-script.md)を使用してアプライアンスを設定する場合、この手順の最初の 2 つの操作は必要ありません。

1. Hyper-V マネージャーの **[仮想マシン]** で、VM を右クリックして **[接続]** を選択します。
2. アプライアンスの言語、タイム ゾーン、パスワードを指定します。
3. VM に接続できる任意のマシン上でブラウザーを開き、アプライアンス Web アプリの URL を開きます (**https://*アプライアンス名または IP アドレス*:44368**)。

   または、アプリ ショートカットをクリックして、アプライアンス デスクトップからアプリを開くこともできます。
1. Web アプリの **[前提条件のセットアップ]** で、以下を実行します。
    - **ライセンス**:ライセンス条項に同意し、サード パーティの情報を確認します。
    - **接続**:VM でインターネットにアクセスできることが、アプリによって確認されます。 VM でプロキシを使用する場合:
      - **[プロキシの設定]** をクリックし、 http://ProxyIPAddress または http://ProxyFQDN の形式で、プロキシ アドレスとリスニング ポートを指定します。
      - プロキシで認証が必要な場合は、資格情報を指定します。
      - サポートされるのは HTTP プロキシのみです。
    - **時刻同期**:時刻が確認されます。 VM の検出を正常に機能させるには、アプライアンス上の時刻がインターネットの時刻と同期している必要があります。
    - **更新プログラムのインストール**:Azure Migrate Server Assessment によって、アプライアンスに最新の更新プログラムがインストールされていることが確認されます。

### <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate にアプライアンスを登録する

1. **[ログイン]** をクリックします。 表示されない場合は、ブラウザーでポップアップ ブロックを無効にしてあることを確認します。
2. 新しいタブで、自分の Azure 資格情報を使用してサインインします。
    - 自分のユーザー名とパスワードを使用してサインインします。
    - PIN を使用したサインインはサポートされていません。
3. 正常にサインインした後、Web アプリに戻ります。
4. Azure Migrate プロジェクトが作成されたサブスクリプションを選択します。 その後、プロジェクトを選択します。
5. アプライアンスの名前を指定します。 名前は、14 文字以下の英数字にする必要があります。
6. **[登録]** をクリックします。


### <a name="delegate-credentials-for-smb-vhds"></a>SMB VHD の資格情報を委任する

SMB 上で VHD を実行している場合は、アプライアンスから Hyper-V ホストへの資格情報の委任を有効にする必要があります。 これを行うには、各ホストがアプライアンスの代理として機能できるようにします。 チュートリアルを順番に行っていれば、この操作は、前のチュートリアルで評価と移行のために Hyper-V を準備した時点で完了しています。 ホストの CredSSP を[手動で](tutorial-prepare-hyper-v.md#enable-credssp-to-delegate-credentials)設定したか、その操作を行う[スクリプトを実行して](tutorial-prepare-hyper-v.md#run-the-script)設定したはずです。

次のようにアプライアンス上で有効にします。

#### <a name="option-1"></a>方法 1

アプライアンス VM 上で、このコマンドを実行します。 HyperVHost1/HyperVHost2 は、ホスト名の例です。

```
Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
```

例: ` Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force `

#### <a name="option-2"></a>方法 2

または、アプライアンス上のローカル グループ ポリシー エディターでこれを行います。

1. **[ローカル コンピューター ポリシー]**  >  **[コンピューターの構成]** で、 **[管理用テンプレート]**  >  **[システム]**  >  **[資格情報の委任]** の順にクリックします。
2. **[新しい資格情報の委任を許可する]** をダブルクリックし、 **[有効]** を選択します。
3. **[オプション]** で **[表示]** をクリックし、検出したい各 Hyper-V ホストを、プレフィックスとして **wsman/** を付けて、一覧に追加します。
4. 次に、 **[資格情報の委任]** で、 **[NTLM のみのサーバー認証で新しい資格情報の委任を許可する]** をダブルクリックします。 もう一度、検出したい各 Hyper-V ホストを、プレフィックスとして **wsman/** を付けて、一覧に追加します。

## <a name="start-continuous-discovery"></a>継続的な検出を開始する

アプライアンスから Hyper-V ホストまたはクラスターに接続し、VM の検出を開始します。

1. **[ユーザー名]** と **[パスワード]** に、VM を検出するためにアプライアンスによって使用されるアカウント資格情報を指定します。 資格情報のフレンドリ名を指定し、 **[詳細の保存]** をクリックします。
2. **[ホストの追加]** をクリックし、Hyper-V のホスト/クラスターの詳細を指定します。
3. **[検証]** をクリックします。 検証後、各ホスト/クラスター上で検出できる VM の数が表示されます。
    - ホストで検証が失敗した場合は、 **[状態]** 列のアイコンをポイントしてエラーを確認します。 問題を修正し、もう一度検証します。
    - ホストまたはクラスターを削除するには、 **[削除]** を選択します。
    - クラスターから特定のホストを削除することはできません。 削除できるのは、クラスター全体のみです。
    - クラスター内の特定のホストに問題がある場合でも、クラスターを追加できます。
4. 検証後、 **[保存して検出を開始]** をクリックして、検出プロセスを開始します。

これで検出が開始されます。 検出されたサーバーのメタデータが Azure portal に表示されるまでにホストあたり約 1.5 分かかります。

### <a name="verify-vms-in-the-portal"></a>ポータル内での VM の特定

検出の完了後、VM がポータルに表示されることを確認できます。

1. Azure Migrate ダッシュボードを開きます。
2. **[Azure Migrate - サーバー]**  >  **[Azure Migrate: Server Assessment]** ページで、 **[検出済みサーバー]** の数を表示するアイコンをクリックします。

## <a name="set-up-an-assessment"></a>評価を設定する

Azure Migrate Server Assessment を使用して実行できる評価には、2 つの種類があります。

**評価** | **詳細** | **データ**
--- | --- | ---
**パフォーマンスベース** | 収集されたパフォーマンス データに基づく評価 | **推奨される VM サイズ**: CPU とメモリの使用率データに基づきます。<br/><br/> **推奨されるディスクの種類 (Standard または Premium マネージド ディスク)** : オンプレミス ディスクの IOPS とスループットに基づきます。
**オンプレミス** | オンプレミスのサイズ設定に基づく評価。 | **推奨される VM サイズ**: オンプレミスの VM サイズに基づきます<br/><br> **推奨されるディスクの種類**: 評価用に選択するストレージの種類の設定に基づきます。



### <a name="run-an-assessment"></a>評価を実行する

評価を実行するには次のようにします。

1. 評価を作成するための[ベスト プラクティス](best-practices-assessment.md)を確認します。
2. **[サーバー]**  >  **[Azure Migrate: Server Assessment]** で、 **[評価]** をクリックします。

    ![アクセス](./media/tutorial-assess-hyper-v/assess.png)

3. **[サーバーの評価]** で、評価の名前を指定します。
4. **[すべて表示]** をクリックして、評価のプロパティを確認します。

    ![評価のプロパティ](./media/tutorial-assess-hyper-v/assessment-properties.png)

3. **[グループを選択または作成します]** で **[新規作成]** を選択し、グループ名を指定します。 グループで、評価のために 1 つ以上の VM をまとめます。
4. **[グループにマシンを追加します]** で、グループに追加する VM を選択します。
5. **[評価を作成します]** をクリックして、グループを作成し、評価を実行します。

    ![評価を作成する](./media/tutorial-assess-hyper-v/assessment-create.png)

6. 評価が作成されたら、それを表示します ( **[サーバー]**  >  **[Azure Migrate: Server Assessment]** を使用した評価と依存関係の視覚化に関する問題のトラブルシューティングに役立ちます。
7. **[評価のエクスポート]** をクリックし、Excel ファイルとしてダウンロードします。


## <a name="review-an-assessment"></a>評価を確認する

評価には以下が記述されています。

- **Azure 対応性**: VM が Azure への移行に適しているかどうか。
- **月間コスト見積もり**: Azure で VM を実行するためのコンピューティングとストレージの月間推定コスト。
- **月間ストレージ コスト見積もり**: 移行後のディスク ストレージの推定コスト。


### <a name="view-an-assessment"></a>評価を表示する

1. **[移行の目標]**  >   **[サーバー]**  >  **[Azure Migrate: Server Assessment]** で、 **[評価]** をクリックします。
2. **[評価]** で、評価をクリックして開きます。

    ![評価の概要](./media/tutorial-assess-hyper-v/assessment-summary.png)


### <a name="review-azure-readiness"></a>Azure 対応性の確認

1. **[Azure 対応性]** で、VM が Azure への移行の準備ができているかどうかを確認します。
2. VM の状態を確認します。
    - **[Azure に対応]** : Azure Migrate によって、評価内の VM の VM サイズとコスト見積もりが推奨されます。
    - **[条件付きで対応]** : 問題と推奨される修復方法が示されます。
    - **[Azure に未対応]** : 問題と推奨される修復方法が示されます。
    - **[Readiness 不明]** : データの可用性の問題により、Azure Migrate が対応性を評価できない場合に使用されます。

2. **[Azure 対応性]** の状態をクリックします。 VM 対応性の詳細を表示し、ドリルダウンして、コンピューティング、ストレージ、ネットワークの設定など、VM の詳細を表示できます。

### <a name="review-cost-details"></a>コスト詳細の確認

このビューには、Azure で VM を実行する際のコンピューティングおよびストレージの推定コストが表示されます。

1. 月間のコンピューティングおよびストレージのコストを確認します。 コストは、評価されるグループ内のすべての VM について集計されます。

    - コストの見積もりは、マシンの推奨サイズ、およびそのディスクとプロパティに基づいています。
    - コンピューティングとストレージの月間推定コストが表示されます。
    - コストの見積もりは、IaaS VM としてのオンプレミス VM の実行に対するものです。 Azure Migrate Server Assessment では、PaaS または SaaS のコストは考慮されません。

2. 月間ストレージ コストの見積もりを確認できます。 このビューには、評価されたグループの集計されたストレージ コストが、ストレージ ディスクの種類ごとに分けて表示されます。
3. ドリルダウンして、特定の VM の詳細を見ることができます。


### <a name="review-confidence-rating"></a>信頼度レーティングを確認する

パフォーマンスベースの評価を実行すると、評価に信頼度レーティングが割り当てられます。

![信頼度レーティング](./media/tutorial-assess-hyper-v/confidence-rating.png)

- 1 つ星 (最低) から 5 つ星 (最高) までの評価が与えられます。
- 信頼度レーティングは、評価による推奨サイズの信頼性を判断する目安となります。
- 信頼度レーティングは、評価の計算に必要なデータ ポイントの可用性に基づいています。

評価の信頼度レーティングは、次のとおりです。

**データ ポイントの可用性** | **信頼度レーティング**
--- | ---
0% - 20% | 1 つ星
21% - 40% | 2 つ星
41% - 60% | 3 つ星
61% - 80% | 4 つ星
81% - 100% | 5 つ星

評価レーティングのベスト プラクティスについて、[さらに学習](best-practices-assessment.md#best-practices-for-confidence-ratings)してください。





## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次のことを行いました。

> [!div class="checklist"]
> * Azure Migrate アプライアンスを設定する
> * 評価を作成して確認する

このシリーズの 3 番目のチュートリアルに進み、Azure Migrate Server Migration を使用して Hyper-V VM を Azure に移行する方法について学習します。

> [!div class="nextstepaction"]
> [Hyper-V VM を移行する](./tutorial-migrate-hyper-v.md)
