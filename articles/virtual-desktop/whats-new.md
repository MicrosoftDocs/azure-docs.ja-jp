---
title: Windows Virtual Desktop の最新情報 - Azure
description: Windows Virtual Desktop の新機能と製品の更新プログラム。
author: Heidilohr
ms.topic: overview
ms.date: 02/23/2021
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.custom: references_regions
ms.openlocfilehash: c0b4ad17c5aefe29a313b9eaf75fc87ce4d88720
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102499885"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Windows Virtual Desktop の最新情報

Windows Virtual Desktop は定期的に更新されます。 この記事では、以下について説明します。

- 最新の更新プログラム
- 新機能
- 既存の機能の強化
- バグの修正

この記事は毎月更新されます。 こちらを頻繁に確認して、新しい更新プログラムに関する最新情報を入手してください。

## <a name="client-updates"></a>クライアント更新

Windows Virtual Desktop およびリモート デスクトップ サービスに関するクライアント向けの更新プログラムについては、次の記事を参照してください。

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)

## <a name="fslogix-updates"></a>FSLogix の更新

FSLogix に対して行われた最新の更新については、 [FSLogix の最新情報](/fslogix/whats-new)に関するページを参照してください。

## <a name="february-2021"></a>2021 年 2 月

2021 年 2 月における変更点は次のとおりです。

### <a name="portal-experience"></a>ポータルでの操作

Azure portal のエクスペリエンスを次のように改善しました。

- セッション ホスト グリッド タブにおけるホストの一括ドレイン モード。 
- MSIX アプリ アタッチのパブリック プレビューが利用できるようになりました。
- 固定ホスト プールの概要情報がダーク モードに対応しました。

### <a name="eu-metadata-storage-now-in-public-preview"></a>EU メタデータ ストレージがパブリック プレビューに

Windows Virtual Desktop におけるサービス メタデータのストレージ オプションとして、ヨーロッパ (EU) 地域のパブリック プレビューがホストされるようになりました。 ユーザーは、サービス オブジェクトを作成するときに、西ヨーロッパと北ヨーロッパのどちらかを選択できます。 ホスト プールに使用されるサービス オブジェクトとメタデータは、各リージョンに関連付けられている Azure 地域に格納されます。 詳細については、[パブリック プレビューを告知するブログ記事](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/announcing-public-preview-of-windows-virtual-desktop-service/m-p/2143939)を参照してください。

### <a name="teams-on-windows-virtual-desktop-plugin-updates"></a>Teams on Windows Virtual Desktop プラグインの更新

Windows Virtual Desktop プラグインのビデオ通話の品質を改良しました。画面が突然暗くなるなど、ビデオとサウンドが同期されていないなど、最も多くレポートされる問題が解消されています。 これらの改良により、アクティブ スピーカーの切り替えによって単一動画ビューのパフォーマンスが向上します。 また、特殊な特性を備えたハードウェア デバイスが Teams で利用できない問題も修正済みです。

## <a name="january-2021"></a>2021 年 1 月

2021 年 1 月における変更点は次のとおりです。

### <a name="new-windows-virtual-desktop-offer"></a>Windows Virtual Desktop の新しいプラン

