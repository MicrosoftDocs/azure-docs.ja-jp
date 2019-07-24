---
title: Azure Marketplace 向けの VHD からの VM のデプロイ | Microsoft Docs
description: Azure にデプロイされた VHD からの VM を登録する方法について説明します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/30/2018
ms.author: pbutlerm
ms.openlocfilehash: f37ab1b9eef5be4aff6e5f8aecebc3688592f040
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59046684"
---
# <a name="deploy-a-vm-from-your-vhds"></a>VHD からの VM のデプロイ

このセクションでは、Azure にデプロイされた仮想ハード ディスク (VHD) からの仮想マシン (VM) をデプロイする方法について説明します。  必要なツールを一覧し、それらのツールを使ってユーザー VM イメージを作成し、PowerShell スクリプトを使って Azure にデプロイする方法を説明しています。

仮想ハード ディスク (VHD) —汎用化されたオペレーティング システム VHD および 0 個以上のデータ ディスク VHD—を Azure ストレージ アカウントにアップロードした後、これらをユーザー VM イメージとして登録できます。 その後、そのイメージをテストできます。 オペレーティング システム VHD は汎用化されるため、VHD URL を指定して VM を直接デプロイすることはできません。

VM イメージの詳細については、次のブログを参照してください。

- [VM イメージ](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [VM Image PowerShell 'How To' (VM イメージ PowerShell ハウツー)](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisite-install-the-necessary-tools"></a>前提条件: 必要なツールのインストール

Azure PowerShell と Azure CLI をまだインストールしていない場合は、次の指示に従ってインストールします。

- [Azure PowerShell をインストールするには](https://docs.microsoft.com/powershell/azure/install-Az-ps)
- [Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="deployment-steps"></a>デプロイメントの手順

次の手順を使用して、ユーザー VM イメージを作成してデプロイします。

1. ユーザー VM イメージを作成します。この場合、イメージのキャプチャと汎用化が必要となります。 
2. 証明書を作成し、新しい Azure Key Vault に格納します。 証明書は、VM 上でセキュリティ保護された WinRM 接続を確立するために必要です。  Azure Resource Manager テンプレートと Azure PowerShell スクリプトが提供されます。 
3. 指定されたテンプレートとスクリプトを使用して、ユーザー VM イメージから VM をデプロイします。

使用する VM がデプロイされたら、[VM イメージを認定](./cpp-certify-vm.md)する準備が整います。

1. **[新規]** をクリックして、**[テンプレートのデプロイ]** を検索し、**[エディターで独自のテンプレートを作成する]** を選択します。  <br/>
   ![Azure portal で VHD デプロイ テンプレートを作成する](./media/publishvm_021.png)

1. この [JSON テンプレート](./cpp-deploy-json-template.md)をコピーしてエディターに貼り付け、**[保存]** をクリックします。 <br/>
   ![Azure portal で VHD デプロイ テンプレートを保存する](./media/publishvm_022.png)

1. 表示されている**カスタム デプロイ**のプロパティ ページのパラメーター値を指定します。

   <table> <tr> <td valign="top"> <img src="./media/publishvm_023.png" alt="Custom deployment property page 1"> </td> <td valign="top"> <img src="./media/publishvm_024.png" alt="Custom deployment property page 2"> </td> </tr> </table> <br/> 

   |  **パラメーター**              |   **説明**                                                            |
   |  -------------              |   ---------------                                                            |
   | ユーザー ストレージ アカウント名   | 汎用化された VHD が配置されているストレージ アカウント名                    |
   | ユーザー ストレージ コンテナー名 | 汎用化された VHD が配置されているコンテナー名                          |
   | パブリック IP の DNS 名      | パブリック IP DNS 名                                                           |
   | 管理ユーザー名             | 新しい VM の管理者アカウントのユーザー名                                  |
   | 管理パスワード              | 新しい VM の管理者アカウントのパスワード                                  |
   | [OS Type]\(OS の種類\)                     | VM オペレーティング システム: `Windows` \| `Linux`                                    |
   | サブスクリプション ID             | 選択したサブスクリプションの識別子                                      |
   | Location                    | デプロイの地理的な場所                                        |
   | VM サイズ                     | [Azure VM サイズ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)、例`Standard_A2` |
   | パブリック IP アドレス名      | パブリック IP アドレスの名前                                               |
   | VM 名                     | 新しい VM の名前                                                           |
   | 仮想ネットワーク名        | VM で使用する仮想ネットワークの名前                                   |
   | NIC 名                    | 仮想ネットワークを実行しているネットワーク インターフェイス カードの名前               |
   | VHD の URL                     | OS ディスクの VHD の URL を完了する                                                     |
   |  |  |
            
1. これらの値を指定した後、**[購入]** をクリックします。 

Azure はデプロイを開始します。これにより、指定のストレージ アカウント パスに、指定されたアンマネージド VHD を含む新しい VM が作成されます。  ポータルの左側にある **[Virtual Machines]** をクリックして Azure portal で進捗状況を追跡できます。  VM が作成されると、状態は `Starting` から `Running` に変わります。 


### <a name="deploy-a-vm-from-powershell"></a>PowerShell からの VM のデプロイ

作成したばかりの汎用化された VM イメージから大規模な VM をデプロイするには、次のコマンドレットを使用します。

``` powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```


## <a name="next-steps"></a>次の手順

次に、ご自分のソリューション用の[ユーザー VM イメージを作成](cpp-create-user-image.md)します。

