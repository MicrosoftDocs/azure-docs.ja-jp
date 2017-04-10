---
title: "Azure Windows VM のイメージをキャプチャする | Microsoft Docs"
description: "クラシック デプロイ モデルを使用して作成された Azure Windows 仮想マシンのイメージをキャプチャする"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: a5986eac-4cf3-40bd-9b79-7c811806b880
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 06a97b48fed8f80679632f5c0320d4dd73b8e1e2
ms.lasthandoff: 03/27/2017


---
# <a name="capture-an-image-of-an-azure-windows-virtual-machine-created-with-the-classic-deployment-model"></a>クラシック デプロイ モデルを使用して作成された Azure Windows 仮想マシンのイメージをキャプチャする
> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。 リソース マネージャー モデルについては、「[Create a copy Windows VM running in Azure」 (Azure で実行されている Windows VM のコピーを作成する)](../../virtual-machines-windows-vhd-copy.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。

ここでは、Windows を実行する Azure 仮想マシンをキャプチャして、他の仮想マシンを作成する際にイメージとして使用する方法を示します。 このイメージには、オペレーティング システム ディスクと、仮想マシンに接続されているすべてのデータ ディスクが含まれます。 ネットワーク構成は含まれないため、イメージを使用する他の仮想マシンを作成するときは、ネットワーク構成を設定する必要があります。

Azure では、すべての Azure サービスを表示したときに表示される **[Compute]** サービスの **[VM イメージ (クラシック)]** にイメージが格納されます。 この場所は、アップロードしたイメージが格納される場所と同じです。 イメージの詳細については、「 [仮想マシン イメージについて](about-images.md?toc=%2fazure%2fvirtual-machines%2fWindows%2fclassic%2ftoc.json)」を参照してください。

## <a name="before-you-begin"></a>開始する前に
これらの手順は、すでに Azure 仮想マシンを作成し、データ ディスクの接続を含め、オペレーティング システムの構成が完了していることを前提としています。 まだ完了していない場合、仮想マシンの作成と準備については、次の記事をご覧ください。

* [イメージから仮想マシンを作成](createportal.md)
* [データ ディスクを仮想マシンに接続する方法](attach-disk.md)
* サーバー ロールが Sysprep でサポートされていることを確認してください。 詳しくは、「 [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)」 (サーバー ロールに対する Sysprep サポート) をご覧ください。

> [!WARNING]
> キャプチャされた後に、このプロセスは元の仮想マシンを削除します。
>
>

Azure 仮想マシンのイメージをキャプチャする前に、ターゲット仮想マシンをバックアップしておくことをお勧めします。 Azure 仮想マシンは、Azure Backup を使用してバックアップできます。 詳細については、「 [Back up Azure virtual machines (Azure 仮想マシンのバックアップ)](../../../backup/backup-azure-vms.md)」を参照してください。 その他のソリューションは認定パートナーから利用できます。 利用可能なソリューションについては、Azure Marketplace を検索して確認してください。

## <a name="capture-the-virtual-machine"></a>仮想マシンをキャプチャする
1. [Azure Portal](http://portal.azure.com) で仮想マシンに**接続**します。 詳細については、[Windows Server が実行されている仮想マシンにサインインする方法][How to sign in to a virtual machine running Windows Server]に関するページを参照してください。
2. 管理者としてコマンド プロンプト ウィンドウを開きます。
3. ディレクトリを `%windir%\system32\sysprep`に変更し、sysprep.exe を実行します。
4. **[システム準備ツール]** ダイアログ ボックスが表示されます。 以下の手順を実行します。

   * **[システム クリーンアップ アクション]** で **[システムの OOBE (Out-of-Box Experience) に入る]** を選択し、**[一般化する]** チェック ボックスがオンになっていることを確認します。 Sysprep の使い方の詳細については、「[Sysprep の使用方法: 紹介][How to Use Sysprep: An Introduction]」を参照してください。
   * **[シャットダウン オプション]** の **[シャットダウン]** を選択します。
   * **[OK]**をクリックします。

   ![Sysprep を実行する](./media/capture-image/SysprepGeneral.png)
5. Sysprep コマンドにより仮想マシンがシャットダウンされます。これにより、Azure クラシック ポータルの仮想マシンの状態が **[停止済み]** に変わります。
6. Azure Portal で **[仮想マシン (クラシック)]** をクリックし、キャプチャする仮想マシンを選択します。 **[VM イメージ (クラシック)]** グループは、**[その他のサービス]** を表示したときに **[Compute]** の下に表示されます。

7. コマンド バーで、 **[取り込み]**をクリックします。

   ![仮想マシンをキャプチャする](./media/capture-image/CaptureVM.png)

   **[仮想マシンからイメージをキャプチャします]** ダイアログ ボックスが表示されます。

8. **[イメージの名前]** に新しいイメージの名前を入力します。 **[イメージ ラベル]** に新しいイメージのラベルを入力します。

9. **[仮想マシンで Sysprep を実行しました]** をクリックします。 このチェック ボックスは、手順 3. ～ 5. の Sysprep での操作を指しています。 Windows Server イメージをカスタム イメージ セットに追加する前に、Sysprep を実行してイメージを一般化する_必要があります_。

10. キャプチャが完了すると、**[Marketplace]** の **[Compute]** の **[VM イメージ (クラシック)]** コンテナーで新しいイメージが利用可能になります。

    ![イメージのキャプチャの成功](./media/capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>次のステップ
イメージを使用して仮想マシンを作成する準備ができました。 イメージを使用して仮想マシンを作成するには、サービス メニューの下部の **[その他のサービス]** メニュー項目を選択し、**[Compute]** グループの **[VM イメージ (クラシック)]** を選択します。 手順については、「 [イメージから仮想マシンを作成](createportal.md)」を参照してください。

[How to sign in to a virtual machine running Windows Server]:connect-logon.md
[How to Use Sysprep: An Introduction]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/capture-image/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/capture-image/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png

