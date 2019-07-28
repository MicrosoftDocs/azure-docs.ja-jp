---
title: B2B ゲスト ユーザーのプロパティ - Azure Active Directory | Microsoft Docs
description: 招待に応じる前と後の Azure Active Directory B2B ゲスト ユーザーのプロパティと状態
services: active-directory
ms.service: active-directory
ms.subservice: b2b
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1416dacd65024457e713547223f5c35290b3d15
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65768162"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Azure Active Directory B2B コラボレーション ユーザーのプロパティ

この記事では、招待に応じる前と後の Azure Active Directory (Azure AD) 内の B2B ゲスト ユーザー オブジェクトのプロパティと状態について説明します。 Azure AD 企業間 (B2B) コラボレーション ユーザーは、UserType = Guest のユーザーです。 このゲスト ユーザーは通常、パートナー組織に属しており、既定では招待側のディレクトリ内で制限された権限を付与されています。

招待側の組織のニーズに応じて、Azure AD B2B コラボレーション ユーザーは、以下のいずれかのアカウント状態になります。

- 状態 1:Azure AD の外部インスタンスに所属し、招待側組織のゲスト ユーザーとして表されます。 この場合、B2B ユーザーは招待されたテナントに属している Azure AD アカウントを使用してサインインします。 パートナー組織が Azure AD を使用しない場合でも、Azure AD にゲスト ユーザーが作成されます。 要件は、ゲスト ユーザーが招待に応じること、および Azure AD が彼らの電子メール アドレスを検証することです。 この状態は、Just-In-Time (JIT) テナント、または "バイラル テナント" とも呼ばれます。

- 状態 2:Microsoft アカウントまたは他のアカウントに所属し、ホスト組織のゲスト ユーザーとして表されます。 この場合、ゲスト ユーザーは Microsoft アカウントまたはソーシャル アカウント (google.com など) でサインインします。 招待されたユーザーの ID は、招待に応じる際に招待側組織のディレクトリ内に Microsoft アカウントとして作成されます。

- 状態 3:ホスト組織のオンプレミス Active Directory に所属し、ホスト組織の Azure AD と同期されます。 Azure AD Connect を使用し、UserType = Guest の Azure AD B2B ユーザーとして、パートナー アカウントをクラウドと同期することができます。 [ローカルで管理されたパートナーのアカウントにクラウド リソースへのアクセスを許可する方法](hybrid-on-premises-to-cloud.md)に関する記事を参照してください。

- 状態 4:ホスト組織の Azure AD に所属し、UserType = Guest で、資格情報はホスト組織によって管理されています。

  ![ユーザーの 4 つの状態を示す図](media/user-properties/redemption-diagram.png)


ここで、Azure AD B2B コラボレーション ユーザーが Azure AD でどのように見えるかを見てみましょう。

### <a name="before-invitation-redemption"></a>招待に応じる前

状態 1 アカウントと状態 2 アカウントは、ゲスト ユーザー自身の資格情報を使用して共同作業するようにゲスト ユーザーを招待した結果です。 招待が最初にゲスト ユーザーに送信されると、アカウントがディレクトリに作成されます。 認証はゲスト ユーザーの ID プロバイダーによって実行されるため、このアカウントには関連付けられた資格情報はありません。 ディレクトリ内のゲスト ユーザー アカウントの **[ソース]** プロパティは **[ユーザーが招待されました]** に設定されています。 

