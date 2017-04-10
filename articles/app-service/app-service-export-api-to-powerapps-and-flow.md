---
title: "Azure でホストされる API を PowerApps と Microsoft Flow にエクスポートする | Microsoft Docs"
description: "App Service でホストされる API を PowerApps および Microsoft Flow に公開する方法の概要について説明します"
services: app-service
documentationcenter: 
author: mattchenderson
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/06/2017
ms.author: mahender
translationtype: Human Translation
ms.sourcegitcommit: 060fbb885f254c0de02fe422460e8e8d659ac848
ms.openlocfilehash: 503f17a629527e8c9a3bfe6cde1da31dcf1f18ef


---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Azure でホストされる API を PowerApps と Microsoft Flow にエクスポートする

## <a name="creating-custom-apis-for-powerapps-and-microsoft-flow"></a>PowerApps および Microsoft Flow 用のカスタム API の作成

[Microsoft ビジネス アプリケーション プラットフォーム](https://businessplatform.microsoft.com/)には、パワー ユーザーを支援するさまざまな製品が含まれます。 [PowerApps](https://powerapps.com) は、ユーザーのデータに接続して異なるプラットフォーム間で動作するカスタム ビジネス アプリを作成して使うためのサービスです。 [Microsoft Flow](https://flow.microsoft.com) は、ユーザーが好むアプリとサービスの間でワークフローとビジネス プロセスを簡単に自動化できるようにします。 PowerApps と Microsoft Flow のどちらにも、Office 365、Dynamics 365、Salesforce などのデータ ソースへのさまざまな組み込みコネクタが付属しています。 ただし、組織で作成されたデータ ソースと API も利用できる必要があります。

同様に、組織内での独自 API の利用範囲を広げるには、PowerApps や Microsoft Flow のユーザーも API を使用できるようにする必要があります。 このトピックでは、Azure App Service または Azure Functions で作成された API を PowerApps および Microsoft Flow に公開する方法について説明します。 サービスとしてのプラットフォームである [Azure App Service](https://azure.microsoft.com/services/app-service/) を利用することで、開発者はエンタープライズ レベルの Web、モバイル、API アプリケーションを迅速かつ簡単に構築できます。 [Azure Functions](https://azure.microsoft.com/services/functions/) はイベント ベースのサーバーレス コンピューティング ソリューションであり、システムの他の部分に対応でき、必要に応じてスケーリングできるコードを、短時間で作成できます。

これらのサービスについて詳しくは、以下の情報をご覧ください。
- [PowerApps ガイド学習](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) 
- [Microsoft Flow ガイド学習](https://flow.microsoft.com/guided-learning/learning-introducing-flow/)
- [App Service とは](https://docs.microsoft.com/azure/app-service/app-service-value-prop-what-is)
- [Azure Functions とは](https://docs.microsoft.com/azure/azure-functions/functions-overview)

## <a name="sharing-an-api-definition"></a>API 定義を共有する

API は、多くの場合、[Open API ドキュメント](https://www.openapis.org/) ("Swagger" ドキュメントと呼ばれることもあります) を使って記述されます。 これには、使用可能な操作とデータの構成方法に関するすべての情報が含まれます。 PowerApps と Microsoft Flow では、任意の Open API 2.0 ドキュメントに対応するカスタム API を作成できます。 作成したカスタム API は、組み込みコネクタとまったく同じように使用でき、アプリケーションに簡単に統合できます。

Azure App Service と Azure Functions には、Open API ドキュメントを作成、ホスト、管理するための[サポートが組み込まれて](https://docs.microsoft.com/azure/app-service-api/app-service-api-metadata)ます。 Web、モバイル、API、関数のアプリに対するカスタム コネクタを作成するには、2 つの手順が必要になります。

1. [App Service または Azure Functions から API 定義を取得する](#export)
2. [PowerApps に API 定義をインポートする](#import)

これら&2; つの作業を実行するためのアクセス許可を両方とも持つユーザーがいないために、2 つの手順を組織内の別のユーザーが行うことが必要になる場合があります。 そのような場合は、App Service アプリケーションまたは Azure Functions アプリケーションへの共同作成者アクセス権がある開発者が、API 定義 (1 つの JSON ファイル) または API 定義へのリンクを取得する必要があります。 そして、その定義を PowerApps または Microsoft Flow の所有者に提供する必要があります。 その所有者は、メタデータを使ってカスタム API を作成できます。

> [!NOTE]
> API 定義のコピーが使われるため、PowerApps と Microsoft Flow は、アプリケーションの更新や重大な変更についてすぐに知ることができません。 新しいバージョンの API が使用可能になった場合は、新しいバージョンに対してこの手順を繰り返す必要があります。 

<a name="export"></a>
## <a name="retrieving-the-api-definition-from-app-service-or-azure-functions"></a>App Service または Azure Functions から API 定義を取得する

このセクションでは、後ほど PowerApps で使用できるように、App Service API の API 定義をエクスポートします。

1. [Azure Portal](https://portal.azure.com) を開き、App Service アプリケーションまたは Azure Functions アプリケーションに移動します。

    Azure App Service を使う場合は、設定一覧の **[API 定義]** を選びます。 
    
    Azure Functions を使う場合は、**[Function App の設定]** を選び、次に **[API メタデータの構成]** を選びます。

2. API 定義が提供されている場合は、**[Export to PowerApps + Microsoft Flow (PowerApps + Microsoft Flow にエクスポート)]** ボタンが表示されます。 このボタンをクリックして、エクスポート プロセスを開始します。

3. **[API 定義のダウンロード]** または **[リンクの取得]** を選ぶことができます。 どちらを選んでも、次のセクションで結果が PowerApps に提供されます。 どちらかのオプションを選んで指示に従います。
 
4. API 定義にセキュリティ定義が含まれる場合は、手順 2 で適用されます。 インポート中に、PowerApps と Microsoft Flow はセキュリティ定義を検出し、セキュリティ情報を要求します。 ユーザーが API にアクセスできるように、サービスはこの情報を使ってユーザーをログインさせます。 API に認証が必要な場合は、Open API ドキュメントで_セキュリティ情報_としてキャプチャされるようにします。

    次のセクションで使うために、各定義に関連する資格情報を収集します。 PowerApps がネイティブにサポートする ID プロバイダーの一覧と、それぞれで必要な資格情報については、「[Register custom APIs in PowerApps]」(PowerApps にカスタム API を登録する) および「[Microsoft Flow にカスタム API を登録する]」をご覧ください。
 
> [!NOTE]
> Azure Active Directory 認証を使っている場合は、作成している API と応答 URL (_https://msmanaged-na.consent.azure-apim.net/redirect_) への委任されたアクセス権を持つ新しい AAD アプリの登録が必要になります。 詳しくは、[こちらの例](
https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/)をご覧ください。Azure Resource Manager に対する API は実際のものに置き換えてください。
>
> 別のユーザーが API 定義を PowerApps にインポートする場合は、API 定義ファイルに加えて、**新しい登録**のクライアント ID とクライアント シークレット、および API のリソース URL を提供します。 これらのシークレットが安全に管理されていることを確認します。 **API 自体のセキュリティ資格情報は共有しないでください。**

<a name="import"></a>
## <a name="importing-the-api-definition-into-powerapps-and-microsoft-flow"></a>PowerApps と Microsoft Flow に API 定義をインポートする

このセクションでは、先に取得した API 定義を使って、PowerApps と Microsoft Flow でカスタム API を作成します。 カスタム API は&2; つのサービスの間で共有されるので、定義をインポートする必要があるのは&1; 回だけです。 カスタム API について詳しくは、「[Register custom APIs in PowerApps]」(PowerApps にカスタム API を登録する) および「[Microsoft Flow にカスタム API を登録する]」をご覧ください。

**PowerApps にインポートするには:**

1. [PowerApps Web ポータル](https://web.powerapps.com)を開き、サインインして、**[接続]** を選びます。 **[新しい接続]** をクリックします。

2. **[カスタム]** を選び、**[New custom API (新しいカスタム API)]** をクリックします。

3. API の名前を指定し、Swagger 定義をアップロードするか、メタデータの URL に貼り付けます。 **[次へ]**をクリックします。

4. 認証の詳細の指定を求めるメッセージが表示されたら、前のセクションで取得した値を入力します。 表示されない場合は、次の手順に進みます。

5. **[作成]**をクリックします。

**Microsoft Flow にインポートするには:**

1. [Microsoft Flow Web ポータル](https://flow.microsoft.com/)を開いてサインインします。 

2. ページの右上にある **[設定]** ボタン (歯車の形) をクリックし、**[カスタム API]** を選びます。 **[カスタム API の作成]** をクリックします。

3. Swagger 定義をアップロードし、**[続行]** をクリックします。

4. 認証の詳細の指定を求めるメッセージが表示されたら、前のセクションで取得した値を入力します。 表示されない場合は、次の手順に進みます。

5. 画面上部にあるチェック ボックスをオンにします。



[Register custom APIs in PowerApps]: https://powerapps.microsoft.com/tutorials/register-custom-api/
[Microsoft Flow にカスタム API を登録する]: https://flow.microsoft.com/documentation/register-custom-api/



<!--HONumber=Feb17_HO2-->