Windows Virtual Desktop の D シリーズと Bs シリーズの仮想マシンでネイティブの Microsoft ソリューションを使用する新規ユーザーは、最長 90 日間、コンピューティングのコストを 30% 節約できます。 このプランは、2021 年 3 月 31 日まで Azure portal で利用できます。 詳細については、[Windows Virtual Desktop プランのページ](https://azure.microsoft.com/services/virtual-desktop/offer/)を参照してください。

### <a name="networksecuritygrouprules-value-change"></a>networkSecurityGroupRules の値を変更 

Azure Resource Manager の入れ子になったテンプレートで、networkSecurityGroupRules の既定値をオブジェクトから配列に変更しました。 今後は、networkSecurityGroupRules の値を指定せずに managedDisks-customimagevm.json を使用してもエラーは発生しません。 これは破壊的変更ではありません。下位互換性があります。

### <a name="fslogix-hotfix-update"></a>FSLogix の修正プログラム

FSLogix バージョン 2009 HF_01 (2.9.7654.46150) をリリースし、以前のリリース (2.9.7621.30127) にあった問題を解決しました。 以前のバージョンの使用は中止し、できるだけ早く FSLogix を更新するようお勧めします。

詳細については、リリース ノートで [FSLogix の最新情報](/fslogix/whats-new#fslogix-apps-2009-hf_01-29765446150)を参照してください。

### <a name="azure-portal-experience-improvements"></a>Azure portal エクスペリエンスの改善

Azure portal のエクスペリエンスについて次の点を改善しました。

- ローカル VM 管理者資格情報を直接追加できるようになりました。Active Directory ドメイン参加アカウントの資格情報でローカル アカウントを追加する必要はありません。
- 個人とグループの両方の割り当てを、個人ユーザー用のタブとグループ用のタブで別々に一覧表示できるようになりました。
- ホスト プールの仮想マシン概要に、Windows Virtual Desktop エージェントのバージョン番号が表示されるようになりました。
- ホスト プールとアプリケーション グループの一括削除機能を追加しました。
- ホスト プール内の複数のセッション ホストを対象にドレイン モードを有効または無効にできるようになりました。
- VM の詳細ページからパブリック IP フィールドを削除しました。

### <a name="windows-virtual-desktop-agent-troubleshooting"></a>Windows Virtual Desktop エージェントのトラブルシューティング

Microsoft はこのほど、一般的な問題に遭遇したお客様を支援するために、[Windows Virtual Desktop エージェントのトラブルシューティング ガイド](troubleshoot-agent.md)を用意しました。

### <a name="microsoft-defender-for-endpoint-integration"></a>Microsoft Defender for Endpoint 統合

Microsoft Defender for Endpoint の統合が一般提供になりました。 この機能により、ローカル Windows 10 マシンと同じ調査エクスペリエンスを Windows Virtual Desktop VM で利用できるようになります。 Windows 10 Enterprise マルチセッションを使用するユーザーのために、Microsoft Defender for Endpoint では最大 50 のコンカレント ユーザー接続がサポートされます。Windows 10 Enterprise マルチセッションのコストを削減し、高い信頼度を備えた Microsoft Defender for Endpoint を活用することが可能です 詳細については、こちらの[ブログ記事](https://techcommunity.microsoft.com/t5/microsoft-defender-for-endpoint/windows-virtual-desktop-support-is-now-generally-available/ba-p/2103712)を参照してください。

### <a name="azure-security-baseline-for-windows-virtual-desktop"></a>Windows Virtual Desktop 用の Azure セキュリティ ベースライン

Microsoft では最近、Windows Virtual Desktop 向け [Azure セキュリティ ベースラインに関する記事](security-baseline.md)を公開し、ユーザーの注意を喚起しています。 これらのガイドラインには、Azure セキュリティ ベンチマーク バージョン 2.0 を Windows Virtual Desktop に適用する方法についての情報が記載されています。 Azure セキュリティ ベンチマークでは、Azure 上のクラウド ソリューションをセキュリティで保護するために Microsoft が推奨する設定と手法が説明されています。

## <a name="december-2020"></a>2020 年 12 月

2020 年 12 月における変更点は次のとおりです。 

### <a name="azure-monitor-for-windows-virtual-desktop"></a>Windows Virtual Desktop 向けの Azure Monitor

Windows Virtual Desktop 向けの Azure Monitor のパブリック プレビューが利用可能になりました。 この新機能には、Azure Monitor ブックを基盤とする堅牢なダッシュボードが含まれており、IT プロフェッショナルが Windows Virtual Desktop 環境を理解するうえで役立ちます。 詳細については、[ブログのお知らせ](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/azure-monitor-for-windows-virtual-desktop-public-preview/m-p/1946587)をご覧ください。 

### <a name="azure-resource-manager-template-change"></a>Azure Resource Manager テンプレートの変更点 

最新の更新プログラムでは、ホスト プールを作成およびプロビジョニングするための Azure Resource Manager テンプレートからすべてのパブリック IP アドレス パラメーターを削除しました。 デプロイを安全に保つために、Windows Virtual Desktop にはパブリック IP を使用しないことを強くお勧めします。 デプロイがパブリック IP に依存している場合は、代わりにプライベート IP を使用するように再構成する必要があります。そうしないと、デプロイが適切に機能しません。

### <a name="msix-app-attach-public-preview"></a>MSIX アプリのアタッチのパブリック プレビュー 

MSIX アプリのアタッチは、今月にパブリック プレビューが開始されたもう 1 つのサービスです。 MSIX アプリのアタッチは、Windows Virtual Desktop セッション ホスト VM に MSIX アプリケーションを動的に提供するサービスです。 詳細については、[ブログのお知らせ](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/msix-app-attach-azure-portal-integration-public-preview/m-p/1986231)をご覧ください。 

### <a name="screen-capture-protection"></a>画面キャプチャ保護 

今月は、画面キャプチャ保護のパブリック プレビューも開始されました。 この機能を使用することで、機密情報がクライアント エンドポイントでキャプチャされるのを防ぐことができます。 [このページ](https://aka.ms/WVDScreenCaptureProtection)に移動して、画面キャプチャ保護をお試しください。  

### <a name="built-in-roles"></a>組み込みのロール

管理者のアクセス許可を付与するための、Windows Virtual Desktop 用の新しい組み込みロールが追加されました。 詳細については、「[Windows Virtual Desktop の組み込みロール](rbac.md)」を参照してください。 

### <a name="application-group-limit-increase"></a>アプリケーション グループの制限の引き上げ

Azure Active Directory テナントあたりのアプリケーション グループの既定の上限値を 200 グループに増やしました。

### <a name="client-updates-for-december-2020"></a>2020 年 12 月のクライアント更新

次のクライアントの新しいバージョンがリリースされました。 

- Android
- macOS
- Windows

クライアントの更新の詳細については、「[クライアント更新](whats-new.md#client-updates)」をご覧ください。

## <a name="november-2020"></a>2020 年 11 月

### <a name="azure-portal-experience"></a>Azure portal での操作

Azure portal のユーザー エクスペリエンスの 2 つのバグを修正しました。

- "VM の追加" ワークフローで、デスクトップ アプリケーションのフレンドリ名が上書きされなくなりました。
- セッション ホストがスケール セットに含まれている場合、[セッション ホスト] タブが読み込まれるようになりました。

### <a name="fslogix-client-version-2009"></a>FSLogix クライアント バージョン 2009 

多くの修正と機能強化が行われた新しいバージョンの FSLogix クライアントがリリースされました。 詳細については、[ブログ記事](https://social.msdn.microsoft.com/Forums/en-US/defe5828-fba4-4715-a68c-0e4d83eefa6b/release-notes-for-fslogix-apps-release-2009-29762130127?forum=FSLogix)をご覧ください。

### <a name="rdp-shortpath-public-preview"></a>RDP Shortpath パブリック プレビュー

RDP Shortpath では、ポイント対サイト VPN、サイト間 VPN、ExpressRoute を使用した、Windows Virtual Desktop セッション ホストへの直接接続が導入されています。 また、URCP トランスポート プロトコルも導入されています。 RDP Shortpath は、ユーザー エクスペリエンスを向上させるために、待ち時間とネットワーク ホップを削減するように設計されています。 詳細については、[Windows Virtual Desktop の RDP Shortpath](shortpath.md) に関する記事を参照してください。

### <a name="azdesktopvirtualization-version-201"></a>Az.DesktopVirtualization、バージョン 2.0.1

Windows Virtual Desktop コマンドレットのバージョン 2.0.1 がリリースされました。 この更新には、MSIX アプリのアタッチを管理できるコマンドレットが含まれています。 新しいバージョンは、[PowerShell ギャラリー](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.0.1)でダウンロードできます。

### <a name="azure-advisor-updates"></a>Azure Advisor の更新

Windows Virtual Desktop での近接ガイダンスに関する新しい推奨事項と、深さ優先の負荷分散ホスト プールにおけるパフォーマンスの最適化に関する新しい推奨事項が Azure Advisor に追加されました。 詳細については、[Azure Web サイト](https://azure.microsoft.com/updates/new-recommendations-from-azure-advisor/)を参照してください。

## <a name="october-2020"></a>2020 年 10 月

2020 年 10 月における変更点は次のとおりです。

### <a name="improved-performance"></a>パフォーマンスの向上

- 次の Azure 地域での接続の待ち時間を短縮することで、パフォーマンスを最適化しました。
    - スイス
    - カナダ

[エクスペリエンス予測ツール](https://azure.microsoft.com/services/virtual-desktop/assessment/)を使用して、これらの地域のユーザー エクスペリエンス品質を見積もることができるようになりました。

### <a name="azure-government-cloud-availability"></a>Azure Government クラウドの提供

Azure Government クラウドの一般提供が開始されました。 詳細については、[ブログ記事](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-generally-available-in-the-azure-government-cloud/)をご覧ください。

### <a name="windows-virtual-desktop-azure-portal-updates"></a>Windows Virtual Desktop Azure portal の更新

Windows Virtual Desktop Azure portal が更新されました。

- ユーザーが [セッション] タブを開くことができなくなる resourceID エラーを修正しました。
- [セッション ホスト] タブの UI を合理化しました。
- RDP のプロパティの [既定値]、[ユーザビリティ]、[既定値に戻す] の各設定を修正しました。
- すべてのタブで "削除" 機能に一貫性を持たせました。
- ポータルで、"アプリの追加" ワークフローのアプリ名が検証されるようになりました。
- セッション ホストのエクスポート データが列に配置されない問題を修正しました。
- ポータルでユーザー セッションを取得できない問題を修正しました。
- 仮想マシンが別のリソース グループに作成されたときに発生するセッション ホストの取得の問題を修正しました。
- アクティブなセッションと切断されたセッションの両方を一覧表示するように [セッション ホスト] タブを更新しました。
- [アプリケーション] タブにページが含まれるようになりました。
- [アプリケーションの一覧] タブに、"コマンド ラインが必要" というテキストが正しく表示されない問題を修正しました。
- ドイツ語版の Shared Image Gallery を使用しているときに、ポータルでホスト プールまたは仮想マシンをデプロイできない問題を修正しました。

### <a name="client-updates-for-october-2020"></a>2020 年 10 月のクライアント更新

クライアントの新しいバージョンがリリースされました。 詳細については、次の記事を参照してください。

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)

他のクライアントの詳細については、「[クライアント更新](#client-updates)」をご覧ください。

## <a name="september-2020"></a>2020 年 9 月

2020 年 9 月における変更点は次のとおりです。

- 次の Azure 地域での接続の待ち時間を短縮することで、パフォーマンスを最適化しました。
    - ドイツ
    - 南アフリカ (検証環境のみ)

[エクスペリエンス予測ツール](https://azure.microsoft.com/services/virtual-desktop/assessment/)を使用して、これらの地域のユーザー エクスペリエンス品質を見積もることができるようになりました。

- Windows Virtual Desktop 用 Windows デスクトップ クライアントのバージョン 1.2.1364 がリリースされました。 この更新では、次の変更を行いました。
    - シングル サインオン (SSO) が Windows 7 で動作しなかった問題を修正しました。
    - Teams のメディア最適化を有効にしているユーザーが、別のアプリでオーディオ ストリームを排他モードで開いている場合に、Teams の会議に通話または参加しようとしたときにクライアントが切断される原因となっていた問題を修正しました。
    - Teams のメディア最適化が有効になっているときに、Teams でオーディオまたはビデオ デバイスを列挙できなかった問題を修正しました。
    - "設定にサポートが必要ですか?" リンクをデスクトップの設定ページに追加しました。
    - ハイコントラストの濃色テーマの使用時に発生する "サブスクライブ" ボタンの問題を修正しました。
    
- ユーザーからの多大な支援によって、Microsoft Store リモート デスクトップ クライアントの 2 つの重大な問題を修正することができました。 クライアントの段階的リリースを世界中の多くのユーザーに拡げるなかで、引き続きフィードバックを確認しながら問題を修正していきます。
    
- Azure portal でお客様のデプロイに VM を追加するワークフローの一環として、VM の場所、イメージ、リソース グループ、プレフィックス名、ネットワーク構成を変更できるようにする新機能が追加されました。

- IT 担当者は、Microsoft エンドポイント マネージャーを使用して、ハイブリッド Azure Active Directory 参加済み Windows 10 Enterprise VM を管理できるようになりました。 詳細については、[こちらのブログ記事](https://techcommunity.microsoft.com/t5/microsoft-endpoint-manager-blog/microsoft-endpoint-manager-announces-support-for-windows-virtual/ba-p/1681048)を参照してください。

## <a name="august-2020"></a>2020 年 8 月

2020 年 8 月における変更点は次のとおりです。

- 次の Azure リージョンで、パフォーマンスの向上により接続の待ち時間が短縮されています。 

    - イギリス
    - フランス
    - ノルウェー
    - 韓国

   [エクスペリエンス予測ツール](https://azure.microsoft.com/services/virtual-desktop/assessment/)を使用すると、これらの変更がユーザーに与える影響を大まかに理解できます。

- Microsoft Store リモート デスクトップ クライアント (v10.2.1522+) の一般提供が開始されました。 このバージョンの Microsoft Store リモート デスクトップ クライアントは、Windows Virtual Desktop と互換性があります。 また、ユーザー エクスペリエンスを向上させるために、更新された UI flows も導入されました。 この更新には、流暢なデザイン、ライト モードとダーク モード、およびその他の多くの興味深い変更が含まれています。 また、クライアントが書き直され、基になるリモート デスクトップ プロトコル (RDP) エンジンに、iOS、macOS、および Android クライアントと同じものが使用されるようになりました。 これにより、すべてのプラットフォームに新しい機能をより迅速に提供できます。 [クライアントをダウンロード](https://www.microsoft.com/p/microsoft-remote-desktop/9wzdncrfj3ps?rtc=1&activetab=pivot:overviewtab)して、お試しください。

- Teams デスクトップ クライアント (バージョン 1.3.00.21759) でチャット、チャネル、予定表に UTC タイム ゾーンのみが表示されていた問題が修正されました。 更新されたクライアントでは、代わりにリモート セッションのタイム ゾーンが表示されるようになりました。

- Azure Advisor が Windows Virtual Desktop の一部になりました。 Azure portal から Windows Virtual Desktop にアクセスすると、お使いの Windows Virtual Desktop 環境を最適化するための推奨事項を確認できます。 [Azure Advisor](azure-advisor.md) の詳細情報。

- Azure CLI で Windows Virtual Desktop (`az desktopvirtualization`) がサポートされ、Windows Virtual Desktop のデプロイを自動化できるようになりました。 拡張コマンドの一覧については、「[desktopvirtualization](/cli/azure/ext/desktopvirtualization/)」をご覧ください。

- Windows Virtual Desktop の Azure Resource Manager インターフェイスとの完全な互換性が確保されるようにデプロイ テンプレートが更新されました。 このテンプレートは [GitHub](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates) で見つけることができます。

- Windows Virtual Desktop US Gov ポータルがパブリック プレビューになりました。 詳細については、[こちらのお知らせ](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-available-in-the-azure-government-cloud-in-preview/)を参照してください。

## <a name="july-2020"></a>2020 年 7 月  

Azure Resource Management 統合を使用した Windows Virtual Desktop の一般提供が 7 月に開始されました。

この新しいリリースでの変更点は次のとおりです。 

- "Fall 2019 リリース" は、"Windows Virtual Desktop (クラシック)" という名称に変わり、今後は "Spring 2020 リリース" が "Windows Virtual Desktop" という名称になります。 詳細については、[こちらのブログ記事](https://azure.microsoft.com/blog/new-windows-virtual-desktop-capabilities-now-generally-available/)を参照してください。 

新機能の詳細については、[こちらのブログ記事](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245)を参照してください。 

### <a name="autoscaling-tool-update"></a>自動スケーリング ツールの更新

プレビュー段階だった自動スケーリング ツールの最新バージョンが一般提供となりました。 このツールは、Azure Automation アカウントと Azure Logic Apps を使用して、ホスト プール内のセッション ホスト仮想マシン (VM) を自動的にシャットダウン、再起動することで、インフラストラクチャ コストを抑えています。 詳細については、「[Azure Automation を使用してセッション ホストをスケーリングする](set-up-scaling-script.md)」を参照してください。

### <a name="azure-portal"></a>Azure portal

Azure portal から Windows Virtual Desktop で次のことを行えるようになりました。 

- 個人用デスクトップ セッション ホストに直接ユーザーを割り当てる  
- ホスト プールの検証環境の設定を変更する 

### <a name="diagnostics"></a>診断

Log Analytics ワークスペース向けに、あらかじめ構築されたいくつかのクエリを新たにリリースしました。 それらのクエリにアクセスするには、 **[ログ]** に移動し、 **[カテゴリ]** の **[Windows Virtual Desktop]** を選択します。 詳細については、「[診断機能に Log Analytics を使用する](diagnostics-log-analytics.md)」を参照してください。

### <a name="update-for-remote-desktop-client-for-android"></a>Android 用リモート デスクトップ クライアントの更新

[Android 用リモート デスクトップ クライアント](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx)で、新たに Windows Virtual Desktop 接続がサポートされます。 バージョン 10.0.7 以降の Android クライアントには、ユーザー エクスペリエンスを改良した新しい UI が備わっています。 また、このクライアントは、Windows Virtual Desktop ワークスペースをサブスクライブする際に、Android デバイス上で Microsoft Authenticator と連携することによって条件付きアクセスが可能となります。  

以前のバージョンのリモート デスクトップ クライアントは、今後 "Remote Desktop 8" と呼ばれます。 以前のバージョンのクライアントにあった既存の接続は、新しいクライアントにシームレスに移行されます。 新しいクライアントは、基になる RDP コア エンジンが、すべてのプラットフォームの中で新機能がいち早くリリースされた iOS や macOS クライアントと同じものに書き換えられています。 

### <a name="teams-update"></a>Teams の更新

Windows Virtual Desktop 用の Microsoft Teams に対して機能強化が行われました。 特筆すべき点として、Windows Virtual Desktop では新たに、Windows デスクトップ クライアント向けのオーディオとビデオの最適化がサポートされました。 ユーザーが通話や会議でオーディオまたはビデオを使用する際にユーザー間に直接パスを作成することで、転送による待ち時間が短縮されます。 距離が短いほどホップ数が少なくなるため、通話時の表示と音が滑らかになります。 [Windows Virtual Desktop で Teams を使用する](teams-on-wvd.md)方法に関するページを参照してください。

## <a name="june-2020"></a>2020 年 6 月

先月、Azure Resource Manager 統合を使用した Windows Virtual Desktop をプレビューとして導入しました。 この更新プログラムには、多くの魅力的な新機能が用意されています。 ここでは、このバージョンの Windows Virtual Desktop について説明します。

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager"></a>Windows Virtual Desktop が Azure Resource Manager と統合

Windows Virtual Desktop が Azure Resource Manager と統合されました。 最新の更新プログラムでは、すべての Windows Virtual Desktop オブジェクトが Azure Resource Manager リソースになりました。 また、この更新プログラムは、Azure ロールベースのアクセス制御 (Azure RBAC) とも統合されています。 詳細については、「[Azure Resource Manager とは](../azure-resource-manager/management/overview.md)」を参照してください。

この変更による効果は次のとおりです。

- Windows Virtual Desktop が Azure portal と統合されました。 つまり、ポータルで直接すべてを管理できるため、PowerShell、Web アプリ、サードパーティ製のツールは必要ありません。 開始するには、[Azure portal でのホスト プールの作成](create-host-pools-azure-marketplace.md)に関するチュートリアルを参照してください。

- この更新プログラムより前は、RemoteApp とデスクトップを個々のユーザーにしか発行できませんでした。 Azure Resource Manager を使用すると、Azure Active Directory グループにリソースを発行できるようになりました。

- 以前のバージョンの Windows Virtual Desktop には、テナントまたはホスト プールに割り当てることができる組み込み管理者ロールが 4 つありました。 これらのロールは、[Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/overview.md) に組み込まれました。 これらのロールは、すべての Windows Virtual Desktop Azure Resource Manager オブジェクトに適用することができます。これにより、完全な多機能な委任モデルを作成できます。

- この更新プログラムでは、ホスト プールを拡張するために Azure Marketplace または GitHub テンプレートを繰り返し実行する必要がなくなりました。 ホスト プールを拡張するために必要なのは、Azure portal で対象のホスト プールに移動し、 **[+ 追加]** を選択して追加のセッション ホストをデプロイすることだけです。

- ホスト プールのデプロイは、[Azure Shared Image Gallery](../virtual-machines/shared-image-galleries.md) と完全に統合されました。 Shared Image Gallery は、イメージのバージョン管理を含む仮想マシン (VM) イメージ定義を格納する、別の Azure サービスです。 また、グローバル レプリケーションを使用して、イメージをコピーし、ローカル デプロイ用に他の Azure リージョンに送信することもできます。

- 従来 PowerShell または診断サービス Web アプリを通じて実行されていた監視機能は、Azure portal の Log Analytics に移動されました。 また、レポートを視覚化するための 2 つのオプションも用意されました。 Kusto クエリを実行し、Workbooks を使用すると、ビジュアル レポートを作成できます。

- Windows Virtual Desktop を使用するために Azure Active Directory (Azure AD) の同意を完了する必要がなくなりました。 この更新プログラムでは、Azure サブスクリプションの Azure AD テナントによってユーザーが認証され、管理者に Azure RBAC 制御が提供されます。

### <a name="powershell-support"></a>PowerShell のサポート

この更新プログラムでは、Azure PowerShell Az モジュールに新しい AzWvd コマンドレットが追加されました。 この新しいモジュールは、.NET Core で実行される PowerShell Core でサポートされます。

このモジュールをインストールするには、「[Windows Virtual Desktop 用 PowerShell モジュールを設定する](powershell-module.md)」の手順に従ってください。

また、使用可能なコマンドの一覧は、[AzWvd PowerShell リファレンス](/powershell/module/az.desktopvirtualization/#desktopvirtualization)で確認できます。

新機能の詳細については、[こちらのブログ記事](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245)を確認してください。

### <a name="additional-gateways"></a>追加のゲートウェイ

接続の待ち時間を短縮するために、南アフリカに新しいゲートウェイ クラスターが追加されました。

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Windows Virtual Desktop での Microsoft Teams (プレビュー)

Windows Virtual Desktop 用の Microsoft Teams に対していくつかの機能強化が行われました。 最も重要なこととして、Windows Virtual Desktop では、オーディオやビジュアルによる通話の転送がサポートされるようになりました。 ユーザーがオーディオまたはビデオを使用して通話する際にユーザー間に直接パスを作成することで、転送による待ち時間が短縮されます。 距離が短いほどホップ数が少なくなるため、通話時の表示と音が滑らかになります。

詳細については、[こちらのブログ記事](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/)を参照してください。

## <a name="next-steps"></a>次のステップ

今後の計画については、[Microsoft 365 Windows 仮想デスクトップ ロードマップ](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop)に関するページを参照してください。
