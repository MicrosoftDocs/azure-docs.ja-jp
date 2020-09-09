---
title: Microsoft Defender Advanced Threat Protection - Azure Security Center
description: このドキュメントでは、Azure Security Center と Microsoft Defender Advanced Threat Protection の統合について説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2020
ms.author: memildin
ms.openlocfilehash: f8ed7dc2caf3ad471ea7d4bb2fc8213114aad109
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089704"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>Microsoft Defender Advanced Threat Protection と Azure Security Center

Azure Security Center は、包括的なエンドポイントの検出と応答 (EDR) 機能を提供する [Microsoft Defender Advanced Threat Protection (ATP)](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp) と統合されます。

## <a name="microsoft-defender-atp-features-in-security-center"></a>Security Center の Microsoft Defender ATP 機能

Microsoft Defender ATP では次のものが提供されます。

- **高度な侵害後検出センサー**:Windows サーバー用の Microsoft Defender ATP センサーにより、ありとあらゆる動作の信号が収集されます。

- **分析に基づくクラウド利用の侵害後検出**:Microsoft Defender ATP は、脅威の変化にすばやく適応します。 高度な分析とビッグ データを使用します。 Microsoft Defender ATP は、Windows、Azure、Office 全体の信号を使用したインテリジェント セキュリティ グラフによって強化され、これにより未知の脅威が検出されます。 アクションにつながるアラートを提供し、迅速に対応できるようにします。

- **脅威インテリジェンス**:Microsoft Defender ATP では、攻撃者のツール、テクニック、およびプロシージャが識別されると、アラートが生成されます。 Microsoft の脅威ハンターおよびセキュリティ チームによって生成され、パートナーによって提供されるインテリジェンスによって強化されたデータを使用します。


Defender ATP を Azure Security Center と統合することによって、次の追加機能を利用することもできます。

- **自動オンボード**:統合により、Azure Security Center によって監視されている Windows サーバー向けの Microsoft Defender ATP センサーが自動的に有効になります (Windows Server 2019 を実行している場合を除く)。

