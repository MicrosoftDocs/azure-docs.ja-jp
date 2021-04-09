---
title: Azure API Management ポリシーでの名前付きの値の使用方法
description: Azure API Management ポリシーでの名前付きの値の使用方法を説明します。 名前付きの値には、リテラル文字列、ポリシー式、および Azure Key Vault に格納されているシークレットを含めることができます。
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: 2bc9b1c5724fa7bab1fdf5ac9332d87ba03a6d11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545821"
---
# <a name="use-named-values-in-azure-api-management-policies"></a>Azure API Management ポリシーで名前付きの値を使用する

[API Management ポリシー](api-management-howto-policies.md)はシステムの強力な機能の 1 つで、これを使用することにより、発行者は構成を通じて API の動作を変更できます。 API の要求または応答に対して順に実行される一連のステートメントが集まってポリシーが形成されます。 ポリシー ステートメントは、リテラル テキストの値、ポリシーの式、名前付きの値を使用して構築できます。

"*名前付きの値*" は、各 API Management インスタンス内にある名前と値ペアのグローバル　コレクションです。 コレクション内の項目の数に制限はありません。 名前付きの値を使用すると、すべての API の構成とポリシーで定数文字列の値とシークレットを管理できます。 

:::image type="content" source="media/api-management-howto-properties/named-values.png" alt-text="Azure portal 内の名前付きの値":::

## <a name="value-types"></a>値型

