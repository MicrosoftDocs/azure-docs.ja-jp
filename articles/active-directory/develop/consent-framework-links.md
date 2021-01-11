---
title: アプリケーションの同意のしくみ
description: Azure AD の同意フレームワークについて、Azure AD でアプリケーションを開発する場合の使用方法とそのしくみを詳しく説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.openlocfilehash: 15270a998aff174c04acf2969d984eb022852635
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88117364"
---
# <a name="how-application-consent-works"></a>アプリケーションの同意のしくみ

この記事では、アプリケーションをより効果的に開発するための Azure AD の同意フレームワークのしくみについて詳しく説明します。

## <a name="recommended-documents"></a>推奨されるドキュメント

- [アプリケーションによるリソースへのアクセスの管理を同意でリソース所有者に許可する方法](./developer-glossary.md#consent)に関する概要を理解してください。
- [Azure AD の同意フレームワークの実装方法](./quickstart-register-app.md)の詳細な手順を入手します。
- 詳細については、[マルチテナント アプリケーションが同意フレームワークを使用して](./howto-convert-app-to-be-multi-tenant.md)多層アプリケーションのパターンのサポートが強化されている、"user" と "admin" の同意を実装する方法について学習します。
- 詳細については、[認証コードの付与フロー中に、OAuth 2.0 プロトコル層で同意がどのようにサポートされるか、学習してください。](../azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code)

## <a name="next-steps"></a>次のステップ
[Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)