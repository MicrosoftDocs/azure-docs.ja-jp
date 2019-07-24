---
title: 各国のクラウドでの Azure AD を使用した認証
description: 各国のクラウドのアプリ登録と認証エンドポイントについて説明します。
services: active-directory
documentationcenter: ''
author: negoe
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/20/2018
ms.author: negoe
ms.reviewer: negoe,andret,saeeda,CelesteDG
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4feaf97de7b833514113af6c91b3745be0503eff
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58113687"
---
# <a name="national-clouds"></a>各国のクラウド

各国のクラウド (ソブリン クラウドとも呼ばれます) は、物理的に分離された Azure インスタンスです。 Azure のこれらのリージョンは、データの保存場所、主権、およびコンプライアンス要件が地理的な境界内で確実に守られるように設計されています。

グローバルなクラウドを含む Azure Active Directory は、次の各国のクラウドにデプロイされています。  

- Azure US Government
- Azure Germany
- Azure China 21Vianet

各国のクラウドは、特有で、Azure グローバルとは異なる環境です。 そのため、アプリケーションの登録、トークンの取得、エンドポイントの構成など、これらの環境に向けたアプリケーションの開発時には、いくつかの主な相違点について知っておくことが重要です。

## <a name="app-registration-endpoints"></a>アプリ登録エンドポイント

各国のクラウドには、それぞれ別個の Azure portal があります。 各国のクラウド内でアプリケーションを Microsoft ID プラットフォームと統合するには、各環境に固有の Azure portal それぞれで、アプリケーションを別個に登録する必要があります。

次の表は、各国のクラウドにアプリケーションを登録するために使用される Azure Active Directory (Azure AD) エンドポイントのベース URL を示しています。

| 各国のクラウド | Azure AD ポータル エンドポイント
| --- | --- |
| 米国政府向け Azure AD |`https://portal.azure.us`
|Azure AD Germany |`https://portal.microsoftazure.de`
|21Vianet が運営する Azure AD China |`https://portal.azure.cn`
|Azure AD (グローバル サービス)|`https://portal.azure.com` 

## <a name="azure-ad-authentication-endpoints"></a>Azure AD 認証エンドポイント

各国のクラウドはすべて、各環境で別個にユーザーを認証し、別個の認証エンドポイントを備えています。

次の表は、各国のクラウドでトークンを取得するために使用される Azure Active Directory (Azure AD) エンドポイントのベース URL を示しています。

| 各国のクラウド | Azure AD 認証エンドポイント
| --- | --- |
| 米国政府向け Azure AD |`https://login.microsoftonline.us`
|Azure AD Germany| `https://login.microsoftonline.de`
|21Vianet が運営する Azure AD China | `https://login.chinacloudapi.cn`
|Azure AD (グローバル サービス)|`https://login.microsoftonline.com`

- Azure AD の承認またはトークン エンドポイントに対する要求は、リージョン固有の適切なベース URL を使用して構成できます。 例として、Azure Germany の場合を示します。

  - 承認共通エンドポイントは `https://login.microsoftonline.de/common/oauth2/authorize` です。
  - トークン共通エンドポイントは `https://login.microsoftonline.de/common/oauth2/token` です。

- シングルテナント アプリケーションの場合は、前の URL の common をテナント ID または名前 (たとえば `https://login.microsoftonline.de/contoso.com`) に置き換えます。

>[!NOTE]
> [Azure AD v2.0 承認]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview)およびトークン エンドポイントは、グローバル サービスにのみ使用できます。 各国のクラウドのデプロイではまだサポートされていません。

## <a name="microsoft-graph-api"></a>Microsoft Graph API

各国のクラウド環境で Microsoft Graph API を呼び出す方法については、[各国のクラウドでの Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/deployments) に関するページを参照してください。



> [!IMPORTANT]
> グローバル サービスの特定のリージョンにある特定のサービスと機能は、各国のクラウドでは使用できない場合もあります。 どのサービスを利用できるかを確認するには、[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

- [Azure Government](https://docs.microsoft.com/azure/azure-government/) の詳細を確認します。
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/) の詳細を確認します。
- [Azure Germany](https://docs.microsoft.com/azure/germany/) の詳細を確認します。
- [Azure AD 認証の基本](authentication-scenarios.md)の詳細を確認します。
