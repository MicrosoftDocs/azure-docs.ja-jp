---
title: Azure Stack 1903 更新プログラム | Microsoft Docs
description: 新機能、既知の問題、更新プログラムをダウンロードする場所など、Azure Stack 統合システムの 1903 更新プログラムについて説明します。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 04/04/2019
ms.openlocfilehash: 2a2e289423eda53d610b2346193f6ee8a30b9c48
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/04/2019
ms.locfileid: "58917687"
---
# <a name="azure-stack-1903-update"></a>Azure Stack 1903 更新プログラム

*適用対象:Azure Stack 統合システム*

この記事では、1903 更新プログラム パッケージの内容について説明します。 更新プログラムには、このバージョンの Azure Stack に対する機能強化、修正、新機能が含まれています。 またこの記事では、このリリースの既知の問題についても説明し、更新プログラムをダウンロードできるリンクも掲載しています。 既知の問題は、更新プロセスに直接関係する問題と、ビルド (インストール後) に関する問題に分けられています。

> [!IMPORTANT]  
> 更新プログラム パッケージは、Azure Stack 統合システム専用です。 Azure Stack Development Kit にこの更新プログラム パッケージは適用しないでください。

## <a name="build-reference"></a>ビルドのリファレンス

Azure Stack 1903 更新プログラムのビルド番号は **1.1903.0.35** です。

> [!IMPORTANT]
> 1903 ペイロードに、ASDK リリースは含まれません。

## <a name="hotfixes"></a>修正プログラム

