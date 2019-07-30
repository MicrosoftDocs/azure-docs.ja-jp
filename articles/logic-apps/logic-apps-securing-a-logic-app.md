---
title: Azure Logic Apps へのアクセスのセキュリティ保護
description: トリガー、入力と出力、パラメーター、その他のサービスなど、Azure Logic Apps にセキュリティを追加します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 02/05/2019
ms.openlocfilehash: 65369e5747f17c0e420d5775f1c5704cac9ca21b
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341099"
---
# <a name="secure-access-in-azure-logic-apps"></a>Azure Logic Apps へのアクセスのセキュリティ保護

アクセスをセキュリティ保護できるお客様のロジック アプリの要素を次に示します。

* [要求トリガーまたは Webhook トリガー](#secure-triggers)
* お客様のロジック アプリの[管理、編集、表示などの操作](#secure-operations)
* お客様のロジック アプリの実行履歴からの[入力と出力](#secure-run-history)
* [アクション パラメーターと入力](#secure-action-parameters)
* お客様のロジック アプリから[要求を受け取るサービス](#secure-requests)

<a name="secure-triggers"></a>

## <a name="secure-access-to-request-triggers"></a>要求トリガーへのアクセスをセキュリティ保護する

お客様のロジック アプリによって HTTP 要求ベースのトリガー ([要求](../connectors/connectors-native-reqres.md)または [Webhook](../connectors/connectors-native-webhook.md) のトリガー) が使用される場合、承認されたクライアントだけがお客様のロジック アプリを起動できるようにアクセスを制限できます。 ロジック アプリで受信されるすべての要求は、Secure Sockets Layer (SSL) プロトコルで暗号化され、セキュリティ保護されます。 このトリガーの種類へのアクセスをセキュリティ保護できるさまざまな方法を次に示します。

* [Shared Access Signature の生成](#sas)
* [受信 IP アドレスの制限](#restrict-incoming-ip-addresses)
* [Azure Active Directory、OAuth、またはその他のセキュリティの追加](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures"></a>Shared Access Signature の生成

ロジック アプリのすべての要求エンドポイントは、エンドポイントの URL に [Shared Access Signature (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md) を含みます。 各 URL には `sp`、`sv`、`sig` のクエリ パラメーターが含まれています。

* `sp` では、使用が許可される HTTP メソッドにマップされるアクセス許可を指定します。
* `sv` では、署名の生成に使用されるバージョンを指定します。
* `sig` は、トリガーへのアクセスを認証するために使用されます。

署名は、SHA256 アルゴリズムとシークレット アクセス キーを使用してすべての URL パスとプロパティで生成されます。 この秘密鍵が公開されることはなく、ロジック アプリと共に暗号化されて格納されます。 お客様のロジック アプリでは、秘密鍵を使用して作成された有効な署名を含むそれらのトリガーのみが承認されます。 

Shared Access Signature によるアクセスのセキュリティ保護の詳細を以下に示します。

* [アクセス キーを再生成する](#access-keys)
* [有効期限付きのコールバック URL を作成する](#expiring-urls)
* [プライマリまたはセカンダリのキーを使用して URL を作成する](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>アクセス キーを再生成する

新しい安全なアクセス キーを随時再生成するには、Azure REST API または Azure portal を使用します。 過去に生成され、古いキーが使用される URL はすべて無効になり、ロジック アプリのトリガーが承認されなくなります。 再生成後に取得する URL は、新しいアクセス キーで署名されます。

1. Azure portal で、再生成したいキーを備えたロジック アプリを開きます。

1. ロジック アプリのメニューにある **[設定]** で、 **[アクセス キー]** を選択します。

1. 再生成したいキーを選択して、プロセスを完了します。

<a name="expiring-urls"></a>

#### <a name="create-callback-urls-with-expiration-dates"></a>有効期限付きのコールバック URL を作成する

要求ベースのトリガー エンドポイントの URL を他の関係者と共有する場合は、必要に応じて、キーと有効期限を指定してコールバック URL を生成できます。 その後、キーをシームレスにロールしたり、お客様のロジック アプリに対するトリガーへのアクセスを特定の期間に制限したりできます。 [Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers) を使用して URL の有効期限を指定できます。次に例を示します。

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

本文では、JSON 日付文字列を使用して `NotAfter` プロパティを指定します。 このプロパティは、`NotAfter` の日付と時刻までに限って有効なコールバック URL を返します。

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>プライマリまたはセカンダリの秘密鍵を使用して URL を作成する

要求ベースのトリガーに対するコールバック URL を生成または一覧表示する場合、URL の署名に使用するキーを指定することもできます。 [Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers) を使用して、特定のキーで署名された URL を生成できます。次に例を示します。

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

本文では、`KeyType` プロパティに `Primary` または `Secondary` を指定します。 このプロパティは、指定された安全なキーによって署名された URL を返します。

<a name="restrict-incoming-ip"></a>

### <a name="restrict-incoming-ip-addresses"></a>受信 IP アドレスの制限

Shared Access Signature と共に、お客様のロジック アプリを呼び出せる特定のクライアントを制限したい場合があるかもしれません。  
たとえば、Azure API Management を使用してお客様の要求エンドポイントを管理する場合は、API Management インスタンスの IP アドレスからのみ要求を受け取るよう、お客様のロジック アプリを制限できます。 

#### <a name="set-ip-ranges---azure-portal"></a>IP 範囲を設定する - Azure portal

Azure portal でこの制限を設定するには、お客様のロジック アプリの設定に移動します。 

1. Azure portal のロジック アプリ デザイナーでお客様のロジック アプリを開きます。 

1. ロジック アプリのメニューの **[設定]** で、 **[ワークフロー設定]** を選択します。

1. **[アクセス制御の構成]**  > 
 **[許可された着信 IP アドレス]** で、 **[特定の IP 範囲]** を選択します。

1. **[トリガーの IP 範囲]** で、トリガーで受け入れられる IP アドレス範囲を指定します。 有効な IP 範囲では、*x.x.x.x/x* または *x.x.x.x-x.x.x.x* の形式が使用されます。 

お客様のロジック アプリが、入れ子にされたロジック アプリとしてのみ起動するようにしたい場合は、 **[許可された着信 IP アドレス]** の一覧で **[他のロジック アプリのみ]** を選択します。 このオプションでは、お客様のロジック アプリのリソースに空の配列を書き込みます。そのため、入れ子にされたロジック アプリをトリガーできるのが Logic Apps サービス (親ロジック アプリ) からの呼び出しだけになります。

> [!NOTE]
> IP アドレスに関わらず、Azure REST API または API Management を通じて `/triggers/{triggerName}/run` を使用することで、要求ベースのトリガーを備えたロジック アプリを引き続き実行できます。 ただし、このシナリオでも Azure REST API に対する認証が必要であり、すべてのイベントが Azure の監査ログに表示されます。 アクセス制御ポリシーを適切に設定するようにしてください。

#### <a name="set-ip-ranges---logic-app-deployment-template"></a>IP 範囲を設定する - ロジック アプリのデプロイ テンプレート

[Azure Resource Manager デプロイ テンプレート](../logic-apps/logic-apps-create-deploy-template.md)を使用してロジック アプリのデプロイを自動化している場合、そのテンプレートで IP 範囲を設定できます。次に例を示します。

``` json
{
   "properties": {
      "definition": {},
      "parameters": {},
      "accessControl": {
         "triggers": {
            "allowedCallerIpAddresses": [
               {
                  "addressRange": "192.168.12.0/23"
               },
               {
                  "addressRange": "2001:0db8::/64"
               }
            ]
         }
      }
   },
   "type": "Microsoft.Logic/workflows",
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Azure Active Directory、OAuth、またはその他のセキュリティの追加

お客様のロジック アプリにさらに承認プロトコルを追加するには、[Azure API Management](https://azure.microsoft.com/services/api-management/) の使用を検討してください。 このサービスでは、あらゆるエンドポイント向けの監視、セキュリティ、ポリシー、ドキュメントが豊富に提供され、お客様のロジック アプリを API として公開できます。 API Management では、お客様のロジック アプリに対して、Azure Active Directory、OAuth、証明書などのセキュリティ標準を利用可能なパブリックまたはプライベート エンドポイントを公開できます。 API Management で要求が受け取られると、サービスによって要求がお客様のロジック アプリに送信されます。さらに、必要な変換または制限もその過程で行われます。 API Management にのみお客様のロジック アプリのトリガーを許可するには、ロジック アプリの受信 IP 範囲の設定を使用できます。 

<a name="secure-operations"></a>

## <a name="secure-access-to-logic-app-operations"></a>ロジック アプリの操作へのアクセスをセキュリティ保護する

特定のユーザーまたはグループにのみお客様のロジック アプリに対する操作の実行を許可するために、管理、編集、表示などのタスクでアクセスを制限できます。 Logic Apps では、[Azure のロールベースのアクセス制御 (RBAC)](../role-based-access-control/role-assignments-portal.md) がサポートされていて、組み込みのロールをカスタマイズしてお客様のサブスクリプションのメンバーに割り当てることができます。次に例を示します。

* [ロジック アプリの共同作成者](../role-based-access-control/built-in-roles.md#logic-app-contributor): ロジック アプリを管理できますが、アクセス権を変更することはできません。

* [ロジック アプリのオペレーター](../role-based-access-control/built-in-roles.md#logic-app-operator): ロジック アプリの読み取り、有効化、無効化ができますが、編集または更新はできません。

他のユーザーがお客様のロジック アプリを変更したり削除したりしないようにするには、[Azure のリソース ロック](../azure-resource-manager/resource-group-lock-resources.md)を使用できます。 この機能は、他のユーザーが運用リソースを変更したり削除したりしないようにするのに役立ちます。

<a name="secure-run-history"></a>

## <a name="secure-access-to-logic-app-run-history"></a>ロジック アプリの実行履歴へのアクセスをセキュリティ保護する

過去のロジック アプリの実行から入力または出力として渡される内容を保護するには、特定の IP アドレス範囲にアクセスを制限できます。 この機能では、さらなるアクセスの制御が提供されます。 ロジック アプリの実行内のデータはすべて、転送中および保存中に暗号化されます。 ロジック アプリの実行履歴を要求すると、Logic Apps によってその要求の認証が行われ、お客様のロジック アプリのワークフローにおける要求と応答からの入力と出力にリンクが提供されます。 これらのリンクを保護して、特定の IP アドレスからの要求のみによってその内容が返されるようにできます。 たとえば、だれも入力と出力にアクセスできないように、`0.0.0.0-0.0.0.0` などの IP アドレスを指定することさえできます。 この制限を削除できるのは管理者アクセス許可を持つ人物のみであるため、お客様のロジック アプリの内容に対する "ジャストインタイム" アクセスが可能になります。

### <a name="set-ip-ranges---azure-portal"></a>IP 範囲を設定する - Azure portal

Azure portal でこの制限を設定するには、お客様のロジック アプリの設定に移動します。

1. Azure portal のロジック アプリ デザイナーでお客様のロジック アプリを開きます。 

1. ロジック アプリのメニューの **[設定]** で、 **[ワークフロー設定]** を選択します。

1. **[アクセス制御の構成]** > 
    **[許可された着信 IP アドレス]** で、 **[特定の IP 範囲]** を選択します。

1. **[コンテンツの IP 範囲]** で、入力と出力からの内容にアクセスできる IP アドレス範囲を指定します。 
   有効な IP 範囲では、*x.x.x.x/x* または *x.x.x.x-x.x.x.x* の形式が使用されます。 

### <a name="set-ip-ranges---logic-app-deployment-template"></a>IP 範囲を設定する - ロジック アプリのデプロイ テンプレート

[Azure Resource Manager デプロイ テンプレート](../logic-apps/logic-apps-create-deploy-template.md)を使用してロジック アプリのデプロイを自動化している場合、そのテンプレートで IP 範囲を設定できます。次に例を示します。

``` json
{
   "properties": {
      "definition": {},
      "parameters": {},
      "accessControl": {
         "contents": {
            "allowedCallerIpAddresses": [
               {
                  "addressRange": "192.168.12.0/23"
               },
               {
                  "addressRange": "2001:0db8::/64"
               }
            ]
         }
      }
   },
   "type": "Microsoft.Logic/workflows",
}
```

<a name="secure-action-parameters"></a>

## <a name="secure-action-parameters-and-inputs"></a>アクション パラメーターと入力をセキュリティ保護する

さまざまな環境全体にデプロイするとき、お客様のロジック アプリのワークフロー定義で特定の要素をパラメーター化したい場合があります。 そのようにすると、お客様が使用する環境に基づいて入力を提供し、機密情報を保護することができます。 たとえば、[Azure Active Directory](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication) で HTTP アクションを認証している場合、認証に使用されるクライアント ID とクライアント シークレットを受け入れるパラメーターを定義し、セキュリティで保護します。 これらのパラメーターについては、独自の `parameters` セクションがロジック アプリ定義にあります。
実行中にパラメーター値にアクセスする場合は、[ワークフロー定義言語](https://aka.ms/logicappsdocs)で提供される `@parameters('parameterName')` 式を使用できます。 

ロジック アプリの編集時または実行履歴の表示時に表示させないパラメーターおよび値を保護するには、型が `securestring` のパラメーターを定義し、必要に応じてエンコードを使用することができます。 この型のパラメーターはリソース定義と一緒に返されず、デプロイ後にリソースを表示するときにもアクセスできません。

> [!NOTE]
> 要求のヘッダーまたは本文でパラメーターを使用している場合、お客様のロジック アプリの実行履歴および発信 HTTP 要求へのアクセス時にそのパラメーターが表示されることがあります。 コンテンツ アクセス ポリシーも適切に設定してください。
> Authorization ヘッダーは入力や出力では表示されません。 そのため、ここでシークレットが使用された場合はそのシークレットを取得できません。

ロジック アプリ定義でパラメーターをセキュリティで保護する方法については、このページの後半の「[ロジック アプリ定義のパラメーターのセキュリティ保護](#secure-parameters-workflow)」を参照してください。

[Azure Resource Manager デプロイ テンプレート](../azure-resource-manager/resource-group-authoring-templates.md#parameters)を使用してデプロイを自動化している場合、それらのテンプレートにあるセキュリティで保護されたパラメーターを使用することもできます。 たとえば、ロジック アプリの作成時に KeyVault シークレットを取得するためのパラメーターを使用できます。 デプロイ テンプレート定義には、ロジック アプリの `parameters` セクションとは別に、独自の `parameters` セクションがあります。 デプロイ テンプレートのパラメーターをセキュリティで保護する方法については、このページの後半の「[デプロイ テンプレートのパラメーターのセキュリティ保護](#secure-parameters-deployment-template)」を参照してください。

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-logic-app-definitions"></a>ロジック アプリ定義のパラメーターのセキュリティ保護

ロジック アプリのワークフロー定義内の機密情報を保護するには、セキュリティで保護されたパラメーターを使用して、ロジック アプリを保存した後にこの情報が表示されないようにします。 たとえば、HTTP アクションの定義で `Basic` 認証を使用しているとします。 この例には、アクション定義のパラメーターを定義する `parameters` セクションに加えて、`username` および `password` パラメーターの値を受け入れる `authentication` セクションが含まれています。 これらのパラメーターの値を提供するために、次のような独立したパラメーター ファイルを使用できます。

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "HTTP": {
         "type": "Http",
         "inputs": {
            "method": "GET",
            "uri": "https://www.microsoft.com",
            "authentication": {
               "type": "Basic",
               "username": "@parameters('usernameParam')",
               "password": "@parameters('passwordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "passwordParam": {
         "type": "securestring"
      },
      "userNameParam": {
         "type": "securestring"
      }
   },
   "triggers": {
      "manual": {
         "type": "Request",
         "kind": "Http",
         "inputs": {
            "schema": {}
         }
      }
   },
   "contentVersion": "1.0.0.0",
   "outputs": {}
}
```

シークレットを使用する場合、[Azure Resource Manager KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md) を使用してデプロイ時にそれらのシークレットを取得できます。

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-deployment-templates"></a>Azure Resource Manager デプロイ テンプレートのパラメーターのセキュリティ保護

次の例では、型が `securestring` のランタイム パラメーターが複数使用される Resource Manager デプロイ テンプレートを示します。

* `armTemplatePasswordParam`。これはロジック アプリ定義の `logicAppWfParam` パラメーターの入力です

* `logicAppWfParam`。これは基本認証が使用される HTTP アクションの入力です

この例には、お客様のロジック アプリのワークフロー定義に属している内部の `parameters` セクションと、お客様のデプロイ テンプレートに属している外部の `parameters` セクションが含まれています。 パラメーターの環境値を指定するために、独立したパラメーター ファイルを使用できます。 

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "logicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "armTemplatePasswordParam": {
         "type": "securestring"
      },
      "logicAppLocation": {
         "type": "string",
         "defaultValue": "[resourceGroup().location]",
         "allowedValues": [
            "[resourceGroup().location]",
            "eastasia",
            "southeastasia",
            "centralus",
            "eastus",
            "eastus2",
            "westus",
            "northcentralus",
            "southcentralus",
            "northeurope",
            "westeurope",
            "japanwest",
            "japaneast",
            "brazilsouth",
            "australiaeast",
            "australiasoutheast",
            "southindia",
            "centralindia",
            "westindia",
            "canadacentral",
            "canadaeast",
            "uksouth",
            "ukwest",
            "westcentralus",
            "westus2"
         ],
         "metadata": {
            "description": "Location of the Logic App."
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "name": "[parameters('logicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('logicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-0601/workflowdefinition.json#",
               "actions": {
                  "HTTP": {
                     "type": "Http",
                     "inputs": {
                        "method": "GET",
                        "uri": "https://www.microsoft.com",
                        "authentication": {
                           "type": "Basic",
                           "username": "@parameters('usernameParam')",
                           "password": "@parameters('logicAppWfParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "logicAppWfParam": {
                     "type": "securestring"
                  },
                  "userNameParam": {
                     "type": "securestring"
                  }
               },
               "triggers": {
                  "manual": {
                     "type": "Request",
                     "kind": "Http",
                     "inputs": {
                        "schema": {}
                     }
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "logicAppWfParam": {
                  "value": "[parameters('armTemplatePasswordParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

シークレットを使用する場合、[Azure Resource Manager KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md) を使用してデプロイ時にそれらのシークレットを取得できます。

<a name="secure-requests"></a>

## <a name="secure-access-to-services-receiving-requests"></a>要求を受信するサービスへのアクセスをセキュリティ保護する

次に示すのは、お客様のロジック アプリがアクセスする必要があって要求を送信するエンドポイントをセキュリティ保護するいくつかの方法です。

### <a name="add-authentication-on-outbound-requests"></a>送信要求に認証を追加する

HTTP、HTTP と Swagger (Open API)、または Webhook アクションを使用する場合は、お客様のロジック アプリによって送信される要求に認証を追加できます。 たとえば、基本認証、証明書認証、または Azure Active Directory 認証を使用できます。 詳細については、「[トリガーまたはアクションを認証する](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)」を参照してください。

### <a name="restrict-access-to-logic-app-ip-addresses"></a>ロジック アプリの IP アドレスへのアクセスを制限する

ロジック アプリからのすべての呼び出しは、リージョンに基づいて、指定された特定の IP アドレスから送信されます。 これらの IP アドレスからのみ要求を受け入れるフィルターを追加できます。 これらの IP アドレスについては、[Azure Logic Apps の制限と構成](logic-apps-limits-and-config.md#configuration)に関するページを参照してください。

### <a name="secure-on-premises-connectivity"></a>オンプレミスの接続をセキュリティ保護する

Azure Logic Apps では、以下のサービスとの統合を行って、安全で信頼性の高いオンプレミス通信を実現できます。

#### <a name="on-premises-data-gateway"></a>オンプレミスのデータ ゲートウェイ

Azure Logic Apps 用の多くのマネージド コネクタは、オンプレミス システム (ファイル システム、SQL、SharePoint、DB2 など) への安全な接続を提供します。 ゲートウェイは、オンプレミスのソースから、Azure Service Bus 経由の暗号化されたチャネルでデータを送信します。 すべてのトラフィックは、ゲートウェイ エージェントからの安全な送信トラフィックとして生成されます。 [オンプレミス データ ゲートウェイのしくみ](logic-apps-gateway-install.md#gateway-cloud-service)を確認してください。

#### <a name="azure-api-management"></a>Azure API Management

[Azure API Management](https://azure.microsoft.com/services/api-management/) には、オンプレミス システムへのプロキシと通信のセキュリティ保護を実現するためのサイト間仮想プライベート ネットワークと ExpressRoute の統合など、オンプレミス接続オプションが用意されています。 ロジック アプリ デザイナーでは、API Management によって公開された API をお客様のロジック アプリのワークフローから選択して、オンプレミス システムに迅速にアクセスできます。

## <a name="next-steps"></a>次の手順

* [デプロイ テンプレートを作成する](logic-apps-create-deploy-template.md)  
* [例外処理](logic-apps-exception-handling.md)  
* [ロジック アプリを監視する](logic-apps-monitor-your-logic-apps.md)  
* [ロジック アプリの障害と問題の診断](logic-apps-diagnosing-failures.md)  
