---
title: "Azure API Management で最初 API を管理する |Microsoft ドキュメント"
description: "Api を作成し、操作を追加し、API Management の概要について説明します。"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 51b7df8b-1c43-43c6-90c9-0aa24f48206b
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 6e76d1ee08f804637999ef2ebf5d25becf6a0408
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="manage-your-first-api-in-azure-api-management"></a>Azure API Management で最初 API を管理します。
## <a name="overview"></a>の概要
このガイドをすばやく Azure API Management の使用を開始し、最初の API 呼び出しを行う方法を示します。

## <a name="concepts"></a>Azure API Management は何ですか。
Azure API Management を使用して、任意のバックエンドを行い、それに基づいて、いわゆる本格的な API プログラムを起動することができます。

一般的なシナリオは次のとおりです。

* **モバイルのインフラストラクチャをセキュリティで保護する**して API キーを持つ、アクセスをゲーティング、調整、または JWT トークンの検証などの高度なセキュリティ ポリシーを使用してを使用して DOS 攻撃を防止します。
* **ISV パートナー エコシステムを有効にする**開発者ポータルから高速なパートナー オンボーディングを提供することにより、API ファサードされるパートナーの消費量の可能性がない内部実装から切り離すことを構築します。
* **内部の API プログラムを実行している**では、可用性と Api に対する最新の変更に関する通信するために組織の一元的な場所を提供する組織のアカウントに基づいたアクセスのゲーティングすべてに基づいて API ゲートウェイと、バックエンドの間でセキュリティで保護されたチャネル。

システムは、次のコンポーネントで構成されています。

* **API ゲートウェイ**エンドポイントです。
  
  * API を呼び出すし、バックエンドにルーティングするを受け取ります。
  * API キー、JWT トークン、証明書、およびその他の資格情報を確認します。
  * 使用状況クォータを適用し、転送率の制限。
  * コードを変更せず、実行時に、API を変換します。
  * 設定されるバックエンド応答をキャッシュします。
  * ログは、分析のためのメタデータを呼び出します。
* **パブリッシャー ポータル**API プログラムを設定する管理インターフェイスです。 使用します。
  
  * 定義または API のスキーマをインポートします。
  * パッケージ Api の製品です。
  * クォータまたは Api での変換などのポリシーを設定します。
  * 分析からインサイトを取得します。
  * ユーザーを管理します。
* **開発者ポータル**役割を果たす主要な web サイト開発者は、することができます。
  
  * Read API のドキュメントです。
  * 対話型コンソールを使用した API を試します。
  * アカウントを作成し、サブスクライブする API キーを取得します。
  * 独自の使用状況の分析をアクセス。

## <a name="create-service-instance"></a>API Management インスタンスを作成します。
> [!NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。 アカウントを持っていない場合は、ほんの数分で無料のアカウントを作成できます。 詳細については、「 [Azure 無料評価版][Azure Free Trial]です。
> 
> 

API Management の操作の最初の手順では、サービス インスタンスを作成します。 サインイン、 [Azure Portal] [ Azure Portal] ] をクリック**新規**、 **Web + モバイル**、 **API Management**です。

![API Management の新しいインスタンス][api-management-create-instance-menu]

**名前**サービスの URL を使用する一意のサブドメイン名を指定します。

必要な選択**サブスクリプション**、**リソース グループ**と**場所**サービス インスタンスのです。

入力**Contoso Ltd.** **組織名**の電子メール アドレスを入力し、**管理者の電子メール**フィールドです。

> [!NOTE]
> この電子メール アドレスは、API Management システムからの通知に使用されます。 詳細については、次を参照してください。[通知を構成すると、Azure API Management でテンプレートを電子メールで送信する方法][How to configure notifications and email templates in Azure API Management]です。
> 
> 

![新しい API Management サービス][api-management-create-instance-step1]

API Management サービス インスタンスが 3 つの階層で使用できます: Developer、Standard、および Premium です。

> [!NOTE]
> 開発者層では、開発、テスト、およびパイロット API プログラムが高可用性が問題ではないためです。 Standard および Premium 層でより多くのトラフィックを処理する、予約ユニット数を拡張することができます。 Standard および Premium 層は、API Management サービスの処理能力とパフォーマンスを提供します。 このチュートリアルを完了するには、任意の層を使用します。 API Management の層の詳細については、次を参照してください。 [API Management 料金][API Management pricing]です。
> 
> 

をクリックして**作成**サービス インスタンスのプロビジョニングを開始します。

![新しい API Management サービス][api-management-instance-created]

サービス インスタンスが作成されると、次の手順は、作成するか、API をインポートするは。

## <a name="create-api"></a>API をインポート
API は、クライアント アプリケーションから呼び出すことができる操作のセットで構成されます。 API 操作は、既存の web サービスに中継します。

Api を作成することができます (および操作を追加することができます)、手動でインポートできますか。 このチュートリアルでは、Microsoft によって提供され、Azure でホストされているサンプル電卓 web サービスの API をインポートします。

