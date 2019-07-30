---
title: Azure Active Directory のシングルテナント アプリとマルチテナント アプリ
description: Azure AD のシングルテナント アプリとマルチテナント アプリの機能と違いについて説明します。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: justhu
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9035cc629a11c125c1b6351bd4bff9f5576f7baf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111081"
---
# <a name="tenancy-in-azure-active-directory"></a>Azure Active Directory のテナント

Azure Active Directory (Azure AD) では、ユーザーやアプリなどのオブジェクトを "*テナント*" と呼ばれるグループにまとめます。 テナントを使用することで、管理者は組織内のユーザーと、組織が所有するアプリに対してポリシーを設定して、セキュリティおよび運用ポリシーを満たすことができます。 

## <a name="who-can-sign-in-to-your-app"></a>アプリケーションにサインインできるユーザー

アプリを開発する場合、開発者は [Azure portal](https://portal.azure.com) でアプリを登録する際に、アプリをシングルテナントまたはマルチテナントとして構成できます。
* シングルテナント アプリは、それらが登録されているテナント (ホーム テナントとも呼ばれます) でのみ使用できます。
* マルチテナント アプリは、ホーム テナントと他のテナントの両方のユーザーが使用できます。

Azure portal で次のように対象ユーザーを設定することで、アプリをシングルテナントまたはマルチテナントとして構成できます。

| 対象ユーザー | シングル/マルチテナント | サインインできるユーザー | 
|----------|--------| ---------|
| このディレクトリ内のアカウントのみ | シングル テナント | ディレクトリ内のすべてのユーザー アカウントとゲスト アカウントが、このアプリケーションまたは API を使用できます。<br>*対象ユーザーが組織の内部にいる場合は、このオプションを使用します。* |
| 任意の Azure AD ディレクトリ内のアカウント | マルチテナント | Microsoft の職場または学校アカウントを持つすべてのユーザーとゲストが、このアプリケーションまたは API を使用できます。 これには、Office 365 を使用する学校や企業が含まれます。<br>*対象ユーザーが企業または教育機関の場合は、このオプションを使用します。* |
| 任意の Azure AD ディレクトリ内のアカウントと個人用 Microsoft アカウント (Skype、Xbox、Outlook.com など) | マルチテナント | 職場または学校アカウントあるいは個人用 Microsoft アカウントを持つすべてのユーザーが、このアプリケーションまたは API を使用できます。 これには、Office 365 を使用する学校と企業、および Xbox や Skype などのサービスへのサインインに使用されている個人用アカウントが含まれます。<br>*最も広範な Microsoft アカウントを対象とする場合は、このオプションを使用します。* | 

## <a name="best-practices-for-multi-tenant-apps"></a>マルチテナント アプリのベスト プラクティス

IT 管理者がテナントで設定できる各種ポリシーの数が多いため、優れたマルチテナント アプリの作成が難しい場合があります。 マルチテナント アプリを作成する場合は、次のベスト プラクティスに従ってください。

* [条件付きアクセス ポリシー](conditional-access-dev-guide.md)が構成されているテナントでアプリをテストします。
* 最小限のユーザー アクセスの原則に従って、アプリでは実際に必要なアクセス許可だけを要求するようにします。 管理者の同意が必要なアクセス許可を要求することは避けます。組織によっては、ユーザーがアプリをまったく取得できなくなる可能性があるためです。 
* アプリの一部として公開するアクセス許可の適切な名前と説明を提供します。 これにより、ユーザーと管理者は、アプリの API を使用しようとしたときに同意する内容を把握しやすくなります。 詳細については、[アクセス許可ガイド](v1-permissions-and-consent.md)のベスト プラクティスのセクションをご覧ください。

## <a name="next-steps"></a>次の手順

* [アプリをマルチテナントに変換する方法](howto-convert-app-to-be-multi-tenant.md)
