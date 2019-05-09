---
title: Azure でホストされる API を PowerApps と Microsoft Flow にエクスポートする | Microsoft Docs
description: App Service でホストされる API を PowerApps および Microsoft Flow に公開する方法の概要について説明します
services: app-service
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: app-service
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/15/2017
ms.author: glenga
ms.reviewer: sunayv
ms.openlocfilehash: 9f4bbf91b09abeb917fd9f49482881e33bf788ec
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2019
ms.locfileid: "54413936"
---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Azure でホストされる API を PowerApps と Microsoft Flow にエクスポートする

[PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) は、ユーザーのデータに接続して異なるプラットフォーム間で動作するカスタム ビジネス アプリを作成して使うためのサービスです。 [Microsoft Flow](https://flow.microsoft.com/guided-learning/learning-introducing-flow/) は、ユーザーが好むアプリとサービスの間でワークフローとビジネス プロセスを簡単に自動化できるようにします。 PowerApps と Microsoft Flow のどちらにも、Office 365、Dynamics 365、Salesforce などのデータ ソースへのさまざまな組み込みコネクタが付属しています。 状況によっては、アプリとフローのビルダーから、組織でビルドされたデータソースと API に接続したい場合もあります。

同様に、組織内での API の利用範囲を広げるには、アプリとフローのビルダーで API を使用できるようにする必要があります。 このトピックでは、[Azure Functions](../azure-functions/functions-overview.md) または [Azure App Service](../app-service/overview.md) を使って構築した API をエクスポートする方法を示します。 エクスポートされた API は、PowerApps や Microsoft Flow で組み込みのコネクタのように使用される*カスタム コネクタ*になります。

## <a name="create-and-export-an-api-definition"></a>API 定義を作成してエクスポートする
API をエクスポートする前に、OpenAPI 定義 (以前は [Swagger](https://swagger.io/) ファイルと呼ばれていたもの) を使用して API を記述する必要があります 。 この定義には、API で使用できる操作の情報と、API の要求データと応答データを構造化する方法に関する情報が含まれています。 PowerApps と Microsoft Flow では、任意の OpenAPI 2.0 定義に対応するカスタム コネクタを作成できます。 Azure Functions と Azure App Service には、OpenAPI 定義を作成、ホスト、管理するためのサポートが組み込まれています。 詳細については、[Azure App Service での CORS を使用した RESTful API のホスト](../app-service/app-service-web-tutorial-rest-api.md)に関する記事をご覧ください。

> [!NOTE]
> OpenAPI 定義を使用せずに、PowerApps と Microsoft Flow の UI でカスタム コネクタをビルドすることもできます。 詳細については、「[PowerApps でのカスタム コネクタの登録と使用](https://powerapps.microsoft.com/tutorials/register-custom-api/)」および「[Microsoft Flow でカスタム コネクタを登録して使用する](https://flow.microsoft.com/documentation/register-custom-api/)」をご覧ください。

API 定義をエクスポートするには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) で、Azure Functions または別の App Service アプリケーションに移動します。

    Azure Functions を使用している場合、関数アプリを選択して、**[プラットフォーム機能]**、**[API 定義]** の順に選択します。

    ![Azure Functions API の定義](media/app-service-export-api-to-powerapps-and-flow/api-definition-function.png)

    Azure App Service を使う場合は、設定一覧の **[API 定義]** を選びます。

    ![App Service API の定義](media/app-service-export-api-to-powerapps-and-flow/api-definition-app.png)

2. **[PowerApps + Microsoft Flow にエクスポート]** ボタンが利用可能になっている必要があります (利用可能ではない場合、まず OpenAPI 定義を作成する必要があります)。 このボタンをクリックして、エクスポート プロセスを開始します。

    ![[PowerApps + Microsoft Flow にエクスポート] ボタン](media/app-service-export-api-to-powerapps-and-flow/export-apps-flow.png)

3. **[エクスポート モード]** を選択します。

    **[簡易]** では Azure Portal 内でカスタム コネクタを作成できます。 PowerApps または Microsoft Flow にサインインし、ターゲット環境でコネクタを作成する権限を持っていることが要件です。 これは、これら 2 つの要件が満たされる場合に推奨される方法です。 このモードを使用する場合は、以下に示す「[簡易エクスポートを使用する](#express)」の手順に従ってください。

    **手動** では、PowerApps または Microsoft Flow のポータルを使用してインポートする API 定義をエクスポートできます。 これは、Azure ユーザーとコネクタを作成するためのアクセス許可を持つユーザーが異なる場合や、コネクタを別の Azure テナントで作成する必要がある場合に推奨される方法です。 このモードを使用する場合は、以下に示す「[手動エクスポートを使用する](#manual)」の手順に従ってください。

    ![エクスポート モード](media/app-service-export-api-to-powerapps-and-flow/export-mode.png)

> [!NOTE]
> カスタム コネクタは API 定義の*コピー*を使用します。そのため、PowerApps と Microsoft Flow では、アプリケーションとその API 定義が変更されたかどうかをすぐには認識しません。 変更を加える場合は、新しいバージョンのエクスポート手順を繰り返します。

<a name="express"></a>
## <a name="use-express-export"></a>簡易エクスポートを使用する

**簡易**モードでエクスポートを完了するには、次の手順に従います。

1. エクスポートする PowerApps または Microsoft Flow テナントにサインインしていることを確認してください。 

2. 次の表で指定されている設定を使用してください。

    |Setting|説明|
    |--------|------------|
    |**Environment**|カスタム コネクタの保存先の環境を選択します。 詳細については、「[環境の概要](https://powerapps.microsoft.com/tutorials/environments-overview/)」を参照してください。|
    |**カスタム API 名**|名前を入力すると、PowerApps および Microsoft Flow ビルダーがコネクタ一覧に表示されます。|
    |**セキュリティ構成の準備**|必要に応じて、API へのアクセスをユーザーに許可するために必要な、セキュリティ構成の詳細を指定します。 この例は API キーを示します。 詳細については、下の「[認証の種類を指定する](#auth)」をご覧ください。|
 
    ![PowerApps および Microsoft Flow への簡易エクスポート](media/app-service-export-api-to-powerapps-and-flow/export-express.png)

3. Click **OK**. カスタム コネクタがビルドされ、指定した環境に追加されました。

Azure Functions を使って**簡易**モードを使用する例については、「[PowerApps から関数を呼び出す](functions-powerapps-scenario.md)」と「[Microsoft Flow から関数を呼び出す](functions-flow-scenario.md)」をご覧ください。

<a name="manual"></a>
## <a name="use-manual-export"></a>手動エクスポートを使用する

**手動**モードでエクスポートを完了するには、次の手順に従います。

1. **[ダウンロード]** をクリックしてファイルを保存するか、または [コピー] ボタンをクリックして URL を保存します。 インポート時には、ダウンロード ファイルや URL を使用します。
 
    ![PowerApps および Microsoft Flow への手動エクスポート](media/app-service-export-api-to-powerapps-and-flow/export-manual.png)
 
2. API 定義にセキュリティ定義が含まれる場合は、手順 2 で適用されます。 インポート中に、PowerApps と Microsoft Flow はセキュリティ定義を検出し、セキュリティ情報を要求します。 次のセクションで使うために、各定義に関連する資格情報を収集します。 詳細については、下の「[認証の種類を指定する](#auth)」をご覧ください。

    ![手動エクスポートのセキュリティ](media/app-service-export-api-to-powerapps-and-flow/export-manual-security.png)

    この例では、OpenAPI 定義に含まれた API キーのセキュリティ定義を示します。

API 定義をエクスポートしたので、この定義をインポートして PowerApps および Microsoft Flow にカスタム コネクタを作成します。 カスタム コネクタは 2 つのサービスの間で共有されるので、定義をインポートする必要があるのは 1 回だけです。

PowerApps と Microsoft Flow に API 定義をインポートするには、次の手順に従います。

1. [powerapps.com](https://web.powerapps.com) または [flow.microsoft.com](https://flow.microsoft.com) に移動します。

2. 右上にある歯車アイコンをクリックし、**[カスタム コネクタ]** をクリックします。

   ![サービスの歯車アイコン](media/app-service-export-api-to-powerapps-and-flow/icon-gear.png)

3. **[カスタム コネクタの作成]**、**[Import an OpenAPI definition]\(OpenAPI 定義のインポート\)** の順にクリックします。

   ![カスタム コネクタの作成](media/app-service-export-api-to-powerapps-and-flow/flow-apps-create-connector.png)

4. カスタム コネクタの名前を入力し、エクスポートした OpenAPI 定義に移動して、**[続行]** をクリックします。

   ![OpenAPI 定義のアップロード](media/app-service-export-api-to-powerapps-and-flow/flow-apps-upload-definition.png)

4. **[全般]** タブで、OpenAPI 定義から取得される情報を確認します。

5. **[セキュリティ]** タブで、認証の種類に適した値を入力します。 **[続行]** をクリックします。

    ![[セキュリティ] タブ](media/app-service-export-api-to-powerapps-and-flow/tab-security.png)

    この例では、API キー認証に必要なフィールドを示します。 フィールドは、認証の種類によって異なります。

6. **[Definitions]** \(定義\) タブでは、OpenAPI ファイルに定義されている操作に自動入力されています。 必要な操作がすべて定義されている場合、次の手順に進むことができます。 すべて定義されていない場合、ここで操作を追加したり、変更したりできます。

    ![[定義] タブ](media/app-service-export-api-to-powerapps-and-flow/tab-definitions.png)

    この例には、`CalculateCosts`という名前の 1 つの操作があります。 **Description** のようなメタデータはすべて、OpenAPI ファイルに由来します。

7. ページの上部にある **[コネクタの作成]** をクリックします。

これで、PowerApps と Microsoft Flow のカスタム コネクタに接続できるようになりました。 PowerApps および Microsoft Flow のポータルでコネクタを作成する方法の詳細については、「[PowerApps でのカスタム コネクタの登録と使用](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector)」および「[カスタム コネクタを登録する (Microsoft Flow) ](https://flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)」をご覧ください。

<a name="auth"></a>
## <a name="specify-authentication-type"></a>認証の種類を指定します

PowerApps および Microsoft Flow では、カスタム コネクタへの認証を提供する ID プロバイダーのコレクションをサポートしています。 API に認証が必要な場合は、以下の例のように、OpenAPI ドキュメントで _セキュリティ定義_ としてキャプチャされるようにします。

```json
"securityDefinitions": {
    "AAD": {
    "type": "oauth2",
    "flow": "accessCode",
    "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
    "scopes": {}
    }
}
``` 
エクスポート中に、PowerApps と Microsoft Flow がユーザーを認証できるように、構成値を指定します。

このセクションでは、**簡易**モードでサポートされる認証の種類 (API キー、Azure Active Directory、および汎用 OAuth 2.0) について説明します。 また、PowerApps および Microsoft Flow では基本認証と、Dropbox、Facebook、SalesForce などの特定のサービスに対する OAuth 2.0 をサポートしています。

### <a name="api-key"></a>API キー
API キーを使用すると、コネクタのユーザーが接続を作成する際にキーを指定するように求められます。 どのキーが必要とされているがわかるように、API キーに名前を指定することができます。 前の例では、API キーに関する情報を取得する場所がわかるように、`API Key (contact meganb@contoso.com)` という名前を使用しました。 Azure Functions の場合、このキーは通常、関数アプリ内の複数の関数に対応している、ホスト キーのいずれかになります。

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
Azure AD を使用する場合、API 自体に対して、または、カスタム コネクタに対しての、2 つの Azure AD アプリケーション登録の方法があります。

- API の登録を構成するには、[App Service の認証と承認の構成](../app-service/configure-authentication-provider-aad.md)機能を使用してください。

- コネクタの登録を構成するには、[Azure AD アプリケーションを追加する](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)ための手順に従います。 登録には、API および `https://msmanaged-na.consent.azure-apim.net/redirect` の応答 URL への委任アクセス許可が含まれている必要があります。 

詳細については、[PowerApps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) および [Microsoft Flow](https://flow.microsoft.com/documentation/customapi-azure-resource-manager-tutorial/) の Azure AD 登録の例をご覧ください。 これらの例では、API として Azure Resource Manager を使用しています。手順に従う場合は、API を置き換えてください。

次の構成値が必要です。
- **クライアント ID** - コネクタの Azure AD 登録のクライアント ID
- **クライアント シークレット** - コネクタの Azure AD 登録のクライアント シークレット
- **ログイン URL** - Azure AD のベース URL。 Azure では、これは通常 `https://login.windows.net`になります。
- **テナント ID** - ログインに使用するテナントの ID。 これは、"common" またはコネクタが作成されるテナントの ID である必要があります。
- **リソース URL** - お使いの API の Azure AD 登録のリソース URL

> [!IMPORTANT]
> 別のユーザーが API 定義を手動フローの一部として PowerApps および Microsoft Flow にインポートする場合は、*コネクタ登録*のクライアント ID とクライアント シークレット、および API のリソース URL を提供する必要があります。 これらのシークレットが安全に管理されていることを確認します。 **API 自体のセキュリティ資格情報は共有しないでください。**

### <a name="generic-oauth-20"></a>汎用 OAuth 2.0
汎用 OAuth 2.0 を使用すると、任意の OAuth 2.0 プロバイダーと統合できます。 これにより、ネイティブにサポートされていないカスタム プロバイダーと連携できます。

次の構成値が必要です。
- **クライアント ID** - OAuth 2.0 のクライアント ID
- **クライアント シークレット** - OAuth 2.0 のクライアント シークレット
- **承認 URL** - OAuth 2.0 の承認 URL
- **トークン URL** - OAuth 2.0 のトークン URL
- **更新 URL** - OAuth 2.0 の更新 URL


