---
title: "HPC Pack クラスターの計算ノードの管理 | Microsoft Docs"
description: "Azure の HPC Pack 2012 R2 クラスターの計算ノードを追加、削除、起動、停止するための PowerShell スクリプト ツールについて説明します。"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,hpc-pack
ms.assetid: 4193f03b-94e9-4704-a7ad-379abde063a9
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: dc9f354191b9e80ff6a01bd401a874c6998bda79
ms.lasthandoff: 03/27/2017


---
# <a name="manage-the-number-and-availability-of-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Azure の HPC Pack クラスターのコンピューティング ノードの数と可用性を管理する
Azure VM で HPC Pack 2012 R2 クラスターを作成した場合に、クラスターでいくつかの計算ノード VM を簡単に追加、削除、起動 (プロビジョニング)、または停止 (プロビジョニング解除) できる方法があれば便利です。 そのような作業を行うには、ヘッド ノード VM にインストールされている Azure PowerShell スクリプトを実行します。 これらのスクリプトを利用すれば、HPC Pack クラスター リソースの数と可用性を制御し、コストを管理できます。

> [!IMPORTANT] 
> この記事は、クラシック デプロイメント モデルを使用して作成された Azure の HPC Pack 2012 R2 クラスターのみを対象としています。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。
> また、この記事で説明されている PowerShell スクリプトは HPC Pack 2016 では使用できません。

## <a name="prerequisites"></a>前提条件
* **Azure VM の HPC Pack 2012 R2 クラスター**: クラシック デプロイメント モデルで HPC Pack 2012 R2 クラスターを作成します。 たとえば、Azure Marketplace の HPC Pack 2012 R2 VM イメージや Azure PowerShell スクリプトを利用して、デプロイを自動化できます。 詳細と前提条件については、「[HPC Pack IaaS デプロイ スクリプトを使用した HPC クラスターの作成](hpcpack-cluster-powershell-script.md)」をご覧ください。
  
    デプロイ後、ヘッド ノードの %CCP\_HOME%bin フォルダーで、ノード管理スクリプトを探します。 各スクリプトは管理者として実行します。
* **Azure 発行設定ファイルまたは管理証明書**: ヘッド ノードで次のいずれかを行う必要があります。
  
  * **Azure 発行設定ファイルをインポートします**。 これを行うには、ヘッド ノードで次の Azure PowerShell コマンドレットを実行します。
    
    ```PowerShell
    Get-AzurePublishSettingsFile
    
    Import-AzurePublishSettingsFile –PublishSettingsFile <publish settings file>
    ```
  * **ヘッド ノードで Azure 管理証明書を構成します**。 .cer ファイルがある場合、それを CurrentUser\My 証明書ストアにインポートし、Azure 環境 (either AzureCloud または AzureChinaCloud) に対して次の Azure PowerShell コマンドレットを実行します。
    
    ```PowerShell
    Set-AzureSubscription -SubscriptionName <Sub Name> -SubscriptionId <Sub ID> -Certificate (Get-Item Cert:\CurrentUser\My\<Cert Thrumbprint>) -Environment <AzureCloud | AzureChinaCloud>
    ```

## <a name="add-compute-node-vms"></a>コンピューティング ノード VM の追加
**Add-HpcIaaSNode.ps1** スクリプトでコンピューティング ノードを追加します。

### <a name="syntax"></a>構文
```PowerShell
Add-HPCIaaSNode.ps1 [-ServiceName] <String> [-ImageName] <String>
 [-Quantity] <Int32> [-InstanceSize] <String> [-DomainUserName] <String> [[-DomainUserPassword] <String>]
 [[-NodeNameSeries] <String>] [<CommonParameters>]

```
### <a name="parameters"></a>パラメーター
* **ServiceName**: 新しい計算ノード VM が追加されるクラウド サービスの名前。
* **ImageName**: Azure VM イメージ名。これは Azure クラシック ポータルまたは Azure PowerShell コマンドレットの **Get-AzureVMImage** で取得できます。 このイメージは次の要件を満たしている必要があります。
  
  1. Windows オペレーティング システムをインストールする必要があります。
  2. コンピューティング ノード ロールに HPC Pack をインストールする必要があります。
  3. イメージは、公開 Azure VM イメージではなく、ユーザー カテゴリのプライベート イメージである必要があります。
* **Quantity**: 追加する計算ノード VM の数。
* **InstanceSize**: 計算ノード VM のサイズ。
* **DomainUserName**: ドメイン ユーザー名。これは新しい VM をドメインに追加するときに使用されます。
* **DomainUserPassword**: ドメイン ユーザーのパスワード。
* **NodeNameSeries** (省略可能): 計算ノードの名前付けパターン。 形式は、&lt;*Root\_Name*&gt;&lt;*Start\_Number*&gt;% です。 たとえば、｢MyCN%10%｣は MyCN11 から始まる一連のコンピューティング ノード名を意味します。 指定されていない場合、HPC クラスターに構成されているノード命名規則が使用されます。

