---
title: Azure Marketplace の VHD 作成における一般的な問題 (FAQ)
description: VHD 作成と関連する問題に関してよく寄せられる質問。
services: Azure Marketplace
author: HannibalSII
ms.service: marketplace
ms.topic: article
ms.date: 10/02/2018
ms.author: hascipio
ms.reviewer: v-divte; v-miclar
ms.openlocfilehash: bb7e62138dbefdd8ff4933ef10602986a5da2bf6
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67874996"
---
# <a name="common-issues-during-vhd-creation-faq"></a>VHD 作成における一般的な問題 (FAQ)

次に挙げるよく寄せられる質問 (FAQ) では、VM オファーの仮想ハード ディスク (VHD) と仮想マシン (VM) の作成中に発生する一般的な問題について説明します。 

## <a name="how-do-you-create-a-vm-from-the-azure-portal-using-the-vhd-that-is-uploaded-to-premium-storage"></a>Premium Storage にアップロードされる VHD を使用して、Azure portal から VM を作成するにはどうすればよいですか?

Azure Marketplace は現在、マネージド ストレージ上に存在するイメージまたは Azure Premium Storage からの VM オファーの作成をサポートしていません。  これらのストレージ オプションについて詳しくは、「[Azure Managed Disks の概要](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)」をご覧ください。


## <a name="can-you-use-generation-2-vms-for-offers"></a>オファーに第 2 世代の VM を使用できますか?

いいえ、第 1 世代の VHD のみがサポートされています。  ただし、現在、Microsoft Azure Platform チームと協力して、第 2 世代の VM のサポートを調査中です。  違いの詳細については、「[Hyper-V で第 1 世代または第 2 世代の仮想マシンを作成する必要はありますか?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)」を参照してください。


## <a name="how-do-you-change-the-name-of-the-host"></a>ホストの名前を変更するにはどうすればよいですか?

変更できません。  VM を作成した後は、ユーザー (所有者を含む) はホストの名前を更新できません。


## <a name="how-do-you-reset-the-remote-desktop-service-or-its-sign-in-password"></a>リモート デスクトップ サービスまたはそのサインイン パスワードをリセットするにはどうすればよいですか?

次の記事では、Windows ベースの VM と Linux ベースの VM の RDS のリセットを実行する方法について説明します。   

- [Windows VM でリモート デスクトップ サービスまたはそのログイン パスワードをリセットする方法](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
- [VMAccess 拡張機能を使用して、Linux VM のパスワードまたは SSH キーをリセットし、SSH 構成を修正し、ディスクの整合性チェックを実行する方法](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)


## <a name="how-do-you-generate-new-ssh-certificates"></a>新しい SSH 証明書を生成するにはどうすればよいですか?

証明書の生成については、後続のセクション「[VM オファーの技術資産の作成](./cpp-create-technical-assets.md)」の「[VM イメージの Shared Access Signature URI の取得](./cpp-get-sas-uri.md)」の記事に説明されています。


## <a name="how-do-you-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>VM で使用するよう仮想プライベート ネットワーク (VPN) を構成するにはどうすればよいですか?

Azure Resource Manager デプロイ モデルを使用している場合、VPN を設定する 3 つの一般的なオプションがあります。
- [Azure portal を使用してルートベースの VPN ゲートウェイを作成する](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
- [PowerShell を使用してルートベースの VPN ゲートウェイを作成する](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
- [CLI を使用してルートベースの VPN ゲートウェイを作成する](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)


## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Azure ベースの VM で Microsoft サーバー ソフトウェアを実行するための Microsoft サポート ポリシーはどのようなものですか?

これらのサポート ポリシーの詳細については、「[Microsoft Azure 仮想マシンのマイクロソフト サーバー ソフトウェアのサポート](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)」を参照してください。


## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>仮想マシンには一意識別子が関連付けられていますか?

はい、Azure でホストされている場合は関連付けられています。  Azure は、作成される新しい VM リソースごとに Azure 仮想マシン一意 ID という一意識別子を割り当てます。  詳細については、ブログ記事「[Azure 仮想マシン一意 ID](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/)」をお読みください。  [List API](https://docs.microsoft.com/rest/api/compute/virtualmachines/list) を使用して、プログラムによりこの識別子を取得することもできます。


## <a name="in-a-vm-how-do-you-manage-the-custom-script-extension-in-the-startup-task"></a>VM で、スタートアップ タスクのカスタム スクリプト拡張機能を管理するにはどうすればよいですか?

次の記事では、Azure PowerShell モジュールと Azure Resource Manager テンプレートを使用したカスタム スクリプト拡張機能の使用方法について詳しく説明します。また、Windows システムでのトラブルシューティング手順についても詳しく説明します。[Windows でのカスタムのスクリプト拡張機能](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)


## <a name="are-32-bit-applications-or-services-supported-in-the-azure-marketplace"></a>32 ビット アプリケーションやサービスは Azure Marketplace でサポートされていますか?

通常はできません。  サポートされるオペレーティング システムと Azure VM の標準サービスはすべて 64 ビットです。  ただし、技術的な観点でいえば、ほとんどの 64 ビット オペレーティング システムが、下位互換性を保つため、32 ビット バージョンのアプリケーションの実行をサポートしています。  ただし、VM ソリューションの一部としての 32 ビット アプリケーションの使用はサポートされていないため、使用しないことを*強くお勧めします*。  代わりに、64 ビットのプロジェクトとして、アプリケーションを再コンパイルします。

詳細については、次の記事を参照してください。
- [32 ビット アプリケーションの実行](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
- [Azure 仮想マシンでの 32 ビット オペレーティング システムのサポート](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Microsoft Azure 仮想マシンのマイクロソフト サーバー ソフトウェアのサポート](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)


## <a name="every-time-i-try-to-create-an-image-from-my-vhds-i-get-the-error-vhd-is-already-registered-with-image-repository-as-the-resource-in-powershell-i-did-not-create-any-image-before-nor-did-i-find-any-image-with-this-name-in-azure-how-do-i-resolve-this-issue"></a>VHD からイメージを作成しようとするたびに、PowerShell でエラー `.VHD is already registered with image repository as the resource` が発生します。 これまでイメージを作成したことはなく、Azure で同じ名前のイメージは見つかっていません。 この問題を解決するにはどうすればよいですか?

この問題は通常、ロックがある VHD からユーザーが VM をプロビジョニングした場合に発生します。  この VHD から割り当てられた VM がないことを確認し、操作を再試行してください。  この問題が解決しない場合、[クラウド パートナー ポータルのサポート](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-support-for-cloud-partner-portal)の説明に従って、サポート チケットを開きます。 

