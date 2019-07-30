---
title: Azure AD エンタイトルメント管理 (プレビュー) での一般的なシナリオ - Azure Active Directory
description: Azure Active Directory エンタイトルメント管理 (プレビュー) での一般的なシナリオの場合に従う必要がある、大まかな手順について説明します。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/23/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8e7709abcc97baac9bf657b9fff2110cb2e72c1
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489028"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management-preview"></a>Azure AD エンタイトルメント管理 (プレビュー) での一般的なシナリオ

> [!IMPORTANT]
> Azure Active Directory (Azure AD) エンタイトルメント管理は、現在パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

組織のエンタイトルメント管理を構成するには、いくつかの方法があります。 ただし、開始したばかりであるなら、管理者、承認者、および要求元の一般的なシナリオを理解することをお勧めします。

## <a name="administrators"></a>管理者

### <a name="im-new-to-entitlement-management-and-i-want-help-with-getting-started"></a>エンタイトルメント管理は初めてであり、開始のために支援が必要

> [!div class="mx-tableFixed"]
> | 手順 | 例 |
> | --- | --- |
> | [チュートリアルに従って最初のアクセス パッケージを作成する](entitlement-management-access-package-first.md) | [![Azure portal アイコン](./media/entitlement-management-scenarios/azure-portal.png)](./media/entitlement-management-scenarios/azure-portal-expanded.png#lightbox) |

### <a name="i-want-to-allow-users-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>自分のディレクトリ内のユーザーに、グループ、アプリケーション、または SharePoint サイトへのアクセスを要求することを許可します。

> [!div class="mx-tableFixed"]
> | 手順 | 例 |
> | --- | --- |
> | **1.** [カタログ内に新しいアクセス パッケージを作成する](entitlement-management-access-package-create.md#start-new-access-package) | ![アクセス パッケージを作成します。](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [リソースのロールをアクセス パッケージに追加する](entitlement-management-access-package-edit.md#add-resource-roles)<ul><li>グループ</li><li>[アプリケーション]</li><li>SharePoint サイト</li></ul> | ![リソース ロールを追加する](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [ポリシーを追加する](entitlement-management-access-package-edit.md#policy-for-users-in-your-directory)<ul><li>ディレクトリ内のユーザーに対して</li><li>承認を要求する</li><li>有効期限の設定</li></ul> | ![ポリシーの追加](./media/entitlement-management-scenarios/policy.png) |

### <a name="i-want-to-allow-users-from-my-business-partners-directory-including-users-not-yet-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>ビジネス パートナー ディレクトリからのユーザー (自分のディレクトリ内にまだいないユーザーを含む) に、グループ、アプリケーション、または SharePoint サイトへのアクセスを要求することを許可します。

> [!div class="mx-tableFixed"]
> | 手順 | 例 |
> | --- | --- |
> | **1.** [カタログ内に新しいアクセス パッケージを作成する](entitlement-management-access-package-create.md#start-new-access-package) | ![アクセス パッケージを作成します。](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [リソースのロールをアクセス パッケージに追加する](entitlement-management-access-package-edit.md#add-resource-roles) | ![リソース ロールを追加する](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [外部ユーザーのポリシーを追加する](entitlement-management-access-package-edit.md#policy-for-users-not-in-your-directory)<ul><li>ディレクトリ内にいないユーザーの場合</li><li>承認を要求する</li><li>有効期限の設定</li></ul> | ![外部ユーザーのポリシーを追加する](./media/entitlement-management-scenarios/policy-external.png) |
> | **4.** [アクセス パッケージを要求するマイ アクセス ポータル リンクをビジネス パートナーに送信する](entitlement-management-access-package-edit.md#copy-my-access-portal-link)<ul><li>ビジネス パートナーはユーザーとリンクを共有できる</li></ul> |  |

### <a name="i-want-to-change-the-groups-applications-or-sharepoint-sites-in-an-access-package"></a>アクセス パッケージ内のグループ、アプリケーション、または SharePoint サイトを変更できる

> [!div class="mx-tableFixed"]
> | 手順 | 例 |
> | --- | --- |
> | **1.** アクセス パッケージを開く | ![リソース ロールを追加する](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [リソース ロールを追加または削除する](entitlement-management-access-package-edit.md#add-resource-roles) | ![リソース ロールを追加する](./media/entitlement-management-scenarios/resource-roles-add.png) |

### <a name="i-want-to-view-who-has-an-assignment-to-groups-applications-or-sharepoint-sites"></a>グループ、アプリケーション、または SharePoint サイトへの割り当てを持っているユーザーを表示する

> [!div class="mx-tableFixed"]
> | 手順 | 例 |
> | --- | --- |
> | **1.** アクセス パッケージを開く | ![リソース ロールを追加する](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [割り当てを表示する](entitlement-management-access-package-edit.md#view-who-has-an-assignment)<ul><li>アクセス パッケージへのアクセスを持つユーザーを表示する</li><li>どのユーザーのアクセスの有効期限が切れているかを表示する</li></ul> |  |

### <a name="i-want-to-view-groups-applications-or-sharepoint-sites-a-user-has-access-to"></a>ユーザーがアクセスするグループ、アプリケーション、または SharePoint サイトを表示する

> [!div class="mx-tableFixed"]
> | 手順 | 例 |
> | --- | --- |
> | [ユーザー割り当てレポートを表示する](entitlement-management-reports.md)<ul><li>いつ要求されてだれが承認されたかを表示する</li></ul> |  |

## <a name="approvers"></a>承認者

### <a name="i-want-to-approve-requests-to-access-groups-applications-or-sharepoint-sites"></a>グループ、アプリケーション、または SharePoint サイトにアクセスする要求を承認する

> [!div class="mx-tableFixed"]
> | 手順 | 例 |
> | --- | --- |
> | **1.** [マイ アクセス ポータルで要求を開く](entitlement-management-request-approve.md#open-request) | [![マイ アクセス ポータル アイコン](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** [アクセス要求を承認する](entitlement-management-request-approve.md#approve-or-deny-request) | ![アクセスを承認する](./media/entitlement-management-scenarios/approve-access.png) |

## <a name="requestors"></a>要求元

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-available-to-me-and-request-access"></a>利用できるグループ、アプリケーション、または SharePoint サイトを表示し、アクセスを要求する

> [!div class="mx-tableFixed"]
> | 手順 | 例 |
> | --- | --- |
> | **1.** [マイ アクセス ポータルへのサインイン](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![マイ アクセス ポータル アイコン](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** アクセス パッケージの検索 |  |
> | **3.** [アクセスの要求](entitlement-management-request-access.md#request-an-access-package) | ![アクセスの要求](./media/entitlement-management-scenarios/request-access.png) |

### <a name="im-an-external-user-and-i-want-to-request-access-to-groups-applications-or-sharepoint-sites-with-a-direct-link"></a>外部ユーザーであり、直接リンクでグループ、アプリケーション、または SharePoint サイトへのアクセスを要求する

> [!div class="mx-tableFixed"]
> | 手順 | 例 |
> | --- | --- |
> | **1.** [受信したマイ アクセス ポータル リンクの検索](entitlement-management-access-package-edit.md#copy-my-access-portal-link) |  |
> | **2.** [マイ アクセス ポータルへのサインイン](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![マイ アクセス ポータル アイコン](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **3.** [アクセスの要求](entitlement-management-request-access.md#request-an-access-package) | ![外部ユーザーのアクセスを要求する](./media/entitlement-management-scenarios/request-access-external.png) |

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-i-already-have-access-to"></a>既にアクセスできるグループ、アプリケーション、または SharePoint サイトを表示する

> [!div class="mx-tableFixed"]
> | 手順 | 例 |
> | --- | --- |
> | **1.** [マイ アクセス ポータルへのサインイン](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![マイ アクセス ポータル アイコン](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** アクティブなアクセス パッケージの表示 |  |

## <a name="next-steps"></a>次の手順

- [チュートリアル:最初のアクセス パッケージを作成する](entitlement-management-access-package-first.md)
- [タスクを委任する](entitlement-management-delegate.md)
