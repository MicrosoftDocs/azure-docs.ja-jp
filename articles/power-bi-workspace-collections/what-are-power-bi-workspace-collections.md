---
title: Power BI ワークスペース コレクションとは
description: Power BI Embedded を利用すると、Web アプリケーションやモバイル アプリケーションに Power BI レポートを統合できるため、カスタム ソリューションを作成する必要はありません。
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: 03649b72-b7d7-40ca-b077-12356d72d4f3
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 6650eed6bf2559e9fb76b573189179a7f5df81ca
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57896706"
---
# <a name="what-are-power-bi-workspace-collections"></a>Power BI ワークスペース コレクションとは

**Power BI ワークスペース コレクション**を使用すると、Web アプリケーションやモバイル アプリケーションに Power BI レポートをすぐに統合することができます。

![アプリケーション ダイアグラム](media/what-are-power-bi-workspace-collections/what-is.png)

> [!IMPORTANT]
> Power BI ワークスペース コレクションは非推奨となっており、2018 年 6 月または契約に定める日までに限り利用できます。 アプリケーションの中断を避けるため、Power BI Embedded への移行をご検討ください。 Power BI Embedded にデータを移行する方法については、[Power BI ワークスペース コレクション コンテンツを Power BI Embedded に移行する方法](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)に関するページを参照してください。

Power BI ワークスペース コレクションは、ISV やアプリ開発者がアプリケーション内で Power BI データ エクスペリエンスを実現することを可能にする **Azure サービス**です。 開発者が作成したアプリケーションは、それぞれにユーザーが存在し、異なる機能セットを備えています。 これらのアプリケーションには、Microsoft Power BI ワークスペース コレクションに対応できるようになったグラフやレポートなどのデータ要素が組み込まれていることもあります。 Power BI アカウントがなくても、アプリケーションを使用できます。 これまでと同様にアプリケーションにサインインし、Power BI のレポートを表示して操作することができます。追加のライセンスは不要です。

## <a name="licensing-for-microsoft-power-bi-workspace-collections"></a>Microsoft Power BI ワークスペース コレクションのライセンス

**Microsoft Power BI ワークスペース コレクション**の使用モデルでは、Power BI のライセンスはエンド ユーザーの責任ではありません。  代わりに、ビジュアルを使用するアプリケーションの開発者が**セッション**を購入します。レンダーの料金は、それらのリソースを所有するサブスクリプションに課金されます。 

## <a name="microsoft-power-bi-workspace-collections-conceptual-model"></a>Microsoft Power BI ワークスペース コレクションの概念モデル

![ワークスペース コレクションによるアプリケーション フロー](media/what-are-power-bi-workspace-collections/model.png)

