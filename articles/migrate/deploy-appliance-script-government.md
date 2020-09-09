---
title: Azure Government で Azure Migrate アプライアンスを設定する
description: Azure Government で Azure Migrate アプライアンスを設定する方法について説明します
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: ffea966c58909ecaab0da13a4204295ecb193895
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936794"
---
# <a name="set-up-an-appliance-in-azure-government"></a>Azure Government でアプライアンスを設定する 

VMware VM、Hyper-V VM、物理サーバー用の [Azure Migrate アプライアンス](./migrate-appliance-architecture.md)を Azure Government クラウドにデプロイするには、この記事に従ってください。 スクリプトを実行してアプライアンスを作成し、アプライアンスから Azure に接続可能であることを確認します。 パブリック クラウドでアプライアンスを設定する場合は、[こちらの記事](deploy-appliance-script.md)に従ってください。


> [!NOTE]
> テンプレートを使用して (VMware VM および Hyper-V VM 用の) アプライアンスをデプロイする方法は、Azure Government ではサポートされません。


## <a name="prerequisites"></a>前提条件

このスクリプトは、既存の物理マシンまたは VM 上に Azure Migrate アプライアンスを設定します。

- アプライアンスとして機能するマシンは、32 GB のメモリ、8 個の vCPUs、80 GB のディスク　ストレージ、および外部仮想スイッチを搭載し、Windows Server 2016 を実行している必要があります。 ここには、静的または動的 IP アドレスと、インターネットへのアクセスが必要です。
- アプライアンスを展開する前に、[VMware VM](migrate-appliance.md#appliance---vmware)、[Hyper-V VM](migrate-appliance.md#appliance---hyper-v)、[物理サーバー](migrate-appliance.md#appliance---physical)の詳細なアプライアンス要件をレビューしてください。
- 既存の Azure Migrate アプライアンスでスクリプトを実行しないでください。

## <a name="set-up-the-appliance-for-vmware"></a>VMware 用のアプライアンスを設定する

VMware 用のアプライアンスを設定するには、Azure portal から ZIP ファイルをダウンロードし、その内容を抽出します。 PowerShell スクリプトを実行して、アプライアンス Web アプリケーションを起動します。 最初に、アプライアンスを設定して構成します。 次に、Azure Migrate プロジェクトにアプライアンスを登録します。

### <a name="download-the-script"></a>スクリプトのダウンロード

1.  **[移行の目標]**  >  **[サーバー]**  >  **[Azure Migrate: Server Assessment]** で、 **[検出]** をクリックします。
2.  **[マシンの検出]**  >  **[マシンは仮想化されていますか?]** で、 **[はい。VMware vSphere Hypervisor を使用します]** を選択します。
3.  **[ダウンロード]** をクリックして、ZIP ファイルをダウンロードします。 


### <a name="verify-file-security"></a>ファイルのセキュリティを確認する

圧縮されたファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたマシンで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、圧縮されたファイルのハッシュを生成します
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 例: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip SHA256```

3. 最新のアプライアンス バージョンとハッシュ値を確認します。

    **アルゴリズム** | **ダウンロード** | **SHA256**
    --- | --- | ---
    VMware (85 MB) | [最新バージョン](https://go.microsoft.com/fwlink/?linkid=2140337) | 31b1bfdd4fc29b3eb923c7c6e7a898af79b7cac0404426bea18809def2284188


### <a name="run-the-script"></a>スクリプトを実行する

スクリプトの機能：

- エージェントと Web アプリケーションをインストールします。
- Windows のロール (Windows Activation Service、IIS、PowerShell ISE など) をインストールします。
- IIS 再書き込み可能モジュールをダウンロードしてインストールします。 [詳細については、こちらを参照してください](https://www.microsoft.com/download/details.aspx?id=7435)。
- Azure Migrate の永続的な設定を使用して、レジストリ キー (HKLM) を更新します。
- ログファイルと構成ファイルを次のように作成します。
    - **構成ファイル**: %ProgramData%\Microsoft Azure\Config
    - **ログ ファイル**: %ProgramData%\Microsoft Azure\Logs

スクリプトを実行するには、次の手順を実行します。

1. アプライアンスをホストするマシン上のフォルダーに ZIP ファイルを展開します。 既存の Azure Migrate アプライアンス マシンでスクリプトを実行しないようご注意ください。
2. （昇格した）管理者権限を使用し、マシンで PowerShell を起動します。
3. PowerShell ディレクトリを、ダウンロードした ZIP ファイルから抽出したコンテンツが格納されたフォルダーに変更します。
4. 次のように、スクリプト **AzureMigrateInstaller.ps1** を実行します。 
    
    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>.\AzureMigrateInstaller.ps1 ```
1. スクリプトが正常に実行されると、アプライアンス Web アプリケーションが起動し、アプライアンスをセットアップできるようになります。 問題が発生した場合は、C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log で、スクリプトログを確認してください。

### <a name="verify-access"></a>アクセスを確認する

[政府機関向け](migrate-appliance.md#government-cloud-urls)クラウドの Azure URL にアプライアンスから接続できることを確認します。


## <a name="set-up-the-appliance-for-hyper-v"></a>Hyper-V のアプライアンスを設定する

Hyper-V のアプライアンスを設定するには、Azure portal から ZIP ファイルをダウンロードし、その内容を抽出します。 PowerShell スクリプトを実行して、アプライアンス Web アプリケーションを起動します。 最初に、アプライアンスを設定して構成します。 次に、Azure Migrate プロジェクトにアプライアンスを登録します。

### <a name="download-the-script"></a>スクリプトのダウンロード

1.  **[移行の目標]**  >  **[サーバー]**  >  **[Azure Migrate: Server Assessment]** で、 **[検出]** をクリックします。
2.  **[マシンの検出]**  >  **[マシンは仮想化されていますか?]** で、 **[はい。Hyper-V を使用します]** を選択します。
3.  **[ダウンロード]** をクリックして、ZIP ファイルをダウンロードします。 


### <a name="verify-file-security"></a>ファイルのセキュリティを確認する

圧縮されたファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたマシンで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、圧縮されたファイルのハッシュを生成します
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 例: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip SHA256```

3. 最新のアプライアンス バージョンとハッシュ値を確認します。

    **シナリオ** | **ダウンロード** | **SHA256**
    --- | --- | ---
    Hyper-V (85 MB) | [最新バージョン](https://go.microsoft.com/fwlink/?linkid=2140424) |  db5311de3d1d4a1167183a94e8347456db9c5749c7332ff2eb4b777798765e48

          

### <a name="run-the-script"></a>スクリプトを実行する

スクリプトの機能：

- エージェントと Web アプリケーションをインストールします。
- Windows のロール (Windows Activation Service、IIS、PowerShell ISE など) をインストールします。
- IIS 再書き込み可能モジュールをダウンロードしてインストールします。 [詳細については、こちらを参照してください](https://www.microsoft.com/download/details.aspx?id=7435)。
- Azure Migrate の永続的な設定を使用して、レジストリ キー (HKLM) を更新します。
- ログファイルと構成ファイルを次のように作成します。
    - **構成ファイル**: %ProgramData%\Microsoft Azure\Config
    - **ログ ファイル**: %ProgramData%\Microsoft Azure\Logs

スクリプトを実行するには、次の手順を実行します。

1. アプライアンスをホストするマシン上のフォルダーに ZIP ファイルを展開します。 既存の Azure Migrate アプライアンス マシンでスクリプトを実行しないようご注意ください。
2. （昇格した）管理者権限を使用し、マシンで PowerShell を起動します。
3. PowerShell ディレクトリを、ダウンロードした ZIP ファイルから抽出したコンテンツが格納されたフォルダーに変更します。
4. 次のように、スクリプト **AzureMigrateInstaller.ps1** を実行します。 

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>.\AzureMigrateInstaller.ps1 ``` 
1. スクリプトが正常に実行されると、アプライアンス Web アプリケーションが起動し、アプライアンスをセットアップできるようになります。 問題が発生した場合は、C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log で、スクリプトログを確認してください。

### <a name="verify-access"></a>アクセスを確認する

[政府機関向け](migrate-appliance.md#government-cloud-urls)クラウドの Azure URL にアプライアンスから接続できることを確認します。


## <a name="set-up-the-appliance-for-physical-servers"></a>物理サーバーのアプライアンスを設定する

VMware 用のアプライアンスを設定するには、Azure portal から ZIP ファイルをダウンロードし、その内容を抽出します。 PowerShell スクリプトを実行して、アプライアンス Web アプリケーションを起動します。 最初に、アプライアンスを設定して構成します。 次に、Azure Migrate プロジェクトにアプライアンスを登録します。

### <a name="download-the-script"></a>スクリプトのダウンロード

1.  **[移行の目標]**  >  **[サーバー]**  >  **[Azure Migrate: Server Assessment]** で、 **[検出]** をクリックします。
2.  **[マシンの検出]**  >  **[マシンは仮想化されていますか?]** で、 **[非仮想化/その他]** を選択します。
3.  **[ダウンロード]** をクリックして、ZIP ファイルをダウンロードします。 


### <a name="verify-file-security"></a>ファイルのセキュリティを確認する

圧縮されたファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたマシンで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、圧縮されたファイルのハッシュを生成します
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 例: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256```

3. 最新のアプライアンス バージョンとハッシュ値を確認します。

    **シナリオ** | **ダウンロード*** | **ハッシュ値**
    --- | --- | ---
    物理 (85 MB) | [最新バージョン](https://go.microsoft.com/fwlink/?linkid=2140338) | 1545f9ce8874cedef6347c1a1332f8b5eabd6811a017440a2382525fb0430309
          

### <a name="run-the-script"></a>スクリプトを実行する

スクリプトの機能：

- エージェントと Web アプリケーションをインストールします。
- Windows のロール (Windows Activation Service、IIS、PowerShell ISE など) をインストールします。
- IIS 再書き込み可能モジュールをダウンロードしてインストールします。 [詳細については、こちらを参照してください](https://www.microsoft.com/download/details.aspx?id=7435)。
- Azure Migrate の永続的な設定を使用して、レジストリ キー (HKLM) を更新します。
- ログファイルと構成ファイルを次のように作成します。
    - **構成ファイル**: %ProgramData%\Microsoft Azure\Config
    - **ログ ファイル**: %ProgramData%\Microsoft Azure\Logs

スクリプトを実行するには、次の手順を実行します。

1. アプライアンスをホストするマシン上のフォルダーに ZIP ファイルを展開します。 既存の Azure Migrate アプライアンス マシンでスクリプトを実行しないようご注意ください。
2. （昇格した）管理者権限を使用し、マシンで PowerShell を起動します。
3. PowerShell ディレクトリを、ダウンロードした ZIP ファイルから抽出したコンテンツが格納されたフォルダーに変更します。
4. 次のように、スクリプト **AzureMigrateInstaller.ps1** を実行します。 

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```
1. スクリプトが正常に実行されると、アプライアンス Web アプリケーションが起動し、アプライアンスをセットアップできるようになります。 問題が発生した場合は、C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log で、スクリプトログを確認してください。

### <a name="verify-access"></a>アクセスを確認する

[政府機関向け](migrate-appliance.md#government-cloud-urls)クラウドの Azure URL にアプライアンスから接続できることを確認します。

## <a name="next-steps"></a>次のステップ

アプライアンスをデプロイしたら、それを最初に構成してから、Azure Migrate プロジェクトに登録する必要があります。

- [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance) のアプライアンスを設定します。
- [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance) のアプライアンスを設定します。
- [物理サーバー](how-to-set-up-appliance-physical.md)のアプライアンスを設定します。
