---
title: Azure の Windows VM に関する FAQ | Microsoft Docs
description: Resource Manager モデルで作成された Windows 仮想マシンについてよく寄せられる質問への回答を示します。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-management
ms.assetid: 757da816-a050-4889-a010-6f75d7978eb7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: 69c79db937d70bcc8ae834bf0bdf8f1930bed3c9
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722746"
---
# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Windows Virtual Machines についてのよく寄せられる質問
この記事では、Resource Manager デプロイ モデルを使用して Azure で作成された Windows 仮想マシンについてよく寄せられる質問に回答します。 このトピックの Linux バージョンについては、「[Linux 仮想マシンについてのよく寄せられる質問](../linux/faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。

## <a name="what-can-i-run-on-an-azure-vm"></a>Azure VM では何を実行できますか。
すべてのサブスクライバーは、Azure 仮想マシンでサーバー ソフトウェアを実行できます。 Microsoft サーバー ソフトウェアを Azure で実行するためのサポートポリシーの詳細については、「[Microsoft Azure 仮想マシンのマイクロソフト サーバー ソフトウェアのサポート](https://support.microsoft.com/kb/2721672)」をご覧ください。

MSDN Azure 特典のサブスクライバーと MSDN 開発テスト用従量課金制プラン (開発およびテスト用) のサブスクライバーを対象に、特定のバージョンの Windows 7、Windows 8.1、および Windows 10 が利用可能となっています。 詳細については、「 [Windows Client images for MSDN subscribers](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/)」を参照してください 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>仮想マシンではどれくらいのストレージ容量を使用できますか。
各データ ディスクで最大 32,767 GiB を使用できます。 使用できるデータ ディスクの数は、仮想マシンのサイズによって決まります。 詳細については、「 [仮想マシンのサイズ](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。

Azure Managed Disks は、Azure Virtual Machines でデータの永続的な記憶域として使用できる、お勧めのディスク ストレージ サービスです。 各仮想マシンで複数の管理ディスクを使用することができます。 Managed Disks には、次の 2 種類の耐久性ストレージ オプションがあります:Premium Managed Disks と Standard Managed Disks。 価格情報については、「[Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks)」を参照してください。

Azure のストレージ アカウントでは、オペレーティング システム ディスクと任意のデータ ディスクのストレージも利用できます。 各ディスクは、実際には .vhd ファイルであり、ページ BLOB として保存されます。 価格の詳細については、「 [Azure Storage の価格](https://azure.microsoft.com/pricing/details/storage/)」を参照してください。

## <a name="how-can-i-access-my-virtual-machine"></a>仮想マシンへのアクセス方法を教えてください。
Windows VM 用のリモート デスクトップ接続 (RDP) を使用してリモート接続を確立します。 手順については、「[Windows が実行されている Azure 仮想マシンに接続してサインオンする方法](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。 最大 2 つのコンカレント接続がサポートされます (サーバーがリモート デスクトップ サービスのセッション ホストとして構成されている場合を除く)。  

リモート デスクトップで問題が発生した場合は、 [Windows ベースの Azure Virtual Machine へのリモート デスクトップ接続に関するトラブルシューティング](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページをご覧ください。 

Hyper-V に慣れている場合は、VMConnect と同様のツールを検討されるかもしれませんが、 Azure では、仮想マシンへのコンソール アクセスがサポートされていないため、類似のツールは提供されていません。

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>一時ディスク (既定では D: ドライブ) をデータを格納するために使用できますか。
データの格納に一時ディスクを使用しないでください。 一時ディスクは一時的なストレージであるため、データ損失の発生時にデータを復旧できない恐れがあります。 データ損失は、仮想マシンを別のホストに移動するときに発生する可能性があります。 仮想マシンが移動される理由としては、ホストの更新、仮想マシンのサイズ変更、ホスト上のハードウェア障害などが挙げられます。

アプリケーションで D: ドライブ文字を使用する必要がある場合は、一時ディスクで D: 以外を使用するようにドライブ文字を再割り当てできます。 手順については、「 [Windows 一時ディスクのドライブ文字を変更する方法](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」を参照してください。


## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>一時ディスクのドライブ文字を変更する方法について教えてください。
ページ ファイルを移動してドライブ文字を再割り当てすることでドライブ文字を変更することができますが、正しい順序で手順を実行するよう注意する必要があります。 手順については、「 [Windows 一時ディスクのドライブ文字を変更する方法](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」を参照してください。

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>既存の VM を可用性セットに追加できますか。
いいえ。 VM を可用性セットの一部にする場合は、VM を作成するときにセットに追加する必要があります。 作成後に VM を可用性セットに追加する方法は現在ありません。

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>仮想マシンを Azure にアップロードすることができますか。
はい。 手順については、[オンプレミス VM を Azure に移行する](on-prem-to-azure.md)方法に関するページを参照してください。

## <a name="can-i-resize-the-os-disk"></a>OS ディスクのサイズを変更できますか。
はい。 手順については、「 [Azure リソース グループで仮想マシンの OS ドライブを展開する方法](expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>既存の Azure VM をコピーまたは複製できますか。
はい。 管理対象イメージを利用し、仮想マシンのイメージを作成し、そのイメージを利用して複数の新しい VM を構築できます。 手順については、[VM のカスタム イメージを作成する](tutorial-custom-images.md)方法に関するページを参照してください。

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Azure Resource Manager でカナダ中部およびカナダ東部のリージョンが表示されない理由を教えてください。

カナダ中部およびカナダ東部の 2 つの新しいリージョンは、既存の Azure サブスクリプションで仮想マシンを作成した場合、自動的には登録されません。 Azure Resource Manager を使用してこれら以外の任意のリージョンに Azure ポータルから仮想マシンをデプロイすると、この登録は自動的に行われます。 その他の任意の Azure リージョンに仮想マシンがデプロイされると、新しいリージョンを後続の仮想マシンで使用できるようになります。

## <a name="does-azure-support-linux-vms"></a>Azure では Linux VM がサポートされていますか?
はい。 Linux VM をすばやく作成して試してみるには、「 [ポータルを使用して Azure に Linux VM を作成する](../linux/quick-create-portal.md)」を参照してください。

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>VM の作成後、NIC を VM に追加できますか。
はい、できるようになりました。 最初に VM を停止して割り当てを解除する必要があります。 その後、NIC を追加または削除 (VM 上の最後の NIC でない場合) できます。 

## <a name="are-there-any-computer-name-requirements"></a>コンピューター名の要件はありますか。
はい。 コンピューター名は最大 15 文字の長さまで指定できます。 リソースの名前付けの詳細については、[名前付け規則と制約事項](/azure/architecture/best-practices/naming-conventions#compute)に関する記事を参照してください。

## <a name="are-there-any-resource-group-name-requirements"></a>リソース グループの名前に関する要件はありますか。
はい。 リソース グループ名は最大 90 文字の長さまで指定できます。 リソース グループの名前付けの詳細については、[名前付け規則と制約事項](/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions)に関する記事を参照してください。

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>VM を作成する際のユーザー名の要件は何ですか。

ユーザー名は最大 20 文字の長さまで指定できますが、末尾を句点 (".") にすることはできません 。 

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


## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>VM を作成する際のパスワードの要件は何ですか。

使うツールによって、パスワードの長さの要件が異なります。
 - ポータル - 12 から 72 文字の間
 - PowerShell - 8 から 123 文字の間
 - CLI - 12 から 123 文字の間

* 小文字を含む
* 大文字を含む
* 数字を含む
* 特殊文字を含む ([\W_] に一致する正規表現)

次のパスワードを指定することはできません。

<table>
    <tr>
        <td>abc@123</td>
        <td>iloveyou!</td>
        <td>P@$$w0rd</td>
        <td>P@ssw0rd</td>
        <td>P@ssword123</td>
    </tr>
    <tr>
        <td>Pa$$word</td>
        <td>pass@word1</td>
        <td>Password!</td>
        <td>Password1</td>
        <td>Password22</td>
    </tr>
</table>
