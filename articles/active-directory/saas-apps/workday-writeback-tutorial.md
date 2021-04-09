---
title: チュートリアル:Azure Active Directory で Workday 書き戻しを構成する | Microsoft Docs
description: Azure AD から Workday への属性の書き戻しを構成する方法について学びます
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 10/14/2020
ms.author: chmutali
ms.openlocfilehash: 3a623a487dd31caf8c85b18771d90e3a6306df68
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954006"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-workday"></a>チュートリアル:Azure AD から Workday への属性の書き戻しを構成する
このチュートリアルの目的は Azure AD から Workday に属性を書き戻すために必要な手順を説明することです。 Workday Writeback プロビジョニング アプリは、次の Workday 属性に対する値の割り当てをサポートします。
* 勤務先の電子メール 
* Workday ユーザー名
* 職場の固定電話番号 (国コード、市外局番、番号、内線番号を含む)
* 職場の固定電話番号のプライマリ フラグ
* 職場の携帯電話番号 (国コード、市外局番、番号を含む)
* 職場の携帯電話のプライマリ フラグ

## <a name="overview"></a>概要

[Workday からオンプレミス AD](workday-inbound-tutorial.md) のプロビジョニング アプリまたは [Workday から Azure AD](workday-inbound-cloud-only-tutorial.md) のプロビジョニング アプリを使用してインバウンド プロビジョニング統合を設定した後、必要に応じて、勤務先の電子メールや電話番号などの連絡先情報を Workday に書き戻すように Workday Writeback アプリを構成することができます。 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>このユーザー プロビジョニング ソリューションが最適な場合

この Workday Writeback ユーザー プロビジョニング ソリューションは、次のお客様に最適です。

* IT が管理する正式な属性 (メールアドレス、ユーザー名、電話番号など) を Workday に書き戻したいと考える、Microsoft 365 を使用している組織

## <a name="configure-integration-system-user-in-workday"></a>Workday の統合システム ユーザーの構成

