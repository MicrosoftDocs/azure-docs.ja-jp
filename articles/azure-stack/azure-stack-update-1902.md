---
title: Azure Stack 1902 更新プログラム | Microsoft Docs
description: 新機能、既知の問題、更新プログラムをダウンロードする場所など、Azure Stack 統合システムの 1902 更新プログラムについて説明します。
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
ms.date: 04/03/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 04/03/2019
ms.openlocfilehash: 5971692b3e6447bc790b2e34cf84eae66979f7f5
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862082"
---
# <a name="azure-stack-1902-update"></a>Azure Stack 1902 更新プログラム

*適用対象:Azure Stack 統合システム*

この記事では、1902 更新プログラム パッケージの内容について説明します。 更新プログラムには、このバージョンの Azure Stack に対する機能強化、修正、新機能が含まれています。 またこの記事では、このリリースの既知の問題についても説明し、更新プログラムをダウンロードできるリンクも掲載しています。 既知の問題は、更新プロセスに直接関係する問題と、ビルド (インストール後) に関する問題に分けられています。

> [!IMPORTANT]  
> 更新プログラム パッケージは、Azure Stack 統合システム専用です。 Azure Stack Development Kit にこの更新プログラム パッケージは適用しないでください。

## <a name="build-reference"></a>ビルドのリファレンス

Azure Stack 1902 更新プログラムのビルド番号は **1.1902.0.69** です。

## <a name="hotfixes"></a>修正プログラム

