---
title: Azure AD ユーザー プロビジョニング サービスの SCIM 2.0 プロトコルへのコンプライアンスに関する既知の問題と解決策 |Microsoft Docs
description: SCIM 2.0 をサポートするギャラリー以外のアプリケーションを Azure AD に追加する際に発生する、一般的なプロトコル互換性の問題を解決する方法
services: active-directory
documentationcenter: ''
author: asmalser
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: asmalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2a2c1c415d0862b2631fa749241a9ae07df3b98
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58880153"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Azure AD ユーザー プロビジョニング サービスの SCIM 2.0 プロトコルへのコンプライアンスに関する既知の問題と解決策

Azure Active Directory (Azure AD) では、[Cross-Domain Identity Management (SCIM) 2.0 プロトコル仕様](https://tools.ietf.org/html/draft-ietf-scim-api-19)のインターフェイスを持つ Web サービスによってアクセスされる任意のアプリケーションまたはシステムに、ユーザーやグループを自動的にプロビジョニングできます。 

Azure AD による SCIM 2.0 プロトコルのサポートについては、「[System for Cross-Domain Identity Management (SCIM) を使用して Azure Active Directory からユーザーとグループをアプリケーションに自動的にプロビジョニングする](use-scim-to-provision-users-and-groups.md)」を参照してください。この記事では、Azure AD から、SCIM 2.0 をサポートするアプリケーションに、ユーザーとグループを自動的にプロビジョニングするために実装するプロトコルの特定の部分がリストされています。

この記事では、Azure AD ユーザー プロビジョニング サービスの SCIM 2.0 プロトコルへの準拠に関する現在および過去の問題と、これらの問題を回避する方法について説明されています。

> [!IMPORTANT]
> Azure AD ユーザー プロビジョニング サービス SCIM クライアントへの最新の更新は、2018 年 12 月 18 日に実行されました。 この更新では、次の表に示す既知の互換性問題に対処しました。 この更新の詳細については、以下のよく寄せられる質問を参照してください。

## <a name="scim-20-compliance-issues-and-status"></a>SCIM 2.0 へのコンプライアンスに関する問題と状態

| **SCIM 2.0 へのコンプライアンスに関する問題** |  **修正済み: ** | **修正日付**  |  
|---|---|---|
| Azure AD で、"/scim" がアプリケーションの SCIM エンドポイント URL のルート内にある必要がある  | はい  |  2018 年 12 月 18 日 | 
| 拡張属性で、属性名の前にコロン ":" 表記ではなくドット "." 表記が使用されている |  はい  | 2018 年 12 月 18 日  | 
|  複数値属性のパッチ要求に無効なパス フィルター構文が含まれている | はい  |  2018 年 12 月 18 日  | 
|  グループの作成要求に無効なスキーマ URI が含まれている | はい  |  2018 年 12 月 18 日  |  

## <a name="were-the-services-fixes-described-automatically-applied-to-my-pre-existing-scim-app"></a>説明されているサービス修正プログラムは、既存の SCIM アプリに自動的に適用されていますか。

いいえ。 これらの変更は、以前の動作を使用するようコード化された SCIM アプリに重大な変更をもたらすため、既存のアプリに自動的に適用されることはありません。

これらの変更は、修正日付後に、Azure portal で構成されたすべての新しい[ギャラリー以外の SCIM アプリ](configure-single-sign-on-non-gallery-applications.md)に適用されます。

最新の修正プログラムを含めるように既存ユーザー プロビジョニング ジョブを移行する方法については、次のセクションを参照してください。

## <a name="can-i-migrate-an-existing-scim-based-user-provisioning-job-to-include-the-latest-service-fixes"></a>最新のサービス修正プログラムを含めるように既存の SCIM ベースのユーザー プロビジョニング ジョブを移行できますか。

はい。 シングル サインオンにこのアプリケーション インスタンスを既に使用していて、最新の修正プログラムを含めるように既存のプロビジョニング ジョブを移行する必要がある場合は、次の手順に従います。 この手順では、Microsoft Graph API および Microsoft Graph API エクスプローラーを使用して、既存の SCIM のアプリから古いプロビジョニング ジョブを削除し、新しい動作を使用する新しいジョブを作成する方法について説明します。

> [!NOTE]
> アプリケーションがまだ開発段階で、シングル サインオンまたはユーザー プロビジョニングのどちらかにまだデプロイされていない場合、最も簡単なソリューションは、Azure portal の **[Azure Active Directory] > [エンタープライズ アプリケーション]** セクションでアプリケーション エントリを削除し、**[アプリケーションの作成] > [Non-gallery]\(ギャラリー以外\)** オプションを使用して、アプリケーションの新しいエントリを単に追加します。 これは、次に示す手順を行う代わりになるものです。
 
1. Azure portal (https://portal.azure.com) にサインインします。
2. Azure portal の **[Azure Active Directory] > [エンタープライズ アプリケーション]** セクションで、既存の SCIM アプリケーションを検索して選択します。
3. 既存 SCIM アプリの **[プロパティ]** セクションで、**[オブジェクト ID]** をコピーします。
4. 新しい Web ブラウザー ウィンドウで https://developer.microsoft.com/graph/graph-explorer に移動し、アプリの追加先の Azure AD テナントの管理者としてサインインします。
5. Graph エクスプローラーで次のコマンドを実行して、プロビジョニング ジョブの ID を確認します。 "[object-id]" を、手順 3 でコピーしたサービス プリンシパル ID (オブジェクト ID) に置き換えます。
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![ジョブの取得](./media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "ジョブの取得") 


6. 結果内で、"customappsso" または "scim" のいずれかで始まる完全な "ID" 文字列をコピーします。
7. バックアップを作成するために、次のコマンドを実行して属性マッピング構成を取得します。 前と同じ [object-id] を使用し、[job-id] を、最後の手順でコピーしたプロビジョニング ジョブ ID に置き換えます。
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![スキーマの取得](./media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "スキーマの取得") 

8. 最後の手順から JSON 出力をコピーし、テキスト ファイルに保存します。 これには、古いアプリに追加したすべてのカスタム属性マッピングが含まれており、およそ数千行の JSON となります。
9. 次のコマンドを実行して、プロビジョニング ジョブを削除します。
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. 次のコマンドを実行して、最新のサービス修正プログラムを含む新しいプロビジョニング ジョブを作成します。

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. 最後の手順の結果内で、"scim" で始まる完全な "ID" 文字列をコピーします。 必要に応じて、[new-job-id] を、先ほどコピーした新しいジョブ ID に置き換え、要求本文として手順 7 の JSON 出力を入力して、次のコマンドを実行して、古い属性マッピングを再適用します。

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. 最初の Web ブラウザー ウィンドウに戻り、アプリケーション用の **[プロビジョニング]** タブを選択します。
13. 構成を確認し、プロビジョニング ジョブを開始します。 

## <a name="can-i-add-a-new-non-gallery-app-that-has-the-old-user-provisioning-behavior"></a>古いユーザー プロビジョニング動作を使用する、ギャラリー以外の新しいアプリを追加することはできますか。

はい。 修正よりも前に存在していた古い動作用にアプリケーションをコード化していて、その新しいインスタンスをデプロイする必要がある場合は、次の手順に従います。 この手順では、Microsoft Graph API および Microsoft Graph API エクスプローラーを使用して、古い動作を使用する SCIM プロビジョニング ジョブを作成する方法について説明します。
 
1. Azure portal (https://portal.azure.com) にサインインします。
2. Azure portal の **[Azure Active Directory] > [エンタープライズ アプリケーション] > [アプリケーションの作成]** セクションで、**ギャラリー以外の**新しいアプリケーションを作成します。
3. 新しいカスタム アプリの **[プロパティ]** セクションで、**[オブジェクト ID]** をコピーします。
4. 新しい Web ブラウザー ウィンドウで https://developer.microsoft.com/graph/graph-explorer に移動し、アプリの追加先の Azure AD テナントの管理者としてサインインします。
5. Graph エクスプローラーで次のコマンドを実行して、アプリのプロビジョニング構成を初期化します。
   "[object-id]" を、手順 3 でコピーしたサービス プリンシパル ID (オブジェクト ID) に置き換えます。

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. 最初の Web ブラウザー ウィンドウに戻り、アプリケーション用の **[プロビジョニング]** タブを選択します。
7. 通常どおりにユーザー プロビジョニング構成を完了します。


## <a name="next-steps"></a>次の手順
[SaaS アプリケーションへのプロビジョニングとプロビジョニング解除の詳細](user-provisioning.md)

