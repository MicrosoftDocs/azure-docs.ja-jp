---
title: Azure AD Connect で同期していない属性のトラブルシューティング |Microsoft Docs
description: このトピックでは、トラブルシューティング タスクを使用して、属性同期の問題のトラブルシューティングを行う方法の手順を示します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6df1347eab57a6971fe2e39c0a55869c8f23939
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91317489"
---
# <a name="troubleshoot-an-attribute-not-synchronizing-in-azure-ad-connect"></a>Azure AD Connect で同期していない属性のトラブルシューティング

## <a name="recommended-steps"></a>**推奨される手順**

属性同期の問題を調査する前に、**Azure AD Connect** の同期プロセスについて理解しておきましょう。

  ![Azure AD Connect の同期プロセス](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**用語**

* **CS:** コネクタ スペース。データベース内のテーブルです。
* **MV:** メタバース。データベース内のテーブルです。
* **AD:** Active Directory
* **AAD:** Azure Active Directory

### <a name="synchronization-steps"></a>**同期のステップ**

* AD からインポートする:Active Directory オブジェクトが AD CS に取り込まれます。

* AAD からインポートする:Azure Active Directory オブジェクトが AAD CS に取り込まれます。

* 同期:**受信同期規則** と **送信同期規則** は、優先順位の番号の低い方から順に実行されます。 同期規則は、デスクトップ アプリケーションから **同期規則エディター** にアクセスして表示することができます。 **受信同期規則** は、CS から MV にデータを取り込みます。 **送信同期規則** は、MV から CS にデータを移動します。

* AD にエクスポートする:同期の実行後、AD CS から **Active Directory** にオブジェクトがエクスポートされます。

* AAD にエクスポートする:同期の実行後、AAD CS から **Azure Active Directory** にオブジェクトがエクスポートされます。

### <a name="step-by-step-investigation"></a>**ステップ バイ ステップの調査**

* まず、**メタバース** から検索し、ソースからターゲットへの属性マッピングを確認します。

* 下図のように、デスクトップ アプリケーションから **Synchronization Service Manager** を起動します。

  ![Synchronization Service Manager を起動する](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/startmenu.png)

* **Synchronization Service Manager** で、 **[メタバース検索]** 、 **[Scope by Object Type]\(オブジェクトの種類でスコープ\)** の順に選択し、属性を使用してオブジェクトを選び、 **[検索]** ボタンをクリックします。

  ![メタバース検索](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvsearch.png)

* **メタバース** 検索で見つかったオブジェクトをダブルクリックし、その属性をすべて表示します。 **[コネクタ]** タブをクリックすることで、すべての **[コネクタ スペース]** の対応するオブジェクトを確認できます。

  ![メタバース オブジェクトのコネクタ](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvattributes.png)

* **[Active Directory コネクタ]** をダブルクリックし、 **[コネクタ スペース]** 属性を表示します。 以下のダイアログで **[プレビュー]** ボタンをクリックし、 **[Generate Preview]\(プレビューの生成\)** ボタンをクリックします。

  ![[コネクタ スペース オブジェクトのプロパティ] 画面を示すスクリーンショット。[プレビュー] ボタンが強調表示されています。](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/csattributes.png)

* ここで、 **[インポート属性フロー]** をクリックします。これにより、**Active Directory コネクタ スペース** から **メタバース** への属性のフローが表示されます。 **[同期規則]** 列には、その属性に関係する **同期規則** が表示されます。 **[データ ソース]** 列には、**コネクタ スペース** からの属性が表示されます。 **[メタバース属性]** 列には、**メタバース** の属性が表示されます。 ここで同期していない属性を見つけることができます。 ここで属性が見つからない場合、これはマップされておらず、新しいカスタム **同期規則** を作成して、属性をマップする必要があります。

  ![コネクタ スペースの属性](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/cstomvattributeflow.png)

* 左側のウィンドウで **[エクスポート属性フロー]** をクリックし、**アウトバウンド同期規則** を使用して、**メタバース** から **Active Directory コネクタ スペース** に戻る属性フローを表示します。

  ![メタバースから Active Directory コネクタ スペースへ戻る [属性フロー] を示すスクリーンショット。ここでは、送信同期規則が使用されています。](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvtocsattributeflow.png)

* 同様に、**Azure Active Directory コネクタ スペース** オブジェクトを表示し、**プレビュー** を生成して **メタバース** から **コネクタ スペース** への、またその逆の属性フローを表示できます。このようにして、属性が同期していない理由を調査できます。

## <a name="recommended-documents"></a>**推奨されるドキュメント**
* [Azure AD Connect 同期:技術的概念](./how-to-connect-sync-technical-concepts.md)
* [Azure AD Connect 同期:アーキテクチャの概要](./concept-azure-ad-connect-sync-architecture.md)
* [Azure AD Connect 同期:宣言型のプロビジョニングについて](./concept-azure-ad-connect-sync-declarative-provisioning.md)
* [Azure AD Connect 同期:宣言型のプロビジョニングの式について](./concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Azure AD Connect 同期:既定の構成について](./concept-azure-ad-connect-sync-default-configuration.md)
* [Azure AD Connect 同期:ユーザー、グループ、および連絡先について](./concept-azure-ad-connect-sync-user-and-contacts.md)
* [Azure AD Connect 同期:シャドウ属性](./how-to-connect-syncservice-shadow-attributes.md)

## <a name="next-steps"></a>次の手順

- [Azure AD Connect 同期](how-to-connect-sync-whatis.md)。
- [ハイブリッド ID とは](whatis-hybrid-identity.md)。