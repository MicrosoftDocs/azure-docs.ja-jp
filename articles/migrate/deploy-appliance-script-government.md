---
title: Azure Government で Azure Migrate アプライアンスを設定する
description: Azure Government で Azure Migrate アプライアンスを設定する方法について説明します
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: c4ca8d8ac24ac174158957e44b5eabe4a89a5340
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775206"
---
# <a name="set-up-an-appliance-in-azure-government"></a>Azure Government でアプライアンスを設定する 

VMware 環境のサーバー、Hyper-V 上のサーバー、物理サーバー用の [Azure Migrate アプライアンス](./migrate-appliance-architecture.md)を Azure Government クラウドにデプロイするには、この記事に従ってください。 スクリプトを実行してアプライアンスを作成し、アプライアンスから Azure に接続可能であることを確認します。 パブリック クラウドでアプライアンスを設定する場合は、[こちらの記事](deploy-appliance-script.md)に従ってください。


> [!NOTE]
> テンプレートを使用して (VMware 環境および Hyper-V 環境のサーバー用の) アプライアンスをデプロイする方法は、Azure Government ではサポートされません。


## <a name="prerequisites"></a>前提条件

このスクリプトは、既存の物理サーバーまたは仮想サーバー上に Azure Migrate アプライアンスを設定します。

