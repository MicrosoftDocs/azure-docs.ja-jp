---
title: "Microsoft Power BI Embedded とは何ですか?"
description: "Power BI Embedded を利用すると、Web アプリケーションやモバイル アプリケーションに Power BI レポートを統合できるため、カスタム ソリューションを作成する必要はありません。"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 03649b72-b7d7-40ca-b077-12356d72d4f3
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 03/20/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 0b5f7a2c3fd16ac32b0bc382616ca6600d378bb8
ms.lasthandoff: 03/21/2017


---
# <a name="what-is-microsoft-power-bi-embedded"></a>Microsoft Power BI Embedded とは何ですか?
**Power BI Embedded**を使用すると、Web アプリケーションやモバイル アプリケーションに Power BI レポートを統合することができます。

![](media/powerbi-embedded-whats-is/what-is.png)

**Power BI Embedded** は、ISV やアプリケーション開発者がアプリケーション内で Power BI データ エクスペリエンスを実現することを可能にする Azure サービスです。 開発者が作成したアプリケーションは、それぞれにユーザーが存在し、異なる機能セットを備えています。 これらのアプリケーションには、Microsoft Power BI Embedded に対応できるようになったグラフやレポートなどのデータ要素が組み込まれていることもあります。 Power BI アカウントがなくても、アプリケーションを使用できます。 これまでと同様にアプリケーションにサインインし、Power BI のレポートを表示して操作することができます。追加のライセンスは不要です。