Azure Stack では、修正プログラムが定期的にリリースされます。 Azure Stack を 1902 に更新する前に、必ず 1901 用の[最新の Azure Stack 修正プログラム](#azure-stack-hotfixes)をインストールしてください。

Azure Stack 修正プログラムを適用できるのは Azure Stack 統合システムのみです。ASDK には修正プログラムをインストールしないでください。

> [!TIP]  
> 以下の *RSS* または *Atom* フィードに登録して、Azure Stack 修正プログラムの最新情報を入手してください。
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Azure Stack 修正プログラム

- **1809**: [KB 4481548 – Azure Stack 修正プログラム 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**: 最新の修正プログラムはありません。
- **1901**: [KB 4495662 – Azure Stack 修正プログラム 1.1901.3.105](https://support.microsoft.com/help/4495662)
- **1902**:[KB 4494719 – Azure Stack 修正プログラム 1.1902.2.73](https://support.microsoft.com/help/4494719)

## <a name="prerequisites"></a>前提条件

> [!IMPORTANT]
> [1.1901.0.95 または 1.1901.0.99](azure-stack-update-1901.md#build-reference) からは、最初に 1901 修正プログラムをインストールすることなく、1902 を直接インストールできます。 ただし、古い **1901.2.103** 修正プログラムをインストールしてある場合は、1902 をインストールする前に、新しい [1901.3.105 修正プログラム](https://support.microsoft.com/help/4495662)をインストールする必要があります。

- この更新プログラムのインストールを開始する前に、次のパラメーターを指定して [Test-AzureStack](azure-stack-diagnostic-test.md) を実行して Azure Stack の状態を確認し、見つかったすべての操作上の問題 (すべての警告とエラーを含む) を解決します。 また、アクティブなアラートを確認し、アクションが必要なアラートを解決します。

    ```powershell
    Test-AzureStack -Include AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

- Azure Stack を System Center Operations Manager (SCOM) で管理している場合は、1902 を適用する前に、[Microsoft Azure Stack 用の管理パック](https://www.microsoft.com/download/details.aspx?id=55184)をバージョン 1.0.3.11 に更新してください。

- Azure Stack の更新プログラム パッケージの形式は、1902 リリースより **.bin/.exe/.xml** から **.zip/.xml** に変更になりました。 インターネットに接続されている Azure Stack スケール ユニットをお持ちのお客様の場合には、ポータルに "**利用可能な更新プログラムがあります**" というメッセージが表示されます。 インターネット接続のないお客様の場合には、.zip と対応する .xml ファイルをダウンロードしてインポートできます。

<!-- ## New features -->

<!-- ## Fixed issues -->

## <a name="improvements"></a>機能強化

- 1902 ビルドでは、Azure Stack の管理者ポータル上にプラン、オファー、クォータ、アドオン プランの作成のための新しいユーザー インターフェイスを導入しています。 スクリーンショットを含めた詳細については、[プラン、オファー、クォータの作成](azure-stack-create-plan.md)に関するページを参照してください。

<!-- 1460884    Hotfix: Adding StorageController service permission to talk to ClusterOrchestrator  Add node -->
- ノード追加による容量拡張にあたりスケール ユニットの状態を [Expanding storage]\(ストレージの拡張中\) から [実行中] に切り替える際の信頼性を向上させました。

<!--
1426197 3852583: Increase Global VM script mutex wait time to accommodate enclosed operation timeout    PNU
1399240 3322580: [PNU] Optimize the DSC resource execution on the Host  PNU
1398846 Bug 3751038: ECEClient.psm1 should provide cmdlet to resume action plan instance    PNU
1398818 3685138, 3734779: ECE exception logging, VirtualMachine ConfigurePending should take node name from execution context   PNU
1381018 [1902] 3610787 - Infra VM creation should fail if the ClusterGroup already exists   PNU
-->
- パッケージの整合性とセキュリティを向上させ、オフラインでの取り込みの管理を容易に行えるようにするため、Microsoft では、更新プログラム パッケージの形式を .exe ファイルと .bin ファイルから .zip ファイルに変更しました。 新しい形式では、パッケージの展開プロセスの信頼性が向上しています (旧形式では、更新プログラムの準備が止まってしまうことがありました)。 OEM から提供される更新プログラム パッケージにも、同じパッケージ形式が適用されます。
- Test-AzureStack を実行するとき、Include ステートメントの後に 10 個ものパラメーターを追加で渡す必要があったのが、"Test-AzureStack -Group UpdateReadiness" を使用するだけで済むようになり、Azure Stack 運用担当者にとってのエクスペリエンスが改善しました。

  ```powershell
    Test-AzureStack -Group UpdateReadiness  
  ```  
  
- コアとなるインフラストラクチャ サービスの更新プロセスにおける全般的な信頼性と可用性を向上させるために、更新アクション プランの一部としてのネイティブ更新リソースプロバイダーにより、必要に応じてグローバル修復が自動で検出および呼び出されるようになりました。 グローバル修復の "修復" ワークフローに含まれる項目は次のとおりです。
    - 最適な状態にないインフラストラクチャ仮想マシンがあるかどうかを確認し、必要に応じて修復を試みる 
    - コントロール プランの一部としての SQL サービスに問題が発生していないかどうかを確認し、必要に応じて修復を試みる
    - ネットワーク コントローラー (NC) の一部を構成するソフトウェア ロード バランサー (SLB) サービスの状態を確認し、必要に応じて修復を試みる
    - ネットワーク コントローラー (NC) サービスの状態を確認し、必要に応じて修復を試みる
    - 緊急回復コンソール サービス (ERCS) のサービス ファブリック ノードの状態を確認し、必要に応じて修復する
    - XRP サービス ファブリック ノードの状態を確認し、必要に応じて修復する
    - Azure Consistent Storage (ACS) のサービス ファブリック ノードの状態を確認し、必要に応じて修復する

<!-- 1460884    Hotfix: Adding StorageController service permission to talk to ClusterOrchestrator  Add node -->
- ノード追加による容量拡張にあたりスケール ユニットの状態を [Expanding storage]\(ストレージの拡張中\) から [実行中] に切り替える際の信頼性を向上させました。    

<!-- 
1426690 [SOLNET] 3895478-Get-AzureStackLog_Output got terminated in the middle of network log   Diagnostics
1396607 3796092: Move Blob services log from Storage role to ACSBlob role to reduce the log size of Storage Diagnostics
1404529 3835749: Enable Group Policy Diagnostic Logs    Diagnostics
1436561 Bug 3949187: [Bug Fix] Remove AzsStorageSvcsSummary test from SecretRotationReadiness Test-AzureStack flag  Diagnostics
1404512 3849946: Get-AzureStackLog should collect all child folders from c:\Windows\Debug   Diagnostics 
-->
- Azure Stack 診断ツールのログ収集の信頼性とパフォーマンスを向上させました。 ネットワーク サービスおよび ID サービスのログ項目を追加しました。 

<!-- 1384958    Adding a Test-AzureStack group for Secret Rotation  Diagnostics -->
- シークレット ローテーションの準備テストのための Test-AzureStack の信頼性を向上させました。

<!-- 1404751    3617292: Graph: Remove dependency on ADWS.  Identity -->
- お客様の Active Directory 環境との通信時の AD Graph の信頼性を向上させました

<!-- 1391444    [ISE] Telemetry for Hardware Inventory - Fill gap for hardware inventory info   System info -->
- Get-AzureStackStampInformation のハードウェア インベントリ コレクションを改善しました。

- ERCS インフラストラクチャ上で実行する操作の信頼性を向上させるため、各 ERCS インスタンスのメモリを 8 GB から 12 GB に増やしました。 Azure Stack 統合システムでは、合計 12 GB の増加となります。

> [!IMPORTANT]
> パッチと更新のプロセスに伴うテナントのダウンタイムを最小限に抑えられるように、**[容量]** ブレードで Azure Stack スタンプに 12 GB を上回る量の空き領域があることを確認してください。 このメモリの増加は、更新プログラムのインストールが正常に完了した後に **[容量]** ブレードに反映されます。

## <a name="common-vulnerabilities-and-exposures"></a>共通脆弱性識別子

この更新では、次のセキュリティ更新プログラムがインストールされます。  
- [ADV190005](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190006)
- [CVE-2019-0595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0595)
- [CVE-2019-0596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0596)
- [CVE-2019-0597](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0597)
- [CVE-2019-0598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0598)
- [CVE-2019-0599](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0599)
- [CVE-2019-0600](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0600)
- [CVE-2019-0601](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0601)
- [CVE-2019-0602](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0602)
- [CVE-2019-0615](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0615)
- [CVE-2019-0616](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0616)
- [CVE-2019-0618](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0618)
- [CVE-2019-0619](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0619)
- [CVE-2019-0621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0621)
- [CVE-2019-0623](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0623)
- [CVE-2019-0625](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0625)
- [CVE-2019-0626](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0626)
- [CVE-2019-0627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0627)
- [CVE-2019-0628](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0628)
- [CVE-2019-0630](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0630)
- [CVE-2019-0631](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0631)
- [CVE-2019-0632](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0632)
- [CVE-2019-0633](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0633)
- [CVE-2019-0635](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0635)
- [CVE-2019-0636](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0636)
- [CVE-2019-0656](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0656)
- [CVE-2019-0659](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0659)
- [CVE-2019-0660](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0660)
- [CVE-2019-0662](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0662)
- [CVE-2019-0663](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0663)

これらの脆弱性の詳細については、上記のリンクをクリックするか、Microsoft サポート技術情報の記事 [4487006](https://support.microsoft.com/en-us/help/4487006) を参照してください。

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

<!-- ### Health and monitoring -->

### <a name="compute"></a>Compute

- 新しい Windows 仮想マシン (VM) を作成するときに、次のエラーが表示されることがあります。

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   このエラーは、VM でブート診断を有効にしても、ブート診断ストレージ アカウントを削除した場合に発生します。 この問題を回避するには、以前使用したものと同じ名前のストレージ アカウントを再作成します。

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- 仮想マシン スケール セット (VMSS) の作成エクスペリエンスでは、デプロイのオプションとして CentOS-based 7.2 が提供されます。 このイメージは Azure Stack では使用できないため、デプロイ用に別のオペレーティング システムを選択するか、またはデプロイする前にオペレーターが Marketplace からダウンロードしておいた別の CentOS イメージを指定する Azure Resource Manager テンプレートをご使用ください。  

<!-- TBD - IS ASDK --> 
- 更新プログラム 1902 の適用後、Managed Disks を使用した VM をデプロイすると、次の問題が発生する可能性があります。

   - 1808 更新の前にサブスクリプションが作成された場合、Managed Disks を使用した VM をデプロイすると、内部エラー メッセージが出て失敗することがあります。 このエラーを解決するには、サブスクリプションごとに次の手順に従ってください。
      1. テナント ポータルで、**[サブスクリプション]** に移動して、サブスクリプションを検索します。 **[リソース プロバイダー]** を選択し、**[Microsoft.Compute]** を選択してから、**[再登録]** をクリックします。
      2. 同じサブスクリプションで、**[アクセス制御 (IAM)]** に移動し、**[Azure Stack – マネージド ディスク]** がリストに含まれていることを確認します。
   - マルチテナント環境を構成した場合、ゲスト ディレクトリに関連付けられているサブスクリプションで VM をデプロイすると、内部エラー メッセージが出て失敗することがあります。 このエラーを解決するには、[この記事](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory)にある手順に従って、各ゲスト ディレクトリを構成します。

- SSH の認可を有効にして作成した Ubuntu 18.04 VM では、SSH キーを使用してログインすることはできません。 回避策として、プロビジョニング後に Linux 拡張機能用の VM アクセスを使用して SSH キーを実装するか、パスワードベースの認証を使用してください。

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

Azure Stack 1902 更新プログラム パッケージは、[ここから](https://aka.ms/azurestackupdatedownload)ダウンロードできます。 

Azure Stack デプロイでは、接続されたシナリオに限り、セキュリティで保護されたエンドポイントが定期的にチェックされ、お客様のクラウド用の更新プログラムが入手可能かどうかが自動的に通知されます。 詳細については、[Azure Stack の更新プログラムの管理](azure-stack-updates.md#using-the-update-tile-to-manage-updates)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

- Azure Stack での更新プログラム管理の概要については、「[Azure Stack での更新プログラムの管理概要](azure-stack-updates.md)」を参照してください。  
- Azure Stack に更新プログラムを適用する方法については、「[Azure Stack で更新を適用する](azure-stack-apply-updates.md)」を参照してください。
- Azure Stack 統合システムのサービス ポリシーについて、およびサポートを受けられる状態にシステムを維持するために必要な作業について確認するには、「[Azure Stack サービス ポリシー](azure-stack-servicing-policy.md)」を参照してください。  
- 特権エンドポイント (PEP) を使用して更新プログラムを監視および再開するには、「[特権エンドポイントを使用して Azure Stack での更新プログラムをモニターする](azure-stack-monitor-update.md)」をご覧ください。  
