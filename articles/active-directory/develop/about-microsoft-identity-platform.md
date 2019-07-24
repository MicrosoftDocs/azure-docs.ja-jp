---
title: Microsoft ID プラットフォームについて | Azure
description: Azure Active Directory の ID サービスおよび開発者プラットフォームの進化版である Microsoft ID プラットフォームについて説明します。
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c7b3eee08c036862e6ce9f0c590a596f7b1d3fb0
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59258693"
---
# <a name="about-microsoft-identity-platform"></a>Microsoft ID プラットフォームについて

Microsoft ID プラットフォームは、Azure Active Directory (Azure AD) の ID サービスおよび開発者プラットフォームの進化版です。 これにより、開発者はすべての Microsoft ID にサインインし、Microsoft Graph、その他の Microsoft API、または開発者が作成した API を呼び出すトークンを取得することができます。 これは多彩な機能を備えたプラットフォームであり、認証サービス、オープン ソース ライブラリ、(開発者ポータルとアプリケーション API による) アプリケーションの登録と構成 、完全な開発者向けドキュメント、サンプル コード、およびその他の開発者向けコンテンツによって構成されています。 Microsoft ID プラットフォームでは、OAuth 2.0 や OpenID Connect など業界標準のプロトコルがサポートされています。

これまで、ほとんどの開発者は Azure AD v1.0 プラットフォームを使用して Azure AD のID (職場や学校のアカウント) を認証してきました。これは、Azure AD Authentication Library (ADAL)、アプリケーションの登録と構成用の Azure ポータル、およびプログラムによるアプリケーション構成用の Azure AD Graph API を 使用して、Azure AD v1.0 エンドポイントからトークンを要求することによって行われていました。 Azure AD v1.0 プラットフォームは成熟したプラットフォーム オファリングであり、エンタープライズ アプリケーションでも引き続き動作します。

マイクロソフト ID プラットフォームの機能を拡張して進化させるために、Azure AD v2.0　エンドポイントと呼ばれているものを使用して、より広範な Microsoft ID (Azure AD の ID、Microsoft アカウント (outlook.com や hotmail.com など)、および Azure AD B2C によるソーシャル アカウントとローカル アカウント) を認証できるようになりました。 ここでは、Microsoft 認証ライブラリ (MSAL) またはオープンソースの OAuth2.0 または OpenID Connect ライブラリ、アプリケーションの登録と構成用の Azure ポータル、およびプログラムによるアプリケーション構成用の Microsoft Graph API を使用します。 更新された Microsoft IDプラットフォーム (特に、MSAL ライブラリと最新の Azure ポータル アプリ登録エクスペリエンス) は、過去 1 年にわたって大きく進化してきました。 このリリースを完成させるために、開発者は最新の Microsoft ID プラットフォームを使用してアプリケーションを開発し、テストすることをお勧めします。

最新の ADAL と最新の MSAL を使用するアプリケーションは、相互にシングル サインオンします。 ADAL から MSAL に更新されたアプリケーションは、ユーザーのサインイン状態を維持します。 ADAL で構築されたアプリケーションは引き続き機能し、サポートされるので、開発者は、環境にあわせて、アプリケーションを MSAL に更新することができます。

## <a name="microsoft-identity-platform-experience"></a>Microsoft ID プラットフォーム エクスペリエンス

次の図は、アプリ登録エクスペリエンス、SDK、エンドポイント、サポートされている ID など、高レベルでの Microsoft ID エクスペリエンスを示しています。

![現在の Microsoft ID プラットフォーム](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

Microsoft ID プラットフォームには、2 つのエンドポイント (v1.0 および v2.0) と、これらのエンドポイントを処理する 2 組のクライアント ライブラリがあります。 新しいアプリケーションを開発する際は、これらのエンドポイントと認証ライブラリの利点と現在の状態を考慮してください。 また、以下の点についても考慮してください。

* サポートされるプラットフォーム

    * [ADAL](active-directory-authentication-libraries.md) は、.NET、JavaScript、iOS、Android、Java、および Python をサポートしています
    * [MSAL プレビュー](reference-v2-libraries.md)は、.NET、JavaScript、iOS、および Android をサポートしています
    * 両方のエンドポイントは、API とサインインを保護するために .NET と Node.js サーバーミドルウェアをサポートしています。 

* v2.0 エンドポイントと MSAL で、動的同意や増分同意などの各種の技術革新を実現しながら、v1.0 と ADAL も引き続きサポートしていきます。

    Azure portal では、以前はアプリに必要なすべてのスコープを静的に識別しなければなりませんでした。 v2.0 エンドポイントとこのエンドポイントに関連付けられたポータルを使用すると、以前と同じようにスコープを静的に定義したり、アプリでアクセス許可が必要なときに動的にスコープを要求することができます。 動的同意に加えて、もう 1 つのオプション機能として、増分同意が用意されています。 増分同意を使用すると、ユーザーが初めて認証するときに必要になるスコープのサブセットを要求したり、必要に応じて追加のスコープを要求することができます。 
    
    たとえば、モバイル デバイスでカメラ アプリを使用する場合、アプリからカメラへのアクセスの許可をユーザーが求められ、ユーザーが同意した後でのみ、アプリがカメラにアクセスして写真を撮ることができるようになります。  アプリで新しい写真を保存する準備ができたときに、写真の読み取り/書き込みのアクセス許可を求めることができます。 

* 考えられる重大な変更

    MSAL は、運用環境での使用に適しています。 MSAL には、現在の運用ライブラリと同様の運用レベル サポートが提供されています。 プレビュー中に、このライブラリの API、内部キャッシュ フォーマット、およびその他のメカニズムが変更されることがあり、それらをバグ フィックスや機能改善とともに取り込むことが必要になります。 これは、アプリケーションに影響を与える場合があります。 たとえば、キャッシュ フォーマットの変更は、再度サインインが必要になるなどユーザーに影響を与える場合があります。 API の変更では、コードの更新が必要な場合があります。 リリースの一般提供 (general availability: GA) が開始されると、プレビュー バージョンのライブラリを使用して作成されたアプリケーションが機能しなくなるため、6 か月以内に GA バージョンに更新していただく必要があります。

## <a name="next-steps"></a>次の手順

v1.0 および v2.0 に関する詳細。

* [v1.0 について](v1-overview.md)
* [v2.0 について](v2-overview.md)
