---
title: "Azure WebJobs のドキュメント リソース"
description: "Azure の Web ジョブと Azure Web ジョブ SDK を使用する方法を学習するための推奨リソース。"
services: app-service
documentationcenter: .net
author: tdykstra
manager: erikre
editor: jimbe
ms.assetid: ed005e56-4334-4641-a5e5-15435c2be36b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2017
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: fc5c87e45f3c92172f01a462451bc145ce8b0b16
ms.lasthandoff: 03/27/2017


---
# <a name="azure-webjobs-documentation-resources"></a>Azure WebJobs のドキュメント リソース
## <a name="overview"></a>概要
このトピックでは、Azure Web ジョブと Azure Web ジョブ SDK の使用方法に関するドキュメント リソースへのリンクを掲載しています。 Azure Web ジョブでは、 [App Service の Web アプリ、API アプリ、またはモバイル アプリ](../app-service/app-service-value-prop-what-is.md)のコンテキストで、スクリプトやプログラムをバックグラウンド プロセスとして簡単に実行できます。 cmd、bat、exe (.NET)、ps1、sh、php、py、js、jar などの実行可能ファイルをアップロードして、実行できます。 これらのプログラムは、スケジュールに従って (cron)、または継続的に Web ジョブとして実行されます。

[WebJobs SDK](websites-webjobs-resources.md) の目的は、Web ジョブで実行できる一般的な作業 (画像処理、キュー処理、RSS 情報集約、ファイル管理、電子メールの送信など) を単純なコードで記述できるようにすることです。 WebJobs SDK には、Azure Storage や Service Bus の操作、タスクのスケジューリング、エラー処理など、一般的な用途に対応した各種の機能が組み込まれています。 拡張性にも優れた設計となっており、 [拡張機能のオープン ソース リポジトリ](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)が存在します。 [Azure Functions](../azure-functions/functions-overview.md) (現在プレビュー段階) は、C# スクリプト、Node.js、その他の言語で動作するバージョンの WebJobs SDK に基づいています。 

Web ジョブの作成、デプロイ、管理は、Visual Studio の統合ツールでシームレスに行うことができます。 テンプレートから Web ジョブを作成し、発行、および管理 (実行、停止、監視、デバッグ) できます。 

Azure ポータルの Web ジョブ ダッシュボードは、Web ジョブ内の個々の関数を呼び出す機能など、強力な管理機能を提供して Web ジョブの実行をきめ細かく制御できます。 また、ダッシュボードでは、関数の実行時間とログ出力も表示されます。 

