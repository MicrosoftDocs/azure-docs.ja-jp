---
title: 秘密度ラベルを使用して Azure Active Directory 内のリソースへの外部アクセスを制御します。
description: 秘密度ラベルを、外部アクセスの全体的なセキュリティ プランの一部として使用します。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fc8ceddce4d41244d72632db058aa58c0d919db
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565174"
---
# <a name="control-access-with-sensitivity-labels"></a>秘密度ラベルを使用してアクセスを制御する 

[秘密度ラベル](/microsoft-365/compliance/sensitivity-labels) は、Office 365 アプリケーション内、および Microsoft Teams、Microsoft 365 グループ、SharePoint サイトなどのコンテナー内のコンテンツに対するアクセスを制御するのに役立ちます。 これは、ユーザーのコラボレーションや生産能力を損なうことなく、コンテンツを保護することができます。 秘密度ラベルを使用することにより、データを保護し、コンプライアンスとセキュリティ ポリシーを順守しながら、デバイス、アプリ、サービス間で組織のコンテンツを送信できます。 

秘密度ラベルを使用すると、次のことができます。

* **保護設定を追加せずにコンテンツを分類する**: コンテンツが使用および共有されるときにコンテンツと共に保持およびローミングされる分類をコンテンツ (ステッカーなど) に割り当てることができます。 この分類を使用して、使用状況レポートを生成し、実際の機密コンテンツのアクティビティ データを確認できます。

* **暗号化、透かし、アクセス制限などの保護設定を適用する**: たとえば、ユーザーはドキュメントや電子メールに "社外秘" ラベルを適用できます。そのラベルによって、[コンテンツを暗号化](/microsoft-365/compliance/encryption-sensitivity-labels)して "社外秘" の透かしを追加できます。 さらに、SharePoint サイトなどの[コンテナーに秘密度ラベルを適用](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)し、外部ユーザーが、コンテナーに含まれるコンテンツにアクセスできるかどうかを強制できます。

電子メールやその他のコンテンツの秘密度ラベルは、コンテンツと共に移動します。 コンテナーの秘密度ラベルはコンテナーへのアクセスを制限できますが、コンテナー内のコンテンツはラベルを継承しません。 たとえば、ユーザーは保護されたサイトからコンテンツを取得してダウンロードし、そのコンテンツにも秘密度ラベルが付いていない限り、制限なしで共有できます。

 >[!NOTE]
>秘密度ラベルを適用するには、ユーザーは Microsoft の職場または学校のアカウントにサインインする必要があります。 

 
## <a name="permissions-necessary-to-create-and-manage-sensitivity-levels"></a>秘密度ラベルの作成および管理に必要なアクセス許可

秘密度ラベルを作成するコンプライアンス チームのメンバーには、Microsoft 365 コンプライアンス センター、Microsoft 365 セキュリティセンター、またはセキュリティ/コンプライアンス センターへのアクセス許可が必要です。

既定では、テナントの全体管理者はこれらの管理センターにアクセスでき、コンプライアンス責任者や他のユーザーに対して、必ずしもテナント管理者のすべてのアクセス許可を与えなくても、アクセスを許可することができます。この委任された制限付き管理者アクセスについては、コンプライアンス データ管理者、コンプライアンス管理者、またはセキュリティ管理者の役割グループにユーザーを追加します。

 

## <a name="determine-your-sensitivity-label-strategy"></a>秘密度ラベル戦略を決定する

コンテンツへの外部アクセスの管理について検討する場合、次のことを決定します。

**すべてのコンテンツおよびコンテナーについて**

* ビジネスへの影響には高、中、または低 (HBI、MBI、LBI) のどれを定義しますか? 特定の種類のコンテンツが不適切に共有された場合の組織への影響を考慮してください。

   * クレジット カードやパスポート番号など、特定の種類の本質的に[機密性の高いコンテンツ](/microsoft-365/compliance/apply-sensitivity-label-automatically)を含むコンテンツ

   * 特定のグループまたはユーザー (コンプライアンス責任者、財務責任者、経営幹部など) によって作成されたコンテンツ

   * 特定のライブラリまたはサイト内のコンテンツ。 たとえば、組織戦略や個人の財務データをホストするコンテナー

   * その他の条件

* 外部ユーザーによるアクセスを制限する必要があるのは、どのカテゴリのコンテンツ (HBI コンテンツなど) ですか?

   * 制限には、コンテナーへのアクセスの制限やコンテンツの暗号化などのアクションを含めることができます。

* HBI データ、サイト、または Microsoft 365 グループにはどのような既定値を設定する必要がありますか?

* 秘密度ラベルを使用して、[ラベルを付け、監視](/microsoft-365/compliance/sensitivity-labels)しますか、それとも[暗号化を適用](/microsoft-365/compliance/encryption-sensitivity-labels)または[コンテナー アクセス制限を適用](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)しますか?

**電子メールおよびコンテンツについて**

* コンテンツに[秘密度ラベルを自動的に適用](/microsoft-365/compliance/apply-sensitivity-label-automatically)しますか、それとも手動で適用しますか?

   * 手動で行うことを選択した場合、[ユーザーがラベルを適用することを推奨](/microsoft-365/compliance/apply-sensitivity-label-automatically)しますか?

**コンテナーについて**

