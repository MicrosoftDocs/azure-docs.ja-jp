---

title: "Azure Active Directory のグループベースのライセンスが使用されるその他のシナリオ | Microsoft Docs"
description: "Azure Active Directory のグループベースのライセンスが使用されるその他のシナリオ"
services: active-directory
keywords: "Azure AD のライセンス"
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: a7240c52e9351a60e3cf577d8112862c7dc8d913
ms.lasthandoff: 03/14/2017


---

# <a name="scenarios-limitations-and-known-issues-with-using-groups-to-manage-licensing-in-azure-active-directory"></a>Azure Active Directory のライセンス管理にグループを使用する際のシナリオ、制限、および既知の問題

次の情報と例を使用して Azure Active Directory (Azure AD) のグループベースのライセンスについて詳しく理解できます。

## <a name="use-group-based-licensing-with-dynamic-groups"></a>グループベースのライセンスを動的グループとともに使用する

グループベースのライセンスは任意のセキュリティ グループとともに使用できます。つまり、Azure AD の動的グループと組み合わせることができます。 動的グループでは、ユーザー オブジェクト属性に対して規則が実行され、グループのユーザーが自動的に追加、削除されます。

たとえば、複数の動的グループ (ユーザーに割り当てる製品のセットごとに&1; つ) を作成できます。 各グループには、ユーザーが受け取る必要のあるライセンスのセットを示す特定の属性を参照する規則が含まれます。 属性をオンプレミスで割り当てて Azure AD と同期するか、属性をクラウドで直接管理できます。

属性を指定すると、ユーザーがこれらの動的ライセンス グループの&1; つ以上に追加されます。 その後まもなく、ユーザーにライセンスが割り当てられます。 属性が削除されると、ユーザーはグループを離れ、ライセンスが削除されます。

### <a name="example"></a>例

どのユーザーがマイクロソフトのどの Web サービスにアクセスできる必要があるかを決定するオンプレミスの ID管理ソリューションがあるとします。 **extensionAttribute1** を使用して、ユーザーが必要とするライセンスを表す文字列値を格納します。 Azure AD Connect がそれを Azure AD と同期します。

この例では、Office 365 Enterprise E5 および Enterprise Mobility + Security のライセンスをユーザーに配布することが目標です。 Azure AD では、製品ごとに&1; つずつ、2 つの動的なグループを作成します。 これは、一部のユーザーは一方のみを必要として、もう一方は不要である可能性があるためです。 その後、各グループに対して動的メンバーシップの規則とライセンス設定を指定します。 これは次にようになります。

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Enterprise E5: 基本サービス

