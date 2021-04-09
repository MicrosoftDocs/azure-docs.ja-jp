---
title: Active Directory を使用して結果をトリミングするためのセキュリティ フィルター
titleSuffix: Azure Cognitive Search
description: セキュリティ フィルターと Azure Active Directory (AD) ID を使用して、Azure Cognitive Search の検索結果に対し、ドキュメント レベルのセキュリティ特権を実装する方法について説明します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/16/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 5788585b2365b12a90a508e5a972b61f73e48c15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97629512"
---
# <a name="security-filters-for-trimming-azure-cognitive-search-results-using-active-directory-identities"></a>Active Directory ID を使用して Azure Cognitive Search の結果をトリミングするためのセキュリティ フィルター

この記事では、Azure Active Directory (AD) セキュリティ ID と共に Azure Cognitive Search のフィルターを使用し、ユーザー グループのメンバーシップに基づいて検索結果をトリミングする方法について説明します。

この記事に含まれるタスクは次のとおりです。
> [!div class="checklist"]
> - Azure AD グループとユーザーを作成します
> - 作成したユーザーとグループを関連付けます
> - 新しいグループをキャッシュします
> - 関連付けられているグループでドキュメントのインデックスを作成します
> - グループ識別子フィルターでの検索要求を発行します
> 
> [!NOTE]
> この記事のサンプル コード スニペットは、C# で書かれています。 [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started)に完全なソース コードがあります。 

## <a name="prerequisites"></a>前提条件

Azure Cognitive Search のインデックスには、ドキュメントに対する読み取りアクセス権を持つグループの ID のリストを格納するための[セキュリティ フィールド](search-security-trimming-for-azure-search.md)が存在する必要があります。 このユース ケースでは、セキュリティ保護可能な項目 (個々の大学アプリケーションなど) とその項目へのアクセス権を持っているユーザー (入学担当者) を指定するセキュリティ フィールドの間に、一対一の対応があることを前提としています。

このチュートリアルを行うには、ユーザー、グループ、および関連付けを作成するために必要な Azure AD 管理者アクセス許可が必要です。 

また、次の手順で説明するように、お使いのアプリケーションが Azure AD にマルチテナント アプリとして登録されている必要があります。

### <a name="register-your-application-with-azure-active-directory"></a>Azure Active Directory にアプリケーションを登録する

この手順では、ユーザー アカウントとグループ アカウントのサインインを受け付けるために、お使いのアプリケーションを Azure AD と統合します。 組織のテナント管理者以外が次の手順を行うには、[新しいテナントの作成](../active-directory/develop/quickstart-create-new-tenant.md)が必要となる場合があります。

