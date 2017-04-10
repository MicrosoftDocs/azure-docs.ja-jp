---
title: "Azure AD Connect 同期: 同期を理解してカスタマイズする | Microsoft Docs"
description: "Azure AD Connect の同期のしくみとカスタマイズ方法について説明します。"
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: ee4bf802-045b-4da0-986e-90aba2de58d6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: aa20b20c86763791eb579883b5273ea79cc714b5
ms.openlocfilehash: 4edac05325ad12596d982d113df0db7461124b12
ms.lasthandoff: 03/29/2017


---
# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Azure AD Connect sync: 同期を理解してカスタマイズする
Azure Active Directory Connect 同期サービス (Azure AD Connect Sync) は、Azure AD Connect の主要コンポーネントです。 オンプレミス環境と Azure AD 間の ID データの同期に関連するすべての操作を処理します。 Azure AD Connect Sync は、Azure Active Directory Connector で構成された DirSync、Azure AD Sync、Forefront Identity Manager の後継となります。

このトピックは **Azure AD Connect 同期** (別名: **同期エンジン**) のホームであり、関連する他のすべてのトピックへのリンクを示します。 Azure AD Connect へのリンクについては、「 [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」を参照してください。

同期サービスは、オンプレミスの **Azure AD Connect 同期** コンポーネントと、**Azure AD Connect 同期サービス**と呼ばれる Azure AD のサービス側の、2 つのコンポーネントで構成されます。 このサービスは、DirSync、Azure AD Sync、および Azure AD Connect で共通です。

## <a name="azure-ad-connect-sync-topics"></a>Azure AD Connect Sync のトピック
| トピック | 内容 |
| --- | --- |
| **Azure AD Connect sync の基礎** | |
| [アーキテクチャの概要](active-directory-aadconnectsync-understanding-architecture.md) |同期エンジンを初めて使うユーザーのために、アーキテクチャと用語について説明します。 |
| [技術的概念](active-directory-aadconnectsync-technical-concepts.md) |アーキテクチャのトピックを要約したもので、用語について簡単に説明します。 |
| [Azure AD Connect のトポロジ](active-directory-aadconnect-topologies.md) |同期エンジンがサポートするさまざまなトポロジとシナリオについて説明します。 |
| **カスタム構成** | |
| [インストール ウィザードの再実行](active-directory-aadconnectsync-installation-wizard.md) |Azure AD Connect インストール ウィザードをもう一度実行する場合に使用できるオプションについて説明します。 |
| [宣言型のプロビジョニングについて](active-directory-aadconnectsync-understanding-declarative-provisioning.md) |宣言型のプロビジョニングと呼ばれる構成モデルについて説明します。 |
| [宣言型のプロビジョニングの式について](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) |宣言型のプロビジョニングで使用される式言語の構文について説明します。 |
| [既定の構成について](active-directory-aadconnectsync-understanding-default-configuration.md) |既定のルールと既定の構成について説明します。 また、既定のシナリオに対するルールの動作についても説明します。 |
| [ユーザーと連絡先について](active-directory-aadconnectsync-understanding-users-and-contacts.md) |前のトピックの続きで、ユーザーと連絡先の構成の連携について説明します (特に複数フォレスト環境)。 |
| [既定の構成を変更する方法](active-directory-aadconnectsync-change-the-configuration.md) |属性フローに対する一般的な構成変更方法について説明します。 |
| [既定の構成の変更するためのベスト プラクティス](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) |既定の構成を変更する場合のサポートの制約。 |
| [フィルター処理の構成](active-directory-aadconnectsync-configure-filtering.md) |Azure AD と同期するオブジェクトを制限する方法についてのさまざまなオプション、およびこれらのオプションの構成手順について説明します。 |
| **機能とシナリオ** | |
| [誤って削除されないように保護する](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) |" *誤って削除されないように保護する* " 機能とその構成方法について説明します。 |
| [Scheduler](active-directory-aadconnectsync-feature-scheduler.md) |データをインポート、同期、およびエクスポートする組み込みのスケジューラについて説明します。 |
| [パスワード同期の実装](active-directory-aadconnectsync-implement-password-synchronization.md) |パスワード同期のしくみ、実装方法、および運用とトラブルシューティングについて説明します。 |
| [デバイスの書き戻し](active-directory-aadconnect-feature-device-writeback.md) |Azure AD Connect のデバイスの書き戻しのしくみについて説明します。 |
| [ディレクトリ拡張機能](active-directory-aadconnectsync-feature-directory-extensions.md) |独自のカスタム属性で Azure AD スキーマを拡張する方法について説明します。 |
| **同期サービス** | |
| [Azure AD Connect 同期サービスの機能](active-directory-aadconnectsyncservice-features.md) |同期サービス側と、Azure AD で同期の設定を変更する方法について説明します。 |
| [重複属性の回復性](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) |**userPrincipalName** と **proxyAddresses** の重複した属性値の回復性を有効にして使用する方法について説明します。 |
| **操作と UI** | |
| [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md) |[[操作]](active-directory-aadconnectsync-service-manager-ui-operations.md)、[[コネクタ]](active-directory-aadconnectsync-service-manager-ui-connectors.md)、[[メタバース デザイナー]](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md)、[[メタバース検索]](active-directory-aadconnectsync-service-manager-ui-mvsearch.md) の各タブなどの Synchronization Service Manager の UI について説明します。 |
| [操作タスクおよび考慮事項](active-directory-aadconnectsync-operations.md) |障害回復などの操作上の考慮事項について説明します。 |
| **方法** | |
| [Azure AD アカウントをリセットする](active-directory-aadconnectsync-howto-azureadaccount.md) |Azure AD Connect 同期サービスから Azure AD への接続に使用するサービス アカウントの資格情報をリセットする方法。 |
| **詳細情報とリファレンス** | |
| [ポート](active-directory-aadconnect-ports.md) |同期エンジンとオンプレミスのディレクトリおよび Azure AD の間で開く必要があるポートの一覧を示します。 |
| [Azure Active Directory に同期される属性](active-directory-aadconnectsync-attributes-synchronized.md) |オンプレミスの AD と Azure AD の間で同期されるすべての属性の一覧を示します。 |
| [関数参照](active-directory-aadconnectsync-functions-reference.md) |宣言型のプロビジョニングで使用できるすべての関数の一覧を示します。 |

## <a name="additional-resources"></a>その他のリソース
* [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)


