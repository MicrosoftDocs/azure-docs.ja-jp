---
title: "Power BI Embedded での認証と承認"
description: "Power BI Embedded での認証と承認"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 1c1369ea-7dfd-4b6e-978b-8f78908fd6f6
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 03/11/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 93027f0f5467e0b21c47bbcbc84c67cdd50b26b8
ms.lasthandoff: 03/14/2017


---
# <a name="authenticating-and-authorizing-with-power-bi-embedded"></a>Power BI Embedded での認証と承認

Power BI Embedded サービスでは、明示的なエンドユーザー認証ではなく、**キー**と**アプリケーション トークン**を使用して認証と承認を行います。 このモデルでは、アプリケーションがエンドユーザーの認証と承認を管理します。 アプリケーションは、必要に応じてアプリケーション トークンを作成して送信し、要求されたレポートをレンダリングするようサービスに伝えます。 この設計では、アプリケーションでユーザーの認証と承認に Azure Active Directory を使用する必要はありません (ただし、Azure Active Directory を使用することもできます)。

## <a name="two-ways-to-authenticate"></a>2 つの認証方法

**キー** - キーは、すべての Power BI Embedded REST API 呼び出しに使用できます。 キーを取得するには、**Azure Portal**で **[すべての設定]**、**[アクセス キー]** の順にクリックします。 キーは常にパスワードと同様に扱います。 これらのキーは、特定のワークスペース コレクションで REST API 呼び出しを行うアクセス許可を持ちます。

REST 呼び出しでキーを使用するには、次の Authorization ヘッダーを追加します。            

    Authorization: AppKey {your key}

**アプリケーション トークン** - アプリケーション トークンは、すべての組み込み要求に使用されます。 アプリケーション トークンはクライアント側で実行されるように設計されているため、1 つのレポートに制限されます。アプリケーション トークンには有効期限を設定するのがベスト プラクティスです。

アプリケーション トークンは、いずれかのキーで署名された JWT (JSON Web トークン) です。

アプリケーション トークンには、次の要求を含めることができます。

| 要求 | Description |
| --- | --- |
| **ver** |アプリケーション トークンのバージョン。 0.2.0 が現行バージョンです。 |
| **aud** |トークンの対象となる受信者。 Power BI Embedded では、"https://analysis.windows.net/powerbi/api" を使用します。 |
| **iss** |トークンを発行したアプリケーションを示す文字列。 |
| **type** |作成されるアプリケーション トークンの種類。 現在サポートされている種類は **embed**だけです。 |
| **wcn** |トークンの発行対象であるワークスペース コレクション名。 |
| **wid** |トークンの発行対象であるワークスペース ID。 |
| **rid** |トークンの発行対象であるレポート ID。 |
| **username** (省略可能) |RLS で使用されます。これは、RLS のルールを適用するときにユーザーを特定するのに役立つ文字列です。 |
| **roles** (省略可能) |行レベルのセキュリティのルールを適用するときに選択したロールを含む文字列。 複数のロールを渡す場合は、文字列配列として渡す必要があります。 |
| **scp** (省略可能) |アクセス許可スコープを含む文字列。 複数のロールを渡す場合は、文字列配列として渡す必要があります。 |
| **exp** (省略可能) |トークンの有効期限を示します。 これらは Unix タイムスタンプとして渡す必要があります。 |
| **nbf** (省略可能) |トークンの有効期間の開始時刻を示します。 これらは Unix タイムスタンプとして渡す必要があります。 |

