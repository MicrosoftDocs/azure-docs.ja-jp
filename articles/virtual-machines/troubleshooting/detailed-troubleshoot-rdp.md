---
title: Azure でのリモート デスクトップの詳細なトラブルシューティング | Microsoft Docs
description: Azure の Windows 仮想マシンに接続できないリモート デスクトップ エラーをトラブルシューティングする詳細な手順の確認
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: gwallace
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: リモート デスクトップに接続できない, リモート デスクトップのトラブルシューティング, リモート デスクトップで接続できない, リモート デスクトップ エラー, リモート デスクトップのトラブルシューティング, リモート デスクトップの問題
ms.assetid: 9da36f3d-30dd-44af-824b-8ce5ef07e5e0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 56fa1363e9737a2ce06ba5f0215235a84ee70ce6
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709910"
---
# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Azure 上の Windows VM へのリモート デスクトップ接続に関する問題の詳細なトラブルシューティング手順
この記事では、Windows ベースの Azure 仮想マシンの複雑なリモート デスクトップのエラーを診断して修正するための詳細なトラブルシューティング手順を説明します。

> [!IMPORTANT]
> リモート デスクトップのより一般的なエラーを解消するには、先に進む前に、必ず[リモート デスクトップの基本的なトラブルシューティングの記事](troubleshoot-rdp-connection.md)をお読みください。

