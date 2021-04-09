---
title: Red Hat Enterprise Linux のサブスクリプション持ち込み Azure イメージ | Microsoft Docs
description: Azure での Red Hat Enterprise Linux のサブスクリプション持ち込みイメージについて説明します。
author: asinn826
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 06/10/2020
ms.author: alsin
ms.openlocfilehash: af4f9dac30c1e443e9647a5b752cdabf9c094a6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676086"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Azure での Red Hat Enterprise Linux のサブスクリプション持ち込み Gold Image

Red Hat Enterprise Linux (RHEL) イメージは、従量課金制またはサブスクリプション持ち込み (BYOS) (Red Hat Gold Image) モデルを使用して Azure で利用できます。 この記事では、Azure の Red Hat Gold Image の概要について説明します。

>[!NOTE]
> RHEL BYOS Gold イメージは、Azure パブリックと Azure Government クラウドで利用できます。 これらは、Azure China および Azure Blackforest クラウドでは利用できません。

## <a name="important-points-to-consider"></a>考慮すべき重要な点

- このプログラムで提供される Red Hat Gold Image は、Azure Marketplace の RHEL 従量課金制イメージとよく似た実稼働対応の RHEL イメージです。
- イメージは、[Azure の Red Hat Enterprise Linux イメージ](./redhat-images.md)に関するページで説明されている現在のポリシーに従います。
- Standard サポート ポリシーは、これらのイメージから作成された VM に適用されます。
- Red Hat Gold Image からプロビジョニングされた VM は、RHEL 従量課金制イメージに関連付けられている RHEL 料金がかかりません。
- イメージには、権利がありません。 Red Hat から直接更新プログラムを取得するために、Red Hat のサブスクリプション マネージャーを使用して VM を登録およびサブスクライブする必要があります。
- [Azure ハイブリッド特典](../../linux/azure-hybrid-benefit-linux.md)を使用して、従量課金制イメージから BYOS に切り替えることができます。 ただし、最初にデプロイされた BYOS から従量課金制に Linux イメージの課金モデルを切り替えることはできません。 課金モデルを BYOS から従量課金制に切り替えるには、それぞれのイメージから VM を再デプロイする必要があります。

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>Red Hat Gold Image にアクセスするための要件と条件

