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
ms.date: 06/20/2017
ms.author: mahender
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 0d166a2e5b60c3b9f911f9fc3ad49ad7f252abb4
ms.contentlocale: ja-jp
ms.lasthandoff: 07/01/2017


---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Azure でホストされる API を PowerApps と Microsoft Flow にエクスポートする

## <a name="creating-custom-connectors-for-powerapps-and-microsoft-flow"></a>PowerApps および Microsoft Flow 用のカスタム コネクタの作成

[PowerApps](https://powerapps.com) は、ユーザーのデータに接続して異なるプラットフォーム間で動作するカスタム ビジネス アプリを作成して使うためのサービスです。 [Microsoft Flow](https://flow.microsoft.com) は、ユーザーが好むアプリとサービスの間でワークフローとビジネス プロセスを簡単に自動化できるようにします。 PowerApps と Microsoft Flow のどちらにも、Office 365、Dynamics 365、Salesforce などのデータ ソースへのさまざまな組み込みコネクタが付属しています。 ただし、組織で作成されたデータ ソースと API も利用できる必要があります。

同様に、組織内での独自 API の利用範囲を広げるには、PowerApps や Microsoft Flow のユーザーも API を使用できるようにする必要があります。 このトピックでは、Azure App Service または Azure Functions で作成された API を PowerApps および Microsoft Flow に公開する方法について説明します。 サービスとしてのプラットフォームである [Azure App Service](https://azure.microsoft.com/services/app-service/) を利用することで、開発者はエンタープライズ レベルの Web、モバイル、API アプリケーションを迅速かつ簡単に構築できます。 [Azure Functions](https://azure.microsoft.com/services/functions/) はイベント ベースのサーバーレス コンピューティング ソリューションであり、システムの他の部分に対応でき、必要に応じてスケーリングできるコードを、短時間で作成できます。

これらのサービスについて詳しくは、以下の情報をご覧ください。
- [PowerApps ガイド学習](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) 
- [Microsoft Flow ガイド学習](https://flow.microsoft.com/guided-learning/learning-introducing-flow/)
- [App Service とは](https://docs.microsoft.com/azure/app-service/app-service-value-prop-what-is)
- [Azure Functions とは](https://docs.microsoft.com/azure/azure-functions/functions-overview)

## <a name="sharing-an-api-definition"></a>API 定義を共有する

API は、多くの場合、[OpenAPI ドキュメント](https://www.openapis.org/) ("Swagger" ドキュメントと呼ばれることもあります) を使って記述されます。 これには、使用可能な操作とデータの構成方法に関するすべての情報が含まれます。 PowerApps と Microsoft Flow では、任意の OpenAPI 2.0 ドキュメントに対応するカスタム コネクタを作成できます。 作成したカスタム コネクタは、組み込みコネクタとまったく同じように使用でき、アプリケーションに簡単に統合できます。

Azure App Service と Azure Functions には、OpenAPI ドキュメントを作成、ホスト、管理するための[サポートが組み込まれて](https://docs.microsoft.com/azure/app-service-api/app-service-api-metadata)います。 Web、モバイル、API、または関数アプリにカスタム コネクタを作成するには、PowerApps および Flow に定義のコピーが必要です。

> [!NOTE]
> API 定義のコピーが使われるため、PowerApps と Microsoft Flow は、アプリケーションの更新や重大な変更についてすぐに知ることができません。 新しいバージョンの API が使用可能になった場合は、新しいバージョンに対してこの手順を繰り返す必要があります。 

PowerApps および Microsoft Flow にアプリでホストされる API の定義を提供するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) を開き、App Service アプリケーションまたは Azure Functions アプリケーションに移動します。

    Azure App Service を使う場合は、設定一覧の **[API 定義]** を選びます。 
    
    Azure Functions を使用する場合、関数アプリを選択して、**[プラットフォーム機能]**、**[API 定義]** の順に選択します。 代わりに **[API 定義 (プレビュー)]** タブを開いて選択することもできます。

2. API 定義が提供されている場合は、**[Export to PowerApps + Microsoft Flow (PowerApps + Microsoft Flow にエクスポート)]** ボタンが表示されます。 このボタンをクリックして、エクスポート プロセスを開始します。

3. **[エクスポート モード]** を選択します。 これにより、コネクタを作成するために従う必要がある手順が決まります。 App Service には PowerApps と Microsoft Flow に API 定義を提供するためのオプションが 2 つあります。

    **[簡易]** では Azure Portal 内でカスタム コネクタを作成できます。 この場合、現在ログインしているユーザーに、対象となる環境でコネクタを作成するためのアクセス許可が必要です。 これは、その要件が満たされる場合に推奨される方法です。 このモードを使用する場合は、以下に示す[簡易エクスポート](#express)の手順に従ってください。

    **[手動]** では、PowerApps または Microsoft Flow のポータルを使用してインポートできる API 定義のコピーをエクスポートできます。 これは、Azure ユーザーとコネクタを作成するためのアクセス許可を持つユーザーが異なる場合や、コネクタを別のテナントで作成する必要がある場合に推奨される方法です。 このモードを使用する場合は、以下に示す[手動でのエクスポートとインポート](#manual)の手順に従ってください。

<a name="express"></a>
## <a name="express-export"></a>簡易エクスポート

このセクションでは、Azure Portal 内で新しいカスタム コネクタを作成します。 エクスポートするテナントにログインしていて、対象となる環境でカスタム コネクタを作成するためのアクセス許可を持っている必要があります。

1. コネクタを作成する環境を選択します。 次に、カスタム コネクタの名前を指定します。

2. API 定義にセキュリティ定義が含まれる場合は、手順 2 で適用されます。 必要に応じて、API へのアクセスをユーザーに許可するために必要な、セキュリティ構成の詳細を指定します。 詳細については、下の「[認証](#auth)」を参照してください。 

3. **[OK]** をクリックして、カスタム コネクタを作成します。


<a name="manual"></a>
## <a name="manual-export-and-import"></a>手動でのエクスポートとインポート

Web、モバイル、API、関数のアプリに対するカスタム コネクタを作成するには、2 つの手順が必要になります。

1. [App Service または Azure Functions から API 定義を取得する](#export)
2. [PowerApps と Microsoft Flow に API 定義をインポートする](#import)

これら 2 つの作業を実行するためのアクセス許可を両方とも持つユーザーがいないために、2 つの手順を組織内の別のユーザーが行うことが必要になる場合があります。 そのような場合は、App Service アプリケーションまたは Azure Functions アプリケーションへの共同作成者アクセス権がある開発者が、API 定義 (1 つの JSON ファイル) または API 定義へのリンクを取得する必要があります。 そして、その定義を PowerApps または Microsoft Flow の所有者に提供する必要があります。 その所有者は、メタデータを使ってカスタム コネクタを作成できます。

<a name="export"></a>
### <a name="retrieving-the-api-definition-from-app-service-or-azure-functions"></a>App Service または Azure Functions から API 定義を取得する

このセクションでは、PowerApps または Microsoft Flow のポータルで後ほど使用できるように、App Service API の API 定義をエクスポートします。

1. **[API 定義のダウンロード]** または **[リンクの取得]** を選ぶことができます。 どちらを選んでも、次のセクションで結果が提供されます。 どちらかのオプションを選んで指示に従います。
 
2. API 定義にセキュリティ定義が含まれる場合は、手順 2 で適用されます。 インポート中に、PowerApps と Microsoft Flow はセキュリティ定義を検出し、セキュリティ情報を要求します。 次のセクションで使うために、各定義に関連する資格情報を収集します。 詳細については、下の「[認証](#auth)」を参照してください。 

<a name="import"></a>
### <a name="importing-the-api-definition-into-powerapps-and-microsoft-flow"></a>PowerApps と Microsoft Flow に API 定義をインポートする

このセクションでは、先に取得した API 定義を使って、PowerApps と Microsoft Flow でカスタム コネクタを作成します。 カスタム コネクタは 2 つのサービスの間で共有されるので、定義をインポートする必要があるのは 1 回だけです。 カスタム コネクタの詳細については、「[PowerApps でのカスタム コネクタの登録と使用]」と「[Microsoft Flow でカスタム コネクタを登録して使用する]」を参照してください。

1. [Powerapps Web ポータル](https://web.powerapps.com)または [Microsoft Flow Web ポータル](https://flow.microsoft.com/)を開いてサインインします。 

2. ページの右上にある **[設定]** ボタン (歯車アイコン) をクリックし、**[Custom connectors]**\(カスタム コネクタ\) を選びます。 

3. **[Create custom connector]**\(カスタム コネクタの作成\) をクリックします。

4. **[全般]** タブで API の名前を指定し、OpenAPI 定義をアップロードするか、メタデータの URL に貼り付けます。 **[続行]** をクリックします。

4. **[セキュリティ]** タブで、認証の詳細の指定を求めるメッセージが表示されたら、前のセクションで取得した値を入力します。 表示されない場合は、次の手順に進みます。

5. **[Definitions]**\(定義\) タブでは、OpenAPI ファイルに定義されている操作に自動入力されています。 必要な操作がすべて定義されている場合、次の手順に進むことができます。 すべて定義されていない場合、ここで操作を追加したり、変更したりできます。

6. **[Create connector]**\(コネクタの作成\) をクリックします。 API 呼び出しをテストする場合、次の手順に進みます。

7. **[Test]**\(テスト\) タブで、接続を作成し、テストする操作を選択し、操作で必要なデータがあればそれを入力します。

8. **[テスト操作]** をクリックします。


<a name="auth"></a>
## <a name="authentication"></a>認証

PowerApps と Microsoft Flow は、カスタム コネクタのユーザーのログインに使用できる ID プロバイダーのコレクションをネイティブでサポートしています。 API に認証が必要な場合は、OpenAPI ドキュメントで_セキュリティ定義_としてキャプチャされるようにします。 エクスポート中に、PowerApps と Microsoft Flow がログイン操作を実行できるように、構成値を指定する必要があります。

このセクションでは、簡易フローでサポートされる認証の種類 (API キー、Azure Active Directory、および汎用 OAuth 2.0) について説明します。 それぞれの種類で必要とされるプロバイダーと資格情報の完全な一覧は、「[PowerApps でのカスタム コネクタの登録と使用]」と「[Microsoft Flow でカスタム コネクタを登録して使用する]」を参照してください。

### <a name="api-key"></a>API キー
このセキュリティ スキーマを使用すると、コネクタのユーザーが接続を作成する際にキーを指定するように求められます。 どのキーが必要とされているがわかるように、API キーに名前を指定することができます。 Azure Functions の場合、これは通常、関数アプリ内の複数の関数に対応している、ホスト キーのいずれかになります。

### <a name="azure-active-directory"></a>Azure Active Directory
AAD のログインを必要とするカスタム コネクタを構成する場合、2 つの AAD アプリケーションの登録が必要となります。1 つはバックエンド API のモデリングに、もう 1 つは PowerApps および Flow のコネクタのモデリングのためです。

API は、初回の登録で動作するように構成する必要があります。これは、[App Service 認証/承認](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication)の機能を使用している場合は既に考慮されています。

[AAD アプリケーションの追加](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application)のセクションで説明されている手順を使用して、コネクタの 2 回目の登録を手動で作成する必要があります。 この登録には、API および `https://msmanaged-na.consent.azure-apim.net/redirect` の応答 URL への委任アクセス許可が含まれている必要があります。 詳しくは、[こちらの例](
https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/)をご覧ください。Azure Resource Manager に対する API は実際のものに置き換えてください。

次の構成値が必要です。
- **クライアント ID** - コネクタの AAD 登録のクライアント ID
- **クライアント シークレット** - コネクタの AAD 登録のクライアント シークレット
- **ログイン URL** - AAD のベース URL。 パブリック Azure では、これは通常、`https://login.windows.net` になります。
- **テナント ID** - ログインに使用するテナントの ID。 これは、"common" またはコネクタが作成されているテナントの ID である必要があります。
- **リソース URL** - API バックエンドの AAD 登録のリソース URL

> [!IMPORTANT]
> 別のユーザーが API 定義を手動フローの一部として PowerApps および Microsoft Flow にインポートする場合は、**コネクタ登録**のクライアント ID とクライアント シークレット、および API のリソース URL を提供する必要があります。 これらのシークレットが安全に管理されていることを確認します。 **API 自体のセキュリティ資格情報は共有しないでください。**

### <a name="generic-oauth-20"></a>汎用 OAuth 2.0
汎用 OAuth 2.0 のサポートにより、 OAuth 2.0 プロバイダーとの統合が可能になります。 これにより、ネイティブでサポートされていないカスタム プロバイダーを取り込むことができます。

次の構成値が必要です。
- **クライアント ID** - OAuth 2.0 のクライアント ID
- **クライアント シークレット** - OAuth 2.0 のクライアント シークレット
- **承認 URL** - OAuth 2.0 の承認 URL
- **トークン URL** - OAuth 2.0 のトークン URL
- **更新 URL** - OAuth 2.0 の更新 URL



[PowerApps でのカスタム コネクタの登録と使用]: https://powerapps.microsoft.com/tutorials/register-custom-api/
[Microsoft Flow でカスタム コネクタを登録して使用する]: https://flow.microsoft.com/documentation/register-custom-api/

