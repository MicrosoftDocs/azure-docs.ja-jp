---
title: Azure Resource Manager の体験版 | Microsoft Docs
description: Azure Resource Manager を使用して Markeplace 体験版を作成します
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick .Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 2fe1a1fc1ff82415a5c2f88c72ae707b12cd5283
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541164"
---
# <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager の体験版

この記事は、Azure Marketplace にオファーのある発行元、または AppSource を使用しているが Azure リソースのみで体験版を構築したい発行元を対象としています。

Azure Resource Manager (ARM) テンプレートは、ソリューションが最適に表現されるように設計する Azure リソースのコード化されたコンテナーです。 Resource Manager テンプレートに慣れていない場合は、[Resource Manager テンプレートの概要](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)と [Resource Manager テンプレートの作成](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)に関するページを読み、独自のテンプレートを作成してテストする方法を理解してください。

体験版は、提供された Resource Manager テンプレートを受け取り、Resource Manager テンプレートから必要なすべてのリソースをリソース グループにデプロイします。

Azure Resource Manager 体験版の構築の要件は次のとおりです。

- 体験版 Resource Manager テンプレートを作成、テスト、アップロードする。
- すべての必要なメタデータと設定を構成して、体験版を有効にする。
- 体験版を有効にしてオファーを再発行する。

## <a name="how-to-build-an-azure-resource-manager-test-drive"></a>Azure Resource Manager 体験版を作成する方法

Azure Resource Manager の体験版を構築するためのプロセスを以下に示します。

1. 顧客に行ってもらいたいことをフロー図にデザインします。
1. 顧客に構築してもらいたいエクスペリエンスを定義します。
1. 上記の定義に基づいて、顧客がそのエクスペリエンスを達成するためにどのような情報やリソースが必要かを決定します (たとえば、D365 インスタンスや、データベースを使った Web サイトなど)。
1. デザインをローカルでビルドし、エクスペリエンスをテストします。
1. エクスペリエンスを ARM テンプレート デプロイにパッケージし、そこから次のことを行います。
    1. リソースのどの部分が入力パラメーターかを定義します。
    1. 変数を定義します。
    1. カスタマー エクスペリエンスに提供される出力を定義します。
1. パブリッシュし、テストを行った後、稼働します。

Azure Resource Manager 体験版の作成に関する最も重要な部分は、顧客が体験するシナリオを定義することです。 ファイアウォール製品がスクリプト インジェクション攻撃をうまく処理する方法をデモするのですか。 ストレージ製品がファイルを速く簡単に圧縮することをデモするのですか。

十分な時間を費やして、製品を見てもらう最善の方法を評価します。 必要なすべてのリソースを具体的にすると、Resource Manager テンプレートのパッケージ化が簡単になります。

このファイアウォールの例のアーキテクチャでは、サービス用のパブリック IP URL と、ファイアウォールで保護する Web サイト用のパブリック IP URL が必要です。 各 IP は仮想マシンにデプロイされ、ネットワーク セキュリティ グループとネットワーク インターフェイスで相互に接続されます。

必要なリソース パッケージの設計が済んだら、体験版 Resource Manager テンプレートを記述して構築します。

## <a name="writing-test-drive-resource-manager-templates"></a>体験版 Resource Manager テンプレートの作成

体験版は完全自動モードでデプロイを実行するので、体験版テンプレートには以下で説明するいくつかの制限があります。

### <a name="parameters"></a>parameters

ほとんどのテンプレートにはパラメーターのセットがあります。 パラメーターでは、リソース名、リソースのサイズ (たとえば、ストレージ アカウントの種類や仮想マシンのサイズ)、ユーザー名とパスワード、DNS 名などを定義します。 Azure portal を使用してソリューションをデプロイするときは、手動でこれらすべてのパラメーターを設定し、使用可能な DNS 名やストレージ アカウント名などを選択できます。

![Azure Resource Manager のパラメーターの一覧](./media/azure-resource-manager-test-drive/param1.png)

