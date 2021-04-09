---
title: Azure CLI と Python を使用して Azure Stack Edge Pro GPU デバイスに VM をデプロイする
description: Azure CLI と Python を使用して Azure Stack Edge Pro GPU デバイスに仮想マシン (VM) を作成し、管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/04/2021
ms.author: alkohli
ms.openlocfilehash: 43853f2eba05794540a72fe64e748fcafd9073d2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102637060"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-using-azure-cli-and-python"></a>Azure CLI と Python を使用して Azure Stack Edge Pro GPU デバイスに VM をデプロイする

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-overview](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-overview.md)]

このチュートリアルでは、Azure コマンド ライン インターフェイス (CLI) と Python を使用して、Azure Stack Edge Pro デバイスに VM を作成し、管理する方法について説明します。

## <a name="vm-deployment-workflow"></a>VM デプロイのワークフロー

次の図にデプロイのワークフローを示します。

![VM デプロイのワークフロー](media/azure-stack-edge-gpu-deploy-virtual-machine-powershell/vm-workflow-r.svg)

デプロイの大まかなワークフローは次のとおりです。

1. Azure Resource Manager に接続する
2. リソース グループを作成する
3. ストレージ アカウントの作成
4. hosts ファイルに BLOB の URI を追加する
5. 証明書をインストールする
6. VHD のアップロード
7. VHD からマネージド ディスクを作成する
8. イメージのマネージド ディスクから VM イメージを作成する
9. 前もって作成しておいたリソースを使用して VM を作成する
10. VNet を作成する
11. VNet のサブネット ID を使用して VNIC を作成する

