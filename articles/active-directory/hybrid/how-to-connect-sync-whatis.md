---
title: 'Azure AD Connect 同期: 同期を理解してカスタマイズする | Microsoft Docs'
description: Azure AD Connect の同期のしくみとカスタマイズ方法について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ee4bf802-045b-4da0-986e-90aba2de58d6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/08/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cea26cb119f64679807bc6c5eaadb41b341e5d5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89662393"
---
# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Azure AD Connect sync: 同期を理解してカスタマイズする
Azure Active Directory Connect 同期サービス (Azure AD Connect Sync) は、Azure AD Connect の主要コンポーネントです。 オンプレミス環境と Azure AD 間の ID データの同期に関連するすべての操作を処理します。 Azure AD Connect Sync は、Azure Active Directory Connector で構成された DirSync、Azure AD Sync、Forefront Identity Manager の後継となります。

このトピックは **Azure AD Connect 同期** (別名: **同期エンジン**) のホームであり、関連する他のすべてのトピックへのリンクを示します。 Azure AD Connect へのリンクについては、「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」を参照してください。

同期サービスは、オンプレミスの **Azure AD Connect 同期** コンポーネントと、**Azure AD Connect 同期サービス** と呼ばれる Azure AD のサービス側の、2 つのコンポーネントで構成されます。

## <a name="azure-ad-connect-sync-topics"></a>Azure AD Connect Sync のトピック
| トピック | 内容 |
| --- | --- |
| **Azure AD Connect sync の基礎** | |
| [アーキテクチャの概要](concept-azure-ad-connect-sync-architecture.md) |同期エンジンを初めて使うユーザーのために、アーキテクチャと用語について説明します。 |
| [技術的概念](how-to-connect-sync-technical-concepts.md) |アーキテクチャのトピックを要約したもので、用語について簡単に説明します。 |
| [Azure AD Connect のトポロジ](plan-connect-topologies.md) |同期エンジンがサポートするさまざまなトポロジとシナリオについて説明します。 |
| **カスタム構成** | |
| [インストール ウィザードの再実行](how-to-connect-installation-wizard.md) |Azure AD Connect インストール ウィザードをもう一度実行する場合に使用できるオプションについて説明します。 |
| [宣言型のプロビジョニングについて](concept-azure-ad-connect-sync-declarative-provisioning.md) |宣言型のプロビジョニングと呼ばれる構成モデルについて説明します。 |
| [宣言型のプロビジョニングの式について](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) |宣言型のプロビジョニングで使用される式言語の構文について説明します。 |
| [既定の構成について](concept-azure-ad-connect-sync-default-configuration.md) |既定のルールと既定の構成について説明します。 また、既定のシナリオに対するルールの動作についても説明します。 |
| [ユーザーと連絡先について](concept-azure-ad-connect-sync-user-and-contacts.md) |前のトピックの続きで、ユーザーと連絡先の構成の連携について説明します (特に複数フォレスト環境)。 |
| [既定の構成を変更する方法](how-to-connect-sync-change-the-configuration.md) |属性フローに対する一般的な構成変更方法について説明します。 |
| [既定の構成の変更するためのベスト プラクティス](how-to-connect-sync-best-practices-changing-default-configuration.md) |既定の構成を変更する場合のサポートの制約。 |
| [フィルター処理の構成](how-to-connect-sync-configure-filtering.md) |Azure AD と同期するオブジェクトを制限する方法についてのさまざまなオプション、およびこれらのオプションの構成手順について説明します。 |
| **機能とシナリオ** | |
| [誤って削除されないように保護する](how-to-connect-sync-feature-prevent-accidental-deletes.md) |" *誤って削除されないように保護する* " 機能とその構成方法について説明します。 |
| [Scheduler](how-to-connect-sync-feature-scheduler.md) |データをインポート、同期、およびエクスポートする組み込みのスケジューラについて説明します。 |
| [パスワード ハッシュ同期の実装](how-to-connect-password-hash-synchronization.md) |パスワード同期のしくみ、実装方法、および運用とトラブルシューティングについて説明します。 |
| [デバイスの書き戻し](how-to-connect-device-writeback.md) |Azure AD Connect のデバイスの書き戻しのしくみについて説明します。 |
| [ディレクトリ拡張機能](how-to-connect-sync-feature-directory-extensions.md) |独自のカスタム属性で Azure AD スキーマを拡張する方法について説明します。 |
| [Microsoft 365 の PreferredDataLocation](how-to-connect-sync-feature-preferreddatalocation.md) |ユーザーの Microsoft 365 リソースをユーザーと同じリージョンに配置する方法について説明します。 |
| **同期サービス** | |
| [Azure AD Connect 同期サービスの機能](how-to-connect-syncservice-features.md) |同期サービス側と、Azure AD で同期の設定を変更する方法について説明します。 |
| [重複属性の回復性](how-to-connect-syncservice-duplicate-attribute-resiliency.md) |**userPrincipalName** と **proxyAddresses** の重複した属性値の回復性を有効にして使用する方法について説明します。 |
| **操作と UI** | |
| [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) |[[操作]](how-to-connect-sync-service-manager-ui-operations.md)、[[コネクタ]](how-to-connect-sync-service-manager-ui-connectors.md)、[[メタバース デザイナー]](how-to-connect-sync-service-manager-ui-mvdesigner.md)、[[メタバース検索]](how-to-connect-sync-service-manager-ui-mvsearch.md) の各タブなどの Synchronization Service Manager の UI について説明します。 |
| [操作タスクおよび考慮事項](./how-to-connect-sync-staging-server.md) |障害回復などの操作上の考慮事項について説明します。 |
| **方法** | |
| [Azure AD アカウントをリセットする](how-to-connect-azureadaccount.md) |Azure AD Connect 同期サービスから Azure AD への接続に使用するサービス アカウントの資格情報をリセットする方法。 |
| **詳細情報とリファレンス** | |
| [ポート](reference-connect-ports.md) |同期エンジンとオンプレミスのディレクトリおよび Azure AD の間で開く必要があるポートの一覧を示します。 |
| [Azure Active Directory に同期される属性](reference-connect-sync-attributes-synchronized.md) |オンプレミスの AD と Azure AD の間で同期されるすべての属性の一覧を示します。 |
| [関数参照](reference-connect-sync-functions-reference.md) |宣言型のプロビジョニングで使用できるすべての関数の一覧を示します。 |

## <a name="additional-resources"></a>その他のリソース
* [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)