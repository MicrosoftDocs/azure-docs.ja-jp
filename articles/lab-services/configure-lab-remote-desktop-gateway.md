---
title: Azure DevTest Labs でリモート デスクトップ ゲートウェイを使用するようにラボを構成する |Microsoft Docs
description: RDP ポートを公開せずにラボ VM に安全にアクセスできるようにするために、リモート デスクトップ ゲートウェイを使用して Azure DevTest Labs でラボを構成する方法について説明します。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 430734878c01d10a4e7dd385dc75d8d502a2d82c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081636"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>リモート デスクトップ ゲートウェイを使用するように Azure DevTest Labs でラボを構成します
Azure DevTest Labs では、RDP ポートを公開することなくラボ仮想マシン (VM) に安全にアクセスできるようにするために、ラボ用のリモート デスクトップ ゲートウェイを構成できます。 ラボは、ラボ ユーザーがアクセスできるすべての仮想マシンを表示および接続するための中央の場所を提供します。 **[仮想マシン]** ページの **[接続]** ボタンにより、そのマシンに接続するために開くことができるマシン固有の RDP ファイルが作成されます。 ラボをリモート デスクトップ ゲートウェイに接続することで、RDP 接続をさらにカスタマイズして保護できます。 

このアプローチでは、ラボ ユーザーがゲートウェイ マシンに対して直接認証するか、ドメインに参加しているゲートウェイ マシンで会社の資格情報を使用してマシンに接続できるため、より安全です。 ラボでは、RDP ポートをインターネットに公開せずに、ユーザーがラボ仮想マシンに接続できるようにするゲートウェイ マシンへのトークン認証も使用できます。 この記事は、トークン認証を使用してラボ マシンに接続するラボを設定する方法の例について説明します。

## <a name="architecture-of-the-solution"></a>ソリューションのアーキテクチャ