### <a name="example"></a>例
次の例では、*hpccnimage1* という VM イメージに基づいて、*hpcservice1* というクラウド サービスに、サイズが Large の計算ノード VM を 20 個追加します。

```PowerShell
Add-HPCIaaSNode.ps1 –ServiceName hpcservice1 –ImageName hpccniamge1
–Quantity 20 –InstanceSize Large –DomainUserName <username>
-DomainUserPassword <password>
```


## <a name="remove-compute-node-vms"></a>コンピューティング ノード VM の削除
**Remove-HpcIaaSNode.ps1** スクリプトでコンピューティング ノードを削除します。

### <a name="syntax"></a>構文
```PowerShell
Remove-HPCIaaSNode.ps1 -Name <String[]> [-DeleteVHD] [-Force] [-WhatIf] [-Confirm] [<CommonParameters>]

Remove-HPCIaaSNode.ps1 -Node <Object> [-DeleteVHD] [-Force] [-Confirm] [<CommonParameters>]
```

### <a name="parameters"></a>パラメーター
* **Name**: 削除するクラスター ノードの名前。 ワイルドカードを利用できます。 パラメーター セット名は「Name」です。 「**Name**」パラメーターと「**Node**」パラメーターの両方を指定することはできません。
* **Node**: 削除するノードの HpcNode オブジェクト。これは HPC PowerShell コマンドレットの [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx) で取得できます。 パラメーター セット名は「Node」です。 「**Name**」パラメーターと「**Node**」パラメーターの両方を指定することはできません。
* **DeleteVHD** (省略可能): 削除される VM の関連ディスクの削除設定。
* **Force** (省略可能): 削除前に HPC ノードを強制的にオフラインにする設定。
* **Confirm** (省略可能): コマンドを実行する前に確認を求めます。
* **WhatIf**: 実際にコマンドを実行せず、コマンドの実行結果を表示する設定。

### <a name="example"></a>例
次の例では、名前が「*HPCNode-CN-*」で始まるノードを強制的にオフラインにし、それからノードと関連ディスクを削除します。

```PowerShell
Remove-HPCIaaSNode.ps1 –Name HPCNodeCN-* –DeleteVHD -Force
```

## <a name="start-compute-node-vms"></a>コンピューティング ノード VM の起動
**Start-HpcIaaSNode.ps1** スクリプトでコンピューティング ノードを起動します。

### <a name="syntax"></a>構文
```PowerShell
Start-HPCIaaSNode.ps1 -Name <String[]> [<CommonParameters>]

Start-HPCIaaSNode.ps1 -Node <Object> [<CommonParameters>]
```
### <a name="parameters"></a>パラメーター
* **Name**: 起動するクラスター ノードの名前。 ワイルドカードを利用できます。 パラメーター セット名は「Name」です。 「**Name**」パラメーターと「**Node**」パラメーターの両方を指定することはできません。
* **Node** - 起動するノードの HpcNode オブジェクト。これは HPC PowerShell コマンドレットの [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx) から取得できます。 パラメーター セット名は「Node」です。 「**Name**」パラメーターと「**Node**」パラメーターの両方を指定することはできません。

### <a name="example"></a>例
次の例では、名前が「 *HPCNode-CN-*」で始まるノードが起動します。

```PowerShell
Start-HPCIaaSNode.ps1 –Name HPCNodeCN-*
```

## <a name="stop-compute-node-vms"></a>コンピューティング ノード VM の停止
**Stop-HpcIaaSNode.ps1** スクリプトでコンピューティング ノードを停止します。

### <a name="syntax"></a>構文
```PowerShell
Stop-HPCIaaSNode.ps1 -Name <String[]> [-Force] [<CommonParameters>]

Stop-HPCIaaSNode.ps1 -Node <Object> [-Force] [<CommonParameters>]
```

### <a name="parameters"></a>パラメーター
* **Name**- 停止するクラスター ノードの名前。 ワイルドカードを利用できます。 パラメーター セット名は「Name」です。 「**Name**」パラメーターと「**Node**」パラメーターの両方を指定することはできません。
* **Node**: 停止するノードの HpcNode オブジェクト。これは HPC PowerShell コマンドレットの [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx) で取得できます。 パラメーター セット名は「Node」です。 「**Name**」パラメーターと「**Node**」パラメーターの両方を指定することはできません。
* **Force** (省略可能): 停止前に HPC ノードを強制的にオフラインにする設定。

### <a name="example"></a>例
次の例では、名前が「 *HPCNode-CN-* 」で始まるノードを強制的にオフラインにし、それからノードを停止します。

```PowerShell
Stop-HPCIaaSNode.ps1 –Name HPCNodeCN-* -Force
```

## <a name="next-steps"></a>次のステップ
* クラスターのジョブやタスクの現在のワークロードに合わせてクラスター ノードを自動的に拡大縮小する方法については、「[クラスターのワークロードに合わせて Azure の HPC Pack クラスター リソースを自動的に拡大縮小する](hpcpack-cluster-node-autogrowshrink.md)」をご覧ください。


