---
title: Azure AD Connect:Office 365 リソースの優先されるデータの場所の構成
description: Azure Active Directory Connect 同期を使用して、Office 365 ユーザー リソースをユーザーの近くに配置する方法について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/11/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 723411191d0990583d039a0fc9651437480807b4
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983264"
---
# <a name="azure-active-directory-connect-sync-configure-preferred-data-location-for-office-365-resources"></a>Azure Active Directory Connect (同期): Office 365 リソースの優先されるデータの場所の構成
このトピックの目的は、Azure Active Directory (Azure AD) Connect 同期で、優先されるデータの場所の属性を構成する方法について説明することです。Office 365 で Multi-Geo 機能を使用するときに、この属性を使用して、ユーザーの Office 365 データの地理的な場所を指定します。 ("*リージョン*" と *geo* という用語は、同じ意味で使用されています。)

## <a name="enable-synchronization-of-preferred-data-location"></a>優先されるデータの場所の同期の有効化
既定では、ユーザーの Office 365 リソースは、Azure AD テナントと同じ geo にあります。 たとえば、テナントが北米にある場合、ユーザーの Exchange メールボックスも北米にあります。 多国籍組織にとっては、これが最適でないことがあります。

**preferredDataLocation** 属性を設定することで、ユーザーの geo を定義できます。 ユーザーの Office 365 リソース (メールボックスや OneDrive など) をユーザーと同じ geo に配置しながら、組織全体で 1 つのテナントを維持できます。

> [!IMPORTANT]
> Multi-Geo は現在、有効なマイクロソフト エンタープライズ契約と 500 以上の Office 365 サービス サブスクリプションを保有しているお客様が利用できます。 詳細については、Microsoft 担当者にお問い合わせください。
>
>

