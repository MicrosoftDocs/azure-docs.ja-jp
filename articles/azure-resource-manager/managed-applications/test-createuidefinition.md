---
title: UI 定義ファイルをテストする
description: ポータルを使用してご自身の Azure マネージド アプリケーションを作成するためのユーザー エクスペリエンスをテストする方法について説明します。
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: f76d3b81c2d5425f7bb91c5c86a79faa097794e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96435002"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Azure Managed Applications 用のポータル インターフェイスをテストする

マネージド アプリケーション用の [createUiDefinition.json ファイルを作成](create-uidefinition-overview.md)したら、ユーザー エクスペリエンスをテストする必要があります。 テストを簡素化するには、ポータルにファイルを読み込むサンドボックス環境を使用します。 マネージド アプリケーションを実際に展開する必要はありません。 サンドボックスでは、最新の全画面表示ポータル エクスペリエンスでユーザー インターフェイスが表示されます。 または、スクリプトを使用してインターフェイスをテストすることもできます。 この記事では、両方の方法を紹介します。 インターフェイスのプレビューには、サンドボックスがお勧めの方法です。

## <a name="prerequisites"></a>前提条件

* **createUiDefinition.json** ファイル。 このファイルがない場合は、[サンプル ファイル](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json)をコピーします。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="use-sandbox"></a>サンドボックスの使用

1. 「[UI 定義サンドボックスの作成](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade)」を開きます。

   ![サンドボックスの表示](./media/test-createuidefinition/show-sandbox.png)

1. 空の定義をご自分の createUiDefinition.json ファイルの内容に置き換えます。 **[Preview]\(プレビュー\)** を選択します。

   ![[プレビュー] の選択](./media/test-createuidefinition/select-preview.png)

1. 作成したフォームが表示されます。 ユーザー エクスペリエンスをステップ実行して、値を入力できます。

   ![フォームの表示](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>トラブルシューティング

**[Preview]\(プレビュー\)** の選択後、フォームが表示されない場合は、構文エラーがある可能性があります。 右側のスクロール バーの赤色のインジケーターを探して、そこに移動します。

![構文エラーの表示](./media/test-createuidefinition/show-syntax-error.png)

フォームが表示されず、代わりにしずくの形が付いたクラウドのアイコンが表示された場合は、プロパティが不足しているなど、フォームにエラーがあります。 ブラウザーで、Web Developer Tools を開きます。 **コンソール** にお使いのインターフェイスに関する重要なメッセージが表示されます。

![エラーの表示](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>テスト スクリプトの使用

ポータルでご自身のインターフェイスをテストするには、お使いのローカル コンピューターに次のスクリプトのいずれかをコピーします。

* [PowerShell side-load スクリプト - Az モジュール](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-AzCreateUIDefinition.ps1)
* [PowerShell side-load スクリプト - Azure モジュール](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Azure CLI side-load スクリプト](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

ポータルでご自身のインターフェイス ファイルを確認するには、ダウンロードしたスクリプトを実行します。 スクリプトによりストレージ アカウントがご自身の Azure サブスクリプションに作成され、該当する createUiDefinition.json ファイルがそのストレージ アカウントにアップロードされます。 ストレージ アカウントが作成されるのは、最初にスクリプトを実行したとき、またはストレージ アカウントが削除されている場合です。 ストレージ アカウントが Azure サブスクリプション内に存在している場合は、そのアカウントが再利用されます。 スクリプトによって、ポータルが開き、ストレージ アカウントから該当するファイルが読み込まれます。

ストレージ アカウントに対して場所を入力し、該当する createUiDefinition.json ファイルがあるフォルダーを指定します。

PowerShell では、次を使用します。

```powershell
.\SideLoad-AzCreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

Azure CLI では、次を使用します。

```bash
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

createUiDefinition.json ファイルがスクリプトと同じフォルダー内にあり、ストレージ アカウントが既に作成されている場合は、これらのパラメーターを指定する必要はありません。

PowerShell では、次を使用します。

```powershell
.\SideLoad-AzCreateUIDefinition.ps1
```

Azure CLI では、次を使用します。

```bash
./sideload-createuidef.sh
```

スクリプトによって、ブラウザーで新しいタブが開きます。 マネージド アプリケーションを作成するためのインターフェイスでポータルが表示されます。

フィールドの値を指定します。 完了すると、テンプレートに渡される値を、ブラウザーの開発者ツール コンソールで確認できます。

![値の表示](./media/test-createuidefinition/show-json.png)

これらの値をパラメーター ファイルとして使用して、デプロイ テンプレートをテストできます。

概要画面で、ポータルがハングした場合、出力セクションにバグがある可能性があります。 たとえば、存在しないコントロールを参照している可能性があります。 出力のパラメーターが空の場合、パラメーターが、存在しないプロパティを参照している可能性があります。 たとえば、コントロールの参照は有効であっても、プロパティの参照が無効になっています。

## <a name="test-your-solution-files"></a>ソリューション ファイルのテスト

お使いのポータル インターフェイスが想定どおりに動作していることを確認できたので、次は該当する createUiDefinition ファイルが目的の mainTemplate.json ファイルに正しく統合されていることを検証します。 検証スクリプト テストを実行すると、createUiDefinition ファイルなど、目的のソリューション ファイルのコンテンツをテストできます。 このスクリプトによって JSON 構文が検証され、テキスト フィールドの正規表現式がチェックされます。また、ポータル インターフェイスの出力値が、該当するテンプレートのパラメーターと一致するかどうかが確認されます。 このスクリプトの実行については、[テンプレート用の静的検証チェックの実行](https://github.com/Azure/azure-quickstart-templates/tree/master/test)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

ポータル インターフェイスを検証したら、[Marketplace で利用できる Azure マネージド アプリケーション](../../marketplace/create-new-azure-apps-offer.md)の作成について確認します。