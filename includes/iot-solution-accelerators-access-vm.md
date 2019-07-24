---
title: インクルード ファイル
description: インクルード ファイル
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/16/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: db1af4f046bd8849fddee299e949d6edbdaae86a
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53609091"
---
## <a name="access-the-virtual-machine"></a>仮想マシンにアクセスする

次の手順では、Azure Cloud Shell の `az` コマンドを使用します。 必要に応じて、開発用マシンに [Azure CLI 2.0 をインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)し、ローカルでコマンドを実行することができます。

次の手順は、**SSH** アクセスを許可するように Azure 仮想マシンを構成する方法を示しています。 ここに示された手順では、ソリューション アクセラレータ用に選択された名前を **contoso-simulation** と想定しています。この値を実際のデプロイの名前に置き換えてください。

1. ソリューション アクセラレータ リソースを含むリソース グループの内容を一覧表示します。

    ```azurecli-interactive
    az resource list -g contoso-simulation -o table
    ```

    仮想マシンの名前、パブリック IP アドレス、およびネットワーク セキュリティ グループをメモします。これらの値は後で必要になります。

1. SSH アクセスを許可するようにネットワーク セキュリティ グループを更新します。 次のコマンドでは、ネットワーク セキュリティ グループの名前を **contoso-simulation-nsg** と想定しています。この値を実際のネットワーク セキュリティ グループの名前に置き換えてください。

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name contoso-simulation-nsg -g contoso-simulation --access Allow -o table
    ```

    SSH アクセスを有効にするのはテストおよび開発中のみにしてください。 SSH を有効にした場合、[できるだけ早く無効にする必要があります](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices#disable-rdpssh-access-to-virtual-machines)。

1. 仮想マシン上の **azureuser** アカウントのパスワードを既知のパスワードに更新します。 次のコマンドを実行するときに自分のパスワードを選択してください。

    ```azurecli-interactive
    az vm user update --name vm-vikxv --username azureuser --password YOURSECRETPASSWORD  -g contoso-simulation
    ```

1. 仮想マシンのパブリック IP アドレスを調べます。 次のコマンドでは、仮想マシンの名前を **vm-vikxv** と想定しています。この値を以前にメモした仮想マシンの名前に置き換えてください。

    ```azurecli-interactive
    az vm list-ip-addresses --name vm-vikxv -g contoso-simulation -o table
    ```

    仮想マシンのパブリック IP アドレスをメモします。
