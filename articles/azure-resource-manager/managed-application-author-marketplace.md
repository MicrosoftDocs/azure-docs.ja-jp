---
title: "Marketplace の Azure マネージ アプリケーション | Microsoft Docs"
description: "Marketplace を介して利用できる Azure マネージ アプリケーションについて説明します。"
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/09/2017
ms.author: gauravbh; tomfitz
ms.openlocfilehash: 58ac7665abf7e75a43bb0b92bdf6f41005c3efe8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-managed-applications-in-the-marketplace"></a>Marketplace の Azure マネージ アプリケーション

 MSP、ISV、およびシステム インテグレーター (SI) は、Azure マネージ アプリケーションを使用して、各自のソリューションを、すべての Azure Marketplace ユーザーに提供できます。 このようなソリューションにより、顧客のメンテナンスとサービスのオーバーヘッドは削減されます。 発行者は Marketplace を通じてインフラストラクチャとソフトウェアを販売できます。 また、サービスと運用サポートをマネージ アプリケーションに付加できます。 詳細については、[マネージ アプリケーションの概要](managed-application-overview.md)に関するページをご覧ください。

この記事では、MSP、ISV、または SI が Marketplace にアプリケーションを発行し、幅広くユーザーに公開されるようにする方法について説明します。

## <a name="prerequisites-for-publishing-a-managed-application"></a>マネージ アプリケーションを発行するための前提条件

Marketplace に掲載するための前提条件:

