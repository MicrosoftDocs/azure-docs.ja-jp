---
title: Power BI ワークスペース コレクションに関するよくある質問
description: Power BI ワークスペース コレクションに関するよくある質問です。
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: markingmyname
ms.author: maghan
ms.topic: article
ms.workload: powerbi
ms.date: 09/25/2017
ms.openlocfilehash: 2de9d57e6febd0f0bec3bbbf1a175700c5b0bf71
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518910"
---
# <a name="power-bi-workspace-collections-faq"></a>Power BI ワークスペース コレクションに関するよくある質問

> [!IMPORTANT]
> Power BI ワークスペース コレクションは非推奨となっており、2018 年 6 月または契約に定める日までに限り利用できます。 アプリケーションの中断を避けるため、Power BI Embedded への移行をご検討ください。 Power BI Embedded にデータを移行する方法については、[Power BI ワークスペース コレクション コンテンツを Power BI Embedded に移行する方法](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)に関するページを参照してください。

## <a name="what-is-microsoft-power-bi-workspace-collections"></a>Microsoft Power BI ワークスペース コレクションとは何ですか。

Power BI ワークスペース コレクションは、アプリーション開発者が時間とコストをかけて独自のコントロールをゼロから構築しなくても、完全に対話型の魅力的なレポートや視覚エフェクトを顧客向けアプリケーションに埋め込むことができる Azure サービスです。 世界 9 か所のデータ センターで、SLA が適用された Power BI ワークスペース コレクションが提供されるようになりました。 高度なフィルター処理を実現する Power BI ワークスペース コレクションの行レベルのセキュリティ (RLS) によるデータ セキュリティのサポートなど、サービスの機能も強化されています。 また、Power BI ワークスペース コレクションの価格モデルが簡素化され、更新されました。

## <a name="who-would-want-to-use-microsoft-power-bi-workspace-collections-and-why"></a>Microsoft Power BI ワークスペース コレクションの対象ユーザーと、その用途を教えてください。

Microsoft Power BI ワークスペース コレクションは、視覚エフェクトを独自に構築することなく、あらゆるデバイスで魅力的な対話型のデータ視覚エフェクト エクスペリエンスを提供したいと考えているアプリケーション開発者を対象としています。 Microsoft Power BI ワークスペース コレクションを使用すれば、開発者は DirectQuery を使って常に最新のビューを提供できます。 また、開発者は、Azure Resource Manager API や Power BI API を使用して、Power BI をプログラムによってデプロイ、管理、自動化することもできます。 その他のサービスと同様に、Power BI Embedded サービスは、使用状況とアプリケーションのニーズに応じて、自動的に拡張します。 Power BI ワークスペース コレクション サービスは、従量課金制の使用量ベースの価格モデルを特徴としています。

## <a name="how-does-power-bi-workspace-collections-relate-to-the-power-bi-service"></a>Power BI ワークスペース コレクションと Power BI サービスはどのように関連しているのですか。

