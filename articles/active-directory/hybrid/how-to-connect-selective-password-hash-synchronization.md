---
title: Azure AD Connect の選択的なパスワード ハッシュ同期
description: この記事では、Azure AD Connect で使用するために、選択的なパスワード ハッシュ 同期をセットアップして構成する方法について説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/16/2021
ms.subservice: hybrid
ms.author: billmath
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 774c78cbb09d2e5e60dfc0cafc0082b25e9b1b45
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103602892"
---
# <a name="selective-password-hash-synchronization-configuration-for-azure-ad-connect"></a>Azure AD Connect の選択的なパスワード ハッシュ同期の構成

[パスワード ハッシュ同期](whatis-phs.md)は、ハイブリッド ID を実現するために使用されるサインイン方法の 1 つです。 Azure AD Connect では、オンプレミスの Active Directory インスタンスからクラウドベースの Azure AD インスタンスに、ユーザーのパスワードのハッシュを同期します。  既定では、セットアップが完了すると、同期しているすべてのユーザーに対してパスワード ハッシュ同期が実行されます。

一部のユーザーを Azure AD へのパスワード ハッシュ同期から除外する場合、この記事で説明されている手順に従って、選択的なパスワード ハッシュ同期を構成することができます。

>[!Important]
> 公式に文書化されている構成やアクションを除き、Microsoft は Azure AD Connect Sync の変更や操作をサポートしません。 このような構成やアクションを行うと、Azure AD Connect Sync が不整合な状態になったり、サポートされていない状態になったりする可能性があります。結果的に、Microsoft ではこのようなデプロイについて効果的なテクニカル サポートを提供することを保証できなくなります。 