1. [Azure portal](https://portal.azure.com) で、サブスクリプションの Azure Active Directory リソースを検索します。

1. 左の **[管理]** で、 **[アプリの登録]** を選択し、次に **[新しい登録]** を選択します。

1. 登録に名前、おそらく検索アプリケーション名に似た名前を付けます。 **[登録]** を選択します。

1. アプリの登録が作成されたら、アプリケーション ID をコピーします。 この文字列をアプリケーションに指定する必要があります。

   [DotNetHowToSecurityTrimming](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK) をステップ実行している場合は、この値を **app.config** ファイルに貼り付けます。

   テナント ID にもこの手順を繰り返します。

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="[Essentials] セクションの [アプリケーション ID]":::

1. 左側で、 **[API のアクセス許可]** を選択してから、 **[アクセス許可の追加]** を選択します。 

1. **[Microsoft Graph]** を選択し、 **[委任されたアクセス許可]** を選択します。

1. 次の委任されたアクセス許可を検索し、追加します。

   - **Directory.ReadWrite.All**
   - **Group.ReadWrite.All**
   - **User.ReadWrite.All**

Microsoft Graph に用意されている API では、REST API を使ってプログラムから Azure AD にアクセスできます。 このチュートリアルのコード サンプルでは、Microsoft Graph API を呼び出すアクセス許可を使って、グループ、ユーザー、および関連付けを作成します。 この API は、高速のフィルター処理用にグループ識別子をキャッシュするためにも使われます。

## <a name="create-users-and-groups"></a>ユーザーとグループを作成する

設定済みのアプリケーションに検索を追加している場合は、ユーザーとグループの識別子が Azure AD に既に存在していることがあります。 その場合は、次の 3 つのステップをスキップできます。 

ただし、既存のユーザーがいない場合は、Microsoft Graph API を使ってセキュリティ プリンシパルを作成できます。 次のコード スニペットでは、識別子を生成する方法を示します。この識別子は、Azure Cognitive Search のインデックスでセキュリティ フィールドのデータ値になります。 この架空の大学入学アプリケーションでは、これは入学スタッフのセキュリティ識別子です。

特に大規模な組織では、ユーザーとグループ メンバーシップが頻繁に変更される場合があります。 ユーザーとグループの ID を作成するコードは、組織のメンバーシップの変更を反映するのに十分な頻度で実行する必要があります。 また、Azure Cognitive Search インデックスについても、許可されたユーザーとリソースの現在の状態を反映するために同様の更新スケジュールが必要です。

### <a name="step-1-create-group"></a>手順 1:[グループの作成](/graph/api/group-post-groups) 

```csharp
private static Dictionary<Group, List<User>> CreateGroupsWithUsers(string tenant)
{
    Group group = new Group()
    {
        DisplayName = "My First Prog Group",
        SecurityEnabled = true,
        MailEnabled = false,
        MailNickname = "group1"
    };
```

### <a name="step-2-create-user"></a>手順 2:[ユーザーの作成](/graph/api/user-post-users)

```csharp
User user1 = new User()
{
    GivenName = "First User",
    Surname = "User1",
    MailNickname = "User1",
    DisplayName = "First User",
    UserPrincipalName = String.Format("user1@{0}", tenant),
    PasswordProfile = new PasswordProfile() { Password = "********" },
    AccountEnabled = true
};
```

### <a name="step-3-associate-user-and-group"></a>手順 3:ユーザーとグループを関連付ける

```csharp
List<User> users = new List<User>() { user1, user2 };
Dictionary<Group, List<User>> groups = new Dictionary<Group, List<User>>() { { group, users } };
```

### <a name="step-4-cache-the-groups-identifiers"></a>手順 4:グループ識別子をキャッシュする

または、ネットワークの待機時間を減らすには、ユーザーとグループの関連付けをキャッシュします。これにより、グループは、検索要求が発行される場合、Azure AD にラウンドトリップせずに、キャッシュから返されます。 [Azure AD Batch API](/graph/json-batching) を使用して、複数のユーザーを含む単一の HTTP 要求を送信し、キャッシュを作成することができます。

Microsoft Graph は、大量の要求を処理できるように設計されています。 ただし、処理できないほど多数の要求が発生すると、Microsoft Graph は HTTP 状態コード 429 で要求を失敗させます。 詳しくは、「[Microsoft Graph 調整ガイド](/graph/throttling)」をご覧ください。

## <a name="index-document-with-their-permitted-groups"></a>許可されているグループでドキュメントにインデックスを付ける

Azure Cognitive Search のクエリ操作は、Azure Search インデックスを介して実行されます。 このステップでは、インデックス操作が、セキュリティ フィルターとして使われる識別子などの検索可能なデータを、インデックスにインポートします。 

Azure Cognitive Search では、ユーザー ID の認証は行われず、ユーザーが表示アクセス許可を持っているコンテンツを確立するためのロジックは提供されません。 セキュリティによるトリミングのユース ケースでは、機密性の高いドキュメントとそのドキュメントにアクセスできるグループ識別子の関連付けをユーザーが提供し、そのまま検索インデックスにインポートされるものと想定されています。 

この仮定の例では、Azure Cognitive Search インデックスに対する PUT 要求の本文には、志望者の小論文または成績証明書と共にそのコンテンツを表示するアクセス許可を持つグループ識別子が含まれます。 

このチュートリアルのコード サンプルで使われている汎用的な例のインデックス操作は次のようなものです。

```csharp
private static void IndexDocuments(string indexName, List<string> groups)
{
    IndexDocumentsBatch<SecuredFiles> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(
            new SecuredFiles()
            {
                FileId = "1",
                Name = "secured_file_a",
                GroupIds = new[] { groups[0] }
            }),
              ...
            };

IndexDocumentsResult result = searchClient.IndexDocuments(batch);
```

## <a name="issue-a-search-request"></a>検索要求を発行する

セキュリティによるトリミングが目的の場合、インデックスのセキュリティ フィールドの値は、検索結果にドキュメントを含めるか除外するために使われる静的な値です。 たとえば、入学者選考のグループ ID が "A11B22C33D44-E55F66G77-H88I99JKK" の場合、Azure Cognitive Search インデックスのセキュリティ フィールドがこの識別子であるドキュメントは、要求元に返送される検索結果に含まれるか、または検索結果から除外されます。

要求を発行したユーザーのグループに基づいて検索結果で返されるドキュメントをフィルター処理するには、次の手順のようにします。

### <a name="step-1-retrieve-users-group-identifiers"></a>手順 1:ユーザーのグループ識別子を取得する

ユーザーのグループがまだキャッシュされていない場合、またはキャッシュの有効期限を過ぎている場合は、[groups](/graph/api/directoryobject-getmembergroups) 要求を発行します。

```csharp
private static async void RefreshCache(IEnumerable<User> users)
{
    HttpClient client = new HttpClient();
    var userGroups = await _microsoftGraphHelper.GetGroupsForUsers(client, users);
    _groupsCache = new ConcurrentDictionary<string, List<string>>(userGroups);
}
```

### <a name="step-2-compose-the-search-request"></a>手順 2:検索要求を作成する

ユーザーのグループ メンバーシップがある場合は、適切なフィルター値を指定して検索要求を発行できます。

```csharp
private static void SearchQueryWithFilter(string user)
{
    // Using the filter below, the search result will contain all documents that their GroupIds field   
    // contain any one of the Ids in the groups list
    string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", String.Join(",", _groupsCache[user])));
    SearchOptions searchOptions =
        new SearchOptions()
        {
            Filter = filter
        };
    searchOptions.Select.Add("name");

    SearchResults<SecuredFiles> results = searchClient.Search<SecuredFiles>("*", searchOptions);

    Console.WriteLine("Results for groups '{0}' : {1}", _groupsCache[user], results.GetResults().Select(r => r.Document.Name));
}
```

### <a name="step-3-handle-the-results"></a>手順 3:結果を処理する

応答には、ユーザーが表示アクセス許可を持つドキュメントだけにフィルター処理されたリストが含まれます。 検索結果ページの作成方法によっては、フィルター処理された結果セットを反映するための視覚的な合図を含めることができます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure AD のサインインを使用して Azure Cognitive Search の結果のドキュメントをフィルター処理し、要求で指定されたフィルターに一致しないドキュメントの結果をトリミングするパターンを説明しました。 より単純な代替パターン、または他のセキュリティ機能を再確認する方法については、次のリンクを参照してください。

- [結果をトリミングするためのセキュリティ フィルター](search-security-trimming-for-azure-search.md)
- [Azure Cognitive Search のセキュリティ](search-security-overview.md)