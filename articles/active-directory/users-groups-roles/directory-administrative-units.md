---
title: 管理単位の管理 (プレビュー) - Azure Active Directory | Microsoft Docs
description: 管理単位を使用した Azure Active Directory での細かいレベルの権限の委任
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 08/01/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58b61186a876af90c812ec7faf41fa9f5b14bf4e
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71336916"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Azure Active Directory での管理単位の管理 (プレビュー)

この記事では、Azure Active Directory (Azure AD) での管理単位について説明します。 管理単位は 1 つのAzure AD リソースであり、他の Azure AD リソースのコンテナーにすることができます。 このプレビュー リリースでは、それらのリソースはユーザーのみが可能です。 たとえば、管理単位をスコープとするユーザー アカウント管理者は、管理単位内のユーザーに対してのみ、プロファイル情報の更新、パスワードのリセット、およびライセンスの割り当てを実行できます。

管理単位を使用して、ユーザーのサブセットに管理権限を委任し、ポリシーを適用できます。 管理単位を使用して、各地域の管理者に権限を委任したり、ポリシーを細かく設定したりできます。

## <a name="deployment-scenario"></a>デプロイ シナリオ

管理単位は、独立した部門を持つ組織で役に立つ可能性があります。 多数の自律的な学校 (ビジネス スクール、エンジニアリング スクールなど) で構成され、各スクールにアクセスの制御、ユーザーの管理、およびスクールのポリシーの設定を行う専用の IT 管理者が存在する大規模な大学の例を検討します。 全体管理者は、ビジネス スクール用の管理単位を作成し、その中にビジネス スクールの学生とスタッフだけを設定できます。 次に、全体管理者は、ビジネス スクールという管理単位内の Azure AD ユーザーのみを対象する管理権限が付与されたスコープ付きロールに IT スタッフを追加できます。

## <a name="license-requirements"></a>ライセンスの要件

管理単位を使用するには、管理単位の管理者ごとに Azure Active Directory Premium ライセンスが必要です。詳細については、「 [Azure AD Premium の概要](../fundamentals/active-directory-get-started-premium.md)」を参照してください。

## <a name="managing-administrative-units"></a>管理単位の管理

このプレビュー リリースでは、管理単位を作成して管理する唯一の方法は、「[管理単位の操作](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)」に説明されているように、Windows PowerShell コマンドレット用の Azure Active Directory モジュールを使用することです。

Azure AD モジュールのソフトウェア要件とインストールの詳細、管理単位を管理するための Azure AD モジュールのコマンドレットの参照情報 (構文、パラメーターの説明、例など) については、「[Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)」をご覧ください。

## <a name="next-steps"></a>次の手順

[Azure Active Directory のエディション](../fundamentals/active-directory-whatis.md)