* M365 グループ、Teams、または SharePoint サイトで、秘密度ラベルを使用してアクセスを制限する必要があるかどうかを決定する基準は何ですか?

* 引き続きこれらのコンテナー内のコンテンツのみにラベルを付ける必要がありますか、それとも SharePoint および OneDrive の既存のファイルに[自動的にラベルを付ける](/microsoft-365/compliance/apply-sensitivity-label-automatically)必要がありますか?

秘密度ラベルの使用方法に関するその他のアイデアについては、これらの[秘密度ラベルの一般的なシナリオ](/microsoft-365/compliance/get-started-with-sensitivity-labels)を参照してください。

### <a name="sensitivity-labels-on-email-and-content"></a>メールおよびコンテンツの秘密度ラベル

秘密度ラベルをドキュメントまたは電子メールに割り当てる場合、それは、コンテンツに適用されるスタンプのようなものであり、カスタマイズ可能なクリア テキストで、永続的です。 

* **カスタマイズ可能** とは、組織に適したラベルを作成し、それらが適用されたときの動作を決定できることを意味します。

* **クリア テキスト** とは、アイテムのメタデータの一部であり、アプリケーションやサービスによって読み取り可能であるため、独自の保護アクションを適用できることを意味します。

* **永続的** とは、ラベルとそれに関連する保護がコンテンツと共にローミングし、ポリシーを適用および強制するための基準になることを意味します。

 

> [!NOTE]
> コンテンツの各アイテムには、単一の秘密度ラベルを適用できます。


### <a name="sensitivity-labels-on-containers"></a>コンテナーの秘密度ラベル

秘密度ラベルは、[Microsoft 365 グループ](../enterprise-users/groups-assign-sensitivity-labels.md)、[Microsoft Teams](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)、[SharePoint サイト](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)などのコンテナーに適用できます。 サポートされているコンテナーにこの秘密度ラベルを適用すると、ラベルにより、接続されたサイトまたはグループに分類と保護の設定が自動的に適用されます。 これらのコンテナーの秘密度ラベルにより、コンテナーの次の側面を制御できます。

* **プライバシー**。 サイトを表示できるユーザー (特定のユーザー、すべての内部ユーザー、またはすべてのユーザー) を選択できます。

* **外部ユーザー アクセス**。 グループ所有者がゲストをグループに追加できるかどうかを制御します。

* **アンマネージド デバイスからのアクセス**。 アンマネージド デバイスでコンテンツにアクセスできるかどうか、およびその方法を決定します。

 

![秘密度ラベルの編集のスクリーンショット](media/secure-external-access/8-edit-label.png)

 

SharePoint サイトなどのコンテナーに秘密度ラベルを適用しても、それに含まれるコンテンツには適用されません。コンテナーの秘密度ラベルは、コンテナー内のコンテンツへのアクセスを制御します。 

* コンテナー内のコンテンツにラベルを自動的に適用する場合は、「[コンテンツに機密度を自動的に適用する](/microsoft-365/compliance/apply-sensitivity-label-automatically)」を参照してください。

* ユーザーがこのコンテンツに手動でラベルを適用できるようにする場合は、[SharePoint および OneDrive で Office ファイルの秘密度ラベルを有効にしている](/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files)ことを確認してください。

### <a name="plan-to-implement-sensitivity-labels"></a>秘密度ラベルの実装を計画する

秘密度ラベルの使用方法、およびそれらを適用するコンテンツとサイトを決定したら、実装に役立つ次のドキュメントを参照してください。

1. [秘密度ラベルの使用を開始する](/microsoft-365/compliance/get-started-with-sensitivity-labels)

2. [デプロイ戦略を作成する](/microsoft-365/compliance/get-started-with-sensitivity-labels)

3. [秘密度ラベルを作成して発行する](/microsoft-365/compliance/create-sensitivity-labels)

4. [秘密度ラベルを使用して暗号化を適用てコンテンツへのアクセスを制限する](/microsoft-365/compliance/encryption-sensitivity-labels)

5. [チーム、グループ、およびサイトで秘密度ラベルを使用する](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)

6. [SharePoint および OneDrive で Office ファイルの秘密度ラベルを有効にする](/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files)

### <a name="next-steps"></a>次のステップ

リソースへの外部アクセスをセキュリティで保護する方法については、次の記事を参照してください。 アクションは、表示されている順序で実行することをお勧めします。

1. [外部アクセスに必要なセキュリティ体制を決定する](1-secure-access-posture.md)

2. [現在の状態を理解する](2-secure-access-current-state.md)

3. [ガバナンス プランを作成する](3-secure-access-plan.md)

4. [セキュリティにグループを使用する](4-secure-access-groups.md)

5. [Azure AD B2B に移行する](5-secure-access-b2b.md)

6. [エンタイトルメント管理を使用してアクセスをセキュリティで保護する](6-secure-access-entitlement-managment.md)

7. [条件付きアクセス ポリシーを使用してアクセスをセキュリティで保護する](7-secure-access-conditional-access.md)

8. [秘密度ラベルを使用してアクセスをセキュリティで保護する](8-secure-access-sensitivity-labels.md) (この記事)

9. [Microsoft Teams、OneDrive、SharePoint へのアクセスをセキュリティで保護する](9-secure-access-teams-sharepoint.md)