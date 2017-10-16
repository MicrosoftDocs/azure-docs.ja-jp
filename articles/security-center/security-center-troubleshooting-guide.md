---
title: "Azure Security Center トラブルシューティング ガイド | Microsoft Docs"
description: "このドキュメントは、Azure Security Center で問題をトラブルシューティングするのに役立ちます。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 44462de6-2cc5-4672-b1d3-dbb4749a28cd
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2017
ms.author: yurid
ms.openlocfilehash: 0e0a0ce5c0795cec0e47cd5f729099f4762381a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-troubleshooting-guide"></a>Azure Security Center トラブルシューティング ガイド
このガイドは、所属組織が Azure Security Center を使用しており、Security Center に関連する問題のトラブルシューティングを必要としている情報技術 (IT) プロフェッショナル、情報セキュリティ アナリスト、クラウド管理者を対象としています。

>[!NOTE] 
>2017 年 6 月上旬より、Security Center では、Microsoft Monitoring Agent を使用してデータの収集と格納を行っています。 詳細については、「[Azure Security Center のプラットフォームの移行](security-center-platform-migration.md)」を参照してください。 この記事の情報は、Microsoft Monitoring Agent に移行した後の Security Center の機能を示しています。
>

## <a name="troubleshooting-guide"></a>トラブルシューティング ガイド
このガイドでは、Security Center に関連する問題のトラブルシューティングの方法について説明します。 Security Center で行われるトラブルシューティングのほとんどは、問題の生じたコンポーネントの [監査ログ](https://azure.microsoft.com/updates/audit-logs-in-azure-preview-portal/) レコードを確認することから始まります。 監査ログを使用すると、次の内容を判断することができます。

* 実行された操作
* 操作を開始したユーザー
* 操作が発生した時間
* 操作の状態
* 操作の調査に役立つ可能性のあるその他のプロパティの値

監査ログには、リソースで実行されたすべての書き込み操作 (PUT、POST、DELETE) が含まれます。ただし、読み取り操作 (GET) は含まれません。

## <a name="microsoft-monitoring-agent"></a>Microsoft Monitoring Agent
Security Center では、Microsoft Monitoring Agent を使用して、仮想マシンからセキュリティ データを収集します。これは Operations Management Suite と Log Analytics サービスで使用されるものと同じエージェントです。 データ収集が有効になり、ターゲット コンピューターにエージェントが正常にインストールされた後、次のプロセスが実行されます。

* HealthService.exe

サービス管理コンソール (services.msc) を開くと、次のように Microsoft Monitoring Agent サービスも実行されていることを確認できます。

![サービス](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig5.png)

エージェントのバージョンを確認するには、**タスク マネージャー**を開き、**[プロセス]** タブで **Microsoft Monitoring Agent サービス**を見つけます。サービスを右クリックし、**[プロパティ]** をクリックします。 **[詳細]** タブで、次のようなファイルのバージョンを確認します。

![ファイル](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig6.png)
   

## <a name="microsoft-monitoring-agent-installation-scenarios"></a>Microsoft Monitoring Agent のインストール シナリオ
コンピューターに Microsoft Monitoring Agent をインストールするシナリオは 2 つあり、生成される結果が異なります。 サポートされるシナリオは次のとおりです。

* **Security Center による自動的なエージェントのインストール**: このシナリオでは、Security Center とログ検索の両方にアラートが表示されます。 リソースが属しているサブスクリプション用のセキュリティ ポリシー内に構成した電子メール アドレスに電子メール通知が送信されます
。
* **Azure の VM へのエージェントの手動インストール**: このシナリオでは、2017 年 2 月以前に手動でダウンロードしてインストールしたエージェントを使用している場合、Security Center ポータルにアラートを表示できるのは、ワークスペースが属しているサブスクリプションをフィルター処理している場合のみです。 リソースが属しているサブスクリプションをフィルター処理している場合、アラートを表示することはできません。 ワークスペースが属しているサブスクリプション用のセキュリティ ポリシー内に構成した電子メール アドレスに電子メール通知が送信されます。

>[!NOTE]
> 後者の動作を回避するために、必ずエージェントの最新バージョンをダウンロードしてください。
> 

## <a name="troubleshooting-monitoring-agent-network-requirements"></a>監視エージェントのネットワーク要件のトラブルシューティング
Security Center に接続して登録するエージェントには、ドメイン URL とポート番号を含むネットワーク リソースへのアクセスが必要です。

- プロキシ サーバーでは、適切なプロキシ サーバーのリソースがエージェントの設定で構成されていることを確認する必要があります。 詳細については、[プロキシ設定を変更する方法](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-windows-agents#configure-proxy-settings)に関する記事を参照してください。
- インターネットへのアクセスを制限するファイアウォールでは、OMS へのアクセスを許可するようにファイアウォールを構成する必要があります。 エージェントの設定で必要な操作はありません。

次の表は、通信で必要なリソースを示しています。

| エージェントのリソース | ポート | バイパス HTTPS 検査 |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | あり |
| *.oms.opinsights.azure.com | 443 | あり |
| *.blob.core.windows.net | 443 | あり |
| *.azure-automation.net | 443 | あり |

エージェントのオンボードに関する問題が発生した場合は、「[Operations Management Suite オンボードに関する問題のトラブルシューティング方法](https://support.microsoft.com/en-us/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues)」の記事を参照してください。


## <a name="troubleshooting-endpoint-protection-not-working-properly"></a>Endpoint Protection が正しく動作していない場合のトラブルシューティング

ゲスト エージェントは、[Microsoft マルウェア対策](../security/azure-security-antimalware.md)拡張機能によって実行されるすべての処理の親プロセスです。 ゲスト エージェント プロセスが失敗すると、ゲスト エージェントの子プロセスとして実行されている Microsoft マルウェア対策も失敗する可能性があります。  このようなシナリオでは、次の点を確認することをお勧めします。

- ターゲット VM がカスタム イメージであり、VM の作成者がゲスト エージェントをインストールしていない場合。
- ターゲットが Windows VM ではなく Linux VM の場合、Linux VM に Windows バージョンのマルウェア対策拡張機能をインストールすると、インストールは失敗します。 Linux ゲスト エージェントには、OS バージョンと必要なパッケージに関して固有の要件があります。それらの要件が満たされていない場合、VM エージェントも機能しません。 
- VM が古いバージョンのゲスト エージェントを使用して作成されている場合。 この場合、一部の古いエージェントが新しいバージョンに自動更新できないことが原因でこの問題が発生している可能性があることに注意してください。 独自のイメージを作成する場合は、必ず最新バージョンのゲスト エージェントを使用します。
- 一部のサード パーティ製管理ソフトウェアによって、ゲスト エージェントが無効化されているか、特定のファイルの場所へのアクセスがブロックされている可能性があります。 VM にサード パーティ製品がインストールされている場合は、エージェントが除外リストに含まれていることを確認してください。
- 特定のファイアウォール設定またはネットワーク セキュリティ グループ (NSG) が、ゲスト エージェントとの間でのネットワーク トラフィックをブロックしている可能性があります。
- 特定のアクセス制御リスト (ACL) がディスク アクセスを妨げている可能性があります。
- ディスク領域の不足によって、ゲスト エージェントが正常に機能できなくなっている可能性があります。 

既定では、Microsoft マルウェア対策ユーザー インターフェイスは無効になっています。必要に応じてこれを有効にする方法の詳細については、[デプロイ後に ARM VM の Microsoft マルウェア対策ユーザー インターフェイスを有効にする方法](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/09/enabling-microsoft-antimalware-user-interface-post-deployment/)に関するブログをご覧ください。

## <a name="troubleshooting-problems-loading-the-dashboard"></a>ダッシュボードの読み込みに関する問題のトラブルシューティング

Security Center ダッシュボードを読み込む際に問題が発生した場合は、Security Center のサブスクリプションを登録するユーザー (つまり、サブスクリプションで Security Center を最初に開いたユーザー) と、データの収集を有効にするユーザーが、サブスクリプションに対する "*所有者*" または"*共同作成者*" であることを確認します。 その時点から、サブスクリプションの "*閲覧者*" も dashboard/alerts/recommendation/policy を参照できます。

## <a name="contacting-microsoft-support"></a>Microsoft サポートへの問い合わせ
一部の問題は、この記事に示したガイドラインを基に特定できます。その他の問題については、Security Center パブリック [フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter)でドキュメントを確認できます。 ただし、さらにトラブルシューティングが必要な場合は、次に示すように **Azure ポータル**を使用して新しいサポート要求を開くことができます。 

![Microsoft Support](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)


## <a name="see-also"></a>関連項目
このドキュメントでは、Azure セキュリティ センターでのセキュリティ ポリシーの構成方法について説明しました。 Azure セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center 計画および運用ガイド](security-center-planning-and-operations-guide.md) 」 -- Azure Security Center を導入するための設計上の考慮事項を計画および理解する方法について説明します。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md) 」 -- Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」 -- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md) 」 -- パートナー ソリューションの正常性状態を監視する方法について説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」 -- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

