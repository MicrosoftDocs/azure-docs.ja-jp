---
title: Azure 仮想マシンのパフォーマンス診断 | Microsoft Docs
description: Windows 用の Azure パフォーマンス診断について説明します。
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: przlplx
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 9/20/2018
ms.author: anandh
ms.openlocfilehash: c2089f9f6267f318dafe641a6a5b22e7e87427ca
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57441068"
---
# <a name="performance-diagnostics-for-azure-virtual-machines"></a>Azure 仮想マシンのパフォーマンス診断

パフォーマンス診断ツールを使用すると、Windows 仮想マシン (VM) に影響を与える可能性のあるパフォーマンスの問題を解決するために役立ちます。 サポートされているトラブルシューティングのシナリオには、既知の問題とベスト プラクティスや、VM のパフォーマンス低下、または CPU、ディスク空間、メモリの高使用率を伴う複雑な問題を迅速に確認することなどがあります。 

Azure portal から直接パフォーマンス診断を実行することもできます。ポータルでは、分析情報や、さまざまなログ、豊富な構成、診断データに関するレポートも確認できます。 Microsoft サポートに問い合わせる前に、パフォーマンス診断を実行し、分析情報と診断データを確認することをお勧めします。

> [!NOTE]
> 現在、パフォーマンス診断は .NET SDK バージョン 4.5 以降のバージョンがインストールされている Windows VM でサポートされています。 クラシック VM でパフォーマンス診断を実行する手順については、[Azure パフォーマンス診断 VM 拡張機能](performance-diagnostics-vm-extension.md)に関するページを参照してください。

### <a name="supported-operating-systems"></a>サポートされているオペレーティング システム
Windows 10、Windows 8、Windows 8 Enterprise、Windows 8 Pro、Windows 8.1、Windows Server 2016、Windows Server 2012、Windows Server 2012 Datacenter、Windows Server 2012 R2、Windows Server 2012 R2 Datacenter、Windows Server 2012 R2 Standard、Windows Server 2012 Standard、Windows Server 2008 R2、Windows Server 2008 R2 Datacenter、Windows Server 2008 R2 Enterprise、Windows Server 2008 R2 Foundation、Windows Server 2008 R2 SP1、Windows Server 2008 R2 Standard。

