---
title: Azure IoT Central アプリケーションを管理する | Microsoft Docs
description: 管理者として、Azure IoT Central アプリケーションを管理する方法
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 29ded279e2a76940049c257b954b1dae75f14836
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57778605"
---
# <a name="administer-your-iot-central-application"></a>IoT Central アプリケーションを管理する

IoT Central アプリケーションを作成したら、**[管理]** セクションに進んで、次のことを行うことができます。

- アプリケーション設定の管理
- ユーザーの管理
- ロールの管理
- 課金状況の表示
- 試用版の従量課金制への変換
- データのエクスポート
- デバイス接続の管理
- アクセス トークンの使用

**[Administration] (管理)** セクションにアクセスして使用するには、Azure IoT Central アプリケーションの**管理者**ロールが必要です。 Azure IoT Central アプリケーションを作成したユーザーは、自動的にそのアプリケーションの**管理者**ロールに割り当てられます。 この記事の「[ユーザーの管理](#manage-users)」セクションでは、**管理者**ロールを他のユーザーに割り当てる方法について詳しく説明します。

## <a name="manage-application-settings"></a>アプリケーション設定の管理

### <a name="change-application-name-and-url"></a>アプリケーションの名前と URL を変更する
**[アプリケーション設定]** ページで、アプリケーションの名前と URL を変更し、**[保存]** を選択できます。

![[Application Settings] (アプリケーション設定) ページ](media/howto-administer/image0-a.png)

> [!Note]
> URL を変更した場合は、Azure IoT Central の別の顧客が古い URL を取得できます。 その場合、その URL は使用できなくなります。 URL を変更すると、古い URL は機能しなくなるため、使用する新しい URL をユーザーに通知する必要があります。

### <a name="prepare-and-upload-image"></a>イメージの準備とアップロード
アプリケーションのイメージを変更する場合は、「[イメージを準備して Azure IoT Central アプリケーションにアップロードする](howto-prepare-images.md)」を参照してください。

### <a name="copy-an-application"></a>アプリケーションをコピーする
デバイス インスタンス、デバイス データ履歴、およびユーザー データ以外の任意のアプリケーションのコピーを作成できます。 コピーは、課金対象となる従量課金制アプリケーションになります。 この方法で試用版アプリケーションを作成することはできません。

**[コピー]** を選択します。 ダイアログ ボックスに、新しい従量課金制アプリケーションの詳細を入力します。 **[コピー]** を選択して､続行することを確認します｡ このフォームのフィールドの詳細については、[アプリケーションの作成](quick-deploy-iot-central.md)に関するクイック スタートを参照してください。

![[Application Settings] (アプリケーション設定) ページ](media/howto-administer/appcopy2.png)

アプリのコピー操作が成功したら、表示されるリンクを使用してアプリケーションをコピーすることで作成された新しいアプリケーションに移動できます。

![[Application Settings] (アプリケーション設定) ページ](media/howto-administer/appCopy3.png)

> [!Note]
> アプリケーションをコピーすると、ルールやアクションの定義もコピーされます。 しかし、コピー元のアプリへのアクセス権を持つユーザーがコピー先のアプリにコピーされることはないため、ユーザーが前提条件となる電子メールなどのアクションには手動でユーザーを追加する必要があります。 一般には、ルールとアクションを調べ、それらが新しいアプリで最新の状態になっていることを確認することをお勧めします。

### <a name="delete-an-application"></a>アプリケーションの削除

> [!Note]
> また、アプリケーションを削除するには、アプリケーションを作成したときに選択した Azure サブスクリプションのリソースを削除する許可も必要です。 詳細は、「[ロールベースのアクセス制御を使用して Azure サブスクリプション リソースへのアクセスを管理する](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)」を参照してください。

**[削除]** ボタンを使用して、IoT Central アプリケーションを完全に削除します。 この操作を行うと、そのアプリケーションに関連付けられているすべてのデータが完全に削除されます。

## <a name="manage-users"></a>ユーザーの管理

### <a name="add-users"></a>Add users

ユーザーが Azure IoT Central アプリケーションにサインインしてアクセスするには、ユーザー アカウントが必要です。 Azure IoT Central では、Microsoft アカウント (MSA) と Azure Active Directory (AAD) アカウントがサポートされています。 Azure Active Directory グループは 現在 Azure IoT Central でサポートされていません。

詳細は、「[Microsoft アカウントのヘルプ](https://support.microsoft.com/products/microsoft-account?category=manage-account)」と「[クイック スタート: Azure Active Directory に新しいユーザーを追加する](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)」を参照してください。

1. IoT Central アプリケーションにユーザーを追加するには、**[管理]** セクションの **[ユーザー]** ページに移動します。

    ![ユーザーの一覧](media/howto-administer/image1.png)

1. **[Users] (ユーザー)** ページで、**[+ Add user] (+ ユーザーの追加)** を選択してユーザーを追加します。

1. **ロール**ドロップダウン メニューからユーザーに対するロールを選択します。 ロールの詳細については、この記事の「[ロールの管理](#manage-roles)」セクションを参照してください。

    ![ロールの選択](media/howto-administer/image3.png)

    > [!NOTE]
    >  ユーザーを一括で追加するには、追加するすべてのユーザーのユーザー ID をセミコロンで区切って入力します。 **[Role] (ロール)** ドロップダウンからロールを選択します｡ 次に、**[保存]** を選択します。

### <a name="edit-the-roles-that-are-assigned-to-users"></a>ユーザーに割り当てられているロールを編集します。

割り当て後、ロールを変更することはできません。 ユーザーに割り当てられているロールを変更するには、ユーザーを削除してから、別のロールを使用してユーザーを追加します。

### <a name="delete-users"></a>ユーザーを削除する

ユーザーを削除するには、**ユーザー**ページで 1 つまたは複数のチェック ボックスを選択します｡ 次に、**[削除]** を選択します。

## <a name="manage-roles"></a>ロールの管理

ロールを使用すると、組織内で IoT Central のさまざまなタスクを実行できるユーザーを制御することができます。 アプリケーションのユーザーに割り当てることができるロールが 3 つあります。

### <a name="administrator"></a>管理者

**管理者**ロール内のユーザーは、アプリケーション内のすべての機能にアクセスできます。

アプリケーションを作成したユーザーは、自動的に**管理者**ロールに割り当てられます。 **管理者**ロールには、少なくとも 1 人のユーザーが常に存在している必要があります。

### <a name="application-builder"></a>Application Builder (アプリケーション ビルダー)

**Application Builder (アプリケーション ビルダー)** ロール内のユーザーは、アプリケーションの管理を除き、アプリケーション内のすべてを実行することができます。 つまり、ビルダーはデバイス テンプレートとデバイスの作成、編集、削除、デバイス セットの管理、および分析とジョブの実行が可能です。 ビルダーが、アプリケーションの **[管理]** セクションにアクセスすることはできません。

### <a name="application-operator"></a>Application Operator (アプリケーション オペレーター)

**Application Operator (アプリケーション オペレーター)** ロール内のユーザーはデバイス テンプレートを変更したり、アプリケーションを管理したりすることはできません。 つまり、オペレーターはデバイスの追加と削除、デバイス セットの管理、分析とジョブの実行が可能です。 オペレーターが、**[Application Builder]\(アプリケーション ビルダー\)** ページと **[管理]** ページにアクセスすることはできません。

## <a name="view-your-bill"></a>課金状況の表示

請求書を表示するには、**課金**ページの**管理**セクションに移動します。 新しいタブに Azure 課金ページが開き、Azure IoT Central アプリケーションごとに請求書を確認することができます。

### <a name="convert-your-trial-to-pay-as-you-go"></a>試用版の従量課金制への変換

試用版アプリケーションを従量課金制アプリケーションに変換することができます。 これらの種類のアプリケーション間の違いを以下に示します。

- **試用版**アプリケーションは、有効期限が切れるまでの 7 日間は無料となります。 有効期限内で、いつでも従量課金制に変更することができます。
- **従量課金制**アプリケーションは、デバイスごとに課金され、最初の 5 個のデバイスは無料です。

価格の詳細については、[Azure IoT Central の価格に関するページ](https://azure.microsoft.com/pricing/details/iot-central/)を参照してください。

このセルフ サービスのプロセスを完了するには、次の手順に従います。

1. **[管理]** セクションの **[課金]** ページに移動します。

    ![試用版の状態](media/howto-administer/freetrialbilling.png)

1. **[Convert to Pay-As-You-Go]\(従量課金制に変換\)** を選択します。

    ![試用版を変換する](media/howto-administer/convert.png)

1. 適切な Azure Active Directory を選択してから、従量課金制アプリケーションで使用する Azure サブスクリプションを選びます。

1. **[変換]** を選択すると、ご利用のアプリケーションが従量課金制アプリケーションとなり、課金が開始されます。

## <a name="export-data"></a>データのエクスポート

**[Continuous data export]\(連続データ エクスポート\)** を有効にすることで、測定、デバイス、デバイス テンプレートのデータを、Azure Blob ストレージ アカウントにエクスポートできます。 詳細については、[データをエクスポートする](howto-export-data.md)方法に関する記述を参照してください。

## <a name="manage-device-connection"></a>デバイス接続の管理

ここではキーと証明書を使用して、アプリケーションで数多くのデバイスを接続します。 詳細については、[デバイスの接続](concepts-connectivity.md)に関する記述を参照してください。

## <a name="use-access-tokens"></a>アクセス トークンの使用

アクセス トークンを生成し、開発者ツールで使用します。 現時点では、利用できる開発者ツール (デバイス メッセージ、およびプロパティと設定の変更を監視するための IoT Central エクスプローラー) が 1 つあります。 詳細については、[IoT Central エクスプローラー](howto-use-iotc-explorer.md)に関する記述を参照してください。

## <a name="use-the-azure-sdks-for-control-plane-operations"></a>Azure SDK を使用して制御プレーンを操作する

Node、Python、C#、フリガナ、Java、Go には､IoT Central の Azure Resource Manager SDK パッケージが用意されています｡ これらライブラリは､IoT Central に対する制御プレーン操作をサポートしており､ IoT Central アプリケーションの作成や一覧表示、更新、 削除を行うことができます｡ これらのライブラリには､各言語に固有の認証処理とエラー処理用のヘルパーも用意されています｡ 

Azure Resource Manager SDK を使用する方法例については､[ https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples) をご覧ください｡

詳細は、GitHub で以下のパッケージを参照してください。

| 言語 | リポジトリ | Package |
| ---------| ---------- | ------- |
| ノード | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>次の手順

ここでは、Azure IoT Central アプリケーションを管理する方法について説明しました。推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [デバイス テンプレートを設定する](howto-set-up-template.md)