ただし、体験版は人間が介入しない完全自動モードで動作するので、サポートされるパラメーター カテゴリのセットに制限があります。 体験版 Resource Manager テンプレートのパラメーターがサポートされているカテゴリのどれにも該当しない場合は、**そのパラメーターを変数または定数値に置き換える**必要があります。

任意の有効なパラメーター名を使用でき、体験版は metadata-type の値を使用してパラメーターのカテゴリを認識します。 **すべてのテンプレート パラメーターに対して metadata-type を指定する必要があります**。指定しないと、テンプレートは検証に合格しません。

```json
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username"
    }
  },
  ...
}
```

また、**すべてのパラメーターは省略可能**なので、使用しない場合は、指定する必要がないことも、注意すべき重要な点です。

### <a name="accepted-parameter-metadata-types"></a>使用できるパラメーター メタデータの種類

| メタデータの種類   | パラメーターのタイプ  | 説明     | 値の例    |
|---|---|---|---|
| **baseuri**     | string          | デプロイ パッケージのベース URI| https:\//\<\..\>.blob.core.windows.net/\<\..\> |
| **username**    | string          | 新しいランダムなユーザー名。| admin68876      |
| **password**    | secure string    | 新しいランダムなパスワード | Lp!ACS\^2kh     |
| **session id**   | string          | 一意の体験版 ID (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="username"></a>username

体験版はこのパラメーターをデプロイ パッケージの**ベース URI** で初期化するので、このパラメーターを使用して、パッケージに含まれるすべてのファイルの URI を作成できます。

```json
"parameters": {
  ...
  "baseuri": {
    "type": "string",
    "metadata": {
      "type": "baseuri",
      "description": "Base Uri of the deployment package."
    }
  },
  ...
}
```

テンプレート内で、このパラメーターを使用して、体験版のデプロイ パッケージからすべてのファイルの URI を作成できます。 次の例では、リンクされたテンプレートの URI を作成する方法を示します。

```json
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>username

体験版では、このパラメーターは新しいランダムなユーザー名で初期化されます。

```json
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username",
      "description": "Solution admin name."
    }
  },
  ...
}
```

値の例:

    admin68876

ソリューションではランダムなユーザー名または固定のユーザー名を使用できます。

#### <a name="password"></a>password

体験版では、このパラメーターは新しいランダムなパスワードで初期化されます。

```json
"parameters": {
  ...
  "password": {
    "type": "securestring",
    "metadata": {
      "type": "password",
      "description": "Solution admin password."
    }
  },
  ...
}
```

値の例:

    Lp!ACS^2kh

ソリューションではランダムなパスワードまたは固定のパスワードを使用できます。

#### <a name="session-id"></a>session ID

体験版では、このパラメーターは体験版のセッション ID を表す一意の GUID で初期化されます。

```json
"parameters": {
  ...
  "sessionid": {
    "type": "string",
    "metadata": {
      "type": "sessionid",
      "description": "Unique Test Drive session id."
    }
  },
  ...
}
```

値の例:

    b8c8693e-5673-449c-badd-257a405a6dee

必要な場合は、このパラメーターを使用して体験版のセッションを一意に識別できます。

### <a name="unique-names"></a>一意の名前

ストレージ アカウントや DNS 名などの一部の Azure リソースには、グローバルに一意の名前が必要です。

つまり、体験版で Resource Manager テンプレートをデプロイするたびに、そのすべてのリソースに対して**一意の名前を持つ新しいリソース グループ**が作成されます。 したがって、リソース グループ ID では [uniquestring](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions#uniquestring) 関数と変数名を連結して、ランダムな一意の値を生成する必要があります。

```json
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

