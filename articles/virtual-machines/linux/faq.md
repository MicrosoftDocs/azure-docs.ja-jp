---
title: Azure の Linux VM に関してよく寄せられる質問 | Microsoft Docs
description: Resource Manager モデルで作成された Linux 仮想マシンについてよく寄せられる質問への回答を示します。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-management
ms.assetid: 3648e09c-1115-4818-93c6-688d7a54a353
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: ae636917a32aae823d0d31b415f9ef9ac16e9c2f
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667784"
---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Linux 仮想マシンについてのよく寄せられる質問
この記事では、Resource Manager デプロイ モデルを使用して Azure で作成された Linux 仮想マシンについてよく寄せられる質問に回答します。 このトピックの Windows バージョンについては、「[Windows Virtual Machines についてのよく寄せられる質問](../windows/faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。

## <a name="what-can-i-run-on-an-azure-vm"></a>Azure VM では何を実行できますか。
すべてのサブスクライバーは、Azure 仮想マシンでサーバー ソフトウェアを実行できます。 詳細については、「[Azure での動作保証済み Linux ディストリビューション](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>仮想マシンではどれくらいのストレージ容量を使用できますか。
各データ ディスクで最大 32,767 GiB を使用できます。 使用できるデータ ディスクの数は、仮想マシンのサイズによって決まります。 詳細については、「 [仮想マシンのサイズ](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

Azure Managed Disks は、Azure Virtual Machines でデータの永続的な記憶域として使用できる、お勧めのディスク ストレージ サービスです。 各仮想マシンで複数の管理ディスクを使用することができます。 Managed Disks には、次の 2 種類の耐久性ストレージ オプションがあります:Premium Managed Disks と Standard Managed Disks。 価格情報については、「[Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks)」を参照してください。

Azure のストレージ アカウントでは、オペレーティング システム ディスクと任意のデータ ディスクのストレージも利用できます。 各ディスクは、実際には .vhd ファイルであり、ページ BLOB として保存されます。 価格の詳細については、「 [Azure Storage の価格](https://azure.microsoft.com/pricing/details/storage/)」を参照してください。

## <a name="how-can-i-access-my-virtual-machine"></a>仮想マシンへのアクセス方法を教えてください。
Secure Shell (SSH) を使用して、仮想マシンにサインオンするためのリモート接続を確立します。 [Windows から](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)接続する手順、または [Linux および Mac から](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)接続する手順を参照します。 SSH では、既定で最大 10 のコンカレント接続が可能です。 この接続数は構成ファイルを編集することで増やすことができます。

問題がある場合は、「[Azure Linux VM に対する SSH 接続の失敗、エラー、拒否のトラブルシューティング](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>データの格納時に一時ディスク (/dev/sdb1) を使用できますか。
データの格納に一時ディスク (/dev/sdb1) を使用しないでください。 一時ディスクは一時的なストレージでしかなく、 データ損失の発生時にデータを復旧できない恐れがあります。

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>既存の Azure VM をコピーまたは複製できますか。
はい。 詳細については、「[Resource Manager デプロイ モデルで Linux 仮想マシンのコピーを作成する方法](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Azure Resource Manager でカナダ中部およびカナダ東部のリージョンが表示されない理由を教えてください。
カナダ中部およびカナダ東部の 2 つの新しいリージョンは、既存の Azure サブスクリプションで仮想マシンを作成した場合、自動的には登録されません。 Azure Resource Manager を使用してこれら以外の任意のリージョンに Azure ポータルから仮想マシンをデプロイすると、この登録は自動的に行われます。 その他の任意の Azure リージョンに仮想マシンがデプロイされると、新しいリージョンを後続の仮想マシンで使用できるようになります。

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>VM の作成後、NIC を VM に追加できますか。
はい、できるようになりました。 最初に VM を停止して割り当てを解除する必要があります。 その後、NIC を追加または削除 (VM 上の最後の NIC でない場合) できます。 

## <a name="are-there-any-computer-name-requirements"></a>コンピューター名の要件はありますか。
はい。 コンピューター名は最大 64 文字の長さまで指定できます。 リソースの名前付けの詳細については、[名前付け規則と制約事項](/azure/architecture/best-practices/naming-conventions)に関する記事を参照してください。

## <a name="are-there-any-resource-group-name-requirements"></a>リソース グループの名前に関する要件はありますか。
はい。 リソース グループ名は最大 90 文字の長さまで指定できます。 リソース グループの名前付けの詳細については、[名前付け規則と制約事項](/azure/architecture/best-practices/naming-conventions)に関する記事を参照してください。

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>VM を作成する際のユーザー名の要件は何ですか。

ユーザー名は 1 ～ 32 文字の長さにする必要があります。

次のユーザー名を指定することはできません。

| | | | |
|-----------------|-----------|--------------------|----------|
| `administrator` | `admin`   | `user`             | `user1`  |
| `test`          | `user2`   | `test1`            | `user3`  |
| `admin1`        | `1`       | `123`              | `a`      |
| `actuser`       | `adm`     | `admin2`           | `aspnet` |
| `backup`        | `console` | `david`            | `guest`  |
| `john`          | `owner`   | `root`             | `server` |
| `sql`           | `support` | `support_388945a0` | `sys`    |
| `test2`         | `test3`   | `user4`            | `user5`  |
| `video`         |

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>VM を作成する際のパスワードの要件は何ですか。

使うツールによって、パスワードの長さの要件が異なります。
 - ポータル - 12 から 72 文字の間
 - PowerShell - 8 から 123 文字の間
 - CLI - 12 から 123 文字の間
 

また、パスワードは次の 4 つの複雑性要件のうち 3 つを満たしている必要があります。

* 小文字を含む
* 大文字を含む
* 数字を含む
* 特殊文字を含む ([\W_] に一致する正規表現)

次のパスワードを指定することはできません。

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@$$w0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">Pa$$word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Password!</td>
        <td style="text-align:center">Password1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">iloveyou!</td>
    </tr>
</table>
