---
title: LinkedIn データの共有と同意 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory で Microsoft のアプリを介して LinkedIn 統合がデータを共有する方法について説明します
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d13ae1e5198666e115507fd91bb0fedb1c5fa64
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96545912"
---
# <a name="linkedin-account-connections-data-sharing-and-consent"></a>LinkedIn アカウント接続のデータ共有と同意

Active Directory (Azure AD) 組織内のユーザーが、自分の Microsoft 職場または学校アカウントを LinkedIn のアカウントに接続することに同意できるように設定できます。 ユーザーは、自分のアカウントを接続すると、LinkedIn からの情報や注意を、一部の Microsoft のアプリとサービスで見ることができます。 また、ユーザーは、LinkedIn での自分のネットワーク エクスペリエンスが、Microsoft からの情報で改善および拡充されることも期待できます。

Microsoft のアプリやサービスで LinkedIn の情報を表示するには、ユーザーは自分の Microsoft アカウントと LinkedIn アカウントを接続することに同意する必要があります。 ユーザーは、Outlook、OneDrive、または SharePoint Online でプロファイル カードの他のユーザーの LinkedIn 情報を初めてクリックするときに、アカウントを接続するように求められます。 ユーザーがエクスペリエンスおよびアカウントの接続に同意するまで、ユーザーに対する LinkedIn アカウントの接続は完全に有効になりません。

