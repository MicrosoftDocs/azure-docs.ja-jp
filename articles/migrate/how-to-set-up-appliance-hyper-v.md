---
title: Hyper-V 向け Azure Migrate アプライアンスを設定する
description: Azure Migrate アプライアンスを設定して Hyper-V VM を評価し移行する方法について説明します。
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: 21d88c4a2b2095fe677fe479bd7320f7a494db9e
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88929937"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Hyper-V VM のアプライアンスを設定する

この記事を読み、[Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) ツールによる Hyper-V VM の評価のために、Azure Migrate アプライアンスを設定してください。

[Azure Migrate アプライアンス](migrate-appliance.md)は、Azure Migrate:Server Assessment/Migration によって、オンプレミス Hyper-V VM を検出し、VM メタデータ/パフォーマンス データを Azure に送信するために使用される軽量アプライアンスです。

アプライアンスは、次の 2 とおりの方法でデプロイできます。

- ダウンロードした VHD を使用して Hyper-V VM 上に設定できます。 この記事で説明しているのは、こちらの方法です。
- PowerShell インストーラー スクリプトを使用して Hyper-V VM 上または物理マシン上に設定します。 VHD を使用して VM を設定できない場合や、Azure Government をご利用の場合は、[この方法](deploy-appliance-script.md)を使用してください。

アプライアンスの作成後、Azure Migrate:Server Assessment に接続できることを確認し、最初の構成を行い、Azure Migrate プロジェクトに登録します。

## <a name="appliance-deployment-vhd"></a>アプライアンスのデプロイ (VHD)

VHD テンプレートを使用してアプライアンスを設定するには:

- アプライアンス名を指定し、ポータルで Azure Migrate プロジェクト キーを生成します。
- Azure portal から圧縮された Hyper-V VHD をダウンロードします。
- アプライアンスを作成し、それが Azure Migrate Server Assessment に接続できることを確認します。
- アプライアンスを初めて構成し、Azure Migrate プロジェクト キーを使用して Azure Migrate プロジェクトに登録します。

### <a name="generate-the-azure-migrate-project-key"></a>Azure Migrate プロジェクト キーを生成する

1. **移行の目標** > **サーバー** > **Azure Migrate: Server Assessment** で、**検出** を選択します。
2. **[マシンの検出]**  >  **[マシンは仮想化されていますか?]** で、 **[はい。Hyper-V を使用します]** を選択します。
3. **[1:Azure Migrate プロジェクト キーを生成します]** で、Hyper-V VM の検出用に設定する Azure Migrate アプライアンスの名前を指定します。名前は 14 文字以内の英数字にする必要があります。
1. **[キーの生成]** をクリックして、必要な Azure リソースの作成を開始します。 リソースの作成中に [マシンの検出] ページを閉じないでください。
1. Azure リソースが正常に作成されると、**Azure Migrate プロジェクト キー**が生成されます。
1. このキーはアプライアンスを設定する際、登録を完了するために必要なので、コピーしておきます。

### <a name="download-the-vhd"></a>VHD をダウンロードする

**[2:Azure Migrate アプライアンスをダウンロードする]** で、.VHD ファイルを選択し、 **[ダウンロード]** をクリックします。 

   ![[マシンの検出] の選択](./media/tutorial-assess-hyper-v/servers-discover.png)


   ![[キーの生成] の選択](./media/tutorial-assess-hyper-v/generate-key-hyperv.png)


### <a name="verify-security"></a>セキュリティを確認する

