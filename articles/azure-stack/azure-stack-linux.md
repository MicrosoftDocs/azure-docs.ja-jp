---
title: "Azure Stack への Linux イメージの追加"
description: "Azure Stack へ Linux イメージを追加する方法について説明します。"
services: azure-stack
documentationcenter: 
author: anjayajodha
manager: byronr
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/25/2017
ms.author: anajod
ms.openlocfilehash: a8763c01cba4e5a9eaa3b7842b627d6eb9661a95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="add-linux-images-to-azure-stack"></a>Azure Stack への Linux イメージの追加

*適用対象: Azure Stack 統合システムと Azure Stack Development Kit* 

Azure Stack Marketplace に Linux ベースのイメージを追加することによって、Azure Stack に Linux 仮想マシンをデプロイできます。 最も容易に Linux イメージを Azure Stack に追加する方法は、Marketplace Management からです。

## <a name="marketplace-management"></a>Marketplace Management

Azure Marketplace の Linux のイメージをダウンロードするには、次の記事の手順に従ってください。 Azure Stack で、ユーザーに提供する Linux のイメージを選択します。

[Azure から Azure Stack に Marketplace の項目をダウンロードする](azure-stack-download-azure-marketplace-item.md)

## <a name="download-an-image"></a>イメージのダウンロード

ユーザーは、次のリンクを使用して、Azure Stack と互換性のある Linux のイメージをダウンロードして抽出できます。


   * [Bitnami](https://bitnami.com/azure-stack)
   * [CentOS](http://olstacks.cloudapp.net/latest/)
   * [CoreOS](https://stable.release.core-os.net/amd64-usr/current/coreos_production_azure_image.vhd.bz2)
   * [SuSE](https://download.suse.com/Download?buildid=VCFi7y7MsFQ~)
   * [Ubuntu 14.04 LTS](https://partner-images.canonical.com/azure/azure_stack/) / [Ubuntu 16.04 LTS](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)

1. 必要に応じて、イメージ VHD を抽出し、[Marketplace にイメージを追加](azure-stack-add-vm-image.md)します。 `OSType` パラメーターが `Linux` に設定されていることを確認してください。
2. Marketplace にイメージを追加すると、Marketplace アイテムが作成され、ユーザーが Linux 仮想マシンをデプロイできます。

## <a name="prepare-your-own-image"></a>独自のイメージを準備する

次のいずれかの手順を使用すると、独自の Linux イメージを準備できます。
   
   * [CentOS ベースのディストリビューション](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Oracle Linux](../virtual-machines/linux/oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Red Hat Enterprise Linux](../virtual-machines/linux/redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [SLES と openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Ubuntu](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

1. [Azure Linux エージェント](https://github.com/Azure/WALinuxAgent/)をダウンロードしてインストールします。
   
    Azure Stack に Linux VM をプロビジョニングするためには、Azure Linux エージェント バージョン 2.1.3 以上が必要です。 前に説明したディストリビューションの多くは、それらのリポジトリに、このバージョン以上のエージェントがパッケージとして既に含まれています (通常 `WALinuxAgent` または `walinuxagent` と呼ばれる)。 ただし、Azure エージェント パッケージのバージョンが 2.1.3 未満 (たとえば 2.0.18 以下) である場合は、エージェントを手動でインストールする必要があります。 インストールされているバージョンは、パッケージの名前から、または VM で `/usr/sbin/waagent -version` を実行して判断できます。
   
    Azure Linux エージェントを手動でインストールするには、以下の手順に従います。
   
   a. まず、[GitHub](https://github.com/Azure/WALinuxAgent/releases) から、最新の Azure Linux エージェントをダウンロードします。例:
     
            # wget https://github.com/Azure/WALinuxAgent/archive/v2.2.16.tar.gz
   b. Azure エージェントを展開します。
     
            # tar -vzxf v2.2.16.tar.gz
   c. python-setuptools をインストールします。
     
        **Debian / Ubuntu**
     
            # sudo apt-get update
            # sudo apt-get install python-setuptools
     
        **Ubuntu 16.04+**
     
            # sudo apt-get install python3-setuptools
     
        **RHEL / CentOS / Oracle Linux**
     
            # sudo yum install python-setuptools
   d. Azure エージェントをインストールします。
     
            # cd WALinuxAgent-2.2.16
            # sudo python3 setup.py install --register-service
     
     Python 2.x および Python 3.x がサイド バイ サイドでインストールされたシステムでは、次のコマンドを実行する必要がある場合があります。
     
         sudo python3 setup.py install --register-service
     詳細については、Azure Linux エージェント [README](https://github.com/Azure/WALinuxAgent/blob/master/README.md) をご覧ください。
2. [Marketplace にイメージを追加](azure-stack-add-vm-image.md)します。 `OSType` パラメーターが `Linux` に設定されていることを確認してください。
3. Marketplace にイメージを追加すると、Marketplace アイテムが作成され、ユーザーが Linux 仮想マシンをデプロイできます。

## <a name="next-steps"></a>次のステップ
[Azure Stack でのサービスの提供の概要](azure-stack-offer-services-overview.md)