Office 365 向けのすべての geo の一覧については、「[データの保管場所](https://aka.ms/datamaps)」を参照してください。

Multi-Geo で使用できる Office 365 の geo を次に示します。

| ジオ (主要地域) | preferredDataLocation 値 |
| --- | --- |
| アジア太平洋 | APC |
| オーストラリア | AUS |
| カナダ | CAN |
| 欧州連合 | EUR |
| フランス | FRA |
| インド | IND |
| 日本 | JPN |
| 韓国 | KOR |
| 南アフリカ | ZAF |
| スイス | CHE |
| アラブ首長国連邦 | ARE |
| イギリス | GBR |
| 米国 | NAM |

* この表に掲載されていない geo (南アメリカなど) は、Multi-Geo には使用できません。

* すべての Office 365 ワークロードでユーザーの geo 設定が使用できるわけではありません。

### <a name="azure-ad-connect-support-for-synchronization"></a>Azure AD Connect の同期のサポート

バージョン 1.1.524.0 以降の Azure AD Connect には、**ユーザー** オブジェクトの **PreferredDataLocation** 属性を同期する機能が備わっています。 具体的な内容は次のとおりです。

* Azure AD コネクタで、**ユーザー** オブジェクト タイプのスキーマが拡張され、**preferredDataLocation** 属性が追加されました。 属性の型は単一値の文字列です。
* メタバースで、**Person** オブジェクト タイプのスキーマが拡張され、**preferredDataLocation** 属性が追加されました。 属性の型は単一値の文字列です。

既定では、**preferredDataLocation** 属性は、同期に対して有効になっていません。 この機能は大規模組織に向けられたものです。 Windows Server 2019 の Active Directory スキーマには、この目的で使用する必要がある属性 **msDS-preferredDataLocation** があります。 Active Directory スキーマを更新しておらず、それを実行できない場合は、ユーザーの Office 365 geo を保持する属性を指定する必要があります。 これは、組織ごとに異なります。

> [!IMPORTANT]
> Azure AD では、**クラウドのユーザー オブジェクト**の **preferredDataLocation** 属性を、Azure AD PowerShell を使用して直接構成できます。 **同期されたユーザー オブジェクト**の preferredDataLocation 属性を構成するには、Azure AD Connect を使用する必要があります。

同期を有効にする前に、以下の操作を実行します。

* Active Directory スキーマを 2019 にアップグレードしていない場合は、基になる属性として使用するオンプレミスの Active Directory 属性を決定します。 属性の型は、**単一値の文字列**である必要があります。
* Azure AD の既存の**同期されているユーザー オブジェクト**に対し、Azure AD PowerShell を使用して **preferredDataLocation** 属性を構成したことがある場合、その属性値を、オンプレミスの Active Directory 内の対応する**ユーザー** オブジェクトにバックポートする必要があります。

    > [!IMPORTANT]
    > これらの値をバックポートしなかった場合、**preferredDataLocation** 属性の同期を有効にすると、Azure AD Connect によって Azure AD の既存の属性値が削除されます。

* ここで、少なくとも 2 つのオンプレミスの Active Directory ユーザー オブジェクトに対するソース属性を構成します。 これは後で検証のために使用できます。

以下のセクションでは、**preferredDataLocation** 属性の同期を有効にする手順について説明します。

> [!NOTE]
> 手順の説明では、単一フォレスト トポロジの Azure AD デプロイを想定しており、また、カスタム同期規則は使用しません。 複数フォレスト トポロジを使用していて、カスタム同期規則が構成されているか、またはステージング サーバーが存在する場合は、適宜これらの手順を調整する必要があります。

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>手順 1:同期スケジューラを無効にし、進行中の同期がないことを確認する
意図しない変更が Azure AD にエクスポートされるのを回避するために、同期規則の更新中は、同期が実行されないようにします。 組み込みスケジューラを無効にするには、以下のようにします。

1. Azure AD Connect サーバーで PowerShell セッションを開始します。
2. `Set-ADSyncScheduler -SyncCycleEnabled $false` コマンドレットを実行することで、スケジュールされた同期を無効にします。
3. **[スタート]**  >  **[Synchronization Service]** の順に移動して、**Sychronization Service Manager** を起動します。
4. **[操作]** タブを選択し、状態が "*進行中*" になっている操作がないことを確認します。

![Synchronization Service Manager のスクリーンショット](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-refresh-the-schema-for-active-directory"></a>手順 2:Active Directory のスキーマを更新する
スキーマ拡張の前に、Active Directory スキーマを 2019 に更新済みで、Connect がインストールされていた場合、Connect スキーマのキャッシュに更新されたスキーマは含まれていません。 この場合、UI に表示するには、ウィザードからスキーマを更新する必要があります。

1. デスクトップから Azure AD Connect ウィザードを起動します。
2. **[ディレクトリ スキーマの更新]** オプションを選択し、 **[次へ]** をクリックします。
3. Azure AD の資格情報を入力し、 **[次へ]** をクリックします。
4. **[ディレクトリ スキーマの更新]** ページで、すべてのフォレストが選択されていることを確認し、 **[次へ]** をクリックします。
5. 完了したら、ウィザードを閉じます。

![Connect ウィザードの [ディレクトリ スキーマの更新] のスクリーンショット](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-refreshschema.png)

## <a name="step-3-add-the-source-attribute-to-the-on-premises-active-directory-connector-schema"></a>手順 3:オンプレミスの Active Directory コネクタのスキーマにソース属性を追加する
**この手順は、Connect バージョン 1.3.21 以前を実行している場合にのみ必要です。1.4.18 以降を使用している場合は、手順 5 に進みます。**  
オンプレミスの Active Directory コネクタ スペースには、すべての Azure AD 属性がインポートされるわけではありません。 既定で同期されない属性を使用するように選択した場合、その属性をインポートする必要があります。 インポート対象の属性のリストにソース属性を追加するには、次の手順を実行します。

1. Synchronization Service Manager の **[コネクタ]** タブを選択します。
2. オンプレミスの Active Directory コネクタを右クリックし、 **[プロパティ]** を選択します。
3. ポップアップ ダイアログ ボックスで **[属性の選択]** タブに移動します。
4. 属性の一覧で、使用することを選択したソース属性のチェック ボックスがオンになっていることを確認します。 属性が表示されない場合は、 **[すべて表示]** チェックボックスを選択します。
5. 保存するには、 **[OK]** を選択します。

![Synchronization Service Manager とプロパティ ダイアログ ボックスのスクリーンショット](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-4-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>手順 4:Azure AD コネクタのスキーマに **PreferredDataLocation** を追加する
**この手順は、Connect バージョン 1.3.21 以前を実行している場合にのみ必要です。1.4.18 以降を使用している場合は、手順 5 に進みます。**  
既定では、Azure AD のコネクタ スペースに **preferredDataLocation** 属性がインポートされません。 これをインポート対象の属性の一覧に追加するには、以下の操作を実行します。

1. Synchronization Service Manager の **[コネクタ]** タブを選択します。
2. Azure AD コネクタを右クリックし、 **[プロパティ]** を選択します。
3. ポップアップ ダイアログ ボックスで **[属性の選択]** タブに移動します。
4. 一覧で **preferredDataLocation** 属性を選択します。
5. 保存するには、 **[OK]** を選択します。

![Synchronization Service Manager とプロパティ ダイアログ ボックスのスクリーンショット](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-5-create-an-inbound-synchronization-rule"></a>手順 5:受信方向の同期規則を作成する
受信方向の同期規則によって、オンプレミスの Active Directory のソース属性からメタバースに属性値が流れるのを許可します。

1. **[スタート]**  >  **[Synchronization Rules Editor]** の順に移動して、**Synchronization Rules Editor** を起動します。
2. 検索フィルターの **[方向]** を **[受信]** に設定します。
3. 受信方向の規則を新規作成するには、 **[新しいルールの追加]** を選択します。
4. **[説明]** タブで次の構成を指定します。

    | 属性 | 値 | 詳細 |
    | --- | --- | --- |
    | 名前 | *名前を入力します* | 例: "In from AD – User preferredDataLocation" |
    | 説明 | "*ユーザー設定の説明を入力します*" |  |
    | 接続先システム | "*オンプレミスの Active Directory コネクタを選択します*" |  |
    | 接続先システム オブジェクトの種類 | **User** |  |
    | メタバース オブジェクトの種類 | **Person** |  |
    | リンクの種類 | **Join** |  |
    | 優先順位 | *1 ～ 99 の範囲の数値を選択します* | 1 ～ 99 は、カスタム同期規則用に予約されています。 他の同期規則で使用されている値は選択しないでください。 |

5. すべてのオブジェクトを含めるために、 **[スコープ フィルター]** は空のままにします。 スコープ フィルターは、実際の Azure AD Connect のデプロイに応じて調整が必要となる場合があります。
6. **[変換]** タブに移動し、次の変換規則を実装します。

    | フローの種類 | ターゲット属性 | source | 1 度だけ適用する | マージの種類 |
    | --- | --- | --- | --- | --- |
    |直接 | preferredDataLocation | ソース属性を選択します | オフ | 更新 |

7. 受信方向の規則を作成するには、 **[追加]** を選択します。

![受信方向の同期規則の作成のスクリーンショット](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-6-create-an-outbound-synchronization-rule"></a>手順 6:送信方向の同期規則を作成する
送信方向の同期規則によって、メタバースから Azure AD の **preferredDataLocation** 属性に属性値が流れるのを許可します。

1. **Synchronization Rules Editor** に移動します。
2. 検索フィルターの **[方向]** を **[送信]** に設定します。
3. **[新しいルールの追加]** を選択します。
4. **[説明]** タブで次の構成を指定します。

    | 属性 | 値 | 詳細 |
    | ----- | ------ | --- |
    | 名前 | *名前を入力します* | 例: "Out to Azure AD – User preferredDataLocation" |
    | 説明 | *説明を入力します* ||
    | 接続先システム | *Azure AD Connector を選択する* ||
    | 接続先システム オブジェクトの種類 | **User** ||
    | メタバース オブジェクトの種類 | **Person** ||
    | リンクの種類 | **Join** ||
    | 優先順位 | *1 ～ 99 の範囲の数値を選択します* | 1 ～ 99 は、カスタム同期規則用に予約されています。 他の同期規則で使用されている値は選択しないでください。 |

5. **[スコープ フィルター]** タブに移動し、次の 2 つの句を使用して単一のスコープ フィルター グループを追加します。

    | 属性 | 演算子 | 値 |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | User |
    | cloudMastered | NOTEQUAL | True |

    この送信方向の同期規則がどの Azure AD オブジェクトに適用されるかは、スコープ フィルターによって決まります。 この例で使用しているスコープ フィルターは、"Out to Azure AD - User Identity" OOB (out-of-box) 同期規則と同じものです。 そうすることで、オンプレミスの Active Directory から同期されていない**ユーザー** オブジェクトに、同期規則が適用されることを回避します。 スコープ フィルターは、実際の Azure AD Connect のデプロイに応じて調整が必要となる場合があります。

6. **[変換]** タブに移動し、次の変換規則を実装します。

    | フローの種類 | ターゲット属性 | source | 1 度だけ適用する | マージの種類 |
    | --- | --- | --- | --- | --- |
    | 直接 | preferredDataLocation | preferredDataLocation | オフ | 更新 |

7. **[追加]** をクリックして送信方向の規則を作成します。

![送信方向の同期規則の作成のスクリーンショット](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-7-run-full-synchronization-cycle"></a>手順 7:完全同期サイクルを実行する
一般に、完全同期サイクルは必須です。 これは、Active Directory と Azure AD コネクタのスキーマの両方に新しい属性を追加し、カスタム同期規則を導入したためです。 Azure AD にエクスポートする前に、これらの変更を検証します。 完全同期サイクルを手動で構成しながら、次の手順に従って変更を検証することができます。

1. オンプレミスの Active Directory コネクタで、**フル インポート**を実行します。

   1. Synchronization Service Manager の **[操作]** タブに進みます。
   2. **オンプレミスの Active Directory コネクタ**を右クリックし、 **[実行]** を選択します。
   3. ダイアログ ボックスで **[フル インポート]** を選択し、 **[OK]** を選択します。
   4. 操作の完了を待ちます。

      > [!NOTE]
      > インポート対象の属性の一覧にソース属性が既に含まれている場合は、オンプレミスの Active Directory コネクタでのフル インポートは省略できます。 つまり、この記事の前半の手順 2 で、何も変更する必要がなかった場合です。

2. Azure AD コネクタで、**フル インポート**を実行します。

   1. **Azure AD コネクタ**を右クリックし、 **[実行]** を選択します。
   2. ダイアログ ボックスで **[フル インポート]** を選択し、 **[OK]** を選択します。
   3. 操作の完了を待ちます。

3. 既存の**ユーザー** オブジェクトに対する同期規則の変更を検証します。

   オンプレミスの Active Directory からのソース属性と、Azure AD からの **preferredDataLocation** が、それぞれのコネクタ スペースにインポートされています。 完全同期の手順を進める前に、オンプレミスの Active Directory のコネクタ スペースにおける既存の**ユーザー** オブジェクトで、プレビューを実行します。 選択したオブジェクトのソース属性に値が設定されている必要があります。 プレビューが成功し、**preferredDataLocation** がメタバースで設定されていれば、同期規則が正しく構成されていると考えられます。 プレビューを実行する方法については、「[変更を確認する](how-to-connect-sync-change-the-configuration.md#verify-the-change)」を参照してください。

4. オンプレミスの Active Directory コネクタで、**完全同期**を実行します。

   1. **オンプレミスの Active Directory コネクタ**を右クリックし、 **[実行]** を選択します。
   2. ダイアログ ボックスで **[完全同期]** を選択し、 **[OK]** を選択します。
   3. 操作の完了を待ちます。

5. Azure AD に対する**保留中のエクスポート**を確認します。

   1. **Azure AD コネクタ**を右クリックし、 **[コネクタ スペースの検索]** を選択します。
   2. **[コネクタ スペースの検索]** ダイアログ ボックスで、次の手順を実行します。

        a. **[スコープ]** を **[Pending Export]\(保留中のエクスポート\)** に設定します。<br>
        b. **[追加]、[変更]、[削除]** を含む、3 つのすべてのチェック ボックスを選択します。<br>
        c. 変更がエクスポートされるオブジェクトを一覧表示するには、 **[検索]** を選択します。 指定したオブジェクトの変更を検証するには、オブジェクトをダブルクリックします。<br>
        d. 変更が正しいことを確認します。

6. **Azure AD コネクタ**で **[エクスポート]** を実行します。

   1. **Azure AD コネクタ**を右クリックし、 **[実行]** を選択します。
   2. **[コネクタの実行]** ダイアログ ボックスで、 **[エクスポート]** を選択し、 **[OK]** を選択します。
   3. 操作の完了を待ちます。

> [!NOTE]
> この手順には、Azure AD コネクタでの完全同期の手順、または Active Directory コネクタでのエクスポートの手順が含まれていません。 これらの手順は必要ありません。なぜなら、属性値の流れが、オンプレミスの Active Directory から Azure AD への一方向であるためです。

## <a name="step-8-re-enable-sync-scheduler"></a>手順 8:同期スケジューラを再度有効にする
次の手順で組み込みの同期スケジューラを再度有効にします。

1. PowerShell セッションを開始します。
2. `Set-ADSyncScheduler -SyncCycleEnabled $true` コマンドレットを実行することで、スケジュールされた同期を再度有効にします。

## <a name="step-9-verify-the-result"></a>手順 9:結果を確認する
次に、構成を確認し、ユーザーに対して有効にします。

1. ユーザーに対して選択した属性に geo を追加します。 使用できる geo の一覧については、こちらの表を参照してください。  
![ユーザーに追加される AD 属性のスクリーンショット](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. 属性が Azure AD に同期されるまで待ちます。
3. Exchange Online PowerShell を使用して、メールボックスのリージョンが適切に設定されていることを確認します。  
![Exchange Online PowerShell のスクリーンショット](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
テナントがこの機能を使用できるように設定されていることを前提として、メールボックスが適切な geo に移動されます。 これは、メールボックスが配置されているサーバー名を調べることで確認できます。

## <a name="next-steps"></a>次のステップ

Office 365 での Multi-Geo の詳細

* [Ignite での Multi-Geo セッション](https://aka.ms/MultiGeoIgnite)
* [OneDrive における Multi-Geo](https://aka.ms/OneDriveMultiGeo)
* [SharePoint Online における Multi-Geo](https://aka.ms/SharePointMultiGeo)

同期エンジンの構成モデルの詳細:

* この構成モデルについて詳しくは、「 [Understanding Declarative Provisioning (宣言型のプロビジョニングについて)](concept-azure-ad-connect-sync-declarative-provisioning.md)」をご覧ください。
* 式言語について詳しくは、「 [宣言型のプロビジョニングの式について](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)」をご覧ください。

概要トピック:

* [Azure AD Connect 同期:同期を理解してカスタマイズする](how-to-connect-sync-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)
