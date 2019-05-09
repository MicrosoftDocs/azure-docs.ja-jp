---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: a4c9ec133b3686a92cec7e7c8d4552c1302e3074
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58125140"
---
Resource Manager のコマンドとテンプレートで Azure CLI を使用して、リソース グループを使用する Azure リソースとワークロードをデプロイするには、Azure のアカウントが必要です。 アカウントがない場合、 [ここから無料の Azure 試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。

まだ Azure CLI をインストールしてサブスクリプションに接続していない場合は、「[Azure CLI のインストール](../articles/cli-install-nodejs.md)」を参照して、`azure config mode arm` を実行してモードを `arm` に設定し、`azure login` コマンドを実行して Azure に接続してください。

## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン
次のいずれかの CLI バージョンを使用してタスクを完了できます。

- Azure クラシック CLI - クラシック デプロイ モデルと Resource Manager デプロイ モデル用の CLI (本記事)
- [Azure CLI](../articles/virtual-machines/linux/cli-manage.md) - Resource Manager デプロイ モデル用の次世代 CLI

## <a name="basic-azure-resource-manager-commands-in-azure-classic-cli"></a>Azure クラシック CLI での 基本的な Azure Resource Manager コマンド

この記事では、Azure サブスクリプションでリソース (主に VM) を管理および操作するために Azure クラシック CLI で使用する基本的なコマンドについて説明します。  特定のコマンド ライン スイッチやオプションの詳細については、「`azure <command> <subcommand> --help`」または「`azure help <command> <subcommand>`」と入力して、コマンド ラインのオンライン ヘルプとオプションを使用します。

> [!NOTE]
> これらの例には、リソース マネージャーでの VM のデプロイにおいて一般的に推奨される、テンプレート ベースの操作は含まれていません。 詳細については、[Azure Resource Manager での Azure CLI の使用](../articles/xplat-cli-azure-resource-manager.md)に関するページと「[Azure Resource Manager テンプレートと Azure CLI を使用した仮想マシンのデプロイと管理](../articles/virtual-machines/linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。
> 
> 

| タスク | リソース マネージャー |
| --- | --- |
| 最も基本的な VM の作成 |`azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`<br/><br/>(`azure vm image list` コマンドから `image-urn` を取得します。 例については[こちらの記事](../articles/virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を参照してください。) |
| Linux VM の作成 |`azure  vm create [options] <resource-group> <name> <location> -y "Linux"` |
| Windows VM の作成 |`azure  vm create [options] <resource-group> <name> <location> -y "Windows"` |
| VM の一覧表示 |`azure  vm list [options]` |
| VM に関する情報の取得 |`azure  vm show [options] <resource_group> <name>` |
| VM の起動 |`azure vm start [options] <resource_group> <name>` |
| VM の停止 |`azure vm stop [options] <resource_group> <name>` |
| VM の割り当て解除 |`azure vm deallocate [options] <resource-group> <name>` |
| VM の再起動 |`azure vm restart [options] <resource_group> <name>` |
| VM の削除 |`azure vm delete [options] <resource_group> <name>` |
| VM のキャプチャ |`azure vm capture [options] <resource_group> <name>` |
| ユーザー イメージからの VM の作成 |`azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>` |
| 専用ディスクからの VM の作成 |`azure  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>` |
| VM へのデータ ディスクの追加 |`azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]` |
| VM からのデータ ディスクの削除 |`azure  vm disk detach [options] <resource-group> <vm-name> <lun>` |
| VM への一般的な拡張機能の追加 |`azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>` |
| VM への VM アクセス拡張機能の追加 |`azure vm reset-access [options] <resource-group> <name>` |
| VM への Docker 拡張機能の追加 |`azure  vm docker create [options] <resource-group> <name> <location> <os-type>` |
| VM 拡張機能の削除 |`azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>` |
| VM リソースの使用量の取得 |`azure vm list-usage [options] <location>` |
| 使用可能なすべての VM サイズの取得 |`azure vm sizes [options]` |

## <a name="next-steps"></a>次の手順
* 基本的な VM 管理の範囲を超えた CLI コマンドのその他の例については、 [Azure Resource Manager での Azure CLI の使用](../articles/virtual-machines/azure-cli-arm-commands.md)に関する記事をご覧ください。
