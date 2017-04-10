---
title: "VM (クラシック) のプライベート IP アドレスの構成 - Azure CLI 1.0 | Microsoft Docs"
description: "Azure コマンドライン インターフェイス (CLI) 1.0 を使用して仮想マシン (クラシック) のプライベート IP アドレスを構成する方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 17386acf-c708-4103-9b22-ff9bf04b778d
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: ed0fe2fea20671063395b9ff089599853278989d
ms.lasthandoff: 03/21/2017


---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-cli-10"></a>Azure CLI 1.0 を使用して仮想マシン (クラシック) のプライベート IP アドレスを構成する

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

この記事では、クラシック デプロイメント モデルについて説明します。 [リソース マネージャーのデプロイメント モデルで静的プライベート IP アドレスを管理する](virtual-networks-static-private-ip-arm-cli.md)こともできます。

次のサンプル Azure CLI コマンドでは、単純な環境が既に作成されていると想定します。 このドキュメントに表示されているコマンドを実行する場合は、まず、 [vnet の作成](virtual-networks-create-vnet-classic-cli.md)に関する記事に示されているテスト環境を構築します。

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>VM 作成時に静的プライベート IP アドレスを指定する方法
上記のシナリオに基づいて、*TestService* という名前の新しいクラウド サービスで *DNS01* という名前の VM を作成するには、以下の手順に従います。

1. Azure CLI を初めて使用する場合は、「 [Azure CLI のインストール](../cli-install-nodejs.md) 」を参照して、Azure のアカウントとサブスクリプションを選択する時点までの指示に従います。
2. **azure service create** コマンドを実行して、クラウド サービスを作成します。
   
        azure service create TestService --location uscentral
   
    予想される出力:
   
        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name TestService
        info:    service create command OK
3. **azure create vm** コマンドを実行して、VM を作成します。 静的プライベート IP アドレスの値を確認します。 出力の後に表示される一覧では、使用されたパラメーターについて説明されています。
   
        azure vm create -l centralus -n DNS01 -w TestVNet -S "192.168.1.101" TestService bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 adminuser AdminP@ssw0rd
   
    予想される出力:
   
        info:    Executing command vm create
        warn:    --vm-size has not been specified. Defaulting to "Small".
        info:    Looking up image bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
        info:    Looking up virtual network
        info:    Looking up cloud service
        warn:    --location option will be ignored
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Retrieving storage accounts
        info:    Creating VM
        info:    OK
        info:    vm create command OK
   
   * **-l (または --location)**。 VM が作成される Azure リージョンです。 ここでは、 *centralus*です。
   * **-n (または --vm-name)**。 作成する VM の名前です。
   * **-w (または --virtual-network-name)**。 VM の作成先となる VNet の名前です。 
   * **-S (または --static-ip)**。 VM の静的プライベート IP アドレスです。
   * **TestService**。 VM の作成先となるクラウド サービスの名前です。
   * **bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2**。 VM の作成に使用されるイメージです。
   * **adminuser**。 Windows VM のローカル管理者です。
   * **AdminP@ssw0rd**こともできます。 Windows VM のローカル管理者のパスワードです。

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>VM 用の静的プライベート IP アドレス情報を取得する方法
上記のスクリプトで作成された VM の静的プライベート IP アドレス情報を表示するには、次の Azure CLI コマンドを実行し、 *Network StaticIP*の値を確認します。

    azure vm static-ip show DNS01

予想される出力:

    info:    Executing command vm static-ip show
    info:    Getting virtual machines
    data:    Network StaticIP "192.168.1.101"
    info:    vm static-ip show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>VM から静的プライベート IP アドレスを削除する方法
上記のスクリプトで VM に追加された静的プライベート IP アドレスを削除するには、次の Azure CLI コマンドを実行します。

    azure vm static-ip remove DNS01

予想される出力:

    info:    Executing command vm static-ip remove
    info:    Getting virtual machines
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip remove command OK

## <a name="how-to-add-a-static-private-ip-to-an-existing-vm"></a>既存の VM に静的プライベート IP を追加する方法
上記のスクリプトを使用して作成した VM に静的プライベート IP アドレスを追加するには、次のコマンドを実行します。

    azure vm static-ip set DNS01 192.168.1.101

予想される出力:

    info:    Executing command vm static-ip set
    info:    Getting virtual machines
    info:    Looking up virtual network
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip set command OK

## <a name="next-steps"></a>次のステップ
* [予約済みパブリック IP](virtual-networks-reserved-public-ip.md) アドレスについて理解する。
* [インスタンスレベル パブリック IP (ILPIP)](virtual-networks-instance-level-public-ip.md) アドレスについて理解する。
* [予約済み IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)を確認する。


