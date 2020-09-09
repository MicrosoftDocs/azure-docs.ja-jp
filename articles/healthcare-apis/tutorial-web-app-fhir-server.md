---
title: Web アプリ チュートリアル - Azure API for FHIR をセットアップする
description: このチュートリアルでは、単純な Web アプリケーションをデプロイする例について説明します。 この最初のチュートリアルでは、Azure API for FHIR の前提条件とデプロイについて説明します
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 4b39c32da04efa3782cb2166c8e1137029b21258
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852958"
---
# <a name="deploy-javascript-app-to-read-data-from-fhir-service"></a>FHIR サービスからデータを読み取るために JavaScript アプリをデプロイする
このチュートリアルでは、小さな JavaScript アプリをデプロイします。このアプリは、FHIR サービスからデータを読み取ります。 このチュートリアルの手順は、次のとおりです。
1. FHIR サーバーをデプロイする
1. パブリック クライアント アプリケーションを登録する
1. アプリケーションへのアクセスをテストする
1. この FHIR データを読み取る Web アプリケーションを作成する

## <a name="prerequisites"></a>前提条件
このチュートリアルのセットを開始する前に、次の項目を用意する必要があります。
1. Azure サブスクリプション
1. Azure Active Directory テナント
1. インストールされている [Postman](https://www.getpostman.com/)

> [!NOTE]
> このチュートリアルでは、FHIR サービス、Azure AD アプリケーション、および Azure AD ユーザーは、すべて同じ Azure AD テナントにあります。 そうでない場合でも、このチュートリアルに従って作業できますが、いくつかの参照ドキュメントを調べて追加の手順を実行しなければならないことがあります。

## <a name="deploy-azure-api-for-fhir"></a>Azure API for FHIR をデプロイする
チュートリアルの最初の手順は、Azure API for FHIR を正しくセットアップすることです。

1. [Azure API for FHIR](fhir-paas-portal-quickstart.md) をデプロイします
1. Azure API for FHIR をデプロイしたら、Azure API for FHIR に移動し、CORS を選択して、[CORS](configure-cross-origin-resource-sharing.md) 設定を構成します。 
    1. **[配信元]** を * に設定します
    1. **[ヘッダー]** を * に設定します
    1. **[メソッド]** で **[すべて選択]** を選択します
    1. **[最長有効期間]** を **600** に設定します

## <a name="next-steps"></a>次の手順
これで、Azure API for FHIR がデプロイされたので、パブリック クライアント アプリケーションを登録する準備ができました。

>[!div class="nextstepaction"]
>[パブリック クライアント アプリケーションを登録する](tutorial-web-app-public-app-reg.md)
