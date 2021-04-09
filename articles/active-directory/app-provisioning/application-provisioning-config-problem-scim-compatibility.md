---
title: System for Cross-Domain Identity Management (SCIM) 2.0 プロトコル準拠に関する既知の問題 -Azure AD
description: SCIM 2.0 をサポートするギャラリー以外のアプリケーションを Azure AD に追加する際に発生する、一般的なプロトコル互換性の問題を解決する方法
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 08/05/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: d13629b4cb05995b9652e862f769a0ffcae30a8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99256900"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Azure AD ユーザー プロビジョニング サービスの SCIM 2.0 プロトコルへのコンプライアンスに関する既知の問題と解決策

Azure Active Directory (Azure AD) では、[Cross-Domain Identity Management (SCIM) 2.0 プロトコル仕様](https://tools.ietf.org/html/draft-ietf-scim-api-19)のインターフェイスを持つ Web サービスによってアクセスされる任意のアプリケーションまたはシステムに、ユーザーやグループを自動的にプロビジョニングできます。 

Azure AD による SCIM 2.0 プロトコルのサポートについては、「[System for Cross-Domain Identity Management (SCIM) を使用して Azure Active Directory からユーザーとグループをアプリケーションに自動的にプロビジョニングする](use-scim-to-provision-users-and-groups.md)」を参照してください。この記事では、Azure AD から、SCIM 2.0 をサポートするアプリケーションに、ユーザーとグループを自動的にプロビジョニングするために実装するプロトコルの特定の部分がリストされています。

この記事では、Azure AD ユーザー プロビジョニング サービスの SCIM 2.0 プロトコルへの準拠に関する現在および過去の問題と、これらの問題を回避する方法について説明されています。

## <a name="understanding-the-provisioning-job"></a>プロビジョニング ジョブについて
プロビジョニング サービスでは、ジョブの概念を使用して、アプリケーションに対して操作を行います。 JobID は[進行状況バー](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar)で確認できます。 すべての新しいプロビジョニング アプリケーションは、"scim" から始まる jobID を使用して作成されます。 scim ジョブはサービスの現在の状態を表します。 以前のジョブの ID は "customappsso" です。 このジョブは、2018 年のサービスの状態を表します。 

ギャラリーでアプリケーションを使用している場合、ジョブには通常、アプリの名前 (たとえば、zoom snowFlake、dataBricks など) が含まれます。 ギャラリー アプリケーションを使用する場合は、このドキュメントをスキップできます。 これは主に、jobID が SCIM または customAppSSO の、ギャラリー以外のアプリケーションに適用されます。

## <a name="scim-20-compliance-issues-and-status"></a>SCIM 2.0 へのコンプライアンスに関する問題と状態
次の表で修正済みとしてマークされている項目は、SCIM ジョブで適切な動作を見つけることができることを意味します。 Microsoft は、加えた変更の下位互換性を確保するように取り組んでいます。 ただし、以前の動作を実装することはお勧めしません。 新しい実装用の新しい動作を使用し、既存の実装を更新することをお勧めします。

> [!NOTE]
> 2018 年に行われた変更については、customappsso の動作に戻すことができます。 2018 年以降に加えられた変更については、URL を使用して以前の動作に戻すことができます。 Microsoft では、以前の jobID に戻せるようにするか、フラグを使用して、Microsoft が加えた変更の下位互換性を確保するようにしました。 ただし、上記のとおり、以前の動作を実装することはお勧めしません。 新しい実装用の新しい動作を使用し、既存の実装を更新することをお勧めします。

| **SCIM 2.0 へのコンプライアンスに関する問題** |  **修正済みである** | **修正日付**  |  **下位互換性** |
|---|---|---|
| Azure AD で、"/scim" がアプリケーションの SCIM エンドポイント URL のルート内にある必要がある  | はい  |  2018 年 12 月 18 日 | customappSSO にダウングレード |
| 拡張属性で、属性名の前にコロン ":" 表記ではなくドット "." 表記が使用されている |  はい  | 2018 年 12 月 18 日  | customappSSO にダウングレード |
| 複数値属性のパッチ要求に無効なパス フィルター構文が含まれている | はい  |  2018 年 12 月 18 日  | customappSSO にダウングレード |
| グループの作成要求に無効なスキーマ URI が含まれている | はい  |  2018 年 12 月 18 日  |  customappSSO にダウングレード |
| PATCH の動作を更新してコンプライアンスを確保する (例: ブール値としての active、適切なグループ メンバーシップの削除) | No | TBD| プレビュー フラグを使用する |

## <a name="flags-to-alter-the-scim-behavior"></a>SCIM の動作を変更するフラグ
既定の SCIM クライアントの動作を変更するには、アプリケーションのテナント URL で以下のフラグを使用します。

:::image type="content" source="media/application-provisioning-config-problem-scim-compatibility/scim-flags.jpg" alt-text="後の動作への SCIM フラグ。":::

* 次の URL を使用して PATCH の動作を更新し、SCIM コンプライアンスを確保します (たとえば、ブール値としての active、適切なグループ メンバーシップの削除)。 この動作は、現在、フラグを使用している場合にのみ使用できますが、今後数か月以内に既定の動作になる予定です。 このプレビュー フラグは現在、オンデマンド プロビジョニングでは機能しません。 
  * **URL (SCIM 準拠):** AzureAdScimPatch062020
  * **SCIM RFC 参照:** 
    * https://tools.ietf.org/html/rfc7644#section-3.5.2
  * **動作:**
  ```json
   PATCH https://[...]/Groups/ac56b4e5-e079-46d0-810e-85ddbd223b09
   {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "remove",
            "path": "members[value eq \"16b083c0-f1e8-4544-b6ee-27a28dc98761\"]"
        }
    ]
   }

    PATCH https://[...]/Groups/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "add",
            "path": "members",
            "value": [
                {
                    "value": "10263a6910a84ef9a581dd9b8dcc0eae"
                }
            ]
        }
    ]
    } 

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "replace",
            "path": "emails[type eq \"work\"].value",
            "value": "someone@contoso.com"
        },
        {
            "op": "replace",
            "path": "emails[type eq \"work\"].primary",
            "value": true
        },
        {
            "op": "replace",
            "value": {
                "active": false,
                "userName": "someone"
            }
        }
    ]
    }

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "replace",
            "path": "active",
            "value": false
        }
    ]
    }

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "add",
            "path": "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department",
            "value": "Tech Infrastructure"
        }
    ]
    }
   
  ```

  * **ダウングレード URL:** ギャラリー以外のアプリケーションで新しい SCIM 準拠の動作が既定になったら、次の URL を使用して、SCIM に準拠していない以前の動作にロールバックすることができます。AzureAdScimPatch2017
  


## <a name="upgrading-from-the-older-customappsso-job-to-the-scim-job"></a>前の customappsso ジョブから SCIM ジョブへのアップグレード
次の手順に従うと、既存の customappsso ジョブが削除され、新しい scim ジョブが作成されます。 
 
1. Azure portal (https://portal.azure.com ) にサインインします。
2. Azure portal の **[Azure Active Directory] > [エンタープライズ アプリケーション]** セクションで、既存の SCIM アプリケーションを検索して選択します。
3. 既存 SCIM アプリの **[プロパティ]** セクションで、 **[オブジェクト ID]** をコピーします。
4. 新しい Web ブラウザー ウィンドウで https://developer.microsoft.com/graph/graph-explorer に移動し、アプリの追加先の Azure AD テナントの管理者としてサインインします。
5. Graph エクスプローラーで次のコマンドを実行して、プロビジョニング ジョブの ID を確認します。 "[object-id]" を、手順 3 でコピーしたサービス プリンシパル ID (オブジェクト ID) に置き換えます。
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![ジョブを取得する](media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "ジョブを取得する") 


6. 結果内で、"customappsso" または "scim" のいずれかで始まる完全な "ID" 文字列をコピーします。
7. バックアップを作成するために、次のコマンドを実行して属性マッピング構成を取得します。 前と同じ [object-id] を使用し、[job-id] を、最後の手順でコピーしたプロビジョニング ジョブ ID に置き換えます。
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![スキーマを取得する](media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "スキーマを取得する") 

8. 最後の手順から JSON 出力をコピーし、テキスト ファイルに保存します。 この JSON には、前のアプリに追加したすべてのカスタム属性マッピングが含まれており、およそ数千行の JSON となります。
9. 次のコマンドを実行して、プロビジョニング ジョブを削除します。
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. 次のコマンドを実行して、最新のサービス修正プログラムを含む新しいプロビジョニング ジョブを作成します。

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. 最後の手順の結果内で、"scim" で始まる完全な "ID" 文字列をコピーします。 必要に応じて、次のコマンドで、[new-job-id] をコピーした新しいジョブ ID に置き換え、要求本文として手順 7 の JSON 出力を入力してコマンドを実行し、前の属性マッピングを再適用します。

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. 最初の Web ブラウザー ウィンドウに戻り、アプリケーション用の **[プロビジョニング]** タブを選択します。
13. 構成を確認し、プロビジョニング ジョブを開始します。 

## <a name="downgrading-from-the-scim-job-to-the-customappsso-job-not-recommended"></a>SCIM ジョブから customappsso ジョブへのダウングレード (非推奨)
 以前の動作にダウングレードできますが、これはお勧めしません。これは、customappsso が一部の更新を利用できず、サポートが無期限にとならない可能があるためです。 

1. Azure portal (https://portal.azure.com ) にサインインします。
2. Azure portal の **[Azure Active Directory] > [エンタープライズ アプリケーション] > [アプリケーションの作成]** セクションで、**ギャラリー以外の** 新しいアプリケーションを作成します。
3. 新しいカスタム アプリの **[プロパティ]** セクションで、 **[オブジェクト ID]** をコピーします。
4. 新しい Web ブラウザー ウィンドウで https://developer.microsoft.com/graph/graph-explorer に移動し、アプリの追加先の Azure AD テナントの管理者としてサインインします。
5. Graph エクスプローラーで次のコマンドを実行して、アプリのプロビジョニング構成を初期化します。
   "[object-id]" を、手順 3 でコピーしたサービス プリンシパル ID (オブジェクト ID) に置き換えます。

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. 最初の Web ブラウザー ウィンドウに戻り、アプリケーション用の **[プロビジョニング]** タブを選択します。
7. 通常どおりにユーザー プロビジョニング構成を完了します。


## <a name="next-steps"></a>次のステップ
[SaaS アプリケーションへのプロビジョニングとプロビジョニング解除の詳細](user-provisioning.md)
