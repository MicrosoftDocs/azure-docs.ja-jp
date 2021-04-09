---
title: ユーザー フローの API コネクタ コード サンプル - Azure AD
description: Azure Active Directory for External Identities のセルフサービス サインアップ フローにおける API コネクタのコード サンプルです。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3995395cfd6256689bedc7a4a3c83effc65c0b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87906936"
---
# <a name="samples-for-external-identities-self-service-sign-up"></a>External Identities のセルフサービス サインアップのサンプル

次の表に、[API コネクタ](api-connectors-overview.md)を使用したセルフサービス サインアップ ユーザー フローで Web API を活用するためのコード サンプルへのリンクを示します。

## <a name="api-connector-azure-function-quickstarts"></a>API コネクタを使用した Azure 関数のクイックスタート

| サンプル                                                                                                                          | 説明                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | この .NET Core Azure 関数サンプルには、特定のテナント ドメインへのサインアップを制限し、ユーザー指定の情報を検証する方法が示されています。 |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | この Node.js Azure 関数サンプルには、特定のテナント ドメインへのサインアップを制限し、ユーザー指定の情報を検証する方法が示されています。  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | この Python Azure 関数サンプルには、特定のテナント ドメインへのサインアップを制限し、ユーザー指定の情報を検証する方法が示されています。    |

<!-- \| [Java](../../azure-docs-pr/articles/active-directory/b2b/invite-internal-users.md#use-the-invitation-api-to-send-a-b2b-invitation) |  The sample below illustrates how to call the invitation API to invite an internal user as a B2B user. | -->

## <a name="custom-approval-workflows"></a>カスタム承認ワークフロー

| サンプル | 説明 |
|--------| ----------- |
| [手動承認ワークフロー](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connectors-approvals) | このサンプルでは、セルフサービス サインアップでゲスト ユーザー アカウントの作成を管理するためのエンド ツー エンドの承認ワークフローを示します |

## <a name="identity-verification"></a>本人確認

| サンプル                                                                                                            | 説明                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [IDology](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-idology-identity-verification) | このサンプルでは、API コネクタを使用して IDology と統合することで、セルフサービス サインアップの一部としてユーザー ID を確認する方法を示します。 |
| [Experian](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-experian-identity-verification) | このサンプルでは、API コネクタを使用して Experian と統合することで、セルフサービス サインアップの一部としてユーザー ID を確認する方法を示します。 |