|Type  |Description  |
|---------|---------|
|普通紙     |  リテラル文字列またはポリシー式     |
|Secret     |   API Management によって暗号化されるリテラル文字列またはポリシー式      |
|[Key vault](#key-vault-secrets)     |  Azure Key Vault に格納されているシークレットの識別子。      |

プレーンテキストの値またはシークレットには[ポリシー式](./api-management-policy-expressions.md)を含めることができます。 たとえば、式 `@(DateTime.Now.ToString())` では、現在の日付と時刻を含む文字列が返されます。

名前付きの値の属性の詳細については、API Management の [REST API リファレンス](/rest/api/apimanagement/2020-06-01-preview/namedvalue/createorupdate)のページを参照してください。

## <a name="key-vault-secrets"></a>キー コンテナーのシークレット

シークレットの値は、API Management 内に暗号化された文字列 (カスタム シークレット) として、または [Azure Key Vault](../key-vault/general/overview.md) 内のシークレットを参照することによって格納できます。 

API Management のセキュリティ向上に役立つため、キー コンテナーのシークレットを使用することをお勧めします。

* キー　コンテナーに格納されているシークレットは、サービス間で再利用できます
* きめ細かい[アクセス ポリシー](../key-vault/general/secure-your-key-vault.md#data-plane-and-access-policies)をシークレットに適用できます
* キー コンテナーで更新されたシークレットは、API Management で自動的にローテーションされます。 キー コンテナー内で更新が行われると、4 時間以内に API Management 内の名前付きの値が更新されます。 また、Azure portal または管理 REST API を使用して、シークレットを手動で更新することもできます。

### <a name="prerequisites-for-key-vault-integration"></a>キー コンテナー統合の前提条件

1. キー コンテナーを作成する手順については、「[クイックスタート: Azure portal を使用してキー コンテナーを作成する](../key-vault/general/quick-create-portal.md)」を参照してください。
1. API Management インスタンスで、システムによって割り当てられた、またはユーザーが割り当てた[マネージド ID](api-management-howto-use-managed-service-identity.md) を有効にします。
1. コンテナーからシークレットを取得して一覧表示するアクセス許可を持つマネージド ID に、[キー コンテナー アクセス ポリシー](../key-vault/general/assign-access-policy-portal.md)を割り当てます。 ポリシーを追加するには、次の手順を実行します。
    1. ポータルで、キー コンテナーに移動します。
    1. **[設定] > [アクセス ポリシー] > [+ アクセス ポリシーの追加]** を選択します。
    1. **[シークレットのアクセス許可]** を選択し、次に **[Get]\(取得\)** と **[List]\(一覧表示\)** を選択します。
    1. **[プリンシパルの選択]** で、マネージド ID のリソース名を選択します。 システムによって割り当てられた ID を使用している場合、プリンシパルは API Management インスタンスの名前です。
1. シークレットを作成するか、キー コンテナーにインポートします。 「[クイック スタート:Azure portal を使用して Azure Key Vault との間でシークレットの設定と取得を行う](../key-vault/secrets/quick-create-portal.md)」をご覧ください。

キー コンテナーのシークレットを使用するには、[名前付きの値を追加または編集](#add-or-edit-a-named-value)し、**キー コンテナー** の種類を指定します。 キー コンテナーからシークレットを選択します。

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-or-edit-a-named-value"></a>名前付きの値を追加または編集する

### <a name="add-a-key-vault-secret"></a>キー コンテナーのシークレットを追加する

「[キー コンテナー統合の前提条件](#prerequisites-for-key-vault-integration)」を参照してください。

> [!CAUTION]
> API Management でキー コンテナーのシークレットを使用する場合は、シークレット、キー コンテナー、またはキー コンテナーにアクセスするために使用するマネージド ID を削除しないように注意してください。

1. [Azure portal](https://portal.azure.com) で、API Management インスタンスに移動します。
1. **[API]** で、 **[名前付きの値]**  >  **[+追加]** を選択します。
1. **[名前]** 識別子を入力し、ポリシー内でプロパティを参照するために使用される **[表示名]** を入力します。
1. **[値の種類]** で、 **[キー コンテナー]** を選択します。
1. キー コンテナーのシークレットの識別子 (バージョンなし) を入力するか、 **[選択]** を選択し、キー コンテナーからシークレットを選択します。
    > [!IMPORTANT]
    > キー コンテナーのシークレット識別子を自分で入力する場合は、バージョン情報が含まれていないことを確認してください。 そうしないと、キー コンテナーで更新が行われた後にシークレットが API Management で自動的にローテーションされません。
1. **[クライアント ID]** で、システムによって割り当てられた、または既存のユーザー割り当てのマネージド ID を選択します。 [API Management サービスでのマネージド ID の追加または変更方法については、こちらを参照してください](api-management-howto-use-managed-service-identity.md)。
    > [!NOTE]
    > ID には、キー コンテナーからシークレットを取得および一覧表示するためのアクセス許可が必要です。 キー コンテナーへのアクセスをまだ構成していない場合は、必要なアクセス許可を使用して ID を自動的に構成できるように、API Management によってプロンプトが表示されます。
1. 名前付きの値を整理するのに役立つ 1 つ以上の省略可能なタグを追加して、**保存** します。
1. **［作成］** を選択します

    :::image type="content" source="media/api-management-howto-properties/add-property.png" alt-text="キー コンテナーのシークレットの値を追加する":::

### <a name="add-a-plain-or-secret-value"></a>プレーンまたはシークレットの値を追加する

### <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure portal](https://portal.azure.com) で、API Management インスタンスに移動します。
1. **[API]** で、 **[名前付きの値]**  >  **[+追加]** を選択します。
1. **[名前]** 識別子を入力し、ポリシー内でプロパティを参照するために使用される **[表示名]** を入力します。
1. **[値の種類]** で、 **[プレーン]** または **[シークレット]** を選択します。
1. **[値]** に、文字列またはポリシー式を入力します。
1. 名前付きの値を整理するのに役立つ 1 つ以上の省略可能なタグを追加して、**保存** します。
1. **［作成］** を選択します

名前付きの値が作成されたら、名前を選択して編集できます。 表示名を変更すると、その名前付きの値を参照するすべてのポリシーが、その新しい表示名を使用するように自動的に更新されます。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI の使用を開始するには:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

名前付きの値を追加するには、[az apim nv create](/cli/azure/apim/nv#az_apim_nv_create) コマンドを使用します。

```azurecli
az apim nv create --resource-group apim-hello-word-resource-group \
    --display-name "named_value_01" --named-value-id named_value_01 \
    --secret true --service-name apim-hello-world --value test
```

名前付きの値を作成したら、[az apim nv update](/cli/azure/apim/nv#az_apim_nv_update) コマンドを使用して更新できます。 すべての名前付きの値を表示するには、[az apim nv list](/cli/azure/apim/nv#az_apim_nv_list) コマンドを実行します。

```azurecli
az apim nv list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

この例で作成した名前付きの値の詳細を表示するには、[az apim nv show](/cli/azure/apim/nv#az_apim_nv_show) コマンドを実行します。

```azurecli
az apim nv show --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

この例は、シークレット値です。 上記のコマンドで値は返されません。 値を表示するには、[az apim nv show-secret](/cli/azure/apim/nv#az_apim_nv_show_secret) コマンドを実行します。

```azurecli
az apim nv show-secret --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

名前付きの値を削除するには、[az apim nv delete](/cli/azure/apim/nv#az_apim_nv_delete) コマンドを使用します。

```azurecli
az apim nv delete --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

---

## <a name="use-a-named-value"></a>名前付きの値を使用する

このセクションの例では、次の表に示す名前付きの値が使用されます。

| 名前               | 値                      | Secret | 
|--------------------|----------------------------|--------|---------|
| ContosoHeader      | `TrackingId`                 | 誤  | 
| ContosoHeaderValue | ••••••••••••••••••••••     | True   | 
| ExpressionProperty | `@(DateTime.Now.ToString())` | 誤  | 

ポリシーで名前付きの値を使用するには、`{{ContosoHeader}}` のように、二重の中括弧でその表示名を囲みます。次に例を示します。

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

この例では、`ContosoHeader` は `set-header` ポリシーのヘッダー名として使用されており、`ContosoHeaderValue` はそのヘッダーの値として使用されています。 このポリシーが API Management ゲートウェイの要求または応答で評価されるとき、`{{ContosoHeader}}` と `{{ContosoHeaderValue}}` はそれぞれの値で置換されます。

名前付きの値は前の例のように完全な属性または要素値として使用できますが、次の例に示すように、リテラル テキスト表現に挿入するか、その一部と組み合わせることもできます:  

```xml
<set-header name = "CustomHeader{{ContosoHeader}}" ...>
```

名前付きの値にはポリシー式を含めることもできます。 次の例では、`ExpressionProperty` 式が使用されています。

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

このポリシーが評価されるとき、`{{ExpressionProperty}}` はその値 `@(DateTime.Now.ToString())` に置き換えられます。 値がポリシー式であるため、式が評価され、ポリシーがその実行に進みます。

これは、名前付きの値が範囲内にあるポリシーを持つ操作を呼び出すことによって、Azure portal または[開発者ポータル](api-management-howto-developer-portal.md)でテストできます。 次の例では、前の 2 つのサンプル `set-header` ポリシーと名前付きの値で操作が呼び出されています。 応答に、ポリシーと名前付きの値を使用して構成された 2 つのカスタム ヘッダーが含まれていることに注意してください。

:::image type="content" source="media/api-management-howto-properties/api-management-send-results.png" alt-text="API 応答をテストする":::

送信 [API トレース](api-management-howto-api-inspector.md)を見て、前の 2 つのサンプル ポリシーと名前付きの値が含まれる呼び出しを探すと、名前付きの値が挿入された 2 つの `set-header` ポリシーと、ポリシー式が含まれる名前付きの値のポリシー式評価を確認できます。

:::image type="content" source="media/api-management-howto-properties/api-management-api-inspector-trace.png" alt-text="API Inspector トレース":::

> [!CAUTION]
> ポリシーで Azure Key Vault 内のシークレットが参照されている場合、キー・コンテナーの値は、[そのAPI 要求トレース](api-management-howto-api-inspector.md)が有効になっているサブスクリプションにアクセスできるユーザーに表示されます。

名前付きの値にはポリシー式を含めることができますが、他の名前付きの値を含めることはできません。 名前付きの値参照を含むテキストが `Text: {{MyProperty}}` などの値に使用されている場合、その参照は解決されず、置き換えられません。

## <a name="delete-a-named-value"></a>名前付きの値を削除する

名前付きの値を削除するには、名前を選択して、コンテキスト メニュー ( **...** ) から **[削除]** を選択します。

> [!IMPORTANT]
> 名前付きの値がいずれかの API Management ポリシーで参照されている場合は、それが使用されているすべてのポリシーから削除してからでないと削除できません。

## <a name="next-steps"></a>次のステップ

-   ポリシーの使用に関する説明
    -   [API Management のポリシー](api-management-howto-policies.md)
    -   [Policy reference (ポリシー リファレンス)](./api-management-policies.md)
    -   [ポリシー式](./api-management-policy-expressions.md)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png