1. [Red Hat Cloud Access プログラム](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)の使用条件について理解を深めます。 [Red Hat サブスクリプション マネージャー](https://access.redhat.com/management/cloud)で、Cloud Access の Red Hat サブスクリプションを有効にします。 Cloud Access に登録する予定の Azure サブスクリプションを手元に用意しておく必要があります。

1. Cloud Access に対して有効にした Red Hat サブスクリプションが資格要件を満たしている場合は、Azure サブスクリプションが Gold Image アクセスに対して自動的に有効になります。

### <a name="expected-time-for-image-access"></a>イメージへのアクセスに予想される時間

Cloud Access を有効にする手順が完了すると、Red Hat によって Red Hat Gold Image の資格が検証されます。 検証が成功した場合、3 時間以内に Gold Image にアクセスできるようになります。

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>Azure portal から Red Hat Gold Image を使用する

1. Azure サブスクリプションが Red Hat Gold Image にアクセスできるようになると、[Azure portal](https://portal.azure.com) でそれらを検索できます。 **[リソースの作成]**  >  **[すべて表示]** に移動します。

1. ページの一番上に、プライベート プランがあることが表示されます。

    ![Marketplace プライベート プラン](./media/rhel-byos-privateoffers.png)

1. 紫色のリンクを選択するか、ページの一番下までスクロールしてプライベート プランを表示します。

1. UI でのプロビジョニングの残り部分は、他の既存の Red Hat イメージと変わりません。 RHEL バージョンを選択し、画面の指示に従って VM をプロビジョニングします。 このプロセスでは、最後の手順でイメージの使用条件に同意することもできます。

>[!NOTE]
>ここまでの手順では、Red Hat Gold Image をプログラムによって展開することはできません。 「追加情報」セクションで説明している追加の手順が必要になります。

このドキュメントの残りの部分では、CLI の方法を使用して、イメージのプロビジョニングとその使用条件への同意を行います。 UI と CLI は、最終的な結果 (プロビジョニングされた RHEL Gold Image VM) に関する限り、完全に同等です。

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>Azure CLI から Red Hat Gold Image を使用する

次の手順では、Azure CLI を使用した RHEL VM の初期デプロイ プロセスについて説明します。 この手順では、[Azure CLI がインストール済み](/cli/azure/install-azure-cli)であることを前提としています。

>[!IMPORTANT]
>次のすべてのコマンドでは、発行元、オファー、プラン、およびイメージの参照ですべて小文字を使用してください。

1. 目的のサブスクリプションを使用していることを確認します。

    ```azurecli
    az account show -o=json
    ```

1. Red Hat Gold Image VM のリソース グループを作成します。

    ```azurecli
    az group create --name <name> --location <location>
    ```

1. イメージの使用条件に同意します。

    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn redhat:rhel-byos:rhel-lvm8:8.0.20190620
    ```

    >[!NOTE]
    >これらの使用条件は、"*Azure サブスクリプションごと、イメージ SKU ごとに 1 回*" 同意する必要があります。

1. (省略可能) 次のコマンドを使用して、VM のデプロイを検証します。

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image redhat:rhel-byos:rhel-lvm8:latest --validate
    ```

1. `--validate` 引数を指定せずに前の例と同じコマンドを実行して、VM をプロビジョニングします。

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn>

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image redhat:rhel-byos:rhel-lvm8:latest
    ```

1. VM に SSH で接続し、権利のないイメージがあることを確認します。 この手順を行うには、`sudo yum repolist`を実行します。 RHEL 8 の場合は `sudo dnf repolist` を使用します。 出力で、サブスクリプション マネージャーを使用して、VM を Red Hat に登録するよう求められます。

>[!NOTE]
>RHEL 8 では、`dnf` と `yum` は交換可能です。 詳細については、[RHEL 8 管理者ガイド](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/packaging_and_distributing_software/index)を参照してください。

## <a name="use-the-red-hat-gold-images-from-powershell"></a>PowerShell から Red Hat Gold Image を使用する

次のスクリプトは一例です。 リソース グループ、場所、VM 名、ログイン情報、その他の変数を、選択した構成で置き換えてください。 発行元とプランの情報は、小文字にする必要があります。

```powershell-interactive
    # Variables for common values
    $resourceGroup = "testbyos"
    $location = "canadaeast"
    $vmName = "test01"

    # Define user name and blank password
    $securePassword = ConvertTo-SecureString 'TestPassword1!' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential("azureuser",$securePassword)
    Get-AzureRmMarketplaceTerms -Publisher redhat -Product rhel-byos -Name rhel-lvm75 | SetAzureRmMarketplaceTerms -Accept

    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroup -Location $location

    # Create a subnet configuration
    $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

    # Create a virtual network
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location
    $location `-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

    # Create a public IP address and specify a DNS name
    $pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location
    $location `-Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

    # Create an inbound network security group rule for port 22
    $nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name
    myNetworkSecurityGroupRuleSSH -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -
    DestinationAddressPrefix * `-DestinationPortRange 22 -Access Allow

    # Create a network security group
    $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location
    $location `-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH

    # Create a virtual network card and associate with public IP address and NSG
    $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -
    Location $location `-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

    # Create a virtual machine configuration
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D3_v2 |
    Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred |
    Set-AzureRmVMSourceImage -PublisherName redhat -Offer rhel-byos -Skus rhel-lvm75 -Version latest | Add-     AzureRmVMNetworkInterface -Id $nic.Id
    Set-AzureRmVMPlan -VM $vmConfig -Publisher redhat -Product rhel-byos -Name "rhel-lvm75"

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

    # Create a virtual machine
    New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>Red Hat Enterprise Linux のサブスクリプション持ち込み Gold Image の暗号化

Red Hat Enterprise Linux の BYOS Gold Image は、[Azure Disk Encryption](../../linux/disk-encryption-overview.md) を利用してセキュリティで保護できます。 サブスクリプションは、暗号化を有効にする前に登録する *必要があります*。 RHEL BYOS Gold Image を登録する方法の詳細については、「[How to register and subscribe a system to the Red Hat Customer Portal using Red Hat Subscription-Manager (Red Hat サブスクリプション マネージャーを使用して、Red Hat カスタマー ポータルにシステムを登録およびサブスクライブする方法)](https://access.redhat.com/solutions/253273)」を参照してください。 アクティブな Red Hat サブスクリプションをお持ちの場合は、「[Creating Red Hat Customer Portal Activation Keys (Red Hat カスタマー ポータルのアクティベーション キーを作成する)](https://access.redhat.com/articles/1378093)」もご覧ください。

[Red Hat カスタム イメージ](../../linux/redhat-create-upload-vhd.md)では、Azure Disk Encryption はサポートされていません。 Azure Disk Encryption の追加の要件と前提条件については、[Linux VM 向けの Azure Disk Encryption](../../linux/disk-encryption-overview.md#additional-vm-requirements) に関するページに記載されています。

Azure Disk Encryption の適用手順については、「[Linux VM での Azure Disk Encryption シナリオ](../../linux/disk-encryption-linux.md)」および関連記事をご覧ください。

## <a name="additional-information"></a>関連情報

- このプランで有効になっていないサブスクリプションで VM をプロビジョニングしようとすると、次のメッセージが表示されます。

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    この場合、Microsoft か Red Hat にお問い合わせいただき、サブスクリプションを有効にしてください。

- RHEL BYOS イメージのスナップショットを変更し、そのカスタム イメージを [Shared Image Gallery](../../shared-image-galleries.md) に公開する場合、スナップショットの元のソースに一致するプラン情報を指定する必要があります。 たとえば、コマンドは次のようになります。

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

    最後の行にある plan パラメーターに注意してください。

    [Azure Disk Encryption](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) はカスタム イメージではサポートされていません。

- RHEL BYOS イメージからの VM のプロビジョニングを自動化している場合、サンプル コマンドに示したような plan パラメーターを指定する必要があります。 たとえば、Terraform を使用する場合は、[plan ブロック](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan)にプラン情報を指定します。

## <a name="next-steps"></a>次のステップ

- Red Hat Cloud Access の詳細については、[Red Hat パブリック クラウドのドキュメント](https://access.redhat.com/public-cloud)を参照してください。
- Cloud Access のステップバイステップ ガイドとプログラムの詳細については、[Red Hat Cloud Access のドキュメント](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index)を参照してください。
- Red Hat Update Infrastructure の詳細については、[Azure Red Hat Update Infrastructure](./redhat-rhui.md) に関する記事を参照してください。
- Azure でのすべての Red Hat イメージの詳細については、[ドキュメントのページ](./redhat-images.md)を参照してください。
- すべてのバージョンの RHEL の Red Hat サポート ポリシーの詳細については、「[Red Hat Enterprise Linux life cycle (Red Hat Enterprise Linux のライフ サイクル)](https://access.redhat.com/support/policy/updates/errata)」を参照してください。
- RHEL Gold Image に関するその他のドキュメントについては、[Red Hat のドキュメント](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/cloud-access-gold-images_cloud-access#proc_using-gold-images-azure_cloud-access)を参照してください。
