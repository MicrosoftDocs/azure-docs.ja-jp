---
title: 'Azure AD Connect 同期: 既定の構成に変更を加える'
description: Azure AD Connect Sync の構成に変更する方法について説明します。
services: active-directory
author: billmath
manager: daveba
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2044653673da10de59d5ff125da44ac1f89e22f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861852"
---
# <a name="azure-ad-connect-sync-make-a-change-to-the-default-configuration"></a>Azure AD Connect 同期: 既定の構成に変更を加える
この記事の目的は、Azure Active Directory (Azure AD) Connect Sync の既定の構成を変更する方法について説明することです。ここでは、いくつかの一般的なシナリオの手順を紹介します。 この知識があれば、独自のビジネス ルールに基づき独自の構成に対して簡単な変更を加えることができます。

> [!WARNING]
> 既定の同期規則に変更を加えた場合、これらの変更は次回の Azure AD Connect の更新時に上書きされ、想定外の望ましくない可能性がある同期が発生する結果になります。
>
> 既定の最初の同期規則には拇印があります。 これらの規則に変更を加えた場合、拇印が一致しなくなります。 その後、Azure AD Connect の新しいリリースを適用しようとすると、問題が発生する可能性があります。 変更する場合は、この記事の方法に従ってください。

## <a name="synchronization-rules-editor"></a>同期規則エディター
同期規則エディターは、既定の構成を表示したり変更したりする際に使用されます。 このエディターは **[スタート]** メニューの **[Azure AD Connect]** グループにあります。  
![[Synchronization Rules Editor] が表示された [スタート] メニュー](./media/how-to-connect-sync-change-the-configuration/startmenu2.png)

このエディターを開くと、既定の標準の規則が表示されます。