* 技術

    *  Azure Resource Manager テンプレートの基本的な構造と構文については、[Azure Resource Manager テンプレート](resource-group-authoring-templates.md)に関するページをご覧ください。
    *  完成したテンプレート ソリューションを表示するには、「[Azure クイック スタート テンプレート](https://azure.microsoft.com/en-us/documentation/templates/)」または[クイック スタート テンプレート リポジトリ](https://github.com/azure/azure-quickstart-templates)を参照してください。
    *  Marketplace を通じてアプリケーションをデプロイする顧客向けのインターフェイスを作成する方法については、[ユーザー インターフェイス定義ファイルの作成](managed-application-createuidefinition-overview.md)に関するページをご覧ください。

* 技術面以外 (ビジネス要件)

    *   貴社またはその子会社は、Marketplace によって販売がサポートされる国に所在している必要があります。
    *   Marketplace でサポートされている課金モデルに対応した方法で、製品のライセンスを取得する必要があります。
    *   商取引上合理的な方法で、顧客に技術サポートを提供していただきます。 無償、有償、コミュニティ サポートの活用など、方法は問いません。
    *   貴社のソフトウェアおよび依存関係にあるすべてのサード パーティ ソフトウェアのライセンス供与を行っていただきます。
    *   Marketplace および Azure Portal にプランが掲載されるための条件を満たすコンテンツを提供する必要があります。
    *   Azure Marketplace 参加ポリシーと発行者契約の条項に同意する必要があります。
    *   使用条件、Microsoft のプライバシーに関する声明、および Microsoft Azure 認定プログラム契約に従うことに同意する必要があります。

## <a name="create-a-new-azure-application-offer"></a>新しい Azure アプリケーション プランの作成

前提条件を満たしたら、マネージ アプリケーション プランを作成できます。 プランと SKU の概要を見ておきましょう。

### <a name="offer"></a>プラン

マネージ アプリケーション用のプランは、発行者から提供される製品のクラスに相当します。 Marketplace で入手できるようにしたい新しい種類のソリューション/アプリケーションがある場合は、それを新しいプランとして設定できます。 プランとは、SKU のコレクションです。 各プランは、Marketplace で独自のエンティティとして表示されます。

### <a name="sku"></a>SKU

SKU とは、プランの購入可能な最小単位です。 同じ製品クラス (プラン) 内では、SKU を使用して次を区別できます。

* サポートされる各種機能。
* プランが管理対象か管理対象外か。
* サポートされる課金モデル。

SKU は、Marketplace では親プランの下に表示されます。 Azure Portal では、独自の購入可能エンティティとして表示されます。

### <a name="set-up-an-offer"></a>プランの設定

1. [クラウド パートナー ポータル](https://cloudpartner.azure.com/)にサインインします。

2. 左側のナビゲーション バーで、**[+ 新しいプラン]** > **[Azure Applications]\(Azure アプリケーション\)** を選択します。

    ![新しいプラン](./media/managed-application-author-marketplace/newOffer.png)

3. **[エディター]** ビューの左側に表示されるフォームに入力します。 必須フィールドには赤いアスタリスク (*) が付いています。

    ![プラン設定](./media/managed-application-author-marketplace/newOffer_OfferSettings.png)

    主に 4 つのフォームが、マネージ アプリケーションを作成するときに使用されます。

    a. プラン設定

    b. SKU

    c. Marketplace

    d. サポート

以降のセクションでは、これらのフォームについて詳しく説明します。

## <a name="offer-settings-form"></a>[プランの設定] フォーム
この基本的なフォームを使用して、プランの設定を指定します。

1. **[プランの設定]** フォームに入力します。 各種フィールドは次のとおりです。

    a. **プラン ID**: この一意識別子は、発行元プロファイル内のプランを特定します。 この ID は、製品 URL、Resource Manager テンプレート、課金レポートに表示されます。 小文字の英数字またはハイフン (-) のみで構成できます。 ID の末尾にはダッシュを使用できません。 最大 50 文字に制限されます。 プランの運用が開始されると、このフィールドはロックされます。

    b. **発行元 ID**: このドロップダウン リストを使用して、このプランを発行するための発行元プロファイルを選択します。 プランの運用が開始されると、このフィールドはロックされます。

    c. **名前**: このプランの表示名は、Marketplace とポータルに表示されます。 最大で 50 文字の長さにできます。 製品の覚えやすいブランド名を使用してください。 また、販売方法である場合を除き、ここでは会社名を使用しないでください。 このプランを貴社の Web サイトでマーケティングしている場合は、この名前が、貴社の Web サイトで表示される名前とまったく同じであることを確認します。

2. **[保存]** を選択して進捗状況を保存します。 

## <a name="skus-form"></a>[SKU] フォーム
次の手順では、作成したプラン用に SKU を追加します。

1. **[SKU]** > **[新しい SKU]** を選択します。 

    ![新しい SKU を選択する](./media/managed-application-author-marketplace/newOffer_skus.png)

2. **SKU ID** を入力します。 SKU ID は、プラン内の SKU を表す一意識別子です。 この ID は、製品 URL、Resource Manager テンプレート、課金レポートに表示されます。 小文字の英数字またはハイフン (-) のみで構成できます。 ID の末尾にはダッシュを使用できず、最大 50 文字に制限されます。 プランの運用が開始されると、このフィールドはロックされます。 プラン内の SKU を複数にすることができます。 発行するイメージごとに SKU が必要です。

3. 次のフォームの **[SKU Details]\(SKU の詳細\)** セクションに入力します。

    ![新しい SKU を指定する](./media/managed-application-author-marketplace/newOffer_newsku.png)

    次のフィールドに入力します。
    
    a. **タイトル**: この SKU のタイトルを入力します。 このタイトルは、ギャラリーでこの項目に対して表示されます。

    b. **概要**: この SKU の簡単な概要を入力します。 このテキストは、タイトルの下に表示されます。

    c. **説明**: SKU に関する詳細な説明を入力します。

    d. **SKU の種類**: 使用できる値は、**[Managed Application]\(マネージ アプリケーション\)** と **[ソリューション テンプレート]** です。 この場合は、**[Managed Application]\(マネージ アプリケーション\)** を選択します。

4. 次のフォームの **[パッケージの詳細]** セクションに入力します。

    ![パッケージ](./media/managed-application-author-marketplace/newOffer_newsku_package.png)

    次のフィールドに入力します。

    a. **現在のバージョン**: アップロードするパッケージのバージョンを入力します。 `{number}.{number}.{number}{number}` 形式にする必要があります。

    b. **パッケージ ファイルの選択**: このパッケージには、.zip ファイルに圧縮されている次のファイルが含まれています。
    * **applianceMainTemplate.json**: ソリューション/アプリケーションをデプロイするために使用されるデプロイ テンプレート ファイル。 デプロイ テンプレート ファイルの作成方法については、[初めての Azure Resource Manager テンプレートの作成](resource-manager-create-first-template.md)に関するページをご覧ください。
    * **appliancecreateUIDefinition.json**: このファイルは、このソリューション/アプリケーションのプロビジョニングに使用されるユーザー インターフェイスを生成するときに、Azure Portal によって使用されます。 詳細については、「[CreateUiDefinition の基本概念](managed-application-createuidefinition-overview.md)」を参照してください。
    * **mainTemplate.json**: このテンプレート ファイルには、Microsoft.Solution/appliances リソースのみが含まれます。 mainTemplate ファイルには、次のプロパティが含まれています。

        *  **kind**: Marketplace のマネージ アプリケーションの場合は **Marketplace** を使用してください。
        *  **ManagedResourceGroupId**: 顧客のサブスクリプション内のこのリソース グループには、applianceMainTemplate.json で定義されているすべてのリソースがデプロイされています。
        *  **PublisherPackageId**: この文字列はパッケージを一意に識別します。 `{publisherId}.{OfferId}.{SKUID}.{PackageVersion}` の形式で値を指定します。

次の図に示すように、**Offer ID** と **Publisher ID** を発行ポータルから取得します。

![プラン ID](./media/managed-application-author-marketplace/UniqueString_pubid_offerid.png)
        
次の図に示すように、**SKU ID** を取得します。

![SKU ID](./media/managed-application-author-marketplace/UniqueString_skuid.png)
        
次の図に示すように、パッケージの**バージョン**を取得します。

![パッケージ バージョン](./media/managed-application-author-marketplace/UniqueString_packageversion.png)
    
  以上の例を踏まえると、**PublisherPackageId** の値は `azureappliance-test.ravmanagedapptest.ravpreviewmanagedsku.1.0.0` です。

  サンプルの mainTemplate.json:

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "storageAccountNamePrefix": {
        "type": "string",
        "metadata": {
          "description": "Specify the name of the storage account"
        }
      },
      "storageAccountType": {
        "type": "string"
      }
    },
    "variables": {
      "managedResourceGroup": "[concat(resourceGroup().id,uniquestring(resourceGroup().id))]"
    },
    "resources": [{
      "type": "Microsoft.Solutions/appliances",
      "apiVersion": "2016-09-01-preview",
      "name": "[concat(parameters('storageAccountNamePrefix'), '-', 'managed')]",
      "location": "[resourceGroup().location]",
      "kind": "marketplace",
      "properties": {
        "managedResourceGroupId": "[variables('managedResourceGroup')]",
        "PublisherPackageId":"azureappliancetest.ravmanagedapptest.ravpreviewmanagedsku.1.0.0",
        "parameters": {
          "storageAccountName": {
            "value": "[parameters('storageAccountNamePrefix')]"
          },
          "storageAccountType": {
            "value": "[parameters('storageAccountType')]"
          }
        }
      }
    }],
    "outputs": {

    }
  }
  ```

このパッケージには、このアプリケーションを正常にプロビジョニングするために必要となる、その他の入れ子になったテンプレートまたはスクリプトを含める必要があります。 mainTemplate.json ファイル、applianceMainTemplate.json ファイル、および applianceCreateUIDefinition.json ファイルは、ルート フォルダーに存在する必要があります。

* **Authorizations**: このプロパティは、顧客のサブスクリプション内のリソースにアクセスできる人と、アクセス レベルを定義します。 発行元はこのプロパティを使用して、顧客に代わってアプリケーションを管理できます。
* **PrincipalId** - このプロパティは、顧客のサブスクリプション内のリソースに対して特定のアクセス許可が付与されているユーザー、ユーザー グループ、またはアプリケーション の Azure Active Directory (Azure AD) 識別子です。 ロールの定義では、アクセス許可について説明します。 
* **Role Definition**: このプロパティは、Azure AD によって提供されているロールベースのアクセス制御 (RBAC) の組み込みロールの一覧です。 最も適切なロールを選択して使用することで、顧客に代わってリソースを管理できます。

複数の承認を追加できますが、 AD ユーザー グループを作成し、その ID を **PrincipalId** で指定することをお勧めします。 これにより、SKU を更新せずに、より多くのユーザーをユーザー グループに追加できます。

RBAC の詳細については、[Azure Portal での RBAC の使用](../active-directory/role-based-access-control-what-is.md)に関するページをご覧ください。

## <a name="marketplace-form"></a>[Marketplace] フォーム

[Marketplace] フォームでは、[Azure Marketplace](https://azuremarketplace.microsoft.com) と [Azure Portal](https://portal.azure.com/) に表示するフィールドについて確認します。

### <a name="preview-subscription-ids"></a>サブスクリプション ID をプレビュー

発行されたプランにアクセスできる Azure サブスクリプション ID の一覧を入力します。 このホワイトリストに追加されたサブスクリプションを使用して、プレビューされたプランを運用開始前にテストできます。 パートナー ポータルでは、最大 100 個のサブスクリプションのホワイト リストをコンパイルできます。

### <a name="suggested-categories"></a>推奨されるカテゴリ

プランとの最適な関連付けが可能な最大 5 つのカテゴリを、一覧から選択します。 このカテゴリは、[Azure Marketplace](https://azuremarketplace.microsoft.com) と [Azure Portal](https://portal.azure.com/) で利用可能な製品カテゴリに、プランをマッピングするときに使用されます。

#### <a name="azure-marketplace"></a>Azure Marketplace

マネージ アプリケーションの概要には、次のフィールドが表示されます。

![Marketplace の概要](./media/managed-application-author-marketplace/publishvm10.png)

マネージ アプリケーションの **[概要]** タブには、次のフィールドが表示されます。

![Marketplace の概要](./media/managed-application-author-marketplace/publishvm11.png)

マネージ アプリケーションの **[プランと価格]** タブには、次のフィールドが表示されます。

![Marketplace のプラン](./media/managed-application-author-marketplace/publishvm15.png)

#### <a name="azure-portal"></a>Azure ポータル

マネージ アプリケーションの概要には、次のフィールドが表示されます。

![ポータルの概要](./media/managed-application-author-marketplace/publishvm12.png)

マネージ アプリケーションの概要には、次のフィールドが表示されます。

![ポータルの概要](./media/managed-application-author-marketplace/publishvm13.png)

#### <a name="logo-guidelines"></a>ロゴのガイドライン

クラウド パートナー ポータルにアップロードするロゴについては、必ず以下のガイドラインに従ってください。

*   Azure の設計には簡単なカラー パレットがあります。 ロゴのプライマリ カラーとセカンダリ カラーの数は制限してください。
*   ポータルのテーマの色は白黒です。 この色を、ロゴの背景色として使用しないでください。 ポータルでロゴが目立つ色を使用します。 背景色としてはシンプルなプライマリ カラーをお勧めします。 "*透明な背景を使用している場合は、ロゴおよびテキストが白、黒、または青ではないことを確認します。*"
*   ロゴではグラデーションの背景を使用しないでください。
*   ロゴにはテキストを使用しないでください (会社またはブランドの名前であっても)。 ロゴのルック アンド フィールはフラットにして、グラデーションは避ける必要があります。
*   ロゴが伸縮されていないことを確認してください。

#### <a name="hero-logo"></a>Hero ロゴ

Hero ロゴはオプションです。 発行元は、Hero ロゴをアップロードしなくてもかまいません。 Hero アイコンがアップロードされたら、削除できません。 その時点で、パートナーは、Hero アイコンの Marketplace ガイドラインに従う必要があります。

Hero ロゴ アイコンに関するガイドラインに従ってください。

*   発行元表示名、プランとのタイトル、およびプランの長い概要は、白のフォント色で表示されます。 したがって、Hero アイコンの背景には明るい色を使用しないでください。 黒、白、または透明の背景は、Hero アイコンでは許可されていません。
*   プランが表示されると、発行元の表示名、プランのタイトル、プランの長い概要、および **[作成]** ボタンが、Hero ロゴ内にプログラムによって埋め込まれます。 したがって、Hero ロゴのデザイン時にはテキストを入力しないでください。 テキストはプログラムによってその場所に挿入されるため、右側にはスペースを空けておいてください。 右側のテキスト用の空のスペースは 415 x 100 ピクセルです。 左からのオフセットは 370 ピクセルです。

    ![Hero ロゴの例](./media/managed-application-author-marketplace/publishvm14.png)

## <a name="support-form"></a>[サポート] フォーム

**[サポート]** フォームには、会社のサポート連絡先情報を入力します。 たとえば、エンジニアリングやカスタマー サポートの連絡先情報です。

## <a name="publish-an-offer"></a>プランの発行

すべてのセクションを完了したら、**[発行]** を選択して、このプランを顧客が使用できるようにするためのプロセスを開始します。

## <a name="next-steps"></a>次のステップ

* マネージ アプリケーションの概要については、[マネージ アプリケーションの概要](managed-application-overview.md)に関するページをご覧ください。
* Marketplace からマネージ アプリケーションを使用する方法については、「[Consume Azure managed applications in the Marketplace (Marketplace での Azure マネージ アプリケーションの使用)](managed-application-consume-marketplace.md)」を参照してください。
* サービス カタログ マネージ アプリケーションの発行については、「[サービス カタログ マネージ アプリケーションの作成と発行](managed-application-publishing.md)」を参照してください。
* サービス カタログ マネージ アプリケーションの使用については、「[サービス カタログ マネージ アプリケーションの使用](managed-application-consumption.md)」を参照してください。