## <a name="getstarted"></a>Web ジョブと Web ジョブ SDK の概要
* [Azure Web ジョブの紹介](http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx)
* [Azure WebJobs はすごいので、今すぐ使ってみよう!](http://www.troyhunt.com/2015/01/azure-webjobs-are-awesome-and-you.html) (Troy Hunt によるブログの投稿です。)
* [Azure Web ジョブの機能](https://azure.microsoft.com/blog/2014/10/22/webjobs-goes-into-full-production/)
* [Azure Web ジョブ SDK とは](websites-dotnet-webjobs-sdk.md)
* [Microsoft Patterns and Practices によるバック グラウンド ジョブのガイダンス](/documentation/articles/best-practices-background-jobs/)
* [Microsoft Azure Web ジョブ SDK の 1.1.0 RTM の発表](https://azure.microsoft.com/blog/azure-webjobs-sdk-1-1-0-rtm/)
* [Azure Web ジョブ SDK の使用](websites-dotnet-webjobs-sdk-get-started.md)
* [Web ジョブ SDK を使用して Azure キュー ストレージを操作する方法](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)
* [Web ジョブ SDK で Azure Blob Storage を使用する方法](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)
* [Web ジョブ SDK を使用して Azure Table Storage を使用する方法](websites-dotnet-webjobs-sdk-storage-tables-how-to.md)
* [Web ジョブ SDK で Azure Service Bus を使用する方法](websites-dotnet-webjobs-sdk-service-bus.md)
* [Azure Web ジョブ SDK クイック リファレンス (PDF ダウンロード)](https://go.microsoft.com/fwlink/p/?linkid=845558)
* [GitHub の Web ジョブ設定に関するドキュメント](https://github.com/projectkudu/kudu/wiki/Web-jobs)。
* ビデオ
  * [Web ジョブと Web ジョブ SDK](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-153-WebJobs-with-Pranav-Rastogi?utm_source=dlvr.it&utm_medium=twitter)
  * [Channel 9 の Azure Web ジョブ ビデオ シリーズ](http://channel9.msdn.com/Tags/azurefridaywebjobs)
  * [Visual Studio 向けの Web ジョブ ツールの概要](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
  * [Web ジョブ ツールとリモート デバッグ](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)
  * [Pranav Rastogi による Azure Web ジョブの更新 - リリース 1.1 での機能拡張](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-183-Azure-WebJobs-Update-with-Pranav-Rastogi)

次の「[Web ジョブのデプロイ](#deploy)」および「[Web ジョブのテストとデバッグ](#debug)」セクションも参照してください。

## <a name="deploy"></a>Web ジョブのデプロイ
* [Visual Studio を使用して Azure WebJobs をデプロイする方法](websites-dotnet-deploy-webjobs.md)
* [Azure ポータルを使用して WebJobs をデプロイする方法](web-sites-create-web-jobs.md)
* [Azure Web ジョブのコマンドラインまたは継続的な配信の有効化](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)
* [Git で Web ジョブを使用して Azure に .NET コンソール アプリケーションをデプロイ](http://blog.amitapple.com/post/73574681678/git-deploy-console-app/)
* [F# WebJob を Azure にデプロイする](http://blogs.msdn.com/b/dave_crooks_dev_blog/archive/2015/02/18/deploying-f-web-job-to-azure.aspx)
* [Deploying custom services as Azure Webjobs (Azure Web ジョブとしてカスタム サービスをデプロイする)](http://withouttheloop.com/articles/2015-06-23-deploying-custom-services-as-azure-webjobs/)
* ビデオ
  * [Visual Studio 向けの Web ジョブ ツールの概要](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
  * [Web ジョブ ツールとリモート デバッグ](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

## <a name="schedule"></a>Web ジョブのスケジュール設定
* [[Azure Web ジョブの追加] ダイアログ ボックス](websites-dotnet-deploy-webjobs.md#configure)
* [Azure ポータルでのスケジュール実行の Web ジョブの作成](web-sites-create-web-jobs.md#CreateScheduled)
* [Hooking up a scheduler job to a WebJob (Web ジョブとスケジューラー ジョブを関連付ける)](http://blog.davidebbo.com/2015/05/scheduled-webjob.html)
* [Scheduling Azure WebJobs with cron expressions (CRON 式を使用して Azure Web ジョブをスケジュールする)](http://blog.amitapple.com/post/2015/06/scheduling-azure-webjobs/)
* [Web ジョブ SDK の TimerTrigger を使用した Web ジョブの個々の関数のスケジューリング](websites-dotnet-webjobs-sdk.md#schedule)

## <a name="debug"></a>Web ジョブのテストとデバッグ
* [Azure Web ジョブに使用できる Visual Studio の新しい開発者機能とデバッグ機能](http://blogs.msdn.com/b/webdev/archive/2014/11/12/new-developer-and-debugging-features-for-azure-webjobs-in-visual-studio.aspx)
* [Web ジョブ ダッシュボードの表示](websites-dotnet-webjobs-sdk-get-started.md#view-the-webjobs-sdk-dashboard)
* [Web ジョブ SDK を使用してログを記述し、ダッシュボードに表示する方法](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)
* [Web ジョブのリモート デバッグ](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebugwj)
* [だれがその BLOB を作成したのか](http://blogs.msdn.com/b/jmstall/archive/2014/02/19/who-wrote-that-blob.aspx) 
* [クラウドでの対話型のコードのホスティング](http://blogs.msdn.com/b/jmstall/archive/2014/04/26/hosting-interactive-code-in-the-cloud.aspx)
* [Azure Web ジョブへのトレースの追加](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)
* [Microsoft Azure Storage の監視、診断、およびトラブルシューティング](../storage/storage-monitoring-diagnosing-troubleshooting.md)
* ビデオ
  * [Web ジョブ ツールとリモート デバッグ](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

## <a name="scale"></a>WebJobs のスケーリング
* [Azure Websites を使用した Web アプリケーションのスケーリング](http://msdn.microsoft.com/magazine/dn786914.aspx)
* [Azure App Service: 大規模なビジネス対応 Web Apps の設計](https://channel9.msdn.com/Events/Build/2014/3-626)。 Web ジョブ SDK など、Web ジョブを使用した Web アプリのスケール方法について説明します。
* ビデオ
  * [Web ジョブのスケール アウト](http://channel9.msdn.com/Shows/Azure-Friday/Azure-WebJobs-105-Scaling-out-Web-Jobs)

## <a name="additional"></a>その他の Web ジョブのリソース
* [Magnus Mårtensson 氏による Azure WebJobs GA ブログ記事](http://magnusmartensson.com/azure-webjobs-ga)
* [Azure App Service での Powershell Web ジョブの実行](http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx)
* [Azure がトリガーした Web ジョブ完了時の通知の受信](http://blog.amitapple.com/post/2014/03/webjobs-notification/)
* [Web ジョブを使用した簡単な Web アプリのバックアップ保持ポリシー](https://azure.microsoft.com/blog/2014/04/28/simple-web-site-backup-retention-policy-with-webjobs/)
* [Azure Web Apps および Cloud Services が、最初の要求時には応答が遅い](http://wp.sjkp.dk/windows-azure-websites-and-cloud-services-slow-on-first-request/)。 Web ジョブを使用して、Standard の価格レベルでのみ使用可能な AlwaysOn 機能をシミュレートする方法を示しています。
* [WebJobs のグレースフル シャットダウン](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.U72Il_5OWUl)。 WebJobs SDK のグレースフル シャットダウンについては、「[グレースフル シャットダウン](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful)」を参照してください。
* [Azure WebJobs と AzCopy を使用したバックアップの自動化](http://markjbrown.com/azure-webjobs-azcopy/)
* ビデオ
  * [Magnus Mårtensson 氏による Azure Web ジョブ ビデオ](https://www.youtube.com/playlist?list=PLqp1ZOYYUSd81yEzMYLTw8cz91wx_LU9r)
  * [Channel 9 の Azure Web ジョブ ビデオ シリーズ](http://channel9.msdn.com/Tags/azurefridaywebjobs)

## <a name="additionalsdk"></a>その他の Web ジョブ SDK のリソース
* [Web ジョブ SDK リリース ノート](https://github.com/Azure/azure-webjobs-sdk/wiki/Release-Notes)
* [Web ジョブ SDK のソース コード](https://github.com/Azure/azure-webjobs-sdk)
* [WebJobs SDK の拡張機能のソース コード](https://github.com/Azure/azure-webjobs-sdk-extensions)と、[機能拡張モデルの詳細なガイド](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)  
* [WebJobs SDK のスクリプトのソース コード](https://github.com/Azure/azure-webjobs-sdk-script/) ([Azure Functions](../azure-functions/functions-overview.md) に使用)
* [Web ジョブ SDK を使用した、FREB のファイルの Azure Storage へのアップロード](http://thenextdoorgeek.com/post/WAWS-WebJob-to-upload-FREB-files-to-Azure-Storage-using-the-WebJobs-SDK)
* [Azure がホストする Web ジョブのログに関するメリットが得られる、Azure 外部での Azure Web ジョブのホスティング](http://bypassion.dk/?p=510)
* [Azure Web ジョブを使用したデータ インポート ツールの構築](http://www.freshconsulting.com/building-data-import-tool-azure-webjobs/)
* [Azure Functions の概要](../azure-functions/functions-overview.md)
* ビデオ
  * [Channel 9 の Azure Web ジョブ ビデオ シリーズ](http://channel9.msdn.com/Tags/azurefridaywebjobs)

## <a name="samples"></a>サンプル Web ジョブ アプリケーション
* [GitHub 上で Web ジョブ チームが提供するサンプル アプリケーション](https://github.com/azure/azure-webjobs-sdk-samples)
* [Web ジョブ SDK を使用した Web ジョブ バックエンドを備えた簡単な Azure Web アプリ](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb)
* [SiteMonitR](http://code.msdn.microsoft.com/SiteMonitR-dd4fcf77)。 スケジュールされたイベント ドリブンの Web ジョブの使用方法を示します。 ブログ記事「 [Azure Web ジョブ SDK を使用した SiteMonitR の再構築](http://www.bradygaster.com/post/rebuilding-the-sitemonitr-using-windows-azure-webjobs)」を参照してください。

## <a name="blogs"></a>ブログ
* [Azure のブログ](/blog)
* [Amit Apple のブログ](http://blog.amitapple.com/)。 Web ジョブ (SDK ではない) に重点を置いています。
* [Magnus Mårtensson 氏のブログ](http://magnusmartensson.com/)

## <a name="gethelp"></a>Web ジョブに関する支援
* [Web ジョブに関する StackOverflow](http://stackoverflow.com/questions/tagged/azure-webjobs)
* [Web ジョブ SDK に関する StackOverflow](http://stackoverflow.com/questions/tagged/azure-webjobssdk)
* [Azure Functions に関する StackOverflow](http://stackoverflow.com/questions/tagged/azure-functions)
* [Azure と ASP.NET のフォーラム](http://forums.asp.net/1247.aspx)
* [Azure App Service Web Apps フォーラム](http://social.msdn.microsoft.com/Forums/azure/home?forum=windowsazurewebsitespreview)
* [Azure Web Apps のユーザーからの意見のサイト](https://feedback.azure.com/forums/169385-websites/)
* [Twitter](http://twitter.com/)。 ハッシュタグ #AzureWebJobs を使用してください。
* [Web ジョブのバグまたは問題を報告する](https://github.com/projectkudu/kudu/wiki/Reporting-WebJobs-issues)


