---
title: "Azure SDK for .NET 2.5.1 リリース ノート"
description: "Azure SDK for .NET 2.5.1 リリース ノート"
services: app-service
documentationcenter: .net,nodejs,java
author: Juliako
manager: erikre
editor: 
ms.assetid: 8d3d815f-bb58-447e-8ff0-f9b9603c7b00
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/10/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: dc7b51cd0d015770b1100e895da633e8bde4b8da


---
# <a name="azure-sdk-for-net-251-release-notes"></a>Azure SDK for .NET 2.5.1 リリース ノート
このドキュメントには、Azure SDK for .NET 2.5.1 リリースのリリース ノートが含まれます。 

## <a name="azure-sdk-for-net-251-release-notes"></a>Azure SDK for .NET 2.5.1 リリース ノート
Azure SDK for .NET 2.5.1 の新機能と更新点を以下に示します。

* **Web ツールの拡張機能**に関連する新機能とシナリオ。 
  
  * Azure Websites から Azure App Service への名称変更。 詳細については、「 [Azure App Service と既存の Azure サービス](../app-service-web/app-service-changes-existing-services.md)」を参照してください。
  * Azure API Apps (プレビュー) のサポートが追加されたため、顧客は ASP.NET プロジェクトを API Apps として発行できるようになり、C# プロジェクトで [追加] > [Azure API アプリ クライアント] ジェスチャを使用して、デプロイされた API アプリの構造に基づいてコードを生成することができます。 
  * サーバー エクスプローラーの Websites ノードは廃止予定で、代わりに Azure App Service ノードが使用されます。このノードには、Azure API アプリ、モバイル アプリ、Web アプリのリソース グループ ベースのグループ化のサポートが含まれています。
  * Azure Mobile Apps (プレビュー) のサポートが追加されたため、顧客は新しいモバイル アプリ プロジェクトの作成、モバイル アプリ コントローラーの追加、プロジェクトの発行、アプリケーションのリモート デバッグを実行できます。
  * [追加] > [Azure API アプリ クライアント] ジェスチャがローカルの Swagger JSON ファイルをサポートするようになり、API 開発者は Swagger を Swashbuckle などのサード パーティ製のパッケージを使用して生成したり、手動で作成することができます。 このように、クライアント開発者はコード生成機能を使用して、C# プロジェクトで任意の Swagger エンドポイントを使用できます。 
  * Web アプリと API アプリの発行ダイアログは、リソース グループ化という Azure ポータルの概念をサポートするように拡張され、Azure リソース グループと App Service プランの選択/作成は、新しい Web アプリと API アプリのプロビジョニング ダイアログで表されます。 
  * Azure API アプリのサーバー エクスプローラーのノードは、Azure ポータルの API アプリに関する深いリンクや、ログのストリーミングやリモート デバッグなどの他の機能へのリンクを提供します。
    
    Azure SDK .NET 2.5.1 の既知の問題と現在の制限事項については、 [この下のセクション](app-service-release-notes.md#known_issues_2_5_1) を参照してください。
* Visual Studio の **HDInsight** ツールに関連する新しい機能とシナリオは、このリリースで有効になっています。 
  
  * Hive スクリプトのローカルな検証。 ツール バーの [スクリプトの検証] をクリックして、スクリプトにエラーがあるかどうかを確認します。 
  * Hive ジョブのデバッグの向上。 Visual Studio の YARN ログにアクセスして、Hive ジョブをデバッグできるようになりました。 アプリケーションにパフォーマンスの問題がある場合、YARN ログを調査することで有用な情報が得られます。
  * (パブリック プレビュー) Hive 用にキーワードのオート コンプリートと IntelliSense のサポート。 Hive スクリプトの作成を容易にするため、HDInsight Tools for Visual Studio では Hive 用にキーワードのオート コンプリートと IntelliSense のサポートが追加されました。
  * Storm のサポート。 HDInsight Tools for Visual Studio を使用して、C# で Storm トポロジ/スパウト/ボルトを開発できるようになりました。 その後、開発したトポロジを Storm クラスターに送信し、トポロジ/ボルト/スパウトの状態を確認することができます。 システム ログと顧客のログを使用して、Storm トポロジ/ボルト/スパウトのトラブルシューティングを行うことができます。 また、Storm の既存の JAVA 資産を HDInsight で使用することもできます。
    
    詳細については、「 [HDInsight Hadoop Tools for Visual Studio の使用開始](../hdinsight/hdinsight-hadoop-visual-studio-tools-get-started.md)」を参照してください。

## <a name="a-idknownissues251aazure-sdk-for-net-251-known-issues-and-limitations"></a><a id="known_issues_2_5_1"></a>Azure SDK for .NET 2.5.1 の既知の問題と制限事項
* Azure の API アプリは、モバイル アプリのデプロイメント ターゲットとして表示されます。 今後のリリースまで、Web アプリがモバイル アプリの唯一のデプロイ先である必要があります。 
* Azure の API アプリのプロビジョニングが成功しても、Azure App Service の [アクティビティ] ウィンドウでの進行状況の更新が断続的に失敗することがあります。 回避策として、Azure ポータルの新しい Azure API アプリで状況を確認します。 
* [ファイル]、[新しいプロジェクト]、[API アプリ] と移動して F5 キーを押すと、default/index.html がないため、HTTP エラーが発生します。 回避策として、/api/values URL を手動で参照します。 
* サーバー エクスプローラーのアイコンが、断続的に平坦に表示されます。 VS を再起動すると、この問題は解決します。 
* Web アプリまたは API アプリのプロビジョニング中に例外 (クォータの超過エラーや Azure API アプリのゲートウェイ名の重複など) がスローされた場合、エラーには未加工の JSON テキストが表示されます。 
* プロジェクトの作成時に Application Insights をオンにすると、断続的なプロジェクト作成の問題が発生します。
* 場合によっては、生成された Azure API アプリのクライアント コードに名前空間が欠けていて、コードをコンパイルするために手動で名前空間を含める (または Visual Studio のキューを使用して自動的にインポートする) 必要があります。 
* モバイル アプリ プロジェクトは Web アプリに発行する必要がありますが、モバイル アプリ プロジェクトにはデータベースが必要なため、Azure ポータルでモバイル アプリとして作成したサイトを選択する必要があります。 
* モバイル アプリの開始ページは、"モバイル アプリ" ではなく、"モバイル サービス" という用語を使用します。 
* モバイル アプリ プロジェクトの作成には、最大で 1 分かかることがあります。 
* API アプリのプロビジョニング中、(場合によっては) アクセス許可を適切に設定できなかったことを示すエラーが Azure API から返されることがありますが、API アプリは適切にプロビジョニングされて、使用可能になっています。 Azure ポータルを使用して、手動でアクセス許可を設定することができます。
* Application Insights は、API アプリ テンプレートとモバイル アプリ テンプレートではサポートされていません。
* API アプリ プロジェクトは、クラウド サービス プロジェクトと組み合わせて使用することはできません。
* API アプリのプロジェクト テンプレートは C# でのみ利用できます。
* コンテキスト メニューの [Azure API アプリ クライアントの追加] を使用した API アプリの使用は、C# でのみサポートされます。




<!--HONumber=Nov16_HO3-->