## <a name="install-and-run-performance-diagnostics-on-your-vm"></a>VM にパフォーマンス診断をインストールして実行する
パフォーマンス診断では、[PerfInsights](https://aka.ms/perfinsights) という診断ツールを実行する VM 拡張機能がインストールされます。 パフォーマンス診断をインストールして実行するには、次の手順を実行します。
1.  コマンドの左側の列にある **[仮想マシン]** を選択します。
1.  VM 名の一覧から、診断を実行する VM を選択します。
1.  コマンドの右側の列で、**[パフォーマンス診断]** を選択します。

    ![パフォーマンス診断の [インストール] ボタンが強調表示されている Azure portal のスクリーンショット](media/performance-diagnostics/performance-diagnostics-install.png)

    > [!NOTE]
    > このスクリーンショットでは、VM 名のブレードは非表示です。
1. ストレージ アカウントを選択します (省略可能)

    1 つのストレージ アカウントを使用して複数の VM のパフォーマンス診断結果を保存する場合は、ツールバーの **[設定]** ボタンをクリックしてストレージ アカウントを選択します。 ストレージ アカウントを選択したら **[OK]** ボタンをクリックします。

    ストレージ アカウントを指定しない場合、既定で新しいストレージ アカウントが作成されます。

    ![[設定] ツール バーのボタンが強調表示されている [パフォーマンス診断] ブレードのスクリーンショット](media/performance-diagnostics/settings-button.png)

    ![[パフォーマンス診断の設定] ブレードのストレージ アカウントの選択のスクリーンショット](media/performance-diagnostics/select-storage-account.png)

1. **[パフォーマンス診断のインストール]** ボタンを選択します。
1. インストールが完了した後で診断を実行するには、**[診断の実行]** チェック ボックスをオンにします。 この選択を行うと、パフォーマンス分析シナリオと関連オプションを選択できるようになります。

    ![パフォーマンス診断のインストール ボタンのスクリーンショット](media/performance-diagnostics/install-diagnostics-button.png)

## <a name="select-an-analysis-scenario-to-run"></a>実行する分析シナリオを選択する

次の分析シナリオは、Azure portal から使用できます。 発生しているパフォーマンスの問題に応じて、分析を選択します。 分析に必要な期間とトレースのオプションを選択します。

* **パフォーマンスのクイック分析**  
    既知の問題を確認し、ベスト プラクティスを分析し、診断データを収集します。 この分析の実行には数分かかります。 [詳細情報](https://aka.ms/perfinsights/quick)

* **パフォーマンス分析**  
    クイック パフォーマンス分析のすべてのチェックを含み、高いリソース使用量を監視します。 このバージョンは、CPU、メモリ、ディスク使用量などの一般的なパフォーマンスの問題を解決するために使用します。 この分析には、選択した期間に応じて 30 秒から 15 分かかります。 [詳細情報](https://aka.ms/perfinsights/vmslow) 
    
* **高度なパフォーマンス分析**  
    パフォーマンス分析のすべてのチェックを含み、以下のセクションに記載されている 1 つまたは複数のトレースを収集します。 このシナリオは、追加のトレースが必要な複雑な問題を解決するために使用します。 このシナリオを長期間実行すると、VM のサイズと選択したトレース オプションに応じて、診断の出力の全体的なサイズが大きくなります。 この分析の実行には、選択した期間に応じて 30 秒から 15 分かかります。 [詳細情報](https://aka.ms/perfinsights/advanced) 
    
* **Azure Files 分析**  
    パフォーマンス分析のすべてのチェックを含み、ネットワーク トレースと SMB カウンターをキャプチャします。 このシナリオは、Azure ファイルのパフォーマンスを解決するために使用します。 この分析の実行には、選択した期間に応じて 30 秒から 15 分かかります。 [詳細情報](https://aka.ms/perfinsights/azurefiles)


![[パフォーマンス診断] ブレード内の [診断の実行] ウィンドウのスクリーンショット](media/performance-diagnostics/run-diagnostics-pane.png)

### <a name="provide-symptoms-optional"></a>現象を指定する (省略可能)
一覧から事前に選択した現象を選択するか、新しい現象を追加します。 これにより、以降の分析の改善に役立ちます。 

### <a name="provide-support-request-number-if-available-optional"></a>使用できる場合はサポート リクエスト番号を指定する (省略可能)
既存のサポート チケットで Microsoft サポート エンジニアとやり取りしている場合は、サポート チケット番号を指定します。 

### <a name="review-the-privacy-policy-and-legal-terms-and-select-the-check-box-to-acknowledge-required"></a>プライバシー ポリシーと法律条項を確認し、同意のチェック ボックスをオンにする (必須)
診断を実行するには、法律条項とプライバシー ポリシーに同意する必要があります。

### <a name="select-ok-to-run-the-diagnostics"></a>[OK] を選択して診断を実行する 
パフォーマンス診断のインストールが開始されると、通知が表示されます。 インストールが完了すると、インストールが成功したことを示す通知が表示されます。 指定された期間、選択した分析が実行されます。 このとき、正確な時刻で診断データをキャプチャできるようにパフォーマンスの問題を再現することをお勧めします。 

分析が完了すると、指定したストレージ アカウントの Azure テーブルとバイナリ ラージ オブジェクト (BLOB) コンテナーに、次の項目がアップロードされます。

*   実行に関するすべての分析情報と関連情報
*   ログ ファイルを含む出力圧縮 (.zip) ファイル (ファイル名は **PerformanceDiagnostics_yyyy-MM-dd_hh-mm-ss-fff.zip**)
*   HTML レポート

アップロード後、Azure portal に新しい診断レポートが一覧表示されます。

![[パフォーマンス診断] ブレードの診断レポート一覧のスクリーンショット](media/performance-diagnostics/diagnostics-report-list.png)

## <a name="how-to-change-performance-diagnostics-settings"></a>パフォーマンス診断設定を変更する方法
**[設定]** ツールバーのボタンを使用して、診断の分析情報と出力を保存できるストレージ アカウントを変更します。 パフォーマンス診断を使用する複数の VM に対して同じストレージ アカウントを使用できます。 ストレージ アカウントを変更しても、以前のレポートと分析情報は削除されません。 ただし、診断レポートの一覧には表示されなくなります。 

## <a name="review-insights-and-performance-diagnostics-report"></a>分析情報とパフォーマンス診断レポートを確認する
各診断の実行には、分析情報と推奨事項、影響を受けるリソース、ログ ファイル、収集されたその他のさまざまな診断情報、およびオフラインで表示するためのレポートの一覧が含まれています。 収集されたすべての診断データの詳細な一覧については、「[What kind of information is collected by PerfInsights?](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-use-perfinsights#what-kind-of-information-is-collected-by-perfinsights)」(PerfInsights で収集される情報の種類) を参照してください。 

### <a name="select-a-performance-diagnostics-report"></a>パフォーマンス診断レポートを選択する
診断レポート一覧から、実行されたすべての診断レポートを見つけることができます。 この一覧には、使用された分析、検出された分析情報、およびそれらの影響レベルに関する詳細情報が含まれています。 詳細を表示する行を選択します。

![[パフォーマンス診断] ブレードから診断レポートを選択するスクリーンショット](media/performance-diagnostics/select-report.png)

### <a name="review-a-performance-diagnostics-report"></a>パフォーマンス診断レポートを確認する
各パフォーマンス診断レポートには、いくつかの分析情報が含まれ、高、中、低の影響レベルが示されます。 各分析情報には、問題を軽減するための推奨事項も含まれています。 分析情報はグループ化されているので、簡単にフィルター処理できます。 

影響レベルは、不適切な構成、既知の問題、または他のユーザーから報告されている問題などの要因に基づいて、パフォーマンスの問題の可能性を表します。 表示されている問題の 1 つまたは複数がまだ発生していない可能性があります。 たとえば、SQL ログファイルとデータベース ファイルを同じデータ ディスクに配置している場合があります。 データベースの使用率が高い場合、この条件がボトルネックやその他のパフォーマンスの問題の原因である可能性が高くなりますが、使用率が低い場合は問題に気付かない可能性があります。

![パフォーマンス診断レポートの概要ブレードのスクリーンショット](media/performance-diagnostics/performance-diagnostics-report-overview.png)

### <a name="reviewing-performance-diagnostics-insights-and-recommendations"></a>パフォーマンス診断の分析情報と推奨事項を確認する
分析情報を選択すると、影響を受けるリソース、推奨される軽減策、および参照リンクに関する詳細を確認できます。 

![[パフォーマンス診断の分析情報の詳細] のスクリーンショット](media/performance-diagnostics/insight-detail.png)

### <a name="download-and-review-the-full-performance-diagnostics-report"></a>詳細なパフォーマンス診断レポートをダウンロードして確認する
**[レポートのダウンロード]** ボタンを使用して、ストレージとネットワークの構成、パフォーマンス カウンター、トレース、プロセスの一覧、ログなど、その他のさまざまな診断情報を含む HTML レポートをダウンロードできます。 内容は選択した分析によって変わります。 高度なトラブルシューティングのために、高い CPU 使用率、高いディスク使用量、過剰なメモリを使用するプロセスに関連する追加情報や対話型のグラフをレポートに含めることができます。 パフォーマンス診断レポートの詳細については、「[Review diagnostics report](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-use-perfinsights#review-the-diagnostics-report)」(診断レポートの確認) を参照してください。

## <a name="manage-performance-diagnostics-reports"></a>パフォーマンス診断レポートを管理する
1 つまたは複数のパフォーマンス診断レポートを削除するには、**[レポートの削除]** ボタンを使用します。

## <a name="how-to-uninstall-performance-diagnostics"></a>パフォーマンス診断をアンインストールする方法
VM からパフォーマンス診断をアンインストールすることができます。 この操作で VM 拡張機能は削除されますが、ストレージ アカウントにある診断データには影響がありません。 

![[アンインストール] ボタンが強調表示された [パフォーマンス診断] ブレード ツールバーのスクリーンショット](media/performance-diagnostics/uninstal-button.png)

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="where-is-the-diagnostics-data-from-my-vm-stored"></a>VM の診断データはどこに保存されていますか。 
すべてのパフォーマンス診断の分析情報とレポートは、お客様固有のストレージ アカウントに保存されています。 分析情報は Azure テーブル内に保存されています。 レポートの圧縮ファイルは、azdiagextnresults というバイナリ ラージ オブジェクト (BLOB) コンテナーに保存されています。

ストレージ アカウント情報を確認するには、ツールバーの [設定] ボタンを使用します。 

### <a name="how-do-i-share-this-data-with-microsoft-customer-support"></a>このデータを Microsoft カスタマー サポートと共有するにはどうすればよいですか。 
診断レポートを Microsoft と共有するには、複数の方法があります。

**オプション 1:** 最新のレポートを自動的に共有する  
Microsoft とのサポート チケットを開くときに、パフォーマンス診断レポートを共有することが重要です。 診断の実行時に (**[診断情報を Microsoft と共有することに同意します]** チェック ボックスをオンにして) この情報を Microsoft と共有することを選択した場合、Microsoft は、実行日から最大 30 日間、出力 zip ファイルへの SAS リンクを使用して、お客様のストレージ アカウントのレポートにアクセスできます。 サポート エンジニアが使用できるのは、最新のレポートのみです。 

**オプション 2:** 診断レポートの圧縮ファイルのために Shared Access Signature を生成する  
Shared Access Signature を使用して、レポートの圧縮ファイルへのリンクを共有することができます。 そのためには、次の手順に従います。 
1.  Azure portal で、診断データが保存されているストレージ アカウントを参照します。
1.  **[Blob service]** セクションにある **[BLOB]** を選択します。 
1.  **azdiagextnresults** コンテナーを選択します。
1.  共有するパフォーマンス診断出力の圧縮ファイルを選択します。
1.  **[SAS の生成]** タブで共有の条件を選択します。 
1.  **[BLOB SAS トークンおよび URL を生成]** をクリックします。
1.  **[BLOB SAS URL]** をコピーし、サポート エンジニアに伝えます。 

**オプション 3:** ストレージ アカウントからレポートをダウンロードする

方法 2 の手順 1 から 4 を使用して、パフォーマンス診断レポートの圧縮ファイルを見つけることもできます。 ファイルのダウンロードを選択し、電子メールで共有するか、サポート エンジニアにファイルのアップロード手順を尋ねます。  

### <a name="how-do-i-capture-the-diagnostics-data-at-the-correct-time"></a>正確な時刻で診断データをキャプチャするにはどうすればよいですか。
各パフォーマンス診断の実行には 2 つの段階があります。 
1.  パフォーマンス診断 VM 拡張機能をインストールまたは更新する。
1.  指定した期間、診断を実行する。

現在のところ、VM 拡張機能のインストールが完了したときを正確に把握する簡単な方法はありません。 一般的に、VM 拡張機能のインストールには約 45 秒から 1 分かかります。 VM 拡張機能をインストールした後は、パフォーマンス診断でトラブルシューティングのために正しいデータ セットがキャプチャされるように再現手順を実行できます。 

## <a name="next-steps"></a>次の手順
パフォーマンス診断の分析情報とレポートを確認しても問題の原因を特定できず、さらにサポートが必要な場合は、Microsoft カスタマー サポートとのサポート チケットを開くことができます。 

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、**[サポートの要求]** をクリックします。 Azure サポートの使用方法の詳細については、「[Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。
