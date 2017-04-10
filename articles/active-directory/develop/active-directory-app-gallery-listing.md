---
title: "Azure Active Directory アプリケーション ギャラリーでのアプリケーションの表示"
description: "シングル サインオンをサポートするアプリケーションを Azure Active Directory ギャラリーに表示する方法 | Microsoft Azure"
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/07/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 28712a9898bf2449de53d21fd87f1ce53da71ff8
ms.openlocfilehash: df60c4737954e79ff860601412dffa58d4dbb471


---
# <a name="listing-your-application-in-the-azure-active-directory-application-gallery"></a>Azure Active Directory アプリケーション ギャラリーでのアプリケーションの表示
Azure Active Directory でのシングル サインオンをサポートするアプリケーション一覧を [Azure AD ギャラリー](https://azure.microsoft.com/marketplace/active-directory/all/)に表示するには、まず次の統合モードのいずれかをアプリケーションに実装する必要があります。

* **OpenID Connect** - 認証には OpenID Connect を使用し、構成には Azure AD Consent API を使用して Azure AD と直接統合します。 統合を始めたばかりで、アプリケーションが SAML をサポートしていない場合、このモードが推奨されます。
* **SAML** - SAML プロトコルを使用してサードパーティの ID プロバイダーを構成する機能が既にアプリケーションにある場合に選択します。

各モードの要件は次のとおりです。

## <a name="openid-connect-integration"></a>OpenID Connect 統合
アプリケーションを Azure AD と統合するには、 [開発者向けの手順](active-directory-authentication-scenarios.md)に従ってください。 また、次の情報を記載し、waadpartners@microsoft.com に送信してください。

* Azure AD チームが統合のテストに使用できるアプリケーションのテスト テナントまたはアカウントの資格情報を記載してください。  
* [Azure AD Consent フレームワーク](active-directory-integrating-applications.md#overview-of-the-consent-framework)を使用して Azure AD チームがサインインし、Azure AD のインスタンスをアプリケーションに接続する手順を説明してください。 
* Azure AD チームがアプリケーションでシングル サインオンをテストするために必要な詳細な手順があれば、説明してください。 
* 次の情報を入力してください。

> 会社名:
> 
> 会社の Web サイト:
> 
> アプリケーション名:
> 
> アプリケーションの説明 (256 文字以内):
> 
> アプリケーションの Web サイト (情報):
> 
> アプリケーションのテクニカル サポートの Web サイト、または連絡先情報:
> 
> https://portal.azure.com でアプリケーションの詳細に示す、アプリケーションのアプリケーション ID:
> 
> 顧客がアプリケーションにサインアップしたり、アプリケーションを購入したりする際にアクセスする、アプリケーションのサインアップ URL:
> 
> アプリケーションを表示するカテゴリを最大&3; つ選択してください (使用できるカテゴリについては、Azure Active Directory Marketplace を参照してください)。
> 
> アプリケーションの小さいアイコン (PNG ファイル、45 x 45px、単色の背景色) を添付してください:
> 
> アプリケーションの大きいアイコン (PNG ファイル、215 x 215px、単色の背景色) を添付してください:
> 
> アプリケーションのロゴ (PNG ファイル、150 x 122px、透明の背景色) を添付してください:
> 
> 

## <a name="saml-integration"></a>SAML の統合
SAML 2.0 をサポートするすべてのアプリケーションは、 [この手順](../active-directory-saas-custom-apps.md)を使用して Azure AD テナントと直接統合し、カスタム アプリケーションを追加できます。 アプリケーションと Azure AD の統合をテストし終わったら、次の情報を <mailto:waadpartners@microsoft.com>に送信してください。

* Azure AD チームが統合のテストに使用できるアプリケーションのテスト テナントまたはアカウントの資格情報を記載してください。  
* [ここ](../active-directory-saas-custom-apps.md)の説明に従って、アプリケーションの SAML サインオン URL (エンティティ ID) と Reply URL (Assertion Consumer Service) 値を記載してください。 通常、SAML メタデータ ファイルの一部としてこれらの値を入力している場合は、それも送信してください。
* SAML 2.0 を使用してアプリケーションの ID プロバイダーとして Azure AD を構成する方法を簡単に説明してください。 セルフサービス管理ポータルで ID プロバイダーとしての Azure AD の構成がアプリケーションでサポートされている場合、前述の資格情報にそれを設定する機能を含めてください。
* 次の情報を入力してください。

> 会社名:
> 
> 会社の Web サイト:
> 
> アプリケーション名:
> 
> アプリケーションの説明 (256 文字以内):
> 
> アプリケーションの Web サイト (情報):
> 
> アプリケーションのテクニカル サポートの Web サイト、または連絡先情報:
> 
> 顧客がアプリケーションにサインアップしたり、アプリケーションを購入したりする際にアクセスする、アプリケーションのサインアップ URL:
> 
> アプリケーションを表示するカテゴリを最大&3; つ選択してください (使用できるカテゴリについては、 [Azure Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/)をご覧ください):
> 
> アプリケーションの小さいアイコン (PNG ファイル、45 x 45px、単色の背景色) を添付してください:
> 
> アプリケーションの大きいアイコン (PNG ファイル、215 x 215px、単色の背景色) を添付してください:
> 
> アプリケーションのロゴ (PNG ファイル、150 x 122px、透明の背景色) を添付してください:
> 
> 




<!--HONumber=Feb17_HO2-->


