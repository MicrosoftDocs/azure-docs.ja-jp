---
title: Azure IoT モデル リポジトリの概念の理解 | Microsoft Docs
description: ソリューション開発者や IT プロフェッショナル向けに、Azure IoT モデル リポジトリの基本的な概念について説明します。
author: prashmo
ms.author: prashmo
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 7d736721e2676a42da90aead3144f8016329f730
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475500"
---
# <a name="azure-iot-model-repository"></a>Azure IoT モデル リポジトリ

Azure IoT モデル リポジトリを使用すると、デバイス ビルダーは IoT プラグ アンド プレイ デバイス モデルを管理および共有できます。 デバイス モデルは、[Digital Twins モデリング言語 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) を使用して定義された JSON LD ドキュメントです。 モデル リポジトリ サービスに格納されているモデルは、ソリューション開発者と共有することができます。そのためには、アクセス制御を介してプライベートに共有するか、認証を求めずにパブリックに共有して、IoT プラグ アンド プレイ クラウド ソリューションの統合および開発を行います。

モデル リポジトリにアクセスするには、次のものを使用します。

- [Azure IoT モデル リポジトリ](https://aka.ms/iotmodelrepo) ポータル
- [Azure IoT モデル リポジトリ REST API](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync)
- [Azure CLI の IoT モデル リポジトリ コマンド](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp?view=azure-cli-latest)

## <a name="public-models"></a>パブリック モデル

モデル リポジトリに格納されているパブリック デジタル ツイン モデルは、すべてのユーザーが認証なしでアプリケーションで使用および統合することができます。 また、パブリック モデルを使用すると、オープンなエコシステムでデバイス ビルダーおよびソリューション開発者が、IoT プラグ アンド プレイ デバイス モデルを共有したり、再利用したりすることができます。

モデル リポジトリにモデルを公開してパブリックにする方法については、「**会社のモデル**」の「[モデルを公開する](#publish-a-model)」の項を参照してください。

モデル リポジトリ ポータルを使用してパブリック モデルを表示するには:

1. [Azure IoT モデル リポジトリ ポータル](https://aka.ms/iotmodelrepo)にアクセスします。

1. **[View public models]** \(パブリック モデルの表示\) を選択します。

    ![パブリック モデルの表示](./media/concepts-model-repository/public-models.png)

REST API を使用してプログラムでパブリック モデルを表示するには、[Get Model](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync) REST API のドキュメントを参照してください。

```csharp
var httpClient = new HttpClient();
httpClient.BaseAddress = new Uri("https://repo.azureiotrepository.com");

var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.GetAsync($"/models/{modelId}?api-version=2020-05-01-preview").ConfigureAwait(false);
```

CLI を使用してパブリック モデルを表示するには、Azure CLI の[モデルの取得](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-show)コマンドを参照してください。

## <a name="company-models"></a>会社のモデル

会社のモデル リポジトリは、会社または組織内のユーザーが作成したデジタル ツイン モデルを作成および管理するための、組織の Azure IoT モデル リポジトリ内のテナントです。 会社のモデルは、会社または組織の認証済みユーザーのみが使用できます。 モデル リポジトリのテナント管理者は、会社のモデル リポジトリ内のモデルに対する、会社または組織内の他のユーザーのアクセス許可を割り当てたり、制御したりすることができます。

### <a name="set-up-your-company-model-repository"></a>会社のモデル リポジトリをセットアップする

*会社または学校の Azure Active Directory (Azure AD) アカウント*を使用して、モデル リポジトリにアクセスします。 組織に既に Azure AD テナントがある場合は、この Azure AD テナントのユーザー アカウントとサービス プリンシパルを使用できます。

Azure AD テナントをセットアップする方法と、Azure AD テナントでユーザーまたはサービス プリンシパルを作成する方法については、「[追加情報](#additional-information)」の項を参照してください。

- 組織で最初にモデル リポジトリにアクセスしたか、ポータルにサインインしたユーザーには、**テナント管理者**ロールが付与されます。 このロールを保持しているユーザーは、組織のリポジトリ テナント内の他のユーザーにロールを割り当てることができます。

- **テナント管理者**によって、**モデルの読み取り**、**モデルの作成**などの他のロールがユーザーに割り当てられる場合があります。

### <a name="understand-access-management"></a>アクセス管理について

次の表は、会社のモデル リポジトリでサポートされている機能と、関連するアクセス許可をまとめたものです。

| 機能  | 権限| 説明|
|-------------|-----------|------------|
|モデルの読み取り|モデルの読み取り|既定では、会社のテナントのすべてのユーザーが会社のモデルを表示できます。 さらに、ユーザーは他の会社がそのユーザーに対して共有しているプライベート モデルを表示することもできます。|
|アクセスを管理する|アクセスを管理する|組織内の他のユーザーのユーザー ロールの割り当てを管理 (追加または削除) します。|
|モデルの作成|モデルの作成|会社のモデル リポジトリにモデルを作成します。|
|モデルの公開|モデルの公開|モデルを公開してパブリックにし、すべてのユーザーがモデルを表示できるようにします。|

次の表は、アクセス管理に使用できる、モデル リポジトリでサポートされているロールとその機能をまとめたものです。

|Role|機能|
|----|----------|
|TenantAdministrator|アクセスの管理、モデルの読み取り|
|Creator|モデルの作成、モデルの読み取り|
|Publisher|モデルの公開、モデルの読み取り|

#### <a name="passing-a-security-token-when-accessing-company-models-with-a-rest-api"></a>REST API を使用して会社のモデルにアクセスするときにセキュリティ トークンを渡す

REST API を呼び出して、プライベートな会社のモデルまたは共有されている会社のモデルを管理する場合は、ユーザーまたはサービス プリンシパルの認証トークンを JWT 形式で渡す必要があります。 ユーザーまたはサービス プリンシパルの JWT トークンを取得する方法については、「[追加情報](#additional-information)」の項を参照してください。

会社のモデルまたは共有されたモデルを対象とする場合は、JWT トークンを API の Authorization HTTP ヘッダーで渡す必要があります。 パブリック モデルを対象とする場合、JWT トークンは必要ありません。

```csharp
// sample token
var authorizationToken = "eyJhbGciOiJIUzI1NiIsInR5cCTI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c";
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authorizationToken);
```

### <a name="view-company-or-shared-models"></a>会社のモデルまたは共有されたモデルを表示する

モデルを読み取るには、リポジトリ テナントの *Reader* ロールのメンバーであるか、モデルが共有されている必要があります。 操作しているユーザーに対して共有されている非公開のモデルの一覧と、共有されている公開済みのモデルの一覧が表示されます。 既定では、ユーザーは所属する会社のモデル、他の会社がそのユーザーに対して共有しているモデル、およびすべてのパブリック モデルを読み取ることができます。

ポータルを使用して会社のモデルまたは共有されたモデルを表示するには:

1. [Azure IoT モデル リポジトリ ポータル](https://aka.ms/iotmodelrepo)にサインインします。

1. 左側のペインで、 **[会社のモデル]** – **[非公開]** を展開します

    ![会社のモデルの表示](./media/concepts-model-repository/view-company-models.png)

1. 左側のペインで、 **[共有されたモデル – 非公開]** を展開します

    ![共有されたモデルの表示](./media/concepts-model-repository/view-shared-models.png)

REST API を使用して会社のモデルまたは共有されたモデルを表示するには、[Get Model](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync) REST API のドキュメントを参照してください。 HTTP 要求で JWT Authorization ヘッダーを渡す方法については、「[REST API を使用して会社のモデルにアクセスするときにセキュリティ トークンを渡す](#passing-a-security-token-when-accessing-company-models-with-a-rest-api)」を参照してください。

```csharp
var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.GetAsync($"/models/{modelId}?api-version=2020-05-01-preview").ConfigureAwait(false);
```

CLI を使用して会社のモデルまたは共有されたモデルを表示するには、Azure CLI の[モデルの取得](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-show)コマンドを参照してください。

### <a name="manage-roles"></a>ロールの管理

テナント管理者は、リポジトリ テナントのユーザーにロールを割り当てて会社または組織のプライベートなモデルを作成したり、モデルを公開したり、他のユーザーのロールを管理したりすることができます。

ポータルを使用して、モデル リポジトリ テナントのロールにユーザーを追加するには:

1. [Azure IoT モデル リポジトリ ポータル](https://aka.ms/iotmodelrepo)にサインインします。

1. 左側のペインで **[アクセス管理]** を選択して、 **[+追加]** を選択します。 **[アクセス許可の追加]** ペインで、ロールに追加するユーザーのメール アドレスを入力します。

    ![メール アドレスの追加](./media/concepts-model-repository/add-user.png)

1. **[ロール]** ドロップダウンから、ユーザーを追加するロールを選択します。 次に、 **[保存]** を選択します。

    ![ロールの選択](./media/concepts-model-repository/choose-role.png)

### <a name="upload-a-model"></a>モデルのアップロード

会社のモデル リポジトリにモデルをアップロードするには、リポジトリ テナントの **Creator** ロールのメンバーである必要があります。

これらのモデルは公開されず、既定では組織内のユーザーのみがアクセスできます。 また、公開されていない 1 つ以上のモデルを外部ユーザーと共有することもできます。

アップロードされたモデルは変更できません。

これらのモデルのモデル ID は、すべてのリポジトリ テナントのアップロードされたすべてのモデルでグローバルに一意である必要があります。

ポータルを使用してモデルをアップロードするには:

1. [Azure IoT モデル リポジトリ ポータル](https://aka.ms/iotmodelrepo)にサインインします。

1. 左側のペインで、 **[会社のモデル]** を展開して、 **[モデルの作成]** を選択します。 次に、 **[JSON をインポート]** を選択します。

    ![モデルの作成](./media/concepts-model-repository/create-model.png)

1. アップロードするファイルを選択します。 ポータルでモデルが正常に検証された場合は、 **[保存]** を選択します。

REST API を使用してモデルをアップロードするには、[モデルの作成](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/createorupdateasync/createorupdateasync) API を参照してください。 HTTP 要求で JWT Authorization ヘッダーを渡す方法については、「[REST API を使用して会社のモデルにアクセスするときにセキュリティ トークンを渡す](#passing-a-security-token-when-accessing-company-models-with-a-rest-api)」を参照してください。

```csharp
var httpContent = new StringContent(jsonLdModel, Encoding.UTF8, "application/json");
var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.PutAsync($"/models/{modelId}?api-version=2020-05-01-preview", httpContent).ConfigureAwait(false);
```

CLI を使用してモデルをアップロードするには、Azure CLI の[モデルの作成](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-create)コマンドを参照してください。

### <a name="publish-a-model"></a>モデルを公開する

モデルを公開するには、次の要件を満たしている必要があります。

1. モデルを公開するには、組織が [Microsoft Partner Network](https://docs.microsoft.com/partner-center/) のメンバーである必要があります。 パートナー センター アカウントを作成するには、[パートナー センター アカウントの作成](https://docs.microsoft.com/partner-center/mpn-create-a-partner-center-account)に関する記事を参照してください。 アカウントが承認されたら、モデルを公開できます。 詳細については、[パートナー センターに関する FAQ](https://support.microsoft.com/help/4340639/partner-center-account-faqs) の記事を参照してください。

2. ユーザーは、リポジトリ テナントの *Publisher* ロールのメンバーである必要があります。

組織内のユーザーによって作成および公開されたモデルは、*公開されたモデル*として表示されます。 これらのモデルはパブリックであり、すべてのユーザーが **[パブリック モデル]** で参照できます。

ポータルを使用してモデルを公開するには:

1. [Azure IoT モデル リポジトリ ポータル](https://aka.ms/iotmodelrepo)にサインインします。

2. 左側のペインで **[会社のモデル]** を展開して、公開するモデルを選択します。 **[発行]** を選択します。

    ![モデルの公開](./media/concepts-model-repository/publish-model.png)

> [!NOTE]
> Microsoft Partner (MPN) ID を保持していないという通知が表示された場合は、通知に記載されている登録手順に従います。 詳細については、この項の冒頭の要件を参照してください。

REST API を使用してモデルを公開するには、[モデルの公開](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/createorupdateasync/createorupdateasync) REST API のドキュメントを参照してください。 REST API を使用してモデルを公開するには、クエリ文字列パラメーター `update-metadata=true` を指定します。 HTTP 要求で JWT Authorization ヘッダーを渡す方法については、「[REST API を使用して会社のモデルにアクセスするときにセキュリティ トークンを渡す](#passing-a-security-token-when-accessing-company-models-with-a-rest-api)」を参照してください。

CLI を使用してモデルを公開するには、Azure CLI の[モデルの公開](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-publish)コマンドを参照してください。

### <a name="share-a-model"></a>モデルを共有する

作成した会社のモデルは、外部組織のユーザーと共有できます。 これにより、コラボレーターがプライベートな会社のモデルを使用してソリューションを表示および開発できるようにすることができます。

たとえば、デバイスの製造元が、モデルを会社または組織のプライベートなモデルとして維持したい場合があります。 彼らは顧客からデバイスの機能の機密性を確保するように要求されることがあります。

会社間または組織間でモデルを共有すると、パブリックではないモデルに安全にアクセスできます。

ポータルを使用して会社のモデルを共有するには:

- モデルの作成者である場合、 **[会社のモデル]** セクションでモデルを表示すると、 **[共有]** ボタンと **[共有ユーザー]** ボタンがアクティブになります。

    ![モデルの共有](./media/concepts-model-repository/share-model.png)

- 外部ユーザーとモデルを共有するには、 **[共有]** を選択します。 **[モデルの共有]** ペインで、外部ユーザーの電子メール アドレスを入力し、 **[保存]** を選択します。

- モデルを共有したユーザーを表示するには、 **[共有ユーザー]** を選択します。

- 特定のユーザーとのモデルの共有を停止するには、 **[共有ユーザー]** ペインで、ユーザーの一覧からユーザーを選択します。 次に、 **[削除]** を選択し、メッセージが表示されたら操作を確定します。

    ![共有を停止する](./media/concepts-model-repository/stop-sharing.png)

## <a name="additional-information"></a>関連情報

Azure AD を使用するときに、次のトピックが役に立つ場合があります。

- 新しい Azure AD のテナントを作成するには、「[Azure AD で新しいテナントを作成する](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)」を参照してください。 ほとんどの組織には、既に Azure AD テナントがあります。

- Azure AD テナントにユーザーまたはゲスト ユーザーを追加する方法については、「[Azure AD を使用してユーザーを追加または削除する](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory)」を参照してください。

- Azure AD にサービス プリンシパルを追加するには、「[方法: リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルをポータルで作成する](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)」を参照してください。

- REST API を呼び出すときに使用する JWT トークンを Azure AD から取得する方法については、「[クライアント アプリケーションからの要求を承認するために Azure AD からトークンを取得する](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app)」を参照してください。

## <a name="next-steps"></a>次のステップ

次の手順では、「[IoT プラグ アンド プレイのアーキテクチャ](concepts-architecture.md)」を確認することをお勧めします。