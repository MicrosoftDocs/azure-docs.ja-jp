---
title: Azure AD Connect クラウド同期の属性マッピング
description: この記事では、Azure AD Connect のクラウド同期機能を使用して属性をマップする方法について説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdb043374cf6252da3929c8f0cda6c0a4be558b7
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555212"
---
# <a name="attribute-mapping-in-azure-ad-connect-cloud-sync"></a>Azure AD Connect クラウド同期の属性マッピング

Azure Active Directory (Azure AD) Connect のクラウド同期機能を使用して、オンプレミスのユーザーまたはグループ オブジェクトと Azure AD 内のオブジェクトの間で属性をマップすることができます。 この機能は、クラウドの同期構成に追加されました。

既定の属性マッピングをビジネスのニーズに合わせてカスタマイズ (変更、削除、または作成) できます。 同期される属性の一覧については、「[Azure Active Directory に同期される属性](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)」を参照してください。

## <a name="understand-types-of-attribute-mapping"></a>属性マッピングの種類について
属性マッピングを使用して、Azure AD 内で属性を設定する方法を制御します。 Azure AD では、次の 4 種類のマッピングがサポートされています。

- **直接**: ターゲットの属性に、Active Directory 内でリンクされているオブジェクトの属性値を設定します。
- **定数**: ターゲットの属性に、指定した特定の文字列を設定します。
- **式**: ターゲットの属性を、スクリプトのような式の結果に基づいて設定します。 詳細については、「[Azure Active Directory における属性マッピングの式の書き方](reference-expressions.md)」を参照してください。
- **なし**: ターゲットの属性を変更しません。 ただし、ターゲットの属性が空の場合は、指定した既定値が設定されます。

これらの基本的な種類とともに、カスタム属性マッピングではオプションの *既定* 値の割り当てという概念をサポートします。 既定値の割り当てでは、Azure AD またはターゲット オブジェクトに値がない場合にも、ターゲットの属性には必ず値が設定されます。 最も一般的な構成では、これを空白のままにします。

## <a name="understand-properties-of-attribute-mapping"></a>属性マッピングのプロパティについて

属性マッピングは、この型プロパティと共に次の属性もサポートしています。

- **ソース属性**: ソース システムのユーザー属性 (例:Active Directory)。
- **対象の属性**: ターゲット システムのユーザー属性 (例:Azure Active Directory)。
- **null の場合の既定値 (オプション)** : ソース属性が null の場合にターゲット システムに渡される値。 この値は、ユーザーを作成するときにのみプロビジョニングされます。 既存のユーザーを更新するときにプロビジョニングされることはありません。  
- **このマッピングを適用する**:
  - **常に**: このマッピングをユーザーの作成と更新の両方のアクションに適用します。
  - **作成中のみ**: このマッピングをユーザーの作成アクションのみに適用します。

> [!NOTE]
> この記事では、Azure portal を使用して属性をマップする方法について説明します。  Microsoft Graph の使用方法については、「[変換](how-to-transformation.md)」を参照してください。

## <a name="add-an-attribute-mapping"></a>属性マッピングを追加する

新しい機能を使用するには、次の手順を実行します。

1.  Azure Portal で、 **[Azure Active Directory]** を選びます。
2.  **[Azure AD Connect]** を選びます。
3.  **[Manage cloud sync]\(クラウド同期の管理\)** を選択します。

    ![クラウドへの同期を管理するリンクを示すスクリーンショット。](media/how-to-install/install-6.png)

4. **[構成]** の下で、自分の構成を選択します。
5. **[クリックしてマッピングを編集]** を選択します。  このリンクにより、 **[属性マッピング]** 画面が開きます。

    ![属性を追加するためのリンクを示すスクリーンショット。](media/how-to-attribute-mapping/mapping-6.png)

6.  **[属性の追加]** を選択します。

    ![属性を追加するためのボタンと、属性およびマッピングの種類の一覧を示すスクリーンショット。](media/how-to-attribute-mapping/mapping-1.png)

7. マッピングの種類を選択します。 この例では、 **[式]** を使用します。
8. ボックスに式を入力します。 この例では、`Replace([mail], "@contoso.com", , ,"", ,)` を使用します。
9. ターゲット属性を入力します。 この例では、「**ExtensionAttribute15**」を使用します。
10. このマッピングを適用するタイミングを選択して、 **[適用]** を選択します。

    ![属性マッピングを作成するための入力済みボックスを示すスクリーンショット。](media/how-to-attribute-mapping/mapping-2a.png)

11. **[属性マッピング]** 画面に戻ると、新しい属性マッピングが表示されます。  
12. **[スキーマの保存]** を選択します。

    ![[スキーマの保存] ボタンを示すスクリーンショット。](media/how-to-attribute-mapping/mapping-3.png)

## <a name="test-your-attribute-mapping"></a>属性マッピングをテストする

属性マッピングをテストするには、[オンデマンドのプロビジョニング](how-to-on-demand-provision.md)を使用できます。 

1. Azure Portal で、 **[Azure Active Directory]** を選びます。
2. **[Azure AD Connect]** を選びます。
3. **[プロビジョニングの管理]** を選択します。
4. **[構成]** の下で、自分の構成を選択します。
5. **[検証]** で、 **[ユーザーのプロビジョニング]** ボタンを選択します。 
6. **[オンデマンドでプロビジョニング]** 画面で、ユーザーまたはグループの識別名を入力し、 **[プロビジョニング]** ボタンを選択します。 

   プロビジョニングが進行中であることが画面に表示されます。

   ![進行中のプロビジョニングを示すスクリーンショット。](media/how-to-attribute-mapping/mapping-4.png)

8. プロビジョニングが完了すると、4 つの緑色のチェック マークが表示され、成功画面が表示されます。 

   **[アクションの実行]** で **[詳細の表示]** を選択します。 右側を見ると、新しい属性が同期され、式が適用されていることがわかります。

   ![成功とエクスポートの詳細を示すスクリーンショット。](media/how-to-attribute-mapping/mapping-5.png)

## <a name="next-steps"></a>次のステップ

- [Azure AD Connect クラウド同期とは](what-is-cloud-sync.md)
- [属性マッピングの式の書き方](reference-expressions.md)
- [Azure Active Directory に同期される属性](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)
