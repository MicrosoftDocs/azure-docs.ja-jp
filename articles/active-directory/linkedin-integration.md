---
title: "AAD で LinkedIn の統合を構成する | Microsoft Docs"
description: "Azure Active Directory で Microsoft アプリの LinkedIn の統合を有効または無効にする方法について説明します。"
services: active-directory
author: jeffgilb
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: jeffgilb
ms.reviewer: jsnow
ms.custom: it-pro
ms.openlocfilehash: 48b26bfcce67ce915c404a0ab2ac0f399c3b821d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="enabling-linkedin-integration-in-azure-active-directory"></a>Azure Active Directory で LinkedIn の統合を有効にする
LinkedIn の統合を有効にすると、ユーザーは Microsoft アプリ内から LinkedIn の公開データと個人の LinkedIn ネットワーク (選択した場合) の両方にアクセスできます。 各ユーザーは、個別に職場アカウントを LinkedIn アカウントに接続することもできます。

### <a name="linkedin-integration-from-your-end-users-perspective"></a>エンド ユーザーの視点から見た LinkedIn の統合
組織内のエンド ユーザーが LinkedIn アカウントを職場アカウントに接続すると、組織内の同僚と組織外のユーザーを区別しやすくなります。 2 つのアカウントを接続することで、ユーザーの LinkedIn 接続とプロファイル データを組織の Microsoft アプリに使用できるようになりますが、データを共有する LinkedIn のアクセス許可を削除することで、いつでもオプトアウトできます。 統合には、公開されているプロファイル情報も使用されます。また、ユーザーは、自分の公開プロファイルとネットワーク情報を LinkedIn プライバシー設定を介して Microsoft アプリケーションと共有するかどうかを選択することができます。

>[!NOTE]
> Azure AD で LinkedIn の統合を有効にすると、アプリとサービスはエンド ユーザーの情報の一部にアクセスできるようになります。 Azure AD で LinkedIn の統合を有効にする場合のプライバシーの考慮事項については、「[Microsoft のプライバシーに関する声明](https://privacy.microsoft.com/privacystatement/)」を参照してください。 

## <a name="enable-linkedin-integration"></a>LinkedIn の統合を有効にする
Azure AD の既定では、企業の LinkedIn の統合は有効です。 そのため、テナントでこの機能を利用できるようにすると、組織内のすべてのユーザーが LinkedIn の機能とプロファイルを表示できます。 LinkedIn の統合を有効にすると、組織内のユーザーは、Outlook で電子メールを受信するときにプロファイルを表示するなど、Microsoft サービス内で LinkedIn 機能を使用できるようになります。 この機能を無効にすると、LinkedIn 機能にアクセスできなくなり、Microsoft サービスを介した LinkedIn と組織間のユーザー アカウントの接続とデータ共有は停止されます。

> [!IMPORTANT]
> この機能は、go-local、Sovereign、および政府機関の各テナントでは使用できません。 また、Azure AD サービスの更新プログラム (LinkedIn の統合機能など) は、全 Azure テナントに同時にはデプロイされません。 お使いの Azure テナントにこの機能がロール アウトされるまでは、LinkedIn と Azure AD の統合を有効にすることはできません。

1. [Azure Active Directory 管理センター](https://aad.portal.azure.com/)に、ディレクトリの全体管理者のアカウントでサインインします。
2. **[ユーザーとグループ]** を選択します。
3. **[ユーザーとグループ]** ブレードで、**[ユーザー設定]** を選択します。
4. **[LinkedIn の統合]** で [はい] を選択すると LinkedIn が有効に、[いいえ] を選択すると無効になります。
   ![LinkedIn の統合を有効にする](./media/linkedin-integration/LinkedIn-integration.PNG)

### <a name="learn-more"></a>詳細情報 
* [Microsoft アプリでの LinkedIn の情報と機能](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn ヘルプ センター](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>次のステップ
次のリンク先で、Azure Portal から LinkedIn と Azure AD の統合を有効または無効にする方法が説明されています。

[LinkedIn の統合を構成する](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 