Azure の他のサービスと同様に、[Azure Resource Manager API](https://msdn.microsoft.com/library/mt712306.aspx) を使用して Power BI ワークスペース コレクションのリソースがプロビジョニングされます。 この場合、プロビジョニング対象のリソースは **Power BI ワークスペース コレクション**です。

## <a name="workspace-collection"></a>ワークスペース コレクション

**ワークスペース コレクション**は、0 個以上の**ワークスペース**が含まれた、リソースの最上位 Azure コンテナーです。  **ワークスペース** **コレクション**には、すべての標準 Azure プロパティだけでなく、次の要素があります。

* **アクセス キー** – Power BI API を安全に呼び出すときに使用されるキーです (後述)。
* **ユーザー** – Azure Portal または Azure Resource Manager API を使用して Power BI ワークスペース コレクションを管理する管理者権限を持つ Azure Active Directory (AAD) ユーザーです。
* **リージョン** – **ワークスペース コレクション**のプロビジョニングの一環として、プロビジョニング先のリージョンを選択できます。 詳細については、「 [Azure のリージョン](https://azure.microsoft.com/regions/)」をご覧ください。

## <a name="workspace"></a>ワークスペース

**ワークスペース**は、Power BI コンテンツのコンテナーであり、データセットとレポートを含めることができます。 **ワークスペース** は、最初に作成された時点では空です。 Power BI Desktop を使用してコンテンツを作成し、[Power BI Import API](https://msdn.microsoft.com/library/mt711504.aspx) を使用して、PBIX をワークスペースにプログラムでデプロイします。 また、Power BI Desktop を使用する代わりに、プログラムでデータセットを作成し、アプリケーション内でレポートを作成することもできます。

## <a name="using-workspace-collections-and-workspaces"></a>ワークスペース コレクションとワークスペースの使用

**ワークスペース コレクション**と**ワークスペース**は、作成するアプリケーションの設計に最適な方法で使用、構成されるコンテンツのコンテナーです。 ワークスペース コレクションとワークスペースの中では、多様な方法でコンテンツを構成できます。 1 つのワークスペースにすべてのコンテンツを配置してから、後でアプリ トークンを使用して、顧客の間でコンテンツをさらに細かく分割することができます。 顧客が分離されるように、複数の個別ワークスペースにすべての顧客を配置することもできます。 または、顧客別ではなくリージョン別にユーザーを構成することもできます。 この柔軟な設計により、コンテンツの最適な構成方法を選択できます。

## <a name="cached-datasets"></a>キャッシュされたデータセット

キャッシュされたデータセットを使用できます。  ただし、キャッシュされたデータは、**Microsoft Power BI ワークスペース コレクション**への読み込み後に更新することはできません。 キャッシュされたデータセットとは、DirectQuery を使用する代わりに Power BI Desktop にデータをインポートしたことを意味します。

## <a name="authentication-and-authorization-with-app-tokens"></a>アプリ トークンを使用した認証と承認

**Microsoft Power BI ワークスペース コレクション**では、必要なユーザーの認証と承認の実行をすべてアプリケーションに委ねます。 エンド ユーザーは Azure Active Directory (Azure AD) の顧客でなければならないという明示的な要件はありません。  代わりに、アプリケーションが**アプリケーション認証トークン (アプリ トークン)** を使用して、Power BI レポートのレンダリングの承認を **Microsoft Power BI ワークスペース コレクション**に伝えます。  これらの **アプリ トークン** は、アプリケーションがレポートをレンダリングするときに必要に応じて生成されます。

![アプリ トークンの使用ダイアグラム](media/what-are-power-bi-workspace-collections/app-tokens.png)

**アプリケーション認証トークン (アプリ トークン)** は、**Microsoft Power BI ワークスペース コレクション**に対する認証に使用されます。  **アプリ トークン**には、次の 3 種類があります。

1. プロビジョニング用トークン - **ワークスペース コレクション**に新しい**ワークスペース**をプロビジョニングするときに使用されます。
2. 開発用トークン - **Power BI REST API**
3. 埋め込み用トークン - 埋め込まれた iframe でのレポートのレンダリングのために呼び出しを行うときに使用されます。

これらのトークンは、**Microsoft Power BI ワークスペース コレクション**とのやり取りのさまざまなフェーズに使用されます。  トークンは、許可をアプリから Power BI に委任できるように設計されています。 詳細については、 [アプリ トークンのフロー](app-token-flow.md)に関するページを参照してください。

## <a name="create-or-edit-reports-within-your-application"></a>アプリケーション内でのレポートの作成または編集

Power BI Desktop を使用せずに、既存のレポートを編集したり、アプリケーションで直接新しいレポートを作成したりできるようになりました。 そのためには、ワークスペース内にデータセットが存在する必要があります。

## <a name="see-also"></a>関連項目

[Microsoft Power BI ワークスペース コレクションの一般的なシナリオ](scenarios.md)  
[Microsoft Power BI ワークスペース コレクションの概要](get-started.md)  
[Get started with Microsoft Power BI Embedded sample (Microsoft Power BI Embedded のサンプルの使用)](get-started-sample.md)  
[レポートの埋め込み](embed-report.md)  
[Power BI ワークスペース コレクションでの認証と承認](app-token-flow.md)  
[JavaScript による埋め込みのサンプル](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[PowerBI-CSharp Git リポジトリ](https://github.com/Microsoft/PowerBI-CSharp)  
[PowerBI-Node Git リポジトリ](https://github.com/Microsoft/PowerBI-Node)  

ご質問は、 [Power BI コミュニティ](https://community.powerbi.com/)で尋ねてみてください。