![Sync Rule Editor](./media/how-to-connect-sync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>エディターのナビゲーション
エディターの上部にあるドロップダウンを使用すると、特定の規則をすばやく見つけることができます。 たとえば、proxyAddresses 属性が含まれる規則を表示する場合は、ドロップダウンを次のように変更します。  
![SRE のフィルター処理](./media/how-to-connect-sync-change-the-configuration/filtering.png)  
フィルターをリセットし、新しい構成を読み込むには、キーボードの F5 キーを押します。

右上には **[新しいルールの追加]** ボタンがあります。 このボタンは、独自のカスタム規則の作成に使用します。

下部には、選択した同期規則に対する操作を行うためのボタンがあります。 **[編集]** と **[削除]** をクリックすると、期待どおりの操作が行われます。 **[エクスポート]** をクリックすると、同期規則を再作成するための PowerShell スクリプトが生成されます。 この方法を使用すると、あるサーバーから別のサーバーへ同期規則を移動できます。

## <a name="create-your-first-custom-rule"></a>初めてのカスタム規則の作成
最も一般的な変更は、属性フローに対する変更です。 ソース ディレクトリ内のデータが、Azure AD 内のデータと異なる可能性があります。 このセクションの例では、常にユーザーの名前の *大文字小文字が正しくなる* ようにします。

### <a name="disable-the-scheduler"></a>スケジューラを無効にする
[スケジューラ](how-to-connect-sync-feature-scheduler.md) は、既定で 30 分ごとに実行されます。 変更中や、新しい規則のトラブルシューティング中は、スケジューラが起動されないようにします。 スケジューラを一時的に無効にするには、PowerShell を起動し、`Set-ADSyncScheduler -SyncCycleEnabled $false` を実行します。

![スケジューラを無効にする](./media/how-to-connect-sync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>規則を作成する
1. **Add new rule (新しい規則の追加)** をクリックします。
2. **[説明]** ページで、次のように入力します。  
   ![Inbound rule filtering](./media/how-to-connect-sync-change-the-configuration/description2.png)  
   * **Name**:規則にわかりやすい名前を付けます。
   * **説明**:規則の目的が他のユーザーにもわかるように説明します。
   * **接続されたシステム**: これはオブジェクトを検出できるシステムです。 この場合、**Active Directory コネクタ** を選択します。
   * **Connected System/Metaverse Object Type (接続されたシステム/メタバース オブジェクトの種類)** : それぞれ **[ユーザー]** と **[人]** を選択します。
   * **リンクの種類**: この値は **[結合]** に変更できます。
   * **優先順位**: システム内で一意になる値を指定します。 指定する数値が小さいほど、優先順位が高くなることを示します。
   * **タグ**: 空のままにします。 Microsoft が提供する標準の規則の場合のみ、このボックスには値が設定されています。
3. **[Scoping filter (スコープ フィルター)]** ページで、「**givenName ISNOTNULL**」と入力します。  
   ![Inbound rule scoping filter](./media/how-to-connect-sync-change-the-configuration/scopingfilter.png)  
   このセクションを使用して、規則を適用するオブジェクトを定義します。 空のままにした場合は、すべてのユーザー オブジェクトに規則が適用されます。 ただし、それには、会議室、サービス アカウントなど、人以外のユーザー オブジェクトも含まれます。
4. **[Join rules]\(結合規則\)** ページで、このフィールドを空のままにします。
5. **[Transformations]\(変換\)** ページで、 **[FlowType]** を **[式]** に変更します。 **[対象の属性]** で、 **[givenName]** を選択します。 **[ソース]** に「**PCase([givenName])** 」と入力します。
   ![Inbound rule transformations](./media/how-to-connect-sync-change-the-configuration/transformations.png)  
   同期エンジンでは、関数名と属性名の両方で大文字小文字が区別されます。 入力した内容が間違っている場合は、規則を追加するときに警告が表示されます。 保存して続行できますが、規則を再度開いて修正する必要があります。
6. **[追加]** をクリックして規則を保存します。

新しいカスタム規則は、システム内の他の同期規則と共に表示されます。

### <a name="verify-the-change"></a>変更を確認する
この新しい変更により、その変更が正常に動作していて、エラーがスローされていないことを確認します。 この手順を実行するには、所有しているオブジェクト数に応じて 2 つの方法があります。

- すべてのオブジェクトに対して完全同期を実行する。
- 1 つのオブジェクトに対してプレビューと完全同期を実行する。

**[スタート]** メニューから **[Synchronization Service]** を開きます。 このセクションの手順はすべて、このツールで行います。

**すべてのオブジェクトに対する完全同期**  

   1. [Actions (操作)] の **[コネクタ]** を選択します。 前の手順で変更を加えたコネクタ (この場合は Active Directory Domain Services) を特定して選択します。 
   2. **[アクション]** から **[実行]** を選択します。
   3. **[完全同期]** を選択し、 **[OK]** を選択します。
   ![Full sync](./media/how-to-connect-sync-change-the-configuration/fullsync.png)  
   これで、オブジェクトはメタバースで更新されます。 メタバース内のオブジェクトを参照して、変更を確認します。

**1 つのオブジェクトに対するプレビューと完全同期**  

   1. [Actions (操作)] の **[コネクタ]** を選択します。 前の手順で変更を加えたコネクタ (この場合は Active Directory Domain Services) を特定して選択します。
   2. **[Search Connector Space (コネクタ スペースの検索)]** を選択します。 
   3. **スコープ** を使用して、変更のテストに使用するオブジェクトを検索します。 オブジェクトを選択し、 **[プレビュー]** をクリックします。 
   4. 新しい画面で、 **[コミット プレビュー]** を選択します。  
   ![Commit preview](./media/how-to-connect-sync-change-the-configuration/commitpreview.png)  
   これで、変更はメタバースにコミットされました。

**メタバースのオブジェクトを確認する**  

1. 値が要求されていることと規則が適用されたことを確認するために、いくつかのサンプル オブジェクトを選択します。 
2. 上部の **[Metaverse Search (メタバース検索)]** を選択します。 関連するオブジェクトを検索するために必要なフィルターを追加します。 
3. 検索結果からオブジェクトを開きます。 属性値を確認し、 **[同期規則]** 列で、規則が適切に適用されていることを確認します。  
![Metaverse search](./media/how-to-connect-sync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>スケジューラを有効にする
すべてが想定どおりになったら、もう一度スケジューラを有効にすることができます。 PowerShell から、 `Set-ADSyncScheduler -SyncCycleEnabled $true`を実行します。

## <a name="other-common-attribute-flow-changes"></a>その他の一般的な属性フローの変更
前のセクションでは、属性フローを変更する方法について説明しました。 このセクションでは、いくつかの例を紹介します。 同期規則の作成方法に関する手順は省略しますが、前のセクションで詳しい手順を確認できます。

### <a name="use-an-attribute-other-than-the-default"></a>既定以外の属性の使用
この Fabrikam シナリオでは、地域ごとのアルファベットを名 (given name)、姓 (surname)、表示名 (display name) に用いたフォレストが存在します。 それらの属性については、対応するラテン文字表現が拡張属性で見つかります。 Azure AD や Microsoft 365 にグローバル アドレス一覧を作成する際は、それらの属性を使用するのが一般的です。

既定の構成では、ローカル フォレストから取得されたオブジェクトが次のように表示されます。  
![Attribute flow 1](./media/how-to-connect-sync-change-the-configuration/attributeflowjp1.png)

他の属性フローで規則を作成するには、次の手順に従います。

1. **[スタート]** メニューから **Synchronization Rules Editor** を開きます。
2. 左側で **[受信]** を選択したまま、 **[新しいルールの追加]** ボタンをクリックします。
3. 規則の名前と説明を入力します。 オンプレミスの Active Directory インスタンスと該当するオブジェクトの種類を選択します。 **[リンクの種類]** で **[結合]** を選択します。 **[優先順位]** には、他の規則で使われていない数値を選びます。 標準の規則は 100 から始まるので、この例では 50 を使用できます。
  ![Attribute flow 2](./media/how-to-connect-sync-change-the-configuration/attributeflowjp2.png)
4. **[スコープ フィルター]** は空のままにします (つまり、フォレスト内のすべてのユーザー オブジェクトに適用されます)。
5. **[Join rules]\(結合規則\)** は空のままにします (つまり、既定の規則で結合を処理します)。
6. **[Transformations]\(変換\)** で、次のフローを作成します。  
  ![属性フロー 3](./media/how-to-connect-sync-change-the-configuration/attributeflowjp3.png)
7. **[追加]** をクリックして規則を保存します。
8. **Synchronization Service Manager** に移動します。 **[コネクタ]** で、先ほど規則を追加したコネクタを選択します。 **[実行]** を選択し、 **[完全同期]** を選択します。 完全同期により、現在の規則を使ってすべてのオブジェクトが再計算されます。

このカスタム規則を同じオブジェクトに適用した後の結果は次のとおりです。  
![Attribute flow 4](./media/how-to-connect-sync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>属性の長さ
文字列属性は、既定ではインデックス可能であり、最大長は 448 文字です。 それより多い文字を含む可能性がある文字列属性を使用する場合は、属性フローに次の式を含めるようにします。  
`attributeName` <- `Left([attributeName],448)`.

### <a name="changing-the-userprincipalsuffix"></a>userPrincipalSuffix の変更
Active Directory の userPrincipalName 属性は、常にユーザーに認識されるわけではなく、サインイン ID として適切でない場合があります。 Azure AD Connect Sync のインストール ウィザードを使用すると、*mail* など別の属性を選択できます。 ただし、場合によっては、属性を計算する必要があります。

たとえば、Contoso 社に 2 つの Azure AD ディレクトリがあり、一方は運用環境用、もう一方はテスト用であるとします。 テスト テナント内のユーザーについて、サインイン ID に含まれる別のサフィックスを使用することを検討しています。  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`.

この式では、最初の @-sign の左側のすべて (Word) を使用し、固定文字列をそこに連結します。

### <a name="convert-a-multi-value-attribute-to-single-value"></a>複数値属性から単一値への変換
Active Directory の一部の属性は、Active Directory ユーザーとコンピューターでは単一値に見えますが、スキーマでは複数値になっています。 例として挙げられるのが、説明属性です。  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`.

この式で属性が値を持つ場合は、属性の最初のアイテムを使用し (*Item*)、先頭と末尾のスペースを削除して (*Trim*)、文字列の最初の 448 文字を維持します (*Left*)。

### <a name="do-not-flow-an-attribute"></a>属性をフローしない
このセクションのシナリオの背景情報については、「 [属性フローの処理の制御](concept-azure-ad-connect-sync-declarative-provisioning.md#control-the-attribute-flow-process)」を参照してください。

属性をフローしない方法は 2 つあります。 1 つ目の方法では、インストール ウィザードを使用して、[選択した属性を削除](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering)します。 このオプションは、以前に属性を同期したことがない場合に使用できます。 ただし、この属性を同期した後に、この機能で属性を削除した場合、同期エンジンによるその属性の管理は停止され、既存の値は Azure AD に残ります。

属性の値を削除し、今後はフローが実行されないようにするには、カスタム規則を作成する必要があります。

この Fabrikam シナリオでは、クラウドと同期する属性の一部が不要であることがわかりました。 不要な属性が Azure AD から削除されるようにする必要があります。  
![不適切な拡張属性](./media/how-to-connect-sync-change-the-configuration/badextensionattribute.png)

1. 新しい受信同期規則を作成し、説明を入力します。
  ![説明](./media/how-to-connect-sync-change-the-configuration/syncruledescription.png)
2. **FlowType** の場合は **Expression**、**Source** の場合は **AuthoritativeNull** を使用して属性フローを作成します。 リテラル **AuthoritativeNull** は、低い優先度の同期規則が値を設定しようとしても、メタバースの値は空になることを示します。
  ![拡張属性の変換](./media/how-to-connect-sync-change-the-configuration/syncruletransformations.png)
3. 同期規則を保存します。 **Synchronization Service** を起動し、コネクタを探して **[実行]** 、 **[完全同期]** の順に選択します。 この手順では、すべての属性フローが再計算されます。
4. コネクタ スペースを検索して、目的の変更がエクスポート対象になっていることを確認します。
  ![段階的な削除](./media/how-to-connect-sync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>PowerShell を使用して規則を作成する
変更箇所がわずかしかない場合は、同期規則エディターで間に合います。 多くの変更を加える必要がある場合は、PowerShell を使用する方法が向いています。 一部の高度な機能は PowerShell のみで利用できます。

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>既定の規則に対応する PowerShell スクリプトを取得する
既定の規則を作成した PowerShell スクリプトを確認するには、同期規則エディターで規則を選択し、 **[エクスポート]** をクリックします。 これで、規則を作成した PowerShell スクリプトを入手できます。

### <a name="advanced-precedence"></a>高度な優先順位
既定の同期規則の優先順位の値は 100 から始まります。 多くのフォレストがあり、多くのカスタム変更を加える必要がある場合、99 個の同期規則では十分でないことがあります。

既定の規則よりも前に他の規則を挿入するように、同期エンジンに指示できます。 この動作を設定するには、次の手順を実行します。

1. Synchronization Rules Editor で最初の既定の同期規則 (**In from AD-User Join**) をマークし、 **[エクスポート]** を選択します。 Copy the SR Identifier value.  
![PowerShell before change](./media/how-to-connect-sync-change-the-configuration/powershell1.png)  
2. 新しい同期規則を作成します。 Synchronization Rules Editor を使用して作成できます。 この規則を PowerShell スクリプトにエクスポートします。
3. プロパティ **PrecedenceBefore** に、既定規則の識別子の値を挿入します。 **[優先順位]** を **0** に設定します。 識別子の属性が一意であり、別の規則の GUID を再利用していないことを確認します。 また、**ImmutableTag** プロパティが設定されていないことを確認します。 このプロパティは、既定の規則に対してのみ設定する必要があります。
4. PowerShell スクリプトを保存して実行します。 これにより、カスタム規則に優先順位の値 100 が割り当てられ、他のすべての既定規則の値が 1 つずつ増えます。  
![変更後の PowerShell](./media/how-to-connect-sync-change-the-configuration/powershell2.png)  

必要に応じて、同じ **PrecedenceBefore** 値を使用する多くのカスタム同期規則を設定できます。

## <a name="enable-synchronization-of-usertype"></a>UserType の同期の有効化
バージョン 1.1.524.0 以降の Azure AD Connect には、**ユーザー** オブジェクトの **UserType** 属性を同期する機能が備わっています。 具体的には、以下の変更が行われています。

- Azure AD コネクタで、**User** オブジェクト タイプのスキーマが拡張され、UserType 属性 (文字列型、単一値) が追加されました。
- メタバースで、**Person** オブジェクト タイプのスキーマが拡張され、UserType 属性 (文字列型、単一値) が追加されました。

既定では、UserType 属性の同期が有効になっていません。これは、対応する UserType 属性がオンプレミスの Active Directory に存在しないためです。 同期を手動で有効にする必要があります。 この操作の前に、Azure AD によって適用されている次の動作に注意する必要があります。

- Azure AD で指定できる UserType 属性の値は、**Member** と **Guest** の 2 つのみです。
- Azure AD Connect で同期に対して UserType 属性が有効になっていない場合、ディレクトリの同期によって作成された Azure AD ユーザーの UserType 属性は **Member** に設定されます。
- バージョン 1.5.30.0 より前、Azure AD では、Azure AD Connect によって既存の Azure AD ユーザーの UserType 属性を変更することは許可されませんでした。 古いバージョンでは、これは、Azure AD ユーザーの作成中にのみ設定でき、[PowerShell を介して変更](/powershell/module/azuread/set-azureaduser)できます。

UserType 属性の同期を有効にする前に、まず UserType 属性をオンプレミス Active Directory からどのように派生させるかを決める必要があります。 その一般的な方法を次に示します。

- 未使用のオンプレミス AD 属性 (extensionAttribute1 など) が、ソース属性として使用されるように指定します。 指定したオンプレミス AD 属性は **文字列** 型、単一値である必要があります。また、**Member** または **Guest** 値が含まれます。 

    このアプローチを選択した場合は、UserType 属性の同期を有効にする前に、Azure AD に同期されているオンプレミス Active Directory の既存のユーザー オブジェクトすべてについて、指定した属性に正しい値が設定されていることを確認する必要があります。

- 別の方法として、UserType 属性の値を他のプロパティから派生させることができます。 たとえば、オンプレミス AD UserPrincipalName 属性の末尾のドメイン部分が <em>@partners.fabrikam123.org</em> である場合、すべてのユーザーを **ゲスト** として同期する必要があるとします。 

    前に説明したように、古いバージョンの Azure AD Connect では、Azure AD Connect によって既存の Azure AD ユーザーの UserType 属性を変更することは許可されません。 したがって、決定したロジックは、テナントの既存の Azure AD ユーザーすべてに対して UserType 属性が既に構成されている方法と一致している必要があります。

UserType 属性の同期を有効にする大まかな手順は次のとおりです。

1.  同期スケジューラを無効にし、進行中の同期がないことを確認する。
2.  オンプレミス AD コネクタのスキーマにソース属性を追加する。
3.  Azure AD コネクタのスキーマに UserType を追加する。
4.  オンプレミス Active Directory から属性値を誘導する受信方向の同期規則を作成する。
5.  Azure AD に属性値を誘導する送信方向の同期規則を作成する。
6.  完全同期サイクルを実行する。
7.  同期スケジューラを有効にする。

>[!NOTE]
> 以降このセクションでは、この手順について説明します。 これらの説明では、単一フォレスト トポロジの Azure AD デプロイを想定しており、また、カスタム同期規則は使用しません。 複数フォレスト トポロジを使用していて、カスタム同期規則が構成されているか、またはステージング サーバーが存在する場合は、適宜これらの手順を調整する必要があります。

### <a name="step-1-disable-the-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>手順 1:同期スケジューラを無効にし、進行中の同期がないことを確認する
意図しない変更が Azure AD にエクスポートされるのを防ぐために、同期規則の更新中は、同期が実行されないように注意してください。 組み込みスケジューラを無効にするには、以下のようにします。

 1. Azure AD Connect サーバーで PowerShell セッションを開始します。
 2. `Set-ADSyncScheduler -SyncCycleEnabled $false` コマンドレットを実行して、スケジュールされた同期を無効にします。
 3. **[スタート]**  >  **[Synchronization Service]** の順に移動して、Sychronization Service Manager を起動します。
 4. **[操作]** タブに移動し、状態が "*進行中*" の操作がないことを確認します。

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>手順 2:オンプレミス AD コネクタのスキーマにソース属性を追加する
オンプレミス AD のコネクタ スペースには、一部の Azure AD 属性がインポートされません。 インポート対象の属性のリストにソース属性を追加するには、次の手順を実行します。

 1. Synchronization Service Manager の **[コネクタ]** タブに進みます。
 2. オンプレミス AD コネクタを右クリックし、 **[プロパティ]** を選択します。
 3. ポップアップ ダイアログ ボックスで **[属性の選択]** タブに移動します。
 4. 属性の一覧でソース属性のチェック ボックスがオンになっていることを確認します。
 5. **[OK]** をクリックして保存します。
![オンプレミス AD コネクタのスキーマへのソース属性の追加](./media/how-to-connect-sync-change-the-configuration/usertype1.png)

### <a name="step-3-add-the-usertype-attribute-to-the-azure-ad-connector-schema"></a>手順 3:Azure AD コネクタのスキーマに UserType 属性を追加する
既定では、Azure AD のコネクタ スペースに UserType 属性がインポートされません。 インポート対象の属性のリストに UserType 属性を追加するには、次の手順を実行します。

 1. Synchronization Service Manager の **[コネクタ]** タブに進みます。
 2. **Azure AD コネクタ** を右クリックし、 **[プロパティ]** を選択します。
 3. ポップアップ ダイアログ ボックスで **[属性の選択]** タブに移動します。
 4. 属性の一覧で UserType 属性のチェック ボックスがオンになっていることを確認します。
 5. **[OK]** をクリックして保存します。

![Azure AD コネクタのスキーマへのソース属性の追加](./media/how-to-connect-sync-change-the-configuration/usertype2.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>手順 4:オンプレミス Active Directory から属性値を誘導する受信方向の同期規則を作成する
受信方向の同期規則によって、属性値をオンプレミス Active Directory のソース属性からメタバースに誘導することができます。

1. **[スタート]**  >  **[Synchronization Rules Editor]** の順に移動して、Synchronization Rules Editor を起動します。
2. 検索フィルターの **[方向]** を **[受信]** に設定します。
3. **[新しいルールの追加]** ボタンをクリックして受信方向の規則を新規作成します。
4. **[説明]** タブで次の構成を指定します。

    | 属性 | 値 | 詳細 |
    | --- | --- | --- |
    | 名前 | *名前を入力します* | 例: *In from AD – User UserType* |
    | 説明 | *説明を入力します* |  |
    | 接続先システム | "*オンプレミス AD コネクタを選択します*" |  |
    | 接続先システム オブジェクトの種類 | **User** |  |
    | メタバース オブジェクトの種類 | **Person** |  |
    | リンクの種類 | **Join** |  |
    | 優先順位 | *1 ～ 99 の範囲の数値を選択します* | 1 ～ 99 は、カスタム同期規則用に予約されています。 他の同期規則で使用されている値は選択しないでください。 |

5. **[スコープ フィルター]** タブに移動し、次の句を使って **単一のスコープ フィルター グループ** を追加します。

    | 属性 | 演算子 | 値 |
    | --- | --- | --- |
    | adminDescription | NOTSTARTWITH | ユーザー\_ |

    この受信方向の同期規則がどのオンプレミス AD オブジェクトに適用されるかは、スコープ フィルターによって決まります。 この例で使用しているスコープ フィルターは、既定の同期規則 *In from AD - User Common* と同じもので、Azure AD のユーザーの書き戻し機能によって作成されたユーザー オブジェクトに同期規則が適用されるのを防ぎます。 スコープ フィルターは、実際の Azure AD Connect のデプロイに応じて調整が必要となる場合があります。

6. **[変換]** タブに移動し、必要な変換規則を実装します。 たとえば、未使用のオンプレミス AD 属性 (extensionAttribute1 など) を UserType のソース属性として指定した場合は、直接属性フローを実装できます。

    | フローの種類 | ターゲット属性 | source | 1 度だけ適用する | マージの種類 |
    | --- | --- | --- | --- | --- |
    | 直接 | UserType | extensionAttribute1 | オフ | 更新 |

    他の例として、他のプロパティから UserType 属性の値を派生させる場合があります。 たとえば、オンプレミス AD UserPrincipalName 属性の末尾のドメイン部分が <em>@partners.fabrikam123.org</em> である場合、すべてのユーザーをゲストとして同期する必要があるとします。次のように式を実装することができます。

    | フローの種類 | ターゲット属性 | source | 1 度だけ適用する | マージの種類 |
    | --- | --- | --- | --- | --- |
    | 式 | UserType | IIF(IsPresent([userPrincipalName]),IIF(CBool(InStr(LCase([userPrincipalName]),"@partners.fabrikam123.org")=0),"Member","Guest"),Error("UserPrincipalName is not present to determine UserType")) | オフ | 更新 |

7. **[追加]** をクリックして受信方向の規則を作成します。

![受信方向の同期規則の作成](./media/how-to-connect-sync-change-the-configuration/usertype3.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>手順 5:Azure AD に属性値を誘導する送信方向の同期規則を作成する
送信方向の同期規則によって、メタバースから Azure AD の UserType 属性に属性値が流れるのを許可します。

1. Synchronization Rules Editor に移動します。
2. 検索フィルターの **[方向]** を **[送信]** に設定します。
3. **[新しいルールの追加]** ボタンをクリックします。
4. **[説明]** タブで次の構成を指定します。

    | 属性 | 値 | 詳細 |
    | ----- | ------ | --- |
    | 名前 | *名前を入力します* | 例: *Out to AAD – User UserType* |
    | 説明 | *説明を入力します* ||
    | 接続先システム | "*AAD コネクタを選択します*" ||
    | 接続先システム オブジェクトの種類 | **User** ||
    | メタバース オブジェクトの種類 | **Person** ||
    | リンクの種類 | **Join** ||
    | 優先順位 | *1 ～ 99 の範囲の数値を選択します* | 1 ～ 99 は、カスタム同期規則用に予約されています。 他の同期規則で使用されている値は選択しないでください。 |

5. **[スコープ フィルター]** タブに移動し、次の 2 つの句を使って **単一のスコープ フィルター グループ** を追加します。

    | 属性 | 演算子 | 値 |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | User |
    | cloudMastered | NOTEQUAL | True |

    この送信方向の同期規則がどの Azure AD オブジェクトに適用されるかは、スコープ フィルターによって決まります。 この例で使用しているスコープ フィルターは、既定の同期規則 *Out to AD - User Identity* と同じものです。 こうすることで、オンプレミス Active Directory との間で同期されていないユーザー オブジェクトに、この同期規則が適用されるのを防いでいます。 スコープ フィルターは、実際の Azure AD Connect のデプロイに応じて調整が必要となる場合があります。

6. **[変換]** タブに移動し、次の変換規則を実装します。

    | フローの種類 | ターゲット属性 | source | 1 度だけ適用する | マージの種類 |
    | --- | --- | --- | --- | --- |
    | 直接 | UserType | UserType | オフ | 更新 |

7. **[追加]** をクリックして送信方向の規則を作成します。

![送信方向の同期規則の作成](./media/how-to-connect-sync-change-the-configuration/usertype4.png)

### <a name="step-6-run-a-full-synchronization-cycle"></a>手順 6:完全同期サイクルを実行する
Active Directory のスキーマと Azure AD コネクタのスキーマに新しい属性を追加し、カスタム同期規則を導入したので、一般に完全同期サイクルが必要です。 Azure AD にエクスポートする前に、これらの変更を検証することができます。 

完全同期サイクルの手順を手動で実行する過程で、次の手順に従って変更を検証できます。

1. **オンプレミス AD コネクタ** で **フル インポート** を実行します。

   1. Synchronization Service Manager の **[コネクタ]** タブに進みます。
   2. **オンプレミス AD コネクタ** を右クリックし、 **[実行]** を選択します。
   3. ポップアップ ダイアログ ボックスで **[フル インポート]** を選択し、 **[OK]** をクリックします。
   4. 操作が完了するのを待ちます。

      > [!NOTE]
      > インポート対象の属性のリストにソース属性が既に追加されている場合、オンプレミス AD コネクタでのフル インポートはスキップしてかまいません。 つまり、この記事の「[手順 2: オンプレミス AD コネクタのスキーマにソース属性を追加する」](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema)の間に何も変更する必要がなかった場合です。

2. **Azure AD コネクタ** で **[フル インポート]** を実行します。

   1. **Azure AD コネクタ** を右クリックし、 **[実行]** を選択します。
   2. ポップアップ ダイアログ ボックスで **[フル インポート]** を選択し、 **[OK]** をクリックします。
   3. 操作が完了するのを待ちます。

3. 既存のユーザー オブジェクトで同期規則の変更を検証します。

    オンプレミス Active Directory からのソース属性と Azure AD からの UserType がそれぞれのコネクタ スペースにインポートされています。 完全同期に進む前に、オンプレミス AD のコネクタ スペースに既に存在しているユーザー オブジェクトで **プレビュー** を実行します。 選択したオブジェクトのソース属性に値が設定されている必要があります。
    
    メタバースの UserType に値が設定されていることを **プレビュー** で確認できれば、同期規則が正しく構成されていると考えられます。 **プレビュー** の方法については、「[変更を確認する](#verify-the-change)」セクションを参照してください。

4. **オンプレミス AD コネクタ** で **完全同期** を実行します。

   1. **オンプレミス AD コネクタ** を右クリックし、 **[実行]** を選択します。
   2. ポップアップ ダイアログ ボックスで **[完全同期]** を選択し、 **[OK]** をクリックします。
   3. 操作が完了するのを待ちます。

5. Azure AD に対する **保留中のエクスポート** を確認します。

   1. **Azure AD コネクタ** を右クリックし、 **[コネクタ スペースの検索]** を選択します。
   2. **[Search Connector Space]\(コネクタ スペースの検索\)** ポップアップ ダイアログ ボックスで次の手順を実行します。

      - **[スコープ]** を **[Pending Export]\(保留中のエクスポート\)** に設定します。
      - 次の 3 つのチェック ボックスすべてをオンにします: **[追加]** 、 **[変更]** 、および **[削除]** 。
      - **[検索]** ボタンをクリックすると、変更がエクスポート待ちになっているオブジェクトが一覧表示されます。 指定したオブジェクトの変更を検証するには、オブジェクトをダブルクリックします。
      - 変更が正しいことを確認します。

6. **Azure AD コネクタ** で **[エクスポート]** を実行します。

   1. **Azure AD コネクタ** を右クリックし、 **[実行]** を選択します。
   2. **[Run Connector]\(コネクタの実行\)** ポップアップ ダイアログ ボックスで、 **[エクスポート]** を選択し、 **[OK]** をクリックします。
   3. Azure AD へのエクスポートが完了するまで待ちます。

> [!NOTE]
> 以上の手順には、Azure AD コネクタでの完全同期とエクスポートの手順が含まれていません。 属性値の流れはオンプレミス Active Directory から Azure AD への一方向であるため、これらの手順は必要ありません。

### <a name="step-7-re-enable-the-sync-scheduler"></a>手順 7:同期スケジューラを有効にする
次の手順で組み込みの同期スケジューラを再度有効にします。

1. PowerShell セッションを開始します。
2. `Set-ADSyncScheduler -SyncCycleEnabled $true` コマンドレットを実行して、スケジュールされた同期を再度有効にします。


## <a name="next-steps"></a>次のステップ
* この構成モデルについて詳しくは、「 [Understanding Declarative Provisioning (宣言型のプロビジョニングについて)](concept-azure-ad-connect-sync-declarative-provisioning.md)」をご覧ください。
* 式言語について詳しくは、「 [宣言型のプロビジョニングの式について](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)」をご覧ください。

**概要トピック**

* [Azure AD Connect 同期:同期を理解してカスタマイズする](how-to-connect-sync-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)
