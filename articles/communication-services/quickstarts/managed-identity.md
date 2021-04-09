---
title: Communication Services でマネージド ID を使用する
titleSuffix: An Azure Communication Services quickstart
description: マネージド ID を使用すると、Azure VM、関数アプリ、その他のリソースで実行されているアプリケーションからの Azure Communication Services へのアクセスを承認できます。
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: peiliu
ms.reviewer: mikben
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: ffda88da451e25b79112a7adf85026158bd27acc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492355"
---
# <a name="use-managed-identities"></a>マネージド ID の使用
マネージド ID を使用して、Azure Communication Services を開始します。 Communication Services の ID および SMS クライアント ライブラリでは、[Azure リソースのマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を使用した Azure Active Directory (Azure AD) 認証がサポートされています。

このクイックスタートでは、マネージド ID をサポートする Azure 環境から、ID および SMS クライアント ライブラリへのアクセスを承認する方法について説明します。 また、開発環境でコードをテストする方法についても説明します。

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity-python.md)]
::: zone-end

## <a name="next-steps"></a>次のステップ

- [Azure のロールベースのアクセス制御の詳細について学習する](../../../articles/role-based-access-control/index.yml)
- [.NET 用 Azure ID ライブラリの詳細について学習する](/dotnet/api/overview/azure/identity-readme)
- [ユーザー アクセス トークンを作成する](../quickstarts/access-tokens.md)
- [SMS メッセージの送信](../quickstarts/telephony-sms/send.md)
- [SMS に関する詳細](../concepts/telephony-sms/concepts.md)
