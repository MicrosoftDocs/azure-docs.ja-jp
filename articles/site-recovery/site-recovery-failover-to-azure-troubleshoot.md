---
title: Azure へのフェールオーバー障害のトラブルシューティング | Microsoft Docs
description: この記事では、Azure へのフェールオーバー時の一般的なエラーをトラブルシューティングする方法を説明します。
author: ponatara
manager: abhemraj
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 01/08/2020
ms.author: mayg
ms.openlocfilehash: d2a0444483c382da7c54accf7dca49d097671771
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92371989"
---
# <a name="troubleshoot-errors-when-failing-over-vmware-vm-or-physical-machine-to-azure"></a>VMware VM または物理マシンから Azure へのフェールオーバー時のエラーをトラブルシューティングする

仮想マシンから Azure へフェールオーバーを実行中に、次のいずれかのエラーを受け取る可能性があります。 トラブルシューティングするには、各エラー条件に説明されている手順に従います。

## <a name="failover-failed-with-error-id-28031"></a>エラー ID 28031 でフェールオーバーが失敗している

Site Recovery は、フェールオーバーした仮想マシンを Azure に作成できませんでした。 次のような原因が考えられます。

* 仮想マシンを作成するためのクォータが不足しています。[サブスクリプション] > [使用量 + クォータ] の順に移動して、使用可能なクォータを確認できます。 [新しいサポート要求](https://aka.ms/getazuresupport)を開いて、クォータを増やすことができます。

* 同じ可用性セットにある異なるサイズ ファミリの仮想マシンのフェールオーバーを試行しています。 同じ可用性セットにあるすべての仮想マシンに対しては、必ず同じサイズ ファミリを選択してください。 仮想マシンの [コンピューティングとネットワーク] の設定に移動してサイズを変更し、フェールオーバーを再試行してください。

* サブスクリプションに、仮想マシンの作成を禁止しているポリシーがあります。 仮想マシンの作成を許可するようにポリシーを変更して、フェールオーバーを再試行してください。

## <a name="failover-failed-with-error-id-28092"></a>エラー ID 28092 でフェールオーバーが失敗している

Site Recovery は、フェールオーバーされた仮想マシンのネットワーク インターフェイスを作成できませんでした。 サブスクリプションでネットワーク インターフェイスを作成するために使用できる十分なクォータがあることを確認してください。 [サブスクリプション] > [使用量 + クォータ] の順に移動して、使用可能なクォータを確認できます。 [新しいサポート要求](https://aka.ms/getazuresupport)を開いて、クォータを増やすことができます。 十分なクォータがある場合、この現象は一時的に発生している可能性があります。もう一度、操作をやり直してください。 再試行しても問題が続く場合、この文書の最後にコメントを残してください。  

## <a name="failover-failed-with-error-id-70038"></a>エラー ID 70038 でフェールオーバーが失敗している

Site Recovery は、フェールオーバーした従来の仮想マシンを Azure に作成できませんでした。 次のような原因が考えられます。

* 仮想ネットワークなど、仮想マシンの作成に必要ないずれかのリソースが存在しない。 仮想マシンの [コンピューティングとネットワーク] の設定に指定されているように仮想ネットワークを作成するか、既に存在している仮想ネットワークの設定を変更して、フェールオーバーを再試行してください。

## <a name="failover-failed-with-error-id-170010"></a>エラー ID 170010 でフェールオーバーが失敗している

Site Recovery は、フェールオーバーした仮想マシンを Azure に作成できませんでした。 これは、オンプレミスの仮想マシンでハイドレーションの内部アクティビティが失敗したために発生します。

Azure でマシンを起動するには、Azure 環境で、いくつかのドライバーがブート開始状態であり、DHCP などのサービスが自動開始状態である必要があります。 そのため、ハイドレーション アクティビティは、フェールオーバーの時点で、**atapi、intelide、storflt、vmbus、および storvsc ドライバー** のスタートアップの種類をブート開始に変換します。 また、DHCP などのいくつかのサービスのスタートアップの種類も自動開始に変換します。 このアクティビティは、環境固有の問題が原因で失敗する可能性があります。 

**Windows Guest OS** 用のドライバーのスタートアップの種類を手動で変更するには、次の手順に従ってください。

1. 次のように、no-hydration スクリプトを[ダウンロード](https://download.microsoft.com/download/5/D/6/5D60E67C-2B4F-4C51-B291-A97732F92369/Script-no-hydration.ps1)して実行します。 このスクリプトは、VM にハイドレーションが必要かどうかを確認します。

    `.\Script-no-hydration.ps1`

    ハイドレーションが必要な場合は、次の結果を示します。

    ```output
    REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

    This system doesn't meet no-hydration requirement.
    ```

    VM がハイドレーションなしの要件を満たしている場合、スクリプトは「このシステムはハイドレーションなしの要件を満たしている」という結果を示します。 この場合、すべてのドライバーとサービスが Azure で必要な状態にあり、VM にハイドレーションは必要ありません。

2. VM がハイドレーションなしの要件を満たしていない場合は、次のように no-hydration-set スクリプトを実行します。

    `.\Script-no-hydration.ps1 -set`
    
    これによりドライバーのスタートアップの種類が変換され、次のような結果が示されます。

    ```output
    REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

    Updating registry:  REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc   start =  0

    This system is now no-hydration compatible.
    ```

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>仮想マシンで [接続] ボタンが灰色表示されているために、/RDP/SSH をフェールオーバーされた仮想マシンに接続できない

RDP の問題に関する詳細なトラブルシューティング手順については、[こちら](../virtual-machines/troubleshooting/troubleshoot-rdp-connection.md)のドキュメントを参照してください。

SSH の問題に関する詳細なトラブルシューティング手順については、[こちら](../virtual-machines/troubleshooting/troubleshoot-ssh-connection.md)のドキュメントを参照してください。

Azure でフェールオーバーされた VM で **[接続]** ボタンが淡色表示され、Express Route またはサイト間 VPN 接続を使用して Azure に接続されない場合は、次の操作を実行します。

1. **[仮想マシン]**  >  **[ネットワーク]** に移動し、必要なネットワーク インターフェイスの名前をクリックします。  ![仮想マシンのネットワーク ページのスクリーンショット。ネットワーク インターフェイスの名前が選択されています。](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. **[IP 構成]** に移動し、必要な IP 構成の名前フィールドをクリックします。 ![ネットワーク インターフェイスの IP 構成ページのスクリーンショット。IP 構成の名前が選択されています。](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. パブリック IP アドレスを有効にするには、 **[有効にする]** をクリックします。 ![IP の有効化](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. **[必要な設定の構成]**  >  **[新規作成]** をクリックします。 ![新規作成](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. パブリック アドレスの名前を入力し、 **[SKU]** と **[割り当て]** の既定のオプションを選択し、 **[OK]** をクリックします。
6. **[保存]** をクリックして変更を保存します。
7. パネルを閉じ、仮想マシンの **[概要]** セクションに移動して、/RDP に接続します。

## <a name="unable-to-connectrdpssh---vm-connect-button-available"></a>接続/RDP/SSH ができない - VM の [接続] ボタンは使用可能

Azure でフェールオーバーされた VM で **[接続]** ボタンが使用できる (淡色表示されていない) 場合は、仮想マシンで **[Boot diagnostics]\(ブート診断)** を調べ、[この記事](../virtual-machines/troubleshooting/boot-diagnostics.md)に記載されているエラーがないかチェックします。

1. 仮想マシンが起動されていない場合は、前の復旧ポイントにフェールオーバーしてみます
2. 仮想マシン内のアプリケーションが開始されていない場合は、アプリケーションと整合性がとれた復旧ポイントにフェールオーバーしてみます
3. 仮想マシンがドメインに参加している場合は、ドメイン コントローラーが適切に機能していることを確認します。 そのためには、以下の手順に従います。

    a. 同じネットワークに新しい仮想マシンを作成します。

    b.  フェールオーバーされた仮想マシンが開始されると予想されているのと同じドメインに参加できることを確認します。

    c. ドメイン コントローラーが適切に機能 **していない** 場合は、ローカル管理者アカウントを使用して、フェールオーバーされた仮想マシンにログインしてみます。
4. カスタム DNS サーバーを使用している場合は、そのサーバーにアクセスできることを確認します。 そのためには、以下の手順に従います。

    a. 同じネットワークに新しい仮想マシンを作成します

    b. カスタムの DNS サーバーを使用して仮想マシンが名前を解決できるかどうかを確認します

>[!Note]
>ブート診断以外の設定を有効にした場合は、フェールオーバーの前に Azure VM エージェントを仮想マシンにインストールする必要があります。

## <a name="unable-to-open-serial-console-after-failover-of-a-uefi-based-machine-into-azure"></a>UEFI ベースのマシンを Azure にフェールオーバーした後、シリアル コンソールを開くことができない

RDP を使用してマシンに接続できても、シリアル コンソールを開くことができない場合は、次の手順に従います。

* マシンの OS が Red Hat または Oracle Linux 7.*/8.0 の場合は、ルート アクセス許可を使用してフェールオーバー Azure VM で次のコマンドを実行します。 コマンドを実行した後に VM を再起動します。

  ```console
  grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg
  ```

* マシンの OS が CentOS 7.* の場合は、ルート アクセス許可を使用してフェールオーバー Azure VM で次のコマンドを実行します。 コマンドを実行した後に VM を再起動します。

  ```console
  grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg
  ```

## <a name="unexpected-shutdown-message-event-id-6008"></a>予期しないシャット ダウンのメッセージ (イベント ID 6008)

フェールオーバー後の Windows VM 起動時に、回復した VM で、予期しないシャット ダウンのメッセージを受信した場合、それはフェールオーバーに使用された復旧ポイントで、VM のシャット ダウン状態がキャプチャされなかったことを示しています。 これは、VM が完全にはシャット ダウンされていないときのポイントに復旧すると発生します。

これは一般に懸念の原因にはならないため、計画外のフェールオーバーであれば通常は無視できます。 フェールオーバーが計画されている場合は、フェールオーバーの前に VM が正しくシャット ダウンされるようにして、オンプレミスの保留中のレプリケーション データが Azure に送信されるのに十分な時間を確保します。 次に、[フェールオーバー画面](site-recovery-failover.md#run-a-failover)の **[Lateset]\(最新)** オプションを使用して、Azure 上の保留中データがすべて処理されて復旧ポイントに入れられるようにします。それが後で、VM のフェールオーバーに使用されます。

## <a name="unable-to-select-the-datastore"></a>データストアを選択できない

この問題は、フェールオーバーが発生した仮想マシンを再保護しようとしたときに、Azure portal でデータストアを表示できない場合に指摘されます。 これは、マスター ターゲットが、Azure Site Recovery に追加された vCenter の仮想マシンとして認識されていないためです。

仮想マシンの再保護の詳細については、「[Azure へのフェールオーバー後に、マシンを再保護し、オンプレミス サイトにフェールバックする](vmware-azure-reprotect.md)」を参照してください。

この問題を解決するには:

ソース マシンを管理する vCenter 内にマスター ターゲットを手動で作成します。 データストアを利用できるのは、次の vCenter の検出および更新ファブリック操作の後です。

> [!Note]
> 
> 検出および更新のファブリック操作は完了までに最大 30 分かかります。 

## <a name="linux-master-target-registration-with-cs-fails-with-a-tls-error-35"></a>Linux マスター ターゲット登録が TLS エラー 35 CS で失敗する 

認証済みプロキシがマスター ターゲットで有効になっているため、構成サーバーでの Azure Site Recovery マスター ターゲット登録が失敗します。 
 
このエラーは、インストール ログ内の次の文字列で示されます。 

```
RegisterHostStaticInfo encountered exception config/talwrapper.cpp(107)[post] CurlWrapper Post failed : server : 10.38.229.221, port : 443, phpUrl : request_handler.php, secure : true, ignoreCurlPartialError : false with error: [at curlwrapperlib/curlwrapper.cpp:processCurlResponse:231]   failed to post request: (35) - SSL connect error. 
```

この問題を解決するには:
 
1. 構成サーバーの VM でコマンド プロンプトを開き、次のコマンドを使用してプロキシ設定を確認します。

    cat /etc/environment  echo $http_proxy  echo $https_proxy 

2. http_proxy または https_proxy のどちらかの設定が定義されていることが前のコマンドの出力で示されている場合は、次のいずれかの方法を使用して構成サーバーとマスター ターゲットの間の通信のブロックを解除します。
   
   - [PsExec ツール](/sysinternals/downloads/psexec) をダウンロードします。
   - このツールを使用して、システム ユーザーのコンテキストにアクセスし、プロキシ アドレスが構成されているかどうかを判断します。 
   - プロキシが構成されている場合は、PsExec ツールを使用してシステム ユーザーのコンテキストで IE を開きます。
  
     **psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"**

   - マスター ターゲット サーバーが構成サーバーと必ず通信できるようにするには
  
     - プロキシ経由でマスター ターゲット サーバーの IP アドレスをバイパスするよう、Internet Explorer のプロキシ設定を変更します。   
     または
     - マスター ターゲット サーバーでプロキシを無効にします。 


## <a name="next-steps"></a>次のステップ
- [Windows VM への RDP 接続](../virtual-machines/troubleshooting/troubleshoot-rdp-connection.md)のトラブルシューティング
- [Linux VM への SSH 接続](../virtual-machines/troubleshooting/detailed-troubleshoot-ssh-connection.md)のトラブルシューティング

さらにサポートが必要な場合は、[Site Recovery に関する Microsoft Q&A 質問ページ](/answers/topics/azure-site-recovery.html)にクエリを投稿するか、この文書の最後にコメントを残してください。 ユーザー支援を可能にするために必要なアクティブ コミュニティを設置しています。