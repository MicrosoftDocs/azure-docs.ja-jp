---
title: "カスタム開発されたアプリケーションへのサインインに関する問題 | Microsoft Docs"
description: "Azure AD で開発したアプリケーションにサインインできなくなる一般的なエラー"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 51fac8adc2fe11aac005b4c0c0e9b67bbca8fa6e
ms.contentlocale: ja-jp
ms.lasthandoff: 04/18/2017

---

# <a name="problems-signing-in-to-an-custom-developed-application"></a>カスタム開発されたアプリケーションへのサインインに関する問題

いくつかのエラーによってアプリにサインインできなくなる場合があります。 この問題が発生する最大の原因は、アプリの構成ミスです。

## <a name="errors-related-to--misconfigured-apps"></a>アプリの構成ミスに関連するエラー

* ポータルの構成がアプリの構成と一致していることを確認します。 具体的には、クライアント/アプリケーション ID、応答 URL、クライアント シークレット/キー、およびアプリ ID URI を比較します。

* コード内でアクセスを要求しているリソースを、**[Required Resources]**(必要なリソース) タブの構成済みアクセス許可と比較して、自分が構成したリソースのみを要求していることを確認します。

* 類似したエラーや問題については、[Azure AD StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory) を参照してください。

## <a name="next-steps"></a>次のステップ

[Azure AD 開発者ガイド](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[同意およびアプリと Azure AD との統合](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications>)<br>

[Azure AD v2.0 収束済みアプリの同意およびアクセス許可](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory>)

