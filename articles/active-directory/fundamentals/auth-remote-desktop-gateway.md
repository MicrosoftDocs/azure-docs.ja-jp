---
title: Azure Active Directory を使用したリモート デスクトップ ゲートウェイ サービス
description: Azure Active Directory を使用したリモート デスクトップ ゲートウェイ サービスの取得に関するアーキテクチャ ガイダンス。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4baaf2de6fbe4a56f64d449644b8594217dc432c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96172739"
---
# <a name="remote-desktop-gateway-services"></a>リモート デスクトップ ゲートウェイ サービス

リモート デスクトップ service (RDS) の標準デプロイには、Windows Server で実行されるさまざまな[リモート デスクトップ ロール サービス](/windows-server/remote/remote-desktop-services/Desktop-hosting-logical-architecture)が含まれます。 Azure Active Directory (Azure AD) アプリケーション プロキシを使用した RDS デプロイには、コネクタ サービスが実行されているサーバーからの永続的な送信接続を使用します。 その他のデプロイでは、ロード バランサーを介した着信接続が開いたままになります。 この認証パターンを使用すると、リモート デスクトップ サービスを通じてオンプレミスのアプリケーションを発行することで、さらに多くのアプリケーションを提供することができます。 また、Azure AD アプリケーション プロキシを使用することで、デプロイの攻撃面を減少させることもできます。

## <a name="use-when"></a>使用する状況

リモート アクセスを提供して、事前認証によってリモート デスクトップ サービスのデプロイを保護する必要がある場合。

![アーキテクチャ図](./media/authentication-patterns/rdp-auth.png)

## <a name="components-of-system"></a>システムのコンポーネント

* **[ユーザー]** :アプリケーション プロキシによって提供される RDS にアクセスします。

* **Web ブラウザー**: アプリケーションの外部 URL にアクセスするためにユーザーが操作するコンポーネントです。

* **Azure AD**:ユーザーを認証します。 

* **アプリケーション プロキシ サービス**: ユーザーから RDS に要求を転送するリバース プロキシとして機能します。 アプリケーション プロキシは、任意の条件付きアクセス ポリシーを適用することもできます。 

* **リモート デスクトップ サービス**: セキュリティで保護されたモバイルおよびリモートでのデスクトップ アクセスを提供し、エンド ユーザーがアプリケーションとデスクトップをクラウドから実行できるようにして、個々の仮想化アプリケーションのプラットフォームとして機能します。 

## <a name="implement-remote-desktop-gateway-services-with-azure-ad"></a>Azure AD を使用したリモート デスクトップ ゲートウェイ サービスを実装する

* [Azure AD アプリケーション プロキシを使用したリモート デスクトップの発行](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md) 

* [Azure AD でアプリケーション プロキシを使用したリモート アクセスを行うためにオンプレミス アプリケーションを追加する](../manage-apps/application-proxy-add-on-premises-application.md)