ワークフロー図の詳しい説明については、[Azure PowerShell を使用して Azure Stack Edge Pro デバイスに VM をデプロイする方法](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)に関するページを参照してください。 Azure Resource Manager に接続する方法については、[Azure PowerShell を使用して Azure Resource Manager に接続する方法](azure-stack-edge-gpu-connect-resource-manager.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

Azure CLI と Python を使用して、Azure Stack Edge Pro デバイスに VM を作成して管理する前に、次の手順に記載された前提条件をすべて満たしていることを確認する必要があります。

1. 次の説明に従って Azure Stack Edge Pro デバイスでネットワーク設定を完了していること。「[手順 1: Azure Stack Edge Pro デバイスを構成する](azure-stack-edge-gpu-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device)」

2. コンピューティング用のネットワーク インターフェイスを有効にします。 このネットワーク インターフェイスの IP を使用して、VM デプロイ用の仮想スイッチを作成します。 このプロセスは、次の手順で実行します。

    1. **[コンピューティング]** に移動します。 仮想スイッチの作成に使用するネットワーク インターフェイスを選択します。

        > [!IMPORTANT] 
        > コンピューティング用に構成できるポートは 1 つだけです。

    2. そのネットワーク インターフェイスでコンピューティングを有効にします。 そのネットワーク インターフェイスに対応する仮想スイッチが Azure Stack Edge Pro によって作成、管理されます。

    <!--If you decide to use another network interface for compute, make sure that you:

    - Delete all the VMs that you have deployed using Azure Resource Manager.

    - Delete all virtual network interfaces and the virtual network associated with this network interface.

    - You can now enable another network interface for compute.-->

3. すべての証明書を作成し、Azure Stack Edge Pro デバイスと、お使いのクライアントの信頼されたストアにインストールします。 「[手順 2: 証明書を作成してインストールする](azure-stack-edge-gpu-connect-resource-manager.md#step-2-create-and-install-certificates)」で説明されている手順に従います。

4. 自分の Azure Stack Edge Pro デバイス用に、Base-64 でエンコードされた *.cer* 証明書 (PEM 形式) を作成します。 この証明書が署名チェーンとしてあらかじめデバイスにアップロードされ、クライアントの信頼されたルート ストアにインストールされている必要があります。 また、このクライアントで Python が正しく動作するためには、この証明書が *pem* 形式になっている必要があります。

    `certutil` コマンドを使用して、この証明書を `pem` 形式に変換してください。 このコマンドは、証明書が格納されているディレクトリで実行する必要があります。

    ```powershell
    certutil.exe <SourceCertificateName.cer> <DestinationCertificateName.pem>
    ```
    コマンドの使用例を次に示します。

    ```powershell
    PS C:\Certificates> certutil.exe -encode aze-root.cer aze-root.pem
    Input Length = 2150
    Output Length = 3014
    CertUtil: -encode command completed successfully.
    PS C:\Certificates>
    ```    
    さらに、後でこの `pem` を Python ストアに追加します。

5. デバイスのローカル Web UI の **[ネットワーク]** ページで、デバイスに IP を割り当てます。 この IP を次の場所に追加します。

    - クライアント上のホスト ファイル、または
    - DNS サーバーの構成
    
    > [!IMPORTANT]
    > エンドポイントの名前解決には、DNS サーバーの構成を変更することをお勧めします。

    1. **メモ帳** を管理者として起動し (hosts ファイルを保存するには管理者特権が必要です)、`C:\Windows\System32\Drivers\etc` にある **hosts** ファイルを開きます。
    
        ![エクスプローラーの hosts ファイル](media/azure-stack-edge-gpu-connect-resource-manager/hosts-file.png)
    
    2. **hosts** ファイルに次のエントリを追加します。値は、実際のデバイスに合わせて置き換えてください。
    
        ```
        <Device IP> login.<appliance name>.<DNS domain>
        <Device IP> management.<appliance name>.<DNS domain>
        <Device IP> <storage name>.blob.<appliance name>.<DNS domain>
        ```
    3. 次の画像を参考にしてください。 **hosts** ファイルを保存します。

        ![メモ帳で開いた hosts ファイル](media/azure-stack-edge-gpu-deploy-virtual-machine-cli-python/hosts-screenshot-boxed.png)

6. この手順で使用した [Python スクリプトをダウンロード](https://aka.ms/ase-vm-python)します。

## <a name="step-1-set-up-azure-clipython-on-the-client"></a>手順 1:クライアントに Azure CLI と Python を設定する

### <a name="verify-profile-and-install-azure-cli"></a>プロファイルを確認して Azure CLI をインストールする

<!--1. Verify the API profile of the client and identify which version of the modules and libraries to include on your client. In this example, the client system will be running Azure Stack 1904 or later. For more information, see [Azure Resource Manager API profiles](/azure-stack/user/azure-stack-version-profiles?view=azs-1908&preserve-view=true#azure-resource-manager-api-profiles).-->

1. お使いのクライアントに Azure CLI をインストールします。 この例では、Azure CLI 2.0.80 がインストールされています。 Azure CLI のバージョンを確認するには、`az --version` コマンドを実行します。

    次に示すのは、前述したコマンドのサンプル出力です。

    ```output
    PS C:\windows\system32> az --version
    azure-cli                         2.0.80
    
    command-modules-nspkg              2.0.3
    core                              2.0.80
    nspkg                              3.0.4
    telemetry                          1.0.4
    Extensions:
    azure-cli-iot-ext                  0.7.1
    
    Python location 'C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\python.exe'
    Extensions directory 'C:\.azure\cliextensions'
    
    Python (Windows) 3.6.6 (v3.6.6:4cf1f54eb7, Jun 27 2018, 02:47:15) [MSC v.1900 32 bit (Intel)]
    
    Legal docs and information: aka.ms/AzureCliLegal
    
    Your CLI is up-to-date.
    
    Please let us know how we are doing: https://aka.ms/clihats
    PS C:\windows\system32>
    ```

    Azure CLI をお持ちでない場合は、[Windows に Azure CLI をダウンロードしてインストール](/cli/azure/install-azure-cli-windows)してください。 Azure CLI は、Windows コマンド プロンプトまたは Windows PowerShell を使用して実行できます。

2. CLI の Python の場所を書き留めておきます。 Azure CLI の信頼されたルート証明書ストアの場所を特定するためには Python の場所が必要です。

3. この記事で使用されているサンプル スクリプトを実行するには、次のバージョンの Python ライブラリが必要となります。

    ```powershell
    azure-common==1.1.23
    azure-mgmt-resource==2.1.0
    azure-mgmt-network==2.7.0
    azure-mgmt-compute==5.0.0
    azure-mgmt-storage==1.5.0
    azure-storage-blob==1.2.0rc1
    haikunator
    msrestazure==0.6.2
    ```
    これらのバージョンをインストールするには、次のコマンドを実行します。

    ```powershell
    .\python.exe -m pip install haikunator
    ```

    次のサンプル出力は、haikunator のインストールを示しています。

    ```output
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe -m pip install haikunator

    Collecting haikunator
      Downloading https://files.pythonhosted.org/packages/43/fa/130968f1a1bb1461c287b9ff35c630460801783243acda2cbf3a4c5964a5/haikunator-2.1.0-py2.py3-none-any.whl
    
    Installing collected packages: haikunator
    Successfully installed haikunator-2.1.0
    You are using pip version 10.0.1, however version 20.0.1 is available.
    You should consider upgrading using the 'python -m pip install --upgrade pip' command.
    
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> 
    ```

    次のサンプル出力は、`msrestazure` の pip インストールを示しています。 
    
    ```output
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe -m pip install msrestazure==0.6.2
    Requirement already satisfied: msrestazure==0.6.2 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (0.6.2)
    Requirement already satisfied: msrest<2.0.0,>=0.6.0 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from msrestazure==0.6.2) (0.6.10)
    === CUT ===========================  CUT ==================================
    Requirement already satisfied: cffi!=1.11.3,>=1.8 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from cryptography>=1.1.0->adal<2.0.0,>=0.6.0->msrestazure==0.6.2) (1.13.2)
    Requirement already satisfied: pycparser in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from cffi!=1.11.3,>=1.8->cryptography>=1.1.0->adal<2.0.0,>=0.6.0->msrestazure==0.6.2) (2.18)
    You are using pip version 10.0.1, however version 20.0.1 is available.
    You should consider upgrading using the 'python -m pip install --upgrade pip' command.
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

### <a name="trust-the-azure-stack-edge-pro-ca-root-certificate"></a>Azure Stack Edge Pro の CA ルート証明書を信頼する

1. マシンで証明書の場所を探します。 この場所は、`az cli` をインストールした場所に応じて異なる場合があります。 Windows PowerShell を管理者として実行します。 `az cli` による Python のインストール先パスに切り替えます (`C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\python.exe`)。

    証明書の場所の種類を取得するには、次のコマンドを入力します。

    ```powershell
    .\python -c "import certifi; print(certifi.where())"
    ```
    
    このコマンドレットから、次のように証明書の場所が返されます。  
        
    ```output
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python -c "import certifi; print(certifi.where())"
    C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```
      
    この場所は、後で使用するので、書き留めておいてください (`C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem`)。

2. Azure Stack Edge Pro の CA ルート証明書を Python の既存の証明書に追加して信頼します。 先ほど PEM 証明書を保存した場所のパスを指定してください。

    ```powershell
    $pemFile = "<Path to the pem format certificate>"
    ```
    たとえば、"C:\VM-scripts\rootteam3device.pem" のようなパスになります。
    
    Windows PowerShell に、次の一連のコマンドを入力します。

    ```powershell
    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
        $root.Import($pemFile)
        
    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry= [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")
    
    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText
    
    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry
    
    Write-Host "Python Cert store was updated to allow the Azure Stack Edge Pro CA root certificate"
    ```
    
### <a name="connect-to-azure-stack-edge-pro"></a>Azure Stack Edge Pro に接続する

1. `az cloud register` コマンドを実行して、自分の Azure Stack Edge Pro 環境を登録します。

    一部のシナリオでは、インターネットへの直接送信接続がプロキシまたはファイアウォール経由でルーティングされ、SSL インターセプトが適用されます。 このような場合は、az cloud register コマンドが、"\"クラウドからエンドポイントを取得できない\"" といったエラーで失敗するおそれがあります。このエラーを回避するには、Windows PowerShell で次の環境変数を設定します。

    ```powershell
    $ENV:AZURE_CLI_DISABLE_CONNECTION_VERIFICATION = 1 
    $ENV:ADAL_PYTHON_SSL_NO_VERIFY = 1
    ```

2. Azure Resource Manager のエンドポイント、リソースの作成先、ソース VHD の保存先のパスについて、スクリプトの環境変数を設定します。 リソースの場所は、すべての Azure Stack Edge Pro デバイスで固定であり、`dbelocal` に設定されます。 さらに、アドレス プレフィックスとプライベート IP アドレスも指定する必要があります。 以下の環境変数は、実際の値に基づいて設定してください。ただし `AZURE_RESOURCE_LOCATION` は、`"dbelocal"` としてハードコーディングする必要があります。

    ```powershell
    $ENV:ARM_ENDPOINT = "https://management.team3device.teatraining1.com"
    $ENV:AZURE_RESOURCE_LOCATION = "dbelocal"
    $ENV:VHD_FILE_PATH = "C:\Downloads\Ubuntu1604\Ubuntu13.vhd"
    $ENV:ADDRESS_PREFIXES = "5.5.0.0/16"
    $ENV:PRIVATE_IP_ADDRESS = "5.5.174.126"
    ```

3. お客様の環境を登録します。 az cloud register を実行するときに、次のパラメーターを使用します。

    | 値 | 説明 | 例 |
    | --- | --- | --- |
    | 環境名 | 接続しようとしている環境の名前 | 名前を指定します (例: `aze-environ`)。 |
    | Resource Manager エンドポイント | この URL は `https://Management.<appliancename><dnsdomain>` です。 <br> この URL を取得するには、自分のデバイスのローカル Web UI で **[デバイス]** ページに移動します。 |たとえば、「 `https://management.team3device.teatraining1.com` 」のように入力します。  |
    
    ```powershell
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.<appliance name>.<DNS domain>"
    ```
    次に示すのは、前述したコマンドの使用例です。
    
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud register -n az-new-env --endpoint-resource-manager "https://management.team3device.teatraining1.com"
    ```
    
    
4. 次のコマンドを使用して、アクティブな環境を設定します。

    ```powershell
    az cloud set -n <EnvironmentName>
    ```
    次に示すのは、前述したコマンドの使用例です。

    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud set -n az-new-env
    Switched active cloud to 'az-new-env'.
    Use 'az login' to log in to this cloud.
    Use 'az account set' to set the active subscription.
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

4. `az login` コマンドを使用して、Azure Stack Edge Pro 環境にサインインします。 Azure Stack Edge Pro 環境には、ユーザーまたは[サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md)としてサインインできます。

   これらの手順に従い、"*ユーザー*" としてサインインします。

   `az login` コマンド内で直接ユーザー名とパスワードを指定するか、ブラウザーを使用して認証できます。 多要素認証が有効になっているアカウントの場合は、後者を実行する必要があります。

   `az login` の使用例を次に示します。
    
    ```powershell
    PS C:\Certificates> az login -u EdgeARMuser
    ```
   login コマンドの使用後、パスワードを入力するように求められます。 Azure Resource Manager のパスワードを入力してください。

   パスワードの入力後にサインインに成功した場合のサンプル出力を次に示します。  
   
   ```output
   PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az login -u EdgeARMuser
   Password:
   [
        {
            "cloudName": "az-new-env",
            "id": "A4257FDE-B946-4E01-ADE7-674760B8D1A3",
            "isDefault": true,
            "name": "Default Provider Subscription",
            "state": "Enabled",
            "tenantId": "c0257de7-538f-415c-993a-1b87a031879d",
            "user": {
                "name": "EdgeArmUser@localhost",
                "type": "user"
            }
        }
   ]
   PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
   ```
   `id` と `tenantId` の値をメモしておきます。これらの値はそれぞれ Azure Resource Manager サブスクリプション ID と Azure Resource Manager テナント ID に対応し、後の手順で使用されるためです。
       
   "*サービス プリンシパル*" として機能するためには、次の環境変数が設定されている必要があります。

   ```
   $ENV:ARM_TENANT_ID = "c0257de7-538f-415c-993a-1b87a031879d"
   $ENV:ARM_CLIENT_ID = "cbd868c5-7207-431f-8d16-1cb144b50971"
   $ENV:ARM_CLIENT_SECRET - "<Your Azure Resource Manager password>"
   $ENV:ARM_SUBSCRIPTION_ID = "A4257FDE-B946-4E01-ADE7-674760B8D1A3"
   ```

   Azure Resource Manager クライアント ID はハードコーディングされています。 ご利用の Azure Resource Manager テナント ID および Azure Resource Manager サブスクリプション ID はどちらも、以前に実行した `az login` コマンドの出力にあります。 Azure Resource Manager のクライアント シークレットは、自分が設定した Azure Resource Manager のパスワードです。

   詳細については、[Azure Resource Manager のパスワード](azure-stack-edge-j-series-set-azure-resource-manager-password.md)に関するページを参照してください。

5. プロファイルをバージョン 2019-03-01-hybrid に変更します。 プロファイルのバージョンを変更するには、次のコマンドを実行します。

    ```powershell
    az cloud update --profile 2019-03-01-hybrid
    ```

    `az cloud update` の使用例を次に示します。

    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud update --profile 2019-03-01-hybrid
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

## <a name="step-2-create-a-vm"></a>手順 2:VM の作成

VM を作成するための Python スクリプトが用意されています。 ユーザーとしてサインインするか、サービス プリンシパルとして設定されるかに応じて適切な入力をスクリプトに渡すことにより、VM が作成されます。

1. Python のインストール先と同じディレクトリから Python スクリプトを実行します。

    `.\python.exe example_dbe_arguments_name_https.py cli`

2. スクリプトの実行時、VHD のアップロードには 20 分から 30 分かかります。 アップロード操作の進行状況を確認するには、Azure Storage Explorer または AzCopy を使用できます。

    以下に示すのは、スクリプトの実行に成功した場合のサンプル出力です。 このスクリプトによって、すべてのリソースが 1 つのリソース グループ内に作成され、それらのリソースを使用して VM が作成されます。そして最後に、作成されたすべてのリソースを含むリソース グループが削除されます。

    
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe example_dbe_arguments_name_https.py cli
    
    Create Resource Group
    Create a storage account
    Uploading to Azure Stack Storage as blob:
            ubuntu13.vhd
    
    Listing blobs...
            ubuntu13.vhd
    
    VM image resource id:
                /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/azure-sample-group-virtual-machines118/providers/Microsoft.Compute/images/UbuntuImage
    
    Create Vnet
    Create Subnet
    Create NIC
    Creating Linux Virtual Machine
    Tag Virtual Machine
    Create (empty) managed Data Disk
    Get Virtual Machine by Name
    Attach Data Disk
    Detach Data Disk
    Deallocating the VM (to prepare for a disk resize)
    Update OS disk size
    Start VM
    Restart VM
    Stop VM
    
    List VMs in subscription
            VM: VmName118
    
    List VMs in resource group
            VM: VmName118
    
    Delete VM
    All example operations completed successfully!
    
    Delete Resource Group
    Deleted: azure-sample-group-virtual-machines118
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ``` 


## <a name="next-steps"></a>次のステップ

[Linux 仮想マシンの一般的な Az CLI コマンド](../virtual-machines/linux/cli-manage.md)