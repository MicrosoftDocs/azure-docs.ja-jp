---
title: Azure での Windows 仮想マシンのライセンス認証に関する問題のトラブルシューティング | Microsoft Docs
description: Azure での Windows 仮想マシンのライセンス認証に関する問題を解決するためのトラブルシューティングの手順を示します
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/15/2018
ms.author: genli
ms.openlocfilehash: bc058cb3f27545b9e4ad8ef1062ca4d2fa4c9fa8
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155145"
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Azure Windows 仮想マシンのライセンス認証に関する問題のトラブルシューティング

カスタム イメージから作成された Azure Windows 仮想マシン (VM) をライセンス認証するときに問題が発生する場合は、問題のトラブルシューティングを行うために、このドキュメントに記載されている情報を使用できます。 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>Azure Virtual Machines の Windows 製品ライセンス認証の Azure KMS エンドポイントについて

Azure では、KMS ライセンス認証に対して、VM が存在するクラウド リージョンに応じたエンドポイント使用します。 このトラブルシューティング ガイドを使用する際は、ご利用のリージョンに適用される適切な KMS エンドポイントを使用してください。

* Azure パブリック クラウド リージョン: kms.core.windows.net:1688
* Azure China 21Vianet 国内クラウド リージョン: kms.core.chinacloudapi.cn:1688
* Azure Germany 国内クラウド リージョン: kms.core.cloudapi.de:1688
* Azure US Gov 国内クラウド リージョン: kms.core.usgovcloudapi.net:1688

## <a name="symptom"></a>症状

Azure Windows VM をライセンス認証しようとすると、次の例のようなエラー メッセージが表示されます。

**エラー:0xC004F074 ソフトウェア ライセンス サービスにより、コンピューターのライセンス認証ができなかったことが報告されました。キー管理サービス (KMS) に接続できませんでした。詳細については、アプリケーション イベント ログを参照してください。**

## <a name="cause"></a>原因

一般に、Azure VM のライセンス認証の問題は、Windows VM が適切な KMS クライアント セットアップ キーを使用して構成されていないか、Windows VM に Azure KMS サービス (kms.core.windows.net、ポート 1688) に対する接続の問題がある場合に発生します。 

## <a name="solution"></a>解決策

>[!NOTE]
>サイト間 VPN と強制トンネリングを使用している場合は、「[Use Azure custom routes to enable KMS activation with forced tunneling (強制トンネリングで KMS ライセンス認証を有効にするために Azure カスタム ルートを使用する)](https://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx)」を参照してください。 
>
>ExpressRoute を使用していて、既定のルートを公開している場合は、「[Azure VM may fail to activate over ExpressRoute (Azure VM が ExpressRoute 経由でのライセンス認証に失敗する場合がある)](https://blogs.msdn.com/b/mast/archive/2015/12/01/azure-vm-may-fail-to-activate-over-expressroute.aspx)」を参照してください。

### <a name="step-1-configure-the-appropriate-kms-client-setup-key"></a>手順 1: 適切な KMS クライアント セットアップ キーを構成する

カスタム イメージから作成された VM の場合、適切な KMS クライアント セットアップ キーを構成する必要があります。

1. 管理者特権でのコマンド プロンプトで、**slmgr.vbs /dlv** を実行します。 出力の Description 値を確認して、リテール (RETAIL チャネル) ライセンス メディアから作成されたのか、ボリューム (VOLUME_KMSCLIENT) ライセンス メディアから作成されたのかを判断します。
  

    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. **slmgr.vbs /dlv** で RETAIL チャネルが表示される場合は、次のコマンドを実行して、使用しているバージョンの Windows Server の [KMS クライアント セットアップ キー](https://technet.microsoft.com/library/jj612867%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396)を設定し、強制的にライセンス認証を再試行します。 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    たとえば、Windows Server 2016 Datacenter の場合は、次のコマンドを実行します。

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>手順 2: VM と Azure KMS サービスの間の接続を確認する

1. ライセンス認証できない VM のローカル フォルダーに [PSping](http:/technet.microsoft.com/sysinternals/jj729731.aspx) ツールをダウンロードし、展開します。 

2. [スタート] で [Windows PowerShell] を検索し、それを右クリックして [管理者として実行] を選択します。

3. 正しい Azure KMS サーバーを使用するように VM が構成されていることを確認します。 そのためには、次のコマンドを実行します。
  

    ```powershell
    Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms kms.core.windows.net:1688"
    ```

    コマンドにより次のメッセージが返されるはずです:"キー管理サービスのマシン名は kms.core.windows.net:1688 に正常に設定されました"。

4. Psping を使用して、KMS サーバーへの接続があることを確認します。 ダウンロードした Pstools.zip を展開したフォルダーに移動し、次のコマンドを実行します。
  

    ```
    \psping.exe kms.core.windows.net:1688
    ```

  
   出力の最後から 2 番目の行が次のようになっていることを確認します:Sent = 4, Received = 4, Lost = 0 (0% loss)。

   Lost が 0 (ゼロ) より大きい場合、VM には KMS サーバーへの接続がありません。 この状況で、VM が仮想ネットワーク内にあり、その VM にカスタム DNS サーバーが指定されている場合は、その DNS サーバーが kms.core.windows.net を解決できることを確認する必要があります。 または、DNS サーバーを、kms.core.windows.net を解決できるサーバーに変更します。

   仮想ネットワークから DNS サーバーをすべて削除すると、VM が Azure 内部の DNS サービスを使用するという点を覚えておいてください。 このサービスは、kms.core.windows.net を解決できます。
  
また、ゲスト ファイアウォールが、ライセンス認証の試行をブロックするような構成になっていないことを確認します。

1. kms.core.windows.net への接続が成功したことを確認した後、管理者特権の Windows PowerShell プロンプトで次のコマンドを実行します。 このコマンドは、ライセンス認証を複数回試行します。

    ```powershell
    1..12 | ForEach-Object { Invoke-Expression “$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato” ; start-sleep 5 }
    ```

ライセンス認証が成功すると、次のような情報が返されます。

**Windows(R), ServerDatacenter Edition (12345678-1234-1234-1234-12345678) のライセンス認証を行っています … 製品は正常にライセンス認証されました。**

## <a name="faq"></a>FAQ 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>Azure Marketplace から Windows Server 2016 を作成しました。 Windows Server 2016 をライセンス認証するために KMS キーを構成する必要はありますか? 

 
いいえ。 Azure Marketplace のイメージでは、適切な KMS クライアント セットアップ キーが既に構成されています。 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>Windows ライセンス認証は、VM が Azure Hybrid Use Benefit (HUB) を使用しているかどうかにかかわらず、同じように動作しますか? 

 
はい。 
 

### <a name="what-happens-if-windows-activation-period-expires"></a>Windows ライセンス認証が期限切れになるとどうなりますか? 

 
猶予期間が終了し、Windows がまだライセンス認証されていない場合、Windows Server 2008 R2 以降のバージョンの Windows OS では、ライセンス認証に関する追加の通知が表示されます。 デスクトップの壁紙は黒のままで、Windows Update はセキュリティ更新プログラムと重要な更新プログラムだけをインストールし、オプションの更新プログラムはインストールしません。 [ライセンスの条件](https://technet.microsoft.com/library/ff793403.aspx)に関するページの下部の「Notifications (通知)」のセクションを参照してください。   

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
