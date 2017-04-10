---
title: "Azure Automation の概要 | Microsoft Docs"
description: "Azure Automation が提供する価値について説明し、よく寄せられる質問への回答を示して、Runbook と Azure Automation DSC の作成と使用を始められるようにします。"
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
keywords: "Automation とは, Azure Automation, Azure Automation の例"
ms.assetid: 0cf1f3e8-dd30-4f33-b52a-e148e97802a9
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/10/2016
ms.author: magoedte;bwren
translationtype: Human Translation
ms.sourcegitcommit: 15456a887aab29ac6c4ee74d15b8b8ef8d3047d3
ms.openlocfilehash: 4e7488f954ce50298aba0da83ae07f7a0de6a73d
ms.lasthandoff: 02/16/2017


---
# <a name="azure-automation-overview"></a>Azure Automation の概要
ユーザーは Microsoft Azure Automation を使用すると、クラウド環境およびエンタープライズ環境で一般的に実行される、手動で実行時間が長く、エラーが起こりやすく、頻繁に繰り返されるタスクを自動化する手段を入手できます。 時間を節約し、普段の管理タスクの信頼性が向上し、一定の間隔で自動的に実行されるようにスケジュールを設定します。 Runbook を使用してプロセスを自動化したり、Desired State Configuration を使用して構成管理を自動化したりすることができます。 この記事では、Azure Automation の概要を説明し、よく寄せられる質問に回答します。 異なるトピックの詳細については、このライブラリの他の記事を参照してください。

## <a name="automating-processes-with-runbooks"></a>Runbook でのプロセスの自動化
Runbook は、Azure Automation で自動化されたプロセスを実行する一連のタスクです。 仮想マシンの開始やログ エントリの作成といった簡単な処理だけでなく、他の小さい Runbook を結合して複数のリソースまたは複数のクラウドやオンプレミスの環境を対象とした複雑な処理を実行する複雑な Runbook も作成できます。  

たとえば、SQL Database が最大サイズに近づいたときにサイズを減らす既存の手動プロセスがあり、それにはサーバーへの接続、データベースへの接続、データベースの現在のサイズの取得、しきい値を超えたかどうかの確認、データベースの縮小とユーザーへの通知などの複数の手順が含まれる場合があります。 手動でこれらの各手順を実行するの代わりに、1 つのプロセスとしてこれらすべてのタスクを実行する Runbook を作成できます。 Runbook を開始し、SQL サーバー名、データベース名、受信者の電子メール アドレスなどの必要な情報を提供した後は、プロセスが完了するまでユーザーが手を出す必要はありません。 

## <a name="what-can-runbooks-automate"></a>Runbook が自動化できるものは何ですか。
Azure Automation での Runbook は、Windows PowerShell または Windows PowerShell Workflow に基づいているため、PowerShell で実行できることはすべて実行できます。 アプリケーションまたはサービスに API がある場合、Runbook はそれを操作できます。 アプリケーションに対する PowerShell モジュールがある場合は、そのモジュールを Azure Automation に読み込み、コマンドレットを Runbook に組み込むことができます。 Azure Automation の Runbook は、Azure クラウド内で実行するので、クラウド リソースや、クラウドからアクセスできる外部リソースにアクセスできます。 [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)を使用すると、Runbook はローカル データ センターで実行してローカル リソースを管理できます。 