## <a name="licensing-for-microsoft-power-bi-embedded"></a>Microsoft Power BI Embedded のライセンス
**Microsoft Power BI Embedded** の使用モデルでは、Power BI のライセンスはエンドユーザーの責任ではありません。  代わりに、ビジュアルを使用するアプリケーションの開発者が**セッション**を購入します。レンダーの料金は、それらのリソースを所有するサブスクリプションに課金されます。 追加の情報については、[価格](https://azure.microsoft.com/en-us/pricing/details/power-bi-embedded/)に関するページをご覧ください。

## <a name="microsoft-power-bi-embedded-conceptual-model"></a>Microsoft Power BI Embedded の概念モデル

![](media/powerbi-embedded-whats-is/model.png)

Azure の他のサービスと同様に、[Azure Resource Manager API](https://msdn.microsoft.com/library/mt712306.aspx) を使用して Power BI Embedded のリソースがプロビジョニングされます。 この場合、プロビジョニング対象のリソースは **Power BI ワークスペース コレクション**です。

## <a name="workspace-collection"></a>ワークスペース コレクション
**ワークスペース コレクション**は、0 個以上の**ワークスペース**が含まれた、リソースの最上位 Azure コンテナーです。  **ワークスペース****コレクション**には、すべての標準 Azure プロパティだけでなく、次の要素があります。

* **アクセス キー** – Power BI API を安全に呼び出すときに使用されるキーです (後述)。
* **ユーザー** – Azure Portal または Azure Resource Manager API を使用して Power BI ワークスペース コレクションを管理する管理者権限を持つ Azure Active Directory (AAD) ユーザーです。
* **リージョン** – **ワークスペース コレクション**のプロビジョニングの一環として、プロビジョニング先のリージョンを選択できます。 詳細については、「 [Azure のリージョン](https://azure.microsoft.com/regions/)」をご覧ください。

## <a name="workspace"></a>ワークスペース
**ワークスペース**は、Power BI コンテンツのコンテナーであり、データセットとレポートを含めることができます。 **ワークスペース** は、最初に作成された時点では空です。 Power BI Desktop を使用してコンテンツを作成し、[Power BI Import API](https://msdn.microsoft.com/library/mt711504.aspx) を使用して、PBIX をワークスペースにプログラムでデプロイします。 また、Power BI Desktop を使用する代わりに、プログラムでデータセットを作成し、アプリケーション内でレポートを作成することもできます。

## <a name="using-workspace-collections-and-workspaces"></a>ワークスペース コレクションとワークスペースの使用
**ワークスペース コレクション**と**ワークスペース**は、作成するアプリケーションの設計に最適な方法で使用、構成されるコンテンツのコンテナーです。 ワークスペース コレクションとワークスペースの中では、多様な方法でコンテンツを構成できます。 1 つのワークスペースにすべてのコンテンツを配置してから、後でアプリ トークンを使用して、顧客の間でコンテンツをさらに細かく分割することができます。 顧客が分離されるように、複数の個別ワークスペースにすべての顧客を配置することもできます。 または、顧客別ではなくリージョン別にユーザーを構成することもできます。 この柔軟な設計により、コンテンツの最適な構成方法を選択できます。

## <a name="cached-datasets"></a>キャッシュされたデータセット
キャッシュされたデータセットを使用できます。  ただし、キャッシュされたデータは、 **Microsoft Power BI Embedded**への読み込み後に更新することはできません。 キャッシュされたデータセットとは、DirectQuery を使用する代わりに Power BI Desktop にデータをインポートしたことを意味します。

## <a name="authentication-and-authorization-with-app-tokens"></a>アプリ トークンを使用した認証と承認
**Microsoft Power BI Embedded** では、必要なユーザーの認証と承認の実行をすべてアプリケーションに委ねます。 エンド ユーザーは Azure Active Directory (Azure AD) の顧客でなければならないという明示的な要件はありません。  代わりに、アプリケーションが**アプリケーション認証トークン (アプリ トークン)** を使用して、Power BI レポートのレンダリングの承認を **Microsoft Power BI Embedded** に伝えます。  これらの **アプリ トークン** は、アプリケーションがレポートをレンダリングするときに必要に応じて生成されます。

![](media/powerbi-embedded-whats-is/app-tokens.png)

**アプリケーション認証トークン (アプリ トークン)** は、**Microsoft Power BI Embedded** に対する認証に使用されます。  **アプリ トークン**には、次の 3 種類があります。

1. プロビジョニング用トークン - **ワークスペース コレクション**に新しい**ワークスペース**をプロビジョニングするときに使用されます。
2. 開発用トークン - **Power BI REST API**
3. 埋め込み用トークン - 埋め込まれた iframe でのレポートのレンダリングのために呼び出しを行うときに使用されます。

これらのトークンは、 **Microsoft Power BI Embedded**とのやり取りのさまざまなフェーズに使用されます。  トークンは、許可をアプリから Power BI に委任できるように設計されています。 詳細については、 [アプリ トークンのフロー](power-bi-embedded-app-token-flow.md)に関するページを参照してください。

## <a name="create-or-edit-reports-within-your-application"></a>アプリケーション内でのレポートの作成または編集

Power BI Desktop を使用せずに、既存のレポートを編集したり、アプリケーションで直接新しいレポートを作成したりできるようになりました。 そのためには、ワークスペース内にデータセットが存在する必要があります。

## <a name="see-also"></a>関連項目

[Common Microsoft Power BI Embedded scenarios (Microsoft Power BI Embedded の一般的なシナリオ)](power-bi-embedded-scenarios.md)  
[Microsoft Power BI Embedded の概要](power-bi-embedded-get-started.md)  
[Get started with Microsoft Power BI Embedded sample (Microsoft Power BI Embedded のサンプルの使用)](power-bi-embedded-get-started-sample.md)  
[レポートの埋め込み](power-bi-embedded-embed-report.md)  
[Power BI Embedded での認証と承認](power-bi-embedded-app-token-flow.md)  
[JavaScript による埋め込みのサンプル](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[PowerBI-CSharp Git リポジトリ](https://github.com/Microsoft/PowerBI-CSharp)  
[PowerBI-Node Git リポジトリ](https://github.com/Microsoft/PowerBI-Node)  
ご質問は、 [Power BI コミュニティ](http://community.powerbi.com/)で尋ねてみてください。