Workday 統合システム ユーザー アカウントとワーカー データを取得するためのアクセス許可の作成については、[統合システム ユーザーの構成](workday-inbound-tutorial.md#configure-integration-system-user-in-workday)に関するセクションを参照してください。 

## <a name="configuring-azure-ad-attribute-writeback-to-workday"></a>Workday への Azure AD 属性の書き戻しの構成

ユーザーのメールアドレスおよびユーザー名を Azure Active Directory から Workday に書き戻すように構成するには、次の手順に従ってください。

* [Writeback コネクタ アプリの追加と Workday への接続の作成](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [書き戻し属性マッピングの構成](#part-2-configure-writeback-attribute-mappings)
* [ユーザー プロビジョニングの有効化と起動](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>パート 1: Writeback コネクタ アプリの追加と Workday への接続の作成

**Workday Writeback コネクタを構成するには:**

1. <https://portal.azure.com> にアクセスします。

2. Azure portal で、 **[Azure Active Directory]** を検索して選択します。

3. **[エンタープライズ アプリケーション]** 、 **[すべてのアプリケーション]** の順に選択します。

4. **[アプリケーションの追加]** を選択し、 **[すべて]** のカテゴリを選択します。

5. **Workday Writeback** を検索し、ギャラリーからそのアプリを追加します。

6. アプリが追加され、アプリの詳細画面が表示されたら、 **[プロビジョニング]** を選択します。

7. **[プロビジョニング** **モード]** を **[自動]** に変更します。

8. 以下のように **[管理者の資格情報]** セクションを完了します。

   * **管理者ユーザー名** – Workday 統合システム アカウントのユーザー名にテナント ドメイン名を追加して入力します。 このようになります。*username\@contoso4*

   * **管理者パスワード** - Workday 統合システム アカウントのパスワードを入力します

   * **テナント URL –** テナントの Workday Web サービス エンドポイントへの URL を入力します。 この値は `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources` のようになります。*contoso4* は適切なテナント名に置き換え、*wd3-impl* は適切な環境文字列に置き換えます (必要な場合)。

   * **メール通知** - メール アドレスを入力し、[send email if failure occurs]\(失敗した場合にメールを送信する\) チェックボックスをオンにします。

   * **[接続のテスト]** ボタンをクリックします。 接続テストが成功した場合、上部の **[保存]** ボタンをクリックします。 失敗した場合は、Workday URL と資格情報が Workday で有効であることを再度確認します。

### <a name="part-2-configure-writeback-attribute-mappings"></a>パート 2: 書き戻し属性マッピングの構成

このセクションでは、Azure AD から Workday への書き戻し属性の流れを構成します。 

1. **[マッピング]** の [プロビジョニング] タブで、マッピング名をクリックします。

2. **[ソース オブジェクト スコープ]** フィールドでは、書き戻しの対象となる Azure Active Directory のユーザー セットを、必要に応じてフィルターで選択することができます。 既定のスコープは、"Azure AD のすべてのユーザー" です。

3. **[属性マッピング]** セクションで、Workday の Worker ID または従業員 ID が格納されている Azure Active Directory の属性を示すように一致する ID を更新します。 一般的なマッチング メソッドは、Workday の Worker ID または従業員 ID を Azure AD の extensionAttribute1-15 に同期してから、この Azure AD の属性を使用して、Workday に戻ってユーザーを照合します。

4. 通常、Azure AD の *userPrincipalName* 属性を Workday の *UserID* 属性にマップし、Azure AD の *mail* 属性を Workday の *EmailAddress* 属性にマップします。 

     >[!div class="mx-imgBorder"]
     >![Azure Portal](./media/workday-inbound-tutorial/workday-writeback-mapping.png)

5. 以下のガイダンスに従って、Azure AD から Workday に電話番号属性の値をマップします。 

     | Workday 電話番号属性 | 必要な値 | マッピングのガイダンス |
     |-------------------------|----------------|------------------|
     | WorkphoneLandlineIsPrimary | true/false | 文字列の "true" または "false" が出力値として得られる定数または式によるマッピング |
     | WorkphoneLandlineCountryCodeName | [3 文字の ISO 3166-1 国コード](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | 3 文字の国コードが出力として得られる定数または式によるマッピング |
     | WorkphoneLandlineCountryCodeNumber | [国際電話番号の国コード](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | 有効な国コード (+ 記号なし) が出力として得られる定数または式によるマッピング |
     | WorkphoneLandlineNumber | 市外局番を含む完全な電話番号 | *telephoneNumber* 属性にマップします。 空白、かっこ、国コードは、正規表現を使用して削除します。 以下の例を参照してください。 |
     | WorkphoneLandlineExtension | 内線番号 | *telephoneNumber* に内線番号が含まれている場合、正規表現を使用して値を抽出します。 |
     | WorkphoneMobileIsPrimary | true/false | 文字列の "true" または "false" が出力値として得られる定数マッピングまたは式によるマッピング |
     | WorkphoneMobileCountryCodeName | [3 文字の ISO 3166-1 国コード](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | 3 文字の国コードが出力として得られる定数または式によるマッピング |
     | WorkphoneMobileCountryCodeNumber | [国際電話番号の国コード](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | 有効な国コード (+ 記号なし) が出力として得られる定数または式によるマッピング |
     | WorkphoneMobileNumber | 市外局番を含む完全な電話番号 | *mobile* 属性にマップします。 空白、かっこ、国コードは、正規表現を使用して削除します。 以下の例を参照してください。 |

     > [!NOTE]
     > Change_Work_Contact Workday Web サービスを呼び出すと、Azure AD から次の定数値が送信されます。 <br>
     > * **Communication_Usage_Type_ID** は、定数文字列 "WORK" に設定されます。 <br>
     > * **Phone_Device_Type_ID** は、携帯電話番号の場合は定数文字列 "Mobile" に、固定電話番号の場合は "Landline" に設定されます。 <br>
     > 
     > ご利用の Workday テナントで異なる Type_ID が使用されている場合、書き戻しエラーが発生します。 そのようなエラーを防ぐために、Workday の **[Maintain Reference IDs]\(参照 ID のメンテナンス\)** タスクを使用して、Azure AD で使用されている値と一致するように Type_ID を更新してください。 <br>
     >  

     **正規表現式リファレンス - 例 1**

     セルフサービス パスワード リセット (SSPR) に必要な形式で Azure AD 内の電話番号が設定されている場合は、以下の正規表現を使用してください。 <br>
     例: 電話番号の値が +1 1112223333 である場合、この正規表現式によって 1112223333 が出力されます。

     ```C#
     Replace([telephoneNumber], , "\\+(?<isdCode>\\d* )(?<phoneNumber>\\d{10})", , "${phoneNumber}", , )
     ```

     **正規表現式リファレンス - 例 2**

     (XXX) XXX-XXXX 形式で Azure AD 内の電話番号が設定されている場合は、以下の正規表現を使用してください。 <br>
     例: 電話番号の値が (111) 222-3333 である場合、この正規表現式によって 1112223333 が出力されます。

     ```C#
     Replace([mobile], , "[()\\s-]+", , "", , )
     ```

6. マッピングを保存するには、[属性マッピング] セクションの上部にある **[保存]** をクリックします。

## <a name="enable-and-launch-user-provisioning"></a>ユーザー プロビジョニングの有効化と起動

Workday プロビジョニング アプリの構成が完了したら、Azure portal でプロビジョニング サービスを有効にすることができます。

> [!TIP]
> 既定では、プロビジョニング サービスを有効にすると、スコープ内のすべてのユーザーに対してプロビジョニング操作が開始されます。 マッピングのエラーまたは Workday データの問題がある場合、プロビジョニング ジョブが失敗し、検疫状態になる可能性があります。 これを避けるために、ベスト プラクティスとして、すべてのユーザーの完全同期を開始する前に、 **[ソース オブジェクト スコープ]** フィルターを構成して少数のテスト ユーザーで属性マッピングをテストすることをお勧めします。 マッピングが機能し、目的の結果が得られていることを確認したら、フィルターを削除するか、徐々に拡張してより多くのユーザーを含めることができます。

1. **[プロビジョニング]** タブで、 **[プロビジョニングの状態]** を **[ON]** に設定します。

1. **[スコープ]** ドロップダウンで **[すべてのユーザーとグループを同期する]** を選択します。 このオプションを使用すると、Writeback アプリによって、すべてのユーザーのマップされた属性が、 **[マッピング]**  ->  **[ソース オブジェクト スコープ]** で定義されたスコープ規則に従って Azure AD から Workday に書き戻されます。 

   > [!div class="mx-imgBorder"]
   > ![書き戻しのスコープを選択する](./media/sap-successfactors-inbound-provisioning/select-writeback-scope.png)

   > [!NOTE]
   > Workday の Writeback プロビジョニング アプリでは、 **[割り当てられたユーザーとグループのみを同期する]** オプションはサポートされていません。
 

2. **[保存]** をクリックします。

3. この操作により初期同期が開始されます。これに要する時間はソース ディレクトリのユーザー数に応じて変わります。 進行状況バーをチェックして、同期サイクルの進行状況を追跡できます。 

4. 好きなときに、Azure portal の **[プロビジョニング ログ]** タブをチェックして、プロビジョニング サービスで実行されたアクションを確認します。 監査ログには、ソースからインポートされたユーザーやターゲット アプリケーションにエクスポートされたユーザーなど、プロビジョニング サービスによって実行された個々の同期イベントがすべて表示されます。  

5. 最初の同期が完了すると、次に示すように、 **[プロビジョニング]** タブに概要レポートが出力されます。

     > [!div class="mx-imgBorder"]
     > ![プロビジョニングの進行状況バー](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="known-issues-and-limitations"></a>既知の問題と制限事項

* Writeback アプリでは、**Communication_Usage_Type_ID** および **Phone_Device_Type_ID** の各パラメーターに定義済みの値が使用されます。 Workday テナントで、これらの属性に別の値が使用されている場合、書き戻し操作は成功しません。 回避策として、Workday の Type_ID を更新することをお勧めします。 
* Writeback アプリがセカンダリ電話番号を更新するように構成されている場合、Workday の既存のセカンダリ電話番号は置き換えられません。 新しいセカンダリ電話番号が worker レコードに追加されます。 この動作に対する回避策はありません。 


## <a name="next-steps"></a>次のステップ

* [Azure AD と Workday の統合シナリオと Web サービス呼び出しについて](../app-provisioning/workday-integration-reference.md)
* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
* [Workday と Azure Active Directory の間でシングル サインオンを構成する方法](workday-tutorial.md)
* [他の SaaS アプリケーションを Azure Active Directory と統合する方法](tutorial-list.md)
* [プロビジョニング構成をエクスポートおよびインポートする方法を学習する](../app-provisioning/export-import-provisioning-configuration.md)

