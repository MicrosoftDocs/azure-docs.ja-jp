---
title: "Azure Active Directory B2B コラボレーションの制限 | Microsoft Docs"
description: "Azure Active Directory B2B コラボレーション プレビューの現在の制限"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/16/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0e71a840d4f503779131ee4a21fe6063d33185f1
ms.openlocfilehash: cbefca2d45a332cd57cfea49dfeaa300426d5502
ms.lasthandoff: 02/24/2017


---

# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Azure AD B2B コラボレーションの制限
現在、Azure Active Directory (Azure AD) B2B コラボレーションには、この記事に記載されている制限が適用されます。

## <a name="invitation-apis-are-in-preview"></a>招待 API がプレビュー段階
API サーフェスは前に進んでいきますが、 すべてのプレリリース版と同様、API は、プレビュー名前空間契約の対象です。 この API は、一般公開 (GA) リリースで番号付きのバージョンに移される予定です。

## <a name="possible-double-multi-factor-authentication"></a>Multi-Factor Authentication 重複の可能性
この冗長性は、パートナー側で既に Azure Multi-Factor Authentication ポリシーが設定されている場合に、発生する可能性があります。 B2B コラボレーション Multi-Factor Authentication は、招待する側の組織で実行および管理され、 すべての ID を処理対象としているほか、B2B コラボレーションで招待されたユーザーの認証の強度を制御できます。このため、これが望ましい認証方法です。

しかし、パートナー側で既に Multi-Factor Authentication ポリシーが設定され、実施されていると、パートナー側のユーザーは、自分の組織と招待する側の組織の両方で認証が必要になる可能性があります。

今後のリリースでは、パートナーの Multi-Factor Authentication を信頼して、認証の重複を回避できるポリシーを導入する予定です。

## <a name="instant-on"></a>インスタント オン
B2B コラボレーションのフローでは、ユーザーをディレクトリに追加し、招待の使用、アプリ割り当てなどの際に動的に更新します。 更新と書き込みは、通常、1 つのディレクトリ インスタンスで行い、すべてのインスタンス間でレプリケートする必要があります。 レプリケーション完了までの時間が制限されていることが原因で、承認の問題が発生する場合があることが確認されました。 この問題は、一般公開の前に改善または解決できるように取り組んでいます。 当面はこの問題が発生する可能性は低いと思われますが、万一発生した場合は、更新または再試行によって解決できます。

## <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する他の記事を参照してください。

* [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B コラボレーション ユーザーのプロパティ](active-directory-b2b-user-properties.md)
* [B2B コラボレーション ユーザーのロールへの追加](active-directory-b2b-add-guest-to-role.md)
* [B2B コラボレーションの招待の委任](active-directory-b2b-delegate-invitations.md)
* [動的グループと B2B コラボレーション](active-directory-b2b-dynamic-groups.md)
* [B2B コラボレーション コードと PowerShell サンプル](active-directory-b2b-code-samples.md)
* [B2B コラボレーション用の SaaS アプリの構成](active-directory-b2b-configure-saas-apps.md)
* [B2B コラボレーション ユーザーのトークン](active-directory-b2b-user-token.md)
* [B2B コラボレーション ユーザーの要求マッピング](active-directory-b2b-claims-mapping.md)
* [Office 365 の外部共有](active-directory-b2b-o365-external-user.md)