Azure Stack では、修正プログラムが定期的にリリースされます。 Azure Stack を 1903 に更新する前に、必ず 1902 用の[最新の Azure Stack 修正プログラム](#azure-stack-hotfixes)をインストールしてください。

Azure Stack 修正プログラムを適用できるのは Azure Stack 統合システムのみです。ASDK には修正プログラムをインストールしないでください。

> [!TIP]  
> 以下の *RSS* または *Atom* フィードに登録して、Azure Stack 修正プログラムの最新情報を入手してください。
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Azure Stack 修正プログラム

- **1902**:[KB 4481548 – Azure Stack 修正プログラム 1.1902.2.73](https://support.microsoft.com/help/4494719)

## <a name="improvements"></a>機能強化

- 1903 の更新プログラムのペイロードには、Azure Stack をホストする、基になるオペレーティング システムを含まない Azure Stack のコンポーネントの更新プログラムが含まれています。 これにより、特定の更新プログラムのみを対象とすることができます。 その結果、1903 の更新プログラムが完了するまでの予測所要時間が短縮しています (約 16 時間。ただし正確な時間は変わる可能性があります)。 この実行時間の短縮は 1903 の更新プログラムに固有のものです。後続の更新プログラムにはオペレーティング システムの更新プログラムが含まれる可能性があるため、実行時間は変わると思われます。 今後の更新プログラムでも、含まれているペイロードに応じて更新プログラムが完了するまでの予測所要時間に関する同様のガイダンスを提供します。

- **パブリック IP アドレス**の**アイドル タイムアウト (分)** 値の変更が有効になるのを阻止するネットワークのバグを修正しました。 以前はこの値の変更が無視されていたため、変更内容に関係なく値は既定値の 4 分でした。 この設定では、クライアントからキープアライブ メッセージを送信しなくても TCP 接続が開いたまま維持される時間 (分) が制御されます。 このバグの影響を受けていたのはインスタンス レベルのパブリック IP のみで、ロード バランサーに割り当てられたパブリック IP への影響はありませんでした。

- 一般的な問題の自動修正を含めた更新エンジンの信頼性が改善され、更新プログラムが中断なしに適用されるようになりました。

- ディスクの空き領域が少ない状態での検出と修復の機能が強化されました。

## <a name="prerequisites"></a>前提条件

> [!IMPORTANT]
> 1903 に更新する前に 1902 用の[最新の Azure Stack 修正プログラム](#azure-stack-hotfixes) (ある場合) をインストールしてください。

- ワークロードの計画とサイズ設定を行うには、最新バージョンの [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) を使用します。 最新バージョンではバグの修正が含まれ、Azure Stack の各更新プログラムでリリースされる新機能が提供されています。

- この更新プログラムのインストールを開始する前に、次のパラメーターを指定して [Test-AzureStack](azure-stack-diagnostic-test.md) を実行して Azure Stack の状態を確認し、見つかったすべての操作上の問題 (すべての警告とエラーを含む) を解決します。 また、アクティブなアラートを確認し、アクションが必要なアラートを解決します。

    ```PowerShell
   Test-AzureStack -Group UpdateReadiness  
    ```

- Azure Stack を System Center Operations Manager で管理している場合は、1903 を適用する前に、[Microsoft Azure Stack 用の管理パック](https://www.microsoft.com/download/details.aspx?id=55184)をバージョン 1.0.3.11 に更新してください。

- Azure Stack の更新プログラム パッケージの形式は、1902 リリースより **.bin/.exe/.xml** から **.zip/.xml** に変更になりました。 インターネットに接続されている Azure Stack スケール ユニットをお持ちのお客様の場合には、ポータルに "**利用可能な更新プログラムがあります**" というメッセージが表示されます。 インターネット接続のないお客様の場合には、.zip と対応する .xml ファイルをダウンロードしてインポートできます。

<!-- ## New features -->

<!-- ## Fixed issues -->

<!-- ## Common vulnerabilities and exposures -->

## <a name="known-issues-with-the-update-process"></a>更新プロセスに関する既知の問題

- [Test-AzureStack](azure-stack-diagnostic-test.md) を実行すると、ベースボード管理コントローラー (BMC) からの警告メッセージが表示されます。 この警告は無視してかまいません。

- <!-- 2468613 - IS --> この更新プログラムのインストール中に、`Error – Template for FaultType UserAccounts.New is missing.` というタイトルのアラートが表示されることがありますが、これらのアラートは無視してかまいません。 この更新プログラムのインストールが完了した後、これらのアラートは自動的に閉じられます。

## <a name="post-update-steps"></a>更新後の手順

- この更新プログラムをインストールした後、適用可能な修正プログラムがあればインストールします。 詳細については、「[修正プログラム](#hotfixes)」および Microsoft の[サービス ポリシー](azure-stack-servicing-policy.md)を参照してください。  

- 保存データの暗号化キーを取得し、お客様の Azure Stack デプロイの外部に安全に保管します。 [キーを取得する方法の手順](azure-stack-security-bitlocker.md)に関する記事に従ってください。

## <a name="known-issues-post-installation"></a>既知の問題 (インストール後)

このビルド バージョンのインストール後について次の既知の問題があります。

### <a name="portal"></a>ポータル

<!-- 2930820 - IS ASDK --> 
- 管理ポータルとユーザー ポータルの両方で、"Docker" を検索すると、返される項目が正しくありません。 これは Azure Stack でご利用になれません。 これを作成しようとすると、エラーを示すブレードが表示されます。 

<!-- 2931230 – IS  ASDK --> 
- アドオン プランとしてユーザー サブスクリプションに追加されたプランは、ユーザー サブスクリプションからプランを削除しても削除できません。 アドオン プランを参照するサブスクリプションも削除されるまで、プランは残ります。 

<!-- TBD - IS ASDK --> 
- バージョン 1804 で導入された 2 種類の管理サブスクリプションは使用しないでください。 **Metering サブスクリプション**と **Consumption サブスクリプション**のサブスクリプションの種類です。 これらのサブスクリプションの種類は、バージョン 1804 以降の新しい Azure Stack 環境では表示されますが、まだ使用できる状態ではありません。 **既定のプロバイダー** サブスクリプションの種類を引き続き使用する必要があります。

<!-- 3557860 - IS ASDK --> 
- ユーザー サブスクリプションを削除すると、リソースが孤立します。 回避策として、まず、ユーザー リソースまたはリソース グループ全体を削除してから、ユーザー サブスクリプションを削除します。

<!-- 1663805 - IS ASDK --> 
- Azure Stack ポータルを使用して、サブスクリプションへのアクセス許可を表示することはできません。 この問題を回避するには、[PowerShell を使用してアクセス許可を確認](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan)します。

<!-- Daniel 3/28 -->
- ユーザー ポータルでストレージ アカウント内の BLOB に移動し、ナビゲーション ツリーから**アクセス ポリシー**を開こうとすると、後続のウィンドウの読み込みに失敗します。

<!-- Daniel 3/28 -->
- ユーザー ポータルで **[OAuth(preview)]\(OAuth (プレビュー)\)** オプションを使用して BLOB をアップロードしようとすると、タスクがエラー メッセージにより失敗します。 この問題を回避するには、**[SAS]** オプションを使用して BLOB をアップロードします。

- Azure Stack ポータルにログインすると、パブリック Azure portal に関する通知が表示される場合があります。 このような通知は現在 Azure Stack には適用されないため、無視しても問題ありません (たとえば、"1 new update - The following updates are now available: Azure portal April 2019 update")。

<!-- ### Health and monitoring -->

### <a name="compute"></a>Compute

- 新しい Windows 仮想マシン (VM) を作成するときに、次のエラーが表示されることがあります。

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   このエラーは、VM でブート診断を有効にしても、ブート診断ストレージ アカウントを削除した場合に発生します。 この問題を回避するには、以前使用したものと同じ名前のストレージ アカウントを再作成します。

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- 仮想マシン スケール セットの作成エクスペリエンスでは、デプロイのオプションとして CentOS-based 7.2 が提供されます。 このイメージは Azure Stack では使用できないため、デプロイ用に別のオペレーティング システムを選択するか、またはデプロイする前にオペレーターが Marketplace からダウンロードしておいた別の CentOS イメージを指定する Azure Resource Manager テンプレートをご使用ください。  

<!-- TBD - IS ASDK --> 
- 更新プログラム 1903 の適用後、Managed Disks を使用した VM をデプロイすると、次の問題が発生する可能性があります。

   - 1808 更新の前にサブスクリプションが作成された場合、Managed Disks を使用した VM をデプロイすると、内部エラー メッセージが出て失敗することがあります。 このエラーを解決するには、サブスクリプションごとに次の手順に従ってください。
      1. テナント ポータルで、**[サブスクリプション]** に移動して、サブスクリプションを検索します。 **[リソース プロバイダー]** を選択し、**[Microsoft.Compute]** を選択してから、**[再登録]** をクリックします。
      2. 同じサブスクリプションで、**[アクセス制御 (IAM)]** に移動し、**[Azure Stack – マネージド ディスク]** がリストに含まれていることを確認します。
   - マルチテナント環境を構成した場合、ゲスト ディレクトリに関連付けられているサブスクリプションで VM をデプロイすると、内部エラー メッセージが出て失敗することがあります。 このエラーを解決するには、[この記事](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory)にある手順に従って、各ゲスト ディレクトリを構成します。

- SSH の認可を有効にして作成した Ubuntu 18.04 VM では、SSH キーを使用してサインインすることはできません。 回避策として、プロビジョニング後に Linux 拡張機能用の VM アクセスを使用して SSH キーを実装するか、パスワードベースの認証を使用してください。

- ハードウェア ライフサイクル ホスト (HLH) をお持ちでない場合:ビルド 1902 以前は、グループ ポリシー *Computer Configuration\Windows Settings\Security Settings\Local Policies\Security Options* を **[LM と NTLM を送信する - ネゴシエーションの場合、NTLMv2 セッション セキュリティを使う]** に設定する必要がありました。 ビルド 1902 からは、**[定義されていません]** のままにするか、**[NTLMv2 応答のみ送信する]** (既定値) に設定する必要があります。 そうしないと、PowerShell リモート セッションを確立できず、"*アクセスが拒否されました*" というエラーが表示されます。

   ```PowerShell
   PS C:\Users\Administrator> $session = New-PSSession -ComputerName x.x.x.x -ConfigurationName PrivilegedEndpoint  -Credential $cred
   New-PSSession : [x.x.x.x] Connecting to remote server x.x.x.x failed with the following error message : Access is denied. For more information, see the 
   about_Remote_Troubleshooting Help topic.
   At line:1 char:12
   + $session = New-PSSession -ComputerName x.x.x.x -ConfigurationNa ...
   +            ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
      + CategoryInfo          : OpenError: (System.Manageme....RemoteRunspace:RemoteRunspace) [New-PSSession], PSRemotingTransportException
      + FullyQualifiedErrorId : AccessDenied,PSSessionOpenFailed
   ```

### <a name="networking"></a>ネットワーク  

<!-- 3239127 - IS, ASDK -->
- Azure Stack ポータルで、VM インスタンスにアタッチされたネットワーク アダプターにバインドされている IP 構成の静的 IP アドレスを変更すると、次の内容の警告メッセージが表示されます。 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`。

    このメッセージは無視してかまいません。たとえ VM インスタンスが再起動しなくても IP アドレスは変更されます。

<!-- 3632798 - IS, ASDK -->
- ポータルで受信セキュリティ規則を追加し、**[Service Tag]\(サービス タグ\)** をソースとして選択すると、Azure Stack では利用できないオプションがいくつか **[Source Tag]\(ソース タグ\)** リストに表示されます。 Azure Stack で有効なのは次のオプションだけです。

  - **インターネット**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
  その他のオプションについては、Azure Stack ではソース タグとしてサポートされません。 同様に、送信セキュリティ規則を追加し、**[Service Tag]\(サービス タグ\)** を宛先として選択した場合も、**[Source Tag]\(ソース タグ\)** のリストに同じオプションが表示されます。 有効なオプションは、前述のリストで説明した **[Source Tag]\(ソース タグ\)** と同じものに限られます。

- ネットワーク セキュリティ グループ (NSG) は、Azure Stack ではグローバル Azure と同様に機能しません。 Azure では、1 つの NSG ルールに複数のポートを設定できます (ポータル、PowerShell、Resource Manager テンプレートを使用)。 ただし、Azure Stack では、ポータルを介して、1 つの NSG ルールに複数のポートを設定できません。 この問題を回避するには、Resource Manager テンプレートまたは PowerShell を使用して、これらの追加ルールを設定します。

<!-- 3203799 - IS, ASDK -->
- Azure Stack では、現在、インスタンス サイズに関係なく、VM インスタンスに 4 つを超えるネットワーク インターフェイス (NIC) をアタッチできません。

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- お客様の最初の Azure 関数をサブスクリプションに作成する前に、ストレージ リソース プロバイダーを登録する必要があります。


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->

## <a name="download-the-update"></a>更新プログラムをダウンロードする

Azure Stack 1903 更新プログラム パッケージは、[ここから](https://aka.ms/azurestackupdatedownload)ダウンロードできます。 

Azure Stack デプロイでは、接続されたシナリオに限り、セキュリティで保護されたエンドポイントが定期的にチェックされ、お客様のクラウド用の更新プログラムが入手可能かどうかが自動的に通知されます。 詳細については、[Azure Stack の更新プログラムの管理](azure-stack-updates.md#using-the-update-tile-to-manage-updates)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

- Azure Stack での更新プログラム管理の概要については、「[Azure Stack での更新プログラムの管理概要](azure-stack-updates.md)」を参照してください。  
- Azure Stack に更新プログラムを適用する方法については、「[Azure Stack で更新を適用する](azure-stack-apply-updates.md)」を参照してください。
- Azure Stack 統合システムのサービス ポリシーについて、およびサポートを受けられる状態にシステムを維持するために必要な作業について確認するには、「[Azure Stack サービス ポリシー](azure-stack-servicing-policy.md)」を参照してください。  
- 特権エンドポイント (PEP) を使用して更新プログラムを監視および再開するには、「[特権エンドポイントを使用して Azure Stack での更新プログラムをモニターする](azure-stack-monitor-update.md)」をご覧ください。  
