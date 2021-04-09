---
title: Azure Security Center に含まれる Microsoft Defender for Endpoint ライセンスを使用する
description: Microsoft Defender for Endpoint と、Azure Security Center からのデプロイについて説明します。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/08/2021
ms.author: memildin
ms.openlocfilehash: 17e54dfc5c369a8cbb35db50ff2cbab131d34c2d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102453769"
---
# <a name="protect-your-endpoints-with-security-centers-integrated-edr-solution-microsoft-defender-for-endpoint"></a>Security Center の統合 EDR ソリューションを使用してエンドポイントを保護する: Microsoft Defender for Endpoint

Microsoft Defender for Endpoint は、クラウドで提供される包括的なエンドポイント セキュリティ ソリューションです。 主な機能は次のとおりです。

- リスクベースの脆弱性の管理と評価 
- 攻撃の回避
- 行動ベースおよびクラウドを利用した保護
- エンドポイントでの検出と対応 (EDR)
- 調査と修復の自動化
- マネージド ハンティング サービス

> [!TIP]
> 元々は **Windows Defender ATP** として提供されたこのエンドポイントでの検出と対応 (EDR) 製品は、2019 年に名前が **Microsoft Defender ATP** に変更されました。
>
> Ignite 2020 で、[Microsoft Defender XDR スイート](https://www.microsoft.com/security/business/threat-protection)の提供が開始されました。この EDR コンポーネントの名前は、**Microsoft Defender for Endpoint** に変更されました。


## <a name="availability"></a>可用性

| 側面                          | 詳細                                                                                                                                                                                                                                                                                                       |
|---------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| リリース状態:                  | 一般提供 (GA)                                                                                                                                                                                                                                                                                      |
| 価格:                        | [Azure Defender for servers](defender-for-servers-introduction.md) が必要                                                                                                                                                                                                                                             |
| サポート対象のプラットフォーム:            |  • Windows を実行している Azure マシン<br> • Windows を実行している Azure Arc マシン|
| サポート対象の Windows のバージョン:  |  • **一般提供 (GA) -** Windows Server 2016、2012 R2、2008 R2 SP1 での検出<br> • **プレビュー -** Windows Server 2019、[Windows Virtual Desktop (WVD)](../virtual-desktop/overview.md)、[Windows 10 Enterprise マルチセッション](../virtual-desktop/windows-10-multisession-faq.md) (旧称 Enterprise for Virtual Desktops (EVD)) での検出|
| サポートされていないオペレーティング システム:  |  • Windows 10 (EVD と WVD 以外)<br> • Linux|
| 必要なロールとアクセス許可: | 統合を有効または無効にするには: **セキュリティ管理者** または **所有者**<br>Security Center の MDATP アラートを表示するには: **セキュリティ閲覧者**、**閲覧さ**、**リソース グループの共同作成者**、**リソース グループの所有者**、**セキュリティ管理者**、**サブスクリプションの所有者**、または **サブスクリプションの共同作成者**|
| クラウド:                         | ![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![いいえ](./media/icons/no-icon.png) China Gov、その他の Gov                                                        |
|                                 |                                                                                                                                                                                                                                                                                                               |

## <a name="microsoft-defender-for-endpoint-features-in-security-center"></a>Security Center の Microsoft Defender for Endpoint 機能

Microsoft Defender for Endpoint では次のものが提供されます。

- **高度な侵害後検出センサー**。 Windows マシン用の Defender for Endpoint のセンサーにより、ありとあらゆる動作の信号が収集されます。

- **分析に基づくクラウド利用の侵害後検出**。 Defender for Endpoint は、脅威の変化にすばやく適応します。 高度な分析とビッグ データを使用します。 Windows、Azure、Office 全体の信号を使用したインテリジェント セキュリティ グラフによって強化され、これにより未知の脅威が検出されます。 アクションにつながるアラートを提供し、迅速に対応できるようにします。

- **脅威インテリジェンス**。 Defender for Endpoint によって攻撃者のツール、テクニック、およびプロシージャが識別されると、アラートが生成されます。 Microsoft の脅威ハンターおよびセキュリティ チームによって生成され、パートナーによって提供されるインテリジェンスによって強化されたデータを使用します。

Defender for Endpoint を Security Center と統合すると、次の追加機能を利用することができます。

- **自動オンボード**。 Security Center によって監視されているすべての Windows サーバー向けの Microsoft Defender for Endpoint センサーが、Security Center によって自動的に有効にされます。

- **1 つのウィンドウ**。 Security Center コンソールには、Microsoft Defender for Endpoint のアラートが表示されます。 さらに調査するには、Microsoft Defender for Endpoint 独自のポータル ページを使用します。ここには、アラート プロセス ツリーやインシデント グラフなどの追加情報が表示されます。 最大 6 か月前まで遡って、すべての動作を示す詳細なマシン タイムラインを見ることもできます。

    :::image type="content" source="./media/security-center-wdatp/microsoft-defender-security-center.png" alt-text="Microsoft Defender for Endpoint 独自の Security Center" lightbox="./media/security-center-wdatp/microsoft-defender-security-center.png":::

## <a name="microsoft-defender-for-endpoint-tenant-location"></a>Microsoft Defender for Endpoint のテナントの場所

Azure Security Center を使用してサーバーを監視すると、Microsoft Defender for Endpoint テナントが自動的に作成されます。 Defender for Endpoint によって収集されたデータは、プロビジョニング時に識別されたテナントの地理的な場所に格納されます。 偽名フォームの顧客データは、米国の中央のストレージおよび処理システムにも格納される可能性があります。 

場所を構成した後は、その場所を変更することはできません。 Microsoft Defender for Endpoint のライセンスを所有していて、データを別の場所に移動する必要がある場合は、Microsoft サポートに連絡してテナントをリセットしてください。


## <a name="enabling-the-microsoft-defender-for-endpoint-integration"></a>Microsoft Defender for Endpoint 統合を有効にする

1. お使いのマシンが Defender for Endpoint に必要な要件を満たしていることを確認します。

    - **Windows のすべてのバージョン**:
        - 「[デバイス プロキシとインターネット接続の設定を構成する](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet)」で説明されているネットワーク設定を構成します。
        - オンプレミスのマシンに対して Defender for Endpoint をデプロイする場合は、「[Azure Arc 対応サーバーでハイブリッド マシンを接続する](../azure-arc/servers/learn/quick-enable-hybrid-vm.md)」の説明に従って Azure Arc に接続します。
    - さらに、**Windows Server 2019 マシン** の場合は、有効なエージェントが実行されていて、MicrosoftMonitoringAgent 拡張機能があることを確認します。

1. **Azure Defender for servers** を有効にします。 「[クイックスタート: Azure Defender を有効にする](enable-azure-defender.md)」をご覧ください。

1. ご利用のサーバーに Microsoft Defender for Endpoints のライセンスが既に供与されデプロイ済みの場合は、「[Windows サーバーのオフボード](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints#offboard-windows-servers)」で説明されている手順を使用して削除します。
1. Security Center のメニューから、 **[価格と設定]** を選択します。
1. 変更するサブスクリプションを選択します。
1. **[脅威検出]** を選択します。
1. **[データに Microsoft Defender for Endpoint がアクセスすることを許可する]** を選択し、 **[保存]** を選択します。

    :::image type="content" source="./media/security-center-wdatp/enable-integration-with-edr.png" alt-text="Azure Security Center および Microsoft の EDR ソリューションの Microsoft Defender for Endpoint 間の統合を有効にする":::

    Azure Security Center によって、サーバーは Microsoft Defender for Endpoint に自動的にオンボードされます。 このオンボードには最大で 24 時間かかることがあります。


## <a name="access-the-microsoft-defender-for-endpoint-portal"></a>Microsoft Defender for Endpoint ポータルにアクセスする

1. ユーザー アカウントに必要なアクセス許可があることを確認します。 詳細については、「[Microsoft Defender セキュリティ センターにユーザー アクセスを割り当てる](/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access)」を参照してください。

1. 匿名トラフィックをブロックしているプロキシまたはファイアウォールがあるかどうかを確認します。 Defender for Endpoint センサーはシステム コンテキストから接続するため、匿名トラフィックを許可する必要があります。 Defender for Endpoint ポータルに制限なく確実にアクセスするには、[プロキシ サーバーでのサービス URL へのアクセスの有効化](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)に関する記事の説明に従ってください。

1. [Microsoft Defender セキュリティ センター ポータル](https://securitycenter.windows.com/)を開きます。 ポータルの機能とアイコンの詳細については、「[Microsoft Defender セキュリティ センター ポータルの概要](/windows/security/threat-protection/microsoft-defender-atp/portal-overview)」を参照してください。 

## <a name="send-a-test-alert"></a>テスト アラートを送信する

Microsoft Defender for Endpoint で無害なテスト アラートを生成するには:

1. フォルダー「C:\test-MDATP-test」を作成します。
1. リモート デスクトップを使用してコンピューターにアクセスします。
1. コマンド ライン ウィンドウを開きます。
1. 次のコマンドをコピーし、プロンプトで実行します。 コマンド プロンプト ウィンドウは自動的に閉じます。

    ```powershell
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```
    :::image type="content" source="./media/security-center-wdatp/generate-edr-alert.png" alt-text="テスト アラートを生成するコマンドを含むコマンド プロンプト ウィンドウ。":::

1. コマンドが成功した場合、Azure Security Center ダッシュボードと Microsoft Defender for Endpoint ポータルに新しいアラートが表示されます。 このアラートは、表示されるまでに数分かかることがあります。
1. Security Center 内でアラートを確認するには、 **[セキュリティ通知]**  >  **[Suspicious Powershell CommandLine]\(疑わしい Powershell コマンド ライン\)** に移動します。
1. 調査ウィンドウで、リンクを選択して Microsoft Defender for Endpoint ポータルに移動します。

    > [!TIP]
    > アラートは、 **[情報]** 重要度を使用してトリガーされます。

## <a name="faq-for-security-centers-integrated-microsoft-defender-for-endpoint"></a>Security Center の統合された Microsoft Defender for Endpoint に関してよくあるご質問

- [Microsoft Defender for Endpoint のライセンス要件はどのようなものですか?](#what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint)
- [Microsoft Defender for Endpoint のライセンスが既にある場合、Azure Defender の割引を受けることができますか?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [サードパーティ製の EDR ツールから切り替える方法は?](#how-do-i-switch-from-a-third-party-edr-tool)

### <a name="what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint"></a>Microsoft Defender for Endpoint のライセンス要件はどのようなものですか?
Defender for Endpoint は、**Azure Defender for server** に追加料金なしで含まれています。 別の方法として、50 台以上のマシンに対して個別に購入することもできます。

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Microsoft Defender for Endpoint のライセンスが既にある場合、Azure Defender の割引を受けることができますか?
Microsoft Defender for Endpoint のライセンスを既に取得している場合は、Azure Defender ライセンスのその部分について料金を支払う必要はありません。

割引を確認するには、Security Center のサポート チームに連絡し、関連するライセンスごとに、関連するワークスペース ID、リージョン、ライセンス情報を提示してください。

### <a name="how-do-i-switch-from-a-third-party-edr-tool"></a>サードパーティ製の EDR ツールから切り替える方法は?
Microsoft 以外のエンドポイント ソリューションからの切り替えの詳しい手順については、Microsoft Defender for Endpoint の[移行の概要](/windows/security/threat-protection/microsoft-defender-atp/switch-to-microsoft-defender-migration)に関するドキュメントを参照してください。
  


## <a name="next-steps"></a>次のステップ

- [Azure Security Center でサポートされている機能とプラットフォーム](security-center-os-coverage.md)
- [Azure セキュリティ センターでのセキュリティに関する推奨事項の管理](security-center-recommendations.md):推奨事項を Azure リソースの保護に活かす方法について説明します。