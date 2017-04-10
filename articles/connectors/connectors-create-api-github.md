---
title: GitHub | Microsoft Docs
description: "Azure App Service を使用してロジック アプリを作成します。 GitHub は、 web ベースの Git リポジトリホスティングサービスです。 独自の機能を追加するだけでなく、Git の分散リビジョン コントロール機能とソース コード管理 (SCM) 機能をすべて提供します。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 8f873e6c-f4c0-4c2e-a5bd-2e953efe5e2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 6f336db8c8719b99420b353dca82e36a0d837769


---
# <a name="get-started-with-the-github-connector"></a>GitHub コネクタの使用
GitHub は、 web ベースの Git リポジトリホスティングサービスです。 独自の機能を追加するだけでなく、Git の分散リビジョン コントロール機能とソース コード管理 (SCM) 機能をすべて提供します。

> [!NOTE]
> 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。 
> 
> 

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)に関する記事を参照してください。

## <a name="triggers-and-actions"></a>トリガーとアクション
GitHub コネクタは、アクションとして使用できます。GitHub コネクタにはトリガーがあります。 すべてのコネクタは、JSON および XML 形式のデータに対応します。 

 GitHub コネクタでは、次のアクションやトリガーを使用できます。

### <a name="github-actions"></a>GitHub のアクション
実行できるアクションは以下のとおりです。

| アクション | 説明 |
| --- | --- |
| [CreateIssue](connectors-create-api-github.md#createissue) |問題を作成する |

### <a name="github-triggers"></a>GitHub のトリガー
次のイベントをリッスンできます。

| トリガー | Description |
| --- | --- |
| 問題が開かれた場合 |問題が開かれます |
| 問題が閉じられた場合 |問題が閉じられます |
| 問題が割当てられた場合 |問題が割当てられます |

## <a name="create-a-connection-to-github"></a>GitHub への接続を作成する
GitHub を使用してロジック アプリを作成するには、まず**接続**を作成してから、次のプロパティの詳細を指定する必要があります。 

| プロパティ | 必須 | 説明 |
| --- | --- | --- |
| トークン |はい |GitHub の資格情報を提供します |

接続を作成したら、接続を使用してアクションを実行し、この記事で説明するトリガーをリッスンできます。 

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 
> [!TIP]
> 他のロジック アプリでもこの接続を使用できます。
> 
> 

## <a name="reference-for-github"></a>GitHub のリファレンス
適用されるバージョン: 1.0

## <a name="createissue"></a>CreateIssue
問題の作成: 問題を作成します 

```POST: /repos/{repositoryOwner}/{repositoryName}/issues``` 

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| repositoryOwner |string |あり |path |なし |リポジトリの所有者です |
| repositoryName |string |あり |path |なし |リポジトリ名です |
| issueBasicDetails | |○ |body |なし |問題の詳細情報です |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 400 |正しくない要求 |
| 401 |権限がありません |
| 403 |許可されていません |
| 404 |見つかりません |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| default |操作に失敗しました。 |

## <a name="issueopened"></a>IssueOpened
問題が開かれた場合: 問題が開かれます 

```GET: /trigger/issueOpened``` 

この呼び出しには、パラメーターはありません

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 400 |正しくない要求 |
| 401 |権限がありません |
| 403 |許可されていません |
| 404 |見つかりません |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| default |操作に失敗しました。 |

## <a name="issueclosed"></a>IssueClosed
問題が閉じられた場合: 問題が閉じられます 

```GET: /trigger/issueClosed``` 

この呼び出しには、パラメーターはありません

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 400 |正しくない要求 |
| 401 |権限がありません |
| 403 |許可されていません |
| 404 |見つかりません |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| default |操作に失敗しました。 |

## <a name="issueassigned"></a>IssueAssigned
問題が割り当てられた場合: 問題が割り当てられます 

```GET: /trigger/issueAssigned``` 

この呼び出しには、パラメーターはありません

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 400 |正しくない要求 |
| 401 |権限がありません |
| 403 |許可されていません |
| 404 |見つかりません |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| default |操作に失敗しました。 |

## <a name="object-definitions"></a>オブジェクト定義
### <a name="issuebasicdetailsmodel"></a>IssueBasicDetailsModel
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| title |string |はい |
| body |string |はい |
| assignee |string |はい |

### <a name="issuedetailsmodel"></a>IssueDetailsModel
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| title |string |はい |
| body |string |はい |
| assignee |string |はい |
| number |string |なし |
| state |string |なし |
| created_at |string |なし |
| repository_url |string |なし |

## <a name="next-steps"></a>次のステップ
[ロジック アプリを作成します](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


