---
title: 自己ホスト型統合ランタイムを作成する
description: Azure Data Factory でセルフホステッド統合ランタイムを作成する方法について説明します。これにより、データ ファクトリからプライベート ネットワーク内のデータ ストアにアクセスできるようになります。
ms.service: data-factory
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.date: 02/10/2021
ms.openlocfilehash: 3e61b6a0f17d2d21aaaebc5ff42b0221cf851a4b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100389506"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>セルフホステッド統合ランタイムを作成して構成する

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

統合ランタイム (IR) は、異なるネットワーク環境間でデータ統合機能を提供するために Azure Data Factory によって使用されるコンピューティング インフラストラクチャです。 IR の詳細については、[ランタイム統合の概要](concepts-integration-runtime.md)に関するページを参照してください。

セルフホステッド統合ランタイムにより、クラウド データ ストアとプライベート ネットワーク内のデータ ストアの間でコピー アクティビティを実行できます。 また、オンプレミス ネットワークまたは Azure Virtual Network 内のコンピューティング リソースに対して変換アクティビティをディスパッチすることができます。 セルフホステッド統合ランタイムは、オンプレミス コンピューター、またはプライベート ネットワーク内の仮想マシンにインストールする必要があります。  

この記事では、セルフホステッド IR を作成して構成する方法について説明します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="considerations-for-using-a-self-hosted-ir"></a>セルフホステッド IR の使用に関する注意点

- 1 つのセルフホステッド統合ランタイムを複数のオンプレミス データ ソースで使用できます。 同じ Azure Active Directory (Azure AD) テナント内の別のデータ ファクトリと共有することもできます。 詳細については、[セルフホステッド統合ランタイムの共有](./create-shared-self-hosted-integration-runtime-powershell.md)に関するセクションを参照してください。
- 単一コンピューター上にインストールできるセルフホステッド統合ランタイムのインスタンスは 1 つのみとなります。 オンプレミス データ ソースにアクセスする必要があるデータ ファクトリが 2 つある場合、[セルフホステッド IR 共有機能](./create-shared-self-hosted-integration-runtime-powershell.md)を使用してセルフホステッド IR を共有するか、またはデータ ファクトリごとに 2 つのオンプレミス コンピューターそれぞれにセルフホステッド IR をインストールします。  
- セルフホステッド統合ランタイムは、データ ソースと同じコンピューター上に存在する必要はありません。 しかし、セルフホステッド統合ランタイムをデータ ソースの近くに配置することにより、セルフホステッド統合ランタイムからデータ ソースへの接続時間が短縮されます。 セルフホステッド統合ランタイムは、オンプレミス データ ソースをホストするコンピューターとは異なるコンピューターにインストールすることをお勧めします。 セルフホステッド統合ランタイムとデータ ソースが別のコンピューター上にある場合、セルフホステッド統合ランタイムではリソースのデータ ソースとの競合は発生しません。
- 同じオンプレミス データ ソースに接続する異なるコンピューター上で、複数のセルフホステッド統合ランタイムを使用することができます。 たとえば、2 つのデータ ファクトリを提供する 2 つのセルフホステッド統合ランタイムがある場合、どちらのデータ ファクトリにも同じオンプレミス データ ソースを登録できます。
- セルフホステッド統合ランタイムを使用して、Azure Virtual Network 内のデータ統合をサポートします。
- Azure ExpressRoute を使用する場合でも、ファイアウォールの背後にあるオンプレミス データ ソースとしてデータ ソースを扱います。 セルフホステッド統合ランタイムを使用して、サービスをデータ ソースに接続します。
- データ ストアがクラウド内の Azure IaaS (サービスとしてのインフラストラクチャ) 仮想マシン上にある場合でも、セルフホステッド統合ランタイムを使用します。
- FIPS 準拠の暗号化が有効になっている Windows Server 上にインストールされているセルフホステッド統合ランタイムでは、タスクが失敗する可能性があります。 この問題を回避するには、資格情報/シークレット値を Azure Key Vault に保存するか、またはサーバーで FIPS 準拠の暗号化を無効にする 2 つのオプションがあります。 FIPS 準拠の暗号化を無効にするには、レジストリ サブキー `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled` の値を 1 (有効) から 0 (無効) に変更します。 [セルフホステッド統合ランタイムを SSIS 統合ランタイムのプロキシとして](./self-hosted-integration-runtime-proxy-ssis.md)使用する場合、FIPS 準拠の暗号化を有効にすることができ、オンプレミスからステージング領域として Azure Blob Storage にデータを移動するときに使用されます。

## <a name="command-flow-and-data-flow"></a>コマンド フローとデータ フロー

オンプレミスとクラウドの間でデータを移動する場合、アクティビティではセルフホステッド統合ランタイムを使用して、オンプレミス データ ソースとクラウドの間でデータを転送します。

