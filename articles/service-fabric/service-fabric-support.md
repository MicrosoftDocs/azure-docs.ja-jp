---
title: Azure Service Fabric のサポート オプションについて説明します。
description: サポートされている Azure Service Fabric クラスターのバージョンと、サポート チケットを提出するためのリンク
author: pkcsf
ms.topic: troubleshooting
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: 977cd79de629670cef526f072340f8897fa6446e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92316497"
---
# <a name="azure-service-fabric-support-options"></a>Azure Service Fabric のサポート オプション

Service Fabric クラスターとアプリケーション ワークロードを管理するニーズに対応するため、いくつかのサポート要求オプションが作成されました。 必要なサポートの緊急度や問題の重大度に応じて、お客様にとって適切なオプションを選択できます。

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>運用上の問題を報告するか、Azure の有料サポートを要求する

Azure で実行されている Service Fabric クラスターに関連する問題を報告するには、[Azure Portal で](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)、または [Microsoft サポート ポータルで](https://support.microsoft.com/oas/default.aspx?prid=16146)サポート チケットを開きます。

各項目の詳細情報
 
- [Azure に関する Microsoft からのサポート](https://azure.microsoft.com/support/plans/?b=16.44)。
- [Microsoft プレミア サポート](https://support.microsoft.com/en-us/premier)。

> [!Note]
> ブロンズの信頼性レベルで実行されているクラスターまたは単一ノード クラスターでは、テスト ワークロードのみを実行できます。 ブロンズの信頼性で実行されているクラスターまたは単一ノード クラスターで問題が発生した場合は、Microsoft サポート チームによって問題を軽減するための支援が提供されますが、根本原因分析は実行されません。 詳細については、[クラスターの信頼性の特徴](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster)に関するページを参照してください。
>
> 運用の準備が整ったクラスターで必要なものの詳細については、「[運用環境の準備状況チェックリスト](./service-fabric-production-readiness-checklist.md)」を参照してください。

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>運用上の問題を報告するか、スタンドアロン Service Fabric クラスターの有料サポートを要求する

オンプレミスまたは他のクラウドで実行されている Service Fabric クラスターに関連する問題を報告するには、[Microsoft サポート ポータル](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)でプロフェッショナル サポートのチケットを開くことができます。

各項目の詳細情報

- [オンプレミスに関する Microsoft によるプロフェッショナル サポート](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)。
- [Microsoft プレミア サポート](https://support.microsoft.com/en-us/premier)。

## <a name="report-azure-service-fabric-issues"></a>Azure Service Fabric の問題を報告する

Microsoft では、Service Fabric の問題を報告するための GitHub リポジトリを設置しました。  また、次のフォーラムも積極的に監視しています。

### <a name="github-repo"></a>GitHub のリポジトリ 

Azure Service Fabric の問題は、[Service Fabric GitHub](https://github.com/microsoft/service-fabric/issues) で報告してください。 このリポジトリは、Azure Service Fabric に関連する問題の報告と追跡に加え、機能に関する小さな要求を行うためのものです。 **稼働中のサイトの問題を報告するのに、この手段を使用しないでください**。

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow と MSDN フォーラム

[StackOverflow の Service Fabric タグ][stackoverflow]と [MSDN の Service Fabric フォーラム][msdn-forum]は、プラットフォームのしくみや、それを使用して特定のタスクを実現する方法について、一般的な質問をする場合に最適です。

### <a name="azure-feedback-forum"></a>Azure フィードバック フォーラム

[Service Fabric の Azure フィードバック フォーラム][uservoice-forum]は、製品の機能に関する重要なアイデアを出すのに最適な場所です。 最も人気のある要求を確認し、それらを考慮して長期的な計画を立てる Microsoft の手段となっています。 Microsoft では、コミュニティ内で自身の提案への支持を集めることをお勧めしています。

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Service Fabric プレビュー バージョン - 運用環境での使用はサポートされない

Microsoft では、場合によって、重要な機能の変更を含む特別なプレビュー リリースを作成します。それについて、早期のフィードバックを調査するためです。 プレビュー バージョンは、運用環境のワークロードを処理しない分離されたテスト環境でのみ使用してください。 運用環境のクラスターは常に、サポートされていて、安定した Service Fabric のバージョンを実行する必要があります。 これらのプレビュー リリースについては、Microsoft は有償のサポート オプションを提供していません。

プレビュー バージョンのメジャーおよびマイナーのバージョン番号は常に 255 で始まります。 たとえば、Service Fabric のバージョンが 255.255.5703.949 であれば、このリリースはプレビュー段階にあり、テスト クラスターでの使用だけが想定されています。 これらのプレビュー リリースは、[Service Fabric チームのブログ](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)でも発表され、含まれる機能の詳細情報があります。 [Azure Service Fabric の問題を報告する](#report-azure-service-fabric-issues)に含まれるオプションのいずれかを使用して、質問またはフィードバックを提供してください。

## <a name="next-steps"></a>次のステップ

[サポートされている Service Fabric のバージョン](service-fabric-versions.md)

<!--references-->
[Microsoft Q&A question page]: /answers/topics/azure-service-fabric.html
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: ./index.yml
[sample-repos]: /samples/browse/?products=azure
[msdn-forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?category=windowsazureplatform