[リモート デスクトップの基本的なトラブルシューティング ガイド](troubleshoot-rdp-connection.md)で扱われている具体的なエラー メッセージのいずれにも似ていないエラー メッセージがリモート デスクトップで表示されることもあります。 次の手順に従って、リモート デスクトップ (RDP) クライアントが Azure VM 上の RDP サービスに接続できない原因を特定してください。

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムとスタック オーバーフロー フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。 Azure サポートの使用方法の詳細については、「 [Azure Support FAQ (Microsoft Azure サポートに関する FAQ)](https://azure.microsoft.com/support/faq/)」を参照してください。

## <a name="components-of-a-remote-desktop-connection"></a>リモート デスクトップ接続のコンポーネント
RDP 接続には以下のコンポーネントが関連しています。

![](./media/detailed-troubleshoot-rdp/tshootrdp_0.png)

先に進む前に、前回 VM に正常にリモート デスクトップ接続できたとき以降、何を変更したかを思い返してみると、役に立つかもしれません。 例:

* VM、または VM が含まれるクラウド サービスのパブリック IP アドレス (仮想 IP アドレス ( [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)) とも呼ばれる) が変更されました。 この RDP 障害は、DNS 名について登録された *古い IP アドレス* が DNS クライアント キャッシュにまだ入っているために発生した可能性があります。 DNS クライアント キャッシュをフラッシュしてから、もう一度 VM に接続してみてください。 または、新しい VIP に直接接続してみてください。
* Azure ポータルによって生成された接続を使用する代わりに、サード パーティのアプリケーションを使用してリモート デスクトップ接続を管理しています。 アプリケーションの構成にリモート デスクトップのトラフィック用の正しい TCP ポートが含まれていることを確認します。 クラシック仮想マシンのこのポートを確認するには、[Azure Portal](https://portal.azure.com) で VM の [設定]、[エンドポイント] の順にクリックします。

## <a name="preliminary-steps"></a>準備作業
詳細なトラブルシューティングに進む前に、以下の操作を実行します。

* Azure Portal で、仮想マシンの状態に明らかな問題があるかどうかを確認します。
* [基本的なトラブルシューティング ガイドの一般的な RDP エラーに対する応急処置の手順](troubleshoot-rdp-connection.md#quick-troubleshooting-steps)に従います。
* カスタム イメージの場合は、アップロード前に VHD が適切に準備されていることを確認してください。 詳細については、「[Azure にアップロードする Windows VHD または VHDX を準備する](../windows/prepare-for-upload-vhd-image.md)」を参照してください。


これらの手順を実行してから、リモート デスクトップを介して VM に再接続してみてください。

## <a name="detailed-troubleshooting-steps"></a>詳細なトラブルシューティングの手順
リモート デスクトップ クライアントから Azure VM のリモート デスクトップ サービスに接続できないのは、以下のソースに問題があることが原因である可能性があります。

* [リモート デスクトップ クライアント コンピューター](#source-1-remote-desktop-client-computer)
* [組織のイントラネットのエッジ デバイス](#source-2-organization-intranet-edge-device)
* [クラウド サービス エンドポイントとアクセス制御リスト (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [ネットワーク セキュリティ グループ](#source-4-network-security-groups)
* [Windows ベースの Azure VM](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>ソース 1: リモート デスクトップ クライアント コンピューター
コンピューターが他のオンプレミスの Windows ベースのコンピューターにリモート デスクトップ接続を実行できることを確認します。

![](./media/detailed-troubleshoot-rdp/tshootrdp_1.png)

接続できない場合は、そのコンピューターで以下のものがないかどうかを確認してください。

* リモート デスクトップのトラフィックをブロックしているローカル ファイアウォールの設定。
* リモート デスクトップ接続を妨げている、ローカルでインストールされたクライアント プロキシのソフトウェア。
* リモート デスクトップ接続を妨げている、ローカルでインストールされたネットワーク監視ソフトウェア。
* リモート デスクトップ接続を妨げている、トラフィックを監視する、あるいは特定種類のトラフィックを許可または禁止する別の種類のセキュリティ ソフトウェア。

いずれの場合も、該当ソフトウェアを一時的に無効にし、リモート デスクトップ経由でオンプレミス コンピューターへの接続を試してください。 この方法で実際の原因が判明した場合は、ネットワーク管理者と共に、リモート デスクトップ接続が可能になるようにそのソフトウェアの設定を修正してください。

## <a name="source-2-organization-intranet-edge-device"></a>ソース 2: 組織のイントラネットのエッジ デバイス
インターネットに直接接続されているコンピューターで、Azure 仮想マシンへのリモート デスクトップ接続を実行できることを確認します。

![](./media/detailed-troubleshoot-rdp/tshootrdp_2.png)

インターネットに直接接続されているコンピューターがない場合は、リソース グループまたはクラウド サービス内に新しい Azure 仮想マシンを作成してテストします。 詳細については、「[Azure 上で Windows を実行する仮想マシンの作成](../virtual-machines-windows-hero-tutorial.md)」を参照してください。 仮想マシンとリソース グループまたはクラウド サービスは、テスト後に削除できます。

インターネットに直接接続されているコンピューターとのリモート デスクトップ接続を作成できるのなら、組織のイントラネットのエッジ デバイスで以下を確認してください。

* インターネットへの HTTPS 接続をブロックしている内部ファイアウォール。
* リモート デスクトップ接続を妨げているプロキシ サーバー。
* リモート デスクトップ接続を妨げている、エッジ ネットワーク内のデバイスで実行されている侵入検出またはネットワーク監視のソフトウェア。

ネットワーク管理者とともに、インターネットへの HTTPS ベースのリモート デスクトップ接続が可能になるように、組織のイントラネットのエッジ デバイスの設定を修正してください。

## <a name="source-3-cloud-service-endpoint-and-acl"></a>ソース 3: クラウド サービス エンドポイントと ACL
クラシック デプロイ モデルを使用して作成された VM の場合は、同じクラウド サービスまたは仮想ネットワーク内にある別の Azure VM が自分の Azure VM にリモート デスクトップ接続できることを確認してください。

![](./media/detailed-troubleshoot-rdp/tshootrdp_3.png)

> [!NOTE]
> Resource Manager で作成された仮想マシンの場合は、「[ソース 4:ネットワーク セキュリティ グループ](#source-4-network-security-groups)」に進んでください。

同じクラウド サービスまたは仮想ネットワーク内に別の仮想マシンがない場合、作成します。 「[Azure 上で Windows を実行する仮想マシンの作成](../virtual-machines-windows-hero-tutorial.md)」の手順に従ってください。 テストが完了した後は、テスト用の仮想マシンを削除します。

同じクラウド サービスまたは仮想ネットワーク内の仮想マシンにリモート デスクトップ経由で接続できる場合は、以下の設定を確認してください。

* ターゲット VM でのリモート デスクトップ トラフィック用のエンドポイント構成: エンドポイントのプライベート TCP ポートは、VM のリモート デスクトップ サービスがリッスンしている TCP ポートと一致する必要があります (既定では 3389)。
* ターゲット VM でのリモート デスクトップ トラフィック エンドポイント用の ACL: ACL を使用すると、発信元 IP アドレスに基づいて、インターネットからの受信トラフィックの許可または拒否を指定できます。 ACL が正しく構成されていないと、そのエンドポイントへのリモート デスクトップの受信トラフィックを受け取れない場合があります。 プロキシまたは他のエッジ サーバーのパブリック IP アドレスからの受信トラフィックが ACL で許可されていることを確認してください。 詳細については、「 [ネットワーク アクセス制御リスト (ACL) の概要](../../virtual-network/virtual-networks-acl.md)

問題の原因がエンドポイントかどうかを確認するには、現在使用されているエンドポイントを削除してから新しいエンドポイントを作成します。このとき、外部ポート番号の範囲 49152 ～ 65535 からランダムなポート番号を選択します。 詳細については、[仮想マシンに対してエンドポイントを設定する方法](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)に関するページを参照してください。

## <a name="source-4-network-security-groups"></a>ソース 4: ネットワーク セキュリティ グループ
ネットワーク セキュリティ グループでは、許可された受信トラフィックと送信トラフィックをより細かく制御できます。 Azure 仮想ネットワーク内のサブネットまたはクラウド サービスの全体に適用されるルールを作成することができます。

[IP フロー検証](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md)を使用して、ネットワーク セキュリティ グループ規則によって、仮想マシンから送受信されるトラフィックがブロックされていないかどうかを確認します。 有効なセキュリティ グループ規則を確認して、RDP ポート (既定では 3389) に対して受信 "許可" NSG 規則が存在し、優先されていることを確認することもできます。 詳細については、「[有効なセキュリティ規則を使用した VM トラフィック フローのトラブルシューティング](../../virtual-network/diagnose-network-traffic-filter-problem.md)」を参照してください。

## <a name="source-5-windows-based-azure-vm"></a>ソース 5: Windows ベースの Azure VM
![](./media/detailed-troubleshoot-rdp/tshootrdp_5.png)

[この記事](../windows/reset-rdp.md)の手順に従ってください。 この記事では、仮想マシン上のリモート デスクトップ サービスを次のようにリセットします。

* 「リモート デスクトップ」 の Windows ファイアウォールの既定ルール (TCP ポート 3389) が有効になる。
* HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections レジストリ値が 0 に設定されるため、リモート デスクトップ接続が有効になる。

もう一度、コンピューターから接続を試みてください。 まだリモート デスクトップ経由で接続できない場合は、以下の問題の可能性を調べてください。

* ターゲット VM 上でリモート デスクトップ サービスが実行されていない。
* リモート デスクトップ サービスが TCP ポート 3389 をリッスンしていない。
* Windows ファイアウォールまたは他のローカルのファイアウォールで、リモート デスクトップのトラフィックを妨げている送信ルールが設定されている。
* Azure 仮想マシンで実行されている侵入検出ソフトウェアまたは監視ソフトウェアが、リモート デスクトップ接続を妨げている。

クラシック デプロイ モデルを使用して作成された VM の場合は、Azure 仮想マシンへのリモート Azure PowerShell セッションを使用できます。 最初に、仮想マシンをホストするクラウド サービスの証明書をインストールする必要があります。 [Azure Virtual Machines への安全なリモート PowerShell アクセスの構成](https://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) に関するページを参照し、 **InstallWinRMCertAzureVM.ps1** スクリプト ファイルを、ローカル コンピューター上にダウンロードします。

次に、まだ Azure PowerShell がインストールされていなければ、インストールします。 「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/overview)」を参照してください。

次に、Azure PowerShell のコマンド プロンプトを開き、現在のフォルダーを、 **InstallWinRMCertAzureVM.ps1** スクリプト ファイルのある場所に変更します。 Azure PowerShell スクリプトを実行するには、適切な実行ポリシーを設定する必要があります。 **Get-executionpolicy** コマンドを実行して、現在のポリシー レベルを決定します。 適切なレベルの設定方法の詳細については、「 [Set-executionpolicy](https://technet.microsoft.com/library/hh849812.aspx)」 を参照してください。

次に、Azure のサブスクリプション名、クラウド サービス名、および仮想マシン名を入力してから (< および > 文字を削除する)、これらのコマンドを実行します。

```powershell
$subscr="<Name of your Azure subscription>"
$serviceName="<Name of the cloud service that contains the target virtual machine>"
$vmName="<Name of the target virtual machine>"
.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName
```

正しいサブスクリプション名は、*Get-AzureSubscription* コマンドで表示される **SubscriptionName** プロパティから取得できます。 仮想マシンのクラウド サービス名は、*Get-AzureVM* コマンドに表示される **ServiceName** 列から取得できます。

新しい証明書があることを確認します。 現在のユーザー用の証明書スナップインを開き、**Trusted Root Certification Authorities\Certificates** フォルダーを調べます。 クラウド サービスの DNS 名が付けられた証明書が、[発行先] 列に表示されます (例: cloudservice4testing.cloudapp.net)、、

次に以下のコマンドを使用して、リモート Azure PowerShell セッションを開始します。

```powershell
$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
$creds = Get-Credential
Enter-PSSession -ConnectionUri $uri -Credential $creds
```

有効な管理者の資格情報を入力すると、Azure PowerShell プロンプトで次のように表示されます。

```powershell
[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>
```

このメッセージの最初の部分は、ターゲット VM を含むクラウド サービス名です。これは、"cloudservice4testing.cloudapp.net" とは異なる場合もあります。 これでこのクラウド サービスに対する Azure PowerShell コマンドを発行して、前述したその他の問題を調査して構成を修正することができます。

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>リモート デスクトップ サービスでリッスンする TCP ポートの手動修正
リモートの Azure PowerShell セッション プロンプトで、このコマンドを実行します。

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

PortNumber プロパティは、現在のポート番号を表示します。 必要に応じて、次のコマンドを使用してリモート デスクトップのポート番号を既定値 (3389) に戻してください。

```powershell
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389
```

次のコマンドを使用して、ポートが 3389 に変更されたことを確認します。

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

次のコマンドを使用して、リモートの Azure PowerShell セッションを終了します。

```powershell
Exit-PSSession
```

Azure VM のリモート デスクトップのエンドポイントでも、内部ポートとして TCP ポート 3398 が使用されていることを確認します。 Azure VM を再起動し、もう一度リモート デスクトップ接続を試してみてください。

## <a name="additional-resources"></a>その他のリソース
[Windows 仮想マシンのパスワードまたはリモート デスクトップ サービスをリセットする方法](../windows/reset-rdp.md)

[Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview)

[Linux ベースの Azure 仮想マシンに対する Secure Shell (SSH) 接続のトラブルシューティング](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Azure の仮想マシンで実行されているアプリケーションへのアクセスに関するトラブルシューティング](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

