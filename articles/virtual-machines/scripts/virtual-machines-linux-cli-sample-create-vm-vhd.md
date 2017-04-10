---
title: "Azure CLI のサンプル スクリプト - VHD を使用した VM の作成 | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - 仮想ハード ディスクを使用して VM を作成します。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/09/2017
ms.author: allclark
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 383929ee41e7d174b4534f988554ba28bb41b9bb
ms.lasthandoff: 03/21/2017

---

# <a name="create-a-vm-with-a-virtual-hard-disk"></a>仮想ハード ディスクを使用した VM の作成

この例では、VHD を使用して仮想マシンを作成します。
ここではリソース グループ、ストレージ アカウント、コンテナーを作成し、VHD を作成したコンテナーにアップロードすることで VM を作成します。
ssh 公開キーをお使いの公開キーに置き換えることで、VM にアクセスできるようになります。

起動可能な VHD が必要です。
ここで使用した VHD は https://azclisamples.blob.core.windows.net/vhds/sample.vhd からダウンロードできます。お持ちの VHD を使用することもできます。 スクリプトは `~/sample.vhd` を探します。

このサンプルは、bash シェルに対応しています。 Azure CLI スクリプトを Windows クライアントで実行する方法については、[Windows での Azure CLI の実行](../virtual-machines-windows-cli-options.md)に関する記事を参照してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[メイン](../../../cli_scripts/virtual-machine/create-vm-vhd/create-vm-vhd.sh "VHD を使用して VM を作成する")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```azurecli
az group delete -n az-cli-vhd
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、仮想マシン、可用性セット、ロード バランサー、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az storage account list](https://docs.microsoft.com/cli/azure/storage/account#list) | ストレージ アカウントの一覧を表示します。 |
| [az storage account check-name](https://docs.microsoft.com/cli/azure/storage/account#check-name) | ストレージ アカウントの名前が有効で、存在していないことを確認します。 |
| [az storage account keys list](https://docs.microsoft.com/cli/azure/storage/account/keys#list) | ストレージ アカウントのキーの一覧を表示します。 |
| [az storage blob exists](https://docs.microsoft.com/cli/azure/storage/blob#exists) | BLOB が存在するかどうかを確認します。 |
| [az storage container create](https://docs.microsoft.com/cli/azure/storage/container#create) | ストレージ アカウントにコンテナーを作成します。 |
| [az storage blob upload](https://docs.microsoft.com/cli/azure/storage/blob#upload) | VHD をアップロードすることで、コンテナーに BLOB を作成します。 |
| [az vm list](https://docs.microsoft.com/cli/azure/vm#list) | `--query` チェックと併用して VM 名が使用中かどうかを確認します。 | 
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set#create) | 仮想マシンを作成します。 |
| [az vm access set-linux-user](https://docs.microsoft.com/cli/azure/vm/access#set-linux-user) | SSH キーをリセットして現在のユーザーに VM へのアクセス許可を付与します。 |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm#list-ip-addresses) | 作成した VM の IP アドレスを取得します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の仮想マシン用の CLI サンプル スクリプトは、[Azure Linux VM のドキュメント](../virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)のページにあります。