## <a name="getting-runbooks-from-the-community"></a>コミュニティからの Runbook の取得
[Runbook Gallery](automation-runbook-gallery.md#runbooks-in-runbook-gallery) で入手できるマイクロソフトやコミュニティが提供する Runbook は、そのまま環境内で使用することも、独自の目的に合わせてカスタマイズすることもできます。 独自の Runbook を作成する方法を知るための参考情報としても役に立ちます。 他のユーザーにも役に立つと思われる Runbook をギャラリーに提供して貢献することもできます。 

## <a name="creating-runbooks-with-azure-automation"></a>Azure Automation での Runbook の作成
最初から[独自の Runbook を作成](automation-creating-importing-runbook.md)することも、[Runbook ギャラリー](http://msdn.microsoft.com/library/azure/dn781422.aspx)の Runbook を独自の要件に合わせて修正することもできます。 要件と PowerShell の経験に基づいて選択できる 4 つの [Runbook の種類](automation-runbook-types.md)があります。 PowerShell コードを直接操作する場合は、[PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) または [PowerShell Workflow Runbook](automation-runbook-types.md#powershell-workflow-runbooks) をオフラインでまたは Azure ポータルで[テキスト エディター](http://msdn.microsoft.com/library/azure/dn879137.aspx)を使用して編集できます。 コードを見ないで Runbook を編集する場合は、Azure ポータルで[グラフィカル エディター](automation-graphical-authoring-intro.md)を使用して、[グラフィカル Runbook](automation-runbook-types.md#graphical-runbooks) を作成できます。 

読むより見る方がよければ、 2015 年 5 月に行われた Microsoft Ignite セッションのビデオをご覧ください。 注: このビデオで説明されている概念と機能は正しいのですが、このビデオが録画されたころよりも Azure Automation は大幅に改善され、現在では Azure ポータルの UI が増え、サポートされる機能も増えました。

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3451/player]
> 
> 

## <a name="automating-configuration-management-with-desired-state-configuration"></a>Desired State Configuration での構成管理の自動化
[PowerShell DSC](https://technet.microsoft.com/library/dn249912.aspx) は管理プラットフォームであり、宣言型 PowerShell 構文を使用して物理ホストと仮想マシンの構成を管理、デプロイ、適用できます。 ターゲット コンピューターが自動的に取得して適用できる構成を、1 つの DSC プル サーバーで一元的に定義できます。 DSC が提供する一連の PowerShell コマンドレットを使用して、構成とリソースを管理できます。  

[Azure Automation DSC](automation-dsc-overview.md) は PowerShell DSC 用のクラウド ベースのソリューションであり、エンタープライズ環境に必要なサービスを提供します。  DSC リソースを Azure Automation で管理し、Azure クラウド内の DSC プル サーバーから構成を取得する仮想マシンおよび物理コンピューターに構成を適用できます。  また、Azure Automation DSC が提供するレポート サービスは、ノードが割り当てられている構成から逸脱したときや、新しい構成が適用されたときなど、重要なイベントをユーザーに通知します。 

## <a name="creating-your-own-dsc-configurations-with-azure-automation"></a>Azure Automation での独自の DSC 構成の作成
[DSC 構成](automation-dsc-overview.md#azure-automation-dsc-terms) はノードの望ましい状態を指定します。  複数のノードで同じ構成を適用し、すべてが同一の状態を維持できます。  ローカル コンピューターで任意のテキスト エディターを使用して構成を作成した後、Azure Automation にインポートしてコンパイルし、ノードに適用できます。

## <a name="getting-modules-and-configurations"></a>モジュールと構成の取得
Runbook や DSC の構成で使用できるコマンドレットを含む [PowerShell モジュール](automation-runbook-gallery.md#modules-in-powershell-gallery)を、[PowerShell ギャラリー](http://www.powershellgallery.com/)から取得できます。 このギャラリーを Azure ポータルから起動して、Azure Automation にモジュールを直接インポートできます。または、手動でダウンロードしてインポートすることもできます。 Azure ポータルからは、直接モジュールをインストールすることはできませんが、他のモジュールと同じように、ダウンロードしてインストールすることはできます。 

## <a name="example-practical-applications-of-azure-automation"></a>Azure Automation の実際の適用例
以下では、Azure Automation を使用したさまざまな自動化のシナリオの例を示します。 

* 仮想マシンを作成し、異なる Azure サブスクリプションにコピーします。 
* ローカル コンピューターから Azure BLOB ストレージ コンテナーへのファイルのコピーをスケジュールします。 
* サービス拒否攻撃が検出されたときにクライアントからの要求を拒否するなど、セキュリティ機能を自動化します。 
* コンピューターが構成されているセキュリティ ポリシーに常に適合しているようにします。
* クラウドとオンプレミスのインフラストラクチャへのアプリケーション コードの継続的なデプロイメントを管理します。 
* ラボ環境用の Active Directory フォレストを Azure に作成します。 
* DB が最大サイズに近づいたら、SQL Database のテーブルを切り捨てます。 
* Azure Web サイトの環境設定をリモートから更新します。 

## <a name="how-does-azure-automation-relate-to-other-automation-tools"></a>Azure Automation と他のオートメーション ツールを関連付けるにはどうすればよいですか。
[Service Management Automation (SMA)](http://technet.microsoft.com/library/dn469260.aspx) は、プライベート クラウドでの管理タスクを自動化するためのものです。 [Microsoft Azure Pack](https://www.microsoft.com/en-us/server-cloud/) のコンポーネントとしてデータ センターにローカルにインストールされます。 SMA と Azure Automation では Windows PowerShell と Windows PowerShell Workflow と同じ Runbook 形式を使用しますが、SMA では [グラフィカル Runbook](automation-graphical-authoring-intro.md)はサポートされません。  

[System Center 2012 Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) はオンプレミスのリソースを自動化するためのものです。 Azure Automation および Service Management Automation とは異なる形式の Runbook を使用し、スクリプトを作成する必要なしに Runbook を作成するためのグラフィカル インターフェイスを備えています。 その Runbook は、Orchestrator 用に特に作成された統合パックのアクティビティで構成されます。 

## <a name="where-can-i-get-more-information"></a>詳細な情報はどこで入手できますか。
Azure Automation や独自の Runbook の作成方法の詳細を確認できる多数のリソースが用意されています。 

* **Azure Automation ライブラリ** です。 このライブラリの記事では、Azure Automation の構成や管理、また独自の Runbook の作成に関する完全なドキュメントが提供されます。 
* [Azure PowerShell コマンドレット](http://msdn.microsoft.com/library/jj156055.aspx) では、Windows PowerShell を使用した Azure の操作の自動化に関する情報が提供されます。 Runbook ではこれらのコマンドレットを使用して Azure リソースを操作します。 
* [管理ブログ](https://azure.microsoft.com/blog/tag/azure-automation/) では、Azure Automation およびその他の Microsoft の管理テクノロジに関する最新情報を提供しています。 このブログをサブスクライブすると、Azure Automation チームによる最新の情報を把握できます。 
* [Automation フォーラム](http://go.microsoft.com/fwlink/p/?LinkId=390561) では Azure Automation に関する質問を投稿でき、マイクロソフトや Automation コミュニティから回答を取得できます。 
* [Azure Automation コマンドレット](https://msdn.microsoft.com/library/mt244122.aspx) は、管理タスクを自動化するための情報を提供します。 Automation アカウント、資産、Runbook、DSC を管理するコマンドレットが含まれます。

## <a name="can-i-provide-feedback"></a>フィードバックを提供できますか。
**フィードバックをお待ちしています。** Azure Automation の Runbook ソリューションや統合モジュールを探索している場合は、スクリプト センターにスクリプトの要求を投稿することができます。 Azure Automation に関するフィードバックや機能に関するご要望は、 [User Voice](http://feedback.windowsazure.com/forums/34192--general-feedback)にお寄せください。 よろしくお願いいたします。 


