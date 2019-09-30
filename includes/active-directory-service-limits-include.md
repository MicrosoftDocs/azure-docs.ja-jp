---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 05/22/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 421e88374a4ca03311fa77a6568a676aa9ffafa5
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "70919700"
---
ここでは、Azure Active Directory (Azure AD) サービスの使用上の制約およびその他のサービスの制限を説明します。

| Category | 制限 |
| --- | --- |
| ディレクトリ | 1 人のユーザーは、最大で 500 の Azure AD ディレクトリにゲストまたはメンバーとして属することができます。<br/>1 人のユーザーは、最大で 20 個のディレクトリを作成できます。 |
| ドメイン | 追加できるマネージド ドメイン名は 900 個以下です。 オンプレミス Active Directory とのフェデレーションをすべてのドメインに設定する場合、各ディレクトリに追加できるドメイン名は 450 個以下です。 |
| Objects |<ul><li>Azure Active Directory の Free エディションのユーザーは、既定で 1 つのディレクトリに最大 50,000 個のオブジェクトを作成できます。 検証済みドメインが少なくとも 1 つあれば、Azure AD でのディレクトリ サービスの既定のクォータは、300,000 オブジェクトに拡張されます。 </li><li>管理者以外のユーザーは、最大 250 個のオブジェクトを作成できます。 アクティブ オブジェクトと復元可能な削除済みオブジェクトの両方が、このクォータに加算されます。 30 日未満に削除された削除済みオブジェクトのみが復元可能です。 復元できなくなった削除済みオブジェクトは、30 日間、 4 分の 1 の値でこのクォータに加算されます。 通常の業務において、このクォータを頻繁に超えると思われる管理者以外のユーザーに、[管理者ロールを割り当てる](../articles/active-directory/users-groups-roles/directory-assign-admin-roles.md)こともできます。</li></ul> |
| スキーマの拡張機能 |<ul><li>文字列型の拡張の最大文字数は 256 文字です。 </li><li>バイナリ型の拡張は 256 バイトに制限されます。</li><li>1 つのオブジェクトに対して書き込める拡張値は、(*すべて*の型と*すべて*のアプリケーションで合計) 100 個のみです。</li><li>文字列型またはバイナリ型の単一値の属性を使用して拡張できるのは、User、Group、TenantDetail、Device、Application、および ServicePrincipal エンティティのみです。</li><li>スキーマ拡張は、Graph API バージョン 1.21 プレビューでのみ使用できます。 拡張を登録するには、アプリケーションに書き込みアクセス権を付与する必要があります。</li></ul> |
| [アプリケーション] |最大 100 人のユーザーが 1 つのアプリケーションの所有者になれます。 |
| グループ |<ul><li>最大 100 人のユーザーが 1 つのグループの所有者になれます。</li><li>任意の数のオブジェクトが 1 つのグループのメンバーになることができます。</li><li>ユーザーは任意の数のグループのメンバーになることができます。</li><li>Azure AD Connect を使用してオンプレミスの Active Directory から Azure Active Directory に同期できるグループ内のメンバーの数は、50,000 ユーザーに制限されています。</li></ul> |
| アプリケーション プロキシ | <ul><li>アプリケーション プロキシ アプリケーションごとに 1 秒あたり最大 500 件のトランザクション</li><li>テナントでの 1 秒あたり最大 750 件のトランザクション</li></ul><br/>トランザクションは、単一の http 要求と一意のリソースの応答として定義されます。 スロットルが行われた場合、クライアントでは 429 応答 (要求が多すぎます) を受信します。 |
| アクセス パネル |<ul><li>各ユーザーのアクセス パネルに表示できるアプリケーション数に制限はありません。 これは、Azure AD Premium または Enterprise Mobility Suite のライセンスが割り当てられているユーザーに適用されます。</li><li>最大で 10 個のアプリ タイルを各ユーザーのアクセス パネルに表示できます。 この制限は、Azure AD Free ライセンス プランのライセンスが割り当てられているユーザーに適用されます。 アプリ タイルの例として、Box、Salesforce、Dropbox が挙げられます。 この制限は、管理者アカウントには適用されません。</li></ul> |
| Reports | いずれのレポートでも、最大 1,000 行を表示またはダウンロードできます。 それを超えるデータは切り捨てられます。 |
| 管理単位 | オブジェクトは最大 30 個の管理単位のメンバーにすることができます。 |
| 管理者ロールとアクセス許可 | <ul><li>グループを[所有者](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#object-ownership)として追加することはできません。</li><li>グループを[ロール](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)に割り当てることはできません。</li><li>管理者以外のすべてのユーザーのすべてのディレクトリ情報へのアクセスを無効にするために、他のユーザーのディレクトリ情報を読み取るユーザーの能力をテナント全体のスイッチの外部に制限することはできません (お勧めできません)。 既定のアクセス許可の詳細については、[ここ](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#to-restrict-the-default-permissions-for-member-users)を参照してください。</li><li>管理者ロールのメンバーシップの追加や失効が有効になるには、最大 15 分またはサインアウト/サインインが必要になることがあります。</li></ul> |