Power BI ワークスペース コレクションと Power BI サービスは別個のサービスです。 Power BI ワークスペース コレクションは、Azure Portal を通じてデプロイされる、使用量ベースの課金モデルを特徴としており、ISV がデータの視覚エフェクトを顧客向けのアプリケーションへ埋め込むことを可能にするよう設計されています。 Power BI サービスは Microsoft 365 管理センターを通して請求およびデプロイが行われ、主に企業による内部使用を対象とするスタンドアロンの汎用 BI サービスです。 Power BI サービスの詳細については、 [https://powerbi.microsoft.com/ja-jp/](https://powerbi.microsoft.com)をご覧ください。

## <a name="how-does-power-bi-workspace-collections-improve-my-app"></a>Power BI ワークスペース コレクションを使用すると、アプリケーションがどのように改善されるのですか。

魅力的な対話型のデータ視覚エフェクトを活用して、アプリケーション内でユーザーに決定事項を直接通知できれば、アプリケーションは大幅に強化されます。 Power BI ワークスペース コレクションを使用することで、対話形式の、常に最新かつ豊富なデータの視覚エフェクトを使用してアプリケーションを強化できます。これにより、アプリの有用性、ユーザーの満足度とロイヤルティを向上させ、容易に任意のデバイスへコンテキスト分析機能を提供します。

## <a name="are-there-any-rules-or-restrictions-about-how-i-can-use-power-bi-workspace-collections-in-my-app"></a>アプリケーションで Power BI ワークスペース コレクションを使用する際の規則や制限はありますか。

Power BI ワークスペース コレクションは、サード パーティ向けに提供されるアプリケーションを対象としています。 Power BI ワークスペース コレクション サービスを使用して社内のビジネス アプリケーションを作成する場合は、各内部ユーザーに Power BI Pro の USL が必要であり、組織には Power BI Pro の USL の料金に加え、Power BI ワークスペース コレクション サービスの使用分が課金されます。 内部アプリケーションに対して Power BI Pro の USL の料金と Power BI ワークスペース コレクションの使用コストの両方が発生するのを防ぐために、Power BI サービスには、Power BI の USL の所有者に追加コストがかからない Power BI ワークスペース コレクション外部の独自のコンテンツ埋め込み機能が用意されています (dev.powerbi.com)。

## <a name="can-power-bi-workspace-collections-be-used-to-create-internal-applications"></a>Power BI ワークスペース コレクションを使って内部アプリケーションを作成することはできますか。

いいえ。Power BI ワークスペース コレクションは外部ユーザーのみを対象としており、社内のビジネス アプリケーション内では使用できません。 社内のビジネス アプリケーションで使用する Power BI コンテンツを埋め込むには、Power BI サービスを使用し、そのコンテンツを使用するすべてのユーザーが、有効な Power BI Free または Power BI Pro のユーザー サブスクリプション ライセンスを保有している必要があります。 社内アプリケーションを Power BI サービスと統合する方法の詳細については、[https://dev.powerbi.com](https://dev.powerbi.com) をご覧ください。

## <a name="is-this-service-available-globally"></a>このサービスは世界中で使用できますか?

Power BI ワークスペース コレクション サービスは、ほとんどのデータ センターで提供されるようになりました。 最新の提供状況については、 [こちら](https://azure.microsoft.com/status/)で常に確認できます。

## <a name="what-is-the-available-sla-for-the-service"></a>サービスで利用可能な SLA は何ですか。

Power BI ワークスペース コレクションには、Azure 標準 SLA が適用されます。 詳細については、「 [サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/) 」をご覧ください。

## <a name="what-is-a-report-session-and-how-is-it-billed"></a>レポート セッションとは何ですか。また、どのように課金されますか。

セッションとは、エンド ユーザーと Power BI ワークスペース コレクション レポートとの一連のやり取りです。 Power BI ワークスペース コレクション レポートがユーザーに表示されるたびに、セッションが開始され、サブスクリプション所有者にセッション分が課金されます。 セッションは、レポート内の視覚要素の数やレポート コンテンツの更新頻度に関係なく、均一料金で請求されます。 セッションは、ユーザーがレポートを閉じたときか、1 時間後にセッションがタイムアウトになったときに終了します。

## <a name="do-you-offer-any-tools-or-guidance-to-help-me-estimate-how-many-renderssession-i-should-expect-how-will-i-know-how-many-renders-have-been-completed"></a>レンダー数やセッション数の見積もりに役立つツールまたはガイダンスは提供されていますか。 また、完了したレンダーの数を確認するにはどうすればよいですか。

Azure Portal では、サブスクリプションに対して実行されたレンダー/レポート セッションの数についての課金の詳細が提供されます。

## <a name="do-i-need-a-power-bi-subscription-in-order-to-develop-applications-with-power-bi-workspace-collections-how-do-i-get-started"></a>Power BI ワークスペース コレクションを使用してアプリケーションを開発するには、Power BI サブスクリプションが必要ですか。 開始するには?

アプリケーション開発者は、アプリケーションで使用するレポートや視覚エフェクトを作成するために、Power BI サブスクリプションを所有している必要はありません。 Microsoft Azure サブスクリプションと無料の Power BI Desktop アプリケーションが必要になります。

Power BI ワークスペース コレクション サービスの使用方法について詳しくは、サービス ドキュメントをご覧ください。

## <a name="i-have-an-azure-subscription-can-i-use-power-bi-workspace-collections-using-my-existing-subscription"></a>Azure サブスクリプションを所有しています。 既存のサブスクリプションを使用して Power BI ワークスペース コレクションを使用できますか。

はい。 既存の Azure サブスクリプションを使用して、Microsoft Power BI ワークスペース コレクション サービスをプロビジョニングし、使用することができます。

## <a name="does-my-application-end-user-need-a-power-bi-license"></a>アプリケーションのエンドユーザーに Power BI ライセンスは必要ですか。

いいえ。 アプリケーションのエンドユーザーは、アプリ内のデータ視覚エフェクトを利用するために、Power BI サブスクリプションを別途購入する必要はありません。 Power BI ワークスペース コレクション モデルでは、Azure の従量課金メーターを使用して、アプリケーション プロバイダーにサービス料金が請求されます。 詳細については、[料金とライセンス](https://go.microsoft.com/fwlink/?LinkId=760527)に関するページをご覧ください。

## <a name="how-does-user-authentication-work-with-power-bi-workspace-collections"></a>Power BI ワークスペース コレクションでのユーザー認証の仕組みを教えてください。

Power BI ワークスペース コレクション サービスでは、明示的なエンドユーザー認証ではなく、アプリケーション トークンを使用して認証と承認を行います。 アプリケーション トークン モデルでは、アプリケーションがエンド ユーザーの認証と承認を管理します。 次に、アプリケーションは、必要に応じて

アプリケーション トークンを作成して送信し、要求されたレポートをレンダリングするようサービスに伝えます。 この設計では、アプリケーションでユーザーの認証と承認に Azure AD を使用する必要はありません (ただし、Azure AD を使用することもできます)。 アプリケーション トークンの詳細については、 [こちら](app-token-flow.md)をご覧ください。 Power BI ワークスペース コレクションでは、高度なセキュリティ フィルター処理シナリオに対応するために、行レベルのセキュリティ機能 (RLS) も導入されました。

## <a name="what-data-sources-are-currently-supported-with-power-bi-workspace-collections"></a>現在、Power BI ワークスペース コレクションでサポートされているデータ ソースを教えてください。

基本的な資格情報を使用するクラウド データ ソースへの直接クエリによるアクセスがサポートされます。 これは、Azure SQL DB や Azure SQL DW などのソースが現在サポートされていることを意味します。 今後数か月で、その他のデータ ソースのサポートおよびアクセスの種類を追加します。 詳細については、「[データ ソースへの接続](connect-datasource.md)」を参照してください。

## <a name="how-does-the-tenancy-model-work-for-power-bi-workspace-collections"></a>Power BI ワークスペース コレクションのテナント モデルの仕組みを教えてください。

Power BI ワークスペース コレクション モデルでは、Azure AD テナントを顧客向けに使用するための明示的な要件はありません。 顧客向けに Azure AD を提供するかどうか選択することができます。 結果として、アプリケーションとインフラストラクチャのアーキテクチャによって、Power BI ワークスペース コレクションでテナント モデルが必要となるかが決定されます。

アプリケーションの開発に取り組んでいる開発者や従業員は、Azure Portal を使用して Azure サブスクリプションとワークスペース コレクションを管理する場合に、AAD ユーザー アカウントが必要になります。 プログラムの API を使用すると、開発者はレポートをインポートし、接続文字列を変更することができます。また、代わりに、埋め込み用 URL を取得し、認証にアプリケーション トークンを利用することもできます。そのため、AAD は不要になります。

## <a name="where-can-i-learn-more"></a>詳細情報を得るには?

[Power BI ワークスペース コレクションのドキュメント ページ](get-started.md)をご覧ください。 このサービスの最新情報については、[Power BI のブログ](https://powerbi.microsoft.com/blog/)または Power BI デベロッパー センター (dev.powerbi.com) をご覧ください。 また、[Stack Overflow](https://stackoverflow.com/questions/tagged/powerbi) で質問することもできます。

## <a name="how-do-i-get-started"></a>開始するには?

今すぐ無料で開始できます。 Azure サブスクリプションを所有している場合は、Azure Portal から直接 Power BI ワークスペース コレクションをプロビジョニングできます。 また、[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成することも可能です。 Power BI ワークスペース コレクション サービスをプロビジョニングしたら、簡単に Power BI REST API を直接使用したり、[GitHub](https://go.microsoft.com/fwlink/?LinkID=746472) で提供されている Developer SDK を使用したりできます。 Developer SDK を活用する方法については、サンプルが提供されます。

## <a name="see-also"></a>関連項目

[Microsoft Power BI ワークスペース コレクションとは](what-are-power-bi-workspace-collections.md)
[Microsoft Power BI ワークスペース コレクションの概要](get-started.md)
[サンプルの使用](get-started-sample.md)   
[JavaScript による埋め込みのサンプル](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

ご質問は、 [Power BI コミュニティ](https://community.powerbi.com/)で尋ねてみてください。