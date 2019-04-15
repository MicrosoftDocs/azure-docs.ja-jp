---
title: Azure Stack 1811 更新プログラム | Microsoft Docs
description: 新機能、既知の問題、更新プログラムをダウンロードする場所など、Azure Stack 統合システムの 1811 更新プログラムについて説明します。
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
ms.date: 04/09/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: 138913414a8e45084d498a0c7b2e864bc443197f
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2019
ms.locfileid: "59362059"
---
# <a name="azure-stack-1811-update"></a>Azure Stack 1811 更新プログラム

*適用対象:Azure Stack 統合システム*

この記事では、1811 更新プログラム パッケージの内容について説明します。 更新プログラム パッケージには、このバージョンの Azure Stack に対する機能強化、修正、新機能が含まれています。 またこの記事では、このリリースの既知の問題について説明し、更新プログラムをダウンロードできるリンクも掲載しています。 既知の問題は、更新プロセスに直接関係する問題と、ビルド (インストール後) に関する問題に分けられています。

> [!IMPORTANT]  
> 更新プログラム パッケージは、Azure Stack 統合システム専用です。 Azure Stack Development Kit にこの更新プログラム パッケージは適用しないでください。

## <a name="build-reference"></a>ビルドのリファレンス

Azure Stack 1811 更新プログラムのビルド番号は **1.1811.0.101** です。

## <a name="hotfixes"></a>修正プログラム

