---
title: "クラシック Azure VM へのログオン | Microsoft Docs"
description: "Azure クラシック ポータルを使用して、クラシック デプロイ モデルを使用して作成された Windows 仮想マシンにログオンします。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 3c1239ed-07dc-48b8-8b3d-dc8c5f0ab20e
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 22bc0b6c047737a291e79d9794e113b0821340ac
ms.lasthandoff: 03/25/2017


---
# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-portal"></a>Azure ポータルを使用して Windows 仮想マシンにログオンする
Azure ポータルの **[接続]** ボタンを使用して、リモート デスクトップ セッションを開始し、Windows VM にログオンします。

Linux VM に接続する場合は、 「[Linux が実行されている仮想マシンにログオンする方法](../../virtual-machines-linux-mac-create-ssh-keys.md)」を参照してください。

<!--
Deleting, but not 100% sure
Learn how to [perform these steps using new Azure portal](../../virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
-->

> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。 Resource Manager モデルを使用した VM へのログオン方法については、[こちら](../../virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)を参照してください。

## <a name="connect-to-the-virtual-machine"></a>仮想マシンへの接続
1. Azure ポータルにサインインします。
2. アクセスする仮想マシンをクリックします。 名前は **[すべてのリソース]** ウィンドウに一覧表示されます。

    ![Virtual-machine-locations](./media/connect-logon/azureportaldashboard.png)

3. 仮想マシンのダッシュボードの一番上にあるコマンド バーで、**[接続]** をクリックします。

    ![仮想マシンへの接続アイコン](./media/connect-logon/virtualmachine_dashboard_connect.png)

<!-- Don't know if this still applies
     I think we can zap this.
> [!TIP]
> If the **Connect** button isn't available, see the troubleshooting tips at the end of this article.
>
>
-->

## <a name="log-on-to-the-virtual-machine"></a>仮想マシンへのログオン
[!INCLUDE [virtual-machines-log-on-win-server](../../../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>次のステップ
* **[接続]** ボタンが非アクティブである場合、またはリモート デスクトップ接続に関するその他の問題が発生している場合は、構成をリセットします。 仮想マシンのダッシュボードで、**[リモート アクセスのリセット]** をクリックします。

    ![Reset-remote-access](./media/connect-logon/virtualmachine_dashboard_reset_remote_access.png)

* パスワードに問題がある場合は、パスワードをリセットしてください。 仮想マシンのダッシュボードの左側のウィンドウで、**[サポートとトラブルシューティング]** の下にある **[パスワードのリセット]** をクリックします。

    ![Reset-password](./media/connect-logon/virtualmachine_dashboard_reset_password.png)

これらのトピックで解決できない場合、または必要な情報が掲載されていない場合は、「[Windows ベースの Azure 仮想マシンへのリモート デスクトップ接続に関するトラブルシューティング](../../virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。 この記事では、一般的な問題の診断と解決の手順について説明します。

