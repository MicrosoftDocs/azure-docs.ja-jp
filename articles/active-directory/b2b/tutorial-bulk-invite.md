---
title: B2B コラボレーション ユーザーを一括で招待するためのチュートリアル - Azure Active Directory | Microsoft Docs
description: このチュートリアルでは、PowerShell と CSV ファイルを使用して、外部の Azure AD B2B コラボレーション ユーザーに招待状を一括送信する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 9/19/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec1a6ea8f363f2ddd4a9568700d5bff3330443c0
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2019
ms.locfileid: "71128719"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users-preview"></a>チュートリアル:Azure AD B2B コラボレーション ユーザーを一括で招待する (プレビュー)

|     |
| --- |
| この記事では、Azure Active Directory のパブリック プレビュー機能について説明します。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。|
|     |


Azure Active Directory (Azure AD) B2B コラボレーションを使用して外部パートナーと協力する場合は、複数のゲスト ユーザーを組織に同時に招待できます。 このチュートリアルでは、Azure portal を使用して、外部ユーザーに招待状を一括送信する方法について説明します。 具体的には、以下を実行します。

> [!div class="checklist"]
> * **ユーザー一括招待 (プレビュー)** を使用して、ユーザー情報と招待状のユーザー設定を含むコンマ区切り値 (.csv) ファイルを準備する
> * Azure AD に .csv ファイルをアップロードする
> * ユーザーがディレクトリに追加されたことを確認する

Azure Active Directory をお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。 

## <a name="prerequisites"></a>前提条件

招待状の送信先となる、複数のテスト用の電子メール アカウントが必要です。 このアカウントは、組織外にある必要があります。 gmail.com や outlook.com のアドレスなどのソーシャル アカウントを含む任意の種類のアカウントを使用できます。

## <a name="invite-guest-users-in-bulk"></a>ゲスト ユーザーを一括招待する

1. 組織のユーザー管理者アカウントで、Azure portal にサインインします。
2. ナビゲーション ペインで、 **[Azure Active Directory]** を選択します。
3. **[管理]** の **[ユーザー]**  >  **[一括招待]** を選択します。
4. **[ユーザー一括招待 (プレビュー)]** ページで、 **[ダウンロード]** を選択して、招待のプロパティを含んだ有効な .csv ファイルを取得します。

    ![一括招待のダウンロード ボタン](media/tutorial-bulk-invite/bulk-invite-button.png)

5. この .csv ファイルを開いて、ゲスト ユーザーごとに 1 行追加します。 必要な値は次のとおりです。

   * **招待するメール アドレス** - 招待が送信されるユーザー

   * **リダイレクト URL** - 招待されたユーザーが招待を承認した後に転送される URL

    ![ゲスト ユーザーが入力されている CSV ファイルの例](media/tutorial-bulk-invite/bulk-invite-csv.png)

   > [!NOTE]
   > **[カスタマイズされた招待メッセージ]** にはコンマを使用しないでください。メッセージが正しく解析されなるためです。

6. ファイルを保存します。
7. **[ユーザー一括招待 (プレビュー)]** ページの **[csv ファイルをアップロードします]** で、そのファイルを参照します。 ファイルを選択すると、.csv ファイルの検証が開始されます。 
8. ファイルの内容が検証されると、"**ファイルが正常にアップロードされました**" と表示されます。 エラーが存在する場合は、ジョブを送信する前にそれらを修正する必要があります。
9. ファイルが検証に合格したら、 **[送信]** を選択して、招待を追加する Azure の一括操作を開始します。 
10. ジョブの状態を表示するには、 **[各操作の状態を表示するには、ここをクリックします]** を選択します。 または、 **[アクティビティ]** セクションの **[一括操作の結果 (プレビュー)]** を選択します。 一括操作に含まれる各行の項目の詳細については、 **[成功数]** 、 **[失敗数]** 、 **[要求数合計]** の各列の値を選択してください。 エラーが発生した場合、その理由が表示されます。

    ![一括操作の結果の例](media/tutorial-bulk-invite/bulk-operation-results.png)

11. ジョブが完了すると、一括操作が成功したという通知が表示されます。

## <a name="verify-guest-users-in-the-directory"></a>ディレクトリのゲスト ユーザーを確認する

追加したゲスト ユーザーがディレクトリに存在することを Azure portal または PowerShell で確認します。

### <a name="view-guest-users-in-the-azure-portal"></a>Azure portal でゲスト ユーザーを表示する

1. 組織のユーザー管理者アカウントで、Azure portal にサインインします。
2. ナビゲーション ペインで、 **[Azure Active Directory]** を選択します。
3. **[管理]** にある **[ユーザー]** を選択します。
4. **[表示]** の **[ゲスト ユーザーのみ]** を選択して、追加したユーザーが一覧表示されていることを確認します。

### <a name="view-guest-users-with-powershell"></a>PowerShell を使用してゲスト ユーザーを表示する

次のコマンドを実行します。

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

招待したユーザーが表示されていることを確認します。*emailaddress*#EXT#\@*domain* 形式のユーザー プリンシパル名 (UPN) になっています。 たとえば、*lstokes_fabrikam.com#EXT#\@contoso.onmicrosoft.com* では、contoso.onmicrosoft.com が招待状を送信した組織になります。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

ディレクトリ内のテスト用ユーザー アカウントは、不要になったら、Azure portal の [ユーザー] ページで削除できます。それには、ゲスト ユーザーの横のチェック ボックスをオンにし、 **[削除]** を選択してください。 

または、次の PowerShell コマンドを実行してユーザー アカウントを削除することもできます。

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```

次に例を示します。`Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`

## <a name="next-steps"></a>次の手順

このチュートリアルでは、組織の外部のゲスト ユーザーに招待状を一括送信しました。 次に、招待の受諾プロセスを理解します。

> [!div class="nextstepaction"]
> [Azure AD B2B コラボレーションの招待の受諾プロセスを確認する](redemption-experience.md)
