---
title: "リモート デスクトップを Linux VM にアタッチする | Microsoft Docs"
description: "リモート デスクトップをインストールして、Microsoft Azure Linux VM に接続するように構成する方法を説明します。"
services: virtual-machines-linux
documentationcenter: 
author: SuperScottz
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 34348659-ddb7-41da-82d6-b5885859e7e4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/01/2016
ms.author: mingzhan
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: b909d0d18452127cd33bbaa362a12ec414a22329
ms.lasthandoff: 03/27/2017


---
# <a name="using-remote-desktop-to-connect-to-a-microsoft-azure-linux-vm"></a>リモート デスクトップを使用して Microsoft Azure Linux VM に接続する
> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。

## <a name="overview"></a>概要
RDP (リモート デスクトップ プロトコル) は、Windows の専用プロトコルです。 RDP を使用して Linux VM (仮想マシン) をリモート接続するにはどうすればよいでしょうか。

このガイドを読めば、その答えがわかります。 RDP は、Microsoft Azure Linux VM に xrdp をインストールして構成するのに役立ちます。また、これには Windows コンピューターからリモート デスクトップを使用して接続できます。 このガイドでは、例として Ubuntu または OpenSUSE を実行する Linux VM を使用します。

xrdp はオープン ソースの RDP サーバーで、これを使用すると、Windows コンピューターからリモート デスクトップを使用して Linux サーバーに接続できます。 このパフォーマンスは、VNC (Virtual Network Computing) よりも優れています。 VNC には "JPEG" 品質と動作が遅いという欠点があるのに対し、RDP は高速かつ明瞭です。

> [!NOTE]
> Linux を実行している既存の Microsoft Azure VM が必要です。 Linux VM を作成してセットアップするには、[Azure Linux VM のチュートリアル](createportal.md)を参照してください。
> 
> 

## <a name="create-endpoint-for-remote-desktop"></a>リモート デスクトップ用のエンドポイントを作成する
このドキュメントでは、リモート デスクトップに既定のエンドポイント 3389 を使用します。 そのため、次のように、Linux VM に対してリモート デスクトップとしてエンドポイント 3389 を設定します。

![イメージ](./media/remote-desktop/no1.png)

VM にエンドポイントを設定する方法がわからない場合は、[ガイダンス](setup-endpoints.md)を参照してください。

## <a name="install-gnome-desktop"></a>Gnome デスクトップをインストールする
Putty を使用して Linux VM に接続し、 `Gnome Desktop`をインストールします。

Ubuntu の場合は、次のコマンドを使用します。

    #sudo apt-get update
    #sudo apt-get install ubuntu-desktop


OpenSUSE の場合は、次のコマンドを使用します。

    #sudo zypper install gnome-session

## <a name="install-xrdp"></a>xrdp をインストールする
Ubuntu の場合は、次のコマンドを使用します。

    #sudo apt-get install xrdp

OpenSUSE の場合は、次のコマンドを使用します。

> [!NOTE]
> 次のコマンドでは、OpenSUSE のバージョンを現在使用中のバージョンに更新してください。`OpenSUSE 13.2` のコマンドの例を次に示します。
> 
> 

    #sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
    #sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc


## <a name="start-xrdp-and-set-xdrp-service-at-boot-up"></a>xrdp を開始して起動時に xdrp サービスを設定する
OpenSUSE の場合は、次のコマンドを使用します。

    #sudo systemctl start xrdp
    #sudo systemctl enable xrdp

Ubuntu の場合は、インストール後の自動起動時に xrdp が開始され、有効になります。

## <a name="using-xfce-if-you-are-using-ubuntu-version-later-than-ubuntu-1204lts"></a>Ubuntu 12.04LTS 以降のバージョンの Ubuntu を使用している場合に xfce を使用する
現在の xrdp では Ubuntu 12.04LTS 以降のバージョンの Ubuntu から Gnome デスクトップへの接続がサポートされていないため、代わりに `xfce` のデスクトップを使用します。

`xfce`をインストールするには、次のコマンドを使用します。

    #sudo apt-get install xubuntu-desktop

`xfce`を有効にするには、次のコマンドを使用します。

    #echo xfce4-session >~/.xsession

構成ファイル `/etc/xrdp/startwm.sh` を編集するには、次のコマンドを使用します。

    #sudo vi /etc/xrdp/startwm.sh   

行 `/etc/X11/Xsession` の前に行 `xfce4-session` を追加します。

xrdp サービスを再起動するには、次のコマンドを使用します。

    #sudo service xrdp restart


## <a name="connect-your-linux-vm-from-a-windows-machine"></a>Windows コンピューターから Linux VM を接続する
Windows コンピューターでリモート デスクトップ クライアントを起動し、Linux VM の DNS 名を入力するか、Azure クラシック ポータルで VM の`Dashboard`に移動して [`Connect`] をクリックすると、Linux VM に接続され、次のようなログイン ウィンドウが表示されます。

![image](./media/remote-desktop/no2.png)

Linux VM の `user` & `password` を使用してログインすると、Microsoft Azure Linux VM からリモート デスクトップを今すぐ利用できるようになります。

## <a name="next"></a>次へ
xrdp の使用の詳細については、 [こちら](http://www.xrdp.org/)を参照してください。