パラメーター/変数文字列 (\'contosovm\') と一意の文字列出力 (\'resourceGroup().id\') を必ず連結してください。このようにすると、各変数の一意性と信頼性が保証されます。

たとえば、ほとんどのリソース名は数字で始めることができませんが、一意文字列関数は数字で始まる文字列を返す可能性があります。 そのため、一意の文字列出力をそのまま使用すると、デプロイは失敗します。 

リソースの名前付け規則と制限に関する追加情報については、[こちらの記事](https://docs.microsoft.com/azure/guidance/guidance-naming-conventions)を参照してください。

### <a name="deployment-location"></a>デプロイの場所

体験版を異なる Azure リージョンで使用できるようにすることができます。 このようにすると、ユーザーは最も近いリージョンを選択でき、ユーザー エクスペリエンスが最適になります。

体験版は、ラボのインスタンスを作成するとき常に、ユーザーが選択したリージョンにリソース グループを作成し、このグループのコンテキストでデプロイ テンプレートを実行します。 そのため、テンプレートでは、リソース グループからデプロイの場所を選択する必要があります。

```json
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

その後は、特定のラボ インスタンスに対するすべてのリソースでこの場所を使用します。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/publicIPAddresses",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/virtualNetworks",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/networkInterfaces",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Compute/virtualMachines",
    "location": "[variables('location')]",
    ...
  }
]
```

選択した各リージョンで必要なすべてのリソースのデプロイがサブスクリプションで許可されていることを確認する必要があります。 また、有効にしようとするすべてのリージョンで、仮想マシンのイメージが使用可能であることを確認する必要があります。使用できない場合は、デプロイ テンプレートが一部のリージョンで機能しません。

### <a name="outputs"></a>出力

通常、Resource Manager テンプレートでは、出力を生成せずにデプロイを実行できます。 これは、テンプレート パラメーターの設定に使用するすべての値がわかっており、常に任意のリソースのプロパティを手動で検査できるためです。

ただし、体験版 Resource Manager テンプレートでは、すべての情報を体験版に返すことが重要です。これは、ラボにアクセスするために必要です (Web サイトの URI、仮想マシンのホスト名、ユーザー名、パスワード)。 これらの変数は顧客に表示されるため、すべての出力名が判読可能であることを確認します。

テンプレートの出力に関する制限はありません。 体験版はすべての出力値を**文字列**に変換するので、出力にオブジェクトを送信すると、ユーザーには JSON 文字列が表示されることを憶えておいてください。

例:

```json
"outputs": {
  "Host Name": {
    "type": "string",
    "value": "[reference(variables('pubIpId')).dnsSettings.fqdn]"
  },
  "User Name": {
    "type": "string",
    "value": "[parameters('adminName')]"
  },
  "Password": {
    "type": "string",
    "value": "[parameters('adminPassword')]"
  }
}
```

### <a name="subscription-limits"></a>サブスクリプションの制限

考慮する必要があるもう 1 つの点は、サブスクリプションとサービスの制限です。 たとえば、最大 10 個の 4 コア仮想マシンをデプロイする場合、ラボに使用するサブスクリプションで 40 コアの使用が許可されていることを確認する必要があります。

Azure サブスクリプションとサービスの制限について詳しくは、[こちらの記事](https://docs.microsoft.com/azure/azure-subscription-service-limits)をご覧ください。 複数の体験版が同時に使用される可能性があるので、コアの数に同時実行できる体験版の合計数を掛けた値を、サブスクリプションが処理できることを確認します。

### <a name="what-to-upload"></a>アップロードするもの

体験版 Resource Manager テンプレートは zip ファイルとしてアップロードされ、それにはさまざまなデプロイ成果物を含めることができますが、**main-template.json** という名前のファイルだけは必ず含まれる必要があります。 このファイルは Azure Resource Manager デプロイ テンプレートであり、体験版はそれを使用してラボをインスタンス化します。

このファイル以外の追加リソースがある場合、テンプレートの内部でそれを外部リソースとして参照するか、または zip ファイルにリソースを含めることができます。

証明書の発行中に、体験版はデプロイ パッケージを解凍し、体験版の内部 BLOB コンテナーにその内容を格納します。 コンテナーの構造は、デプロイ パッケージの構造を反映しています。

| package.zip                       | 体験版 BLOB コンテナー         |
|---|---|
| main-template.json                | https:\//\<\...\>.blob.core.windows.net/\<\...\>/main-template.json  |
| templates/solution.json           | https:\//\<\...\>.blob.core.windows.net/\<\...\>/templates/solution.json |
| scripts/warmup.ps1                | https:\//\<\...\>.blob.core.windows.net/\<\...\>/scripts/warmup.ps1  |


この BLOB コンテナーの URI をベース URI と呼びます。 ラボのすべてのリビジョンには固有の BLOB コンテナーがあるので、ラボのすべてのリビジョンは固有のベース URI を持っています。 体験版は、テンプレート パラメーターを使用して、解凍されたデプロイ パッケージのベース URI をテンプレートに渡すことができます。

## <a name="transforming-template-examples-for-test-drive"></a>体験版のテンプレート変換の例

リソースのアーキテクチャを体験版 Resource Manager テンプレートに変換するプロセスは難しい場合があります。 このプロセスを簡単にするために、[こちらに現在のデプロイ テンプレートを最適に変換する](./transforming-examples-for-test-drive.md)方法に関する例を用意しました。

## <a name="how-to-publish-a-test-drive"></a>体験版の発行方法

体験版の作成が済んだので、このセクションでは体験版を正常に発行するために必要な各フィールドについて説明します。

![ユーザー インターフェイスでの体験版の有効化](./media/azure-resource-manager-test-drive/howtopub1.png)

最初の最も重要なフィールドは、オファーで体験版を有効にするかどうかの切り替えです。 **[はい]** を選択すると、必要なフィールドを含むフォームの残りの部分を設定できるようになります。**[いいえ]** を選択するとフォームは無効になり、体験版を無効にして再発行した場合、運用環境から体験版が削除されます。

注:ユーザーによってアクティブに使用されている体験版が存在する場合は、そのセッションの期限が切れるまで、それらの体験版は引き続き実行されます。

### <a name="details"></a>詳細

次に設定するセクションは、体験版オファーに関する詳細です。

![体験版の詳細情報](./media/azure-resource-manager-test-drive/howtopub2.png)

**[Description]\(説明\) -** "*必須*" 体験版に関する主要な説明をここに記述します。 顧客はここを読んで、体験版でカバーされている製品のシナリオを理解します。 

**[User Manual]\(ユーザー マニュアル\) -** "*必須*" 体験版エクスペリエンスの詳細なチュートリアルです。 顧客はこれを開き、体験版で顧客に行ってもらいたいことの正確な手順を把握します。 この内容は簡単に理解して実行できるものにすることが重要です。 (.pdf ファイルにする必要があります)

**[Test Drive Demo Video]\(体験版デモ ビデオ\) -** "*推奨*" ユーザー マニュアルと似ており、体験版のエクスペリエンスのビデオ チュートリアルを含めるのが最善です。 顧客は体験版を使用する前または使用中にこれを見て、体験版で顧客に行ってもらいたいことの正確な手順を把握します。 この内容は簡単に理解して実行できるものにすることが重要です。

- **[Name]\(名前\)** - ビデオのタイトル
- **[Link]\(リンク\)** - YouTube またはビデオの URL を埋め込む必要があります。 埋め込み URL を取得する方法の例を後で示します。
- **[Thumbnail]\(サムネイル\)** - 高品質の画像 (533 x 324 ピクセル) にする必要があります。 体験版エクスペリエンスの一部のスクリーンショットをここに表示することをお勧めします。

体験版使用中の顧客に対するこれらのフィールドの表示を次に示します。

![Marketplace オファーでの体験版の場所フィールド](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>技術的構成

次のセクションでは、体験版 Resource Manager テンプレートをアップロードする場所を指定し、体験版インスタンスの動作を具体的に定義します。

![](./media/azure-resource-manager-test-drive/howtopub5.png)

**[Instances]\(インスタンス\) -** "*必須*" ここでは、必要なインスタンスの数、リージョン、顧客が体験版を取得できる速さを構成します。

- **[Instances]\(インスタンス\)** - [Select regions]\(リージョンの選択\) では、体験版 Resource Manager テンプレートのデプロイ先を選択します。 最も多くの顧客がいると予想されるリージョンを 1 つ選択することをお勧めします。
- **[Hot]\(ホット\)** - 選択したリージョンごとに既にデプロイされていてアクセスを待っている体験版インスタンスの数です。 顧客は、デプロイを待つことなくすぐにこの体験版にアクセスできます。 トレードオフとして、これらのインスタンスは Azure サブスクリプションで常に実行しているので、大きな稼働時間コストが発生します。 ほとんどの顧客は完全なデプロイが完了するまで待ちたくなく、顧客の利用が減るので、**少なくとも 1 つのホット インスタンス**を用意することを強くお勧めします。
- **[Warm]\(ウォーム\)** - デプロイされた後、VM が停止されて Azure ストレージに格納されている、リージョンごとの体験版インスタンスの数です。 ウォーム インスタンスの待機時間はホット インスタンスより長くなりますが、ストレージの稼働時間コストも少なくなります。
- **[Cold]\(コールド\)** - デプロイできる状態の、リージョンごとの体験版インスタンスの数です。 コールド インスタンスでは、顧客が体験版を要求した時点で体験版 Resource Manager テンプレート全体をデプロイする必要があるため、ホットまたはウォーム インスタンスより遅くなります。 ただし、トレードオフとして、体験版の期間のみの支払いで済みます。

この時点で、同時に使用可能になる可能性がある体験版の数の合計を計算し、サブスクリプションのクォータ制限がその同時実行数を処理できることを確認します。

**(選択したリージョンの数 x ホット インスタンス数) + (選択したリージョンの数 x ウォーム インスタンス数) + (選択したリージョンの数 x コールド インスタンス数)**

**[Test Drive Duration (hours)]\(体験版期間 (時間)\) -** "*必須*" 体験版をアクティブなままにしておく期間の時間数です。 この期間が終わると、体験版は自動的に終了します。

**[Test Drive Resource Manager template]\(体験版 Resource Manager テンプレート\) -** "*必須*" Resource Manager テンプレートをここにアップロードします。 これは、前のセクションで作成したファイルです。 メイン テンプレート ファイルに "main-template.json" という名前を付け、必要な主要変数の出力パラメーターが Resource Manager テンプレートに含まれることを確認します。 (.zip ファイルにする必要があります)

**[Access Information]\(アクセス情報\) -** "*必須*" 顧客が体験版を取得すると、アクセス情報が顧客に対して表示されます。 これらの説明は、体験版 Resource Manager テンプレートからの役に立つ出力パラメーターを共有するためのものです。 出力パラメーターを含めるには、二重中かっこを使用し (例: **{{outputname}}**)、正しい場所に挿入します。 (フロントエンドでレンダリングするため、HTML 文字列形式にすることをお勧めします)。

### <a name="test-drive-deployment-subscription-details"></a>体験版デプロイ サブスクリプションの詳細

設定する最後のセクションでは、Azure サブスクリプションと Azure Active Directory (AD) を接続することで、体験版を自動的にデプロイできるようにします。

![体験版デプロイ サブスクリプションの詳細](./media/azure-resource-manager-test-drive/subdetails1.png)

**[Azure Subscription ID]\(Azure サブスクリプション ID\) -** "*必須*" Azure サービスと Azure portal へのアクセス権を付与します。 このサブスクリプションで、リソースの使用状況が報告され、サービスが課金されます。 体験版 "**専用**" の Azure サブスクリプションがまだない場合は、先に進んで作成します。 Azure サブスクリプション ID を確認するには、Azure portal にログインし、左側のメニューでサブスクリプションに移動します。 (例: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Azure サブスクリプション](./media/azure-resource-manager-test-drive/subdetails2.png)

**[Azure AD Tenant ID]\(Azure AD テナント ID\) -** "*必須*" 使用できるテナント ID が既にある場合は、[プロパティ] -\> [ディレクトリ ID] の下で確認できます。

![Azure Active Directory のプロパティ](./media/azure-resource-manager-test-drive/subdetails3.png)

または、Azure Active Directory で新しいテナントを作成します。

![Azure Active Directory テナントの一覧](./media/azure-resource-manager-test-drive/subdetails4.png)

![Azure AD テナントの組織、ドメイン、国を定義します](./media/azure-resource-manager-test-drive/subdetails5.png)

![選択を確認する](./media/azure-resource-manager-test-drive/subdetails6.png)

**[Azure AD App ID]\(Azure AD アプリ ID\) -** "*必須*" 次の手順では、新しいアプリケーションを作成して登録します。 このアプリケーションを使用して、体験版インスタンスの操作を実行します。

1. 新しく作成したディレクトリまたは既存のディレクトリに移動し、フィルター ウィンドウで Azure Active Directory を選択します。
2. [アプリの登録] を探して、[追加] をクリックします
3. アプリケーション名を指定します。
4. 種類として [Web app / API]\(Web アプリ/API\) を選択します
5. サインオン URL に任意の値を指定します。ここでは、このフィールドは使用しません。
6. [作成] をクリックします。
7. アプリケーションが作成されたら、[プロパティ] -\> [Set the application as multi-tenant]\(アプリケーションをマルチテナントとして設定する\) に移動し、[保存] をクリックします。

[保存] をクリックします。 最後の手順では、この登録済みアプリのアプリケーション ID を取得して、体験版のフィールドに貼り付けます。

![Azure AD アプリケーション ID の詳細](./media/azure-resource-manager-test-drive/subdetails7.png)

アプリケーションを使用してサブスクリプションにデプロイするので、サブスクリプションで共同作成者としてアプリケーションを追加する必要があります。 その手順は以下のとおりです。

1. [サブスクリプション] ブレードに移動し、体験版のみに使用している適切なサブスクリプションを選択します。
1. **[アクセス制御 (IAM)]** をクリックします。
1. **[ロールの割り当て]** タブをクリックします。![新しいアクセス制御プリンシパルを追加する](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. **[ロールの割り当ての追加]** をクリックします。
1. **共同作成者**としてロールを設定します。
1. Azure AD アプリケーションの名前を入力し、ロールを割り当てるアプリケーションを選択します。
    ![アクセス許可を追加する](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. **[Save]** をクリックします。

**[Azure AD App Key]\(Azure AD アプリ キー\) -** "*必須*" 最後のフィールドでは、認証キーを生成します。 キーの下で、キーの説明を追加し、期間を無期限に設定して、[保存] を選択します。 キーの有効期限が切れないようにすることが**重要**です。切れると、運用環境の体験版が中断されます。 この値をコピーし、必要な体験版のフィールドに貼り付けます。

![Azure AD アプリケーションのキーを表示する](./media/azure-resource-manager-test-drive/subdetails8.png)

## <a name="next-steps"></a>次の手順

体験版のすべてのフィールドを設定したので、オファーを**再発行**します。 体験版が認定に合格したら、オファーの**プレビュー**で顧客エクスペリエンスを広範にテストする必要があります。 UI で体験版を開始し、Azure portal 内で Azure サブスクリプションを開いて、体験版が完全に正しくデプロイされていることを確認します。

![Azure ポータル](./media/azure-resource-manager-test-drive/subdetails9.png)

重要なこととして、体験版は顧客用にプロビジョニングされており、顧客がその使用を終了すると体験版サービスによってリソース グループが自動的にクリーンアップされるので、体験版のインスタンスを削除しないでください。

プレビューのオファリングに問題がなければ、**稼働**を開始します。 オファーが発行されると Microsoft による最終的なレビュー プロセスが行われ、エクスペリエンス全体が再確認されます。 何らかの理由によりオファーが却下された場合は、オファーのエンジニアリング連絡先に通知され、必要な修正が説明されます。

さらに質問がある場合やトラブルシューティングに関するアドバイスが必要な場合、あるいは体験版をより成功させたい場合は、「[FAQ, Troubleshooting, & Best Practices](./marketing-and-best-practices.md)」(FAQ、トラブルシューティング、およびベスト プラクティス) をご覧ください。
