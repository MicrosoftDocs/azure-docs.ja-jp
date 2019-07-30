---
title: Windows Defender Advanced Threat Protection と Azure Security Center
description: このドキュメントでは、Azure Security Center と Windows Defender Advanced Threat Protection の統合について説明します。
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2018
ms.author: v-mohabe
ms.openlocfilehash: 87f5a14bcd6003ad81b663ed97e5349dcbff2a30
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68296529"
---
# <a name="windows-defender-advanced-threat-protection-with-azure-security-center"></a>Windows Defender Advanced Threat Protection と Azure Security Center

Azure Security Center のクラウド ワークロード保護プラットフォーム オファリングは、[Windows Defender Advanced Threat Protection](https://www.microsoft.com/en-us/WindowsForBusiness/windows-atp) (ATP) との統合によって拡張されています。
この変更により、包括的なエンドポイントの検出と対応 (EDR) 機能が実現されます。 Windows Defender ATP の統合により、異常を見つけることができます。 また、Azure Security Center によって監視されているサーバー エンドポイントでの高度な攻撃を検出して対応することもできます。

## <a name="windows-defender-atp-features-in-security-center"></a>Security Center の Windows Defender ATP 機能

Windows Defender ATP を使用すると、次の機能が得られます。

- **次世代の侵害後検出センサー**:Windows サーバー用の Windows Defender ATP センサーは、ありとあらゆる動作の信号を収集します。

- **分析に基づくクラウド利用の侵害後検出**:Windows Defender ATP は、脅威の変化にすばやく適応します。 高度な分析とビッグ データを使用します。 Windows Defender ATP は、Windows、Azure、Office 全体の信号を使用したインテリジェント セキュリティ グラフによって強化され、未知の脅威を検出します。 アクションにつながるアラートを提供し、迅速に対応できるようにします。

- **脅威インテリジェンス**:Windows Defender ATP は、攻撃者のツール、テクニック、およびプロシージャを識別します。 これらを検出すると、アラートを生成します。 Microsoft の脅威ハンターおよびセキュリティ チームによって生成され、パートナーによって提供されるインテリジェンスによって強化されたデータを使用します。

現在、Azure Security Center では次の機能を利用できます。

- **自動オンボード**:Windows Defender ATP センサーは、Azure Security Center にオンボードされた Windows サーバーに対して自動的に有効になります。

- **1 つのウィンドウ**:Azure Security Center コンソールには、Windows Defender ATP のアラートが表示されます。

- **マシンの詳細な調査**:Azure Security Center のお客様は、Windows Defender ATP コンソールにアクセスして、詳細な調査を実行し、侵害の範囲を明らかにすることができます。

![アラートの一覧と各アラートに関する一般的な情報が表示されている Azure Security Center](media/security-center-wdatp/image1.png)

Windows Defender ATP にピボットすることによって、アラートをさらに調査できます。 アラート プロセス ツリーやインシデント グラフなどの追加情報を表示できます。 最大 6 か月前まで遡って、すべての動作を示す詳細なマシン タイムラインを見ることもできます。

![アラートに関する詳細情報が表示された Windows Defender ATP ページ](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>プラットフォームのサポート

Security Center の Windows Defender ATP は、Standard サービス サブスクリプションに属する Windows Server 2012 R2 および Windows Server 2016 オペレーティング システムでの検出をサポートしています。

> [!NOTE]
> Azure Security Center を使用してサーバーを監視すると、Windows Defender ATP テナントが自動的に作成され、Windows Defender ATP データが既定ではヨーロッパに格納されます。 データを別の場所に移動する必要がある場合は、Microsoft サポートに連絡してテナントをリセットする必要があります。

## <a name="onboarding-servers-to-security-center"></a>Security Center にサーバーをオンボードする 

サーバーを Security Center にオンボードするには、Windows Defender ATP サーバーのオンボードから **[Azure Security Center に移動] をクリックしてサーバーをオンボード**します。

1. **[オンボード]** ブレードで、データを保存するワークスペースを選択または作成します。 <br>
2. すべてのワークスペースが表示されない場合は、アクセス許可が不足している可能性があります。ワークスペースが Azure Security Standard レベルに設定されていることを確認してください。 詳細については、「[Azure Security Center を Standard レベルへアップグレードすることによるセキュリティ強化](security-center-pricing.md)」を参照してください。
    
3. **[サーバーの追加]** を選択すると、Microsoft Monitoring Agent をインストールする手順が表示されます。 

4. オンボード後は、 **[計算とアプリ]** でマシンを監視できます。

   ![コンピューターをオンボードする](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-windows-defender-atp-integration"></a>Windows Defender ATP の統合を有効にする

Windows Defender ATP の統合が有効になっているかどうかを確認するには、 **[セキュリティ センター]**  >  **[Pricing & settings]\(価格と設定\)** を選択し、ご利用のサブスクリプションをクリックします。

  ![Azure Security Center のポリシー管理](media/security-center-wdatp/policy-management.png)

ここでは、現在有効になっている統合を確認できます。

  ![Windows Defender ATP の統合が有効になっている Azure Security Center の脅威検出設定ページ](media/security-center-wdatp/enable-integrations.png)

- Azure Security Center の Standard レベルにサーバーを既にオンボードした場合は、何も行う必要はありません。 Azure Security Center によって、サーバーは Windows Defender ATP に自動的にオンボードされます。 これには最大で 24 時間かかることがあります。

- Azure Security Center の Standard レベルにサーバーをオンボードしたことがない場合は、通常どおりにサーバーを Azure Security Center にオンボードします。

- Windows Defender ATP からサーバーをオンボードした場合:
  - [サーバー マシンをオフボードする方法](https://go.microsoft.com/fwlink/p/?linkid=852906)については、ガイダンスの資料をご覧ください。
  - これらのサーバーを Azure Security Center にオンボードします。

## <a name="access-to-the-windows-defender-atp-portal"></a>Windows Defender ATP ポータルにアクセスする

「[ポータルへのユーザー アクセスの割り当て](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/assign-portal-access-windows-defender-advanced-threat-protection)」の指示に従ってください。

## <a name="set-the-firewall-configuration"></a>ファイアウォールの構成を設定する

匿名のトラフィックをブロックするプロキシまたはファイアウォールがある場合、Windows Defender ATP センサーはシステム コンテキストから接続するので、匿名のトラフィックが許可されていることを確認します。 「[Enable access to Windows Defender ATP service URLs in the proxy server](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/configure-proxy-internet-windows-defender-advanced-threat-protection#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)」(プロキシ サーバーで Windows Defender ATP サービス URL へのアクセスを有効にする) の説明に従ってください。

## <a name="test-the-feature"></a>機能をテストする

Windows Defender ATP で無害なテスト アラートを生成するには:

1. リモート デスクトップを使用して、Windows Server 2012 R2 VM または Windows Server 2016 VM にアクセスします。  コマンド プロンプト ウィンドウを開きます。

2. 次のコマンドをコピーし、プロンプトで実行します。 コマンド プロンプト ウィンドウは自動的に閉じます。

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-WDATP-test\\invoice.exe'); Start-Process 'C:\\test-WDATP-test\\invoice.exe'
    ```

   ![上記のコマンドが表示されているコマンド プロンプト ウィンドウ](media/security-center-wdatp/image4.jpeg)

3. コマンドが成功した場合、Azure Security Center ダッシュボードと Windows Defender ATP ポータルに新しいアラートが表示されます。 このアラートは、表示されるまでに数分かかることがあります。

4. Security Center 内でアラートを確認するには、 **[セキュリティ通知]**  >   **[Suspicious Powershell CommandLine]\(疑わしい Powershell コマンド ライン\)** に移動します。

5. 調査ウィンドウで、リンクを選択して Windows Defender ATP ポータルに移動します。

## <a name="next-steps"></a>次の手順

- [Azure Security Center でのセキュリティ ポリシーの設定](tutorial-security-policy.md):Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明します。
- [Azure セキュリティ センターでのセキュリティに関する推奨事項の管理](security-center-recommendations.md):推奨事項を Azure リソースの保護に活かす方法について説明します。
- [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md):Azure リソースの正常性を監視する方法について説明しています。
