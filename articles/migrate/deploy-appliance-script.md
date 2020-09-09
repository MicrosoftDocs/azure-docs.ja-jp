---
title: スクリプトを使用して Azure Migrate アプライアンスを設定する
description: スクリプトを使用して Azure Migrate アプライアンスを設定する方法について
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: 43e47c6b4121a2f389dfec27873b90b7031c4f2c
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88929977"
---
# <a name="set-up-an-appliance-with-a-script"></a>スクリプトを使用してアプライアンスを設定する

この記事では、VMware VM および Hyper-V VM の評価または移行のために [Azure Migrate アプライアンス](./migrate-appliance-architecture.md)を作成する方法について説明します。 スクリプトを実行してアプライアンスを作成し、それが Azure に接続可能であることを確認します。 

VMware および Hyper-V VM 用のアプライアンスをデプロイするには、スクリプトを使用するか、または Azure portal からダウンロードしたテンプレートを使用します。 ダウンロードしたテンプレートを使用して VM を作成できない場合は、スクリプトを使用する方法が有用です。

- テンプレートを使用するには、[VMware](tutorial-prepare-vmware.md) または [Hyper-V](tutorial-prepare-hyper-v.md) のチュートリアルに従ってください。
- 物理サーバー用のアプライアンスを設定する場合は、スクリプトのみ使用できます。 [こちらの記事](how-to-set-up-appliance-physical.md)に従ってください。
- Azure Government クラウドでアプライアンスを設定するには、[こちらの記事](deploy-appliance-script-government.md)に従ってください。

## <a name="prerequisites"></a>前提条件

このスクリプトは、既存の物理マシンまたは VM 上に Azure Migrate アプライアンスを設定します。

- アプライアンスとして機能するマシンでは、次のハードウェア要件と OS 要件を満たす必要があります。

シナリオ | 必要条件
--- | ---
VMware | メモリが 32 GB、vCPU が 8 個、ディスク ストレージが約 80 GB の Windows Server 2016
Hyper-V | メモリが 16 GB、vCPU が 8 個、ディスク ストレージが約 80 GB の Windows Server 2016
- マシンには外部仮想スイッチも必要です。 ここには、静的または動的 IP アドレスと、インターネットへのアクセスが必要です。
- アプライアンスを展開する前に、[VMware VM](migrate-appliance.md#appliance---vmware) と [Hyper-V VM](migrate-appliance.md#appliance---hyper-v) の詳細なアプライアンス要件をご確認ください。
- 既存の Azure Migrate アプライアンスでスクリプトを実行しないでください。

## <a name="set-up-the-appliance-for-vmware"></a>VMware 用のアプライアンスを設定する

VMware 用のアプライアンスを設定するには、ポータルまたは[こちら](https://go.microsoft.com/fwlink/?linkid=2140334)から AzureMigrateInstaller-Server-Public.zip という名前の zip ファイルをダウンロードし、中身を解凍します。 PowerShell スクリプトを実行して、アプライアンス Web アプリケーションを起動します。 最初に、アプライアンスを設定して構成します。 次に、Azure Migrate プロジェクトにアプライアンスを登録します。


### <a name="verify-file-security"></a>ファイルのセキュリティを確認する

圧縮されたファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたマシンで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、圧縮されたファイルのハッシュを生成します
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 例: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256```
3. Azure パブリック クラウド向けの最新のアプライアンス バージョンとスクリプトを確認します。

    **アルゴリズム** | **ダウンロード** | **SHA256**
    --- | --- | ---
    VMware (85 MB) | [最新バージョン](https://go.microsoft.com/fwlink/?linkid=2140334) | 5d0a3dbce4b5010980d59d49859f809acfeb17f5a36f57af4dac44a0a62dde1f



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

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario VMware ```
   
5. スクリプトが正常に実行されると、アプライアンス Web アプリケーションが起動し、アプライアンスをセットアップできるようになります。 問題が発生した場合は、C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log で、スクリプトログを確認してください。

### <a name="verify-access"></a>アクセスを確認する

[パブリック](migrate-appliance.md#public-cloud-urls) クラウド向けクラウドの Azure URL にアプライアンスから接続できることを確認します。

## <a name="set-up-the-appliance-for-hyper-v"></a>Hyper-V のアプライアンスを設定する

Hyper-V 用のアプライアンスを設定するには、ポータルまたは[こちら](https://go.microsoft.com/fwlink/?linkid=2105112)から AzureMigrateInstaller-Server-Public.zip という名前の zip ファイルをダウンロードし、中身を解凍します。 PowerShell スクリプトを実行して、アプライアンス Web アプリケーションを起動します。 最初に、アプライアンスを設定して構成します。 次に、Azure Migrate プロジェクトにアプライアンスを登録します。


### <a name="verify-file-security"></a>ファイルのセキュリティを確認する

圧縮されたファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたマシンで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、圧縮されたファイルのハッシュを生成します
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 例: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256```

3. Azure パブリック クラウド向けの最新のアプライアンス バージョンとスクリプトを確認します。

    **シナリオ** | **ダウンロード** | **SHA256**
    --- | --- | ---
    Hyper-V (85 MB) | [最新バージョン](https://go.microsoft.com/fwlink/?linkid=2140334) |  5d0a3dbce4b5010980d59d49859f809acfeb17f5a36f57af4dac44a0a62dde1f

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

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. スクリプトが正常に実行されると、アプライアンス Web アプリケーションが起動し、アプライアンスをセットアップできるようになります。 問題が発生した場合は、C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log で、スクリプトログを確認してください。

### <a name="verify-access"></a>アクセスを確認する

[パブリック](migrate-appliance.md#public-cloud-urls) クラウド向けクラウドの Azure URL にアプライアンスから接続できることを確認します。

## <a name="next-steps"></a>次のステップ

アプライアンスをデプロイしたら、それを最初に構成してから、Azure Migrate プロジェクトに登録する必要があります。

- [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance) のアプライアンスを設定します。
- [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance) のアプライアンスを設定します。
