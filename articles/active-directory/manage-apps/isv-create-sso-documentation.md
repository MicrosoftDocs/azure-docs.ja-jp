---
title: アプリケーション用のシングル サインオンに関するドキュメントを作成して発行する
description: Azure Active Directory との統合に関する独立系ソフトウェア ベンダー向けガイダンス
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53221343ac606b6076cc9cc3cff6e0f96c1a3ac3
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659453"
---
# <a name="create-and-publish-single-sign-on-documentation-for-your-application"></a>アプリケーション用のシングル サインオンに関するドキュメントを作成して発行する   

## <a name="documentation-on-your-site"></a>サイト上のドキュメント

導入の容易さは、エンタープライズ ソフトウェアの決定において重要な要素です。 明確で分かりやすいドキュメントは、導入の過程でお客様の役に立ち、サポート コストも削減されます。 Microsoft は、数千のソフトウェア ベンダーと連携する過程で、どのようなものが有効かを見てきました。

サイト上のドキュメントには、少なくとも次の項目を含めることをお勧めします。

* SSO 機能の概要

  * サポートされるプロトコル

  * バージョンと SKU

  * サポートされる ID プロバイダーの一覧とドキュメント リンク

* アプリケーションのライセンス情報

* SSO を構成するためのロールベースのアクセス制御

* SSO の構成手順

  * SAML の UI 構成要素とプロバイダーからの予期される値

  * ID プロバイダーに渡すサービス プロバイダー情報

* OIDC/OAuth の場合

  * 業務上の正当な理由に同意するために必要なアクセス許可の一覧

* パイロット ユーザーのテスト手順

* エラー コードとメッセージを含むトラブルシューティング情報

* お客様のサポート メカニズム

## <a name="documentation-on-the-microsoft-site"></a>Microsoft サイト上のドキュメント

Azure Active Directory アプリケーション ギャラリーにアプリケーションを登録すると (これにより、Azure Marketplace でもアプリケーションが公開されます)、Microsoft により、段階的なプロセスを説明するために、共通のお客様向けのドキュメントが生成されます。 [こちら](https://aka.ms/appstutorial)で例を参照できます。 このドキュメントは、ギャラリーへの送信に基づいて作成されます。GitHub アカウントを使用してアプリケーションに変更を加える場合、このドキュメントを簡単に更新できます。

## <a name="next-steps"></a>次の手順

[Azure AD アプリケーション ギャラリーにアプリケーションを登録する](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)