圧縮されたファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたマシンで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、VHD のハッシュを生成します
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用例: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.vhd SHA256```
3.  アプライアンス バージョン 2.19.11.12 の場合は、生成されたハッシュがこれらの[設定](./tutorial-assess-hyper-v.md#verify-security)と一致する必要があります。




## <a name="create-the-appliance-vm"></a>アプライアンス VM を作成する

ダウンロードしたファイルをインポートし、VM を作成します。

1. アプライアンス VM をホストする Hyper-V ホスト上のフォルダーに、圧縮された VHD ファイルを抽出します。 3 つのフォルダーが抽出されます。
2. Hyper-V マネージャーを開きます。 **[アクション]** で **[仮想マシンのインポート]** をクリックします。

    ![VHD をデプロイする](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. 仮想マシンのインポート ウィザードの **[開始する前に]** で、 **[次へ]** をクリックします。
3. **[フォルダーの検索]** で、抽出された VHD が含まれているフォルダーを指定します。 続けて、 **[次へ]** をクリックします。
1. **[仮想マシンを選択します]** で、 **[次へ]** をクリックします。
2. **[インポートの種類の選択]** で、 **[仮想マシンをコピーする (新しい一意な ID を作成する)]** をクリックします。 続けて、 **[次へ]** をクリックします。
3. **[移動先の選択]** は、既定の設定のままにします。 **[次へ]** をクリックします。
4. **[保存フォルダー]** は、既定の設定のままにします。 **[次へ]** をクリックします。
5. **[ネットワークの選択]** で、VM によって使用される仮想スイッチを指定します。 このスイッチには、Azure にデータを送信するためのインターネット接続が必要です。
6. **[概要]** で、設定を確認します。 **[完了]** をクリックします。
7. Hyper-V マネージャーの **[仮想マシン]** で、VM を起動します。


### <a name="verify-appliance-access-to-azure"></a>アプライアンスによる Azure へのアクセスを確認する

[パブリック](migrate-appliance.md#public-cloud-urls) クラウドと[政府機関向け](migrate-appliance.md#government-cloud-urls)クラウドの Azure URL にアプライアンス VM から接続できることを確認します。

### <a name="configure-the-appliance"></a>アプライアンスを構成する

アプライアンスを初めて設定します。

> [!NOTE]
> ダウンロードした VHD でなく、[PowerShell スクリプト](deploy-appliance-script.md)を使用してアプライアンスを設定する場合、この手順の最初の 2 つの操作は必要ありません。

1. Hyper-V マネージャーの **[仮想マシン]** で、VM を右クリックして **[接続]** を選択します。
2. アプライアンスの言語、タイム ゾーン、パスワードを指定します。
3. VM に接続できる任意のマシン上でブラウザーを開き、アプライアンス Web アプリの URL を開きます (**https://*アプライアンス名または IP アドレス*:44368**)。

   または、アプリ ショートカットをクリックして、アプライアンス デスクトップからアプリを開くこともできます。
1. **ライセンス条項**に同意し、サード パーティの情報を確認します。
1. Web アプリの **[前提条件のセットアップ]** で、以下を実行します。
    - **接続**:VM でインターネットにアクセスできることが、アプリによって確認されます。 VM でプロキシを使用する場合:
      - **[プロキシの設定]** をクリックし、プロキシ アドレス (http://ProxyIPAddress または http://ProxyFQDN) の形式) とリッスン ポートを指定します。
      - プロキシで認証が必要な場合は、資格情報を指定します。
      - サポートされるのは HTTP プロキシのみです。
      - プロキシの詳細を追加した場合、またはプロキシまたは認証を無効にした場合は、 **[保存]** をクリックして接続チェックを再度トリガーします。
    - **時刻同期**:時刻が確認されます。 VM の検出を正常に機能させるには、アプライアンス上の時刻がインターネットの時刻と同期している必要があります。
    - **更新プログラムのインストール**:Azure Migrate Server Assessment では、アプライアンスに最新の更新プログラムがインストールされているかどうかの確認が行われます。チェックが完了したら、 **[View appliance services]\(アプライアンス サービスを表示\)** をクリックして、アプライアンスで実行されているコンポーネントの状態とバージョンを確認できます。

### <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate にアプライアンスを登録する

1. ポータルからコピーした **Azure Migrate プロジェクト キー**を貼り付けます。 このキーがない場合は、 **[Server Assessment] > [検出] > [Manage existing appliances]\(既存のアプライアンスの管理\)** に移動して、キーの生成時に指定したアプライアンス名を選択して、対応するキーをコピーします。
1. **[ログイン]** をクリックします。 新しいブラウザー タブで Azure ログイン プロンプトが開きます。表示されない場合は、ブラウザーでポップアップ ブロックを無効にしてあることを確認します。
1. 新しいタブで、Azure のユーザー名とパスワードを使用してサインインします。
   
   PIN を使用したサインインはサポートされていません。
3. 正常にログインしたら、Web アプリに戻ります。 
4. ログに使用した Azure ユーザー アカウントに、キーの生成時に作成した Azure リソースに対する正しい[アクセス許可](tutorial-prepare-hyper-v.md#prepare-azure)が付与されている場合、アプライアンスの登録が開始されます。
1. アプライアンスが正常に登録された後は、 **[詳細の表示]** をクリックすることで登録の詳細を確認できるようになります。



### <a name="delegate-credentials-for-smb-vhds"></a>SMB VHD の資格情報を委任する

SMB 上で VHD を実行している場合は、アプライアンスから Hyper-V ホストへの資格情報の委任を有効にする必要があります。 アプライアンスからこれを行うには、次のようにします。

1. アプライアンス VM 上で、このコマンドを実行します。 HyperVHost1/HyperVHost2 は、ホスト名の例です。

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. または、アプライアンス上のローカル グループ ポリシー エディターでこれを行います。
    - **[ローカル コンピューター ポリシー]**  >  **[コンピューターの構成]** で、 **[管理用テンプレート]**  >  **[システム]**  >  **[資格情報の委任]** の順にクリックします。
    - **[新しい資格情報の委任を許可する]** をダブルクリックし、 **[有効]** を選択します。
    - **[オプション]** で **[表示]** をクリックし、検出したい各 Hyper-V ホストを、プレフィックスとして **wsman/** を付けて、一覧に追加します。
    - **[資格情報の委任]** で **[NTLM のみのサーバー認証で新しい資格情報の委任を許可する]** をダブルクリックします。 もう一度、検出したい各 Hyper-V ホストを、プレフィックスとして **wsman/** を付けて、一覧に追加します。

## <a name="start-continuous-discovery"></a>継続的な検出を開始する

アプライアンスから Hyper-V ホストまたはクラスターに接続し、VM の検出を開始します。

1. **[ステップ 1:Provide Hyper-V host credentials]\(ステップ 1: Hyper-V ホストの資格情報を指定する\)** で、 **[Add credentials]\(資格情報の追加\)** をクリックして資格情報のフレンドリ名を指定し、アプライアンスで VM の検出に使用される Hyper-V ホストまたはクラスターの **[ユーザー名]** と **[パスワード]** を追加します。 **[Save]** をクリックします。
1. 複数の資格情報を一度に追加するには、 **[Add more]\(さらに追加\)** をクリックして資格情報を保存して追加します。 Hyper-V VM の検出では、複数の資格情報がサポートされています。
1. **[ステップ 2:Provide Hyper-V host/cluster details]\(ステップ 2: Hyper-V ホストまたはクラスターの詳細を指定する\)** で、 **[Add discovery source]\(検出ソースの追加\)** をクリックして、Hyper-V ホストまたはクラスターの **[IP address/FQDN]\(IP アドレスまたは FQDN\)** と、ホストまたはクラスターに接続するための資格情報のフレンドリ名を指定します。
1. 一度に **1 つの項目を追加**するか、一括で**複数の項目を追加**することができます。 また、 **[Import CSV]\(CSV のインポート\)** を使用して、Hyper-V ホストまたはクラスターの詳細を指定することもできます。

    ![検出ソースを追加するための選択](./media/tutorial-assess-hyper-v/add-discovery-source-hyperv.png)

    - **[Add single item]\(1 つの項目を追加\)** を選択した場合は、資格情報のフレンドリ名と Hyper-V ホストまたはクラスターの **[IP address/FQDN]\(IP アドレスまたは FQDN\)** を指定して **[保存]** をクリックします。
    - **[Add multiple items]\(複数の項目を追加\)** _(既定で選択)_ を選択した場合は、テキスト ボックスで Hyper-V ホストまたはクラスターの **[IP address/FQDN]\(IP アドレスまたは FQDN\)** を資格情報のフレンドリ名と共に指定することで、一度に複数のレコードを追加できます。追加したレコードを **[Verify]\(検証\)** し、 **[保存]** をクリックします。
    - **[Import CSV]\(CSV のインポート\)** を選択した場合は、CSV テンプレート ファイルをダウンロードし、そのファイルに Hyper-V ホストまたはクラスターの **[IP address/FQDN]\(IP アドレスまたは FQDN\)** および資格情報のフレンドリ名を入力できます。 その後、ファイルをアプライアンスにインポートし、ファイル内のレコードを **[Verify]\(検証\)** して、 **[保存]** をクリックします。

1. [保存] をクリックすると、アプライアンスによって追加された Hyper-V ホストまたはクラスターへの接続が検証され、各ホストまたはクラスターの **[Validation status]\(検証状態\)** が表に表示されます。
    - 正常に検証されたホストまたはクラスターについては、その IP アドレスまたは FQDN をクリックすることで詳細を表示できます。
    - ホストの検証に失敗した場合は、表の状態列にある **[Validation failed]\(検証に失敗しました\)** をクリックしてエラーを確認します。 問題を修正し、もう一度検証してください。
    - ホストまたはクラスターを削除するには、 **[Delete]\(削除\)** をクリックします。
    - クラスターから特定のホストを削除することはできません。 削除できるのは、クラスター全体のみです。
    - クラスター内の特定のホストに問題がある場合でも、クラスターを追加できます。
1. 検出を開始する前に、ホストまたはクラスターへの接続はいつでも**再検証**できます。
1. 正常に検証されたホストまたはクラスターから VM の検出を開始するには **[Start discovery]\(検出の開始\)** をクリックします。 検出が正常に開始されたら、各ホストまたはクラスターに対する検出の状態を表で確認できます。

これで検出が開始されます。 検出されたサーバーのメタデータが Azure portal に表示されるまでにホストあたり約 2 分かかります。

## <a name="verify-vms-in-the-portal"></a>ポータル内での VM の特定

検出の完了後、VM がポータルに表示されることを確認できます。

1. Azure Migrate ダッシュボードを開きます。
2. **[Azure Migrate - サーバー]**  >  **[Azure Migrate: Server Assessment]** ページで、 **[検出済みサーバー]** の数を表示するアイコンをクリックします。


## <a name="next-steps"></a>次のステップ

Azure Migrate Server Assessment による [Hyper-V 評価](tutorial-assess-hyper-v.md)を試します。
