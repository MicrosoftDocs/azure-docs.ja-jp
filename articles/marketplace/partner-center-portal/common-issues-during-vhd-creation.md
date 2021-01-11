---
title: VHD 作成における一般的な問題 (FAQ)
description: 仮想ハード ディスク (VHD) を作成するときの一般的な問題についてよく寄せられる質問。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/09/2020
ms.openlocfilehash: f4d30cdb931d6523eba3aac003caeee38a8c024d
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653530"
---
# <a name="common-issues-during-vhd-creation"></a>VHD 作成における一般的な問題

これらのよく寄せられる質問 (FAQ) では、Azure 仮想マシン オファーの仮想ハード ディスク (VHD) を作成するときに発生する可能性のある一般的な問題について説明します。

## <a name="how-do-i-create-a-vm-from-the-azure-portal-using-a-vhd-in-premium-storage"></a>Premium Storage の VHD を使用して Azure portal から VM を作成するにはどうすればよいですか?

Azure Marketplace は現在、マネージド ストレージ上のイメージまたは Azure Premium Storage からの VM オファーの作成をサポートしていません。 詳細については、「[Azure Managed Disks の概要](../../virtual-machines/managed-disks-overview.md)」を参照してください。

## <a name="can-i-use-generation-2-vms-for-offers"></a>オファーに第 2 世代の VM を使用できますか?

いいえ、第 1 世代の VHD のみがサポートされています。 ただし、現在、Microsoft Azure Platform チームと協力して、第 2 世代の VM のサポートを調査中です。 詳細については、「[Hyper-V では、第 1 世代と第 2 世代の仮想マシンのどちらを作成するべきですか?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)」を参照してください。

## <a name="how-do-i-change-the-name-of-the-host"></a>ホストの名前を変更するにはどうすればよいですか?

実行できません。 VM が作成されると、ユーザー (所有者を含む) はホスト名を更新できません。

## <a name="how-do-i-reset-the-remote-desktop-service-or-its-sign-in-password"></a>リモート デスクトップ サービスまたはそのサインイン パスワードをリセットするにはどうすればよいですか?

次の記事で、Windows および Linux ベースの VM に対する RDS のリセットを実行する方法について説明しています。

* [Windows VM でリモート デスクトップ サービスまたはそのログイン パスワードをリセットする方法](/azure/virtual-machines/troubleshooting/reset-rdp)
* [VMAccess 拡張機能を使用して、Linux VM のパスワードまたは SSH キーをリセットし、SSH 構成を修正し、ディスクの整合性チェックを実行する方法](/azure/virtual-machines/extensions/vmaccess)

## <a name="how-do-i-generate-new-ssh-certificates"></a>新しい SSH 証明書を生成するにはどうすればよいですか?

証明書の生成については、[「Azure VM イメージの認定](https://aks.ms/CertifyVMimage)」で説明されています。

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>VM で使用するよう仮想プライベート ネットワーク (VPN) を構成するにはどうすればよいですか?

Azure Resource Manager デプロイ モデルを使用している場合、3 つのオプションがあります。

* [Azure portal を使用してルートベースの VPN ゲートウェイを作成する](../../vpn-gateway/create-routebased-vpn-gateway-portal.md)
* [Azure PowerShell を使用してルートベースの VPN ゲートウェイを作成する](../../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
* [CLI を使用してルートベースの VPN ゲートウェイを作成する](../../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Azure ベースの VM で Microsoft サーバー ソフトウェアを実行するための Microsoft サポート ポリシーはどのようなものですか?

詳細については 「[Microsoft Azure 仮想マシンのマイクロソフト サーバー ソフトウェアのサポート](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)」を参照してください。

## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>仮想マシンには一意識別子が関連付けられていますか?

はい、Azure でホストされている場合は関連付けられています。 Azure では、作成される新しい VM リソースそれぞれに [Azure 仮想マシン一意 ID](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/) という一意識別子が割り当てられます。 詳細については、「Azure 仮想マシン一意 ID」をご覧ください。 この識別子は、[List API](https://docs.microsoft.com/rest/api/compute/virtualmachines/list) を使用して取得することもできます。

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>VM で、スタートアップ タスクのカスタム スクリプト拡張機能を管理するにはどうすればよいですか?

Azure PowerShell モジュールと Azure Resource Manager テンプレートを使用したカスタム スクリプト拡張機能の使用方法と、Windows システムでのトラブルシューティング手順の詳細については、「[Windows でのカスタムのスクリプト拡張機能](/azure/virtual-machines/extensions/custom-script-windows)」を参照してください。

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>32 ビットのアプリケーションやサービスは Azure Marketplace でサポートされていますか?

通常はできません。 サポートされるオペレーティング システムと Azure VM の標準サービスはすべて 64 ビットです。 ほとんどの 64 ビット オペレーティング システムは、下位互換性のために 32 ビット バージョンのアプリケーションをサポートしていますが、VM ソリューションの一部として 32 ビット アプリケーションを使用することはサポートされておらず、非常にお勧めできません。 アプリケーションを 64 ビットのプロジェクトとして再作成してください。

詳細と例については、次の記事をご覧ください。

* [32 ビット アプリケーションの実行](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
* [Azure 仮想マシンでの 32 ビット オペレーティング システムのサポート](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
* [Microsoft Azure 仮想マシンのマイクロソフト サーバー ソフトウェアのサポート](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>エラー:VHD は、既にリソースとしてイメージ リポジトリに登録されています

VHD からイメージを作成しようとするたびに、Azure PowerShell で "VHD は、既にリソースとしてイメージ リポジトリに登録されています" というエラーが発生します。 これまでイメージを作成したことはなく、Azure でこの名前のイメージは見つかっていません。 解決するにはどうすればよいですか?

この問題は通常、ロックがある VHD から VM を作成した場合に発生します。 この VHD から割り当てられた VM がないことを確認し、操作を再試行してください。 それでもこの問題が解決しない場合は、サポート チケットを開いてください。 [パートナー センターのサポート](support.md)に関する記事を参照してください。
