---
title: "Azure Active Directory の管理単位の管理"
description: "管理単位を使用した Azure Active Directory での細かいレベルの権限の委任"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 8464cd6b-1d1a-470d-a4fb-ee29b8eab4c4
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: b30ed9727229c082bff65023bfe4900640caa995
ms.lasthandoff: 12/29/2016


---
# <a name="administrative-units-management-in-azure-ad---public-preview"></a>Azure AD の管理単位 - パブリック プレビュー
この記事では、管理単位について説明します。管理単位は、管理権限をユーザーのサブセットに委任したり、ユーザーのサブセットにポリシーを適用したりするために使用できる、Azure Active Directory の新しいリソース コンテナーです。 Azure Active Directory では、全体管理者が管理単位を使用して、各地域の管理者に権限を委任したり、細かなレベルでポリシーを設定したりできます。

これは、部門が独立している組織、たとえば、互いに独立している多数の自律的なスクール (ビジネス スクール、エンジニアリング スクールなど) で構成される大きな大学で役に立ちます。 このような部門には、その部門のアクセスの制御、ユーザーの管理、およびポリシーの設定を行う IT 管理者が存在します。 全体管理者は、これらの部門の管理者に、特定の部門のユーザーに適用される権限を与えることができます。 前の例を使用して具体的に説明すると、全体管理者は、特定のスクール (ビジネス スクール) の管理単位を作成し、その中にビジネス スクールのユーザーだけを含めることができます。 その後、全体管理者は、ビジネススクールの IT スタッフに限定されたロールを追加できます。つまり、ビジネス スクールの IT スタッフに、ビジネス スクールという管理単位にのみ適用される権限を与えます。

> [!IMPORTANT]
> 管理単位は、Azure Active Directory Premium を有効にした場合にのみ、作成して使用できます。 詳細については、「 [Azure AD Premium の概要](active-directory-get-started-premium.md)」を参照してください。
>
>

全体管理者の観点から見ると、管理単位は、作成してリソースを設定できるディレクトリ オブジェクトです。 **このリリースでは、設定できるリソースはユーザーだけです。**  管理単位を作成して設定した後、与えられた権限の適用先を管理単位に含まれているリソースに限定するスコープとして使用できます。

## <a name="managing-administrative-units"></a>管理単位の管理
このプレビュー リリースでは、Azure Active Directory Module for Windows PowerShell コマンドレットを使用して、管理単位の作成と管理を実行できます。

Azure AD モジュールのソフトウェア要件とインストールの詳細、および管理単位を管理するための Azure AD モジュールのコマンドレット (構文、パラメーターの説明、および例) については、「 [Windows PowerShell による Azure AD の管理](https://msdn.microsoft.com/library/azure/jj151815.aspx)」を参照してください。

## <a name="next-steps"></a>次のステップ
[Azure Active Directory のエディション](active-directory-editions.md)

