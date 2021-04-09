---
title: Microsoft Graph API
description: RESTful Web API である Microsoft Graph API を使用すると、Microsoft クラウド サービスのリソースにアクセスできます。
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 02/13/2020
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: a799d7c32ad632303931adf838ca8e2e715b2adc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96860186"
---
# <a name="microsoft-graph-api"></a>Microsoft Graph API

RESTful Web API である Microsoft Graph API を使用すると、Microsoft クラウド サービスのリソースにアクセスできます。 アプリを登録し、ユーザーまたはサービスのための認証トークンを取得した後、Microsoft Graph API に要求を行うことができます。 詳しくは、「[Microsoft Graph の概要](/graph/overview)」をご覧ください。

Microsoft Graph では、次の Microsoft 365 サービスのデータにアクセスするための REST API とクライアント ライブラリが公開されています。
- Microsoft 365 サービス:Delve、Excel、Microsoft Bookings、Microsoft Teams、OneDrive、OneNote、Outlook/Exchange、Planner、SharePoint
- Enterprise Mobility + Security サービス: Advanced Threat Analytics、Advanced Threat Protection、Azure Active Directory、Identity Manager、Intune
- Windows 10 サービス: アクティビティ、デバイス、通知
- Dynamics 365 Business Central

## <a name="versions"></a>バージョン

現在、Microsoft Graph では v1.0 とベータの 2 つのバージョンがサポートされています。 v1.0 バージョンには、一般提供の API が含まれています。 すべての運用アプリでは、v1.0 を使用してください。 ベータ版には、現在プレビュー段階にある API が含まれています。 ベータ版 API では破壊的変更が行われる可能性があるため、ベータ版は開発中のアプリをテストする場合にのみ使用することをお勧めします。運用アプリではベータ版 API を使用しないでください。 詳しくは、「[Microsoft Graph のバージョン管理、サポートと重大な変更の方針](/graph/versioning-and-support)」をご覧ください。

ベータ版 API の使用を始めるには、「[Microsoft Graph ベータ版のエンドポイント リファレンス](/graph/api/overview?view=graph-rest-beta)」をご覧ください。

v1.0 API の使用を始めるには、「[Microsoft Graph REST API v1.0 リファレンス](/graph/api/overview)」をご覧ください。

## <a name="get-started"></a>はじめに

ユーザーやメール メッセージなどのリソースに対して読み取りまたは書き込みを行うには、次のような要求を作成します。

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

作成された要求の要素について詳しくは、「[Microsoft Graph API を使用する](/graph/use-the-api)」をご覧ください。

Microsoft Graph API の機能にアクセスする方法がわかるクイックスタート サンプルが用意されています。 使用可能なサンプルでは、1 つの認証を使用して次の 2 つのサービスにアクセスします: Microsoft アカウントと Outlook。 各クイックスタートでは、Microsoft アカウント ユーザーのプロファイルの情報にアクセスし、予定表のイベントを表示します。
クイックスタートには、次の 4 つの手順が含まれます。
- プラットフォームを選択する
- アプリ ID (クライアント ID) を取得する
- サンプルをビルドする
- サインインして、予定表のイベントを表示する

クイックスタートを完了すると、実行できるアプリが完成します。 詳しくは、「[Microsoft Graph のクイック スタートのよくあるご質問](/graph/quick-start-faq)」をご覧ください。 サンプルを使い始めるには、「[Microsoft Graph のクイック スタート](https://developer.microsoft.com/graph/quick-start)」をご覧ください。

## <a name="tools"></a>ツール

Web ベースのツールである Microsoft Graph エクスプローラーを利用すると、Microsoft Graph API を使用して要求を作成し、テストできます。 Microsoft Graph エクスプローラーには、`https://developer.microsoft.com/graph/graph-explorer` でアクセスできます。

Postman も、Microsoft Graph API を使用して要求を作成し、テストするために使用できるツールです。 Postman は `https://www.getpostman.com/` でダウンロードできます。 Postman で Microsoft Graph を操作するには、Postman の Microsoft Graph コレクションを使用します。 詳しくは、「[Microsoft Graph API で Postman を使用する](/graph/use-postman?context=graph%2Fapi%2Fbeta&view=graph-rest-beta)」をご覧ください。
