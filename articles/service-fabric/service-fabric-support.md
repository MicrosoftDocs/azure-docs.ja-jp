---
title: "Azure Service Fabric のサポート オプションを学習する | Microsoft Docs"
description: "サポートされている Azure Service Fabric クラスターのバージョンと、サポート チケットを提出するためのリンク"
services: service-fabric
documentationcenter: .net
author: pkc
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/15/2017
ms.author: pkc
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: 78e68cff3a757cbbcd8dc6f53120e6a4af54591a
ms.contentlocale: ja-jp
ms.lasthandoff: 08/12/2017

---
# <a name="azure-service-fabric-support-options"></a>Azure Service Fabric のサポート オプション

アプリケーション ワークロードを実行している Service Fabric クラスターに適切なサポートを提供するために、Microsoft では、さまざまなオプションを用意しました。 必要なサポートのレベルや問題の重大度に応じて、適切なオプションを選ぶことができます。 


<a id="getlivesitesupportonazure"></a>

## <a name="report-production-or-live-site-issues-or-request-paid-support-for-azure"></a>運用上の問題または稼働中のサイトの問題を報告するか、Azure の有料サポートを要求する

Azure にデプロイされた Service Fabric クラスター上の稼働中のサイトの問題を報告する場合は、[Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) または [Microsoft サポート ポータル](http://support.microsoft.com/oas/default.aspx?prid=16146)でプロフェッショナル サポートのチケットを申請してください。

各項目の詳細情報
 
- [Azure に関する Microsoft によるプロフェッショナル サポート](https://azure.microsoft.com/en-us/support/plans/?b=16.44)。
- [Microsoft プレミア サポート](https://support.microsoft.com/en-us/premier)。

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-or-live-site-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>運用上の問題または稼働中のサイトの問題を報告するか、スタンドアロン Service Fabric クラスターの有料サポートを要求する

オンプレミスまたは他のクラウドにデプロイされた Service Fabric クラスター上の稼働中のサイトの問題を報告する場合は、[Microsoft サポート ポータル](http://support.microsoft.com/oas/default.aspx?prid=16146)でプロフェッショナル サポートのチケットを開きます。

各項目の詳細情報

- [オンプレミスに関する Microsoft によるプロフェッショナル サポート](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)。
- [Microsoft プレミア サポート](https://support.microsoft.com/en-us/premier)。


<a id="getsupportonissues"></a>
## <a name="report-azure-service-fabric-issues"></a>Azure Service Fabric の問題を報告する 
Microsoft では、Service Fabric の問題を報告するための GitHub リポジトリを設置しました。  また、次のフォーラムも積極的に監視しています。

### <a name="github-repo"></a>GitHub のリポジトリ 
Azure Service Fabric の問題は、[Service-Fabric-issues git リポジトリ](https://github.com/Azure/service-fabric-issues)で報告してください。 このリポジトリは、Azure Service Fabric に関する問題の報告と追跡、簡単な機能に関する要求を行うためのものです。 **稼働中のサイトの問題の報告にはこれを使用しないでください**。

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow と MSDN フォーラム

[StackOverflow の Service Fabric タグ][stackoverflow]と [MSDN の Service Fabric フォーラム][msdn-forum]は、プラットフォームのしくみやそのプラットフォームで特定のタスクを実行する方法について質問する場合に最適です。

### <a name="azure-feedback-forum"></a>Azure フィードバック フォーラム

[Service Fabric 用の Azure フィードバック フォーラム][uservoice-forum]は、ユーザーが製品について持っている多くの機能に関するアイデアを提起するのに最適な場所です。Microsoft は、最も人気のあるリクエストが中長期的な計画に含まれるように再検討します。 Microsoft では、コミュニティ内で自身の提案への支持を集めることをお勧めしています。


<a id="releasesuport"></a>
## <a name="supported-service-fabric-versions"></a>サポートされている Service Fabric のバージョン

サポートされている Service Fabric のバージョンが常にクラスターで実行されているようにします。 Microsoft が Service Fabric の新バージョン リリースをアナウンスした日から最短で 60 日後には、以前のバージョンがサポート期間の終了として指定されます。 新バージョンのリリースは、[Service Fabric チーム ブログ](https://blogs.msdn.microsoft.com/azureservicefabric/)でお知らせします。

サポートされている Service Fabric のバージョンをクラスターで実行し続ける方法の詳細については、次のドキュメントを参照してください。

- [Azure クラスターで Service Fabric のバージョンをアップグレードする](service-fabric-cluster-upgrade.md)
- [スタンドアロンの Windows Server クラスターで Service Fabric のバージョンをアップグレードする](service-fabric-cluster-upgrade-windows-server.md)
 
サポートされている Service Fabric のバージョンとそのサポートの終了日の一覧を次に示します。

| **Service Fabric ランタイム クラスター** | **互換性のある SDK / NuGet パッケージのバージョン** | **サポートの終了日** |
| --- | --- | --- |
| 5.3.121 より前のすべてのクラスター バージョン |バージョン 2.3 以前 |2017 年 1 月 20 日 |
| 5.3.* |バージョン 2.3 以前 |2017 年 2 月 24 日 |
| 5.4.* |バージョン 2.4 以前 |2017 年 5 月 10 日     |
| 5.5.* |バージョン 2.5 以前 |2017 年 8 月 10 日    |
| 5.6.* |バージョン 2.6 以前 |2017 年 10 月 13 日    |
| 5.7.* |バージョン 2.7 以前 |最新バージョンのため、終了日はありません

<a id="previewversion"></a>
## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Service Fabric プレビュー バージョン - 運用環境で使用がサポートされていません。
時折、フィードバックをいただきたい重要な機能を持つバージョンをプレビューとしてリリースします。 これらのプレビュー バージョンは、テスト目的でのみ使用するようにしてください。 運用環境のクラスターは常に、サポートされていて、安定した Service Fabric のバージョンを実行する必要があります。 プレビュー バージョンのメジャーおよびマイナーのバージョン番号は常に 255 で始まります。 たとえば、Service Fabric 255.255.5703.949 のバージョンを参照すると、このリリース バージョンはテスト クラスター内のみで使用されるべきであり、プレビュー段階です。 これらのプレビュー リリースは、[Service Fabric チームのブログ](https://blogs.msdn.microsoft.com/azureservicefabric)でも発表され、含まれる機能の詳細情報があります。

これらのプレビュー リリースの有償サポート オプションはありません。 [Azure Service Fabric の問題を報告する](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues)に含まれるオプションのいずれかを使用して、質問またはフィードバックを提供してください。

## <a name="next-steps"></a>次のステップ

- [Azure クラスターで Service Fabric のバージョンをアップグレードする](service-fabric-cluster-upgrade.md)
- [スタンドアロンの Windows Server クラスターで Service Fabric のバージョンをアップグレードする](service-fabric-cluster-upgrade-windows-server.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: http://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: http://aka.ms/servicefabricdocs
[sample-repos]: http://aka.ms/servicefabricsamples

