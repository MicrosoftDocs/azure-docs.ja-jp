---
title: v1.0 について | Azure
description: この記事では、Azure Active Directory v1.0 エンドポイントおよびプラットフォームを使用した Microsoft の職場および学校アカウントのサインインの概要について説明します。
services: active-directory
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd39ee1dc22fb6143a8d098276172c453388170b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56163990"
---
# <a name="about-v10"></a>v1.0 について

Azure Active Directory (Azure AD) はクラウドの ID サービスです。開発者はこのサービスを使って、Microsoft の職場または学校アカウントによる安全なサインインをユーザーに提供するアプリを構築できます。 Azure AD は、シングル テナント アプリと基幹業務 (LOB) アプリを構築する開発者、さらには、マルチテナント アプリの開発を目指す開発者を支援します。 Azure AD を使用することで、基本的なサインイン機能が得られることに加え、Microsoft の API ([Microsoft Graph](https://docs.microsoft.com/graph/overview) など) のほか、Azure AD プラットフォーム上に構築されたカスタム API をアプリから呼び出せるようになります。 このドキュメントでは、OAuth 2.0 や OpenID Connect といった業界標準のプロトコルを使用して、アプリケーションに Azure AD のサポートを追加する方法を示します。

> [!NOTE]
> このページの内容の大部分では、v1.0 エンドポイントおよびプラットフォームを重点的に取り上げていますが、このエンドポイントおよびプラットフォームでサポートされるのは、Microsoft の職場または学校アカウントだけです。 コンシューマー用または個人用 Microsoft アカウントでのサインインに対応する必要がある場合は、[v2.0 エンドポイントおよびプラットフォーム](v2-overview.md)に関する情報を参照してください。 v2.0 エンドポイントでは、Microsoft のすべての ID でサインイン可能なアプリを開発できるように、統合された開発者エクスペリエンスが提供されています。

| | |
| --- | --- |
|[認証の基本](authentication-scenarios.md) | Azure AD での認証の概要。 |
|[アプリケーションの種類](app-types.md) | Azure AD でサポートされる認証シナリオの概要。 |
| | |

## <a name="get-started"></a>作業開始

v1.0 のクイック スタートおよびチュートリアルでは、Azure AD Authentication Library (ADAL) SDK を使って好みのプラットフォームでアプリを構築する方法がわかりやすく説明されています。 作業を開始するには、「[Microsoft ID プラットフォーム (開発者向け Azure Active Directory)](index.yml)」の **v1.0 のクイック スタート**と **v1.0 のチュートリアル**に関するセクションを参照してください。

## <a name="how-to-guides"></a>ハウツー ガイド

詳細な情報と Azure AD で最も一般的なタスクのチュートリアルについては、**v1.0 のハウツー ガイド**を参照してください。

## <a name="reference-topics"></a>参照トピック

次の記事には、Azure AD で使用される API、プロトコル メッセージ、用語に関する詳細な情報が記載されています。

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [認証ライブラリ (ADAL)](active-directory-authentication-libraries.md)   | Azure AD が提供するライブラリと SDK の概要。 |
| [コード サンプル](sample-v1-code.md)                                  | Azure AD のコード サンプルの全一覧。 |
| [用語集](developer-glossary.md)                                      | このドキュメント全体で使用されている用語と語句の定義。 |
|  |  |


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