- アプライアンスとして機能するサーバーは、32 GB のメモリ、8 個の vCPUs、80 GB のディスク ストレージ、および外部仮想スイッチを搭載し、Windows Server 2016 を実行している必要があります。 ここには、静的または動的 IP アドレスと、インターネットへのアクセスが必要です。
- アプライアンスをデプロイする前に、[VMware 上のサーバー](migrate-appliance.md#appliance---vmware)、[Hyper-V 上のサーバー](migrate-appliance.md#appliance---hyper-v)、および[物理サーバー](migrate-appliance.md#appliance---physical)の詳細なアプライアンス要件を確認してください。
- 既存の Azure Migrate アプライアンスでスクリプトを実行しないでください。

## <a name="set-up-the-appliance-for-vmware"></a>VMware 用のアプライアンスを設定する

VMware 用のアプライアンスを設定するには、Azure portal から ZIP ファイルをダウンロードし、その内容を抽出します。 PowerShell スクリプトを実行して、アプライアンス Web アプリケーションを起動します。 最初に、アプライアンスを設定して構成します。 次に、プロジェクトにアプライアンスを登録します。

### <a name="download-the-script"></a>スクリプトのダウンロード

1. **[移行の目標]**  >  **[Windows、Linux、SQL Server]**  >  **[Azure Migrate: Discovery and Assessment]** で、 **[検出]** をクリックします。
2. **[サーバーの検出]**  >  **[お使いのサーバーは仮想化されていますか?]** で、 **[はい。VMware vSphere Hypervisor を使用します]** を選択します。
3. **[ダウンロード]** をクリックして、ZIP ファイルをダウンロードします。

### <a name="verify-file-security"></a>ファイルのセキュリティを確認する

圧縮されたファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたサーバーで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、圧縮されたファイルのハッシュを生成します
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 例: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip SHA256```

3. 最新のアプライアンス バージョンとハッシュ値を確認します。

    **アルゴリズム** | **ダウンロード** | **SHA256**
    --- | --- | ---
    VMware (85.8 MB) | [最新バージョン](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca


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

1. アプライアンスをホストするサーバー上のフォルダーに ZIP ファイルを抽出します。 既存の Azure Migrate アプライアンスが存在するサーバー上でスクリプトを実行しないよう注意してください。
2. (昇格された) 管理者特権を使用して、サーバーで PowerShell を起動します。
3. PowerShell ディレクトリを、ダウンロードした ZIP ファイルから抽出したコンテンツが格納されたフォルダーに変更します。
4. 次のように、スクリプト **AzureMigrateInstaller.ps1** を実行します。
    
    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>.\AzureMigrateInstaller.ps1 ```
1. スクリプトが正常に実行されると、アプライアンス Web アプリケーションが起動し、アプライアンスをセットアップできるようになります。 問題が発生した場合は、C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log で、スクリプトログを確認してください。

### <a name="verify-access"></a>アクセスを確認する

[政府機関向け](migrate-appliance.md#government-cloud-urls)クラウドの Azure URL にアプライアンスから接続できることを確認します。


## <a name="set-up-the-appliance-for-hyper-v"></a>Hyper-V のアプライアンスを設定する

Hyper-V のアプライアンスを設定するには、Azure portal から ZIP ファイルをダウンロードし、その内容を抽出します。 PowerShell スクリプトを実行して、アプライアンス Web アプリケーションを起動します。 最初に、アプライアンスを設定して構成します。 次に、プロジェクトにアプライアンスを登録します。

### <a name="download-the-script"></a>スクリプトのダウンロード

1.  **[移行の目標]**  >  **[Windows、Linux、SQL Server]**  >  **[Azure Migrate: Discovery and Assessment]** で、 **[検出]** をクリックします。
2.  **[サーバーの検出]**  >  **[お使いのサーバーは仮想化されていますか?]** で、 **[はい。Hyper-V を使用します]** を選択します。
3.  **[ダウンロード]** をクリックして、ZIP ファイルをダウンロードします。 


### <a name="verify-file-security"></a>ファイルのセキュリティを確認する

圧縮されたファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたサーバーで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、圧縮されたファイルのハッシュを生成します
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 例: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip SHA256```

3. 最新のアプライアンス バージョンとハッシュ値を確認します。

    **シナリオ** | **ダウンロード** | **SHA256**
    --- | --- | ---
    Hyper-V (85.8 MB) | [最新バージョン](https://go.microsoft.com/fwlink/?linkid=2140424) |  db5311de3d1d4a1167183a94e8347456db9c5749c7332ff2eb4b777798765e48

          

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

1. アプライアンスをホストするサーバー上のフォルダーに ZIP ファイルを抽出します。 既存の Azure Migrate アプライアンスを実行するサーバー上でスクリプトを実行しないよう注意してください。
2. (昇格された) 管理者特権を使用して、サーバーで PowerShell を起動します。
3. PowerShell ディレクトリを、ダウンロードした ZIP ファイルから抽出したコンテンツが格納されたフォルダーに変更します。
4. 次のように、スクリプト **AzureMigrateInstaller.ps1** を実行します。 

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>.\AzureMigrateInstaller.ps1 ``` 
1. スクリプトが正常に実行されると、アプライアンス Web アプリケーションが起動し、アプライアンスをセットアップできるようになります。 問題が発生した場合は、C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log で、スクリプトログを確認してください。

### <a name="verify-access"></a>アクセスを確認する

[政府機関向け](migrate-appliance.md#government-cloud-urls)クラウドの Azure URL にアプライアンスから接続できることを確認します。


## <a name="set-up-the-appliance-for-physical-servers"></a>物理サーバーのアプライアンスを設定する

VMware 用のアプライアンスを設定するには、Azure portal から ZIP ファイルをダウンロードし、その内容を抽出します。 PowerShell スクリプトを実行して、アプライアンス Web アプリケーションを起動します。 最初に、アプライアンスを設定して構成します。 次に、プロジェクトにアプライアンスを登録します。

### <a name="download-the-script"></a>スクリプトのダウンロード

1. **[移行の目標]**  >  **[Windows、Linux、SQL Server]**  >  **[Azure Migrate: Discovery and Assessment]** で、 **[検出]** をクリックします。
2. **[サーバーの検出]**  >  **[お使いのサーバーは仮想化されていますか?]** で、 **[非仮想化/その他]** を選択します。
3. **[ダウンロード]** をクリックして、ZIP ファイルをダウンロードします。

### <a name="verify-file-security"></a>ファイルのセキュリティを確認する

圧縮されたファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたサーバーで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、圧縮されたファイルのハッシュを生成します
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 例: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256```

3. 最新のアプライアンス バージョンとハッシュ値を確認します。

    **シナリオ** | **ダウンロード** _ | _ *ハッシュ値**
    --- | --- | ---
    物理 (85 MB) | [最新バージョン](https://go.microsoft.com/fwlink/?linkid=2140338) | cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f
          

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

1. アプライアンスをホストするサーバー上のフォルダーに ZIP ファイルを抽出します。 既存の Azure Migrate アプライアンスを実行するサーバー上でスクリプトを実行しないよう注意してください。
2. (昇格された) 管理者特権を使用して、サーバーで PowerShell を起動します。
3. PowerShell ディレクトリを、ダウンロードした ZIP ファイルから抽出したコンテンツが格納されたフォルダーに変更します。
4. 次のように、スクリプト **AzureMigrateInstaller.ps1** を実行します。

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```
1. スクリプトが正常に実行されると、アプライアンス Web アプリケーションが起動し、アプライアンスをセットアップできるようになります。 問題が発生した場合は、C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log で、スクリプトログを確認してください。

### <a name="verify-access"></a>アクセスを確認する

[政府機関向け](migrate-appliance.md#government-cloud-urls)クラウドの Azure URL にアプライアンスから接続できることを確認します。

## <a name="next-steps"></a>次のステップ

アプライアンスをデプロイしたら、それを最初に構成し、プロジェクトに登録する必要があります。

- [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance) のアプライアンスを設定します。
- [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance) のアプライアンスを設定します。
- [物理サーバー](how-to-set-up-appliance-physical.md)のアプライアンスを設定します。
