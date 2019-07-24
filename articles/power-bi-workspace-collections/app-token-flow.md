---
title: Power BI ワークスペース コレクションでの認証と承認 | Microsoft Docs
description: Power BI ワークスペース コレクションでの認証と承認について説明します。
services: power-bi-workspace-collections
author: markingmyname
ms.author: maghan
ms.service: power-bi-workspace-collections
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 5d7b5f2655fc94b9a060c30e11be66bd2eacdee8
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520617"
---
# <a name="authenticating-and-authorizing-with-power-bi-workspace-collections"></a>Power BI ワークスペース コレクションでの認証と承認

Power BI ワークスペース コレクション サービスでは、明示的なエンドユーザー認証ではなく、**キー**と**アプリケーション トークン**を使用して認証と承認を行います。 このモデルでは、アプリケーションがエンド ユーザーの認証と承認を管理します。 アプリケーションは、必要に応じてアプリケーション トークンを作成して送信し、要求されたレポートをレンダリングするようサービスに伝えます。 この設計では、アプリケーションでユーザーの認証と承認に Azure Active Directory を使用する必要はありません (ただし、Azure Active Directory を使用することもできます)。

> [!IMPORTANT]
> Power BI ワークスペース コレクションは非推奨となっており、2018 年 6 月または契約に定める日までに限り利用できます。 アプリケーションの中断を避けるため、Power BI Embedded への移行をご検討ください。 Power BI Embedded にデータを移行する方法については、[Power BI ワークスペース コレクション コンテンツを Power BI Embedded に移行する方法](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)に関するページを参照してください。

## <a name="two-ways-to-authenticate"></a>2 つの認証方法

**キー** - Power BI ワークスペース コレクションのすべての REST API 呼び出しでキーを使用できます。 このキーは **Microsoft Azure Portal** の **[すべての設定]**、**[アクセス キー]** にあります。 キーは常にパスワードと同様に扱ってください。 これらのキーは、特定のワークスペース コレクションで REST API 呼び出しを行うアクセス許可を持ちます。

REST 呼び出しでキーを使用するには、次の Authorization ヘッダーを追加します。

    Authorization: AppKey {your key}

**アプリケーション トークン** - アプリケーション トークンは、すべての組み込み要求に使用されます。 アプリケーション トークンはクライアント側で実行されるように設計されているため、1 つのレポートに制限されます。アプリケーション トークンには有効期限を設定するのがベスト プラクティスです。

アプリケーション トークンは、いずれかのキーで署名された JWT (JSON Web トークン) です。

アプリケーション トークンには、次の要求を含めることができます。

| 要求 | 説明 |    
| --- | --- |
| **ver** |アプリケーション トークンのバージョン。 0.2.0 が現行バージョンです。 |
| **aud** |トークンの対象となる受信者。 Power BI ワークスペース コレクションの場合は *https:\//analysis.windows.net/powerbi/api* を使用します。 |
| **iss** |トークンを発行したアプリケーションを示す文字列。 |
| **type** |作成されるアプリケーション トークンの種類。 現在サポートされている種類は **embed**だけです。 |
| **wcn** |トークンの発行対象であるワークスペース コレクション名。 |
| **wid** |トークンの発行対象であるワークスペース ID。 |
| **rid** |トークンの発行対象であるレポート ID。 |
| **username** (省略可能) |RLS で使用されます。これは、RLS のルールを適用するときにユーザーを特定するのに役立つ文字列です。 |
| **roles** (省略可能) |行レベルのセキュリティのルールを適用するときに選択したロールを含む文字列。 複数のロールを渡す場合は、文字列配列として渡す必要があります。 |
| **scp** (省略可能) |アクセス許可スコープを含む文字列。 複数のロールを渡す場合は、文字列配列として渡す必要があります。 |
| **exp** (省略可能) |トークンの有効期限を示します。 この値は Unix タイムスタンプとして渡す必要があります。 |
| **nbf** (省略可能) |トークンの有効期間の開始時刻を示します。 この値は Unix タイムスタンプとして渡す必要があります。 |

