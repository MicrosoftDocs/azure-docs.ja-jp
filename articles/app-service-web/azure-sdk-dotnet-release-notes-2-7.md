---
title: "Azure SDK for .NET 2.7 および .NET 2.7.1 リリース ノート"
description: "Azure SDK for .NET 2.7 および .NET 2.7.1 リリース ノート"
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: 
ms.assetid: 877d070a-9bd5-49b3-8fac-6bb5f65c3554
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: 877fb34d2fe5d4b85157a8338e180358d2226ef1


---
# <a name="azure-sdk-for-net-27-and-net-271-release-notes"></a>Azure SDK for .NET 2.7 および .NET 2.7.1 リリース ノート
## <a name="overview"></a>概要
このドキュメントには、Azure SDK for .NET 2.7 リリースのリリース ノートが含まれます。 

このドキュメントには、Azure SDK for .NET 2.7.1 リリースのリリース ノートも含まれています。

Azure SDK 2.7 は、Visual Studio 2015 と Visual Studio 2013 でのみサポートされています。 [Azure SDK 2.6](https://azure.microsoft.com/downloads/) は、Visual Studio 2012 でサポートされる最後の SDK です。

このリリースの詳細については、[Azure SDK 2.7 の発表に関する投稿](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)と[Azure SDK 2.7.1 の発表に関する投稿](http://go.microsoft.com/fwlink/?LinkId=623850)をご覧ください。

## <a name="azure-sdk-for-net-27"></a>Azure SDK for .NET 2.7
### <a name="sign-in-improvements-for-visual-studio-2015"></a>Visual Studio 2015 のサインインの機能強化
Visual Studio 2015 用の Azure SDK 2.7 は、Visual Studio 2015 の新しい ID 管理機能をサポートしています。  これには、ロールベースの Access Control、クラウド ソリューション プロバイダー、DreamSpark や他のアカウント、サブスクリプションの種類を使用して Azure にアクセスするアカウント向けのサポートが含まれています。

Azure SDK 2.7 に含まれているサインインの機能強化は、Visual Studio 2015 でのみ利用できます。 Visual Studio 2013 のサポートは、Azure SDK 2.7.1 に含まれています。

### <a name="mobile-sdk"></a>Mobile SDK
**Mobile Apps** テンプレートが、最新の [NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) とセットアップ プロセスを反映するように更新されました。

### <a name="service-bus"></a>Service Bus
一般的なバグの修正と機能強化。 更新プログラムと機能の詳細については、最新の [Service Bus NuGet](http://www.nuget.org/packages/WindowsAzure.ServiceBus/)のリリース ノートをご覧ください。

### <a name="hdinsight-tools"></a>HDInsight ツール
このリリースでは、次の更新が行われました。 これらの更新プログラムはプレビュー段階です。 詳細については、 [このブログ](http://go.microsoft.com/fwlink/?LinkId=619108)をご覧ください。

* Tez ジョブでの Hive 用の Hive グラフ
* 完全な Hive DML IntelliSense のサポート
* Pig テンプレートのサポート
* Azure サービス用の Storm テンプレート

#### <a name="breaking-changes"></a>重大な変更
* このバージョンのツールを使用する場合は、以前の **Storm** プロジェクトをアップグレードする必要があります。 詳細については、 [このブログ](http://go.microsoft.com/fwlink/?LinkId=619108)をご覧ください。
* Visual Studio Web Express はサポートされなくなりました。 詳細については、 [このブログ](http://go.microsoft.com/fwlink/?LinkId=619108)をご覧ください。

### <a name="azure-app-service-tools"></a>Azure App Service ツール
このリリースでは、Web ツールの拡張機能に対して次の更新が行われました。 詳細については、[このブログ](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)をご覧ください。 

* DreamSpark アカウントのサポートの追加
* 新しい Azure リソース管理 API をサポートするための Azure ツールの全面的な変更
* [Cloud Explorer](#cloud_explorer) に追加された Azure App Service のサポート

#### <a name="known-issues"></a>既知の問題
Web アプリ デプロイメント スロットのノードがサーバー エクスプローラーのスロット ノードに表示されません。また、Web アプリ デプロイ スロットの子ノードがクラウド エクスプローラーに読み込まれません。 この問題は解決され、次の SDK リリースに向けて準備されています。 

### <a name="a-namecloudexploreracloud-explorer-for-visual-studio-2015"></a><a name="cloud_explorer"></a>Cloud Explorer for Visual Studio 2015
Azure SDK 2.7 には、Azure リソースを表示し、そのプロパティを調べ、Visual Studio 内から開発者が重要な操作を実行できる、Visual Studio 2015 のクラウド エクスプローラーが含まれています。 

クラウド エクスプローラーは、次をサポートしています。

* Azure リソースのリソース グループとリソースの種類の表示 
* 名前によるリソースの検索 (リソースの種類の表示で使用可能)
* ロール ベースの Access Control (RBAC) が適用されたサブスクリプションとリソースのサポート 
* 選択したリソース固有の開発者向けの操作を表示する統合されたアクション パネル (例: リソース マネージャー スタックを使用して作成された Virtual Machines 用のリモート デバッガーのアタッチ、Virtual Machines 用の診断データの表示など)
* 開発やテスト時に一般的に必要な開発者向けのプロパティを表示する統合されたプロパティ パネル 
* リソースの列挙時に使用するアカウントの簡単な切り替え (ツールバーの [設定] コマンドを使用) 
* リソースの列挙時に使用するサブスクリプションのフィルター処理 (ツールバーの [設定] コマンドを使用) 
* リソースおよびリソース グループを管理するための Azure ポータルへのディープ リンク 

### <a name="azure-resource-manager-tools"></a>Azure リソース マネージャー ツール
Azure リソース マネージャー ツールは、ロール ベースの Access Control (RBAC) と新しいサブスクリプションの種類を使用するように更新されました。  これらの変更には、従来のストレージに加えて、新しいストレージ アカウントを使用してデプロイメント中にアーティファクトを格納する機能が含まれています。  

SDK 2.7 を使用して以前のバージョンの SDK から Azure リソース グループのプロジェクトを使用している場合は、従来のストレージの代わりに新しいストレージ アカウントを使用してデプロイするために、新しいデプロイメント スクリプトが必要です。  プロジェクトに変更を加えて新しいスクリプトを追加する前に、プロンプトが表示されます。  古いスクリプトは名前が変更されます。新しいスクリプトに手動で変更を加える必要があります。

### <a name="storage-explorer-tools"></a>ストレージ エクスプローラー ツール
* 追加の BLOB を表示するためのサポート。 詳細については、 [このブログの投稿](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/13/introducing-azure-storage-append-blob.aspx)をご覧ください。 
* サーバー エクスプローラーから Premium Storage アカウントを表示するためのサポート。 サーバー エクスプローラーには、Premium Storage アカウントをサポートする唯一の種類である、Premium Storage アカウントのページ BLOB のみが表示されます。

### <a name="azure-data-factory-tools-for-visual-studio"></a>Visual Studio の Azure Data Factory ツール
Visual Studio 用の **Azure Data Factory ツール** が導入されています。 次に、有効な機能を示します。 詳細については、 [このブログ](http://go.microsoft.com/fwlink/?LinkId=617530) をご覧ください。

* **テンプレートに基づく作成**: 使用事例ベースのテンプレート、データ移動のテンプレート、データ処理のテンプレートを選択して、エンド ツー エンドのデータ統合ソリューションをデプロイし、Data Factory を使用して簡単に作業を開始できます。 
* **Data Factory エンティティの作成とデプロイのためのソリューション エクスプローラーとの統合**: パイプラインと関連エンティティを Visual Studio プロジェクトとして作成し、デプロイします。 
* **作成時の視覚的相互作用のためのダイアグラム ビューとの統合**: ダイアグラム ビューの補助を使用してパイプラインとデータセットを視覚的に作成します。 
* **デプロイ済みのエンティティの参照と相互作用のためのサーバー エクスプローラーとの統合**: サーバー エクスプローラーを利用して、デプロイ済みの Data Factory と対応するエンティティを参照します。 デプロイ済みの Data Factory や任意のエンティティ (パイプライン、リンクされたサービス、データセット) をプロジェクトにインポートします。 
* **スキーマ検証と豊富な Intellisense による JSON 編集**: 豊富な Intellisense とスキーマ検証を使用して Data Factory エンティティの JSON ドキュメントを効率的に構成し、編集します。 
* **マルチ環境の公開**: 開発環境、テスト環境、運用環境に対して、環境ごとに個別の構成ファイルを作成し、作成したパイプラインを発行します。
* **Pig、Hive、.Net ベースのデータ処理のサポート**: Data Factory プロジェクトにおける Pig スクリプトと Hive スクリプトをサポートします。 .Net アクティビティを管理するための C# プロジェクトの参照をサポートします。

## <a name="azure-sdk-for-net-271"></a>Azure SDK for .NET 2.7.1
次のセクションには、the Azure SDK for .NET 2.7.1 に導入された更新が含まれています。

### <a name="hdinsight-tools"></a>HDInsight ツール
HDInsight ツールの更新の詳細については、 [このブログ](http://go.microsoft.com/fwlink/?LinkId=623831)をご覧ください。

* Hive ジョブ演算子ビュー (新機能)
  
    Hive クエリを理解しやすくするために、Hive 演算子表示機能が追加されました。 頂点内のすべての演算子を表示するには、ジョブ グラフの頂点をダブルクリックします。 特定の演算子の詳細を表示するには、その演算子の上にマウス ポインターを置きます。
* Hive エラー マーカー (新機能)
  
    文法エラーをすぐに表示できるように、Hive エラー マーカー機能が追加されました。 さらに、エラー メッセージが強化され、詳細な文法エラーをすぐに確認できるようになりました (このリリースの前は、Hive スクリプトをクラスターに送信し、エラー メッセージの詳細を取得する前にしばらく待つ必要がありました)。  
* Storm トポロジ グラフ (新機能)
  
    トポロジが期待どおりに動作しているかどうかを確認する場合は、視覚化することが非常に重要です。 このリリースでは、Storm グラフの視覚化を追加しました。 トポロジの重要なメトリックを視覚化できます (たとえば、特定のボルトが “ビジー” かどうかを色で示すことができます)。 また、ボルト/スパウトをダブルクリックすることで詳細を表示できます。
* Azure ポータルで作成された HDInsight クラスターのサポート (バグ修正プログラム) 
  
    Visual Studio を使用して、クラスターの作成場所に関係なく、すべての HDInsight クラスターのジョブを表示し、クラスターにジョブを送信できるようになりました。
* IntelliSense に対するサポートの向上と Hive メタデータの高速読み込み (機能強化)
  
    ユーザー フレンドリーな候補を追加することによって IntelliSense の機能を強化しました。 たとえば、テーブルのエイリアスも IntelliSense の候補として表示されるため、クエリをより簡単に記述できるようになりました。 さらに、Hive メタデータの読み込み機能も強化されているため、Hive メタストアのすべてのデータベース、テーブル、列がほんの数秒で表示されます。

HDInsight ツールの更新の詳細については、 [このブログ](http://go.microsoft.com/fwlink/?LinkId=623831)をご覧ください。

### <a name="improvements-in-visual-studio-2013"></a>Visual Studio 2013 での機能強化
* Azure SDK 2.7.1 では、Visual Studio 2013 は、ロール ベースの Access Control、クラウド ソリューション プロバイダー、および Dreamspark を通して Azure アカウントとサブスクリプションにアクセスできます。
* Azure SDK 2.7.1 では、新しい Cloud Explorer ツール ウィンドウを Visual Studio 2013 で利用できるようになりました。

### <a name="known-issues"></a>既知の問題
Visual Studio Community 2013 用のAzure SDK 2.6 または 2.7.1 を英語版以外の OS にインストールすると、Visual Studio の英語のリソースと英語以外のリソースが不一致である可能性があるという警告が表示されます。 この警告は無視しても問題はありません。 これは、コンピューターに Visual Studio Community 2013 がインストールされているないときに、英語版以外の OS に SDK をインストールする場合にのみ発生します。 警告は、言語パックの RTM リソースが Visual Studio に適用された後、Update 4 が適用される前に表示されます。 警告を消去すれば、言語パックの処理が続行され、言語パックの中の Update 4 のバージョンの適用を完了できます。

LightSwitch プロジェクトはこのリリースと互換性がありません。 この問題は、次の SDK のリリースで解決されます。

## <a name="also-see"></a>関連トピック
[Azure SDK 2.7.1 の発表に関する投稿](http://go.microsoft.com/fwlink/?LinkId=623850)

[Azure SDK 2.7 アナウンスの投稿](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)

[Azure SDK for .NET および API のサポートと提供終了に関する情報](https://msdn.microsoft.com/library/azure/dn479282.aspx/)




<!--HONumber=Dec16_HO2-->


