---
title: Azure Active Directory ハイブリッド ID の設計上の考慮事項の概要 | Microsoft Docs
description: ハイブリッド ID の設計上の考慮事項のガイドの概要とコンテンツ マップ
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 100509c4-0b83-4207-90c8-549ba8372cf7
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7f8dd49f3668b8f68753681123a04d21edac46c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997734"
---
# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Azure Active Directory ハイブリッド ID の設計上の考慮事項
企業世界では消費者基準のデバイスが広まっており、クラウド基準の SaaS (Software-as-a-Service/サービスとしてのソフトウェア) アプリケーションの導入が簡単になっています。 結果的に、内部データセンターとクラウド プラットフォームにわたりユーザーのアプリケーション アクセスを制御することが課題となります。  

Microsoft の ID ソリューションでは、オンプレミスとクラウドを基盤とする機能を利用する際に、場所に関係なく、1 つのユーザー ID ですべてのリソースの認証と権限付与を行います。 この概念は、ハイブリッド ID と呼ばれています。 Microsoft ソリューションによるハイブリッド ID にはさまざまな設計と構成の選択肢があり、組織のニーズに最適な組み合わせを判断することが難しい場合もあります。 

ハイブリッド ID の設計に関するこの考慮事項ガイドは、組織のビジネス ニーズと技術ニーズに最適なハイブリッド ID ソリューションを設計する方法を理解するために役立ちます。  このガイドでは、組織の固有要件を満たすハイブリッド ID ソリューションを設計するための一連の手順と作業について詳述します。 このガイドでは、機能面とサービス面における品質 (可用性、拡張性、パフォーマンス、管理容易性、セキュリティ) レベル要件を満たすために組織が利用できる技術と機能の選択肢を手順と作業を通して提示します。 

ハイブリッド ID の設計に関するこの考慮事項ガイドの目標は、具体的には、次の質問に答えることにあります。 

* 技術面や問題面で、要件を最も効果的に満たすハイブリッド ID 固有の設計を推し進めるには、どのような質問をして、どのように答える必要があるでしょうか。
* 技術面や問題面で、ハイブリッド ID ソリューションを設計するためにどのような一連のアクティビティを完了する必要があるでしょうか。 
* 要件を満たすために、ハイブリッド ID の技術と構成にはどのような選択肢があるでしょうか。 選択肢にはそれぞれどのような長所や短所があり、ビジネスに最適な選択するために考慮できるでしょうか。

## <a name="who-is-this-guide-intended-for"></a>このガイドはどのような人物を対象読者としていますか。
 中小企業のハイブリッド ID ソリューションの設計を担当する CIO、CITO、ID 設計者代表、企業設計者、IT 設計者です。

## <a name="how-can-this-guide-help-you"></a>このガイドはどのように役立つでしょうか。
クラウド基盤の ID 管理ステムと現行のオンプレミス ID ソリューションを統合できるハイブリッド ID ソリューションの設計方法を理解するためにこのガイドを利用できます。 

次の図はハイブリッド ID ソリューションの例です。IT 管理者はオンプレミスの現行の Windows Server Active Directory ソリューションと Microsoft Azure Active Directory を統合し、クラウドとオンプレミスにあるすべてのアプリケーションでユーザーが SSO (シングル サインオン) を利用することを許可できます。

![例](media/plan-hybrid-identity-design-considerations/hybridID-example.png)

上の図はハイブリッド ID ソリューションの一例ですが、クラウド サービスを活用してオンプレミスの機能と統合し、エンド ユーザーの認証プロセスを統一し、IT リソースの管理を楽にしています。 この例は一般的なシナリオであり、多くの場合、要件が異なれば、組織のハイブリッド ID 設計は図 1 と異なるものになります。 

このガイドでは、組織の固有要件を満たすハイブリッド ID ソリューションを設計するための一連の手順と作業を紹介します。 次の手順と作業を通して、機能面とサービス面における組織の品質レベル要件を満たすために利用できる技術と機能の選択肢を提示します。

**前提条件**: Windows Server、Active Directory Domain Services、Azure Active Directory の利用経験があること。 本書では、ソリューションが単独または統合ソリューションの形式でビジネス ニーズを満たす方法を読者が探しているものと想定しています。

## <a name="design-considerations-overview"></a>設計上の考慮事項の概要
本書では、要件を満たすハイブリッド ID ソリューションを設計するための一連の手順と作業を紹介します。 手順には連続番号が付き、その順番で実行します。 ただし、選択した設計の競合に起因し、後の手順で学習する設計上の考慮事項により先の手順で行った決定を変更しなければならなくなることもあります。 本書全体で、設計上の競合が潜在する場合、可能な限り警告されます。 

本書内のすべての考慮事項を組み入れるために必要な回数だけ手順を繰り返した後でなければ、要件を最も効果的に満たす設計に到達しません。 

| ハイブリッド ID フェーズ | トピックの一覧 |
| --- | --- |
| ID 要件を決定する |[ビジネス ニーズの決定](plan-hybrid-identity-design-considerations-business-needs.md)<br> [ディレクトリ同期要件の決定](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [多要素認証要件の決定](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [ハイブリッド ID 導入戦略の定義](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md) |
| 強力な ID ソリューションによりデータ セキュリティを強化するための計画を立てる |[データ保護要件の決定](plan-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [コンテンツ管理要件の決定](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [アクセス制御要件の決定](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [インシデント対応要件の決定](plan-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [データ保護戦略の定義](plan-hybrid-identity-design-considerations-data-protection-strategy.md) |
| ハイブリッド ID ライフサイクルの計画を立てる |[ハイブリッド ID 管理タスクの決定](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [同期管理](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [ハイブリッド ID 管理の導入戦略の決定](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |

## <a name="next-steps"></a>次の手順
[ID 要件を決定する](plan-hybrid-identity-design-considerations-business-needs.md)