## <a name="consider-your-implementation"></a>実装について検討する  
構成管理作業を軽減するために、まずパスワード ハッシュ同期から除外するユーザー オブジェクトの数を考慮する必要があります。 以下のシナリオのうち、相互に排他的なものを確認し、要件に合わせて適切な構成オプションを選択してください。
- **除外** するユーザーの数が、**含める** ユーザー数より **小さい** 場合は、[こちらのセクション](#excluded-users-is-smaller-than-included-users)の手順に従います。
- **除外** するユーザーの数が、**含める** ユーザー数より **大きい** 場合は、[こちらのセクション](#excluded-users-is-larger-than-included-users)の手順に従います。

> [!Important]
> いずれの構成オプションを選択しても、変更を適用するために必要な初期同期 (完全同期) が、次の同期サイクルで自動的に実行されます。

### <a name="the-admindescription-attribute"></a>AdminDescription 属性
どちらのシナリオも、ユーザーの adminDescription 属性を特定の値に設定することに依存しています。  これにより、規則が適用され、選択的な PHS が機能するようになります。

|シナリオ|adminDescription 値|
|-----|-----|
|除外するユーザーが含めるユーザーよりも少ない|PHSFiltered|
|除外するユーザーが含めるユーザーよりも多い|PHSIncluded|

この属性は、以下のいずれかの方法で、手動で設定できます。

- Active Directory ユーザーとコンピューター UI を使用
- `Set-ADUser` PowerShell コマンドレットの使用  詳細については、「[Set-ADUser](https://docs.microsoft.com/powershell/module/addsadministration/set-aduser)」を参照してください。

 


### <a name="disable-the-synchronization-scheduler"></a>同期スケジューラを無効にする
どちらのシナリオを開始する場合も、同期規則を変更している間、同期スケジューラを無効にしておく必要があります。
 1. Windows PowerShell を起動します。

     ```Set-ADSyncScheduler -SyncCycleEnabled $false``` 
 
2.  次のコマンドレットを実行して、スケジューラが無効になっていることを確認します。
     
    ```Get-ADSyncScheduler```

スケジューラについて詳しくは、[Azure AD Connect スケジューラ](how-to-connect-sync-feature-scheduler.md)に関する記事を参照してください。




## <a name="excluded-users-is-smaller-than-included-users"></a>除外するユーザーが含めるユーザーよりも少ない
次のセクションでは、**除外** するユーザーの数が、**含める** ユーザー数より **小さい** 場合に、選択的なパスワード ハッシュ同期を有効にする方法について説明します。

>[!Important]
> 既に説明したように、続行する前に、同期スケジューラが無効になっていることを確認してください。

- **In from AD – User AccountEnabled** の編集可能なコピーを作成し、**パスワード ハッシュ同期を未選択にできるようにする** オプションを選択します。そして、スコープ フィルターを定義します。 
- 既定の **In from AD – User AccountEnabled** の編集可能なコピーをもう 1 つ作成し、**パスワード ハッシュ同期を選択できるようにする** オプションを選択します。そして、スコープ フィルターを定義します。 
- 同期スケジューラを再度有効にする 
- パスワード ハッシュ同期で許可するユーザーに対してスコープ属性として定義されていた Active Directory の属性値を設定します。 

>[!Important]
>選択的なパスワード ハッシュ同期を構成するために指定された手順は、Active Directory で設定された属性 **adminDescription** (値は **phsfiltered**) を持つユーザー オブジェクトにのみ適用されます。
この属性が設定されていない場合、または値が **Phsfiltered** 以外の場合、これらの規則はユーザー オブジェクトに適用されません。


### <a name="configure-the-necessary-synchronization-rules"></a>必要な同期規則を構成します。

 1. 同期規則エディターを起動し、フィルター **[パスワード同期]** を **[オン]** にし、 **[規則の種類]** を **[標準]** に設定します。
     ![同期規則エディターを起動する](media/how-to-connect-selective-password-hash-synchronization/exclude-1.png)
 2. 選択的なパスワード ハッシュ同期を構成する Active Directory フォレスト コネクタで、規則 **[In from AD – User AccountEnabled]** を選択し、 **[編集]** をクリックします。 次のダイアログ ボックスで **[はい]** を選択して、元の規則の編集可能なコピーを作成します。
     ![規則の選択](media/how-to-connect-selective-password-hash-synchronization/exclude-2.png)
 3. 最初の規則で、パスワード ハッシュ同期を無効にします。新しいカスタム規則に、「**In from AD - User AccountEnabled - Filter Users from PHS**」という名前を指定します。
 優先順位の値を 100 未満の数値に変更します (たとえば、**90** または使用している環境で使用可能な最小値)。
 **[パスワード同期を有効にする]** チェックボックスと **[無効]** チェックボックスがオフになっていることを確認します。
 **[次へ]** をクリックします。
  ![受信の編集](media/how-to-connect-selective-password-hash-synchronization/exclude-3.png)
 4. **[スコープ フィルター]** で **[句の追加]** をクリックします。
 [属性] 列で **[adminDescription]** 、[演算子] 列で **[EQUAL]** を選択し、値として「**PHSFiltered**」と入力します。
     ![スコープ フィルター](media/how-to-connect-selective-password-hash-synchronization/exclude-4.png)
 5. 他の変更は必要ありません。 **[結合規則]** および **[変換]** は、既定のコピーされた設定のままにし、ここで **[保存]** をクリックできます。
 コネクタの次の同期サイクルで完全同期が実行されることを通知する警告ダイアログ ボックスで **[OK]** をクリックします。
     ![規則の保存](media/how-to-connect-selective-password-hash-synchronization/exclude-5.png)
 6. 次に、パスワード ハッシュ同期を有効にする別のカスタム規則を作成します。 選択的なパスワード ハッシュ同期を構成する Active Directory フォレスト コネクタで、既定の規則 **[In from AD – User AccountEnabled]** をもう一度選択し、 **[編集]** をクリックします。 次のダイアログ ボックスで **[はい]** を選択して、元の規則の編集可能なコピーを作成します。
     ![カスタム規則](media/how-to-connect-selective-password-hash-synchronization/exclude-6.png)
 7. 新しいカスタム規則に、「**In from AD - User AccountEnabled - Users included for PHS**」という名前を指定します。
 優先順位の値を、前に作成した規則よりも低い数値に変更します (この例では **89** になります)。
 **[パスワード同期を有効にする]** チェックボックスがオンで、 **[無効]** チェックボックスがオフになっていることを確認します。
 **[次へ]** をクリックします。  
     ![新しい規則の編集](media/how-to-connect-selective-password-hash-synchronization/exclude-7.png)
 8. **[スコープ フィルター]** で **[句の追加]** をクリックします。
 [属性] 列で **[adminDescription]** 、[演算子] 列で **[NOTEQUAL]** を選択し、値として「**PHSFiltered**」と入力します。
     ![スコープ規則](media/how-to-connect-selective-password-hash-synchronization/exclude-8.png)
 9. 他の変更は必要ありません。 **[結合規則]** および **[変換]** は、既定のコピーされた設定のままにし、ここで **[保存]** をクリックできます。
 コネクタの次の同期サイクルで完全同期が実行されることを通知する警告ダイアログ ボックスで **[OK]** をクリックします。
     ![結合規則](media/how-to-connect-selective-password-hash-synchronization/exclude-9.png)
 10. 規則の作成を確認します。 **[パスワード同期]** が **オン** で、 **[規則の種類]** が **[標準]** のフィルターを削除します。 先ほど作成した新しい規則が両方とも表示されます。
     ![規則の確認](media/how-to-connect-selective-password-hash-synchronization/exclude-10.png) 


### <a name="re-enable-synchronization-scheduler"></a>同期スケジューラを再度有効にする:  
必要な同期規則を構成する手順を完了したら、次の手順で同期スケジューラを再度有効にします。
 1. Windows PowerShell で次を実行します。

     ```Set-ADSyncScheduler -SyncCycleEnabled $true```
 2. 次を実行して、正常に有効にされたことを確認します。

     ```Get-ADSyncScheduler```

スケジューラについて詳しくは、[Azure AD Connect スケジューラ](how-to-connect-sync-feature-scheduler.md)に関する記事を参照してください。

### <a name="edit-users-admindescription-attribute"></a>ユーザーの **adminDescription** 属性の編集:
すべての構成が完了したら、Active Directory でパスワード ハッシュ同期から **除外** するすべてのユーザーの属性 **adminDescription** を編集し、スコープ フィルターで使用される文字列「**PHSFiltered**」を追加する必要があります。
   
  ![属性を編集する](media/how-to-connect-selective-password-hash-synchronization/exclude-11.png)


## <a name="excluded-users-is-larger-than-included-users"></a>除外するユーザーが含めるユーザーよりも多い
次のセクションでは、**除外** するユーザーの数が、**含める** ユーザー数より **大きい** 場合に、選択的なパスワード ハッシュ同期を有効にする方法について説明します。

>[!Important]
> 既に説明したように、続行する前に、同期スケジューラが無効になっていることを確認してください。

次に、以下の手順で実行されるアクションの概要を示します。

- **In from AD – User AccountEnabled** の編集可能なコピーを作成し、**パスワード ハッシュ同期を未選択にできるようにする** オプションを選択します。そして、スコープ フィルターを定義します。 
- 既定の **In from AD – User AccountEnabled** の編集可能なコピーをもう 1 つ作成し、**パスワード ハッシュ同期を選択できるようにする** オプションを選択します。そして、スコープ フィルターを定義します。 
- 同期スケジューラを再度有効にする 
- パスワード ハッシュ同期で許可するユーザーに対してスコープ属性として定義されていた Active Directory の属性値を設定します。 

>[!Important]
>選択的なパスワード ハッシュ同期を構成するために指定された手順は、Active Directory で設定された属性 **adminDescription** (値は **PHSIncluded**) を持つユーザー オブジェクトにのみ適用されます。
この属性が設定されていない場合、または値が **PHSIncluded** 以外の場合、これらの規則はユーザー オブジェクトに適用されません。


### <a name="configure-the-necessary-synchronization-rules"></a>必要な同期規則を構成します。

 1. 同期規則エディターを起動し、フィルター **[パスワード同期]** を **[オン]** にし、 **[規則の種類]** を **[標準]** に設定します。
     ![規則の種類](media/how-to-connect-selective-password-hash-synchronization/include-1.png)
 2. 選択的なパスワード ハッシュ同期を構成する Active Directory フォレスト コネクタで、規則 **[In from AD – User AccountEnabled]** をもう一度選択し、 **[編集]** をクリックします。 次のダイアログ ボックスで **[はい]** を選択して、元の規則の編集可能なコピーを作成します。
     ![In from AD](media/how-to-connect-selective-password-hash-synchronization/include-2.png)
 3. 最初の規則で、パスワード ハッシュ同期を無効にします。新しいカスタム規則に、「**In from AD - User AccountEnabled - Filter Users from PHS**」という名前を指定します。
 優先順位の値を 100 未満の数値に変更します (たとえば、**90** または使用している環境で使用可能な最小値)。
 **[パスワード同期を有効にする]** チェックボックスと **[無効]** チェックボックスがオフになっていることを確認します。
 **[次へ]** をクリックします。
  ![優先順位の設定](media/how-to-connect-selective-password-hash-synchronization/include-3.png)
 4. **[スコープ フィルター]** で **[句の追加]** をクリックします。
[属性] 列で **[adminDescription]** 、[演算子] 列で **[NOTEQUAL]** を選択し、値として「**PHSIncluded**」と入力します。
     ![句の追加](media/how-to-connect-selective-password-hash-synchronization/include-4.png)
 5. 他の変更は必要ありません。 **[結合規則]** および **[変換]** は、既定のコピーされた設定のままにし、ここで **[保存]** をクリックできます。
 コネクタの次の同期サイクルで完全同期が実行されることを通知する警告ダイアログ ボックスで **[OK]** をクリックします。
     ![変換](media/how-to-connect-selective-password-hash-synchronization/include-5.png)
 6. 次に、パスワード ハッシュ同期を有効にする別のカスタム規則を作成します。 選択的なパスワード ハッシュ同期を構成する Active Directory フォレスト コネクタで、既定の規則 **[In from AD – User AccountEnabled]** をもう一度選択し、 **[編集]** をクリックします。 次のダイアログ ボックスで **[はい]** を選択して、元の規則の編集可能なコピーを作成します。
     ![User AccountEnabled](media/how-to-connect-selective-password-hash-synchronization/include-6.png)
 7. 新しいカスタム規則に、「**In from AD - User AccountEnabled - Users included for PHS**」という名前を指定します。
 優先順位の値を、前に作成した規則よりも低い数値に変更します (この例では **89** になります)。
 **[パスワード同期を有効にする]** チェックボックスがオンで、 **[無効]** チェックボックスがオフになっていることを確認します。
 **[次へ]** をクリックします。
     ![パスワード同期を有効にする](media/how-to-connect-selective-password-hash-synchronization/include-7.png)
 8. **[スコープ フィルター]** で **[句の追加]** をクリックします。
 [属性] 列で **[adminDescription]** 、[演算子] 列で **[EQUAL]** を選択し、値として「**PHSIncluded**」と入力します。
     ![PHSIncluded](media/how-to-connect-selective-password-hash-synchronization/include-8.png)
 9. 他の変更は必要ありません。 **[結合規則]** および **[変換]** は、既定のコピーされた設定のままにし、ここで **[保存]** をクリックできます。
 コネクタの次の同期サイクルで完全同期が実行されることを通知する警告ダイアログ ボックスで **[OK]** をクリックします。
     ![今すぐ保存](media/how-to-connect-selective-password-hash-synchronization/include-9.png)
 10.    規則の作成を確認します。 **[パスワード同期]** が **オン** で、 **[規則の種類]** が **[標準]** のフィルターを削除します。 先ほど作成した新しい規則が両方とも表示されます。
     ![同期オン](media/how-to-connect-selective-password-hash-synchronization/include-10.png)

### <a name="re-enable-synchronization-scheduler"></a>同期スケジューラを再度有効にする:  
必要な同期規則を構成する手順を完了したら、次の手順で同期スケジューラを再度有効にします。
 1. Windows PowerShell で次を実行します。

     ```Set-ADSyncScheduler -SyncCycleEnabled $true```
 2. 次を実行して、正常に有効にされたことを確認します。

     ```Get-ADSyncScheduler```

スケジューラについて詳しくは、[Azure AD Connect スケジューラ](how-to-connect-sync-feature-scheduler.md)に関する記事を参照してください。

### <a name="edit-users-admindescription-attribute"></a>ユーザーの **adminDescription** 属性の編集:
すべての構成が完了したら、Active Directory でパスワード ハッシュ同期に **含める** すべてのユーザーの属性 **adminDescription** を編集し、スコープ フィルターで使用される文字列「**PHSIncluded**」を追加する必要があります。

  ![属性の編集](media/how-to-connect-selective-password-hash-synchronization/include-11.png)
 
 

## <a name="next-steps"></a>次の手順
- [パスワード ハッシュ同期とは](whatis-phs.md)
- [パスワード ハッシュ同期の動作](how-to-connect-password-hash-synchronization.md)