![ソリューションのアーキテクチャ](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. **[接続]** ボタンを選択すると、[[RDP ファイル コンテンツの取得]](/rest/api/dtl/virtualmachines/getrdpfilecontents) アクションが呼び出されます。1 
1. [RDP ファイル コンテンツの取得] アクションは、認証トークンを要求するために `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` を呼び出します。
    1. `{gateway-hostname}` は、Azure portal のラボ用の **[ラボの設定]** ページに指定されているゲートウェイ ホスト名です。 
    1. `{lab-machine-name}` は、接続しようとしているマシンの名前です。
    1. `{port-number}` は、接続を確立する必要があるポートです。 通常、このポートは 3389 です。 ラボ VM が DevTest Labs の[共有 IP](devtest-lab-shared-ip.md) 機能を使用している場合、ポートは異なります。
1. リモート デスクトップ ゲートウェイは、認証トークンを生成するために、`https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` からの Azure 関数への呼び出しを延期します。 DevTest Labs サービスは、要求ヘッダーに関数キーを自動的に含めます。 関数キーは、ラボのキー コンテナーに保存されます。 そのシークレットの名前が、ラボの **[ラボの設定]** ページに**ゲートウェイ トークン シークレット**として表示されます。
1. Azure 関数は、ゲートウェイ マシンに対する証明書ベースのトークン認証用のトークンを返すと想定されます。  
1. 続いて、[RDP ファイル コンテンツの取得] アクションは、認証情報を含む完全な RDP ファイルを返します。
1. 任意の RDP 接続プログラムを使用して RDP ファイルを開きます。 すべての RDP 接続プログラムがトークン認証をサポートしているわけではないことに注意してください。 認証トークンには、関数アプリによって設定された有効期限があります。 トークンの有効期限が切れる前にラボ VM への接続を作成してください。
1. リモート デスクトップ ゲートウェイ マシンが RDP ファイル内のトークンを認証すると、接続はラボ マシンに転送されます。

### <a name="solution-requirements"></a>ソリューションの要件
DevTest Labs のトークン認証機能を使用するには、ゲートウェイ マシン、ドメイン ネーム サービス (DNS)、関数に関するいくつかの構成要件があります。

### <a name="requirements-for-remote-desktop-gateway-machines"></a>リモート デスクトップ ゲートウェイ マシンの要件
- HTTPS トラフィックを処理するには、SSL 証明書がゲートウェイ マシンにインストールされている必要があります。 証明書は、ゲートウェイ ファームのロード バランサーの完全修飾ドメイン名 (FQDN)、またはマシンが 1 台しかない場合はマシン自体の FQDN と一致する必要があります。 ワイルドカード SSL 証明書は機能しません。  
- 署名証明書がゲートウェイ マシンにインストールされている必要があります。 署名証明書は [Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1) スクリプトを使用して作成します。
- リモート デスクトップ ゲートウェイのトークン認証をサポートする[プラグ可能認証](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273)モジュールをインストールします。 このようなモジュールの 1 つの例に、[System Center Virtual Machine Manager (VMM) のイメージ](/system-center/vmm/install-console?view=sc-vmm-1807)に付属している `RDGatewayFedAuth.msi` があります。 System Center の詳細については、[System Center のドキュメント](https://docs.microsoft.com/system-center/)と[価格の詳細](https://www.microsoft.com/cloud-platform/system-center-pricing)を参照してください。  
- ゲートウェイ サーバーで `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` に対する要求を処理できます。

    ゲートウェイのホスト名は、ゲートウェイ ファームのロード バランサーの FQDN、またはマシンが 1 台しかない場合はマシン自体の FQDN です。 `{lab-machine-name}` は接続を試みているラボ マシンの名前であり、`{port-number}` は接続が確立されるポートです。  既定では、このポートは 3389 です。  ただし、仮想マシンが DevTest Labs の[共有 IP](devtest-lab-shared-ip.md) 機能を使用している場合、このポートは異なります。
- Internet Information Server (IIS) の[アプリケーション ルーティング要求](/iis/extensions/planning-for-arr/using-the-application-request-routing-module)モジュールを使用して、`https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` 要求を、認証用のトークンを取得する要求を処理する azure 関数にリダイレクトできます。


## <a name="requirements-for-azure-function"></a>Azure 関数の要件
Azure 関数は、`https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` という形式で要求を処理し、ゲートウェイ マシンにインストールされているものと同じ署名証明書に基づいて認証トークンを返します。 `{function-app-uri}` は、関数にアクセスするために使用する uri です。 関数は、要求のヘッダーで自動的に渡されます。 関数のサンプルについては、[https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs) を参照してください。 


## <a name="requirements-for-network"></a>ネットワークの要件

- ゲートウェイ マシンにインストールされている SSL 証明書に関連付けられている FQDN の DNS は、ゲートウェイ マシンまたはゲートウェイ マシン ファームのロード バランサーにトラフィックを送信する必要があります。
- ラボ マシンでプライベート IP を使用する場合は、同じ仮想ネットワークを共有するか、ピアリングされた仮想ネットワークを使用することによって、ゲートウェイ マシンからラボ マシンへのネットワーク パスを設定する必要があります。

## <a name="configure-the-lab-to-use-token-authentication"></a>トークン認証を使用するようにラボを構成する 
このセクションでは、トークン認証をサポートするリモート デスクトップ ゲートウェイ マシンを使用するようにラボを構成する方法について説明します。 このセクションでは、リモート デスクトップ ゲートウェイ ファーム自体の設定方法については説明しません。 その情報については、この記事の最後にある「[リモート デスクトップ ゲートウェイを作成するためのサンプル](#sample-to-create-a-remote-desktop-gateway)」セクションを参照してください。 

ラボの設定を更新する前に、認証トークンを返す関数を正常に実行するために必要なキーをラボのキー コンテナーに格納します。 関数のキー値は、Azure portal 内の関数の **[管理]** ページで取得できます。 シークレットをキー コンテナーに保存する方法について詳しくは、「[Key Vault にシークレットを追加する](../key-vault/quick-create-portal.md#add-a-secret-to-key-vault)」を参照してください。 後で使用するためにシークレットの名前を保存します。

ラボのキー コンテナーの ID を見つけるには、次の Azure CLI コマンドを実行します。 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

次の手順を使用して、トークン認証を使用するようにラボを構成します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
1. ラボの一覧で、目的の**ラボ**を選択します。
1. ラボのページで、 **[構成とポリシー]** を選択します。
1. 左側のメニューの **[設定]** セクションで、 **[ラボの設定]** を選択します。
1. **[リモート デスクトップ]** セクションで、 **[ゲートウェイ ホスト名]** フィールドに、リモート デスクトップ サービスのゲートウェイ マシンまたはファームの完全修飾ドメイン名 (FQDN) または IP アドレスを入力します。 この値は、ゲートウェイ マシンで使用されている SSL 証明書の FQDN と一致する必要があります。

    ![ラボ設定のリモート デスクトップ オプション](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. **[リモート デスクトップ]** セクションの **[Gateway token secret]\(ゲートウェイ トークン シークレット\)**  に、先ほど作成したシークレットの名前を入力します。 この値は関数キー自体ではなく、関数キーを保持するラボのキー コンテナー内のシークレットの名前です。

    ![ラボ設定のゲートウェイ トークン シークレット](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. 変更を**保存**します。

    > [!NOTE] 
    > **[保存]** をクリックして、[リモート デスクトップ ゲートウェイのライセンス条項](https://www.microsoft.com/licensing/product-licensing/products)に同意します。 リモート ゲートウェイの詳細については、「[リモート デスクトップ サービスへようこそ](https://aka.ms/rds)」と「[リモート デスクトップ環境を展開する](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)」を参照してください。


自動化によってラボを構成したい場合は、**ゲートウェイ ホスト名**と**ゲートウェイ トークン シークレット**を設定するための PowerShell のサンプル スクリプトとして「[Set-DevTestLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1)」を参照してください。 [Azure DevTest Labs GitHub リポジトリ](https://github.com/Azure/azure-devtestlab)にも、**ゲートウェイ ホスト名**と**ゲートウェイ トークン シークレット**の設定を使用してラボを作成または更新するための Azure Resource Manager テンプレートが用意されています。

## <a name="configure-network-security-group"></a>ネットワーク セキュリティ グループを構成する
ラボをさらに保護するために、ラボ仮想マシンが使用する仮想ネットワークにネットワーク セキュリティ グループ (NSG) を追加できます。 NSG を設定する方法については、「[ネットワーク セキュリティ グループの作成、変更、削除](../virtual-network/manage-network-security-group.md)」を参照してください。

以下に、最初にゲートウェイを通過してラボ マシンに到達するトラフィックのみを許可する NSG の例を示します。 この規則のソースは、1 つのゲートウェイ マシンの IP アドレス、またはゲートウェイ マシンの前にあるロード バランサーの IP アドレスです。

![ネットワーク セキュリティ グループ - 規則](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>リモート デスクトップ ゲートウェイを作成するためのサンプル

> [!NOTE] 
> サンプルのテンプレートを使用して、[リモート デスクトップ ゲートウェイのライセンス条項](https://www.microsoft.com/licensing/product-licensing/products)に同意します。 リモート ゲートウェイの詳細については、「[リモート デスクトップ サービスへようこそ](https://aka.ms/rds)」と「[リモート デスクトップ環境を展開する](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)」を参照してください。

[Azure DevTest Labs GitHub リポジトリ](https://github.com/Azure/azure-devtestlab)は、DevTest Labs でトークン認証とリモート デスクトップ ゲートウェイを使用するために必要なリソースのセットアップに役立ついくつかのサンプルを提供しています。 これらのサンプルには、ゲートウェイ マシン、ラボの設定、関数アプリ用の Azure Resource Manager テンプレートが含まれています。

リモート デスクトップ ゲートウェイ ファーム用のサンプル ソリューションを設定するには、次の手順に従います。

1. 署名証明書を作成します。  [Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1) を実行します。 作成した証明書の拇印、パスワード、Base64 エンコードを保存します。
2. SSL 証明書を取得します。 SSL 証明書に関連付けられている FQDN は、管理するドメインのものでなくてなはりません。 この証明書の拇印、パスワード、Base64 エンコードを保存します。 PowerShell を使用して拇印を取得するには、次のコマンドを使用します。

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import(‘path-to-certificate’);
    $hash = $cer.GetCertHashString()
    ```

    PowerShell を使用して Base64 エンコードを取得するには、次のコマンドを使用します。

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(‘path-to-certificate’))
    ```
3. [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway) からファイルをダウンロードします。

    このテンプレートでは、同じベース URI にあるその他のいくつかの Resource Manager テンプレートおよび関連リソースへのアクセスが必要になります。 [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway) のすべてのファイルと RDGatewayFedAuth.msi をストレージ アカウントの BLOB コンテナーにコピーします。  
4. [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway) から **azuredeploy.json** をデプロイします。 このテンプレートは、以下のパラメーターを取ります。
    - adminUsername – 必須。  ゲートウェイ マシンの管理者ユーザー名。
    - adminPassword – 必須。 ゲートウェイ マシンの管理者アカウントのパスワード。
    - instanceCount – 作成するゲートウェイ マシンの数。  
    - alwaysOn – 作成した Azure Functions アプリをウォーム状態に保つかどうかを示します。 Azure Functions アプリを保つことにより、ユーザーが最初にラボ VM に接続しようとしたときの遅延を回避できますが、コストの問題が生じます。  
    - tokenLifetime – 作成されたトークンが有効な期間。 形式は HH:MM:SS です。
    - sslCertificate – ゲートウェイ マシン用の SSL 証明書の Base64 エンコード。
    - sslCertificatePassword – ゲートウェイ マシン用の SSL 証明書のパスワード。
    - sslCertificateThumbprint - SSL 証明書のローカル証明書ストアにある識別用の証明書の拇印。
    - sslCertificate – ゲートウェイ マシン用の署名証明書の Base64 エンコード。
    - signCertificatePassword – ゲートウェイ マシン用の署名証明書のパスワード。
    - signCertificateThumbprint - 署名証明書のローカル証明書ストアにある識別用の証明書の拇印。
    - _artifactsLocation – すべての対応リソースがある URI の場所。 この値は、相対パスではなく、完全修飾 UIR でなければなりません。
    - _artifactsLocationSasToken – 場所が Azure ストレージ アカウントの場合に、対応リソースにアクセスするために使用する Shared Access Signature (SAS) トークン。

    このテンプレートは、Azure CLI で次のコマンドを使用してデプロイできます。

    ```azurecli
    az group deployment create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation=”{storage-account-endpoint}/{container-name}” -–parameters _artifactsLocationSasToken = “?{sas-token}”
    ```

    以下に、パラメーターの説明を示します。

    - {storage-account-endpoint} は、`az storage account show --name {storage-acct-name} --query primaryEndpoints.blob` を実行することにより取得できます。  {storage-acct-name} は、アップロードしたファイルを保持するストレージ アカウントの名前です。  
    - {container-name} は、アップロードしたファイルを保持する {storage-acct-name} 内のコンテナーの名前です。  
    - {sas-token} は、`az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}` を実行することにより取得できます。 
        - {storage-acct-name} は、アップロードしたファイルを保持するストレージ アカウントの名前です。  
        - {container-name} は、アップロードしたファイルを保持する {storage-acct-name} 内のコンテナーの名前です。  
        - {utc-expiration-date} は、SAS トークンが期限切れになり、SAS トークンを使用してストレージアカウントにアクセスできなくなる日付を UTC で表したものです。

    テンプレートのデプロイ出力から gatewayFQDN と gatewayIP の値を記録します。 新しく作成された関数 ([[Function App の設定]](../azure-functions/functions-how-to-use-azure-function-app-settings.md) タブにあります) の関数キーの値を保存する必要もあります。
5. SSL 証明書の FQDN が前のステップの gatewayIP の IP アドレスを指すように DNS を構成します。

    リモート デスクトップ ゲートウェイ ファームが作成され、DNS が適切に更新されたら、DevTest Labs のラボで使用できるようになります。 デプロイしたゲートウェイ マシンを使用するように、**ゲートウェイ ホスト名**と**ゲートウェイ トークン シークレット**の設定を構成する必要があります。 

    > [!NOTE]
    > ラボ マシンでプライベート IP を使用する場合は、同じ仮想ネットワークを共有するか、ピアリングされた仮想ネットワークを使用することによって、ゲートウェイ マシンからラボ マシンへのネットワーク パスを設定する必要があります。

    ゲートウェイとラボの両方が構成されると、ラボ ユーザーが **[接続]** をクリックしたときに作成される接続ファイルに、トークン認証を使用して接続するために必要な情報が自動的に含まれます。     

## <a name="next-steps"></a>次の手順
リモート デスクトップ サービスの詳細については、次の記事を参照してください。[リモート デスクトップ サービスのドキュメント](/windows-server/remote/remote-desktop-services/Welcome-to-rds)


