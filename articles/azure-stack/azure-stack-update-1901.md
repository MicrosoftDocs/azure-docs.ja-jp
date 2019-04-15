---
title: Azure Stack 1901 更新プログラム | Microsoft Docs
description: 新機能、既知の問題、更新プログラムをダウンロードする場所など、Azure Stack 統合システムの 1901 更新プログラムについて説明します。
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
ms.date: 03/27/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 03/27/2019
ms.openlocfilehash: 00eb4fc3eb0b2e7120208e6318bf35fc2cc6f188
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58649409"
---
# <a name="azure-stack-1901-update"></a>Azure Stack 1901 更新プログラム

*適用対象:Azure Stack 統合システム*

この記事では、1901 更新プログラム パッケージの内容について説明します。 更新プログラムには、このバージョンの Azure Stack に対する機能強化、修正、新機能が含まれています。 またこの記事では、このリリースの既知の問題についても説明し、更新プログラムをダウンロードできるリンクも掲載しています。 既知の問題は、更新プロセスに直接関係する問題と、ビルド (インストール後) に関する問題に分けられています。

> [!IMPORTANT]  
> 更新プログラム パッケージは、Azure Stack 統合システム専用です。 Azure Stack Development Kit にこの更新プログラム パッケージは適用しないでください。

## <a name="build-reference"></a>ビルドのリファレンス

2019 年 2 月 26 日以降、Azure Stack 1901 更新プログラムのビルド番号は **1.1901.0.95** または **1.1901.0.99** です。 次のメモを参照してください。

> [!IMPORTANT]  
> Microsoft は 1811 (1.1811.0.101) から 1901 に更新するお客様が影響を受ける可能性のある問題を検出し、この問題を解決するために、更新された 1901 パッケージ をリリースしました。ビルド番号は 1.1901.0.99 です (1.1901.0.95 から更新)。 1.1901.0.95 に更新済みのお客様は、追加のアクションは不要です。
>
> 1811 を使用している接続済みのお客様には、新しい 1901 (1.1901.0.99) パッケージが管理者ポータルに自動的に表示され、準備ができたらインストールする必要があります。 接続していないお客様は、[こちらの説明](azure-stack-apply-updates.md)と同じ方法を使用して、新しい 1901 パッケージをダウンロードしてインポートできます。
>
> いずれかのバージョンの 1901 を使用しているお客様は、完全パッケージまたは修正プログラムを次回インストールするときに影響を受けません。

## <a name="hotfixes"></a>修正プログラム

