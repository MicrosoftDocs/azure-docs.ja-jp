---
title: Microsoft Azure Stack Hub 適合性チェッカー ツールを使用して証明書を作成する | Microsoft Docs
description: Azure Stack Hub 適合性チェッカー ツールを使用して証明書要求を作成し、Azure Stack Edge Pro GPU デバイスで証明書を取得してインストールする方法について説明します。
services: Azure Stack Edge Pro
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: a04243093b89b6a2498efc48f80cbd7a47d57337
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102437725"
---
# <a name="create-certificates-for-your-azure-stack-edge-pro-using-azure-stack-hub-readiness-checker-tool"></a>Microsoft Azure Stack Hub 適合性チェッカー ツールを使用して Azure Stack Edge Pro の証明書を作成する 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Microsoft Azure Stack Hub 適合性チェッカー ツールを使用して Azure Stack Edge Pro の証明書を作成する方法について説明します。 

## <a name="using-azure-stack-hub-readiness-checker-tool"></a>Azure Stack Hub 適合性チェッカー ツールを使用する

Azure Stack Hub 適合性チェッカー ツールを使用して、Azure Stack Edge Pro デバイスのデプロイのための証明書署名要求 (CSR) を作成できます。 これらの要求は、Azure Stack Edge Pro デバイスの注文を行ってデバイスが届くのを待ってから作成できます。

> [!NOTE]
> このツールは、実稼働デバイスではなく、テストまたは開発目的でのみ使用してください。 

Azure Stack Hub 対応性チェッカー ツール (AzsReadinessChecker) を使用すると、次の証明書を要求できます。

- Azure Resource Manager 証明書
- ローカル UI 証明書
- ノード証明書
- BLOB 証明書
- VPN 証明書


## <a name="prerequisites"></a>前提条件

Azure Stack Edge Pro デバイスのデプロイ用の CSR を作成するには、次のことを確認してください。 

