---
title: "Azure Toolkit for Eclipse の新機能"
description: "Azure Toolkit for Eclipse の最新の機能について説明します。"
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 16b066ea-aae7-4c30-9a12-fa0c3711b93e
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm;asirveda;martinsawicki
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 80f25488b6573579d980880cb4aaadf7fc59c5e7


---
# <a name="whats-new-in-the-azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse の新機能
## <a name="azure-toolkit-for-eclipse-releases"></a>Azure Toolkit for Eclipse のリリース
この記事では、Azure Toolkit for Eclipse の各種リリースと最新情報について記載しています。

> [!NOTE]
> IntelliJ IDE 用の Azure Toolkit もあります。 詳細については、「 [Azure Toolkit for IntelliJ]」をご覧ください。
> 
> 

### <a name="august-26-2016"></a>2016 年 8 月 26 日
Azure Toolkit for Eclipse - August 2016 リリースでは、次の点が強化されています。

* **カスタム JDK ディストリビューション**。 Azure Toolkit for Eclipse には、Azure WebApp コンテナーに対する任意の JDK バージョンの指定および展開をサポートするようになりました。
  * Azure によって提供されている JDK に加えて、Azul Systems から提供されている Azure で利用できる Zulu OpenJDK の多様なバージョンから選択できます。
  * ZIP ファイルとしてストレージ アカウントにアップロードする場合は、独自の JDK ディストリビューションも指定できます。
* **Azure 用エクスプローラー ビューの機能強化**:
  * Azure の新しい Resource Manager モデルを使用した仮想マシンの管理をサポートします。IDE を離れずにリソース マネージャー ベースの仮想マシンを一覧表示、作成、削除できます。
  * Azure Resource Manager を使用したストレージ アカウントの BLOB 管理をサポートします。これは、「クラシック」ストレージ アカウントを管理するための既存の機能を補完するものです。
* **Microsoft JDBC Driver 6.0 for SQL Server**。 この更新は、Microsoft SQL Server (v6.0) の最新の JDBC ドライバーを組み込みます。このドライバーは、Java プロジェクトに簡単に追加できるライブラリとして含まれて、以前のバージョンから置き換わるようになりました。

### <a name="june-29-2016"></a>2016 年 6 月 29 日
Azure Toolkit for Eclipse - June 2016 リリースでは、次の点が強化されています。

* **Java 8 の要件**。 Azure Toolkit for Eclipse には、現在、Java 8 が必要です。ただし、この要件はツールキットにのみ適用されます。 - アプリケーションは、Azure でサポートされている Java のすべてのバージョンを引き続き使用できます。
* **最新の Java JDK のサポート**。 Java JDK の最新バージョンは、現在、Azure Toolkit for Eclipse でサポートされています。
* **Azure SDK v2.9.1 のサポート**。 最新バージョンの Azure SDK が、Azure Toolkit for Eclipse を使用するための最低限の前提条件になりました。
* **統合サンプル**。 Azure Toolkit for Eclipse には、現在、開発者の作業開始に役立ついくつかのサンプル アプリケーションが特徴付けられています。
* **HDInsight ツールの統合**。 Azure の HDInsight ツールは、現在、Azure Toolkit for Eclipse にバンドルされています。 詳細については、 [Eclipse 用の HDInsight Tools プラグイン]に関するページを参照してください。
* **Java Web アプリのリモート デバッグ**。 Azure Toolkit for Eclipse には、現在、Azure App Service での Java Web アプリのリモート デバッグがサポートされています。
* **Eclipse Luna リリースのサポート。**  今後、Eclipse IDE バージョンは Luna 以上が必須となります。

### <a name="april-12-2016"></a>2016 年 4 月 12 日
Azure Toolkit for Eclipse - April 2016 リリースでは、次の点が強化されています。

* **Azure SDK v2.9.0 のサポート**。 最新バージョンの Azure SDK が、Azure Toolkit for Eclipse を使用するための最低限の前提条件になりました。
* **Azure Web アプリのサポートに関連する、その他の操作性、応答性、およびパフォーマンスの強化**。 Toolkit が Azure と通信する際のパフォーマンスがさまざまな点で最適化されたため、より応答性の高い UI となっています。
* **Eclipse 内から Azure の既存の Web アプリケーション コンテナーを削除する機能**。 Azure Toolkit for Eclipse を使用して、Eclipse を離れることなく既存の Azure Web コンテナーを削除できるようになりました。

### <a name="march-7-2016"></a>2016 年 3 月 7 日
Azure Toolkit for Eclipse - March 2016 リリースでは、次の点が強化されています。

* **軽量の Java アプリケーションの簡易デプロイのサポート**。 Azure Toolkit for Eclipse では、軽量の Java アプリケーションを Azure Web アプリ コンテナーに簡易デプロイできるようになりました。Java アプリケーションのデプロイが数分ではなく数秒で完了します。
* **Azure Explorer ビューによる Web アプリ管理のサポート**。 ツールキットの Azure Explorer ビューで Azure Web Apps を一覧表示し、起動し、停止できるようになりました。
* **Tomcat、Jetty、Zulu OpenJDK のディストリビューションの更新**。 Azure Toolkit for Eclipse は更新版の Tomcat、Jetty、Zulu OpenJDK に対応しており、Java を Azure Cloud Services にデプロイするために利用できます。

### <a name="january-4-2016"></a>2016 年 1 月 4 日
Azure Toolkit for Eclipse - January 2016 リリースでは、次の点が強化されています。

* **Zulu OpenJDK 更新をサポート**。 詳細については、 [Azul Systems の Zulu OpenJDK に関する Web ページ]を参照してください。
* **Tomcat と Jetty ディストリビューションを更新**。 Microsoft Azure で Azure Toolkit for Eclipse から利用できる Jetty ディストリビューションと Tomcat ディストリビューションが更新されました。
* **Azure の Eclipse ツールキットと IntelliJ ツールキットの機能の同等性**。 Azure Toolkit for Eclipse と [Azure Toolkit for IntelliJ] は、同じ機能セットをサポートするようになりました。

### <a name="september-1-2015"></a>2015 年 9 月 1 日
Azure Toolkit for Eclipse - September 2015 リリースでは、次の点が強化されています。