[!INCLUDE [active-directory-gdpr-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="benefits-of-sharing-linkedin-information"></a>LinkedIn の情報を共有するメリット

Microsoft のアプリおよびサービス内で LinkedIn の情報にアクセスすると、ユーザーは、組織の内部および外部の同僚、顧客、パートナーとの間でいっそう簡単にプロフェッショナルな関係を結び、協力して、構築することができるようになります。 新しいユーザーは、同僚と関係を結び、同僚について知り、より多くの情報に簡単にアクセスできることで、より速く組織にとけ込むことができます。 Microsoft のアプリのプロファイル カードに LinkedIn の情報が表示される方法の例を次に示します。

![組織内で LinkedIn の統合を有効にする](./media/linkedin-user-consent/display-example.png)

## <a name="enable-and-announce-linkedin-integration"></a>LinkedIn の統合を有効にして発表する

組織の設定を管理するには Azure Active Directory 管理者でなければなりません。 すべてのユーザーについて有効にすることも、特定のユーザー セットだけにすることもできます。

1. 統合を有効または無効にするには、[Azure AD 組織の LinkedIn の統合の同意](linkedin-integration.md)に関するページに記載されている手順に従います。
2. LinkedIn の統合を組織で発表するときは、[Microsoft のアプリおよびサービスで LinkedIn の情報](https://support.office.com/article/about-linkedin-information-and-features-in-microsoft-apps-and-services-dc81cc70-4d64-4755-9f1c-b9536e34d381)に関する FAQ にユーザーの注意を促します。 この記事では、LinkedIn の情報が表示される場所、[データの共有とプライバシー](https://support.microsoft.com/office/your-data-ae9c08a7-4d06-45b5-a065-320a97bc1400)、[アカウントの接続方法](https://support.microsoft.com/office/connect-your-linkedin-and-work-or-school-accounts-c7c245f2-fa56-4c9b-ba20-3fceb23c5772)などについて説明します。

管理者は、LinkedIn の統合についてユーザーに発表し、[LinkedIn の統合でのデータの共有とプライバシー](https://support.microsoft.com/office/your-data-ae9c08a7-4d06-45b5-a065-320a97bc1400)に関するすべての情報をユーザーに提供する必要があります。 

## <a name="user-consent-for-data-access-in-microsoft-and-linkedin"></a>Microsoft と LinkedIn でのデータ アクセスに対するユーザーの同意

LinkedIn からアクセスされるデータが Microsoft のサービスに永続的に保存されることはありません。 連絡先を除き、Microsoft からアクセスされるデータが LinkedIn に永続的に格納されることはありません。 [インポートされた連絡先の LinkedIn からの削除](https://www.linkedin.com/help/linkedin/answer/43377)に関するページで説明されているように、Microsoft の連絡先はユーザーが削除するまで LinkedIn に格納されています。

ユーザーは、自分のアカウントを接続すると、LinkedIn からの情報や分析情報を、プロファイル カードなどの Microsoft のアプリで見ることができます。 また、ユーザーは、LinkedIn での自分のネットワーク エクスペリエンスが、Microsoft からの情報で改善および拡充されることも期待できます。
組織のユーザーが、自分の LinkedIn アカウントと職場または学校の Microsoft アカウントに接続する際は、2 つのオプションがあります。

* 両方のアカウントからアクセスされるデータに対するアクセス許可を付与します。 つまり、ユーザーは、Microsoft の職場または学校アカウントに LinkedIn アカウントのデータにアクセスする許可を与え、[LinkedIn アカウントに Microsoft の職場または学校のデータにアクセスする](https://www.linkedin.com/help/linkedin/answer/84077)許可を与えます。
* Microsoft の職場または学校アカウントによってアクセスされる LinkedIn のデータだけに対するアクセス許可を付与します。

ユーザーは、いつでもアカウントの接続を解除してデータのアクセス許可を削除することができ、各自の LinkedIn プロフィールを Microsoft のアプリで閲覧できるかどうかなど、[自分の LinkedIn プロフィールの公開方法を制御](https://www.linkedin.com/help/linkedin/answer/83)することができます。

### <a name="linkedin-account-data"></a>LinkedIn アカウントのデータ

Microsoft のアカウントと LinkedIn のアカウントを接続するときに、次のデータを Microsoft に提供することを LinkedIn に許可します。

* プロファイル データ - LinkedIn の ID、連絡先情報、[LinkedIn プロファイル](https://www.linkedin.com/help/linkedin/answer/15493)上で他のユーザーと共有する情報などです。
* 関心についてのデータ - フォローしている人やトピック、コース グループ、お気に入りで共有するコンテンツなど、LinkedIn での関心を含みます。
* サブスクリプション データ - LinkedIn のアプリケーションとサービスおよび関連付けられたデータに対するサブスクリプションです。 
* 接続データ - 一次接続のプロファイルや連絡先情報などの [LinkedIn ネットワーク](https://www.linkedin.com/help/linkedin/answer/110)です。

LinkedIn からアクセスされるデータが Microsoft のサービスに永続的に保存されることはありません。 Microsoft による個人データの使用について詳しくは、「[Microsoft のプライバシーに関する声明](https://privacy.microsoft.com/privacystatement/)」をご覧ください。

### <a name="microsoft-work-or-school-account-data"></a>Microsoft の職場または学校アカウントのデータ

Microsoft のアカウントと LinkedIn のアカウントを接続するときに、次のデータを LinkedIn に提供することを Microsoft に許可します。

* プロファイル データ - 名、姓、プロフィール写真、メール アドレス、上司、部下などの情報です。
* 予定表データ - 予定表の会議、日時、場所、出席者の連絡先情報などです。 議題、内容、タイトルなどの会議に関する情報は、予定表のデータには含まれません。
* 関心についてのデータ - Cortana や Bing for Business などの Microsoft のサービスの使用に基づく、アカウントに関連付けられている関心に関する情報です。
* サブスクリプション データ - Microsoft 365 などの Microsoft のアプリとサービスに対して組織によって提供するサブスクリプションです。
* 連絡先データ - 頻繁に通信または共同作業する人の連絡先情報など、Outlook、Skype、他の Microsoft アカウント サービスでの連絡先リストです。 連絡先情報は、たとえば接続の提案、連絡先の整理、連絡先に関する更新の表示などのために、LinkedIn によって定期的にインポートされ、格納されて、使用されます。

連絡先を除き、Microsoft からアクセスされるデータが LinkedIn に永続的に格納されることはありません。 Microsoft の連絡先は、ユーザーが削除するまで LinkedIn に格納されます。 詳しくは、[インポートされた連絡先の LinkedIn からの削除](https://www.linkedin.com/help/linkedin/answer/43377)に関するページをご覧ください。

LinkedIn での個人データの使用に関して詳しくは、[LinkedIn のプライバシー ポリシー](https://www.linkedin.com/legal/privacy-policy)をご覧ください。 LinkedIn のサービス、データ転送、およびストレージの場合、データを欧州連合と米国の間で双方向に伝送でき、プライバシーは[欧州連合のデータ転送](https://www.linkedin.com/help/linkedin/answer/62533)に従って保護されます。

## <a name="next-steps"></a>次のステップ

* [職場または学校アカウントを使用する Microsoft アプリケーションでの LinkedIn](https://www.linkedin.com/help/linkedin/answer/84077)
