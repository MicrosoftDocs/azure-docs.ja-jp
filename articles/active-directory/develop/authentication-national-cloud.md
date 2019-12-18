---
title: Azure AD の認証と各国のクラウド | Azure
titleSuffix: Microsoft identity platform
description: 各国のクラウドのアプリ登録と認証エンドポイントについて説明します。
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: negoe
ms.reviewer: negoe,celested
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 14b97677f5aa9624ba70696114ac34fcd9f46182
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74918034"
---
# <a name="national-clouds"></a>各国のクラウド

各国のクラウドは、物理的に分離された Azure のインスタンスです。 Azure のこれらのリージョンは、データの保存場所、主権、およびコンプライアンス要件が地理的な境界内で確実に守られるように設計されています。

グローバル クラウドを含め、Azure Active Directory (Azure AD) は次の各国のクラウドにデプロイされています。  

- Azure Government
- Azure Germany
- Azure China 21Vianet

各国のクラウドは独特であり、Azure グローバルとは別の環境です。 これらの環境用にアプリケーションを開発するときは、その主な違いに注意することが重要です。 これらの違いには、アプリケーションの登録、トークンの取得、およびエンドポイントの構成が含まれます。

## <a name="app-registration-endpoints"></a>アプリ登録エンドポイント

各国のクラウドには、それぞれ別個の Azure portal があります。 各国のクラウドでアプリケーションを Microsoft ID プラットフォームに統合するには、その環境に固有の各 Azure Portal で個別にアプリケーションを登録する必要があります。

次の表は、各国のクラウドごとにアプリケーションを登録するために使用される Azure AD エンドポイントのベース URL の一覧を示しています。

| 各国のクラウド | Azure AD ポータル エンドポイント |
|----------------|--------------------------|
| 米国政府向け Azure AD | `https://portal.azure.us` |
| Azure AD Germany | `https://portal.microsoftazure.de` |
| 21Vianet が運営する Azure AD China | `https://portal.azure.cn` |
| Azure AD (グローバル サービス) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Azure AD 認証エンドポイント

各国のクラウドはすべて、各環境で別個にユーザーを認証し、別個の認証エンドポイントを備えています。

次の表は、各国のクラウドごとにトークンを取得するために使用される Azure AD エンドポイントのベース URL の一覧を示しています。

| 各国のクラウド | Azure AD 認証エンドポイント |
|----------------|-------------------------|
| 米国政府向け Azure AD | `https://login.microsoftonline.us` |
| Azure AD Germany| `https://login.microsoftonline.de` |
| 21Vianet が運営する Azure AD China | `https://login.chinacloudapi.cn` |
| Azure AD (グローバル サービス)| `https://login.microsoftonline.com` |

適切なリージョン固有のベース URL を使用して、Azure AD 承認またはトークン エンドポイントへの要求を構成できます。 例として、Azure Germany の場合を示します。

  - 承認共通エンドポイントは `https://login.microsoftonline.de/common/oauth2/authorize` です。
  - トークン共通エンドポイントは `https://login.microsoftonline.de/common/oauth2/token` です。

シングルテナント アプリケーションの場合は、前の URL にある "common" をテナント ID またはテナント名に置き換えます。 例: `https://login.microsoftonline.de/contoso.com`。

## <a name="microsoft-graph-api"></a>Microsoft Graph API

各国のクラウドの環境で Microsoft Graph API を呼び出す方法については、[各国のクラウドのデプロイでの Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/deployments)に関するページを参照してください。

> [!IMPORTANT]
> グローバル サービスの特定のリージョンにある特定のサービスや機能が、一部の各国のクラウドでは使用できない場合があります。 どのようなサービスが使用できるかを見つけるには、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast)」を参照してください。

Microsoft ID プラットフォームを使用してアプリケーションを構築する方法を学習するには、[Microsoft Authentication Library (MSAL) のチュートリアル](msal-national-cloud.md)に従ってください。 具体的には、このアプリはユーザーをサインインさせ、Microsoft Graph API を呼び出すためのアクセス トークンを取得します。

## <a name="next-steps"></a>次の手順

各項目の詳細情報

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Germany](https://docs.microsoft.com/azure/germany/)
- [Azure AD 認証の基本](authentication-scenarios.md)