Azure Stack では、修正プログラムが定期的にリリースされます。 Azure Stack を 1901 に更新する前に、必ず 1811 用の[最新の Azure Stack 修正プログラム](#azure-stack-hotfixes)をインストールしてください。

Azure Stack 修正プログラムを適用できるのは Azure Stack 統合システムのみです。ASDK には修正プログラムをインストールしないでください。

> [!TIP]  
> 以下の *RSS* または *Atom* フィードに登録して、Azure Stack 修正プログラムの最新情報を入手してください。
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Azure Stack 修正プログラム

既に 1901 があり、まだいずれの修正プログラムもインストールしていない場合は、最初に 1901 修正プログラムをインストールすることなく [1902 を直接インストールする](azure-stack-update-1902.md)ことができます。

- **1809**: [KB 4481548 – Azure Stack 修正プログラム 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**: 最新の修正プログラムはありません。
- **1901**: [KB 4495662 – Azure Stack 修正プログラム 1.1901.3.105](https://support.microsoft.com/help/4495662)

## <a name="prerequisites"></a>前提条件

> [!IMPORTANT]
> 1901 に更新する前に 1811 用の[最新の Azure Stack 修正プログラム](#azure-stack-hotfixes) (ある場合) をインストールしてください。 既に 1901 があり、まだいずれの修正プログラムもインストールしていない場合は、最初に 1901 修正プログラムをインストールすることなく 1902 を直接インストールすることができます。

- この更新プログラムのインストールを開始する前に、次のパラメーターを指定して [Test-AzureStack](azure-stack-diagnostic-test.md) を実行して Azure Stack の状態を確認し、見つかったすべての操作上の問題 (すべての警告とエラーを含む) を解決します。 また、アクティブなアラートを確認し、アクションが必要なアラートを解決します。

    ```powershell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

- Azure Stack を System Center Operations Manager (SCOM) で管理している場合は、1901 を適用する前に、Microsoft Azure Stack 用の管理パックをバージョン 1.0.3.11 に更新してください。

## <a name="new-features"></a>新機能

この更新プログラムには、Azure Stack に対する次の新機能と機能強化が含まれています。

- Azure Stack 上のマネージド イメージにより、生成された VM (アンマネージドとマネージドの両方) でマネージド イメージ オブジェクトを作成できます。このオブジェクトは、以後、マネージド ディスク VM のみ作成できます。 詳細については、[Azure Stack のマネージド ディスク](user/azure-stack-managed-disk-considerations.md#managed-images)に関するページを参照してください。

- **AzureRm 2.4.0**
   * **AzureRm.Profile**  
         バグの修正 - 保存済みのトークンを正しく逆シリアル化するための `Import-AzureRmContext`。  
   * **AzureRm.Resources**  
         バクの修正 - リソースの種類別に大文字と小文字を区別せずクエリを行うための `Get-AzureRmResource`。  
   * **Azure.Storage**  
         AzureRm ロールアップ モジュールに、**api-version 2017-07-29** をサポートする既に公開済みのバージョン 4.5.0 が組み込まれました。  
   * **AzureRm.Storage**  
         AzureRm ロールアップ モジュールに、**api-version 2017-10-01** をサポートする既に公開済みのバージョン 5.0.4 が組み込まれました。  
   * **AzureRm.Compute**  
         `New-AzureRmVM` と `New-AzureRmVmss` にシンプルなパラメーター セットが追加されました。`-Image` パラメーターは、ユーザー イメージの指定をサポートします。  
   * **AzureRm.Insights**  
         AzureRm ロールアップ モジュールに、メトリック、メトリック定義リソース タイプ用の **api-version 2018-01-01** をサポートする既に公開済みのバージョン 5.1.5 が組み込まれました。

- **AzureStack 1.7.1**: これは破壊的変更を伴うリリースです。 重大な変更について詳しくは、 https://aka.ms/azspshmigration171 を参照してください。
   * **Azs.Backup.Admin モジュール**  
         重大な変更:バックアップが証明書ベースの暗号化モードに変更されました。 対称キーのサポートは非推奨となりました。  
   * **Azs.Fabric.Admin モジュール**  
         `Get-AzsInfrastructureVolume` は非推奨となりました。 新しいコマンドレット `Get-AzsVolume` を使用してください。  
         `Get-AzsStorageSystem` は非推奨となりました。  新しいコマンドレット `Get-AzsStorageSubSystem` を使用してください。  
         `Get-AzsStoragePool` は非推奨となりました。 `StorageSubSystem` オブジェクトには、容量のプロパティが含まれます。  
   * **Azs.Compute.Admin モジュール**  
         バグの修正 - `Add-AzsPlatformImage`、`Get-AzsPlatformImage`: 成功パスでのみ `ConvertTo-PlatformImageObject` を呼び出します。  
         バグの修正 - `Add-AzsVmExtension`、`Get-AzsVmExtension`: 成功パスでのみ ConvertTo-VmExtensionObject を呼び出します。  
   * **Azs.Storage.Admin モジュール**  
         バグの修正 - 新しい Storage クォータでは、値が未指定の場合に既定値が使用されます。

更新されたモジュールのリファレンスを確認する場合、[Azure Stack モジュール リファレンス](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0&viewFallbackFrom=azurestackps-1.7.0)に関するページを参照してください。

## <a name="fixed-issues"></a>修正された問題

- ポータルで、ポリシー ベースの VPN ゲートウェイを作成するオプションが表示されていた問題が修正されました。このオプションは、Azure Stack ではサポートされていません。 このオプションがポータルから削除されました。

<!-- 16523695 – IS, ASDK -->
- Virtual Network の DNS 設定が **[Use Azure Stack DNS]\(Azure Stack DNS を使用\)** から **[Custom DNS]\(カスタム DNS\)** に更新された後、新しい設定でインスタンスが更新されないという問題が修正されました。

- <!-- 3235634 – IS, ASDK -->
  **v2** サフィックスを含むサイズ (**Standard_A2_v2** など) で VM をデプロイする場合に、サフィックスを **Standard_A2_v2** (小文字の v) で指定しなければならないという問題が修正されました。 グローバル Azure と同様に、**Standard_A2_V2** (大文字の V) を使用できるようになりました。

<!--  2795678 – IS, ASDK --> 
- ポータルを使用して Premium VM サイズ (DS、Ds_v2、FS、FSv2) の仮想マシン (VM) を作成した場合に警告が発生するという問題が修正されました。 VM は Standard ストレージ アカウントで作成されました。 これは、機能、IOP、または課金には影響しませんが、警告は修正されました。

<!-- 1264761 - IS ASDK -->  
- 次のアラートを生成していた**正常性コントローラー**コンポーネントに関する問題が修正されました。 次のアラートは無視してもかまいません。

    - アラート #1:
       - 名前: インフラストラクチャ ロールの異常
       - 重大度: 警告
       - コンポーネント: 正常性コントローラー
       - 説明: 正常性コントローラーのハートビート スキャナーは使用できません。 これは、正常性レポートとメトリックに影響する可能性があります。  

    - アラート #2:
       - 名前: インフラストラクチャ ロールの異常
       - 重大度: 警告
       - コンポーネント: 正常性コントローラー
       - 説明: 正常性コントローラーの障害スキャナーは使用できません。 これは、正常性レポートとメトリックに影響する可能性があります。


<!-- 3507629 - IS, ASDK --> 
- [[compute quota types]\(Compute クォータの種類\)](azure-stack-quota-types.md#compute-quota-types) でマネージド ディスク クォータの値を 0 に設定した場合に、既定値の 2048 GiB と同等になるという問題が修正されました。 クォータ値 0 が有効になるようになりました。

<!-- 2724873 - IS --> 
- スケール ユニットの管理に PowerShell コマンドレットの **Start-AzsScaleUnitNode** または **Stop-AzsScaleunitNode** を使用した場合に、スケール ユニットを開始または停止しようとすると、1 回目は失敗する可能性があるという問題が修正されました。

<!-- 2724961- IS ASDK --> 
- サブスクリプション設定で **Microsoft.Insight** リソース プロバイダーを登録し、ゲスト OS 診断を有効にした Windows VM を作成した場合に、VM の概要ページの CPU 使用率グラフにメトリック データが表示されないという問題が修正されました。 データが正しく表示されるようになりました。

- 同じ特権エンドポイント (PEP) セッションで **Test-AzureStack** を実行した後に **Get-AzureStackLog** コマンドレットの実行が失敗するという問題が修正されました。 **Test-AzureStack** を実行したのと同じ PEP セッションを使用できるようになりました。

<!-- bug 3615401, IS -->
- スケジューラ サービスが予期せず無効状態になる自動バックアップに関する問題が修正されました。 

<!--2850083, IS ASDK -->
- **[Reset Gateway]\(ゲートウェイのリセット\)** ボタンが Azure Stack ポータルから削除されました。このボタンをクリックすると、エラーがスローされていました。 Azure Stack には、各テナント VPN ゲートウェイの専用 VM インスタンスではなくてマルチテナント ゲートウェイがあるため、このボタンは Azure Stack では機能しません。このため、混乱を防ぐために削除されました。 

<!-- 3209594, IS ASDK -->
- **[有効なセキュリティ ルール]** リンクが **[ネットワーク プロパティ]** ブレードから削除されました。これは、この機能が Azure Stack でサポートされないためです。 このリンクが表示されていたため、この機能がサポートされているような印象がありましたが、この機能は動作しません。 混乱を避けるために、このリンクは削除されました。

<!-- 3139614 | IS -->
- Azure Stack の更新を OEM から適用した後に、**利用可能な更新プログラムがあります**の通知が Azure Stack 管理者ポータルに表示されないという問題が修正されました。

## <a name="changes"></a>変更点

<!-- 3083238 IS -->
- この更新プログラムでのセキュリティ強化により、ディレクトリ サービス ロールのバックアップ サイズが増えます。 外部の保存場所のサイズに関する最新のガイダンスについては、[Infrastructure Backup に関するドキュメント](azure-stack-backup-reference.md#storage-location-sizing)を参照してください。 この変更により、転送するデータのサイズが増えることから、バックアップの所要時間も長くなります。 この変更は、統合システムに影響します。 

- 2019 年 1 月以降、Kubernetes クラスターを、Active Directory フェデレーション サービス (AD FS) に登録されている、接続済みの Azure Stack スタンプにデプロイできます (インターネット アクセスが必要です)。 新しい Kubernetes Marketplace アイテムをダウンロードするには、[この](azure-stack-solution-template-kubernetes-cluster-add.md)手順に従います。 Kubernetes クラスターをデプロイするには、[この](user/azure-stack-solution-template-kubernetes-adfs.md)手順に従います。 ターゲット システムが ADD と AD FS のどちらに登録されているか示すための新しいパラメーターに注意してください。 AD FS の場合、デプロイ証明書が格納される Key Vault パラメーターを入力するための新しいフィールドを使用できます。

   AD FS サポートを使用する場合でも、Kubernetes クラスターをデプロイするにはインターネット アクセスが必要となるので注意してください。

- Azure Stack に更新プログラムまたは修正プログラムをインストールした後、1 つ以上の ID アプリケーションに新しいアクセス許可を付与する必要がある新しい機能が導入されることがあります。 これらのアクセス許可を付与するには、ホーム ディレクトリへの管理アクセスが必要です。このため、自動的にアクセス許可を付与することはできません。 例: 

   ```powershell
   $adminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"
   $homeDirectoryTenantName = "<homeDirectoryTenant>.onmicrosoft.com" # This is the primary tenant Azure Stack is registered to

   Update-AzsHomeDirectoryTenant -AdminResourceManagerEndpoint $adminResourceManagerEndpoint `
     -DirectoryTenantName $homeDirectoryTenantName -Verbose
   ```

- Azure Stack のキャパシティを正確に計画するための新しい考慮事項があります。 1901 更新プログラムでは、作成できる仮想マシンの合計数に制限があります。  この制限は、ソリューションの不安定性を回避するための一時的なものです。 VM の数が多い場合の安定性の問題の原因は対処されていますが、修復のための具体的なタイムラインは決定されていません。 1901 更新プログラムでは、VM の数が、サーバーごとに 60 個、ソリューションの合計で 700 個に制限されています。  たとえば、8 サーバーの Azure Stack VM の制限は 480 個 (8 * 60) になります。  12 ～ 16 サーバーの Azure Stack ソリューションでは、制限は 700 個になります。 この制限は、オペレーターがスタンプで維持したい回復性の予約や仮想と物理の CPU の比率など、コンピューティング容量に関するすべての考慮事項を念頭に置いて作成されています。 詳細については、Capacity Planner の新しいリリースに関するページを参照してください。  
VM のスケールの上限に達すると、次のエラー コードが返されます: VMsPerScaleUnitLimitExceeded、VMsPerScaleUnitNodeLimitExceeded。 
 

- Compute API のバージョンが 2017-12-01 に更新されました。

- インフラストラクチャのバックアップで、バックアップ データの暗号化に公開キーのみを含む証明書 (.CER) が必要になりました。 対称暗号化キーのサポートは 1901 以降非推奨となりました。 1901 に更新する前にインフラストラクチャ バックアップが構成された場合、暗号キーはそのまま残ります。 バックアップ設定を更新するために、下位互換性をサポートする少なくとも 2 つの追加更新プログラムがあります。 詳細については、[Azure Stack インフラストラクチャ バックアップのベスト プラクティス](azure-stack-backup-best-practices.md)に関するページを参照してください。

## <a name="common-vulnerabilities-and-exposures"></a>共通脆弱性識別子

この更新では、次のセキュリティ更新プログラムがインストールされます。  

- [CVE-2018-8477](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8477)
- [CVE-2018-8514](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8514)
- [CVE-2018-8580](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8580)
- [CVE-2018-8595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8595)
- [CVE-2018-8596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8596)
- [CVE-2018-8598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8598)
- [CVE-2018-8621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8621)
- [CVE-2018-8622](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8622)
- [CVE-2018-8627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8627)
- [CVE-2018-8637](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8637)
- [CVE-2018-8638](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8638)
- [ADV190001](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190001)
- [CVE-2019-0536](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0536)
- [CVE-2019-0537](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0537)
- [CVE-2019-0545](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0545)
- [CVE-2019-0549](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0549)
- [CVE-2019-0553](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0553)
- [CVE-2019-0554](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0554)
- [CVE-2019-0559](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0559)
- [CVE-2019-0560](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0560)
- [CVE-2019-0561](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0561)
- [CVE-2019-0569](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0569)
- [CVE-2019-0585](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0585)
- [CVE-2019-0588](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0588)


これらの脆弱性の詳細については、上記のリンクをクリックするか、Microsoft サポート技術情報の記事 [4480977](https://support.microsoft.com/en-us/help/4480977) を参照してください。

## <a name="known-issues-with-the-update-process"></a>更新プロセスに関する既知の問題

- [Test-AzureStack](azure-stack-diagnostic-test.md) を実行しているときに、**AzsInfraRoleSummary** または **AzsPortalApiSummary** テストに失敗すると、**Test-AzureStack** を `-Repair` フラグで実行するように求められます。  このコマンドを実行すると、次のエラー メッセージが表示されて失敗します: `Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`

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
- 更新プログラム 1901 の適用後、Managed Disks を使用した VM をデプロイすると、次の問題が発生する可能性があります。

   - 1808 更新の前にサブスクリプションが作成された場合、Managed Disks を使用した VM をデプロイすると、内部エラー メッセージが出て失敗することがあります。 このエラーを解決するには、サブスクリプションごとに次の手順に従ってください。
      1. テナント ポータルで、**[サブスクリプション]** に移動して、サブスクリプションを検索します。 **[リソース プロバイダー]** を選択し、**[Microsoft.Compute]** を選択してから、**[再登録]** をクリックします。
      2. 同じサブスクリプションで、**[アクセス制御 (IAM)]** に移動し、**AzureStack-DiskRP-Client** がリストに含まれていることを確認します。
   - マルチテナント環境を構成した場合、ゲスト ディレクトリに関連付けられているサブスクリプションで VM をデプロイすると、内部エラー メッセージが出て失敗することがあります。 このエラーを解決するには、[この記事](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory)にある手順に従って、各ゲスト ディレクトリを構成します。

- SSH の認可を有効にして作成した Ubuntu 18.04 VM では、SSH キーを使用してログインすることはできません。 回避策として、プロビジョニング後に Linux 拡張機能用の VM アクセスを使用して SSH キーを実装するか、パスワードベースの認証を使用してください。

### <a name="networking"></a>ネットワーク  

<!-- 3239127 - IS, ASDK -->
- Azure Stack ポータルで、VM インスタンスにアタッチされたネットワーク アダプターにバインドされている IP 構成の静的 IP アドレスを変更すると、次の内容の警告メッセージが表示されます。 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`

    このメッセージは無視してかまいません。たとえ VM インスタンスが再起動しなくても IP アドレスは変更されます。

<!-- 3632798 - IS, ASDK -->
- ポータルで受信セキュリティ規則を追加し、**[Service Tag]\(サービス タグ\)** をソースとして選択すると、Azure Stack では利用できないオプションがいくつか **[Source Tag]\(ソース タグ\)** リストに表示されます。 Azure Stack で有効なのは次のオプションだけです。

  - **Internet**
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

Azure Stack 1901 更新プログラム パッケージは、[ここから](https://aka.ms/azurestackupdatedownload)ダウンロードできます。 

Azure Stack デプロイでは、接続されたシナリオに限り、セキュリティで保護されたエンドポイントが定期的にチェックされ、お客様のクラウド用の更新プログラムが入手可能かどうかが自動的に通知されます。 詳細については、[Azure Stack の更新プログラムの管理](azure-stack-updates.md#using-the-update-tile-to-manage-updates)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

- Azure Stack での更新プログラム管理の概要については、「[Azure Stack での更新プログラムの管理概要](azure-stack-updates.md)」を参照してください。  
- Azure Stack に更新プログラムを適用する方法については、「[Azure Stack で更新を適用する](azure-stack-apply-updates.md)」を参照してください。
- Azure Stack 統合システムのサービス ポリシーについて、およびサポートを受けられる状態にシステムを維持するために必要な作業について確認するには、「[Azure Stack サービス ポリシー](azure-stack-servicing-policy.md)」を参照してください。  
- 特権エンドポイント (PEP) を使用して更新プログラムを監視および再開するには、「[特権エンドポイントを使用して Azure Stack での更新プログラムをモニターする](azure-stack-monitor-update.md)」をご覧ください。  