- Windows 10 または Windows Server 2016 以降を実行しているクライアントがある。 
- このシステムの [PowerShell ギャラリー](https://aka.ms/AzsReadinessChecker)から Microsoft Azure Stack Hub 適合性チェッカー ツールをダウンロードした。
- 証明書のための次の情報がある。
  - [デバイス名]
  - ノードのシリアル番号
  - 外部完全修飾ドメイン名 (FQDN)

## <a name="generate-certificate-signing-requests"></a>証明書の署名要求を生成する

次の手順を使用して、Azure Stack Edge Pro デバイス証明書を準備します。

1. PowerShell (5.1 以降) を管理者として実行します。
2. Azure Stack Hub 適合性チェッカー ツールをインストールします。 PowerShell プロンプトで次のように入力します。 

    ```azurepowershell
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

    インストールされているバージョンを表示するには、次のように入力します。  

    ```azurepowershell
    Get-InstalledModule -Name Microsoft.AzureStack.ReadinessChecker  | ft Name, Version 
    ```

3. すべての証明書用のディレクトリがまだない場合は作成します。 型: 
    
    ```azurepowershell
    New-Item "C:\certrequest" -ItemType Directory
    ``` 
    
4. 証明書要求を作成するには、次の情報を指定します。 VPN 証明書を生成する場合、これらの入力の一部は適用されません。
    
    |入力 |説明  |
    |---------|---------|
    |`OutputRequestPath`|証明書要求を作成するローカル クライアント上のファイル パス。        |
    |`DeviceName`|デバイスのローカル Web UI で **[デバイス]** ページに表示されるデバイスの名前。 <br> このフィールドは、VPN 証明書には必要ありません。         |
    |`NodeSerialNumber`|デバイスのローカル web UI の **[ネットワーク]** ページに表示されるデバイス ノードのシリアル番号。 <br> このフィールドは、VPN 証明書には必要ありません。       |
    |`ExternalFQDN`|デバイスのローカル Web UI で **[デバイス]** ページに表示される DNSDomain 値。         |
    |`RequestType`|要求の種類は、`MultipleCSR` (さまざまなエンドポイントに対して異なる証明書) または `SingleCSR` (すべてのエンドポイントに対して 1 つの証明書) を指定できます。 <br> このフィールドは、VPN 証明書には必要ありません。     |

    VPN 証明書以外のすべての証明書の場合は、次のように入力します。 
    
    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeDEVICE'
        DeviceName = 'myTEA1'
        NodeSerialNumber = 'VM1500-00025'
        externalFQDN = 'azurestackedge.contoso.com'
        requestType = 'MultipleCSR'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    VPN 証明書を作成する場合は、次のように入力します。 

    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeVPN'
        externalFQDN = 'azurestackedge.contoso.com'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    
5. 上記の OutputRequestPath パラメーターで指定したディレクトリ内に、証明書要求ファイルがあります。 `MultipleCSR` パラメーターを使用すると、`.req` の拡張子を持つ次の 4 つのファイルが表示されます。

    
    |ファイル名  |証明書要求の種類  |
    |---------|---------|
    |`DeviceName` で始まる     |ローカル Web UI 証明書要求      |
    |`NodeSerialNumber` で始まる     |ノード証明書要求         |
    |`login` 以降     |Azure Resource Manager エンドポイント証明書要求       |
    |`wildcard` 以降     |BLOB ストレージ証明書要求。 これには、デバイス上に作成できるすべてのストレージ アカウントに対応するため、ワイルドカードが含まれています。          |
    |`AzureStackEdgeVPNCertificate` 以降     |VPN クライアント証明書要求。         |

    INF フォルダーも表示されます。 ここには証明書の詳細を説明するクリア テキストの management.<edge-devicename> という情報ファイルが含まれています。  


6. これらのファイルを証明機関 (内部またはパブリックのいずれか) に送信します。 CA によって、生成された要求が使用され、[ノード証明書](azure-stack-edge-gpu-manage-certificates.md#node-certificates)、[エンドポイント証明書](azure-stack-edge-gpu-manage-certificates.md#endpoint-certificates)、[ローカル UI 証明書](azure-stack-edge-gpu-manage-certificates.md#local-ui-certificates)に対する Azure Stack Edge Pro 証明書要件を満たす証明書が生成されることを確認してください。

## <a name="prepare-certificates-for-deployment"></a>デプロイ用の証明書を準備する

証明機関 (CA) から取得した証明書ファイルは、Azure Stack Edge Pro デバイスの証明書要件に一致するプロパティを使用してインポートおよびエクスポートする必要があります。 証明書署名要求を生成したのと同じシステムで、次の手順を実行します。

- 証明書をインポートするには、[Azure Stack Edge Pro デバイスにアクセスするクライアントに証明書をインポートする](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)方法に関する手順に従います。

- 証明書をエクスポートするには、[Azure Stack Edge Pro デバイスにアクセスするクライアントからの証明書のエクスポート](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)の手順に従います。


## <a name="validate-certificates"></a>証明書を検証する

まず、適切なフォルダー構造を生成し、対応するフォルダーに証明書を配置します。 その後で初めて、ツールを使用して証明書を検証します。

1. PowerShell を管理者として実行します。

2. 適切なフォルダー構造を生成するには、プロンプトで次のように入力します。

    `New-AzsCertificateFolder -CertificateType AzureStackEdge -OutputPath "$ENV:USERPROFILE\Documents\AzureStackCSR"`

3. PFX パスワードをセキュリティで保護された文字列に変換します。 型:       

    `$pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString` 

4. 次に、証明書を検証します。 型:

    `Invoke-AzsCertificateValidation -CertificateType AzureStackEdge -DeviceName mytea1 -NodeSerialNumber VM1500-00025 -externalFQDN azurestackedge.contoso.com -CertificatePath $ENV:USERPROFILE\Documents\AzureStackCSR\AzureStackEdge -pfxPassword $pfxPassword`

## <a name="next-steps"></a>次のステップ

[Azure Stack Edge Pro デバイスを配置する](azure-stack-edge-gpu-deploy-prep.md)
