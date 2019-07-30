---
title: Azure Service Fabric のサポート オプションを学習する | Microsoft Docs
description: サポートされている Azure Service Fabric クラスターのバージョンと、サポート チケットを提出するためのリンク
services: service-fabric
documentationcenter: .net
author: pkcsf
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: 667ee6a49238c1ecaabec631cb72804b0c4b2323
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304251"
---
# <a name="azure-service-fabric-support-options"></a>Azure Service Fabric のサポート オプション

アプリケーション ワークロードを実行している Service Fabric クラスターに適切なサポートを提供するために、Microsoft では、さまざまなオプションを用意しました。 必要なサポートのレベルや問題の重大度に応じて、適切なオプションを選ぶことができます。 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>運用上の問題を報告するか、Azure の有料サポートを要求する

Azure にデプロイされた Service Fabric クラスター上の問題を報告する場合は、[Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) または [Microsoft サポート ポータル](https://support.microsoft.com/oas/default.aspx?prid=16146)でサポートのチケットを開きます。

各項目の詳細情報
 
- [Azure に関する Microsoft からのサポート](https://azure.microsoft.com/support/plans/?b=16.44)。
- [Microsoft プレミア サポート](https://support.microsoft.com/en-us/premier)。

> [!Note]
> ブロンズの信頼性レベルで実行されているクラスターでは、テスト ワークロードのみを実行できます。 ブロンズの信頼性で実行されているクラスターで問題が発生した場合は、Microsoft サポート チームによって問題を軽減するための支援が提供されますが、根本原因分析は実行されません。 詳細については、「[クラスターの信頼性の特徴](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster)」を参照してください。
>
> 運用の準備が整ったクラスターで必要なものの詳細については、「[運用環境の準備状況チェックリスト](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)」を参照してください。

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>運用上の問題を報告するか、スタンドアロン Service Fabric クラスターの有料サポートを要求する

オンプレミスまたは他のクラウドにデプロイされた Service Fabric クラスター上の問題を報告する場合は、[Microsoft サポート ポータル](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)でサポートのチケットを開きます。

各項目の詳細情報

- [オンプレミスに関する Microsoft によるプロフェッショナル サポート](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)。
- [Microsoft プレミア サポート](https://support.microsoft.com/en-us/premier)。

## <a name="report-azure-service-fabric-issues"></a>Azure Service Fabric の問題を報告する

Microsoft では、Service Fabric の問題を報告するための GitHub リポジトリを設置しました。  また、次のフォーラムも積極的に監視しています。

### <a name="github-repo"></a>GitHub のリポジトリ 

Azure Service Fabric の問題は、[Service-Fabric-issues git リポジトリ](https://github.com/Azure/service-fabric-issues)で報告してください。 このリポジトリは、Azure Service Fabric に関する問題の報告と追跡、簡単な機能に関する要求を行うためのものです。 **稼働中のサイトの問題の報告にはこれを使用しないでください**。

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow と MSDN フォーラム

[StackOverflow の Service Fabric タグ][stackoverflow]と and the [Service Fabric forum on MSDN][msdn-forum]は、プラットフォームのしくみやそのプラットフォームで特定のタスクを実行する方法について質問する場合に最適です。

### <a name="azure-feedback-forum"></a>Azure フィードバック フォーラム

[Service Fabric 用の Azure フィードバック フォーラム][uservoice-forum]は、ユーザーが製品について持っている多くの機能に関するアイデアを提起するのに最適な場所です。Microsoft では、最も人気のあるリクエストが中長期的な計画に含まれるように再検討しています。 Microsoft では、コミュニティ内で自身の提案への支持を集めることをお勧めしています。

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Service Fabric プレビュー バージョン - 運用環境での使用はサポートされない

時折、フィードバックをいただきたい重要な機能を持つバージョンをプレビューとしてリリースします。 これらのプレビュー バージョンは、テスト目的でのみ使用するようにしてください。 運用環境のクラスターは常に、サポートされていて、安定した Service Fabric のバージョンを実行する必要があります。 プレビュー バージョンのメジャーおよびマイナーのバージョン番号は常に 255 で始まります。 たとえば、Service Fabric 255.255.5703.949 のバージョンを参照すると、このリリース バージョンはテスト クラスター内のみで使用されるべきであり、プレビュー段階です。 これらのプレビュー リリースは、[Service Fabric チームのブログ](https://blogs.msdn.microsoft.com/azureservicefabric)でも発表され、含まれる機能の詳細情報があります。
これらのプレビュー リリースの有償サポート オプションはありません。 [Azure Service Fabric の問題を報告する](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues)に含まれるオプションのいずれかを使用して、質問またはフィードバックを提供してください。

## <a name="next-steps"></a>次の手順

[サポートされている Service Fabric のバージョン](service-fabric-versions.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: https://aka.ms/servicefabricdocs
[sample-repos]: https://aka.ms/servicefabricsamples