![Office 365 Enterprise E5 の基本サービスのスクリーンショット](media/active-directory-licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security: ライセンスを持つユーザー

![Enterprise Mobility + Security のライセンスを持つユーザーのスクリーンショット](media/active-directory-licensing-group-advanced/o365-e5-licensed-users.png)

この例では、1 人のユーザーを変更し、extensionAttribute1 の値を `EMS;E5_baseservices;` に設定します。 このユーザーに両方のライセンスを付与するためです。 この変更はオンプレミスで実行できます。 変更がクラウドと同期されると、ユーザーは両方のグループに自動的に追加され、ライセンスが割り当てられます。

![ユーザーの extensionAttribute1 を設定する方法を示すスクリーン ショット](media/active-directory-licensing-group-advanced/user-set-extensionAttribute1.png)

### <a name="modify-a-dynamic-group-membership-rule"></a>動的グループのメンバーシップ規則の変更

既存のグループのメンバーシップ規則を変更するときには注意が必要です。 ルールを変更すると、すべてのユーザーがグループから削除されます。 ルールが評価され、その後ユーザーが新しい条件でグループに追加されます。

グループにライセンスが割り当てられている場合は、プロセス中にすべてのユーザーからそれらのライセンスが削除されます。 新しいライセンスは、新しい規則が評価され、ユーザーがもう一度追加された後にのみ適用されます。 これは、ユーザーに対してサービスの損失、または場合によってはデータの損失が発生する可能性があることを意味します。

ライセンスの割り当てに使用されるグループのメンバーシップ規則は変更しないことをお勧めします。 代わりに、新しい規則で新しいグループを作成し、元のグループと同じライセンス設定を指定します。 メンバーが追加され、すべてのユーザーにライセンスが割り当てられるまで待ちます。 元のグループを削除できるのはその後だけです。 この方法により、ユーザーのアクセス権やデータを失わずに、新しいメンバーシップ規則に安全かつ段階的に切り替えることができます。


## <a name="multiple-groups-and-multiple-licenses"></a>複数のグループと複数のライセンス

ユーザーは、ライセンスを持つ複数のグループのメンバーになることができます。 考慮事項をいくつか以下に示します。

- 同じ製品の複数のライセンスが重複することがあり、その結果、有効になっているすべてのサービスがユーザーに適用されることがあります。 次の例は、2 つのライセンス グループを示します。"*E3 base services (E3 基本サービス)*" にはすべてのユーザーに先にデプロイする基本サービスが含まれています。 "*E3 extended services (E3 拡張サービス)*" には一部のユーザーにのみデプロイするその他のサービス (Sway と Planner) が含まれています。 Yammer は将来のデプロイのため無効のままになっています。 この例では、ユーザーは両方のグループに追加されました。

  ![有効になっているサービスのスクリーンショット](media/active-directory-licensing-group-advanced/view-enabled-services.png)

  その結果、この製品のライセンスを 1 つのみ使用して、製品の 12 のサービスのうち 7 つが有効になっています。

- "*E3*" ライセンスを選ぶと、どのグループが原因でどのサービスがユーザーに対して有効になったかという情報などの詳細が表示されます。

  ![グループ別に有効になっているサービスのスクリーンショット](media/active-directory-licensing-group-advanced/view-enabled-service-by-group.png)

## <a name="direct-licenses-coexist-with-group-licenses"></a>グループ ライセンスと共存する直接ライセンス

ユーザーがグループからライセンスを継承する場合、ユーザー オブジェクトに対する直接ライセンス割り当てを削除または変更することはできません。 代わりに、グループで変更を行い、システムによってすべてのユーザーに反映する必要があります。

ただし、継承されたライセンスに加えて、ユーザーに対して同じ製品のライセンスを直接割り当てることができます。 たとえば、これを使用して、他のユーザーに影響を与えずに、1 人のユーザーに対してのみ製品の追加サービスを有効にすることができます。

直接割り当てられたライセンスは削除できますが、継承されたライセンスには影響しません。 たとえば、グループから Office 365 Enterprise E3 ライセンスを継承する次のユーザーについて考えてみましょう。

1. 最初に、ユーザーは "*E3 basic services (E3 基本サービス)*" グループからのみライセンスを継承します。 これにより、次の図に示すように 4 つのサービス プランが有効になります。

  ![E3 グループで有効になっているサービスのスクリーンショット](media/active-directory-licensing-group-advanced/e3-group-enabled-services.png)

2. **[割り当て]** をクリックすることで、E3 ライセンスをユーザーに直接割り当てることができます。 このケースでは、Yammer Enterprise 以外のすべてのサービス プランを無効にします。

  ![ユーザーにライセンスを直接割り当てる方法を示すスクリーンショット](media/active-directory-licensing-group-advanced/assign-license-to-user.png)

3. その結果、ユーザーは引き続き E3 製品のライセンスを&1; つだけ使用します。 ただし、直接割り当てにより Yammer Enterprise のサービスはそのユーザーに対してのみ有効になります。 次の図は、グループ メンバーシップと直接割り当てによって有効になっているサービスを示します。

  ![継承と直接割り当てのスクリーンショット](media/active-directory-licensing-group-advanced/direct-vs-inherited-assignment.png)

4. 直接割り当てを使用すると、次の操作を行うことができます。

  a. Yammer Enterprise はユーザー オブジェクトに対して直接無効にできます。 その他のサービスと異なり、このサービスに対して **[オン]/[オフ]** の切り替えが有効になっています。 これは、このサービスがそのユーザーに対して直接有効になっており、変更できるためです。

  b. 直接割り当てられたライセンスの一部として、追加サービスも有効にすることができます。

  c. **[削除]** ボタンを使用して、ユーザーから直接ライセンスを削除できます。 ユーザーは継承されたグループ ライセンスのみ持つようになり、元のサービスだけが有効なままであることを確認できます。

    ![直接割り当てを削除する方法を示すスクリーンショット](media/active-directory-licensing-group-advanced/remove-direct-license.png)

## <a name="usage-location"></a>利用場所

一部の Microsoft サービスは、すべての場所で利用できないことがあります。 ライセンスをユーザーに割り当てる前に、管理者はユーザーの **[利用場所]** プロパティを指定しておく必要があります。 これは、[Azure Portal](https://portal.azure.com) で **[ユーザー]**、**[プロファイル]**、**[設定]** セクションの順に移動して実行できます。

グループ ライセンスの割り当ての際、利用場所が指定されていないユーザーは、ディレクトリの場所を継承します。 異なる場所にユーザーがいる場合、そのことをユーザー オブジェクトに正しく反映させてから、ライセンスが付与されたグループにユーザーを追加するようにしてください。

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>PowerShell を使用して、継承したライセンスと直接ライセンスを持つユーザーを表示する

Azure AD リリースのプレビュー期間中は、PowerShell を使用してグループのライセンス割り当てを完全に制御することはできません。 ただし、ユーザーの状態に関する基本情報の検出や、ライセンスがグループから継承されたか直接割り当てられたかの判定には使用できます。 次のコード サンプルは、管理者がライセンス割り当てについての基本的なレポートを生成する方法を示しています。

1. `connect-msolservice` コマンドレットを実行してテナントを認証し、接続します。

2. `Get-MsolAccountSku` を使用して、テナントにプロビジョニングされているすべての製品ライセンスを検出します。

  ![Get-Msolaccountsku コマンドレットのスクリーンショット](media/active-directory-licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. この例では、Enterprise Mobility + Security のライセンスが直接、グループから、またはその両方で割り当てられているユーザーを調べます。 次の PowerShell スクリプトを使用できます。
  
  ```
  \# Returns TRUE if the user has the license assigned directly
  function UserHasLicenseAssignedDirectly
  {
      Param([Microsoft.Online.Administration.User]$user, [string]$skuId)
      foreach($license in $user.Licenses)
      {
          \# we look for the specific license SKU in all licenses assigned to the user
          if ($license.AccountSkuId -ieq $skuId)
          {
              \# GroupsAssigningLicense contains a collection of IDs of objects assigning the license
              \# This could be a group object or a user object (contrary to what the name suggests)
              \# If the collection is empty, this means the license is assigned directly. This is the case for users who have never been licensed via groups in the past
              if ($license.GroupsAssigningLicense.Count -eq 0)
              {
                  return $true
              }
              \# If the collection contains the ID of the user object, this means the license is assigned directly
              \# Note: the license may also be assigned through one or more groups in addition to being assigned directly
              foreach ($assignmentSource in $license.GroupsAssigningLicense)
              {
                  if ($assignmentSource -ieq $user.ObjectId)
                  {
                      return $true
                  }
              }
              return $false
          }
      }
      return $false
  }
  \# Returns TRUE if the user is inheriting the license from a group
  function UserHasLicenseAssignedFromGroup
  {
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)
     foreach($license in $user.Licenses
     {
        \# we look for the specific license SKU in all licenses assigned to the user
        if ($license.AccountSkuId -ieq $skuId)
        {
          \# GroupsAssigningLicense contains a collection of IDs of objects assigning the license
          \# This could be a group object or a user object (contrary to what the name suggests)
            foreach ($assignmentSource in $license.GroupsAssigningLicense)
          {
                  \# If the collection contains at least one ID not matching the user ID this means that the license is inherited from a group.
                  \# Note: the license may also be assigned directly in addition to being inherited
                  if ($assignmentSource -ine $user.ObjectId)

            {
                      return $true
            }
          }
              return $false
        }
      }
      return $false
  }
  ```

4. スクリプトの残りの部分ではすべてのユーザーを取得し、各ユーザーにこれらの関数を実行します。 その後、テーブルに出力をフォーマットします。
    
  ```
  \# the license SKU we are interested in
  $skuId = "reseller-account:EMS"
  \# find all users that have the SKU license assigned
  Get-MsolUser -All | where {$_.isLicensed -eq $true -and $_.Licenses.AccountSKUID -eq $skuId} | select `
      ObjectId, `
      @{Name="SkuId";Expression={$skuId}}, `
      @{Name="AssignedDirectly";Expression={(UserHasLicenseAssignedDirectly $_ $skuId)}}, `
      @{Name="AssignedFromGroup";Expression={(UserHasLicenseAssignedFromGroup $_ $skuId)}}
  ```

5. 次のように、完全なスクリプトの出力が表示されます。

  ![PowerShell スクリプトの出力のスクリーンショット](media/active-directory-licensing-group-advanced/powershell-script-output.png)

## <a name="limitations-and-known-issues"></a>制限事項と既知の問題

グループベースのライセンスを使用する場合、次の制限事項と既知の問題の一覧について理解することをお勧めします。

- グループベースのライセンスでは、「入れ子になったグループ」(他のグループを含むグループ) を現在サポートしていません。 入れ子になったグループにライセンスを適用した場合は、グループの第&1; レベルのユーザー メンバーだけにライセンスが適用されます。

- グループのメンバーシップから継承したため、1 つ以上のライセンスを変更できませんでした。 グループベースのライセンスを表示または変更するには、Azure 管理ポータルにアクセスしてください。

- 現在、[Office 365 管理ポータル](https://portal.office.com )では、グループベースのライセンスはサポートされていません。 ユーザーがライセンスをグループから継承する場合、このライセンスは Office 管理ポータルに通常のユーザー ライセンスとして表示されます。 そのライセンスを変更しようとした (たとえば、ライセンスのサービスを無効にしようとした) 場合、ポータルはエラー メッセージを返します。 継承されたグループのライセンスをユーザーで直接変更することはできません。

- ユーザーがグループから削除され、ライセンスを失うと、そのライセンスのサービス プラン (Exchange Online や SharePoint Online など) は、「中断」状態に設定されます。 サービス プランは最終的な無効状態には設定されていません。 これは、管理者がグループ メンバーシップの管理で間違いを犯した場合にユーザー データが誤って削除されるのを回避するための予防措置として行われます。

- 非常に大きなグループ (例: ユーザーが 100,000 人) に対してライセンスを割り当てるまたはライセンスを変更すると、パフォーマンスに影響を及ぼすことがあります。 特に、Azure AD のオートメーションによって生成される変更の量によっては、Azure AD とオンプレミスのシステム間のディレクトリの同期のパフォーマンスに悪影響を及ぼす可能性があります。 これは、環境内のディレクトリ同期の遅延の原因となることがあります。

- ライセンス管理のオートメーションは、環境内のすべての種類の変更に自動的には反応しません。 たとえば、ライセンスの不足により一部のユーザーがエラー状態になる場合があります。 使用可能なシート数を解放するために、他のユーザーから直接割り当てられた一部のライセンスを削除できます。 ただし、システムがこの変更に自動的に反応してそのエラー状態のユーザーを修正することはありません。

  この種の制限の回避策として、Azure AD の 使用可能なシート数を解放するために、**[グループ]** ブレードに移動し、**[再処理]** ボタンをクリックします。 これにより、そのグループ内のすべてのユーザーが処理され、可能であればエラー状態が解決されます。

## <a name="next-steps"></a>次のステップ

グループベースのライセンスを通じたライセンス管理の他のシナリオについては、以下をご覧ください

* [What is group-based licensing in Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md) (Azure Active Directory のグループベースのライセンスとは)
* [Assigning licenses to a group in Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md) (Azure Active Directory でのグループへのライセンス割り当て)
* [Azure Active Directory のグループのライセンスに関する問題の特定と解決](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [How to migrate individual licensed users to group-based licensing in Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md) (Azure Active Directory で個別にライセンスを付与されたユーザーをグループベースのライセンスに移行する方法)

