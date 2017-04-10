---
title: "Azure の Windows VM に MongoDB をインストールする | Microsoft Docs"
description: "Windows Server を実行するクラシック デプロイ モデルで作成された Azure VM に MongoDB をインストールする方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 4095df41-bb69-4bbe-9c1c-70923b0d84ba
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 7cf2c0a9e5e7785397a7229e3b44e9dbcc26f429
ms.lasthandoff: 03/27/2017


---
# <a name="install-mongodb-on-a-windows-vm-in-azure"></a>Azure の Windows VM に MongoDB をインストールします。
> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、[Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。  この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。 リソース マネージャーのデプロイ モデルを使用して MongoDB をインストールおよび構成する方法については、[この記事](../../virtual-machines-windows-install-mongodb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)をご覧ください。

[MongoDB][MongoDB] は、高いパフォーマンスを特徴とし、広く普及しているオープンソースの NoSQL データベースです。 この記事では、[Azure Portal][AzurePortal] を使用して Windows Server 仮想マシン (VM) を作成する手順について説明します。 その後で、MongoDB をインストールして構成する前に、データ ディスクを作成して VM に接続します。 使用する VM が Azure に既に存在する場合は、 [MongoDB のインストールと構成](#install-and-run-mongodb-on-the-virtual-machine)を直接開始できます。

## <a name="create-a-virtual-machine-running-windows-server"></a>Windows Server を実行する仮想マシンの作成
仮想マシンを作成するには、以下の手順を実行します。

[!INCLUDE [virtual-machines-create-WindowsVM](../../../../includes/virtual-machines-create-windowsvm.md)]

> [!NOTE]
> 仮想マシンの作成中に MongoDB のエンドポイントを追加して、**Mongo** という名前を付けることができます。また、プロトコルとして **TCP** を指定し、パブリック ポートとプライベート ポートの両方を **27017** に設定します。
>
>

## <a name="attach-a-data-disk"></a>データ ディスクの接続
仮想マシン用のストレージを用意するため、データ ディスクを接続し、Windows で使用できるように初期化します。 データ ディスクが既にある場合は、その既存のディスクを接続するか、空のディスクを接続することができます。

[!INCLUDE [howto-attach-disk-windows-linux](../../../../includes/howto-attach-disk-windows-linux.md)]

ディスクを初期化する方法については、「 [データ ディスクを Windows 仮想マシンに接続する方法](attach-disk.md)」の「方法: Windows Server での新しいデータ ディスクの初期化」を参照してください。

## <a name="install-and-run-mongodb-on-the-virtual-machine"></a>仮想マシンへの MongoDB のインストールと実行
[!INCLUDE [install-and-run-mongo-on-win2k8-vm](../../../../includes/install-and-run-mongo-on-win2k8-vm.md)]

## <a name="summary"></a>概要
このチュートリアルでは、Windows Server を実行する仮想マシンを作成する方法、リモートから仮想マシンに接続する方法、データ ディスクを接続する方法について学習しました。  Windows ベースの仮想マシンに MongoDB をインストールして構成する方法も学習しました。 [MongoDB のドキュメント][MongoDocs]の高度なトピックに従って Windows ベースの仮想マシンで MongoDB に今すぐアクセスすることができます。

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzurePortal]: https://portal.azure.com/

<!-- Classic portal. Removed 03/07/2017 -->
<!-- [AzurePortal]: http://manage.windowsazure.com  -->

