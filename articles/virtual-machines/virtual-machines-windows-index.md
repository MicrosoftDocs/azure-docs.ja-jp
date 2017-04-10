---
title: "クラシック Windows VM に関する技術解説記事 | Microsoft Azure"
description: "クラシック デプロイメント モデルにおける Windows 仮想マシンに関する Microsoft Azure ドキュメント記事の一覧"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
tags: azure-service-management
editor: 
ms.assetid: 7f9a508b-34ec-4bdb-92d1-8844480369d5
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/13/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 4933d4ea33fd7299b220f90f8f904ae71c446e0c
ms.lasthandoff: 03/25/2017


---
# <a name="technical-articles-for-windows-vms-in-the-classic-deployment-model"></a>クラシック デプロイメント モデルにおける Windows VM に関する技術解説記事
ここでは、クラシック デプロイメント モデルで Windows ベースの Azure 仮想マシンを作成および管理するのに必要なすべてのドキュメントを紹介します。

> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../azure-resource-manager/resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。

## <a name="overview"></a>概要
[仮想マシンについて](virtual-machines-windows-about.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[クラシック デプロイ モデルで作成された Azure Virtual Machines についてよく寄せられる質問](windows/classic/faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[VM、Web サイト、およびクラウド サービスの比較](../app-service-web/choose-web-site-cloud-service-vm.md)

[Virtual Machines とコンテナーが Azure にもたらすメリット](virtual-machines-windows-containers.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="environment-setup"></a>環境のセットアップ
[無料アカウント](https://azure.microsoft.com/free/)

[Azure PowerShell をインストールするには](/powershell/azureps-cmdlets-docs)

[Azure CLI のインストール](../cli-install-nodejs.md)

## <a name="get-started"></a>作業開始
[Windows VM のラーニング パス](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/)

[Azure クラシック ポータルで Windows 仮想マシンを作成する](windows/classic/tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Windows Server が実行されているクラシック仮想マシンにログオンする方法](windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="plan"></a>プラン
[クラシック仮想マシンのイメージについて](windows/classic/about-images.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[仮想マシンのサイズ](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[H シリーズとコンピューティング集中型 A シリーズの VM について](virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Azure Virtual Machines に対する計画的なメンテナンス](virtual-machines-windows-planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Azure インフラストラクチャ サービス実装ガイドライン](virtual-machines-windows-infrastructure-subscription-accounts-guidelines.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[仮想マシンの可用性セットの作成](windows/classic/configure-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="deploy"></a>デプロイ
[Windows を実行するカスタム仮想マシンの作成](windows/classic/createportal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[クラシック デプロイ モデルで作成された Windows 仮想マシンをキャプチャする](windows/classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[PowerShell を使用したクラシック Windows Server VHD の作成とアップロード](windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Chef で Azure 仮想マシンのデプロイメントを自動化する](virtual-machines-windows-chef-automation.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Azure PowerShell でのクラシック Windows 仮想マシンの作成と構成](windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Azure の仮想マシンにカスタム データを挿入する](windows/classic/inject-custom-data.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="manage"></a>管理
[Azure PowerShell を使用した仮想マシンの管理](windows/classic/manage-psh.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[従来の VNet を新しい VNet に接続する](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)

[仮想マシンのエージェントおよび拡張機能について](windows/classic/agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[仮想マシン拡張機能の管理](windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[クラシック Windows 仮想マシンのカスタム スクリプト拡張機能](windows/classic/extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[プラットフォームでサポートされているクラシックから Azure Resource Manager への移行](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)

## <a name="configure"></a>構成
[Windows 仮想マシンのパスワードまたはリモート デスクトップ サービスをリセットする方法](virtual-machines-windows-reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[仮想マシンの拡張機能とその機能について](virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Windows VM に Symantec Endpoint Protection をインストールし、構成する方法](windows/classic/install-symantec.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Windows VM に Trend Micro Deep Security をサービスとしてインストールし、構成する方法](windows/classic/install-trend.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[クラシック デプロイ モデルに仮想マシンの可用性セットを構成する方法](windows/classic/configure-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[クラシック Azure 仮想マシンでエンドポイントをセットアップする方法](windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="storage"></a>Storage
[Azure 仮想マシン用のディスクと VHD について](../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[クラシック Windows 仮想マシンにデータ ディスクを接続する方法](windows/classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[クラシック Windows 仮想マシンからデータ ディスクを切断する方法](windows/classic/detach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Windows 仮想マシンのデータ ドライブとしての D ドライブの使用](virtual-machines-windows-change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="networking"></a>ネットワーク
[Virtual Network の概要](../virtual-network/virtual-networks-overview.md)

[仮想ネットワークまたはクラウド サービスで構成されるクラシック デプロイ モデルで作成された仮想マシンに接続します。](windows/classic/connect-vms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Azure PowerShell で NSG を管理する方法](../virtual-network/virtual-networks-create-nsg-classic-ps.md)

[ロード バランサーの作成](../load-balancer/load-balancer-get-started-internet-classic-portal.md)

## <a name="develop"></a>開発
[Visual Studio での Azure Virtual Machines の作成と管理](windows/classic/manage-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Visual Studio での Web アプリケーション用仮想マシンの作成](windows/classic/web-app-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[仮想マシンで多くのコンピューティング処理を要する Java タスクを実行する方法](windows/classic/java-run-compute-intensive-task.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Windows Server VM での Django Hello World Web アプリケーション](windows/classic/python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="workloads"></a>ワークロード
[HPC Pack](virtual-machines-windows-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[MongoDB](windows/classic/install-mongodb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[MySQL](windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Oracle](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html#support)

[SAP](windows/classic/sap-get-started.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[SQL Server](./windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[Tomcat](windows/classic/java-run-tomcat-app-server.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="reference"></a>リファレンス
[サービス管理モードでの Azure CLI コマンド](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)

[サービス管理 REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx)

[サービス管理 .NET API](https://msdn.microsoft.com/library/azure/mt420161.aspx)

[Azure サービス管理 PowerShell コマンドレットのリファレンス ドキュメント](https://msdn.microsoft.com/library/azure/dn708504.aspx)

## <a name="troubleshooting"></a>トラブルシューティング
[Windows を実行する Azure 仮想マシンへの Remote Desktop 接続に関するトラブルシューティング](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Azure 仮想マシンで実行されているアプリケーションへのアクセスに関するトラブルシューティング](virtual-machines-windows-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Azure で VM を作成、再起動、またはサイズ変更するときの割り当てエラーのトラブルシューティング](virtual-machines-windows-allocation-failure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Azure での新しい Windows 仮想マシンの作成に関するクラシック デプロイメントの問題のトラブルシューティング](windows/classic/troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Azure での既存の Windows 仮想マシンの再起動またはサイズ変更に関するクラシック デプロイメントの問題のトラブルシューティング](windows/classic/virtual-machines-windows-classic-restart-resize-error-troubleshooting.md)