Azure Stack では、修正プログラムが定期的にリリースされます。 Azure Stack を 1811 に更新する前に、必ず 1809 用の[最新の Azure Stack 修正プログラム](#azure-stack-hotfixes)をインストールしてください。

> [!TIP]  
> 以下の *RSS* または *Atom* フィードに登録して、Azure Stack 修正プログラムの最新情報を入手してください。
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Azure Stack 修正プログラム

- **1809**: [KB 4481548 – Azure Stack 修正プログラム 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**: 最新の修正プログラムはありません。

## <a name="prerequisites"></a>前提条件

> [!IMPORTANT]
> 1811 更新プログラムのインストール中は、管理者ポータルのすべてのインスタンスを必ず閉じなければいけません。 ユーザー ポータルは開いたままでかまいませんが、管理ポータルは閉じる必要があります。

- Azure Stack の拡張機能ホスト用にお客様の Azure Stack デプロイを準備します。 次のガイダンスを使用して、お客様のシステムを準備します。「[Azure Stack の拡張機能ホストを準備する](azure-stack-extension-host-prepare.md)」。 
 
- 1811 に更新する前に 1809 用の[最新の Azure Stack 修正プログラム](#azure-stack-hotfixes)をインストールしてください。

- この更新プログラムのインストールを開始する前に、次のパラメーターを指定して [Test-AzureStack](azure-stack-diagnostic-test.md) を実行して Azure Stack の状態を確認し、見つかったすべての操作上の問題 (すべての警告とエラーを含む) を解決します。 また、アクティブなアラートを確認し、アクションが必要なアラートを解決します。  

    ```powershell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

    拡張機能ホストの要件が満たされていないと、`Test-AzureStack` の出力で次のメッセージが表示されます。 
  
    `To proceed with installation of the 1811 update, you will need to import 
    the SSL certificates required for Extension Host, which simplifies network 
    integration and increases the security posture of Azure Stack. Refer to this 
    link to prepare for Extension Host:
    https://docs.microsoft.com/azure/azure-stack/azure-stack-extension-host-prepare`

- Azure Stack 1811 更新プログラムを適用するには、お客様の Azure Stack 環境に、拡張機能ホストの必須の証明書を適切にインポートしておく必要があります。 1811 更新プログラムのインストールを続行するには、拡張機能ホストに必要な SSL 証明書をインポートする必要があります。 証明書のインポートについては、[こちらのセクション](azure-stack-extension-host-prepare.md#import-extension-host-certificates)を参照してください。

    すべての警告を無視して 1811 更新プログラムのインストールを続行した場合、およそ 1 時間以内に次のメッセージが表示されて、更新に失敗します。   
 
    `The required SSL certificates for the Extension Host have not been found.
    The Azure Stack update will halt. Refer to this link to prepare for 
    Extension Host: https://docs.microsoft.com/azure/azure-stack/azure-stack-extension-host-prepare,
    then resume the update.
    Exception: The Certificate path does not exist: [certificate path here]` 
 
    拡張機能ホストの必須の証明書を適切にインポートしたら、管理者ポータルから 1811 の更新を再開することができます。 Microsoft では、Azure Stack のオペレーターに対し、更新プロセス中にメンテナンス期間をスケジュールするよう推奨していますが、拡張機能ホストの証明書が存在しないことに起因するエラーが、既存のワークロードやサービスに影響することはないと考えられます。  

    この更新プログラムのインストール中、拡張機能ホストが構成されている間、Azure Stack ユーザー ポータルは利用できません。 拡張機能ホストの構成には最大で 5 時間かかる場合があります。 その間、[Azure Stack 管理者 PowerShell または特権エンドポイント](azure-stack-monitor-update.md)を使用して、更新プログラムの状態を確認したり、失敗した更新プログラムのインストールを再開したりできます。

- Azure Stack を System Center Operations Manager (SCOM) で管理している場合は、1811 を適用する前に、Microsoft Azure Stack 用の管理パックをバージョン 1.0.3.11 に更新してください。

## <a name="new-features"></a>新機能

この更新プログラムには、Azure Stack に対する次の新機能と機能強化が含まれています。

- このリリースでは、[拡張機能ホスト](azure-stack-extension-host-prepare.md)が有効になります。 拡張機能ホストによって、ネットワークの統合が簡素化され、Azure Stack のセキュリティ体制が強化されます。

- Active Directory Federated Services (AD FS) でのデバイス認証に対するサポートが追加されました (特に Azure CLI を使用している場合)。 詳細については、「[Azure Stack での Azure CLI による API バージョンのプロファイルの使用](./user/azure-stack-version-profiles-azurecli2.md)」を参照してください。

- Active Directory Federated Services (AD FS) でクライアント シークレットを使用することにより、サービス プリンシパルに対するサポートが追加されました。 詳細については、[AD FS のサービス プリンシパルの作成](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs)に関するページを参照してください。

- このリリースでは、サポートされる Azure Storage Service API バージョンが追加されました (**2017-07-29** と **2017-11-09**)。 Azure Storage Resource Provider API バージョンについてもサポートが追加されています (**2016-05-01**、**2016-12-01**、**2017-06-01**、**2017-10-01**)。 詳しくは、「[Azure Stack Storage:違いと考慮事項](./user/azure-stack-acs-differences.md)」をご覧ください。

- ADFS のサービス プリンシパルを更新したり削除したりするための新しい特権エンドポイント コマンドが追加されました。 詳細については、[AD FS のサービス プリンシパルの作成](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs)に関するページを参照してください。

- Azure Stack オペレーターがスケール ユニット ノードを開始、停止、シャットダウンできる新しいスケール ユニット ノード操作が追加されました。 詳細については、「[Azure Stack でのスケール ユニット ノードの操作](azure-stack-node-actions.md)」を参照してください。

- 環境の詳しい登録情報を表示する新しいリージョン プロパティ ブレードが追加されました。 この情報は、管理者ポータルの既定のダッシュボードにある **[Region Management]\(リージョン管理\)** タイルをクリックし、**[プロパティ]** を選択することで表示できます。

- 物理マシンとの通信に使用される BMC 資格情報をユーザー名とパスワードで更新するための新しい特権エンドポイント コマンドが追加されました。 詳細については、「[ベースボード管理コントローラー \(BMC) のパスワードを更新する](azure-stack-rotate-secrets.md)」を参照してください。

- Azure portal と同様に、管理者ポータルとユーザー ポータルの右上隅にあるサポート アイコン (疑問符) とヘルプを通じて Azure ロードマップにアクセスできるようになりました。

- 非接続ユーザー向けの Marketplace 管理エクスペリエンスが強化されました。 非接続環境で Marketplace アイテムを発行するアップロード プロセスが簡素化され、画像と Marketplace パッケージを別々にアップロードするのではなく、1 つのステップで行えるようになりました。 また、アップロードされた製品が Marketplace の管理ブレードに表示されるようになります。 詳細については、[こちらのセクション](azure-stack-download-azure-marketplace-item.md#import-the-download-and-publish-to-azure-stack-marketplace-1811-and-higher)をご覧ください。 

- このリリースでは、[Azure Stack シークレットのローテーション](azure-stack-rotate-secrets.md)中に外部証明書だけをローテーションする機能が追加され、シークレットのローテーションに必要なメンテナンス期間が短縮されます。

- [Azure Stack PowerShell](azure-stack-powershell-install.md) がバージョン 1.6.0 に更新されました。 この更新には、Azure Stack の新しいストレージ関連機能のサポートが含まれます。 詳細については、[PowerShell ギャラリーで Azure Stack Administration Module 1.6.0](https://www.powershellgallery.com/packages/AzureStack/1.6.0) のリリース ノートを参照してください。Azure Stack PowerShell の更新とインストールについては、「[PowerShell for Azure Stack をインストールする](azure-stack-powershell-install.md)」を参照してください。

- Azure Stack ポータルを使用して仮想マシンを作成するときに、今後は Managed Disks が既定で有効になります。 Managed Disks で VM 作成エラーを回避するために必要な追加の手順については、[既知の問題](#known-issues-post-installation)に関するセクションを参照してください。

- このリリースでは、Azure Stack オペレーター向けにアラートの**修復**操作が採用されています。 1811 のいくつかのアラートには、**[修復]** ボタンが表示され、そのボタンを選択することで問題を解決できます。 詳細については、「[Azure Stack での正常性およびアラートの監視](azure-stack-monitor-health.md)」を参照してください。

- Azure Stack で更新プログラムのエクスペリエンスに更新します。 更新プログラムの機能強化には以下が含まれます。 
  - 進行中の更新および完了した更新の追跡向上のために、更新履歴から更新を分割するタブ。
  - 現在のバージョンと OEM バージョンおよび最終更新日に対する新しいアイコンとレイアウトによる、要点セクションの強化された状態の視覚化。
  - リリース ノート列の **[表示]** リンクでは、一般的な更新プログラム ページではなく、その更新プログラムに固有のドキュメントに直接移動します。
  - 各更新プログラムの実行日時を特定するために使用される **[更新の履歴]** タブと、強化されたフィルター処理機能。  
  - オンラインの Azure Stack スケール ユニットでは、更新プログラムが利用可能になると**利用可能な更新プログラム**を自動的に受け取ります。
  - オフラインの Azure Stack スケール ユニットでは、前と同じように、更新プログラムをインポートできます。 
  - ポータルから JSON ログをダウンロードするプロセスに変更はありません。 Azure Stack オペレーターは、手順を展開して進行状況を見ることができます。

    詳しくは、「[Azure Stack で更新を適用する](azure-stack-apply-updates.md)」をご覧ください。


## <a name="fixed-issues"></a>修正された問題

<!-- TBD - IS ASDK --> 
- パブリック IP アドレス使用量メーターのデータが、レコードの作成日時を示す **TimeDate** スタンプではなく各レコードに対して同じ **EventDateTime** 値を示す問題が修正されました。 このデータを使用してパブリック IP アドレスの使用を正確に算出できるようになりました。

<!-- 3099544 – IS, ASDK --> 
- Azure Stack ポータルを使用して新しい仮想マシン (VM) を作成するときに発生していた問題を修正しました。 VM サイズを選択すると、**[米国ドル/月]** 列に**利用不可**というメッセージが表示されていました。 今後、この列は表示されません。VM 価格列の表示は、Azure Stack ではサポートされていません。

<!-- 2930718 - IS ASDK --> 
- 管理者ポータルで、ユーザーのサブスクリプションの詳細にアクセスした後、ブレードを閉じて、**[最近]** をクリックしても、ユーザーのサブスクリプション名が表示されませんでしたが、この問題は修正されました。 今後は、ユーザーのサブスクリプション名が表示されます。

<!-- 3060156 - IS ASDK --> 
- 管理者ポータルとユーザー ポータルの両方で、ポータルの設定をクリックした後、**[すべての設定とプライベート ダッシュボードを削除]** を選んでも、想定どおりに機能せず、エラー通知が表示されていましたが、この問題は修正されました。 このオプションは正しく機能するようになりました。

<!-- 2930799 - IS ASDK --> 
- 管理者ポータルとユーザー ポータルの両方で、**[すべてのサービス]** の下に、資産 **[DDoS protection プラン]** が間違って一覧表示されていましたが、この問題は修正されました。 これは Azure Stack でご利用になれません。 この一覧は削除されました。
 
<!--2760466 – IS  ASDK --> 
- 新しい Azure Stack 環境をインストールするときに発生していた問題 ("**アクティブ化が必要**" であること示すアラートが表示されない) が修正されました。 今後は正しく表示されます。

<!--1236441 – IS  ASDK --> 
- ADFS の使用時に RBAC ポリシーをユーザー グループに適用できない問題が修正されました。

<!--3463840 - IS, ASDK --> 
- パブリック VIP ネットワークからファイル サーバーにアクセスできないことが原因でインフラストラクチャ バックアップに失敗する問題が修正されました。 この修正により、インフラストラクチャ バックアップ サービスはパブリック インフラストラクチャ ネットワークに戻されます。 この問題を解決する最新の [1809 用の Azure Stack 修正プログラム](#azure-stack-hotfixes)を提供してある場合、1811 更新プログラムによってそれ以上の変更が加えられることはありません。 

<!-- 2967387 – IS, ASDK --> 
- Azure Stack の管理ポータルまたはユーザー ポータルへのサインインに使用したアカウントが **[識別されないユーザー]** と表示されていた問題が修正されました。 このメッセージは、アカウントに "**名**" と "**姓**" がどちらも指定されていない場合に表示されていました。   

<!--  2873083 - IS ASDK --> 
- Internet Explorer の使用時にポータルを使って仮想マシン スケール セット (VMSS) を作成すると、**[インスタンス サイズ]** ドロップダウンが正しく読み込まれませんでしたが、この問題は修正されました。 現在、このブラウザーは正しく動作します。  

<!-- 3190553 - IS ASDK -->
- インフラストラクチャ ロール インスタンスが利用できない、またはスケール ユニットがオフラインであることを示すわずらわしいアラートが表示される問題を修正しました。

<!-- 2724961 - IS ASDK -->
- VM の概要ページに VM のメトリック グラフが正しく表示されない問題が修正されました。 

## <a name="changes"></a>変更点

- 1811 では、プランのクォータを表示したり編集したりするための新しい方法が導入されました。 詳細については、「[[View an existing quota]\(既存のクォータの表示\)](azure-stack-quota-types.md#view-an-existing-quota)」を参照してください。

<!-- 3083238 IS -->
- この更新プログラムでのセキュリティ強化により、ディレクトリ サービス ロールのバックアップ サイズが増えます。 外部の保存場所のサイズに関する最新のガイダンスについては、[Infrastructure Backup に関するドキュメント](azure-stack-backup-reference.md#storage-location-sizing)を参照してください。 この変更により、転送するデータのサイズが増えることから、バックアップの所要時間も長くなります。 この変更は、統合システムに影響します。 

- BitLocker 回復キーを取得するための既存の PEP コマンドレット Get-AzsCsvsRecoveryKeys の名前が、1811 では Get-AzsRecoveryKeys に変更されました。 BitLocker 回復キーを取得する方法の詳細については、[キーを取得する方法の手順](azure-stack-security-bitlocker.md)に関するページを参照してください。

## <a name="common-vulnerabilities-and-exposures"></a>共通脆弱性識別子

この更新では、次のセキュリティ更新プログラムがインストールされます。  

- [CVE-2018-8256](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8256)
- [CVE-2018-8407](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8407)
- [CVE-2018-8408](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8408)
- [CVE-2018-8415](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8415)
- [CVE-2018-8417](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8417)
- [CVE-2018-8450](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8450)
- [CVE-2018-8471](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8471)
- [CVE-2018-8476](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8476)
- [CVE-2018-8485](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8485)
- [CVE-2018-8544](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8544)
- [CVE-2018-8547](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8547)
- [CVE-2018-8549](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8549)
- [CVE-2018-8550](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8550)
- [CVE-2018-8553](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8553)
- [CVE-2018-8561](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8561)
- [CVE-2018-8562](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8562)
- [CVE-2018-8565](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8565)
- [CVE-2018-8566](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8566)
- [CVE-2018-8584](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8584)

これらの脆弱性の詳細については、上記のリンクをクリックするか、Microsoft サポート技術情報の記事 [4478877](https://support.microsoft.com/help/4478877) を参照してください。

## <a name="known-issues-with-the-update-process"></a>更新プロセスに関する既知の問題

- 同じ特権エンドポイント (PEP) セッションで、**Test-AzureStack** の実行後に **Get-AzureStackLog** PowerShell コマンドレットを実行すると、**Get-AzureStackLog** が失敗します。 この問題を回避するには、**Test-AzureStack** を実行した PEP セッションを閉じてから、新しいセッションを開いて、**Get-AzureStackLog** を実行します。

- 1811 更新プログラムのインストール中は、その間、管理者ポータルのすべてのインスタンスを必ず閉じておいてください。 ユーザー ポータルは開いたままでかまいませんが、管理ポータルは閉じる必要があります。

- [Test-AzureStack](azure-stack-diagnostic-test.md) を実行しているときに、**AzsInfraRoleSummary** または **AzsPortalApiSummary** テストに失敗すると、**Test-AzureStack** を `-Repair` フラグで実行するように求められます。  このコマンドを実行すると、次のエラー メッセージが表示されて失敗します。`Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`  この問題は今後のリリースで修正される予定です。

- 1811 更新プログラムのインストール中、拡張機能ホストが構成されている間に Azure Stack ユーザー ポータルは利用できません。 拡張機能ホストの構成には最大で 5 時間かかる場合があります。 その間、[Azure Stack 管理者 PowerShell または特権エンドポイント](azure-stack-monitor-update.md)を使用して、更新プログラムの状態を確認したり、失敗した更新プログラムのインストールを再開したりできます。 

- 1811 更新プログラムのインストール中は、ユーザー ポータルのダッシュボードを利用できない場合があり、カスタマイズが失われる可能性があります。 更新の完了後、ポータルの設定を開き、**[既定の設定に戻す]** を選択することで、ダッシュボードを既定の設定に復元することができます。

- [Test-AzureStack](azure-stack-diagnostic-test.md) を実行すると、ベースボード管理コントローラー (BMC) からの警告メッセージが表示されます。 この警告は無視してかまいません。

- <!-- 2468613 - IS --> この更新プログラムのインストール中に、`Error – Template for FaultType UserAccounts.New is missing.` というタイトルのアラートが表示されることがありますが、これらのアラートは無視してかまいません。 この更新プログラムのインストールが完了した後、これらのアラートは自動的に閉じられます。

- <!-- 3139614 | IS --> Azure Stack の更新をお客様の OEM から適用した場合は、Azure Stack 管理者ポータルに「**利用可能な更新プログラムがあります**」という通知が表示されないことがあります。 Microsoft Update をインストールするには、こちらの「[Azure Stack で更新を適用する](azure-stack-apply-updates.md)」に記載されている手順を使用して、手動でダウンロードおよびインポートしてください。

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

<!-- TBD - IS ASDK --> 
- ユーザー サブスクリプションを削除すると、リソースが孤立します。 回避策として、まず、ユーザー リソースまたはリソース グループ全体を削除してから、ユーザー サブスクリプションを削除します。

<!-- TBD - IS ASDK --> 
- Azure Stack ポータルを使用して、サブスクリプションへのアクセス許可を表示することはできません。 この問題を回避するには、[PowerShell を使用してアクセス許可を確認](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan)します。

### <a name="health-and-monitoring"></a>正常性と監視

<!-- 1264761 - IS ASDK -->  
- 以下の詳細情報の**正常性コントローラー** コンポーネントのアラートが表示されることがあります:  

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

    いずれのアラートも無視してかまいません。 時間が経つと自動的に閉じられます。  

### <a name="compute"></a>Compute

- 新しい Windows 仮想マシン (VM) を作成するとき、作業を続行するために **[設定]** ブレードでパブリック受信ポートを選択するよう要求されます。 1811 では、この設定が必須ですが、作用はありません。 この機能は Azure Firewall に依存していますが、Azure Stack には Azure Firewall が実装されていないためです。 **[パブリック受信ポートなし]** または他の任意のオプションを選択して、VM の作成を続行できます。 この設定には一切作用がありません。

- 新しい Windows 仮想マシン (VM) を作成するときに、次のエラーが表示されることがあります。

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   このエラーは、VM でブート診断を有効にしても、ブート診断ストレージ アカウントを削除した場合に発生します。 この問題を回避するには、以前使用したものと同じ名前のストレージ アカウントを再作成します。

- [Dv2 シリーズ VM](./user/azure-stack-vm-considerations.md#virtual-machine-sizes) を作成するときは、D11 14v2 VM ではそれぞれ 4、8、16、32 のデータ ディスクを作成することができます。 ただし、VM の作成ウィンドウには、8、16、32、および 64 のデータ ディスクが表示されます。

- Azure Stack の使用状況記録には、予期しない大文字が含まれている場合があります。たとえば、次のとおりです。

   `{"Microsoft.Resources":{"resourceUri":"/subscriptions/<subid>/resourceGroups/ANDREWRG/providers/Microsoft.Compute/
   virtualMachines/andrewVM0002","location":"twm","tags":"null","additionalInfo":
   "{\"ServiceType\":\"Standard_DS3_v2\",\"ImageType\":\"Windows_Server\"}"}}`

   この例では、リソース グループの名前は **AndrewRG** である必要があります。 この不一致は無視してかまいません。

<!-- 3235634 – IS, ASDK -->
- **v2** サフィックスを含むサイズ (**Standard_A2_v2** など) で VM をデプロイするには、サフィックスを **Standard_A2_v2** (小文字の v) と指定してください。 **Standard_A2_V2** (大文字の V) は使用しないでください。 これは、グローバル Azure で動作し、Azure Stack では不整合になります。

<!-- 2869209 – IS, ASDK --> 
- [**Add-AzsPlatformImage** コマンドレット](/powershell/module/azs.compute.admin/add-azsplatformimage)を使用する場合は、ディスクのアップロード先のストレージ アカウント URI として **-OsUri** パラメーターを使用する必要があります。 ディスクのローカル パスを使用した場合、次のエラーが出てコマンドレットが失敗します。 

    `Long running operation failed with status 'Failed'`

<!--  2795678 – IS, ASDK --> 
- ポータルを使用して Premium VM サイズ (DS、Ds_v2、FS、FSv2) の仮想マシン (VM) を作成すると、VM は Standard ストレージ アカウントで作成されます。 Standard ストレージ アカウントで作成しても、機能、IOPS、課金に影響はありません。 次の警告は無視してかまいません。 

    `You've chosen to use a standard disk on a size that supports premium disks. This could impact operating system performance and is not recommended. Consider using premium storage (SSD) instead.`

<!-- 2967447 - IS, ASDK --> 
- 仮想マシン スケール セット (VMSS) の作成エクスペリエンスでは、デプロイのオプションとして CentOS-based 7.2 が提供されます。 このイメージは Azure Stack では使用できないため、デプロイ用に別のオペレーティング システムを選択するか、またはデプロイする前にオペレーターが Marketplace からダウンロードしておいた別の CentOS イメージを指定する Azure Resource Manager テンプレートをご使用ください。  

<!-- 2724873 - IS --> 
- スケール ユニットの管理に PowerShell コマンドレットの **Start-AzsScaleUnitNode** または **Stop-AzsScaleunitNode** を使った場合、スケール ユニットを開始または停止しようとすると、1 回目は失敗することがあります。 初回実行時にコマンドレットが失敗した場合は、もう一度コマンドレットを実行してください。 2 回目の実行では操作が正常に完了します。 

<!-- TBD - IS ASDK --> 
- VM のデプロイで拡張機能のプロビジョニングに時間がかかりすぎる場合は、プロセスを停止して VM の割り当て解除または削除を試みるのではなく、プロビジョニングをタイムアウトさせる必要があります。  

<!-- 1662991 IS ASDK --> 
- Linux の VM 診断は、Azure Stack でサポートされていません。 VM 診断を有効にして Linux VM を展開すると、展開が失敗します。 診断設定で Linux VM の基本メトリックを有効にした場合も、展開が失敗します。  

<!-- 3507629 - IS, ASDK --> 
- Managed Disks では、2 つの新しい[コンピューティング クォータの種類](azure-stack-quota-types.md#compute-quota-types)を作成して、プロビジョニングできるマネージド ディスクの最大容量を制限します。 既定では、2,048 GiB がマネージド ディスク クォータの種類ごとに割り当てられます。 ただし、次の問題が発生する可能性があります。

   - 更新プログラム 1808 より前に作成されたクォータでは、Managed Disks のクォータは 2,048 GiB が割り当てられているものの、管理者ポータルでは値 0 が表示されます。 値は実際のニーズに基づいて増減することができ、新しく設定したクォータ値が 2,048 GiB の既定値をオーバーライドします。
   - クォータ値を 0 に更新することは、2,048 GiB の既定値と同じことになります。 回避策として、クォータ値を 1 に設定します。

<!-- TBD - IS ASDK --> 
- 更新プログラム 1811 の適用後、Managed Disks を使用した VM をデプロイすると、次の問題が発生する可能性があります。

   - 1808 更新の前にサブスクリプションが作成された場合、Managed Disks を使用した VM をデプロイすると、内部エラー メッセージが出て失敗することがあります。 このエラーを解決するには、サブスクリプションごとに次の手順に従ってください。
      1. テナント ポータルで、**[サブスクリプション]** に移動して、サブスクリプションを検索します。 **[リソース プロバイダー]** を選択し、**[Microsoft.Compute]** を選択してから、**[再登録]** をクリックします。
      2. 同じサブスクリプションで、**[アクセス制御 (IAM)]** に移動し、**[AzureStack-DiskRP-Client]** ロールがリストに含まれていることを確認します。
   - マルチテナント環境を構成した場合、ゲスト ディレクトリに関連付けられているサブスクリプションで VM をデプロイすると、内部エラー メッセージが出て失敗することがあります。 このエラーを解決するには、[この記事](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory)にある手順に従って、各ゲスト ディレクトリを構成します。

- SSH の認可を有効にして作成した Ubuntu 18.04 VM では、SSH キーを使用してログインすることはできません。 回避策として、プロビジョニング後に Linux 拡張機能用の VM アクセスを使用して SSH キーを実装するか、パスワードベースの認証を使用してください。

### <a name="networking"></a>ネットワーク  

<!-- 1766332 - IS ASDK --> 
- **[ネットワーク]** で、**[Create VPN Gateway]\(VPN ゲートウェイの作成\)** をクリックして VPN 接続を設定する場合、VPN の種類として **[ポリシー ベース]** が表示されます。 このオプションを選択しないでください。 Azure Stack では **[ルート ベース]** オプションのみがサポートされています。

<!-- 1902460 - IS ASDK --> 
- Azure Stack では、IP アドレスごとに 1 つの "*ローカル ネットワーク ゲートウェイ*" がサポートされます。 これは、テナントのすべてのサブスクリプションに当てはまります。 最初のローカル ネットワーク ゲートウェイ接続を作成した後に、続いて同じ IP アドレスでローカル ネットワーク ゲートウェイ リソースを作成しようとすると拒否されます。

<!-- 16309153 - IS ASDK --> 
- **自動**の DNS サーバー設定を使用して作成された仮想ネットワークでは、カスタム DNS サーバーへの変更が失敗します。 更新した設定は、その Vnet 内の VM にプッシュされません。

<!-- 2529607 - IS ASDK --> 
- Azure Stack の "*シークレット ローテーション*" 中は、2 分から 5 分、パブリック IP アドレスが到達不能になる期間があります。

<!-- 2664148 - IS ASDK --> 
- テナントが S2S VPN トンネルを使用して仮想マシンにアクセスするシナリオでは、ゲートウェイが既に作成された後でオンプレミスのサブネットがローカル ネットワーク ゲートウェイに追加された場合、接続しようとしても失敗するシナリオが発生する可能性があります。 

- Azure Stack ポータルで、VM インスタンスにアタッチされたネットワーク アダプターにバインドされている IP 構成の静的 IP アドレスを変更すると、次の内容の警告メッセージが表示されます。 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`。 

    このメッセージは無視してかまいません。たとえ VM インスタンスが再起動しなくても IP アドレスは変更されます。

- ポータルの **[Networking Properties]\(ネットワーク プロパティ\)** ブレードに、**[有効なセキュリティ規則]** のリンクがネットワーク アダプターごとに存在します。 このリンクを選択すると、新しいブレードが開き、`Not Found.` というエラー メッセージが表示されます。このエラーが発生する理由は、Azure Stack がまだ**有効なセキュリティ規則**をサポートしていないためです。

- ポータルで受信セキュリティ規則を追加し、**[Service Tag]\(サービス タグ\)** をソースとして選択すると、Azure Stack では利用できないオプションがいくつか **[Source Tag]\(ソース タグ\)** リストに表示されます。 Azure Stack で有効なのは次のオプションだけです。

  - **インターネット**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
    その他のオプションについては、Azure Stack ではソース タグとしてサポートされません。 同様に、送信セキュリティ規則を追加し、**[Service Tag]\(サービス タグ\)** を宛先として選択した場合も、**[Source Tag]\(ソース タグ\)** のリストに同じオプションが表示されます。 有効なオプションは、前述のリストで説明した **[Source Tag]\(ソース タグ\)** と同じものに限られます。

- **New-AzureRmIpSecPolicy** PowerShell コマンドレットの `DHGroup` パラメーターでは、**DHGroup24** 設定がサポートされません。

- ネットワーク セキュリティ グループ (NSG) は、Azure Stack ではグローバル Azure と同様に機能しません。 Azure では、1 つの NSG ルールに複数のポートを設定できます (ポータル、PowerShell、Resource Manager テンプレートを使用)。 Azure Stack では、ポータルを介して、1 つの NSG ルールに複数のポートを設定できません。 この問題を回避するには、Resource Manager テンプレートを使用して、これらの追加ルールを設定します。

### <a name="infrastructure-backup"></a>Infrastructure Backup

<!--scheduler config lost, bug 3615401, new issue in 1811,  hectorl-->
<!-- TSG: https://www.csssupportwiki.com/index.php/Azure_Stack/KI/Backup_scheduler_configuration_lost --> 
- 自動バックアップを有効にした後、スケジューラ サービスが予期せず無効状態になります。 バックアップ コントローラー サービスによって、自動バックアップが無効であることが検出され、管理者ポータルに警告が生成されます。 この警告は、自動バックアップが無効になっていると発生する可能性があります。 
    - 原因: この問題の原因は、サービスのバグによってスケジューラの構成が失われることです。 このバグによって、保存場所、ユーザー名、パスワード、暗号化キーが変わることはありません。   
    - 修復: この問題を軽減するには、Infrastructure Backup リソース プロバイダーのバックアップ コントローラー設定ブレードを開き、**[自動バックアップを有効にする]** を選択します。 必要な頻度と保持期間を設定してください。
    - 発生頻度: 低 

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- お客様の最初の Azure 関数をサブスクリプションに作成する前に、ストレージ リソース プロバイダーを登録する必要があります。


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->

### <a name="syslog"></a>syslog

- syslog 構成は更新サイクル全体で維持されず、その結果、syslog クライアントはその構成を失い、処理が停止されることが syslog によって通知されます。 この問題は、syslog クライアントの一般提供以降、Azure Stack のすべてのバージョンに適用されます (1809)。 この問題を回避するには、Azure Stack の更新プログラムを適用した後、syslog クライアントを再構成します。

## <a name="download-the-update"></a>更新プログラムをダウンロードする

Azure Stack 1811 更新プログラム パッケージは、[ここから](https://aka.ms/azurestackupdatedownload)ダウンロードできます。 

Azure Stack デプロイでは、接続されたシナリオに限り、セキュリティで保護されたエンドポイントが定期的にチェックされ、お客様のクラウド用の更新プログラムが入手可能かどうかが自動的に通知されます。 詳細については、[Azure Stack の更新プログラムの管理](azure-stack-updates.md#using-the-update-tile-to-manage-updates)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

- Azure Stack 統合システムのサービス ポリシーについて、およびサポートを受けられる状態にシステムを維持するために必要な作業について確認するには、「[Azure Stack サービス ポリシー](azure-stack-servicing-policy.md)」を参照してください。  
- 特権エンドポイント (PEP) を使用して更新プログラムを監視および再開するには、「[特権エンドポイントを使用して Azure Stack での更新プログラムをモニターする](azure-stack-monitor-update.md)」をご覧ください。  
- Azure Stack での更新プログラム管理の概要については、「[Azure Stack での更新プログラムの管理概要](azure-stack-updates.md)」を参照してください。  
- Azure Stack に更新プログラムを適用する方法については、「[Azure Stack で更新を適用する](azure-stack-apply-updates.md)」を参照してください。  