サンプルのアプリケーション トークンは、次のようになります。

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsInR5cGUiOiJlbWJlZCIsIndjbiI6Ikd1eUluQUN1YmUiLCJ3aWQiOiJkNGZlMWViMS0yNzEwLTRhNDctODQ3Yy0xNzZhOTU0NWRhZDgiLCJyaWQiOiIyNWMwZDQwYi1kZTY1LTQxZDItOTMyYy0wZjE2ODc2ZTNiOWQiLCJzY3AiOiJSZXBvcnQuUmVhZCIsImlzcyI6IlBvd2VyQklTREsiLCJhdWQiOiJodHRwczovL2FuYWx5c2lzLndpbmRvd3MubmV0L3Bvd2VyYmkvYXBpIiwiZXhwIjoxNDg4NTAyNDM2LCJuYmYiOjE0ODg0OTg4MzZ9.v1znUaXMrD1AdMz6YjywhJQGY7MWjdCR3SmUSwWwIiI
```

デコードされると、次のようになります。

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

SDK には、アプリケーション トークンの作成を容易にするメソッドが用意されています。 たとえば、.NET では、[Microsoft.PowerBI.Security.PowerBIToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken) クラスと [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) メソッドを使用できます。

.NET SDK については、[Scopes](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.scopes) に関するページを参照してください。

## <a name="scopes"></a>スコープ

埋め込みトークンを使用する場合は、アクセス権を与えたリソースの使用を制限することができます。 このため、スコープを持つアクセス許可が与えられたトークンを生成できます。

Power BI ワークスペース コレクションで使用可能なスコープを次に示します。

|Scope (スコープ)|説明|
|---|---|
|Dataset.Read|指定されたデータセットに対する読み取りアクセス許可を提供します。|
|Dataset.Write|指定したデータセットに対する書き込みアクセス許可を提供します。|
|Dataset.ReadWrite|指定したデータセットに対する読み取りおよび書き込みアクセス許可を提供します。|
|Report.Read|指定されたレポートに対する表示アクセス許可を提供します。|
|Report.ReadWrite|指定されたレポートに対する表示および編集アクセス許可を提供します。|
|Workspace.Report.Create|指定されたワークスペース内で新しいレポートを作成するアクセス許可を提供します。|
|Workspace.Report.Copy|指定されたワークスペース内で既存のレポートを複製するアクセス許可を提供します。|

次のようにスコープ間をスペースで区切ると、複数のスコープを指定できます。

```csharp
string scopes = "Dataset.Read Workspace.Report.Create";
```

**必須の要求 - scopes**

ワークスペース リソース (Report、Dataset など) への許可されたアクセス許可を示す scp: {scopesClaim} scopesClaim には、文字列または文字列の配列を指定できます。

スコープが定義されているデコードされたトークンは、次のようになります。

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "scp": "Report.Read",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

### <a name="operations-and-scopes"></a>操作とスコープ

|Operation|ターゲット リソース|トークン アクセス許可|
|---|---|---|
|データセットに基づいて新しいレポートを (メモリ内で) 作成する。|Dataset|Dataset.Read|
|データセットに基づいて新しいレポートを (メモリ内で) 作成し、レポートを保存する。|Dataset|* Dataset.Read<br>* Workspace.Report.Create|
|既存のレポートを表示し、閲覧または (メモリ内で) 編集する。 Report.Read は Dataset.Read を暗黙的に示します。 Report.Read は編集の保存を許可しません。|レポート|Report.Read|
|既存のレポートを編集および保存する。|レポート|Report.ReadWrite|
|レポートのコピーを保存する (名前を付けて保存)。|レポート|* Report.Read<br>* Workspace.Report.Copy|

## <a name="heres-how-the-flow-works"></a>フローのしくみ
1. アプリケーションに API キーをコピーします。 このキーは **Azure Portal** で取得できます。
   
    ![Azure Portal の API キーの場所](media/get-started-sample/azure-portal.png)
1. トークンが要求をアサートします。トークンには有効期限があります。
   
    ![アプリケーション トークンのフロー - トークンが要求をアサートする](media/get-started-sample/token-2.png)
1. トークンが API アクセス キーで署名されます。
   
    ![アプリケーション トークンのフロー - トークンが署名される](media/get-started-sample/token-3.png)
1. ユーザーがレポートの表示を要求します。
   
    ![アプリケーション トークンのフロー - ユーザーがレポートの表示を要求する](media/get-started-sample/token-4.png)
1. トークンが API アクセス キーで検証されます。
   
   ![アプリケーション トークンのフロー - トークンが検証される](media/get-started-sample/token-5.png)
1. Power BI ワークスペース コレクションがユーザーにレポートを送信します。
   
   ![アプリケーション トークンのフロー - サービスがユーザーにレポートを送信する](media/get-started-sample/token-6.png)

**Power BI ワークスペース コレクション**がユーザーにレポートを送信したら、ユーザーはカスタム アプリケーションでレポートを表示できます。 たとえば、[Analyzing Sales Data PBIX サンプル](https://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix)をインポートした場合、サンプル Web アプリが次のように表示されます。

![アプリケーションに埋め込まれたレポートのサンプル](media/get-started-sample/sample-web-app.png)

## <a name="see-also"></a>関連項目

[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[Microsoft Power BI ワークスペース コレクションのサンプルの使用](get-started-sample.md)  
[Microsoft Power BI ワークスペース コレクションの一般的なシナリオ](scenarios.md)  
[Microsoft Power BI ワークスペース コレクションの概要](get-started.md)  
[PowerBI-CSharp Git リポジトリ](https://github.com/Microsoft/PowerBI-CSharp)

ご質問は、 [Power BI コミュニティ](https://community.powerbi.com/)で尋ねてみてください。
