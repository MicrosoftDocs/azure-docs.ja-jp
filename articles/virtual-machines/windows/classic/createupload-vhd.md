---
title: "Powershell を使用した VM イメージの作成とアップロード | Microsoft Docs"
description: "従来のデプロイ モデルと Azure Powershell を使用し、一般化された Windows Server イメージ (VHD) を作成してアップロードする方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 8c4a08fe-7714-4bf0-be87-c728a7806d3f
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/21/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 8cae78052a4433f0a26d774a7085c110e32911c9
ms.lasthandoff: 03/27/2017


---
# <a name="create-and-upload-a-windows-server-vhd-to-azure"></a>Windows Server VHD の作成と Azure へのアップロード
この記事では、仮想マシンを作成できるように、独自の一般化された VM イメージを仮想ハードディスク (VHD) としてアップロードする方法について説明します。 Microsoft Azure でのディスクと VHD の詳細については、「[Virtual Machines 用のディスクと VHD について](../../../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。

> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。 Resource Manager モデルを使用して、仮想マシンを[アップロード](../../virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)することもできます。

## <a name="prerequisites"></a>前提条件
この記事では、以下のことを前提としています。

* **Azure サブスクリプション** - サブスクリプションがない場合は、 [Azure アカウントを無料で作成](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)できます。
* **[Microsoft Azure PowerShell](/powershell/azureps-cmdlets-docs)** - Microsoft Azure PowerShell モジュールをインストールし、サブスクリプションを使用するように構成しておきます。
* **.vhd ファイル** - .vhd ファイルに格納され、仮想マシンに接続された、Windows オペレーティング システムでサポートされている。 VHD で実行されているサーバー ロールが Sysprep でサポートされていることを確認してください。 詳しくは、「 [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)」 (サーバー ロールに対する Sysprep サポート) をご覧ください。

    > [!IMPORTANT]
    > VHDX 形式は、Microsoft Azure ではサポートされていません。 Hyper-V マネージャーまたは [Convert-VHD コマンドレット](http://technet.microsoft.com/library/hh848454.aspx)を使用して、ディスクを VHD 形式に変換できます。 詳細については、この [ブログの投稿](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx)を参照してください。

## <a name="step-1-prep-the-vhd"></a>手順 1: VHD を準備する
VHD を Azure にアップロードする前に、Sysprep ツールを使用して一般化する必要があります。 一般化によって、VHD をイメージとして使用できるように準備します。 Sysprep の詳細については、「 [Sysprep の使用方法: 紹介](http://technet.microsoft.com/library/bb457073.aspx)」を参照してください。 Sysprep を実行する前に、VM をバックアップします。

オペレーティング システムがインストールされている仮想マシンから、次の手順を実行します。

1. オペレーティング システムにサインインします。
2. 管理者としてコマンド プロンプト ウィンドウを開きます。 ディレクトリを **%windir%\system32\sysprep** に変更し、`sysprep.exe` を実行します。

    ![コマンド プロンプト ウィンドウを開く](./media/createupload-vhd/sysprep_commandprompt.png)
3. **[システム準備ツール]** ダイアログ ボックスが表示されます。

   ![Sysprep の開始](./media/createupload-vhd/sysprepgeneral.png)
4. **[システム準備ツール]** で **[システムの OOBE (Out-of-Box Experience) に入る]** を選択し、**[一般化する]** チェック ボックスがオンになっていることを確認します。
5. **[シャットダウン オプション]** の **[シャットダウン]** を選択します。
6. **[OK]**をクリックします。

## <a name="step-2-create-a-storage-account-and-a-container"></a>手順 2: ストレージ アカウントとコンテナーを作成する
.vhd ファイルをアップロードする場所を用意するために、Azure のストレージ アカウントが必要です。 この手順では、アカウントを作成する方法と、既存のアカウントから必要な情報を取得する方法について説明します。 角かっこ (&lsaquo;&rsaquo;) 内の変数は実際の情報に置き換えます。

1. ログイン

    ```powershell
    Add-AzureAccount
    ```

2. Azure サブスクリプションを設定します。

    ```powershell
    Select-AzureSubscription -SubscriptionName <SubscriptionName>
    ```

3. 新しいストレージ アカウントを作成します。 ストレージ アカウントの名前は、一意の 3 ～ 24 文字にする必要があります。 この名前は、文字と数字の任意の組み合わせとすることができます。 "米国東部" などの場所を指定する必要があります。

    ```powershell
    New-AzureStorageAccount –StorageAccountName <StorageAccountName> -Location <Location>
    ```

4. 新しいストレージ アカウントを既定に設定します。

    ```powershell
    Set-AzureSubscription -CurrentStorageAccountName <StorageAccountName> -SubscriptionName <SubscriptionName>
    ```

5. 新しいコンテナーを作成します。

    ```powershell
    New-AzureStorageContainer -Name <ContainerName> -Permission Off
    ```

## <a name="step-3-upload-the-vhd-file"></a>手順 3: .vhd ファイルをアップロードする
[Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx) を使用して VHD をアップロードします。

前の手順で使用した Azure PowerShell ウィンドウから次のコマンドを入力し、角かっこ (&lsaquo;&rsaquo;) 内の変数を実際の情報に置き換えます。

```powershell
Add-AzureVhd -Destination "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -LocalFilePath <LocalPathtoVHDFile>
```

## <a name="step-4-add-the-image-to-your-list-of-custom-images"></a>手順 4: カスタム イメージの一覧にイメージを追加する
[Add-AzureVMImage](https://msdn.microsoft.com/library/mt589167.aspx) コマンドレットを使用して、カスタム イメージの一覧にイメージを追加します。

```powershell
Add-AzureVMImage -ImageName <ImageName> -MediaLocation "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -OS "Windows"
```

## <a name="next-steps"></a>次のステップ
アップロードしたイメージを使用して、[カスタム VM を作成](createportal.md)できるようになりました。