- **1 つのウィンドウ**:Azure Security Center コンソールには、Microsoft Defender ATP のアラートが表示されます。 さらに調査するには、Microsoft Defender ATP を使用します。 Microsoft Defender ATP には、アラート プロセス ツリーやインシデント グラフなどの追加情報が表示されます。 最大 6 か月前まで遡って、すべての動作を示す詳細なマシン タイムラインを見ることもできます。

    ![アラートに関する詳細情報が表示された Microsoft Defender ATP ページ](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>プラットフォームのサポート

Security Center の Microsoft Defender ATP では、Windows Server 2016、2012 R2、2008 R2 SP1 での検出がサポートされています。 Azure VM の場合、Standard レベルのサブスクリプションが必要です。Azure 以外の VM の場合は、ワークスペース レベルのみで Standard レベルが必要です。

この統合を使用したサーバー エンドポイントの監視は、Office 365 GCC のお客様に対して無効になっています。

## <a name="data-storage-location"></a>データ ストレージの場所

Azure Security Center を使用してサーバーを監視すると、Microsoft Defender ATP テナントが自動的に作成されます。 Microsoft Defender ATP によって収集されたデータは、プロビジョニング時に識別されたテナントの geo ロケーションに格納されます。 偽名フォームの顧客データは、米国の中央のストレージおよび処理システムにも格納される可能性があります。 

構成されると、データが格納されている場所を変更することはできません。 データを別の場所に移動する必要がある場合は、Microsoft サポートに連絡してテナントをリセットしてください。


## <a name="onboarding-servers-to-security-center"></a>Security Center にサーバーをオンボードする 

サーバーを Security Center にオンボードするには、Microsoft Defender ATP サーバーのオンボードから **[Azure Security Center に移動]** をクリックしてサーバーをオンボードします。

1. **[オンボード]** 領域で、データを保存するワークスペースを選択または作成します。

2. すべてのワークスペースが表示されない場合は、アクセス許可が不足している可能性があります。ワークスペースが Azure Security の Standard 価格レベルに設定されていることを確認してください。 詳細については、「[Azure Security Center を Standard レベルへアップグレードすることによるセキュリティ強化](security-center-pricing.md)」を参照してください。
    
3. **[サーバーの追加]** を選択すると、Log Analytics エージェントをインストールする手順が表示されます。 

4. オンボード後は、 **[計算とアプリ]** でマシンを監視できます。

   ![コンピューターをオンボードする](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>Microsoft Defender ATP の統合を有効にする

Microsoft Defender ATP の統合が有効になっているかどうかを確認するには、 **[セキュリティ センター]**  >  **[Pricing & settings]\(価格と設定\)** を選択し、ご利用のサブスクリプションをクリックします。
ここでは、現在有効になっている統合を確認できます。

  ![Microsoft Defender ATP の統合が有効になっている Azure Security Center の脅威検出設定ページ](media/security-center-wdatp/enable-integrations.png)

- Azure Security Center の Standard レベルにサーバーを既にオンボードした場合は、何も行う必要はありません。 Azure Security Center によって、サーバーは Microsoft Defender ATP に自動的にオンボードされます。 このオンボードには最大で 24 時間かかることがあります。

- Azure Security Center の Standard レベルにサーバーをオンボードしたことがない場合は、通常どおりにサーバーを Azure Security Center にオンボードします。

- Microsoft Defender ATP からサーバーをオンボードした場合:
  - [サーバー マシンをオフボードする方法](https://go.microsoft.com/fwlink/p/?linkid=852906)については、ガイダンスの資料をご覧ください。
  - これらのサーバーを Azure Security Center にオンボードします。

## <a name="access-to-the-microsoft-defender-atp-portal"></a>Microsoft Defender ATP ポータルにアクセスする

1. 「[ポータルへのユーザー アクセスの割り当て](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access)」の指示に従ってください。

1. 匿名トラフィックをブロックしているプロキシまたはファイアウォールがあるかどうかを確認します。 Defender ATP センサーはシステム コンテキストと接続するため、匿名トラフィックを許可する必要があります。 Microsoft Defender ATP ポータルに制限なく確実にアクセスするには、「[プロキシ サーバーで Microsoft Defender ATP サービス URL へのアクセスを有効にする](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)」の説明に従ってください。

## <a name="test-the-feature"></a>機能をテストする

Microsoft Defender ATP で無害なテスト アラートを生成するには:

1. フォルダー「C:\test-MDATP-test」を作成します。

1. リモート デスクトップを使用して、Windows Server 2012 R2 VM または Windows Server 2016 VM にアクセスします。 コマンド ライン ウィンドウを開きます。

1. 次のコマンドをコピーし、プロンプトで実行します。 コマンド プロンプト ウィンドウは自動的に閉じます。

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```

   ![上記のコマンドが表示されているコマンド プロンプト ウィンドウ](media/security-center-wdatp/image4.jpeg)

1. コマンドが成功した場合、Azure Security Center ダッシュボードと Microsoft Defender ATP ポータルに新しいアラートが表示されます。 このアラートは、表示されるまでに数分かかることがあります。

1. Security Center 内でアラートを確認するには、 **[セキュリティ通知]**  >  **[Suspicious Powershell CommandLine]\(疑わしい Powershell コマンド ライン\)** に移動します。

1. 調査ウィンドウで、リンクを選択して Microsoft Defender ATP ポータルに移動します。

## <a name="next-steps"></a>次のステップ

- [Azure Security Center でサポートされている機能とプラットフォーム](security-center-os-coverage.md)
- [Azure Security Center でのセキュリティ ポリシーの設定](tutorial-security-policy.md):Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明します。
- [Azure セキュリティ センターでのセキュリティに関する推奨事項の管理](security-center-recommendations.md):推奨事項を Azure リソースの保護に活かす方法について説明します。
- [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md):Azure リソースの正常性を監視する方法について説明しています。