サンプル アプリケーション トークンは次のようになります。

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsInR5cGUiOiJlbWJlZCIsIndjbiI6Ikd1eUluQUN1YmUiLCJ3aWQiOiJkNGZlMWViMS0yNzEwLTRhNDctODQ3Yy0xNzZhOTU0NWRhZDgiLCJyaWQiOiIyNWMwZDQwYi1kZTY1LTQxZDItOTMyYy0wZjE2ODc2ZTNiOWQiLCJzY3AiOiJSZXBvcnQuUmVhZCIsImlzcyI6IlBvd2VyQklTREsiLCJhdWQiOiJodHRwczovL2FuYWx5c2lzLndpbmRvd3MubmV0L3Bvd2VyYmkvYXBpIiwiZXhwIjoxNDg4NTAyNDM2LCJuYmYiOjE0ODg0OTg4MzZ9.v1znUaXMrD1AdMz6YjywhJQGY7MWjdCR3SmUSwWwIiI
```

これをデコードすると次のようになります。

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

SDK には、アプリケーション トークンの作成を容易にするメソッドが用意されています。 たとえば、.NET では、[Microsoft.PowerBI.Security.PowerBIToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken) クラスと [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_) メソッドを使用できます。

.NET SDK については、[Scopes](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.scopes) に関するページを参照してください。

## <a name="scopes"></a>スコープ

埋め込みトークンを使用する場合は、アクセス権を与えたリソースの使用を制限することができます。 このため、スコープを持つアクセス許可が与えられたトークンを生成できます。

Power BI Embedded の使用可能なスコープを次に示します。

|Scope|Description|
|---|---|
|Dataset.Read|指定されたデータセットに対する読み取りアクセス許可を提供します。|
|Dataset.Write|指定したデータセットに対する書き込みアクセス許可を提供します。|
|Dataset.ReadWrite|指定したデータセットに対する読み取りおよび書き込みアクセス許可を提供します。|
|Report.Read|指定されたレポートに対する表示アクセス許可を提供します。|
|Report.ReadWrite|指定されたレポートに対する表示および編集アクセス許可を提供します。|
|Workspace.Report.Create|指定されたワークスペース内で新しいレポートを作成するアクセス許可を提供します。|
|Workspace.Report.Copy|指定されたワークスペース内で既存のレポートを複製するアクセス許可を提供します。|

次のようにスコープ間をスペースで区切ると、複数のスコープを指定できます。

```
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

|操作|ターゲット リソース|トークン アクセス許可|
|---|---|---|
|データセットに基づいて新しいレポートを (メモリ内で) 作成する。|Dataset|Dataset.Read|
|データセットに基づいて新しいレポートを (メモリ内で) 作成し、レポートを保存する。|Dataset|*Dataset.Read<br>* Workspace.Report.Create|
|既存のレポートを表示し、閲覧または (メモリ内で) 編集する。 Report.Read は Dataset.Read を暗黙的に示します。 編集内容を保存するアクセス許可は許可されません。|レポート|Report.Read|
|既存のレポートを編集および保存する。|レポート|Report.ReadWrite|
|レポートのコピーを保存する (名前を付けて保存)。|レポート|*Report.Read<br>* Workspace.Report.Copy|

## <a name="heres-how-the-flow-works"></a>フローのしくみ
1. アプリケーションに API キーをコピーします。 このキーは **Azure ポータル**で取得できます。
   
    ![](media/powerbi-embedded-get-started-sample/azure-portal.png)
2. トークンが要求をアサートします。トークンには有効期限があります。
   
    ![](media/powerbi-embedded-get-started-sample/power-bi-embedded-token-2.png)
3. トークンが API アクセス キーで署名されます。
   
    ![](media/powerbi-embedded-get-started-sample/power-bi-embedded-token-3.png)
4. ユーザーがレポートの表示を要求します。
   
    ![](media/powerbi-embedded-get-started-sample/power-bi-embedded-token-4.png)
5. トークンが API アクセス キーで検証されます。
   
   ![](media/powerbi-embedded-get-started-sample/power-bi-embedded-token-5.png)
6. Power BI Embedded がユーザーにレポートを送信します。
   
   ![](media/powerbi-embedded-get-started-sample/power-bi-embedded-token-6.png)

**Power BI Embedded** がユーザーにレポートを送信したら、ユーザーはカスタム アプリケーションでレポートを表示できます。 たとえば、 [Analyzing Sales Data PBIX サンプル](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix)をインポートした場合、サンプル Web アプリが次のように表示されます。

![](media/powerbi-embedded-get-started-sample/sample-web-app.png)

## <a name="see-also"></a>関連項目

[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Get started with Microsoft Power BI Embedded sample (Microsoft Power BI Embedded のサンプルを使ってみる)](power-bi-embedded-get-started-sample.md)  
[Common Microsoft Power BI Embedded scenarios (Microsoft Power BI Embedded の一般的なシナリオ)](power-bi-embedded-scenarios.md)  
[Microsoft Power BI Embedded の概要](power-bi-embedded-get-started.md)  
[PowerBI-CSharp Git リポジトリ](https://github.com/Microsoft/PowerBI-CSharp)  
ご質問は、 [Power BI コミュニティ](http://community.powerbi.com/)で尋ねてみてください。


