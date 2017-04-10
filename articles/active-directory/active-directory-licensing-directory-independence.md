---
title: "Azure Active Directory ディレクトリの特性 | Microsoft Docs"
description: "完全に独立したリソースとしてディレクトリを理解することで Azure Active Directory ディレクトリを管理する"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 2b862b75-14df-45f2-a8ab-2a3ff1e2eb08
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: f8b63e5831897d3a45298b0415bb2d6d44ab0de1
ms.openlocfilehash: 5ec00d5e8380f121dd9302cf08a0708c530aab9b
ms.lasthandoff: 03/01/2017


---
# <a name="understand-how-multiple-azure-active-directory-directories-interact"></a>複数の Azure Active Directory ディレクトリの相互作用を理解する
Azure Active Directory (Azure AD) の各ディレクトリは、完全に独立したリソースです。つまり、対等であり、フル機能を備え、管理対象の他のディレクトリから論理的に独立しています。 ディレクトリ間に親子関係はありません。 このディレクトリ間の独立には、リソースの独立、管理上の独立、同期の独立があります。

## <a name="resource-independence"></a>リソースの独立
後述する外部ユーザーの一部の例外を除き、あるディレクトリでリソースを作成または削除しても、別のディレクトリのリソースには影響しません。 あるディレクトリで "contoso.com" というカスタム ドメインを使用している場合、このドメインを他のディレクトリで使用することはできません。

## <a name="administrative-independence"></a>管理上の独立
"Contoso" ディレクトリの管理者以外のユーザーが、"Test" というテスト ディレクトリを作成した場合、次のようになります。

* 既定では、ディレクトリを作成したユーザーがその新しいディレクトリの外部のユーザーとして追加され、そのディレクトリのグローバル管理者ロールが割り当てられます。
* "Test" の管理者が管理者特権を明示的に付与した場合を除き、"Contoso" ディレクトリの管理者には、"Test" ディレクトリに対する直接的な管理者特権はありません。 "Contoso" の管理者は、"Test" を作成したユーザー アカウントを制御している場合は、"Test" ディレクトリへのアクセスを制御できます。
* あるディレクトリでユーザーの管理者ロールを変更 (追加または削除) した場合、変更は、別のディレクトリでそのユーザーに割り当てられている可能性のある管理者ロールには影響しません。

## <a name="synchronization-independence"></a>同期の独立
次のどちらかの&1; つのインスタンスからデータが同期されるように各 Azure AD ディレクトリを独立して構成することができます。

* 1 つは、単一の AD フォレストとデータを同期するディレクトリ同期 (DirSync) ツールです。
* もう&1; つは、1 つ以上のオンプレミスのフォレストと Azure AD 以外のデータ ソースのいずれか、または両方とデータを同期する Forefront Identity Manager 用 Azure Active Directory コネクタです。

## <a name="add-an-azure-ad-directory"></a>Azure AD ディレクトリを追加する
Azure クラシック ポータルで Azure AD ディレクトリを追加するには、左側で Azure Active Directory 拡張機能を選択し、 **[追加]**をタップします。

> [!NOTE]
> 他の Azure リソースとは異なり、ディレクトリは Azure サブスクリプションの子リソースではありません。 Azure サブスクリプションを取り消した場合や、期限切れを許可した場合でも、Azure PowerShell、Azure Graph API、Office 365 管理センターなどの他のインターフェイスを使用して、ディレクトリ データに引き続きアクセスできます。 また、ディレクトリに別のサブスクリプションを関連付けることもできます。
>
>

## <a name="next-steps"></a>次のステップ
Azure AD のライセンスに関する問題とベスト プラクティスの概要については、 [Azure Active Directory ライセンス](active-directory-licensing-what-is.md)に関するページを参照してください。

