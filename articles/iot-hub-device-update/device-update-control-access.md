---
title: IoT Hub 用のデバイス更新の認証と認可を理解する | Microsoft Docs
description: IoT Hub 用のデバイス更新で Azure RBAC が使用され、ユーザーとサービス API の認証と認可が提供されるしくみについて説明します。
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 07310a5b6f275d4a35a3649c22aeea68045dde8b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101660506"
---
# <a name="azure-role-based-access-control-rbac-and-device-update"></a>Azure ロールベースのアクセス制御 (RBAC) とデバイス更新

デバイス更新では、Azure RBAC を使用し、ユーザーとサービス API の認証と認可が提供されます。

## <a name="configure-access-control-roles"></a>アクセス制御ロールの構成

他のユーザーや他のアプリケーションでデバイス更新を利用するには、ユーザーまたはアプリケーションにこのリソースへのアクセス権限を付与する必要があります。 デバイス更新でサポートされているロールは次のようになります

|   ロール名   | 説明  |
| :--------- | :---- |
|  デバイス更新管理者 | あらゆる デバイス更新リソースにアクセスできます  |
|  デバイス更新閲覧者| すべての更新プログラムとデプロイを表示できます |
|  デバイス更新コンテンツ管理者 | 更新プログラムを表示、インポート、削除できます  |
|  デバイス更新コンテンツ閲覧者 | 更新プログラムを表示できます  |
|  デバイス更新デプロイ管理者 | 更新プログラムのデバイスへのデプロイを管理できます|
|  デバイス更新デプロイ閲覧者| 更新プログラムのデバイスへのデプロイを表示できます |

ロールを組み合わせることで、正しいレベルのアクセス権限を提供できます。 たとえば、デバイス更新コンテンツ管理者ロールを利用することで開発者は更新プログラムをインポートしたり、管理したりできますが、更新プログラムの進捗状況を表示するにはデバイス更新デプロイ閲覧者ロールが必要になります。 これとは逆に、デバイス更新閲覧者ロールが付与されているソリューション オペレーターはあらゆる更新プログラムを表示できますが、特定の更新プログラムをデバイスにデプロイするにはデバイス更新デプロイ管理者ロールを使用する必要があります。


## <a name="authenticate-to-device-update-rest-apis-for-publishing-and-management"></a>公開と管理のためにデバイス更新 REST API に対して認証する

デバイス更新でも、サービス API を経由したコンテンツの公開や管理の認証に Azure AD が使用されます。 まず、クライアント アプリケーションを作成して構成する必要があります。

### <a name="create-client-azure-ad-app"></a>クライアント Azure AD アプリの作成

アプリケーションまたはサービスと Azure AD を統合するには、アプリケーションを Azure AD に[最初に登録します](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)。 クライアント アプリケーションのセットアップは、使用される認可フローによって異なります。  下の構成は、デバイス更新 REST API を使用する場合のガイダンスです。

* クライアントの認可を 'redirect URIs for native or web client' に設定します。
* API アクセス許可の設定 - IoT Hub 用のデバイス更新では次が公開されます。
  * 委任されたアクセス許可: 'user_impersonation'
  * **任意**、管理者の同意を付与する

[次の手順: デバイス更新リソースを作成し、アクセス制御ロールを構成する](./create-device-update-account.md)
