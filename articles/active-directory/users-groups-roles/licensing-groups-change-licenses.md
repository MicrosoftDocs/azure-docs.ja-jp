---
title: グループを使用して製品ライセンスにユーザーを移行する方法 - Azure Active Directory | Microsoft Docs
description: グループベースのライセンスを使用して異なる製品ライセンス (Office 365 Enterprise E1 と E3) にグループ内のユーザーを移行するために推奨されるプロセスについて説明します
services: active-directory
keywords: Azure AD のライセンス
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b65eb38b6c8102295f40b5e169ae7c32a2342a2
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201366"
---
# <a name="change-the-license-for-a-single-user-in-a-licensed-group-in-azure-active-directory"></a>Azure Active Directory のライセンス グループ内の 1 人のユーザーのライセンスを変更する

この記事では、グループベースのライセンスの使用時に製品ライセンス間でユーザーを移動する場合に推奨される方法について説明します。 この手法の目的は、移行中にサービスやデータが失われないようにすることです。ユーザーは製品間でシームレスに切り替える必要があります。 2 種類の移行プロセスについて説明します。

- 競合するサービス プランが含まれていない製品ライセンス間の単純な移行 (例: Office 365 Enterprise E3 と Office 365 Enterprise E5 間の移行)。

- 競合するサービス プランが一部含まれている製品間の複雑な移行 (例: Office 365 Enterprise E1 と Office 365 Enterprise E3 間の移行)。 競合の詳細については、[競合するサービス プラン](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-group-problem-resolution-azure-portal#conflicting-service-plans)と[同時に割り当てることができないサービス プラン](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-product-and-service-plan-reference#service-plans-that-cannot-be-assigned-at-the-same-time)についての記事を参照してください。

この記事には、移行と検証の手順を実行するために使用できる PowerShell のサンプル コードを紹介します。 このコードは、手動でこの手順を実行することが難しい大規模な運用の場合に特に役立ちます。

## <a name="before-you-begin"></a>開始する前に
移行を開始する前に、移行されるすべてのユーザーについて、特定の前提条件が真であることを確認することが重要です。 すべてのユーザーについて前提条件が真でない場合、一部のユーザーの移行に失敗することがあります。 その結果、一部のユーザーがサービスまたはデータへのアクセスを失う可能性があります。 次の前提条件を確認する必要があります。

- ユーザーには、グループベースのライセンスを使用して*ソース ライセンス*が割り当てられている。 移行元となる製品のライセンスは、1 つのソース グループから継承されており、直接割り当てられていません。

    >[!NOTE]
    >ライセンスが直接的にも割り当てられている場合、*ターゲット ライセンス*の適用が妨げられる可能性があります。 [直接ライセンス割り当てとグループ ライセンス割り当て](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-group-advanced#direct-licenses-coexist-with-group-licenses)の詳細について学習してください。 [PowerShell スクリプト](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-ps-examples#check-if-user-license-is-assigned-directly-or-inherited-from-a-group)を使用して、ユーザーに直接ライセンスが割り当てられているかどうかを確認することができます。

- 対象製品について使用可能なライセンスが十分にある。 十分なライセンスがない場合、一部のユーザーが*ターゲット ライセンス*を取得できないこともあります。 [使用可能なライセンス数を確認する](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products)ことができます。

- ユーザーは、*ターゲット ライセンス*と競合するか、または*ソース ライセンス*の削除を妨げる可能性があるその他の製品ライセンスを割り当てられていない。 たとえば、Workplace Analytics や Project Online のようなアドオン製品からのライセンスは、他の製品に依存しています。

- グループが自身の環境でどのように管理されているかを把握している。 たとえば、グループをオンプレミスで管理し、それらを Azure AD Connect を介して Azure Active Directory (Azure AD) に同期している場合は、オンプレミス システムを使用してユーザーを追加または削除できます。 変更が Azure AD に同期され、グループ ベースのライセンスによって取得されるまでに時間がかかります。 Azure AD の動的グループのメンバーシップを使用している場合は、代わりに属性を使用してユーザーを追加または削除します。 ただし、移行プロセス全体は変わりません。 唯一の違いは、グループ メンバーシップに対してユーザーを追加または削除する方法です。

## <a name="migrate-users-between-products-that-dont-have-conflicting-service-plans"></a>競合するサービス プランがない製品間のユーザーの移行

移行の目的は、グループベースのライセンスを使用して、ユーザー ライセンスを*ソース ライセンス* (この例では、Office 365 Enterprise E3) から*ターゲット ライセンス* (この例では、Office 365 Enterprise E5) に変更することです。 このシナリオの 2 つの製品は、競合するサービス プランを含まないため、競合することなく同時に完全に割り当てることができます。 移行中は常に、ユーザーがサービスまたはデータへのアクセスを失うことのないようにします。 移行は小規模な「バッチ」で行います。 各バッチの結果を検証し、処理中に発生するおそれのある問題の範囲を最小限に抑えることができます。 全体として、プロセスは次のとおりです。

1.  ユーザーはソース グループのメンバーであり、そのグループから*ソース ライセンス*を継承しています。

2.  "*ターゲット ライセンス*" を持つターゲット グループを作成しますが、メンバーはいません。

3.  ユーザーのバッチをターゲット グループに追加します。 グループベースのライセンスは変更を取得して*ターゲット ライセンス*を割り当てます。 このプロセスには、バッチのサイズとテナント内での他の活動に応じて非常に長い時間がかかる場合があります。

4.  ユーザーのバッチがグループベースのライセンスによって完全に処理されたことを検証します。 *ターゲット ライセンス*が各ユーザーに割り当てられていることを確認します。 他の製品との競合やライセンスの不足など、ユーザーが最終的にエラー状態になっていないことを確認します。 エラーの詳細については、[Active Directory のライセンス グループの問題の解決](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-group-problem-resolution-azure-portal)についての記事を参照してください。

5.  この時点で、ユーザーには*ソース ライセンス*と *ターゲット ライセンス*の両方が割り当てられています。

6.  ソース グループから同じユーザー バッチを削除します。 グループベースのライセンスが変更に応答し、*ソース ライセンス*がユーザーから削除されます。

7.  後続のユーザー バッチに対して処理を繰り返します。

### <a name="migrate-a-single-user-by-using-the-azure-portal"></a>Azure Portal の使用による単一ユーザーの移行

これは、単一ユーザーを移行する方法を示す簡単なチュートリアルです。

**手順 1**:ユーザーは、グループから継承された*ソース ライセンス*を持っています。 ライセンスの直接の割り当てはありません。

![ソース ライセンスがグループから継承されているユーザー](./media/licensing-groups-change-licenses/UserWithSourceLicenseInherited.png)

**手順 2**:ユーザーがターゲット グループに追加され、グループベースのライセンスによって変更が処理されます。 これでユーザーは、グループから継承された*ソース ライセンス*と*ターゲット ライセンス*の両方を持つことになります。

![ソース ライセンスとターゲット ライセンスの両方がグループから継承されているユーザー](./media/licensing-groups-change-licenses/UserWithBothSourceAndTargetLicense.png)

**手順 3**:ユーザーがソース グループから削除され、グループベースのライセンスによって変更が処理されます。 これで、ユーザーは*ターゲット ライセンス*のみを持つことになります。

![ターゲット ライセンスがグループから継承されているユーザー](./media/licensing-groups-change-licenses/UserWithTargetLicenseAssigned.png)

### <a name="automate-migration-by-using-azure-powershell"></a>Azure PowerShell の使用による移行の自動化
次のスニペットは、大規模な操作の移行プロセスを自動化する方法を示します。

> [!NOTE]
> このサンプル コードでは、この記事の[最後のセクション](#powershell-automation-of-migration-and-verification-steps)に記載されている PowerShell 関数を使用します。

```powershell
# A batch of users that we want to migrate in this iteration.
# The batch can be specified as an array of User Principal Names (string) or ObjectIds (Guid).
# Note: The batch can be loaded from a text file that represents a larger batch of users that we want to migrate.
[string[]]$usersToMigrate = 'MigrationUser@tailspinonline.com','MigrationUser2@tailspinonline.com'

############### NON-CONFLICTING LICENSES SCENARIO ################

# The source group and source license to remove the user from.
$sourceGroupId = [Guid]'b82c04f0-ce30-4ff1-bac7-735d92d83036'
$sourceSkuId = 'TailspinOnline:ENTERPRISEPACK'      # <-- This is the Office 365 Enterprise E3 product.

# The target group and target license to assign to the user.
$targetGroupId = [Guid]'bcf279d1-5ad5-46a5-b469-4b8a552aa2fe'
$targetSkuId = 'TailspinOnline:ENTERPRISEPREMIUM'   # <-- This is the Office 365 Enterprise E5 product.

if(-Not (VerifyAssumptions $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId))
{
    throw "Some users did not pass validation checks. See the output for details. Aborting migration process."
}

Write-Host "STEP 1: Adding users to the target group $targetGroupId. This will assign the target license $targetSkuId to all users"
AddUsersToGroup $usersToMigrate $targetGroupId

# Verify that the target license shows up in the conflict state for each user on the list.
# This step runs in a loop, forever, until all of the users are in the expected state.
# Group-based licensing (GBL) can take some time to reflect the changes on users.
# As a result, the loop should terminate after a period of time that's dependent on the size of the user collection.
# Note: If the loop hasn't terminated after a long period of time, stop the script.
#       Inspect the users that are reported as not yet in the expected state.
#       Verify that the users are not blocked for some other reason.
ExecuteVerificationLoop ${function:VerifySourceandTargetLicensePresent} 'STEP 2: Checking if all users still have the source license and now also have the target license from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId

# Now it's safe to remove the users from the source group.
Write-Host "STEP 3: Removing users from the source group $sourceGroupId. This will remove the source license $sourceSkuId."
RemoveUsersFromGroup $usersToMigrate $sourceGroupId

# Verify that the target license is now active on each user and the source license is removed.
ExecuteVerificationLoop ${function:VerifySourceLicenseRemovedAndTargetLicenseAssignedFromGroup} 'STEP 4: Checking if all users have source license removed and target license assigned from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId
```

**サンプル出力 (2 人のユーザーの移行)**

```powershell
Verifying initial assumptions:
Enough TailspinOnline:ENTERPRISEPREMIUM licenses available (13) for users: 2.
migrationuser@tailspinonline.com                OK
migrationuser2@tailspinonline.com               OK
Checks passed for all 2 users.

STEP 1: Adding users to the target group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe. This will assign the target license TailspinOnline:ENTERPRISEPREMIUM to all users
Adding user MigrationUser@tailspinonline.com to group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe
Adding user MigrationUser2@tailspinonline.com to group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe

STEP 2: Checking if all users still have the source license and now also have the target license from target group. Check iteration: 1

MigrationUser@tailspinonline.com                Expected state: NO
MigrationUser2@tailspinonline.com               Expected state: NO

Total users checked: 2. In expected state: 0. Not yet: 2
Not all users are in expected state. Waiting 60 seconds to try again.

STEP 2: Checking if all users still have the source license and now also have the target license from target group. Check iteration: 2

MigrationUser@tailspinonline.com                Expected state: YES
MigrationUser2@tailspinonline.com               Expected state: YES

Total users checked: 2. In expected state: 2. Not yet: 0
Check passed for all users. Exiting check loop.

STEP 3: Removing users from the source group b82c04f0-ce30-4ff1-bac7-735d92d83036. This will remove the source license TailspinOnline:ENTERPRISEPACK.
Removing user MigrationUser@tailspinonline.com from group b82c04f0-ce30-4ff1-bac7-735d92d83036
Removing user MigrationUser2@tailspinonline.com from group b82c04f0-ce30-4ff1-bac7-735d92d83036

STEP 4: Checking if all users have source license removed and target license assigned from target group. Check iteration: 1

MigrationUser@tailspinonline.com                Expected state: NO
MigrationUser2@tailspinonline.com               Expected state: NO

Total users checked: 2. In expected state: 0. Not yet: 2
Not all users are in expected state. Waiting 60 seconds to try again.

STEP 4: Checking if all users have source license removed and target license assigned from target group. Check iteration: 2

MigrationUser@tailspinonline.com                Expected state: YES
MigrationUser2@tailspinonline.com               Expected state: YES

Total users checked: 2. In expected state: 2. Not yet: 0
Check passed for all users. Exiting check loop.
```

## <a name="migrate-users-between-products-that-have-conflicting-service-plans"></a>競合するサービス プランがある製品間のユーザーの移行
移行の目的は、グループベースのライセンスを使用して、ユーザー ライセンスを*ソース ライセンス* (この例では、Office 365 Enterprise E1) から*ターゲット ライセンス* (この例では、Office 365 Enterprise E3) に変更することです。 このシナリオの 2 つの製品には競合するサービス プランが含まれているため、ユーザーをシームレスに移行するには競合を回避する必要があります。 これらの競合の詳細については、[Active Directory のライセンス グループの問題の解決に関する記事の「サービス プランの競合」](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-group-problem-resolution-azure-portal#conflicting-service-plans)を参照してください。 移行中は常に、ユーザーがサービスまたはデータへのアクセスを失うことのないようにします。 移行は小規模な「バッチ」で行います。 各バッチの結果を検証し、処理中に発生するおそれのある問題の範囲を最小限に抑えることができます。 全体として、プロセスは次のとおりです。

1.  ユーザーはソース グループのメンバーであり、そのグループから*ソース ライセンス*を継承しています。

2.  "*ターゲット ライセンス*" を持つターゲット グループを作成しますが、メンバーはいません。

3.  ユーザーのバッチをターゲット グループに追加します。 グループベースのライセンスは変更を取得して*ターゲット ライセンス*を割り当てようとします。 割り当ては、2 つの製品内のサービス間の競合が原因で失敗します。 グループ ベースのライセンスは、失敗を各ユーザーのエラーとして記録します。 このプロセスには、バッチのサイズとテナント内での他の活動に応じて非常に長い時間がかかる場合があります。

4.  ユーザーのバッチがグループベースのライセンスによって完全に処理されたことを検証します。 各ユーザーに競合エラーが記録されていることを確認します。 一部のユーザーが最終的に予期しないエラー状態にならなかったことを確認します。 エラーの詳細については、[Active Directory のライセンス グループの問題の解決](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-group-problem-resolution-azure-portal)についての記事を参照してください。

5.  この時点で、ユーザーにはまだ*ソース ライセンス*があり、*ターゲット ライセンス*の競合エラーが発生しています。 ユーザーには*ターゲット ライセンス*がまだ割り当てられていません。

6.  ソース グループから同じユーザー バッチを削除します。 グループベースのライセンスが変更に応答し、*ソース ライセンス*が各ユーザーから削除されます。 競合エラーが同時に削除され (他の製品ライセンスがエラーの原因になっていない場合)、*ターゲット ライセンス*が割り当てられます。 この処理により、移行中にサービスやデータが失われることはありません。

7.  後続のユーザー バッチに対して処理を繰り返します。

### <a name="migrate-a-single-user-by-using-the-azure-portal"></a>Azure Portal の使用による単一ユーザーの移行
これは、単一ユーザーを移行する方法を示す簡単なチュートリアルです。

**手順 1**:ユーザーは、グループから継承された*ソース ライセンス*を持っています。 ライセンスの直接の割り当てはありません。

![ソース ライセンスがグループから継承されているユーザー](./media/licensing-groups-change-licenses/UserWithSourceLicenseInheritedConflictScenario.png)

**手順 2**:ユーザーがターゲット グループに追加され、グループベースのライセンスによって変更が処理されます。 ユーザーにまだ*ソース ライセンス*があるため、*ターゲット ライセンス*は競合によるエラー状態になっています。

![ソース ライセンスがグループから継承され、ターゲット ライセンスがエラー状態のユーザー](./media/licensing-groups-change-licenses/UserWithSourceLicenseAndTargetLicenseInConflict.png)

**手順 3**:ユーザーがソース グループから削除され、グループベースのライセンスによって変更が処理されます。 *ターゲット ライセンス*がユーザーに適用されます。

![ターゲット ライセンスがグループから継承されているユーザー](./media/licensing-groups-change-licenses/UserWithTargetLicenseAssignedConflictScenario.png)


### <a name="automate-migration-by-using-azure-powershell"></a>Azure PowerShell の使用による移行の自動化
次のスニペットは、大規模な操作の移行プロセスを自動化する方法を示します。

> [!NOTE]
> このサンプル コードでは、この記事の[最後のセクション](#powershell-automation-of-migration-and-verification-steps)に記載されている PowerShell 関数を使用します。

```powershell
# A batch of users that we want to migrate in this iteration.
# The batch can be specified as an array of User Principal Names (string) or ObjectIds (Guid).
# Note: The batch can be loaded from a text file that represents a larger batch of users that we want to migrate.
[string[]]$usersToMigrate = 'MigrationUser@tailspinonline.com', 'MigrationUser2@tailspinonline.com'

############### CONFLICTING LICENSES SCENARIO ################

# The source group and source license to remove the user from.
$sourceGroupId = [Guid]'b82c04f0-ce30-4ff1-bac7-735d92d83036'
$sourceSkuId = 'TailspinOnline:STANDARDPACK'             # <-- This is the Office 365 Enterprise E1 product.

# The target group and target license to assign to the user.
$targetGroupId = [Guid]'bcf279d1-5ad5-46a5-b469-4b8a552aa2fe'
$targetSkuId = 'TailspinOnline:ENTERPRISEPACK'           # <-- This is the Office 365 Enterprise E3 product.

# Assumptions before migration:
# 1. Users are already in the source group and they have the source license assigned from that group.
# 2. Users don't have the same source license assigned from another group at the same time,
#    and they don't have the source license assigned directly.
#    IMPORTANT: If Assumption 2 isn't true, removing users from the source group in STEP 3
#               won't result in the target license being correctly applied.
# 3. There are enough available licenses to assign a target license to all of the users that are being migrated.
if(-Not (VerifyAssumptions $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId))
{
    throw "Some users did not pass validation checks. See the output for details. Aborting migration process."
}

Write-Host "STEP 1: Adding users to the target group $targetGroupId. This will put target license $targetSkuId in conflict state with the source license $sourceSkuId"
AddUsersToGroup $usersToMigrate $targetGroupId

# Verify that the target license shows up in the conflict state for each user on the list.
# This step runs in a loop, forever, until all of the users are in the expected state.
# Group-based licensing (GBL) can take some time to reflect the changes on users.
# As a result, the loop should terminate after a period of time that's dependent on the size of the user collection.
# Note: If the loop hasn't terminated after a long period of time, stop the script.
#       Inspect the users that are reported as not yet in the expected state.
#       Verify that the users are not blocked for some other reason.
ExecuteVerificationLoop ${function:VerifySourceLicensePresentAndTargetLicenseInConflictState} 'STEP 2: Checking if all users still have the source license and are in conflict state for license from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId

# Now it's safe to remove the users from the source group.
Write-Host "STEP 3: Removing users from the source group $sourceGroupId. This will remove the source license $sourceSkuId and remove the conflict on target license $targetSkuId which will become assigned."
RemoveUsersFromGroup $usersToMigrate $sourceGroupId

# Verify that the target license is now active on each user and the source license is removed.
ExecuteVerificationLoop ${function:VerifySourceLicenseRemovedAndTargetLicenseAssignedFromGroup} 'STEP 4: Checking if all users have source license removed and target license assigned from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId
```

**サンプル出力 (2 人のユーザーの移行)**

```powershell
Verifying initial assumptions:
Enough TailspinOnline:ENTERPRISEPACK licenses available (61) for users: 2.
migrationuser@tailspinonline.com                OK
migrationuser2@tailspinonline.com               OK
Checks passed for all 2 users.
STEP 1: Adding users to the target group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe. This will put target license TailspinOnline:ENTERPRISEPACK in conflict state with the source license TailspinOnline:STANDARDPACK
Adding user MigrationUser@tailspinonline.com to group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe
Adding user MigrationUser2@tailspinonline.com to group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe

STEP 2: Checking if all users still have the source license and are in conflict state for license from target group. Check iteration: 1

MigrationUser@tailspinonline.com                Expected state: NO
MigrationUser2@tailspinonline.com               Expected state: NO

Total users checked: 2. In expected state: 0. Not yet: 2
Not all users are in expected state. Waiting 60 seconds to try again.

STEP 2: Checking if all users still have the source license and are in conflict state for license from target group. Check iteration: 3

MigrationUser@tailspinonline.com                Expected state: YES
MigrationUser2@tailspinonline.com               Expected state: YES

Total users checked: 2. In expected state: 2. Not yet: 0
Check passed for all users. Exiting check loop.

STEP 3: Removing users from the source group b82c04f0-ce30-4ff1-bac7-735d92d83036. This will remove the source license TailspinOnline:STANDARDPACK and remove the conflict on target license TailspinOnline:ENTERPRISEPACK which will become assigned.
Removing user MigrationUser@tailspinonline.com from group b82c04f0-ce30-4ff1-bac7-735d92d83036
Removing user MigrationUser2@tailspinonline.com from group b82c04f0-ce30-4ff1-bac7-735d92d83036

STEP 4: Checking if all users have source license removed and target license assigned from target group. Check iteration: 1

MigrationUser@tailspinonline.com                Expected state: NO
MigrationUser2@tailspinonline.com               Expected state: NO

Total users checked: 2. In expected state: 0. Not yet: 2
Not all users are in expected state. Waiting 60 seconds to try again.

STEP 4: Checking if all users have source license removed and target license assigned from target group. Check iteration: 3

MigrationUser@tailspinonline.com                Expected state: YES
MigrationUser2@tailspinonline.com               Expected state: YES

Total users checked: 2. In expected state: 2. Not yet: 0
Check passed for all users. Exiting check loop.
```

<h2 id="powershell-automation-of-migration-and-verification-steps">移行の自動化および検証のための PowerShell コードの実行</h2>

このセクションには、この記事で説明されているスクリプトを実行するために必要な PowerShell コードが含まれています。

>[!WARNING]
>このコードは、デモンストレーション用のサンプルとして提供されています。 ご利用の環境で使用する場合は、コードをまず小規模にテストするか別のテスト テナントでテストすることを検討してください。 お使いの環境の具体的なニーズに合わせてコードの調整が必要になる場合があります。

コードを実行するには、[Azure AD PowerShell v1.0 ライブラリ](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0)の説明を使用してください。 スクリプトを実行する前に、`connect-msolservice`コマンドレットを実行してテナントにサインインします。

```powershell
# BEGIN: Helper functions that are used in the scripts.

# GetUserObject function
# Retrieve a user object based on the ObjectId or UserPrincipalName.
function GetUserObject
{
    [OutputType([Microsoft.Online.Administration.User])]
    Param([object]$userId)

    $userIdType = $userId.GetType()

    if($userIdType -eq [Guid])
    {
        return Get-MsolUser -ObjectId $userId
    }
    elseif($userIdType -eq [string])
    {
        return Get-MsolUser -UserPrincipalName $userId
    }
    else
    {
        throw "User Id type must be a Guid or a string (UserPrincipalName). The user id type was: $($userIdType.Name)"
    }
}

# GetGuidUserId function
# Get a Guid objectId for a user, even when a UserPrincipal string is passed in.
# Guid ids are needed for group membership manipulation, where UPNs cannot be used.
function GetGuidUserId
{
    [OutputType([Guid])]
    Param([object]$userId)

    [Guid]$guidId = [Guid]::Empty
    if($userId.GetType() -eq [String])
    {
        $user = GetUserObject $userId
        return $user.ObjectId
    }
    elseif($userId.GetType() -eq [Guid])
    {
        return $userId
    }
    else
    {
        throw "UserId type must be a Guid or a string (UserPrincipalName). The user id type was: $($userId.GetType().Name)"
    }
}

# AddUsersToGroup function
# Add a collection of users to a group.
# Note: This function fails if a user is already a member of the specified group.
function AddUsersToGroup
{
    Param([object[]]$userIds, [Guid]$groupId)

    foreach($userId in $userIds)
    {
        $fetchId = GetGuidUserId $userId
        Write-Host "Adding user $userId to group $groupId"
        Add-MsolGroupMember -GroupObjectId $groupId -GroupMemberType User -GroupMemberObjectId $fetchId
    }
}

# RemoveUsersFromGroup function
# Remove a collection of users from a group.
# Note: This function fails if a user is not a member of the specified group.
function RemoveUsersFromGroup
{
    Param([object[]]$userIds, [Guid]$groupId)

    foreach($userId in $userIds)
    {
        $fetchId = GetGuidUserId $userId
        Write-Host "Removing user $userId from group $groupId"
        Remove-MsolGroupMember -GroupObjectId $groupId -GroupMemberType User -GroupMemberObjectId $fetchId
    }
}

# GetUserLicense function
# Return the license object that corresponds to the skuId.
# Return NULL if no object is found.
function GetUserLicense
{
    [OutputType([Microsoft.Online.Administration.UserLicense])]
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    # Look for the specific license SKU in all of the licenses that are assigned to the user.
    foreach($license in $user.Licenses)
    {
        if ($license.AccountSkuId -ieq $skuId)
        {
            return $license
        }
    }
    return $null
}

# IsLicenseAssignedToUser function
# Check if the specific SKU license is assigned to the user,
#    regardless of how the license is assigned (directly or via GBL).
function IsLicenseAssignedToUser
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [string]$skuId)

    $license = GetUserLicense $user $skuId

    return ($license -ne $null)
}

# GetObjectIdsAssigningLicense function
function GetObjectIdsAssigningLicense
{
    [OutputType([Guid[]])]
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    $license = GetUserLicense $user $skuId

    if ($license -ne $null)
    {
        return [Guid[]]$license.GroupsAssigningLicense
    }
    return [Array]::CreateInstance([Guid],0)
}

# UserHasLicenseAssignedFromThisGroup function
# Return TRUE if the user inherits the license from the specific group.
# Note: This function returns true only if the license is successfully assigned from the group.
#       If the license is in an error state, the function return false.
function UserHasLicenseAssignedFromThisGroup
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId, [Guid]$groupId)

    [Guid[]]$objectsAssigningLicense = GetObjectIdsAssigningLicense $user $skuId

    # GroupsAssigningLicense contains a collection of object IDs for assigning the license.
    # This could be a group object or a user object (contrary to what the name suggests).
    foreach ($assignmentSource in $objectsAssigningLicense)
    {
        # If the collection contains at least one ID that doesn't match the user ID, the license is inherited from a group.
        # Note: The license might also be assigned directly, in addition to being inherited.
        if ($assignmentSource -ieq $groupId)
        {
            return $true
        }
    }
    return $false
}

# GetErrorsForLicense function
# Return error objects for a specific license.
function GetErrorsForLicense
{
    [OutputType([Microsoft.Online.Administration.IndirectLicenseError[]])]
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    [Microsoft.Online.Administration.IndirectLicenseError[]] $errorObjects = $user.IndirectLicenseErrors | Where {"$($_.AccountSku.AccountName):$($_.AccountSku.SkuPartNumber)" -ieq $skuId}

    if($errorObjects -eq $null)
    {
        #there are no errors at all
        return [Array]::CreateInstance([Microsoft.Online.Administration.IndirectLicenseError],0)
    }

    return $errorObjects
}

# GetErrorForLicenseFromGroup function
# Return an error label that's associated with a specific license that's inherited from a specific group.
# Return $null if there's no error.
function GetErrorForLicenseFromGroup
{
    [OutputType([string])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$groupId, [string]$skuId)


    # There are some errors. Check if any of the errors are associated with the group.
    foreach($licenseError in GetErrorsForLicense $user $skuId)
    {
        if($licenseError.ReferencedObjectId -eq $groupId)
        {
            return $licenseError.Error
        }
    }
    return $null
}

# IsExpectedLicenseStateForGroup function
# Check if the license is in an expected state for a given group.
# If expectedError is set to a value, the function checks if the license is in the specific error state for the group.
# If expectedError is NULL, the function checks if the license is successfully assigned from the group.
function IsExpectedLicenseStateForGroup
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$groupId, [string]$skuId, [string]$expectedError)

    # The license is expected to be fully assigned from the group and not in an error state.
    if([string]::IsNullOrEmpty($expectedError))
    {
        # Check if the assigned license is inherited from the expected group and without an error on it.
        return (UserHasLicenseAssignedFromThisGroup $user $skuId $groupId)
    }
    # The license is expected to be in the specific error state on the specific group.
    else
    {
        $error = GetErrorForLicenseFromGroup $user $groupId $skuId
        return ($error -ieq $expectedError)
    }
}

# VerifySourceLicensePresentAndTargetLicenseInConflictState function
# Detect if the licenses are in a specific state where the source license is assigned, but the target license is in a conflict state.
# Note: If the source license is gone, the function throws an exception to abort the script.
#       The conflict state can signify that something went wrong with the migration steps and the user lost access to services.
function VerifySourceLicensePresentAndTargetLicenseInConflictState
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    # Check if the user still has the source license. If not, abort the script because something is seriously wrong.
    if(-Not (UserHasLicenseAssignedFromThisGroup $user $sourceSkuId $sourceGroupId))
    {
        throw "User $($user.UserPrincipalName) ($($user.ObjectId)) does not have the expected license $sourceSkuId from source group $sourceGroupId, which may result in loss of access and data. This is unexpected and should be investigated. Aborting execution."
    }
    # Check if the target license is in conflict, as expected.
    $conflictError = 'MutuallyExclusiveViolation'
    return (IsExpectedLicenseStateForGroup $user $targetGroupId $targetSkuId $conflictError)
}

# VerifySourceLicenseRemovedAndTargetLicenseAssignedFromGroup function
# Detect if the licenses are in a specific state where the source license isn't present,
#    but the target license is correctly assigned.
# Note: If the source license is gone and the target license isn't present,
#       the function throws an exception to abort the script.
#       Something went wrong and the user may have lost access to services.
function VerifySourceLicenseRemovedAndTargetLicenseAssignedFromGroup
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    # Check if user has the source license completely removed, which is a prerequisite to the target license eventually kicking in.
    if(IsLicenseAssignedToUser $user $sourceSkuId)
    {
        return $false
    }

    # Check if the user has the target license at all. If not, abort the script because something is seriously wrong.
    if(-Not (IsLicenseAssignedToUser $user $targetSkuId))
    {
        throw "User $($user.UserPrincipalName) ($($user.ObjectId)) does not have the expected license $targetSkuId assigned, which may result in loss of access and data. This is unexpected and should be investigated. Aborting execution."
    }
    # Check if the target license is assigned from the expected target group, and no longer in an error state.
    return (IsExpectedLicenseStateForGroup $user $targetGroupId $targetSkuId $null)
}

# VerifySourceandTargetLicensePresent function
# Detect if the licenses are in the specific state where the source license is assigned and the target license is also assigned.
# Note: If the source license is gone, the function throws an exception to abort the script.
#       This state can signify that something went wrong with the migration steps and the user lost access to services.
function VerifySourceandTargetLicensePresent
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    #check user still has source license - if not, abort because something is seriously wrong
    if(-Not (UserHasLicenseAssignedFromThisGroup $user $sourceSkuId $sourceGroupId))
    {
        throw "User $($user.UserPrincipalName) ($($user.ObjectId)) does not have the expected license $sourceSkuId from source group $sourceGroupId, which may result in loss of access and data. This is unexpected and should be investigated. Aborting execution."
    }
    #check if the target license is also assigned from the target group
    return (UserHasLicenseAssignedFromThisGroup $user $targetSkuId $targetGroupId)
}

# VerifyAssumptionsForUser function
# Verify basic assumptions that should be true for a user before we execute the migration process.
# The function prints details about the verification steps.
# Return TRUE if all of the assumptions are true.
function VerifyAssumptionsForUser
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    $userName = $user.UserPrincipalName
    # 1. The user has the source license assigned from the source group.
    if(-Not (UserHasLicenseAssignedFromThisGroup $user $sourceSkuId $sourceGroupId))
    {
        Write-Host "$userName does not have source license $sourceSkuId assigned from source group $sourceGroupId."
        return $false
    }

    # 2. The user doesn't have the same source license assigned from another group at the same time,
    #    and the user doesn't have the source license assigned directly.
    [Guid[]]$otherObjectsAssigningLicense = GetObjectIdsAssigningLicense $user $sourceSkuId | Where {$_ -ne $sourceGroupId}
    foreach($otherObject in $otherObjectsAssigningLicense)
    {
        if($otherObject -eq $user.ObjectId)
        {
            Write-Host "$userName has source license assigned directly to the user."
        }
        else
        {
            Write-Host "$username has source license assigned from an additional unexpected group $otherObject."
        }
    }
    if($otherObjectsAssigningLicense -and $otherObjectsAssigningLicense.Count -gt 0)
    {
        return $false
    }

    # 3. The user doesn't have the target license assigned.
    if(IsLicenseAssignedToUser $user $targetSkuId)
    {
        Write-Host "$userName already has target license assigned."
        return $false
    }

    # 4. The user doesn't have the target license in an error state from some groups.
    [Microsoft.Online.Administration.IndirectLicenseError[]]$licenseErrors = GetErrorsForLicense $user $targetSkuId
    foreach($licenseError in $licenseErrors)
    {
        Write-Host "$userName has target license in error state $($licenseError.Error) from unexpected group $($licenseError.ReferencedObjectId)."
    }
    if($licenseErrors -and $licenseErrors.Count -gt 0)
    {
        return $false
    }

    Write-Host "$userName`t`tOK"
    return $true
}

# VerifyAssumptions function
# Check if all of the users to be migrated are in a correct state.
function VerifyAssumptions
{
    [OutputType([bool])]
    Param([object[]]$userIds, [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    Write-Host "Verifying initial assumptions:"

    # Check if there are enough target licenses for all of the users.
    $skuState = Get-MsolAccountSku | Where {$_.AccountSkuId -ieq $targetSkuId}

    if($skuState -eq $null)
    {
        Write-Host "Target license SKU $targetSkuId does not exist in the tenant at all."
        return $false
    }

    $availableLicenses = $skuState.ActiveUnits - $skuState.ConsumedUnits

    if($userIds.Count -gt $availableLicenses)
    {
        Write-Host "Not enough licenses for all users. User count: $($userIds.Count), licenses available: $availableLicenses"
        return $false
    }
    else
    {
        Write-Host "Enough $targetSkuId licenses available ($availableLicenses) for users: $($userIds.Count)."
    }

    # Check if each user to be migrated is in an expected state.
    $usersOK = 0
    $usersNotOK = 0
    foreach($userId in $userIds)
    {
        $user = GetUserObject $userId
        if(VerifyAssumptionsForUser $user $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId)
        {
            ++$usersOK
        }
        else
        {
            ++$usersNotOK
        }
    }
    if($usersNotOK -gt 0)
    {
        Write-Host "Checks passed for $usersOK users, but failed for $usersNotOK users."
        return $false
    }
    Write-Host "Checks passed for all $usersOK users."
    return $true
}

# ExecuteVerificationLoop function
# Execute a verification function (passed in as a delegate by using $checkFunction) for each user.
# The function tracks how many users passed/failed verification.
# The function repeats the verification loop until all of the users have passed the check.
#   The loop may never terminate if some users never reach the expected state.
#   If the loop doesn't terminate, you should investigate to determine the cause.
# Note: If the verification function fails with an exception,
#       such as the function detects an unexpected user state,
#       the loop terminates and investigation into the user state is needed.
function ExecuteVerificationLoop
{
    Param([System.Management.Automation.ScriptBlock]$checkFunction, [string]$consoleMessage, [object[]]$userIds,  [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    # How long to wait until the loop is retried.
    $sleepIntervalSeconds = 60
    $retryIteration = 1

    While($true)
    {
        Write-Host "`n$consoleMessage. Check iteration: $retryIteration`n"

        $usersInExpectedState = 0
        $usersNotYet = 0

        foreach ($userId in $userIds)
        {
            $user = GetUserObject $userId
            if($checkFunction.InvokeReturnAsIs($user, $sourceGroupId, $sourceSkuId, $targetGroupId, $targetSkuId))
            {
                Write-Host "$userId`t`tExpected state: YES"
                ++$usersInExpectedState
            }
            else
            {
                Write-Host "$userId`t`tExpected state: NO"
                ++$usersNotYet
            }
        }

        Write-Host "`nTotal users checked: $($userIds.Count). In expected state: $usersInExpectedState. Not yet: $usersNotYet"

        if($usersNotYet -eq 0)
        {
            Write-Host "Check passed for all users. Exiting check loop."
            return
        }

        ++$retryIteration
        Write-Host "Not all users are in expected state. Waiting $sleepIntervalSeconds seconds to try again."
        Start-Sleep -Seconds $sleepIntervalSeconds
    }
}
# END: Helper functions that are used in the script.

# BEGIN: Execute the script.

# Enable strict execution mode.
Set-StrictMode -Version latest
# Stop the script when the first exception is thrown.
$ErrorActionPreference = "Stop"

# A batch of users that we want to migrate in this iteration.
# The batch can be specified as an array of User Principal Names (string) or ObjectIds (Guid).
# Note: The batch can be loaded from a text file that represents a larger batch of users that we want to migrate.
[string[]]$usersToMigrate = 'MigrationUser@tailspinonline.com', 'MigrationUser2@tailspinonline.com'

############### CONFLICTING LICENSES SCENARIO ################

# The source group and source license to remove the user from.
$sourceGroupId = [Guid]'b82c04f0-ce30-4ff1-bac7-735d92d83036'
$sourceSkuId = 'TailspinOnline:STANDARDPACK'             # <-- This is the Office 365 Enterprise E1 product.

# The target group and target license to assign to the user.
$targetGroupId = [Guid]'bcf279d1-5ad5-46a5-b469-4b8a552aa2fe'
$targetSkuId = 'TailspinOnline:ENTERPRISEPACK'           # <-- This is the Office 365 Enterprise E3 product.

# Assumptions before migration:
# 1. Users are already in the source group and they have the source license assigned from that group.
# 2. Users don't have the same source license assigned from another group at the same time,
#    and they don't have the source license assigned directly.
#    IMPORTANT: If Assumption 2 isn't true, removing users from the source group in STEP 3
#               won't result in the target license being correctly applied.
# 3. There are enough available licenses to assign a target license to all of the users that are being migrated.
if(-Not (VerifyAssumptions $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId))
{
    throw "Some users did not pass validation checks. See the output for details. Aborting migration process."
}

Write-Host "STEP 1: Adding users to the target group $targetGroupId. This will put target license $targetSkuId in conflict state with the source license $sourceSkuId"
AddUsersToGroup $usersToMigrate $targetGroupId

# Verify that the target license shows up in the conflict state for each user on the list.
# This step runs in a loop, forever, until all of the users are in the expected state.
# Group-based licensing (GBL) can take some time to reflect the changes on users.
# As a result, the loop should terminate after a period of time that's dependent on the size of the user collection.
# Note: If the loop hasn't terminated after a long period of time, stop the script.
#       Inspect the users that are reported as not yet in the expected state.
#       Verify that the users are not blocked for some other reason.
ExecuteVerificationLoop ${function:VerifySourceLicensePresentAndTargetLicenseInConflictState} 'STEP 2: Checking if all users still have the source license and are in conflict state for license from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId

# Now it's safe to remove the users from the source group.
Write-Host "STEP 3: Removing users from the source group $sourceGroupId. This will remove the source license $sourceSkuId and remove the conflict on target license $targetSkuId which will become assigned."
RemoveUsersFromGroup $usersToMigrate $sourceGroupId

# Verify that target license is now active on each user and the source license is removed.
ExecuteVerificationLoop ${function:VerifySourceLicenseRemovedAndTargetLicenseAssignedFromGroup} 'STEP 4: Checking if all users have source license removed and target license assigned from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId

# END: Execute the script.
```

## <a name="next-steps"></a>次の手順

グループによるライセンス管理の他のシナリオについては、以下の記事を参照してください。

* [Assigning licenses to a group in Azure Active Directory](../users-groups-roles/licensing-groups-assign.md) (Azure Active Directory でのグループへのライセンス割り当て)
* [Azure Active Directory のグループのライセンスに関する問題の特定と解決](../users-groups-roles/licensing-groups-resolve-problems.md)
* [Azure Active Directory で個別にライセンスを付与されたユーザーをグループベースのライセンスに移行する方法](../users-groups-roles/licensing-groups-migrate-users.md)
* [Azure Active Directory グループベース ライセンスのその他のシナリオ](../users-groups-roles/licensing-group-advanced.md)
* [Azure Active Directory のグループベースのライセンスの PowerShell の例](../users-groups-roles/licensing-ps-examples.md)
