---
title: "Azure 診断のバージョン履歴 | Microsoft Docs"
description: "さまざまな Microsoft Azure SDK バージョンに付属している Azure 診断の異なるバージョンの変更の説明。"
services: multiple
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 0ae6738e-d3bf-4774-86bf-6e30a9d16c79
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/12/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 965ef4b0f41699fa657b4873cb177c7f0c32c855
ms.lasthandoff: 03/22/2017


---
# <a name="microsoft-azure-diagnostics-version-history"></a>Microsoft Azure 診断のバージョン履歴
Azure 診断を初めてご利用になりますか? [Azure 診断の概要](azure-diagnostics.md)に関するページを参照してください。

Azure SDK の各バージョンは、Azure 診断の新しいバージョンに通常付属します。 次の表では、SDK のリリースに関連付けられている Azure SDK および Azure 診断のバージョンについて説明します。

| Azure SDK のバージョン | Azure 診断のバージョン | モデル |
| --- | --- | --- |
| 1.x |1.0 |プラグイン |
| 2.0 から 2.4 |1.0 |" |
| 2.5 |1.2 |拡張機能 |
| 2.6 |1.3 |" |
| 2.7 |1.4 |" |
| 2.8 |1.5 |" |

最新バージョンは1.5 で、Azure SDK 2.8 に付属しています。 SDK に付属している Azure 診断拡張機能のバージョンは、ローカル エミュレーターのシナリオでのみ使用されます。 アプリケーションがビルドされている SDK のバージョンに関係なく、Azure で実行する場合にデプロイされたアプリケーションは自動的に最新バージョンを使用します。 ただし、最新の Azure SDK をインストールする場合を除き、新しい機能を利用するためのツールがすべてない可能性があります。

さまざまな機能と変更点について次に説明します。

## <a name="azure-sdk-28"></a>Azure SDK 2.8
Azure SDK 2.8 には、診断データを [Application Insights](../application-insights/app-insights-cloudservices.md) に送信することで、アプリケーションとシステムおよびインフラストラクチャのレベル全体の問題を診断しやすくする機能が追加されました。

## <a name="azure-26-diagnostics-changes"></a>Azure 2.6 の診断の変更
Visual Studio の Azure SDK 2.6 クラウド サービス プロジェクトでは、次の変更が行われました。 (これらの変更はそれ以降のバージョンの Azure SDK にも当てはまります。)

* ローカル エミュレーターで診断がサポートされるようになりました。 つまり Visual Studio での開発およびテスト時に診断データを収集し、開発中のアプリケーションで正しくトレースが作成されることを確認できます。 Visual Studio で Azure ストレージ エミュレーターを使ってクラウド サービス プロジェクトを実行している間に行われる診断データ収集が、接続文字列 `UseDevelopmentStorage=true` を指定すると有効になります。 すべての診断データは、(開発ストレージ) ストレージ アカウントに収集されます。
* 診断ストレージ アカウントの接続文字列 (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) の保存先が、再びサービス構成 (.cscfg) ファイルに戻されました。 Azure SDK 2.5 では、診断ストレージ アカウントが diagnostics.wadcfgx ファイルで指定されていました。

Azure SDK 2.4 以前と Azure SDK 2.6 以降とで、接続文字列の働きに大きな違いがいくつかあります。

* Azure SDK 2.4 以前では、診断プラグインが診断ログを転送するためのストレージ アカウント情報を取得する目的でラインタイムとして接続文字列を使用していました。
* Azure SDK 2.6 以降では、Visual Studio が発行時に適切なストレージ アカウント情報を使って診断拡張機能を構成する目的で診断接続文字列を使用します。 Visual Studio が発行時に使用する各種サービス構成に対し、異なるストレージ アカウントを接続文字列で定義することができます。 しかし、(Azure SDK 2.5 以降) 診断プラグインが使用できなくなったため、.cscfg ファイルだけでは診断拡張機能を有効にできません。 Visual Studio や PowerShell などのツールを使用して個別に拡張機能を有効にする必要があります。
* PowerShell を使用して診断拡張機能を構成するプロセスを単純化するために、Visual Studio からの出力パッケージには、ロールごとの診断拡張機能のパブリック構成 XML も含まれます。 Visual Studio は、診断接続文字列を使用して、パブリック構成に存在するストレージ アカウント情報を取り込みます。 このパブリック構成ファイルは、PaaSDiagnostics<RoleName>.PubConfig.xml という名前で拡張機能フォルダーに作成されます。 このファイルを PowerShell ベースのデプロイで使用し、各構成をロールにマップすることができます。
* .cscfg ファイル内の接続文字列は、Azure Portal で診断データにアクセスするときにも使用されるため、**[監視]** タブで確認できます。 ポータルで監視データを詳細出力するようにサービスを構成するには、この接続文字列が必要となります。

### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Azure SDK 2.6 以降へのプロジェクトの移行
Azure SDK 2.5 から Azure SDK 2.6 以降に移行するとき、.wadcfgx ファイルで指定した診断ストレージ アカウントがある場合、そのアカウントが維持されます。 さまざまなストレージ構成で異なるストレージ アカウントの使用の柔軟性を利用するには、接続文字列をプロジェクトに手動で追加する必要があります。 Azure SDK 2.4 以前から Azure SDK 2.6 にプロジェクトを移行する場合は、診断接続文字列が保持されます。 ただし、前のセクションでも触れたように、Azure SDK 2.6 では接続文字列の扱いが変更されているので注意してください。

### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>使用する診断ストレージ アカウントを Visual Studio がどのように決定するか
* 診断接続文字列が .cscfg ファイルに指定されている場合、Visual Studio は、発行時のほか、パッケージ化時にパブリック構成の xml ファイルを生成するときに、.cscfg ファイルに指定されている診断接続文字列を使用して診断拡張機能を構成します。
* 診断接続文字列が .cscfg ファイルに指定されていない場合、Visual Studio は .wadcfgx ファイルに指定されたストレージ アカウントを使用して、発行時、およびパッケージ化におけるパブリック構成 xml ファイルの生成時に診断拡張機能を構成するようにフォールバックします。
* .cscfg ファイルの診断接続文字列は、.wadcfgx ファイルのストレージ アカウントよりも優先されます。 診断接続文字列が .cscfg ファイルに指定されている場合、Visual Studio はそちらを使用し、.wadcfgx のストレージ アカウントは無視します。

### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>"開発ストレージ接続文字列の更新" チェック ボックスの機能
**[Microsoft Azure への公開時に診断とキャッシュのための開発ストレージの接続文字列を Microsoft Azure ストレージ アカウントの資格情報で更新する]** チェック ボックスの機能は、発行時に指定した Azure ストレージ アカウントで開発ストレージ アカウントの接続文字列を更新することです。

たとえば、このチェック ボックスをオンにして、診断接続文字列で `UseDevelopmentStorage=true`を指定するとします。 この診断接続文字列は、プロジェクトを Azure に発行するとき、公開ウィザードで指定されたストレージ アカウントに自動的に更新されます。 ただし、実際のストレージ アカウントが診断接続文字列として指定されている場合は、そのアカウントが代わりに使用されます。

## <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Azure SDK 2.4 以前と Azure SDK 2.5 以降の診断機能の違い
プロジェクトを Azure SDK 2.4 から Azure SDK 2.5 以降にアップグレードする場合、次の診断機能の違いに留意してください。

* **構成 API は非推奨となりました** – Azure SDK 2.4 以前のバージョンでは、プログラムから診断機能を構成できましたが、Azure SDK 2.5 以降ではプログラムによる診断の構成が非推奨扱いとなりました。 現在、診断の構成をコードで定義している場合、その診断機能を引き続き利用するためには、移行したプロジェクトでそれらの設定を最初から構成し直す必要があります。 Azure SDK 2.4 の診断構成ファイルは diagnostics.wadcfg で、Azure SDK 2.5 以降では diagnostics.wadcfgx です。
* **クラウド サービス アプリケーションの診断はロール レベルでのみ構成でき、インスタンス レベルでは構成できません。**
* **アプリケーションをデプロイするたびに診断の構成が更新されます** – サーバー エクスプローラーで診断の構成を変更してからアプリを再デプロイした場合、これによってパリティの問題が発生する可能性があります。
* **Azure SDK 2.5 以降では、クラッシュ ダンプがコードからではなく診断構成ファイルで構成されます** - クラッシュ ダンプをコードから構成していた場合は、その構成を手動でコードから構成ファイルに移す必要があります。Azure SDK 2.6 への移行の際にクラッシュ ダンプは移行されません。