> [!NOTE]
> API を作成し、操作を手動で追加のガイダンスについては、次を参照してください。 [Api を作成する方法](api-management-howto-create-apis.md)と[API に操作を追加する方法](api-management-howto-add-operations.md)です。
> 
> 

Api は、パブリッシャー ポータルから構成されます。 到達できないように、をクリックして**パブリッシャー ポータル**サービスは、ツールバーからです。

![パブリッシャー ポータル][api-management-management-console]

電卓 API をインポートする をクリックして**Api**から、 **API Management** 、左側のメニューをクリックし、**インポート API**です。

![API [インポート] ボタン][api-management-import-api]

計算ツール API を構成する次の手順を実行します。

1. をクリックして**URL から**、入力**http://calcapi.cloudapp.net/calcapi.json**に、**仕様のドキュメントの URL**テキスト ボックス、およびクリック、 **Swagger**ラジオ ボタンをクリックします。
2. 型**calc**に、 **Web API URL サフィックス**テキスト ボックス。
3. をクリックして、 **(省略可) 製品**ボックスし、選択**スターター**です。
4. をクリックして**保存**API をインポートします。

![新しい API を追加します。][api-management-import-new-api]

> [!NOTE]
> **API Management**現在インポートの Swagger ドキュメントの 1.2 および 2.0 の両方のバージョンをサポートしています。 確認して、たとえ[Swagger 2.0 仕様](http://swagger.io/specification)を宣言`host`、 `basePath`、および`schemes`プロパティは省略可能、Swagger 2.0 ドキュメント**必要があります**これらのプロパティが含まれます。 それ以外の場合インポート取得されません。 
> 
> 

API をインポートすると、パブリッシャー ポータルで、API の概要ページが表示されます。

![API の概要][api-management-imported-api-summary]

API のセクションでは、いくつかのタブがあります。 **概要**タブは、基本的なメトリックと API に関する情報が表示されます。 [設定](api-management-howto-create-apis.md#configure-api-settings) タブを表示および編集の構成 API を使用します。 [Operations](api-management-howto-add-operations.md) API の操作の管理 タブを使用します。 **セキュリティ** タブは、基本認証を使用して、バックエンド サーバー用のゲートウェイ認証を構成するために使用できますか[相互証明書の認証](api-management-howto-mutual-certificates.md)、および構成する[OAuth 2.0 を使用してユーザーの承認](api-management-howto-oauth2.md)です。  **問題**を独自の Api を使用している開発者によって報告された問題を表示 タブを使用します。 **製品**をこの API が含まれている製品の構成 タブを使用します。

既定では、各 API Management インスタンスはサンプルの 2 つの製品に付属します。

* **スターター**
* **無制限**

このチュートリアルでは、基本的な電卓 API は、API がインポートされたときに Starter 製品に追加されました。

API への呼び出しを行うために、開発者はそれらにアクセスできるように、製品にサブスクライブ最初にする必要があります。 開発者ポータルでの製品にサブスクライブできます開発者または管理者がサブスクライブできるは、開発者は、パブリッシャー ポータル内の製品をします。 管理者は、作成したため、API Management インスタンス前の手順で、チュートリアルで既に既定ですべての製品にサブスクライブしているためです。

## <a name="call-operation"></a>開発者ポータルから、操作を呼び出す
操作は、表示し、API の動作をテストするための便利な方法を提供する開発者ポータルから直接呼び出すことができます。 このチュートリアルの手順では基本的な電卓 API の呼び出しは**2 つの整数を追加**操作します。 をクリックして**開発者ポータル**上部のメニューから、パブリッシャー ポータルの右。

![開発者ポータル][api-management-developer-portal-menu]

をクリックして**Api**クリックして上部のメニューから**基本的な電卓**に使用可能な操作を参照してください。

![開発者ポータル][api-management-developer-portal-calc-api]

サンプルの説明と、API と操作、により、この操作を使用する開発者向けのドキュメントと共にインポートされたパラメーターに注意してください。 操作が手動で追加されたときに、これらの説明を追加することもできます。

呼び出す、 **2 つの整数を追加**操作をクリックして**お試しください**です。

![お試しください。][api-management-developer-portal-calc-api-console]

パラメーターのいくつかの値を入力または、既定値を保持し、をクリックできます**送信**です。

![HTTP Get][api-management-invoke-get]

開発者ポータルを表示、操作を呼び出した後、**応答ステータス**、**応答ヘッダー**と任意**応答コンテンツ**です。

![[応答]][api-management-invoke-get-response]

## <a name="view-analytics"></a>分析を表示
を表示する分析基本的な電卓を切り替えてパブリッシャー ポータルに戻る を選択して**管理**上部のメニューから、開発者ポータルの右。

![コンピューターの][api-management-manage-menu]

パブリッシャー ポータルの既定のビューは、**ダッシュ ボード**、API Management インスタンスの概要を提供します。

![[ダッシュボード]][api-management-dashboard]

マウス ポインターのグラフ**基本的な電卓**特定期間中に、API の使用方法の特定のメトリックを表示します。

> [!NOTE]
> 場合は、グラフの線が表示されない、開発者ポータルに戻ると、API にいくつかの呼び出しを行う、しばらく待ってから、およびダッシュ ボードに戻ってです。
> 
> 

をクリックして**詳細を表示する**大きなバージョンの表示のメトリックを含む、API の概要 ページを表示します。

![分析][api-management-mouse-over]

![[概要]][api-management-api-summary-metrics]

詳細なメトリックとレポートは、をクリックして**Analytics**から、 **API Management**  をクリックします。

![概要][api-management-analytics-overview]

**Analytics**セクションには次の 4 つのタブ。

* **一目**全体的な使用状況と正常性メトリック、だけでなく最上位の開発者、上位の製品、最上位の Api、top 操作を提供します。
* **使用状況**API 呼び出しと帯域幅、地理的な表現を含むで詳しく説明します。
* **正常性**焦点を当てていますステータス コードのキャッシュの成功率、応答時間、および API とサービスの応答時間。
* **アクティビティ**開発者、製品、API、および操作で特定のアクティビティにドリル ダウンするレポートを提供します。

## <a name="next-steps"></a>次の手順
* 学習方法[転送率の制限と API を保護する](api-management-howto-product-with-rules.md)です。

[Azure Free Trial]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=api_management_hero_a

[Create an API Management instance]: #create-service-instance
[Create an API]: #create-api
[Add an operation]: #add-operation
[Add the new API to a product]: #add-api-to-product
[Subscribe to the product that contains the API]: #subscribe
[Call an operation from the Developer Portal]: #call-operation
[View analytics]: #view-analytics
[Next steps]: #next-steps


[How to manage developer accounts in Azure API Management]: api-management-howto-create-or-invite-developers.md
[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[How to configure notifications and email templates in Azure API Management]: api-management-howto-configure-notifications.md
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md
[API Management pricing]: http://azure.microsoft.com/pricing/details/api-management/

[Azure Portal]: https://portal.azure.com/

[api-management-management-console]: ./media/api-management-get-started/api-management-management-console.png
[api-management-create-instance-menu]: ./media/api-management-get-started/api-management-create-instance-menu.png
[api-management-create-instance-step1]: ./media/api-management-get-started/api-management-create-instance-step1.png
[api-management-create-instance-step2]: ./media/api-management-get-started/api-management-create-instance-step2.png
[api-management-instance-created]: ./media/api-management-get-started/api-management-instance-created.png
[api-management-import-api]: ./media/api-management-get-started/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-get-started/api-management-import-new-api.png
[api-management-imported-api-summary]: ./media/api-management-get-started/api-management-imported-api-summary.png
[api-management-calc-operations]: ./media/api-management-get-started/api-management-calc-operations.png
[api-management-list-products]: ./media/api-management-get-started/api-management-list-products.png
[api-management-add-api-to-product]: ./media/api-management-get-started/api-management-add-api-to-product.png
[api-management-add-myechoapi-to-product]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
[api-management-api-added-to-product]: ./media/api-management-get-started/api-management-api-added-to-product.png
[api-management-developers]: ./media/api-management-get-started/api-management-developers.png
[api-management-add-subscription]: ./media/api-management-get-started/api-management-add-subscription.png
[api-management-add-subscription-window]: ./media/api-management-get-started/api-management-add-subscription-window.png
[api-management-subscription-added]: ./media/api-management-get-started/api-management-subscription-added.png
[api-management-developer-portal-menu]: ./media/api-management-get-started/api-management-developer-portal-menu.png
[api-management-developer-portal-calc-api]: ./media/api-management-get-started/api-management-developer-portal-calc-api.png
[api-management-developer-portal-calc-api-console]: ./media/api-management-get-started/api-management-developer-portal-calc-api-console.png
[api-management-invoke-get]: ./media/api-management-get-started/api-management-invoke-get.png
[api-management-invoke-get-response]: ./media/api-management-get-started/api-management-invoke-get-response.png
[api-management-manage-menu]: ./media/api-management-get-started/api-management-manage-menu.png
[api-management-dashboard]: ./media/api-management-get-started/api-management-dashboard.png

[api-management-add-response]: ./media/api-management-get-started/api-management-add-response.png
[api-management-add-response-window]: ./media/api-management-get-started/api-management-add-response-window.png
[api-management-developer-key]: ./media/api-management-get-started/api-management-developer-key.png
[api-management-mouse-over]: ./media/api-management-get-started/api-management-mouse-over.png
[api-management-api-summary-metrics]: ./media/api-management-get-started/api-management-api-summary-metrics.png
[api-management-analytics-overview]: ./media/api-management-get-started/api-management-analytics-overview.png
[api-management-analytics-usage]: ./media/api-management-get-started/api-management-analytics-usage.png
[api-management-]: ./media/api-management-get-started/api-management-.png
[api-management-]: ./media/api-management-get-started/api-management-.png