* **Zulu OpenJDK 更新をサポート**。 詳細については、 [Azul Systems の Zulu OpenJDK に関する Web ページ]を参照してください。
* **Tomcat と Jetty ディストリビューションを更新**。 Microsoft Azure で Azure Toolkit for Eclipse から利用できる Jetty ディストリビューションと Tomcat ディストリビューションが更新されました。 開発者は、Azure Toolkit for Eclipse からこれらのディストリビューションを利用し、開発プロジェクトやテスト プロジェクトをすばやく作成することができます。
* **Tomcat と Jetty の参照の自動的な更新のサポート**。 Azure で利用できる特定のバージョンの Tomcat と Jetty に加えて、開発者は、"Latest (auto-updated)" と呼ばれるディストリビューションを参照できるようになりました。次回ロール インスタンスをリサイクルするときに、Jetty または Tomcat が各メジャー バージョンの最新ディストリビューションに自動で更新されます (リサイクルは自動的に実行されますが、開発者が Azure ポータルから手動でリサイクルをトリガーすることもできます)。つまり、開発者はアプリケーションを再デプロイしなくてもサーバー ソフトウェアを更新することができます。 (
* ただし現在、この機能の用途は開発とテスト (つまりミッションクリティカルではないアプリケーション) に限定されており、運用環境での使用はお勧めできません。
* **Azure Explorer で Azure Storage 内の BLOB、キュー、テーブルを表示**。 ストレージ アーティファクトに対する一連の基本タスクを、開発者が Eclipse IDE から直接実行できるようになりました (例: BLOB の削除、アップロード、ダウンロード)。

### <a name="august-1-2015"></a>2015 年 8 月 1 日
Azure Toolkit for Eclipse - August 2015 リリースでは、次の点が強化されています。

* **Application Insights インストルメンテーション キー管理**。 Application Insights インストルメンテーション キーの取得、作成、管理を Eclipse IDE から直接行うことができます。
* **Microsoft JDBC Driver 4.1 for SQL Server**。 Microsoft SQL Server 用の最新の JDBC ドライバーがサポートされます。
* **Azure SDK の Version 2.7**。 最新の Azure SDK です。 Azure Toolkit for Eclipse を Windows にインストールするための新たな前提条件となっています(Windows 以外のオペレーティング システムでは不要)。
* **Zulu OpenJDK v7 更新をサポート**。 詳細については、 [Azul Systems の Zulu OpenJDK に関する Web ページ]を参照してください。

### <a name="may-1-2015"></a>2015 年 5 月 1 日
Azure Toolkit for Eclipse - May 2015 リリースでは、次の点が強化されています。

* **サーバー選択の UI を改良**。 Windows 以外のオペレーティング システムにおけるツールキットの使用がこのリリースで簡素化されました。
* **Maven プロジェクトのサポート**。 このリリースでは、アプリケーションとして Maven プロジェクトがサポートされています。Maven プロジェクトを Azure にデプロイし、Application Insights を構成することができます。
* **Azure SDK の Version 2.6**。 最新の Azure SDK です。 Azure Toolkit for Eclipse を Windows にインストールするための新たな前提条件となっています(Windows 以外のオペレーティング システムでは不要)。
* **デプロイのアップグレードに対応 (再発行が不要)**。 デプロイ プロジェクトを再発行するとき、以前のバージョンが既に運用されていた場合、従来は、以前のデプロイをシャットダウンしてゼロから再発行していましたが、今後は Azure のデプロイ アップグレード機能が使用されます。 更新時であってもクラウド サービスの中断を可能な限りなくして高い可用性を確保すると共に、再発行プロセスにかかる時間を短縮できます。
* **最新の Zulu OpenJDK v8 (Update 40) をサポート**。 詳細については、 [Azul Systems の Zulu OpenJDK に関する Web ページ]を参照してください。

### <a name="march-9-2015"></a>2015 年 3 月 9 日
Azure Toolkit for Eclipse - March 2015 リリースでは、次の点が強化されています。

* **Mac、Ubuntu、各種 Linux ディストリビューションの追加サポート**。 このリリースで、Azure Toolkit for Eclipse は新しく Mac OS のほか、いくつかの Unix プラットフォームに対応しました。Windows 以外のオペレーティング システムに Azure Toolkit for Eclipse をインストールし、Java プロジェクトを作成、構成して、Azure Cloud Services (PaaS) に発行することができます。

> [!NOTE]
> この機能はプレビュー版であり、運用環境での使用は推奨されません。 カスタマー サポートのサービス レベル アグリーメント (SLA) ありませんが、フィードバックは歓迎いたします。
> 
> 

* **新しい Application Insights プラグイン**。 Azure で Application Insights を使用し、自動サーバー テレメトリを構成できるようになりました。
* **Ant ベースのコマンド ライン デプロイ オートメーション**。 Eclipse の外部で Ant を使用し、新しいバージョンのデプロイに伴う発行処理を自動化できます。 Eclipse からのプロジェクトの初回デプロイ後、そのプロジェクト用のスクリプトがあらかじめ生成され、自動的に構成されます。その後はスクリプトを使い、コマンド ラインのみで完全にデプロイを自動化できます。
* **Tomcat と Jetty が Azure で利用できるようになりデプロイの手間と時間が削減**。 Java サーバーを各自のアカウントに (またはツールキットを介して) アップロードせずに、Azure に用意されている各種バージョンの Tomcat と Jetty を直接参照できるようになりました。Java サーバーをアップロードする必要がない分、立ち上げにかかる時間を短縮することができます。
* **Java Web アプリを簡単な方法で Azure Cloud Services に発行**。 開発とテストを手軽に行えるよう、従来よりも簡単に Java アプリケーションを Azure に発行できるようにしました。 従来のように Azure デプロイ プロジェクトを作成、構成する必要はありません。Tomcat v8 と Zulu JVM (OpenJDK) の既定のインスタンスを使ってアプリケーションがデプロイされます。

### <a name="january-30-2015"></a>2015 年 1 月 30 日
Azure Toolkit for Eclipse - January 2015 リリースでは、次の点が強化されています。

* **IBM® WebSphere® Application Server Liberty Core のサポート**。 ツールキットから Azure にデプロイできるサポート対象アプリケーション サーバーに IBM WebSphere Application Server Liberty Core が追加されました。 Azure Toolkit for Eclipse は既に、各種バージョンの Tomcat、Jetty、JBoss、GlassFish を &quot;標準&quot;でサポートしていますが、この最新のリリースで、サポート対象のアプリケーション サーバーがまた 1 つ増えたことになります。
* **Application Insights SDK の追加**。 新しくリリースされたクライアント API ライブラリ (v0.9.0) が Package for Azure Libraries for Java に追加されました。
* **Package for Azure Libraries for Java を更新**。 Azure Libraries for Java v0.7.0 と Storage Client API v2.0.0 のほか、新しくリリースされた Application Insights SDK v0.9.0 が追加されました。

### <a name="november-12-2014"></a>2014 年 11 月 12 日
Azure Toolkit for Eclipse - November 2014 リリースでは、次の点が強化されています。

* **Azure SDK 2.5 のサポート**。 最新の Azure SDK です。Azure Toolkit for Eclipse の新たな前提条件となっています。
* **新しいバージョンの Zulu OpenJDK v1.8、v1.7、v1.6 パッケージをサポート**。 詳細については、 [Azul Systems の Zulu OpenJDK に関する Web ページ]を参照してください。
* **Standard D サイズのクラウド サービスを新たにサポート**。パフォーマンスが向上し、メモリ リソースが増強されています。 詳細については、「 [Azure の仮想マシンおよびクラウド サービスのサイズ]」をご覧ください。

### <a name="october-17-2014"></a>2014 年 10 月 17 日
Azure Toolkit for Eclipse - October 2014 リリースでは、次の点が強化されています。

* **クラウドに発行するときのパフォーマンスを強化**。 サブスクリプションやストレージ アカウントをユーザーが複数所有しているときのサブスクリプション情報の読み込みが従来よりもはるかに高速になりました。
* **新しいバージョンの Zulu OpenJDK v1.8 パッケージをサポート**。 詳細については、 [Azul Systems の Zulu OpenJDK に関する Web ページ]を参照してください。
* **旧バージョンのサード パーティ JDK を非推奨化**。 推奨されない JDK パッケージは、新しいデプロイ プロジェクトではドロップダウン メニューに表示されません。 推奨されない JDK パッケージを既存のプロジェクトで参照している場合、当面そのパッケージを使用できますが、そのようなプロジェクトについては、最新のパッケージを使用するようにアップグレードすることをお勧めします。
* **Package for Azure Libraries for Java クライアント API ライブラリのバージョンを更新**。 詳細については、 [Microsoft Azure クライアント API]に関するページを参照してください。
* **バグの修正。**  ユーザーからの報告やテストによって判明した各種バグ修正が数多く行われています。

### <a name="august-5-2014"></a>2014 年 8 月 5 日
Azure Toolkit for Eclipse - August 2014 リリースでは、次の点が強化されています。

* **Azure SDK 2.4 のサポート。**  以前のバージョンの Eclipse Toolkit で、この新しくリリースされた SDK を使用することはできません。
* **新しいバージョンの Zulu OpenJDK v1.6、v1.7、v1.8 パッケージ。** 詳細については、 [Azul Systems の Zulu OpenJDK に関する Web ページ]を参照してください。
* **Package for Azure Libraries for Java クライアント API ライブラリのバージョンを更新。** 詳細については、 [Microsoft Azure クライアント API]に関するページを参照してください。
* **最新の発行設定ファイルの形式をサポート。**  新たにバージョン 2.0 形式の発行設定ファイルがサポートされます。
* **クラウドへの発行機能に伴いアーキテクチャを変更。**  クラウドへの発行機能をサポートするため、新しくリリースされた Microsoft Azure Client API for Java が使用されます。
* **バグの修正。**  このリリースでは、ユーザーから要請された多数のバグ修正が行われています。

### <a name="june-12-2014"></a>2014 年 6 月 12 日
Azure Toolkit for Eclipse - June 2014 リリースは、小規模なメンテナンス アップデートが行われています。次の点が強化されました。

* **Zulu OpenJDK パッケージ v1.8 をサポート。** 詳細については、 [Azul Systems の Zulu OpenJDK に関する Web ページ]を参照してください。
* **新しいバージョンの Zulu OpenJDK v1.6 および v1.7 パッケージ。** 詳細については、 [Azul Systems の Zulu OpenJDK に関する Web ページ]を参照してください。
* **Package for Azure Libraries for Java クライアント API ライブラリのバージョンを更新。** 詳細については、 [Microsoft Azure クライアント API]に関するページを参照してください。
* **バグの修正。**  このリリースでは、ユーザーから要請された多数のバグ修正が行われています。

### <a name="april-4-2014"></a>2014 年 4 月 4 日
Azure Plugin for Eclipse - April 2014 がリリースされました。 このアップデートに伴い Azure SDK 2.3 がリリースされています。Azure SDK 2.3 は、このプラグインの前提条件であり、プラグインをインストールするときに自動的にダウンロードされます。 このアップデートでは、February 2014 Preview 以降の新機能の追加とバグ修正が行われているほか、フィードバックに基づいてユーザビリティが強化されています。

* **Azure SDK 2.3 リリースをサポート。**  Azure Plugin for Eclipse - April 2014 リリースには Azure SDK 2.3 が必要となります。 新しいプラグインを使用するときに、まだ Azure SDK 2.3 がインストールされていない場合、プロンプトに従ってインストールすることができます。 それより前のバージョンのプラグインで Azure SDK 2.3 を使用することは避けてください。
* **パッケージ全体をデプロイしなくてもアプリケーションのアップグレードが可能。** プロジェクトに属している Java アプリケーションをデプロイする際、それらのアプリケーションがこのプラグインによって自動的に、選択されたストレージ アカウントにアップロードされます。パッケージ全体を再構築して再デプロイしなくても、プロジェクトを更新し、ロール インスタンスをリサイクルして、最新のアプリケーション ビットをデプロイすることができます。
* **認識対象のアプリケーション サーバーとして新たに Tomcat 8 を追加。** コンピューター上の Tomcat 8 のインストール ディレクトリを **[Azure Deployment Project]** ダイアログの **[Server]** タブで選択した場合、既に登録されていた以前のバージョンの Tomcat と同様、プラグインがそれを自動的に検出し、オートメーション化された方法で Tomcat 8 をデプロイできるようになりました。
* **Azul Zulu OpenJDK パッケージの更新: v1.7 update 51 および v1.6 update 47。**  このリリースから、Azul Systems の Zulu Open JDK v7 パッケージ update 51 が利用できるようになりました。 また、Zulu Open JDK v6 パッケージも update 47 以降で利用可能となります。 既に利用可能となっていた Zulu Open JDK v7 パッケージ update 45、update 40、update 25 に、これらのアップデートが加わることになります。
* **A8 と A9 の Microsoft Azure 仮想マシン サイズをサポート。**  メモリ容量を増強した A8 と A9 サイズの仮想マシンにクラウド サービスをデプロイできるようになりました。 VM サイズの詳細については、「 [Azure の仮想マシンおよびクラウド サービスのサイズ]」を参照してください。
* **HTTP から HTTPS への自動リダイレクトによって SSL が有効なロールに対応。**  ご利用のクラウド サービスに存在するのが HTTPS ロールのみであるとき、ユーザーの要求で HTTP が指定された場合に、HTTPS へと自動的にリダイレクトされます。 HTTP 要求を処理するロールを別途作成する必要はありません。
* **Express Emulator を使用したローカル エミュレーション。**  アプリケーションをローカルでデバッグする際のエミュレーターとして Azure Express Emulator が使用されます。
* **Azure のブランド名を Microsoft Azure に変更。** Azure のブランド名変更に伴い、UI 画面が更新されています。Azure の旧名称は廃止されました。

### <a name="february-6-2014"></a>2014 年 2 月 6 日
Azure Plugin for Eclipse - February 2014 Preview がリリースされました。 このアップデートでは、October 2013 Preview 以降の新機能の追加とバグ修正が行われているほか、フィードバックに基づいてユーザビリティが強化されています。

* **SSL オフロードをサポート。** SSL (Secure Sockets Layer) オフロードの機能が追加され、Azure への Java のデプロイで HTTPS (ハイパーテキスト転送プロトコル セキュア) を利用しやすくなりました。今後は、Java アプリケーション サーバーで SSL を構成する必要はありません。 このことは特に、セッション アフィニティや認証通信のシナリオで重要となります。 たとえば、ツールキットで既にサポートされている Access Control Service (ACS) フィルターを使用している状況が該当します。 詳細については、[SSL オフロード]と [SSL オフロードの使用方法]に関するページを参照してください。
* **認識対象のアプリケーション サーバーとして新たに GlassFish 4 を追加。** コンピューター上の GlassFish 4 のインストール ディレクトリを **[Azure Deployment Project]** ダイアログの **[Server]** タブで選択した場合、既に登録されていた GlassFish OSE 3 バージョンと同様、プラグインがそれを自動的に検出し、オートメーション化された方法で GlassFish OSE 4 をデプロイできるようになりました。
* **Azul Zulu OpenJDK パッケージ update 45。** このリリースから Azul Systems の Zulu (Open JDK v7 パッケージ) update 45 が利用できるようになりました。既に利用可能となっていた update 40 と update 25 に、これらのアップデートが加わることになります。
* **プライベート エンドポイントのポートに "自動" をサポート。** 入力エンドポイントと内部エンドポイントのプライベート ポートを "自動" に設定することができます。この場合そのエンドポイントには、Azure によって自動的にポートが割り当てられます。 従来は、決まったポート番号しか割り当てることができませんでした。
* **自己署名証明書の作成 UI で証明書名 (CN) のカスタマイズが可能に。** 従来、新しい証明書にはすべて、ハードコーディングされた同じ名前が使用されていましたが、今後は証明書名を独自に指定できます。目的の異なる複数の証明書が Azure ポータルで見分けやすくなりました。
* **Azure ツール バー:** Azure ツール バーに次の変更が加えられました。 
  * ![][ic710876]**[New Azure Deployment Project]**用にこのアイコンが追加されました。
  * ![][ic710877] 自己署名証明書の作成ダイアログのショートカットとしてこのアイコンが追加されました。
* **Azure 仮想マシン サイズとして A5 をサポート。**  メモリ容量を増強した A5 サイズの仮想マシンにクラウド サービスをデプロイできるようになりました。 この VM サイズの詳細については、「 [Azure の仮想マシンおよびクラウド サービスのサイズ]」を参照してください。
* **Microsoft Windows Server 2012 R2 をサポート。**  クラウド オペレーティング システムとして Windows Server 2012 R2 を選択できるようになりました。

### <a name="october-22-2013"></a>2013 年 10 月 22 日
Azure Plugin for Eclipse - October 2013 Preview がリリースされました。 このアップデートでは、September 2013 Preview 以降の新機能の追加とバグ修正が行われているほか、フィードバックに基づいてユーザビリティが強化されています。

* **Azure SDK 2.2 リリースをサポート。** Azure Plugin for Eclipse - October 2013 Preview では Azure SDK 2.2 がサポートされます。 Azure SDK 2.1 でも引き続きプラグインは動作します。Azure SDK 2.1 以上がインストールされていない場合は、Azure SDK 2.2 が自動的にインストールされます。
* **Azul Zulu OpenJDK パッケージ update 40。** September 2013 Preview でアナウンスされたように、このプラグインを使うと、サード パーティ提供の JDK を直接 Azure で使用できるようになり、独自に JDK をアップロードする必要がありません。 October 2013 リリースでは、Azul Systems の Zulu (Open JDK v7 パッケージ) update 40 が利用できるようになりました。もともと発行されていた update 25 に、このアップデートが加わることになります。
* **クラウド デプロイへのリンクをアクティビティ ログで使用可能。** デプロイのステータスが **[発行済み]** であるとき、Azure のアクティビティ ログ内で **[発行済み]** をクリックすることができます。対象のデプロイへのリンクになっており、ブラウザーにそのデプロイが表示されます  (**[発行済み]** ステータスには以前、**[実行中]** という名称が使われていました)。
* **ターゲット OS を発行時に選択可能。** **[Publish to Azure (Azure に発行する)]** ダイアログに **[Target OS (ターゲット OS)]** というフィールドが新たに追加され、従来よりもわかりやすい方法でターゲット オペレーティング システムを設定することができます。
* **以前のデプロイを自動上書き。** **[Publish to Azure (Azure に発行する)]** ダイアログに **[Overwrite previous deployment (以前のデプロイの上書き)]** というチェック ボックスが追加されています。 このオプションをオンにした場合、新しいデプロイを発行すると、以前のデプロイが自動的に上書きされます。以前のデプロイの発行を取り消さずに同じ場所に発行しようとすると発生する &quot;409 競合&quot; の問題を回避できます。
* **認識対象のアプリケーション サーバーとして新たに Jetty 9 を追加。** コンピューター上の Jetty 9 のインストール ディレクトリを **[Azure Deployment Project]** ダイアログの **[Server (サーバー)]** タブで選択した場合、既に登録されていた以前のバージョンの Jetty と同様、プラグインがそれを自動的に検出し、オートメーション化された方法で Jetty 9 をデプロイできるようになりました。
* **[Project (プロジェクト)] コンテキスト メニューからロールを追加。** **Azure** プロジェクトのコンテキスト メニューに **[Add Role (役割の追加)]** という新しいメニュー項目が追加されました。従来よりも見やすくなり、新しいロールをすばやく Azure プロジェクトに追加できます。
* **Package for the Azure Libraries for Java ライブラリを更新。**  [Microsoft Azure クライアント API]」をご覧ください。

### <a name="september-25-2013"></a>2013 年 9 月 25 日
Azure Plugin for Eclipse - September 2013 Preview がリリースされました。 このアップデートでは、August 2013 Preview 以降の新機能の追加とバグ修正が行われているほか、フィードバックに基づいてユーザビリティが強化されています。

* **Azure で利用できる Azul Zulu OpenJDK パッケージをデプロイする機能。**  Azure のデプロイで使用する JDK を指定するときのオプションが新たに追加されました。 このオプションを使用すると、サード パーティの JDK パッケージを直接 Azure クラウドにデプロイでき、独自にアップロードする必要はありません。 その初となるパッケージは Azul Systems から提供されている Zulu という OpenJDK ベースのパッケージで、このオプションを使ってデプロイすることができます。
* **Package for the Azure Libraries for Java ライブラリを更新。**  [Microsoft Azure クライアント API]」をご覧ください。

### <a name="august-1-2013"></a>2013 年 8 月 1 日
Azure Plugin for Eclipse - August 2013 Preview がリリースされました。 このアップデートに伴い Azure SDK 2.1 がリリースされています。Azure SDK 2.1 は、このプラグインの前提条件であり、プラグインをインストールするときに自動的にダウンロードされます。 このアップデートでは、July 2013 Preview 以降の新機能の追加とバグ修正が行われているほか、フィードバックに基づいてユーザビリティが強化されています。

* **デプロイ パッケージの一部としてローカル JDK とローカル アプリケーション サーバーを追加するオプションを廃止。**  JDK とアプリケーション サーバーは、パッケージのコンポーネントとして埋め込むよりもデプロイ時にクラウド ストレージからダウンロードした方が、デプロイ パッケージのサイズを抑え、デプロイの所要時間を短縮でき、メンテナンスもしやすくなります。 そのため、JDK とアプリケーション サーバーをデプロイ パッケージに追加するオプションは廃止しました。 ローカル JDK とローカル アプリケーション サーバーをデプロイ パッケージに埋め込むように構成された既存のプロジェクトは自動的に変換され、JDK とアプリケーション サーバーがクラウド ストレージに自動的にアップロードされます。
* **Azure SDK 2.1 リリースをサポート。** Azure Plugin for Eclipse - August 2013 Preview には Azure SDK 2.1 が必要となります。 以前のバージョンの Azure SDK で August 2013 Preview を使用することは避けてください。同様に、以前のバージョンの Azure Plugin for Eclipse で Azure SDK 2.1 を使用することも避けてください。
* **Eclipse Kepler リリースをサポート。**  それに関連して今後、Eclipse IDE バージョンは Indigo 以上が必須となります。 今後、Helios での Azure Plugin for Eclipse の公式テストは実施されません。

### <a name="july-3-2013"></a>2013 年 7 月 3 日
Azure Plugin for Eclipse - July 2013 Preview がリリースされました。 このアップデートでは、May 2013 Preview 以降の新機能の追加とバグ修正が行われているほか、フィードバックに基づいてユーザビリティが強化されています。

* **新しいストレージ アカウントを作成する機能。** **[ストレージ アカウントの追加]** ダイアログに **[新規]** ボタンが追加されました。 これにより Azure 管理ポータルにログインせずに、Eclipse プラグイン内でストレージ アカウントを作成することができます (この機能を使用するためには、あらかじめ Azure サブスクリプションが必要です)。新しいストレージ アカウントの作成の詳細については、「[新しいストレージ アカウントを作成するには]」を参照してください。
* JDK とサーバーの自動デプロイやキャッシュに使用するストレージ アカウントに関して、**新たに &quot;(auto)&quot; オプションを追加。** JDK とアプリケーション サーバーに関して **[自動的にアップロード]** オプションを選択する際、JDK やアプリケーション サーバーのアップロード時または Azure Caching の使用時に用いる URL とストレージ アカウントに **[(auto)]** を指定できるようになりました。 この場合、 **[Publish to Azure]** ダイアログで選択したストレージ アカウントが自動的に使用されます。 「 [Azure 向け Hello World アプリケーションを Eclipse で作成する] 」のチュートリアルは、新しい **[(auto)]** オプションを使用するように更新されました。
* **Azure サービス エンドポイントを設定する機能。**  グローバル Azure プラットフォーム、中国の 21Vianet で運営される Azure、プライベート Azure プラットフォームのうち、どこにアプリケーションをデプロイし、どこで管理するかを決定するサービス エンドポイントを指定します。 詳細については、 [Azure のサービス エンドポイント]に関するページを参照してください。
* **大規模なデプロイでローカル ストレージ リソースを指定可能に。**  デプロイの規模が大きすぎて既定の approot フォルダーに格納できない場合、JDK とアプリケーション サーバーのデプロイ先としてローカル ストレージ リソースを指定できるようになりました。 詳細については、 [大規模な環境のデプロイ]に関するページを参照してください。
* **A6 と A7 の Azure 仮想マシン サイズをサポート。**  メモリ容量を増強した A6 と A7 サイズの仮想マシンにクラウド サービスをデプロイできるようになりました。 VM サイズの詳細については、「 [Azure の仮想マシンおよびクラウド サービスのサイズ]」を参照してください。
* **Package for the Azure Libraries for Java ライブラリを更新。**  [Microsoft Azure クライアント API]」をご覧ください。

### <a name="may-1-2013"></a>2013 年 5 月 1 日
Azure Plugin for Eclipse - May 2013 Preview がリリースされました。 このメジャー アップデートに伴い Azure SDK 2.0 がリリースされています。Azure SDK 2.0 は、このプラグインの前提条件であり、プラグインをインストールするときに自動的にダウンロードされます。 このリリースでは、February 2013 Preview 以降の新機能の追加とバグ修正が行われているほか、フィードバックに基づいてユーザビリティが強化されています。

* **Azure Storage への JDK とアプリケーション サーバーの自動アップロード、および Azure Storage からのデプロイ。** 選択した JDK とアプリケーション サーバーを必要に応じて特定の Azure ストレージ アカウントに自動的にアップロードし、それらのコンポーネントをそこからデプロイする新しいオプションが追加されました。今後は、コンポーネントをデプロイ パッケージに埋め込んだり、ユーザーに手動でアップロードさせたりする必要はありません。 これは多くのユーザーから望まれていた機能であり、特に初級者にとって今後は、JDK とサーバー コンポーネントをデプロイしやすくなります。 これらのオプションを使用するチュートリアルについては、「[Azure 向け Hello World アプリケーションを Eclipse で作成する]」を参照してください。
* **ストレージ アカウントの追跡が一元化され、ストレージ アカウントを (ドロップダウン コントロールで) 容易に参照可能。**  JDK やサーバー コンポーネントのデプロイ、キャッシュなど、ストレージに依存した各種機能が対象となります。 詳細については、 [Azure ストレージ アカウントの一覧]に関するページを参照してください。
* **クラウドへの発行ウィザードにおけるリモート アクセスの設定を単純化。** 必要な設定は、リモート アクセスを有効にするユーザーの名前とパスワードを入力するだけです。引き続きリモート アクセスを無効にする場合は、空欄にしておいてください。
* **Package for the Azure Libraries for Java ライブラリを更新。**  [Microsoft Azure クライアント API]」をご覧ください。
* **Windows Server 2012 でスティッキー セッションをサポート。**  これまでスティッキー セッションは Windows Server 2008 R2 でしか動作しませんでしたが、今後は、両方のクラウド オペレーティング システム ターゲットで、セッション アフィニティがサポートされます。
* **パッケージのアップロード パフォーマンスが向上。**  JDK とアプリケーション サーバーをデプロイ パッケージに埋め込んだときでも、デプロイ プロセスのアップロード部分の処理速度が、以前のバージョンと比べ約 2 倍にアップしています。

### <a name="february-8-2013"></a>2013 年 2 月 8 日
Azure Plugin for Eclipse - February 2013 Preview がリリースされました。 このマイナー アップデートでは、November 2012 Preview 以降の新機能の追加とバグ修正が行われているほか、フィードバックに基づいてユーザビリティが強化されています。

* JDK やアプリケーション サーバーなど、クラウドへのデプロイ時にそのパッケージに追加されてきたコンポーネントは今後、パブリックまたはプライベートの Azure Blob Storage にダウンロードし、そこからデプロイすることができます。
* **[Azure ロールのプロパティ]** の **[コンポーネント]** セクションに**上へ移動**ボタンと**下へ移動**ボタンが追加され、ロールのユーザー定義コンポーネントの処理順序を変更できるようになりました。
* **Package for the Azure Libraries for Java** ライブラリを [Microsoft Azure クライアント API]のバージョン 0.4.0 に基づいて更新しました。

### <a name="november-5-2012"></a>2012 年 11 月 5 日
Azure Plugin for Eclipse - November 2012 Preview がリリースされました。 このメジャー アップデートでは、September 2012 Preview 以降の多数の新機能の追加とバグ修正が行われているほか、フィードバックに基づいてユーザビリティが強化されています。

* クラウド オペレーティング システムとして Microsoft Windows Server 2012 をサポート。
* memcached クライアント向けに Azure の併置型キャッシュ機能をサポート。
* Azure AMQP ベースのメッセージングを活用するための Apache Qpid JMS クライアント ライブラリを追加。
* **プロジェクトの新規作成ウィザードを改良。**ウィザードの最後に新しいページが追加され、スティッキー セッション、キャッシュ、リモート デバッグなど、日常的に使われる重要な機能をプロジェクトに対していち早く有効にすることができます。
* コンピューティング エミュレーターでの実行時、サーバー インスタンス間におけるポート バインドの競合を避けるために、ロール インスタンスを自動的に 1 に減らします。

### <a name="september-28-2012"></a>2012 年 9 月 28 日
Azure Plugin for Eclipse - September 2012 Preview がリリースされました。 このサービス更新では、August 2012 Preview 以降の多数のバグ修正が行われているほか、フィードバックに基づいて既存機能のユーザビリティが強化されています。

* Microsoft Windows 8 と Microsoft Windows Server 2012 を開発オペレーティング システムとしてサポート。従来これらのオペレーティング システムで適切に動作しなかったプラグインの問題が解決されます。
* エンドポイントのポート範囲を指定するための機能を強化。
* 空白を含んだファイル パスに関連するバグを修正。
* ロール コンテキスト メニューの改善により、ロール固有の構成設定にすばやくアクセス可能。
* **クラウドへの発行** ウィザードの微調整とさまざまなバグ修正。

### <a name="august-28-2012"></a>2012 年 8 月 28 日
Azure Plugin for Eclipse - August 2012 Preview がリリースされました。 このサービス更新では、July 2012 Preview 以降のバグ修正が行われているほか、フィードバックに基づいて既存機能のユーザビリティが強化されています。

* [Azure Access Control Services Filter] ダイアログ内:
  * **署名証明書を組み込むオプション** 。
  * **自己署名証明書を作成するオプション** 。 Azure Access Control Services Filter の詳細については、「 [Eclipse を使用して Azure の Access Control Service で Web ユーザーを認証する方法]」を参照してください。
* Azure デプロイ プロジェクト ウィザード内 (ロールのサーバー構成プロパティ ページも同様):
  * **JDK の場所を自動検出** (必要に応じて上書きできます)。
  * **サーバーの種類を自動検出** 。

### <a name="july-15-2012"></a>2012 年 7 月 15 日
Azure Plugin for Eclipse - July 2012 Preview では、June 2012 リリース後に見つかった、またはユーザーから報告されたきわめて優先度の高いバグが数多く修正されています。 これはサービス更新のみです。新しい機能は含まれていません。

### <a name="june-7-2012"></a>2012 年 6 月 7 日
Azure Plugin for Eclipse - June 2012 CTP がリリースされました。 新機能は次のとおりです。

* **Azure デプロイ プロジェクトの新規作成ウィザード:** ウィザードの UI が改善され、JDK、Java アプリケーション サーバー、Java アプリケーションを直接選択できるようになりました。 Tomcat 6、Tomcat 7、GlassFish OSE 3、Jetty 7、Jetty 8、JBoss 6、JBoss 7 (スタンドアロン) から難しい設定なしでサーバー構成を選択できます。 加えて一連のサーバー構成をカスタマイズすることもできます。 従来、圧縮ファイルをドラッグ アンド ドロップし、起動スクリプトを上書きコピーする方法が主に用いられていましたが、今回の UI の改良によって、その作業が不要となりました。 従来の方法は引き続き利用できます。ただし一般に、従来の方法が必要となるのは、通常よりも高度なシナリオに限られます。
* **サーバー構成ロールのプロパティ ページ:** デプロイに関連付けられている JDK、Java アプリケーション サーバー、アプリケーションは、プロジェクトの作成後に簡単に切り替えることができます。 詳細については、 [サーバー構成のプロパティ]に関するページを参照してください。
* **&quot;クラウドへの発行&quot; ウィザード:** 画面の指示に従って簡単に、Eclipse から直接 Azure にプロジェクトをデプロイできます。たとえば従来のように、資格情報を取得して、Azure 管理ポータルにサインインし、パッケージをアップロードするという手間が自動化されます。プロジェクトを Azure に直接デプロイする例については、「[Azure 向け Hello World アプリケーションを Eclipse で作成する]」を参照してください。
* **Azure ツール バー:** Azure のツール バーを Eclipse で使用できるようになりました。次の機能を起動するためのボタンがツール バーに表示されます。
  * ![][ic710879] **Azure エミュレーターで実行**: エミュレーターでプロジェクトを実行します。
  * ![][ic710880] **Azure エミュレーターのリセット**: エミュレーターをリセットします。
  * ![][ic710881] **Azure のクラウド パッケージのビルド**: デプロイ用にパッケージをコンパイルします。
  * ![][ic710876] **新しい Azure デプロイ プロジェクト**: 新しい Azure デプロイ プロジェクトを作成します。
  * ![][ic710882] **Azure クラウドへの発行**: プロジェクトを Azure に発行します。
  * ![][ic710883] **発行の取り消し**: デプロイを削除します。
  * ここに示した Azure ツール バー ボタンの多くは、「 [Azure 向け Hello World アプリケーションを Eclipse で作成する]」で使用されています。
* **Azure Libraries for Java:** 単一の Package for Azure Libraries for Java ライブラリの構成要素として利用できるようになりました。プラグインと一緒にインストールされ、必要な依存コンポーネントもすべて含まれています。 それらを個別にダウンロードする必要はなく、このライブラリへの参照を Java プロジェクトで 1 回追加するだけで済みます。 詳細については、「 [Azure Toolkit for Eclipse のインストール]」を参照してください。
* **Microsoft JDBC Driver 4.0 for SQL Server がプラグイン インストール中に利用可能に:** 新しいプラグインのインストール中に、Microsoft JDBC Driver for SQL Server の最新バージョンをインストールできます。
* **プラグインのインストール時に Azure Access Control Service Filter が利用可能に:** Eclipse ライブラリとしてツールキットに付属している新しいコンポーネントです。Java Web アプリケーションは、このコンポーネントを通じて Google、Live.com、Yahoo! など、さまざまな ID プロバイダーを利用し、Azure Access Control Service (ACS) 認証をシームレスに活用することができます。 今後、認証ロジックを独自に記述する必要はありません。いくつかのオプションを構成するだけで、ACS を使ったサインインを可能にする面倒な作業は、Azure Access Control Service Filter によって自動的に行われます。 要求オブジェクト内のフィルターによってアプリケーションに返される ID に基づき、リソースへのアクセス権限をユーザーに与えるコードの記述にだけ専念できます。 ACS フィルターの使用方法については、「[Eclipse を使用して Azure の Access Control Service で Web ユーザーを認証する方法]」を参照してください。
* **Azure SDK 1.7 前提条件の自動検出:** Azure デプロイ プロジェクトを新規作成するとき、Azure SDK 1.7 がインストールされていなければ自動的にダウンロードされます。
* **インスタンス エンドポイント:** 負荷分散されたロール インスタンスとの通信でポート エンドポイント アクセスを許可します。 インスタンス エンドポイントは、 [エンドポイントのプロパティ] に関するセクションにある、エンドポイントの UI を使用して追加できます。 これによりマルチインスタンス デプロイのシナリオにおいて、クラウドで実行されている特定のコンピューティング インスタンスに対してリモート デバッグと JMX 診断を行うことができます。 
* **コンポーネントの UI:** プロジェクト内の個々の Azure ロール間や他の外部リソース (Java アプリケーション プロジェクトなど) 間で生じるプロジェクトの依存関係を上級ユーザーが設定しやすくなりました。また、デプロイのロジックも概念化しやすくなっています。 詳細については、「[コンポーネントのプロパティ]」を参照してください。
* **以前のバージョンのプロジェクトを自動的にアップグレード:** ワークスペースを開いたとき、以前のバージョンのプラグインで作成した Azure プロジェクトが含まれていると、その古いプロジェクトは、閉じた状態で Eclipse に表示されます。これは、以前のバージョンのプロジェクトと新しいリリースとの間に互換性がないためです。 これらの古いプロジェクトの 1 つを開こうとすると、アップグレード ウィザードが起動します。 アップグレードに同意すると、名前に **_Upgraded** が追加されて新しいプロジェクトが作成され、新しいリリースで作業できるよう自動的に更新されます。 新しいプロジェクトの名前は必要に応じて変更できます。 アップグレードの過程で元のプロジェクトに変更が加えられることはありません (閉じた状態で維持されます)。

### <a name="december-10-2011"></a>2011 年 12 月 10 日
Azure Plugin for Eclipse - December 2011 CTP がリリースされました。 新機能は次のとおりです。

* **セッション アフィニティ (&quot;スティッキー セッション&quot;) のサポート:** チェック ボックスを 1 つオンにするだけで、クラスター化された、ステートフルな Java アプリケーションを実現できます。 詳細については、 [セッション アフィニティ]に関するページを参照してください。
* **起動スクリプトの既製サンプル:** ごく一般的な Java サーバー (Tomcat、Jetty、JBoss、GlassFish) については、プロジェクトのサンプル ディレクトリからサンプルをコピーし、起動スクリプトに貼り付けて手軽に利用できます。
* **エミュレーター起動をリアルタイムに出力:** 専用のコンソール ウィンドウで起動スクリプトの全ステップの実行を監視できるようになりました。Azure により実行されるスクリプトの進捗状況とエラーが表示されます。
* **軽量なスクリプトで java.exe を自動的に監視:** デプロイに自動的に追加される軽量な既製スクリプトを利用し、java.exe が実行を停止したとき、ロール リサイクルを強制的に実行します。
* **Java アプリのリモート デバッグ構成 UI:** Eclipse のリモート デバッガーをすばやく有効にして、エミュレーターまたは Azure クラウドで実行されている Java アプリにアクセスできます。リアルタイムで Java コードをステップ実行し、デバッグすることができます。 詳細については、「[Eclipse での Azure アプリケーションのデバッグ]」を参照してください。
* **ローカル ストレージ リソース構成 UI:** 今後は、XML を直接操作してローカル リソースを構成する必要はありません。 また、デプロイ後におけるローカル リソースの有効なファイル パスには、起動スクリプトから直接参照できる環境変数を介してアクセスできます。 詳細については、 [ローカル ストレージのプロパティ]に関するページを参照してください。
* **環境変数構成 UI:** 今後は、構成 XML を直接編集して環境変数を設定する必要はありません。 詳細については、 [環境変数のプロパティ]に関するページを参照してください。
* **SQL Azure の JDBC ドライバー:** シームレスに統合された Eclipse ライブラリとしてプラグインを介してインストールされます。SQL Azure に対するプログラミングが省力化されます。 
* **ロール構成 UI にコンテキスト メニューから簡単アクセス:** ロール フォルダーを右クリックし、**[プロパティ]** をクリックします。
* **カスタム Azure プロジェクトとロール フォルダーのアイコン:** ワークスペース内とプロジェクト内が見やすくなり、移動しやすくなりました。

## <a name="see-also"></a>関連項目
Azure Toolkits for Java IDE の詳細については、次のリンクをご覧ください。

* [Azure Toolkit for Eclipse]
  * [Azure Toolkit for Eclipse のインストール]
  * [Eclipse で Azure 用の Hello World Web アプリを作成する]
  * *Azure Toolkit for Eclipse の新機能 (この記事)*
* [Azure Toolkit for IntelliJ]
  * [Azure Toolkit for IntelliJ のインストール]
  * [IntelliJ で Azure 用の Hello World Web アプリを作成する]
  * [Azure Toolkit for IntelliJ の新機能]

Java での Azure の使用の詳細については、 [Azure Java デベロッパー センター]を参照してください。

<!-- URL List -->

[Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse.md
[Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij.md
[Eclipse で Azure 用の Hello World Web アプリを作成する]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[IntelliJ で Azure 用の Hello World Web アプリを作成する]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Azure Toolkit for Eclipse のインストール]: ./azure-toolkit-for-eclipse-installation.md
[Azure Toolkit for IntelliJ のインストール]: ./azure-toolkit-for-intellij-installation.md
[What's New in the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Azure Toolkit for IntelliJ の新機能]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java デベロッパー センター]: http://go.microsoft.com/fwlink/?LinkID=699547

[Azul Systems の Zulu OpenJDK に関する Web ページ]: http://go.microsoft.com/fwlink/?LinkId=402457
[Azure のサービス エンドポイント]: http://go.microsoft.com/fwlink/?LinkID=699526
[Azure ストレージ アカウントの一覧]: http://go.microsoft.com/fwlink/?LinkID=699528
[コンポーネントのプロパティ]: http://go.microsoft.com/fwlink/?LinkID=699525#components_properties
[Azure 向け Hello World アプリケーションを Eclipse で作成する]: http://go.microsoft.com/fwlink/?LinkID=699533
[Eclipse での Azure アプリケーションのデバッグ]: http://go.microsoft.com/fwlink/?LinkID=699535
[大規模な環境のデプロイ]: http://go.microsoft.com/fwlink/?LinkID=699536
[エンドポイントのプロパティ]: http://go.microsoft.com/fwlink/?LinkID=699525#endpoints_properties
[環境変数のプロパティ]: http://go.microsoft.com/fwlink/?LinkID=699525#environment_variables_properties
[Eclipse 用の HDInsight Tools プラグイン]: ./hdinsight/hdinsight-apache-spark-eclipse-tool-plugin.md
[Eclipse を使用して Azure の Access Control Service で Web ユーザーを認証する方法]: http://go.microsoft.com/fwlink/?LinkID=264703
[SSL オフロードの使用方法]: http://go.microsoft.com/fwlink/?LinkID=699545
[Azure Toolkit for Eclipse のインストール]: http://go.microsoft.com/fwlink/?LinkId=699546
[ローカル ストレージのプロパティ]: http://go.microsoft.com/fwlink/?LinkID=699525#local_storage_properties
[Microsoft Azure クライアント API]: http://go.microsoft.com/fwlink/?LinkId=280397
[サーバー構成のプロパティ]: http://go.microsoft.com/fwlink/?LinkID=699525#server_configuration_properties
[セッション アフィニティ]: http://go.microsoft.com/fwlink/?LinkID=699548
[SSL オフロード]: http://go.microsoft.com/fwlink/?LinkID=699549
[新しいストレージ アカウントを作成するには]: http://go.microsoft.com/fwlink/?LinkID=699528#create_new
[Azure の仮想マシンおよびクラウド サービスのサイズ]: http://go.microsoft.com/fwlink/?LinkId=466520

<!-- IMG List -->

[ic710876]: ./media/azure-toolkit-for-eclipse-whats-new/ic710876.png
[ic710877]: ./media/azure-toolkit-for-eclipse-whats-new/ic710877.png
[ic710879]: ./media/azure-toolkit-for-eclipse-whats-new/ic710879.png
[ic710880]: ./media/azure-toolkit-for-eclipse-whats-new/ic710880.png
[ic710881]: ./media/azure-toolkit-for-eclipse-whats-new/ic710881.png
[ic710876]: ./media/azure-toolkit-for-eclipse-whats-new/ic710876.png
[ic710882]: ./media/azure-toolkit-for-eclipse-whats-new/ic710882.png
[ic710883]: ./media/azure-toolkit-for-eclipse-whats-new/ic710883.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh694270.aspx -->



<!--HONumber=Dec16_HO2-->


