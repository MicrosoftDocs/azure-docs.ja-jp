---
title: Key Vault と Azure の Windows VM 上の SQL Server との統合 (クラシック) | Microsoft Docs
description: Azure Key Vault で使用するために SQL Server 暗号化の構成を自動化する方法について説明します。 このトピックでは、クラシック デプロイ モデルで作成される SQL Server 仮想マシンで Azure Key Vault 統合を使用する方法について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: ab8d41a7-1971-4032-ab71-eb435c455dc1
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/17/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 96d2dc567a4ccc96d33c2ccac233268a5b9148e4
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100335"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Azure Virtual Machines (クラシック) 上の SQL Server 向け Azure Key Vault 統合の構成
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-ps-sql-keyvault.md)
> * [クラシック](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>概要
[透過的なデータ暗号化 (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)、[列レベルの暗号化 (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)、[バックアップ暗号化](https://msdn.microsoft.com/library/dn449489.aspx) など、SQL Server 暗号化機能が複数存在します。 これらの形態の暗号化では、暗号化に利用する暗号鍵を管理し、保存する必要があります。 Azure Key Vault (AKV) サービスは、セキュリティを強化し、安全かつ可用性の高い場所で鍵を管理できるように設計されています。 [SQL Server コネクタ](https://www.microsoft.com/download/details.aspx?id=45344) を利用すると、SQL Server は Azure Key Vault にある鍵を利用できます。

> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、2 種類のデプロイ モデルがあります。[Resource Manager とクラシック](../../../azure-resource-manager/resource-manager-deployment-model.md)です。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイメントでは、リソース マネージャー モデルを使用することをお勧めします。

SQL Server をオンプレミス コンピューターで実行している場合、 [いくつかの手順を踏んでオンプレミスの SQL Server コンピューターから Azure Key Vault にアクセスできます](https://msdn.microsoft.com/library/dn198405.aspx)。 ただし、Azure VM の SQL Server の場合、 *Azure Key Vault 統合* 機能を利用し、時間を節約できます。 いくつかの Azure PowerShell コマンドレットでこの機能を有効にし、SQL VM が Key Vault にアクセスするために必要な構成を自動化できます。

この機能が有効になっていると、SQL Server コネクタが自動的にインストールされ、Azure Key Vault にアクセスするように EKM プロバイダーが構成され、Vault へのアクセスを許可する資格情報が作成されます。 前述のオンプレミス文書の手順を見れば、この機能で手順 2 と 3 が自動化されることがわかります。 手動でしなければならないことは、Key Vault と鍵を作成することだけです。 そこから先は、SQL VM の設定全体が自動化されます。 この機能でこの設定が完了したら、T-SQL ステートメントを実行し、通常どおり、データベースやバックアップの暗号化を開始できます。

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>AKV 統合の構成
PowerShell を使用し、Azure Key Vault 統合を構成します。 次のセクションでは、必要なパラメーターの概要とサンプル PowerShell スクリプトを提供します。

### <a name="install-the-sql-server-iaas-extension"></a>SQL Server IaaS 拡張機能のインストール
まず、 [SQL Server IaaS 拡張機能をインストール](../classic/sql-server-agent-extension.md)します。

### <a name="understand-the-input-parameters"></a>入力パラメーターについて
次の表は、以降のセクションで PowerShell スクリプトを実行するために必要となるパラメーターをまとめたものです。

| パラメーター | 説明 | 例 |
| --- | --- | --- |
| **$akvURL** |**Key Vault の URL** |"https:\//contosokeyvault.vault.azure.net/" |
| **$spName** |**サービス プリンシパル名** |"fde2b411-33d5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**サービス プリンシパル シークレット** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=" |
| **$credName** |**資格情報名**:AKV 統合により SQL Server 内に資格情報が作成されます。VM に Key Vault にアクセスする許可が与えられます。 この資格情報の名前を選択します。 |"mycred1" |
| **$vmName** |**[仮想マシン名]** : 前に作成した SQL VM の名前。 |"myvmname" |
| **$serviceName** |**サービス名**:SQL VM に関連付けられているクラウド サービスの名前。 |"mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>PowerShell で AKV 統合を有効にする
**New-AzureVMSqlServerKeyVaultCredentialConfig** コマンドレットにより、Azure Key Vault 統合機能の構成オブジェクトが作成されます。 **Set-AzureVMSqlServerExtension** により、**KeyVaultCredentialSettings** パラメーターでこの統合が構成されます。 次の手順では、これらのコマンドを使用する方法を示します。

1. Azure PowerShell で、最初に、このトピックの前のセクションで説明した特定の値で入力パラメーターを構成します。 次のスクリプトは一例です。
   
        $akvURL = "https:\//contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2. 次のスクリプトを利用し、AKV 統合を構成し、有効にします。
   
        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

SQL IaaS Agent Extension により、この新しい構成で SQL VM が更新されます。

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