セルフホステッド IR でコピーするデータ フロー手順の概要を以下に示します。

![データ フローの大まかな概要](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. データ開発者は、Azure portal または PowerShell コマンドレットを使用して Azure Data Factory 内でセルフホステッド統合ランタイムを作成します。

2. データ開発者は、オンプレミスのデータ ストアに使用するリンクされたサービスを作成します。 開発者は、データ ストアへの接続にサービスが使用するセルフホステッド統合ランタイムのインスタンスを指定することによって、この操作を行います。

3. セルフホステッド統合ランタイム ノードでは、Windows DPAPI (Data Protection Application Programming Interface) を使用して資格情報を暗号化し、その資格情報をローカルに保存します。 高可用性を目的として複数ノードが設定されている場合、資格情報はさらに他のノード間で同期されます。 各ノードでは、DPAPI を使用して資格情報を暗号化し、それらをローカルに格納します。 資格情報の同期は、データ開発者に透過的であり、セルフホステッド IR によって処理されます。

4. Azure Data Factory は、セルフホステッド統合ランタイムと通信して、ジョブのスケジュール設定と管理を行います。 通信は、[Azure Relay](../azure-relay/relay-what-is-it.md#wcf-relay) 共有接続を使用する制御チャネルを介して行われます。 アクティビティ ジョブを実行する必要がある場合、Data Factory はリクエストと資格情報をキューに入れます。 これは、セルフホステッド統合ランタイムに資格情報がまだ格納されていない場合に備えて行われます。 セルフホステッド統合ランタイムでは、そのキューをポーリングした後、ジョブを開始します。

5. セルフホステッド統合ランタイムによって、オンプレミス ストアとクラウドとの間でデータがコピーされます。 コピーの方向は、データ パイプライン内でのコピー アクティビティの構成方法によって異なります。 この手順では、セルフホステッド統合ランタイムは、セキュリティで保護された HTTPS チャネルを使用して、クラウド ベースのストレージ サービス (Azure Blob Storage など) と直接通信を行います。

## <a name="prerequisites"></a>前提条件

- サポートされている Windows のバージョンは次のとおりです。
  - Windows 8.1
  - Windows 10
  - Windows Server 2012
  - Windows Server 2012 R2
  - Windows Server 2016
  - Windows Server 2019

セルフホステッド統合ランタイムのドメイン コントローラーへのインストールはサポートされていません。

- セルフホステッド統合ランタイムには、.NET Framework 4.7.2 以降を含む 64 ビット オペレーティング システムが必要です。 詳細については、「 [.NET Framework システム要件](/dotnet/framework/get-started/system-requirements) 」をご覧ください。
- セルフホステッド統合ランタイム コンピューターに推奨される最小構成は、4 コアの 2 GHz プロセッサ、8 GB の RAM、および 80 GB の使用可能なハード ドライブ領域です。 システム要件の詳細については、[ダウンロード](https://www.microsoft.com/download/details.aspx?id=39717)のページを参照してください。
- ホスト コンピューターが休止状態の場合、セルフホステッド統合ランタイムはデータ要求に応答しません。 セルフホステッド統合ランタイムをインストールする前に、コンピューターに適切な電源プランを構成します。 コンピューターが休止状態に入るよう構成されている場合、セルフホステッド統合ランタイムのインストーラーによりメッセージが出力されます。
- セルフホステッド統合ランタイムを正常にインストールして構成するには、コンピューターの管理者である必要があります。
- コピー アクティビティは特定の頻度で実行されます。 コンピューター上のプロセッサおよび RAM の使用量は、ピーク時とアイドル時のある同じパターンに従います。 また、リソース使用量は、移動されるデータの量に大きく依存します。 複数のコピー ジョブが進行中のときには、ピーク時にリソース使用率が上昇します。
- Parquet、ORC、または Avro 形式のデータの抽出中にタスクが失敗することがあります。 Parquet の詳細については、「[Azure Data Factory での Parquet 形式](./format-parquet.md#using-self-hosted-integration-runtime)」を参照してください。 ファイルの作成は、セルフホステッド統合コンピューターで実行されます。 予想どおりに動作するには、ファイルの作成に次の前提条件が必要です。
  - [Visual C++ 2010 再領布](https://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe)パッケージ (x64)
  - [Adopt OpenJDK](https://adoptopenjdk.net/) などの JRE プロバイダーの Java Runtime (JRE) バージョン 8。 `JAVA_HOME` 環境変数が、JRE フォルダー (JDK フォルダーだけでなく) に設定されていることを確認します。

## <a name="setting-up-a-self-hosted-integration-runtime"></a>セルフホステッド統合ランタイムをセットアップする

セルフホステッド統合ランタイムを作成およびセットアップするには、次の手順に従います。

### <a name="create-a-self-hosted-ir-via-azure-powershell"></a>Azure PowerShell を使用してセルフホステッド IR を作成する

1. このタスクには Azure PowerShell を使用できます。 たとえば次のようになります。

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. セルフホステッド統合ランタイムをローカル コンピューターに[ダウンロード](https://www.microsoft.com/download/details.aspx?id=39717)してインストールします。

3. 認証キーを取得し、そのキーを使用してセルフホステッド統合ランタイムを登録します。 PowerShell の例を次に示します。

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

### <a name="create-a-self-hosted-ir-via-azure-data-factory-ui"></a>Azure Data Factory の UI を使用してセルフホステッド IR を作成する

Azure Data Factory の UI を使用してセルフホステッド IR を作成するには、次の手順に従います。

1. Azure Data Factory の UI の **[Let's get started]\(始めましょう\)** ページで、左端のペインの [[管理] タブ](./author-management-hub.md)を選択します。

   ![ホーム ページの [管理] ボタン](media/doc-common-process/get-started-page-manage-button.png)

1. 左ペインの **[統合ランタイム]** を選択し、 **[+ 新規]** を選択します。

   ![統合ランタイムの作成](media/doc-common-process/manage-new-integration-runtime.png)

1. **[Integration runtime setup]\(統合ランタイムのセットアップ\)** ページで、 **[Azure, Self-Hosted]\(Azure、セルフホステッド\)** を選択してから、 **[Continue]\(続行\)** を選択します。

1. 次のページで、セルフホステッド IR を作成する **[Self-Hosted]\(セルフホステッド\)** を選択してから、 **[Continue]\(続行\)** を選択します。
   ![セルフホステッド IR を作成する](media/create-self-hosted-integration-runtime/new-selfhosted-integration-runtime.png)

1. IR の名前を入力し、 **[作成]** を選択します。

1. **[Integration runtime setup]\(統合ランタイムのセットアップ\)** ページで、 **[Option 1]\(オプション 1\)** の下にあるリンクを選択して、コンピューターで高速セットアップを開きます。 または、**オプション 2** の手順に従って、手動でセットアップします。 以降の手順は、手動セットアップに基づいています。

   ![統合ランタイムのセットアップ](media/create-self-hosted-integration-runtime/integration-runtime-setting-up.png)

    1. 認証キーをコピーして貼り付けます。 **[統合ランタイムのダウンロードとインストール]** を選択します。

    1. セルフホステッド統合ランタイムをローカルの Windows マシンにダウンロードします。 インストーラーを実行します。

    1. **[統合ランタイム (セルフホステッド) の登録]** ページで、前に保存したキーを貼り付け、 **[登録]** を選択します。

       ![統合ランタイムの登録](media/create-self-hosted-integration-runtime/register-integration-runtime.png)

    1. **[新しい統合ランタイム (セルフホステッド) ノード]** ページで **[完了]** を選択します。

1. セルフホステッド統合ランタイムが正常に登録されると、次のウィンドウが表示されます。

    ![成功した登録](media/create-self-hosted-integration-runtime/registered-successfully.png)

### <a name="set-up-a-self-hosted-ir-on-an-azure-vm-via-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して Azure VM にセルフホステッド IR をセットアップする

[セルフホステッド IR の作成テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime)を使用して、Azure 仮想マシンでのセルフホステッド IR のセットアップを自動化できます。 このテンプレートを使用すると、Azure 仮想ネットワーク内で完全に機能するセルフホステッド IR を簡単に作成できます。 ノード数を 2 以上に設定している限り、IR には高可用性とスケーラビリティの機能があります。

### <a name="set-up-an-existing-self-hosted-ir-via-local-powershell"></a>ローカル PowerShell を使用して既存のセルフホステッド IR をセットアップする

コマンド ラインを使用して、既存のセルフホステッド IR を設定または管理できます。 この使用方法は、セルフホステッド IR ノードのインストールと登録を自動化するのに特に役立ちます。

Dmgcmd.exe は、セルフホステッド インストーラーに含まれています。 これは、通常は、C:\Program Files\Microsoft Integration Runtime\4.0\Shared\ フォルダーに置かれます。 このアプリケーションはさまざまなパラメーターをサポートしており、自動化のためにバッチ スクリプトを使用してコマンド ラインから呼び出すことができます。

次のようにアプリケーションを使用します。

```powershell
dmgcmd ACTION args...
```

アプリケーションのアクションと引数の詳細を次に示します。 

|ACTION|args|説明|
|------|----|-----------|
|`-rn`,<br/>`-RegisterNewNode`|"`<AuthenticationKey>`" ["`<NodeName>`"]|指定された認証キーおよびノード名を使用して、セルフホステッド統合ランタイム ノードを登録します。|
|`-era`,<br/>`-EnableRemoteAccess`|"`<port>`" ["`<thumbprint>`"]|現在のノードでリモート アクセスを有効にして、高可用性クラスターをセットアップします。 または、Azure Data Factory を介さずにセルフホステッド IR に対する資格情報の直接設定を有効にします。 後者を実行するには、同じネットワーク内のリモート コンピューターから **New-AzDataFactoryV2LinkedServiceEncryptedCredential** コマンドレットを使用します。|
|`-erac`,<br/>`-EnableRemoteAccessInContainer`|"`<port>`" ["`<thumbprint>`"]|ノードがコンテナーで実行されているときに、現在のノードへのリモート アクセスを有効にします。|
|`-dra`,<br/>`-DisableRemoteAccess`||現在のノードへのリモート アクセスを無効にします。 マルチノード設定にはリモート アクセスが必要です。 **New-AzDataFactoryV2LinkedServiceEncryptedCredential** PowerShell コマンドレットは、リモート アクセスが無効な場合でも機能します。 この動作は、コマンドレットがセルフホステッド IR ノードと同じコンピューター上で実行されている場合に当てはまります。|
|`-k`,<br/>`-Key`|"`<AuthenticationKey>`"|以前の認証キーを上書きまたは更新します。 この操作には注意してください。 キーが新しい統合ランタイムのものである場合、これにより以前のセルフホステッド IR ノードがオフラインになる可能性があります。|
|`-gbf`,<br/>`-GenerateBackupFile`|"`<filePath>`" "`<password>`"|現在のノードのバックアップ ファイルを生成します。 バックアップ ファイルには、ノード キーとデータ ストアの資格情報が含まれています。|
|`-ibf`,<br/>`-ImportBackupFile`|"`<filePath>`" "`<password>`"|バックアップ ファイルからノードを復元します。|
|`-r`,<br/>`-Restart`||セルフホステッド統合ランタイムのホスト サービスを再開します。|
|`-s`,<br/>`-Start`||セルフホステッド統合ランタイムのホスト サービスを開始します。|
|`-t`,<br/>`-Stop`||セルフホステッド統合ランタイムのホスト サービスを停止します。|
|`-sus`,<br/>`-StartUpgradeService`||セルフホステッド統合ランタイムのアップグレード サービスを開始します。|
|`-tus`,<br/>`-StopUpgradeService`||セルフホステッド統合ランタイムのアップグレード サービスを停止します。|
|`-tonau`,<br/>`-TurnOnAutoUpdate`||セルフホステッド統合ランタイムの自動更新を有効にします。|
|`-toffau`,<br/>`-TurnOffAutoUpdate`||セルフホステッド統合ランタイムの自動更新を無効にします。|
|`-ssa`,<br/>`-SwitchServiceAccount`|"`<domain\user>`" ["`<password>`"]|DIAHostService を新しいアカウントとして実行するように設定します。 システム アカウントおよび仮想アカウントの場合は空のパスワード "" を使用します|

## <a name="install-and-register-a-self-hosted-ir-from-microsoft-download-center"></a>Microsoft ダウンロード センターからセルフホステッド IR をインストールして登録する

1. [Microsoft Integration Runtime のダウンロード ページ](https://www.microsoft.com/download/details.aspx?id=39717)に移動します。
2. **[ダウンロード]** を選択し、64 ビット バージョンを選んでから **[次へ]** を選択します。 32ビット バージョンはサポートされていません。
3. マネージド ID ファイルを直接実行するか、ハード ドライブに保存してから実行します。
4. **[ようこそ]** ウィンドウで言語を選び、 **[次へ]** を選択します。
5. マイクロソフト ソフトウェア ライセンス条項に同意して、 **[次へ]** を選択します。
6. セルフホステッド統合ランタイムをインストールする **フォルダー** を選んで、 **[次へ]** を選択します。
7. **[インストールの準備完了]** ページで **[インストール]** をクリックします。
8. **[完了]** を選択してインストールを完了します。
9. PowerShell を使用して認証キーを取得します。 認証キーを取得するための PowerShell の例を以下に示します。

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```

10. ご利用のコンピューターで実行している Microsoft Integration Runtime 構成マネージャーの **[統合ランタイム (セルフホスト) の登録]** ウィンドウで、次の手順を行います。

    1. 認証キーをテキスト領域に貼り付けます。

    2. 必要に応じて、 **[認証キーの表示]** を選択してキー テキストを表示します。

    3. **[登録]** を選択します。

## <a name="service-account-for-self-hosted-integration-runtime"></a>セルフホステッド統合ランタイムのサービス アカウント

セルフホステッド統合ランタイムの既定のログオン サービス アカウントは、**NT SERVICE\DIAHostService** です。 これは **[サービス] -> [Integration Runtime サービス] -> [プロパティ] -> [ログオン]** で確認できます。

![セルフホステッド統合ランタイムのサービス アカウント](media/create-self-hosted-integration-runtime/shir-service-account.png)

アカウントにサービスとしてのログオンの権限があることを確認します。 そうでないと、セルフホステッド統合ランタイムを正常に開始できません。 アクセス許可は、 **[ローカル セキュリティ ポリシー] -> [セキュリティの設定] -> [ローカル ポリシー] -> [ユーザー権利の割り当て] -> [サービスとしてログオン]** で確認できます。

![[ローカル セキュリティ ポリシー] のスクリーンショット - [ユーザー権利の割り当て]](media/create-self-hosted-integration-runtime/shir-service-account-permission.png)

![[サービスとしてログオン] のスクリーンショット、[ユーザー権利の割り当て]](media/create-self-hosted-integration-runtime/shir-service-account-permission-2.png)

## <a name="notification-area-icons-and-notifications"></a>通知領域のアイコンと通知

通知領域のアイコンまたはメッセージの上にカーソルを移動すると、セルフホステッド統合ランタイムの状態の詳細が表示されます。

![通知領域内の通知](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="high-availability-and-scalability"></a>高可用性とスケーラビリティ

セルフホステッド統合ランタイムを複数のオンプレミス マシンまたは Azure の仮想マシンに関連付けることができます。 これらのコンピューターは、ノードと呼ばれます。 セルフホステッド統合ランタイムには最大で 4 つのノードを関連付けることができます。 論理ゲートウェイ用にゲートウェイがインストールされているオンプレミス コンピューターに複数のノードを配置すると、次のような利点があります。

- セルフホステッド統合ランタイムの可用性が向上することによって、ビッグ データ ソリューションや Data Factory を使用したクラウド データ統合において、単一障害点となることはなくなります。 この可用性により、最大 4 つのノードを使用する場合に継続性が確保されます。
- オンプレミスとクラウド データ ストアとの間のデータ移動は、パフォーマンスとスループットが向上しました。 詳しくは[パフォーマンス比較](copy-activity-performance.md)を参照してください。

セルフホステッド統合ランタイム ソフトウェアを[ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=39717)からインストールして、複数のノードを関連付けることができます。 その後、[チュートリアル](tutorial-hybrid-copy-powershell.md)の説明に従って、**New-AzDataFactoryV2IntegrationRuntimeKey** コマンドレットから取得した認証キーのいずれかを使用して、登録します。

> [!NOTE]
> 各ノードを関連付けるために新しいセルフホステッド統合ランタイムを作成する必要はありません。 セルフホステッド統合ランタイムを別のコンピューターにインストールし、同じ認証キーを使用してそれを登録できます。

> [!NOTE]
> 高可用性とスケーラビリティのために別のノードを追加する前に、最初のノードで **[Remote access to intranet]\(イントラネットへのリモート アクセス\)** オプションを必ず有効にしてください。 これを行うには、 **[Microsoft Integration Runtime 構成マネージャー]**  >  **[設定]**  >  **[Remote access to intranet]\(イントラネットへのリモート アクセス\)** を選択します。

### <a name="scale-considerations"></a>スケールに関する考慮事項

#### <a name="scale-out"></a>スケール アウト

セルフホステッド IR で、プロセッサ使用率が高く、使用可能なメモリが低減している場合、複数のコンピューターで負荷をスケールアウトするために、新しいノードを追加します。 タイムアウトのため、またはセルフホステッド IR ノードがオフライン状態であるために、アクティビティが失敗した場合に、ノードをゲートウェイに追加すると役立ちます。

#### <a name="scale-up"></a>スケールアップ

プロセッサと使用可能な RAM はあまり使用されていないが、同時実行ジョブの実行がノードの制限に達した場合は、1 つのノードが実行できる同時実行ジョブの数を増やしてスケールアップします。 また、セルフホステッド IR が過負荷になっているために、アクティビティがタイムアウトになる場合も、スケールアップが必要になることがあります。 次の画像に示すように、ノードの最大容量を増やすことができます。  

![ノードで実行できる同時実行ジョブの数を増やす](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL 証明書の要件

統合ランタイム ノード間の通信を保護するために使用される TLS/SSL 証明書の要件を次に示します。

- 証明書は必ず、公的に信頼されている X509 v3 証明書とします。 公的パートナー証明機関 (CA) によって発行された証明書を使用することをお勧めします。
- 統合ランタイムの各ノードで、この証明書を信頼する必要があります。
- 最後の SAN 項目のみが使用されるため、サブジェクトの別名 (SAN) 証明書は推奨されません。 その他のすべての SAN 項目は無視されます。 たとえば、SAN が **node1.domain.contoso.com** と **node2.domain.contoso.com** の SAN 証明書がある場合、完全修飾ドメイン名 (FQDN) が **node2.domain.contoso.com** のコンピューターでのみこの証明書を使用できます。
- 証明書では、TLS/SSL 証明書のために Windows Server 2012 R2 でサポートされている任意のキー サイズを使用できます。
- CNG キーを使用する証明書はサポートされません。  

> [!NOTE]
> この証明書は次の場合に使用されます。
>
> - セルフホステッド IR ノードのポートの暗号化。
> - 状態の同期のためのノード間通信 (ノード間でのリンクされたサービスの資格情報同期を含む)。
> - ローカル ネットワーク内からのリンクされたサービス資格情報の設定に対して PowerShell コマンドレットを使用するとき。
>
> プライベート ネットワーク環境が安全でない場合、またはプライベート ネットワーク内のノード間の通信をセキュリティで保護したい場合は、この証明書を使用することをお勧めします。
>
> セルフホステッド IR から他のデータ ストアへの転送におけるデータ移動は、この証明書が設定されているかどうかに関係なく、常に暗号化チャネル内で行われます。

## <a name="proxy-server-considerations"></a>プロキシ サーバーに関する考慮事項

企業ネットワーク環境でプロキシ サーバーを使用してインターネットにアクセスする場合は、適切なプロキシ設定を使用するようにセルフホステッド統合ランタイムを構成します。 プロキシは、初期登録フェーズ中に設定できます。

![プロキシを指定する](media/create-self-hosted-integration-runtime/specify-proxy.png)

構成されると、セルフホステッド統合ランタイムはプロキシ サーバーを使用してクラウド サービスのソースおよびコピー先 (HTTP または HTTPS プロトコルを使用しているもの) に接続します。 初期セットアップ時に **[変更] リンク** を選択したのはこのためです。

![プロキシを設定する](media/create-self-hosted-integration-runtime/set-http-proxy.png)

3 つの構成オプションがあります。

- **プロキシを使用しない**:セルフホステッド統合ランタイムは、クラウド サービスに接続するときにプロキシを明示的には使用しません。
- **システム プロキシを使用する**:セルフホステッド統合ランタイムでは、diahost.exe.config と diawp.exe.config で構成されているプロキシ設定を使用します。これらのファイルでプロキシ構成が指定されていない場合、セルフホステッド統合ランタイムではプロキシを経由せず、直接クラウド サービスに接続します。
- **カスタム プロキシを使用する**:diahost.exe.config と diawp.exe.config の構成は使用せず、セルフホステッド統合ランタイムで使用するように HTTP プロキシ設定を構成します。 **[アドレス]** と **[ポート]** の値は必須です。 **[ユーザー名]** と **[パスワード]** の値は、プロキシの認証設定によっては省略できます。 すべての設定は Windows DPAPI を使用して、自己ホスト型統合ランタイム上で暗号化され、コンピューターにローカルに格納されます。

統合ランタイムのホスト サービスは、更新済みのプロキシ設定を保存した後に自動的に再起動されます。

セルフホステッド統合ランタイムが登録された後、プロキシ設定を表示または更新する必要がある場合は、Microsoft Integration Runtime 構成マネージャーを使用します。

1. **Microsoft Integration Runtime 構成マネージャー** を開きます。
1. **[設定]** タブを選択します。
1. **[HTTP プロキシ]** の **[変更]** リンクを選択して、 **[HTTP プロキシを設定する]** ダイアログ ボックスを開きます。
1. **[次へ]** を選択します。 その後、プロキシ設定を保存して統合ランタイムのホスト サービスを再起動するためのアクセス許可を求める警告が表示されます。

構成マネージャー ツールを使用して、HTTP プロキシを表示して更新することができます。

![プロキシを表示および更新する](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> NTLM 認証でプロキシ サーバーを設定する場合は、ドメイン アカウントで統合ランタイムのホスト サービスが実行されます。 ドメイン アカウントのパスワードを後で変更する場合は、忘れずにサービスの構成設定を更新し、サービスを再起動してください。 この要件のため、パスワードを頻繁に更新する必要がない専用のドメイン アカウントを使用して、プロキシ サーバーにアクセスすることをお勧めします。

### <a name="configure-proxy-server-settings"></a>プロキシ サーバーの設定を構成する

HTTP プロキシに対して **[システム プロキシを使用する]** オプションを選択すると、セルフホステッド統合ランタイムで、diahost.exe.config と diawp.exe.config のプロキシ設定が使用されます。これらのファイルでプロキシが指定されていない場合、セルフホステッド統合ランタイムではプロキシを経由せず、直接クラウド サービスに接続します。 diahost.exe.config ファイルを更新する手順を以下に示します。

1. エクスプローラーで、元のファイルのバックアップとして C:\Program Files\Microsoft Integration Runtime\4.0\Shared\diahost.exe.config のセーフ コピーを作成します。
1. 管理者として実行中のメモ帳を開きます。
1. メモ帳で、テキスト ファイル C:\Program Files\Microsoft Integration Runtime\4.0\Shared\diahost.exe.config を開きます。
1. 次のコードに示されている既定の **system.net** のタグを見つけます。

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```

    その後、次の例で示すように、プロキシ サーバーの詳細を追加できます。

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    プロキシ タグでは、`scriptLocation` のように必要な設定を指定するための追加のプロパティが許可されます。 構文については、「[\<proxy\> 要素 (ネットワーク設定)](/dotnet/framework/configure-apps/file-schema/network/proxy-element-network-settings)」を参照してください。

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```

1. 元の場所に構成ファイルを保存します。 次に、セルフホステッド統合ランタイムのホスト サービスを再開して、変更を取得します。

   サービスを再開するには、コントロール パネルのサービス アプレットを使用します。 または、Integration Runtime 構成マネージャーから、 **[サービスの停止]** ボタンを選択し、 **[サービスの開始]** を選びます。

   サービスが開始されない場合は、不正確な XML タグ構文が編集済みのアプリケーション構成ファイルに追加されている可能性があります。

> [!IMPORTANT]
> diahost.exe.config と diawp.exe.config の両方を忘れずに更新してください。

また、Microsoft Azure が会社の許可リストにあることを確認する必要もあります。 有効な Azure IP アドレスの一覧は、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=41653)からダウンロードできます。

### <a name="possible-symptoms-for-issues-related-to-the-firewall-and-proxy-server"></a>ファイアウォールとプロキシ サーバーに関する問題で発生する可能性がある症状

次のようなエラー メッセージが表示される場合は、ファイアウォールまたはプロキシ サーバーの構成が正しくない可能性があります。 このような構成では、セルフホステッド統合ランタイムが Data Factory に接続して自身を認証できません。 ファイアウォールとプロキシ サーバーが確実に正しく構成されるようにするには、前のセクションを参照してください。

- セルフホステッド統合ランタイムを登録すると、次のエラー メッセージが表示されます。"この Integration Runtime ノードの登録に失敗しました。 認証キーが有効であり、Integration Runtime ホスト サービスがこのマシンで実行されていることをご確認ください。"
- Integration Runtime 構成マネージャーを開くと、 **[切断]** または **[接続中]** という状態が表示されます。 **[イベント ビューアー]**  >  **[アプリケーションとサービス ログ]**  >  **[Microsoft Integration Runtime]** の順に選択して Windows イベント ログを表示すると、次のようなエラー メッセージが表示されます。

  ```output
  Unable to connect to the remote server
  A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
  ```

### <a name="enable-remote-access-from-an-intranet"></a>イントラネットからのリモート アクセスを有効にする

PowerShell を使用して、セルフホステッド統合ランタイムがインストールされているのとは別のネットワーク接続されたコンピューターから資格情報を暗号化する場合、 **[イントラネットからのリモート アクセス]** オプションを有効にすることができます。 PowerShell を使用して、セルフホステッド統合ランタイムがインストールされているコンピューターで資格情報を暗号化する場合、 **[イントラネットからのリモート アクセス]** オプションを有効にすることはできません。

高可用性とスケーラビリティ用の別のノードを追加する前に、 **[イントラネットからのリモート アクセス]** を有効にしす。  

既定では、セルフホステッド統合ランタイムのセットアップ バージョン 3.3 以降の実行時に、セルフホステッド統合ランタイムのインストーラーにより、セルフホステッド統合ランタイム コンピューター上の **[イントラネットからのリモート アクセス]** が無効化されます。

パートナーまたは他社のファイアウォールを使用する場合は、ポート 8060 またはユーザーが構成したポートを手動で開くことができます。 セルフホステッド統合ランタイムの設定中にファイアウォールの問題が発生した場合は、次のコマンドを使用して、ファイアウォールを構成せずにセルフホステッド統合ランタイムをインストールしてください。

```cmd
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

セルフホステッド統合ランタイム コンピューター上でポート 8060 を開かない場合は、資格情報の設定アプリケーション以外のメカニズムを使用して、データ ストア資格情報を構成します。 たとえば、**New-AzDataFactoryV2LinkedServiceEncryptCredential** PowerShell コマンドレットを使用できます。

## <a name="ports-and-firewalls"></a>ポートとファイアウォール

考慮すべきファイアウォールが 2 つあります。

- 組織の中央ルーターで実行されている "*企業ファイアウォール*"
- セルフホステッド統合ランタイムがインストールされているローカル コンピューターでデーモンとして実行されている "*Windows ファイアウォール*"

![ファイアウォール](media/create-self-hosted-integration-runtime/firewall.png)

企業ファイアウォール レベルでは、次のドメインと送信ポートを構成する必要があります。

[!INCLUDE [domain-and-outbound-port-requirements](./includes/domain-and-outbound-port-requirements-internal.md)]

Windows ファイアウォール レベル (コンピューター レベル) では、通常、これらの送信ポートが有効になっています。 有効になっていない場合は、セルフホステッド統合ランタイム コンピューターで、ドメインとポートを構成することができます。

> [!NOTE]
> 現在 Azure Relay でサービス タグはサポートされていないので、Azure Relay との通信のためには、NSG ルールでサービス タグ **AzureCloud** または **Internet** を使用する必要があります。
> Azure Data Factory との通信のためには、NSG ルールの設定でサービス タグ **DataFactoryManagement** を使用できます。

お使いのソースやシンクに基づいて、追加のドメインと送信ポートを企業ファイアウォールまたは Windows ファイアウォールで許可しなければならない場合があります。

[!INCLUDE [domain-and-outbound-port-requirements](./includes/domain-and-outbound-port-requirements-external.md)]

Azure SQL Database や Azure Data Lake などの一部のクラウド データベースでは、そのファイアウォール構成でセルフホステッド統合ランタイム コンピューターの IP アドレスを許可しなければならない場合があります。

### <a name="get-url-of-azure-relay"></a>Azure Relay の URL の取得

ファイアウォールの許可リストに含める必要がある 1 つの必須ドメインとポートは、Azure Relay との通信用です。 それはセルフホステッド統合ランタイムによって、テスト接続、フォルダー リストやテーブル リストの参照、スキーマの取得、データのプレビューなどのインタラクティブな作成に使用されます。 **.servicebus.windows.net** を許可せずに、より具体的な URL を使用する必要がある場合は、セルフホステッド統合ランタイムに必要なすべての FQDN を ADF ポータルから確認できます。 次の手順のようにします。

1. ADF ポータルにアクセスし、セルフホステッド統合ランタイムを選択します。
2. [編集] ページで **[ノード]** を選択します。
3. **[View Service URLs]\(サービスの URL を表示\)** を選択して、すべての FQDN を取得します。

   ![Azure Relay の URL](media/create-self-hosted-integration-runtime/Azure-relay-url.png)

4. これらの FQDN をファイアウォール規則の許可リストに追加できます。

### <a name="copy-data-from-a-source-to-a-sink"></a>ソースからシンクへのデータのコピー

ファイアウォール ルールを、企業ファイアウォール、セルフホステッド統合ランタイム コンピューター上の Windows ファイアウォール、およびデータ ストア自体に対して確実に正しく有効にします。 このルールを有効にすると、セルフホステッド統合ランタイムは、ソースとシンクの両方に正常に接続されます。 コピー操作に関連するデータ ストアごとにルールを有効にしてください。

たとえば、 オンプレミスのデータ ストアから SQL Database シンクまたは Azure Synapse Analytics シンクにコピーするには、以下の手順を実行します。

1. 送信 TCP 通信を、Windows ファイアウォールと企業ファイアウォールの両方に対して、ポート 1433 上で許可します。
2. SQL Database のファイアウォール設定を、セルフホステッド統合ランタイム コンピューターの IP アドレスを許可された IP アドレスのリストに追加するように構成します。

> [!NOTE]
> ファイアウォールで送信ポート 1433 が許可されていない場合、セルフホステッド統合ランタイムで SQL データベースに直接アクセスすることはできません。 この場合、SQL Database と Azure Synapse Analytics に向けた[ステージング コピー](copy-activity-performance.md)を使用できます。 このシナリオでは、データ移動に HTTPS (ポート 443) のみが必要になります。

## <a name="installation-best-practices"></a>インストールのベスト プラクティス

セルフホステッド統合ランタイムのインストールは、マネージド ID セットアップ パッケージを [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=39717)からダウンロードして実行できます。 詳細な手順については、[オンプレミスとクラウドの間でのデータ移動](tutorial-hybrid-copy-powershell.md)に関する記事を参照してください。

- コンピューターが休止状態にならないように、セルフホステッド統合ランタイム用のホスト コンピューターの電源プランを構成します。 ホスト コンピューターが休止状態になると、セルフホステッド統合ランタイムはオフラインになります。
- 定期的に、セルフホステッド統合ランタイムに関連付けられている資格情報をバックアップします。
- セルフホステッド IR の設定操作を自動化する方法については、[PowerShell を使用した既存のセルフホステッド IR の設定](#setting-up-a-self-hosted-integration-runtime)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

詳細な手順については、[チュートリアル:オンプレミスのデータをクラウドにコピーする](tutorial-hybrid-copy-powershell.md)