![招待に応じる前のユーザー プロパティを示すスクリーンショット](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>招待に応じた後

ゲスト ユーザーが招待を受け入れると、 **[ソース]** プロパティはゲスト ユーザーの ID プロバイダーに基づいて更新されます。

状態 1 のゲスト ユーザーの場合、 **[ソース]** は **[外部の Azure Active Directory]** です。

![オファーに応じた後の状態 1 のゲスト ユーザー](media/user-properties/after-redemption-state1.png)

状態 2 のゲスト ユーザーの場合、 **[ソース]** は **[Microsoft アカウント]** です。

![オファーに応じた後の状態 2 のゲスト ユーザー](media/user-properties/after-redemption-state2.png)

状態 3 と状態 4 のゲスト ユーザーの場合、次のセクションで説明するように、 **[ソース]** プロパティは **[Azure Active Directory]** または **[Windows Server Active Directory]** に設定されます。

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Azure AD B2B コラボレーション ユーザーの主要なプロパティ
### <a name="usertype"></a>UserType
このプロパティは、ユーザーとホスト テナントとの関係を示します。 このプロパティは次の 2 つの値を取ります。
- メンバー:この値は、ホスト組織の従業員や組織の給与支払い名簿にあるユーザーを示します。 たとえば、このユーザーは、内部専用のサイトにアクセスできることが想定されます。 このユーザーは外部コラボレーターとは見なされません。

- ゲスト:この値は、社内ユーザーと見なされないユーザー (外部コラボレーター、パートナー、顧客など) を示します。 このようなユーザーに、CEO の社内メモの送信や各種手当の給付が行われることは想定されません。

  > [!NOTE]
  > UserType は、ユーザーのサインイン方法、ユーザーのディレクトリ ロールなどとは関係ありません。 このプロパティは、単にユーザーとホスト組織との関係を示しており、組織はこのプロパティに基づくポリシーを強制できます。

### <a name="source"></a>source
このプロパティは、ユーザーのサインイン方法を示します。

- ユーザーが招待されました:このユーザーは招待されましたが、まだ招待に応じていません。

- 外部 Active Directory:このユーザーは外部組織に所属し、他の組織に属している Azure AD アカウントを使用して認証を行います。 この種類のサインインは、状態 1 に対応しています。

- Microsoft アカウント:このユーザーは Microsoft アカウントに所属し、Microsoft アカウントを使用して認証を行います。 この種類のサインインは、状態 2 に対応しています。

- Windows Server Active Directory:このユーザーは、この組織に属しているオンプレミス Active Directory からサインインします。 この種類のサインインは、状態 3 に対応しています。

- Azure Active Directory:このユーザーは、この組織に属している Azure AD アカウントを使用して認証されます。 この種類のサインインは、状態 4 に対応しています。
  > [!NOTE]
  > Source と UserType は、独立したプロパティです。 Source の値は、UserType の特定の値を意味しません。

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Azure AD B2B ユーザーをゲストではなくメンバーとして追加できるか
通常は、Azure AD B2B ユーザーとゲスト ユーザーは同義です。 そのため、Azure AD B2B コラボレーション ユーザーは、既定では UserType = Guest のユーザーとして追加されます。 ただし、一部のケースでは、パートナー組織がより大きな組織のメンバーであり、ホスト組織もその組織に所属しています。 そのような場合は、ホスト組織がパートナー組織のユーザーをゲストではなくメンバーとして扱いたいことがあります。 Azure AD B2B Invitation Manager API を使用して、パートナー組織のユーザーをホスト組織にメンバーとして追加または招待します。

## <a name="filter-for-guest-users-in-the-directory"></a>ディレクトリのゲスト ユーザーのフィルター処理

![ゲスト ユーザーのフィルターを示すスクリーンショット](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>UserType の変換
PowerShell を使用して、UserType をメンバーからゲストに、またはその逆に変換できます。 ただし、UserType プロパティはユーザーと組織の関係を表しています。 そのため、このプロパティは、ユーザーと組織の関係が変化した場合にのみ変更するようにします。 ユーザーの関係が変化した場合に、ユーザー プリンシパル名 (UPN) を変更する必要はありますか。 また、同じリソースへのアクセス権を引き続きユーザーに持たせる必要がありますか。 メールボックスを割り当てる必要があるか、などの疑問です。 PowerShell を使用してアトミック アクティビティとして UserType を変更することはお勧めしません。 また、PowerShell を使用してこのプロパティを変更できなくなる場合に備えて、この値には依存しないことをお勧めします。

## <a name="remove-guest-user-limitations"></a>ゲスト ユーザー制限の削除
ゲスト ユーザーに、より高い権限を付与したい場合があります。 ゲスト ユーザーを任意のロールに追加することができます。また、メンバーと同じ権限を付与するために、ディレクトリでの既定のゲスト ユーザー制限を削除することもできます。

既定の制限を無効にして、会社のディレクトリのゲスト ユーザーにメンバー ユーザーと同じアクセス許可を持たせることができます。

![ユーザー設定の [外部ユーザー] オプションを示すスクリーンショット](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Exchange のグローバル アドレス一覧にゲスト ユーザーを表示できますか。
はい。 既定では、ゲスト オブジェクトは組織のグローバル アドレス一覧には表示されませんが、Azure Active Directory PowerShell を使用してそれらを表示できます。 詳細については、「[Office 365 グループでゲスト アクセスを管理する](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups?redirectSourcePath=%252fen-us%252farticle%252fmanage-guest-access-in-office-365-groups-9de497a9-2f5c-43d6-ae18-767f2e6fe6e0&view=o365-worldwide#faq)」の「**グローバル アドレス一覧にゲスト オブジェクトを表示できますか?** 」を参照してください。 

## <a name="next-steps"></a>次の手順

* [Azure AD B2B コラボレーションとは](what-is-b2b.md)
* [B2B コラボレーション ユーザーのトークン](user-token.md)
* [B2B コラボレーション ユーザーの要求マッピング](claims-mapping.md)
