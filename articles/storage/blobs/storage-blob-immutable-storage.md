---
title: Azure Storage Blob の不変ストレージ | Microsoft Docs
description: Azure Storage では、BLOB (オブジェクト) ストレージの WORM (Write Once Read Many) がサポートされています。これにより、指定された期間、消去および変更できない状態でデータを保存できます。
services: storage
author: xyh1
ms.service: storage
ms.topic: article
ms.date: 03/26/2019
ms.author: hux
ms.subservice: blobs
ms.openlocfilehash: 32328b89e8a220269f0d07c3700566db5b899d5b
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445684"
---
# <a name="store-business-critical-data-in-azure-blob-storage"></a>ビジネスに不可欠なデータを Azure Blob Storage 内に保管する

Azure Blob Storage の不変ストレージを使用すると、ユーザーはビジネスに不可欠なデータ オブジェクトを WORM (Write Once Read Many) 状態で保存できます。 この状態では、ユーザーが指定した期間、データを消去および変更できなくなります。 保持間隔の間、BLOB オブジェクトの作成と読み取りは可能ですが、変更または削除することはできません。 不変ストレージは、Azure リージョン内の General Purpose V2 および Blob Storage アカウントに対して有効になっています。

## <a name="overview"></a>概要

不変ストレージを使用すると、医療機関、金融機関、および関連する業界 (特に、ブローカー ディーラー組織) は、データを安全に保存できるようになります。 これは、重要なデータを変更や削除から保護するためのどのシナリオでも利用できます。 

一般的な用途は次のとおりです。

- **法令遵守**:Azure Blob Storage の不変ストレージは、組織が SEC 17a-4(f)、CFTC 1.31(d)、FINRA などの規制に準拠するのに役立ちます。 不変ストレージがこれらの規制要件にどのように対応しているかについて詳細に示した、Cohasset Associates によるテクニカル ホワイトペーパーをご用意しています。[Microsoft Service Trust Portal](https://aka.ms/AzureWormStorage) からダウンロードしてご利用ください。 [Azure セキュリティ センター](https://www.microsoft.com/trustcenter/compliance/compliance-overview)では、コンプライアンス関連の Microsoft の認定資格に関する詳しい情報を確認できます。

- **セキュリティ保護されたドキュメント リテンション**:Azure Blob Storage の不変ストレージにより、アカウント管理特権を持つユーザーを含め、どのユーザーもデータを変更したり、削除したりできなくなります。

- **訴訟ホールド**:Azure Blob Storage の不変ストレージを使用すると、ユーザーは訴訟やビジネス用途に不可欠な機密情報を、ホールドが削除されるまでの必要な期間中、改ざん防止状態で保存できます。 この機能は、法的なユース ケースのみに限定されず、イベント トリガーや会社のポリシーに基づいたデータの保護が必要な、イベント ベースのホールドまたはエンタープライズ ロックとして考えることもできます。

不変ストレージは以下のサポートがあります。

- **[時間ベースのリテンション ポリシーのサポート](#time-based-retention)**:ユーザーは、指定した期間、データを保存するポリシーを設定できます。 時間ベースのリテンション ポリシーを設定すると、BLOB の作成と読み取りは可能ですが、変更または削除はできません。 保持期間の期限が切れた後は BLOB を削除できますが、上書きはできません。

- **[訴訟ホールド ポリシーのサポート](#legal-holds)**:保持間隔がわからない場合は、訴訟ホールドを設定することで、訴訟ホールドがクリアされるまでデータを不変状態で保存できます。  訴訟ホールド ポリシーを設定すると、BLOB の作成と読み取りは可能ですが、変更または削除はできません。 各訴訟ホールドは、識別子文字列として使用されるユーザー定義の英数字のタグ (事件 ID や事件名など) に関連付けられています。 

- **すべての BLOB 層のサポート:** WORM ポリシーは Azure Blob Storage 層から独立しており、ホット、クール、アーカイブのすべての層に適用されます。 ユーザーは、データの不変性を維持しながら、ワークロードに対応する最もコストが最適化された層にデータを移行できます。

- **コンテナー レベルの構成:** ユーザーは、時間ベースのリテンション ポリシーと訴訟ホールド タグをコンテナー レベルで構成できます。 ユーザーは、コンテナー レベルのシンプルな設定を使用して、時間ベースのリテンション ポリシーの作成とロック、保持間隔の延長、訴訟ホールドの設定とクリアなどを行うことができます。 これらのポリシーは、コンテナー内の既存および新規のすべての BLOB に適用されます。

- **監査ログのサポート**:各コンテナーには、監査ログが含まれています。 各コンテナーでは、保持間隔の延長の最大 3 つのログと共に、ロック済みの時間ベースのリテンション ポリシーの時間ベースのリテンション コマンドが最大 5 つ表示されます。 時間ベースのリテンションの場合、ログにはユーザー ID、コマンドの種類、タイム スタンプ、保持間隔が含まれます。 訴訟ホールドの場合、ログにはユーザー ID、コマンドの種類、タイム スタンプ、訴訟ホールド タグが含まれます。 このログは、SEC 17a-4(f) 規制ガイドラインに従い、コンテナーの有効期間の間保持されます。 コントロール プレーンのすべてのアクティビティのより包括的なログは、[Azure アクティビティ ログ](../../azure-monitor/platform/activity-logs-overview.md)に表示されます。有効になっているときは [Azure 診断ログ](../../azure-monitor/platform/diagnostic-logs-overview.md)が保持され、データ プレーン操作が表示されます。 規制や他の目的で必要になる可能性のあるログは、ユーザーが永続的に保存する必要があります。

## <a name="how-it-works"></a>動作のしくみ

Azure Blob Storage の不変ストレージでは、時間ベースのリテンションと訴訟ホールドの 2 種類の WORM (不変) ポリシーがサポートされています。 時間ベースのリテンション ポリシーまたは訴訟ホールドをコンテナーに適用すると、既存のすべての BLOB が 30 秒以内に不変 WORM 状態に移行します。 そのコンテナーにアップロードされるすべての新しい BLOB も不変状態に移行します。 すべての BLOB が不変ストレージに移行されると、不変ポリシーが承認され、不変コンテナー内の既存のオブジェクトと新しいオブジェクトのすべての上書きまたは削除操作は許可されなくなります。

コンテナーとアカウントの削除は、不変ポリシーによって保護されている BLOB がある場合にも許可されません。 ロック済みの時間ベースのリテンション ポリシーまたは訴訟ホールドが適用された BLOB が 1 つ以上存在すると、Delete Container 操作は失敗します。 訴訟ホールドまたは保持間隔がアクティブな BLOB が格納された WORM コンテナーが 1 つ以上ある場合、ストレージ アカウントの削除は失敗します。 

### <a name="time-based-retention"></a>時間ベースのリテンション

> [!IMPORTANT]
> SEC 17a-4(f) や他の規制を順守するために BLOB を不変 (書き込みおよび削除禁止) 状態にするには、時間ベースのリテンション ポリシーを"*ロック*"する必要があります。 適切な時間内 (通常は 24 時間以内) にポリシーをロックすることをお勧めします。 短期間の機能評価以外の目的で、"*ロック解除*"状態を使用しないことをお勧めします。

時間ベースのリテンション ポリシーをコンテナーに適用すると、"*有効な*"リテンション期間中、コンテナー内のすべての BLOB が不変状態のままになります。 既存の BLOB の有効な保有期間は、BLOB の変更時刻とユーザーが指定した保有期間の差になります。

新しい BLOB の場合、有効な保有期間はユーザーが指定した保有期間と同じです。 ユーザーは保持間隔を延長できるので、不変ストレージでは、ユーザー指定の保持間隔の最新の値が有効な保有期間の計算に使用されます。

> [!TIP]
> **例:** ユーザーが、保持間隔が 5 年の時間ベースのリテンション ポリシーを作成しました。
>
> そのコンテナー内の既存の BLOB である _testblob1_ は、1 年前に作成されました。 _testblob1_ の有効なリテンション期間は、4 年です。
>
> 新しい BLOB (_testblob2_) がコンテナーにアップロードされました。 この新しい BLOB の有効なリテンション期間は、5 年です。

### <a name="legal-holds"></a>訴訟ホールド

訴訟ホールドを設定する場合、訴訟ホールドがクリアされるまで、既存および新規のすべての BLOB が不変状態のままになります。 訴訟ホールドを設定およびクリアする方法の詳細については、「[Getting started](#getting-started)」 (使用の開始) セクションを参照してください。

コンテナーには、訴訟ホールドと時間ベースのリテンション ポリシーの両方を同時に適用できます。 有効なリテンション期間が終了していても、すべての訴訟ホールドがクリアされるまで、そのコンテナー内のすべての BLOB が不変状態のままになります。 逆に、すべての訴訟ホールドがクリアされていても、有効なリテンション期間が終了するまで、BLOB は不変状態のままになります。

次の表に、さまざまな不変シナリオで無効になる BLOB 操作の種類を示します。 詳細については、「[Azure Blob Service API に関する記事](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)」を参照してください。

|シナリオ  |BLOB の状態  |禁止されている BLOB 操作  |
|---------|---------|---------|
|BLOB の有効なリテンション期間がまだ終了していないか、訴訟ホールドが設定されている     |不変: 削除および書き込み禁止         | Put Blob<sup>1</sup>、Put Block<sup>1</sup>、Put Block List<sup>1</sup>、Delete Container、Delete Blob、Set Blob Metadata、Put Page、Set Blob Properties、Snapshot Blob、Incremental Copy Blob、Append Block         |
|BLOB の有効なリテンション期間が終了している     |書き込み禁止のみ (削除操作は可能)         |Put Blob<sup>1</sup>、Put Block<sup>1</sup>、Put Block List<sup>1</sup>、Set Blob Metadata、Put Page、Set Blob Properties、Snapshot Blob、Incremental Copy Blob、Append Block         |
|すべての訴訟ホールドがクリアされており、時間ベースのリテンション ポリシーがコンテナー上で設定されていない     |変更可能         |なし         |
|WORM ポリシーが作成されていない (時間ベースのリテンションまたは訴訟ホールド)     |変更可能         |なし         |

<sup>1</sup> アプリケーションでは、これらの操作で新しい BLOB を 1 回作成することができます。 不変コンテナーの既存の BLOB パスでの以降のすべての上書き操作は、許可されません。

## <a name="pricing"></a>価格

この機能の使用に対する追加料金はありません。 不変データは、通常の変更可能データと同様に価格が設定されます。 Azure Blob Storage の価格設定については、[Azure Storage の価格に関するページ](https://azure.microsoft.com/pricing/details/storage/blobs/)を参照してください。

## <a name="getting-started"></a>使用の開始
不変ストレージは、General Purpose v2 および Blob Storage アカウントでのみ使用できます。 これらのアカウントは、[Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) を使用して管理する必要があります。 既存の General Purpose v1 ストレージ アカウントのアップグレードについては、[ストレージ アカウントのアップグレード](../common/storage-account-upgrade.md)に関する記事を参照してください。

[Azure portal](https://portal.azure.com)、[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)、および [Azure PowerShell](https://github.com/Azure/azure-powershell/releases) の最新リリースでは、Azure Blob Storage の不変ストレージがサポートされます。 [クライアント ライブラリのサポート](#client-libraries)も提供されています。


### <a name="azure-portal"></a>Azure ポータル

1. 新しいコンテナーを作成するか、既存のコンテナーを選択して、不変状態に維持する必要がある BLOB を格納します。
 コンテナーは、GPv2 ストレージ アカウントまたは BLOB ストレージ アカウントに存在する必要があります。
2. [コンテナーの設定] 内にある **[アクセス ポリシー]** を選択します。 次に、**[Immutable blob storage]\(不変 BLOB ストレージ\)** の下で **[+ Add policy]\(+ポリシーの追加\)** を選択します。

    ![ポータルでコンテナーの設定](media/storage-blob-immutable-storage/portal-image-1.png)

3. 時間ベースのリテンションを有効にするには、ドロップダウン メニューから **[時間ベースの保持]** を選択します。

    ![[ポリシーの種類] の下で選択されている [時間ベースの保持]](media/storage-blob-immutable-storage/portal-image-2.png)

4. 保持間隔を日数で入力します (指定できる値は 1 ～ 146000 日)。

    ![[Update retention period to]\(保持期間を次に更新\) ボックス](media/storage-blob-immutable-storage/portal-image-5-retention-interval.png)

    ポリシーの初期状態のロックが解除されて、ロックする前に機能をテストし、ポリシーに変更を加えることができます。 ポリシーのロックは、SEC 17a-4 などの規制に準拠するために必要です。

5. ポリシーをロックします。 省略記号 (**...**) を右クリックすると、追加のアクションと共に次のメニューが表示されます。

    ![メニュー上の [ポリシーのロック]](media/storage-blob-immutable-storage/portal-image-4-lock-policy.png)

6. **[ポリシーのロック]** を選択し、ロックを確認します。 これでポリシーがロックされて削除できなくなり、保持間隔の延長だけが許可されるようになります。 BLOB の削除とオーバーライドは許可されていません。 

    ![メニューで [ポリシーのロック] を確認する](media/storage-blob-immutable-storage/portal-image-5-lock-policy.png)

7. 訴訟ホールドを有効にするには、**[+ Add Policy]\(+ ポリシーの追加\)** を選択します。 ドロップダウン メニューから **[訴訟ホールド]** を選択します。

    ![[ポリシーの種類] の下のメニューにある [訴訟ホールド]](media/storage-blob-immutable-storage/portal-image-legal-hold-selection-7.png)

8. 1 つ以上のタグを持つ訴訟ホールドを作成します。

    ![[ポリシーの種類] の下にある [タグ名] ボックス](media/storage-blob-immutable-storage/portal-image-set-legal-hold-tags.png)

9. 訴訟ホールドをクリアするには、適用された訴訟ホールド ID タグを削除するだけです。

### <a name="azure-cli"></a>Azure CLI

この機能は、`az storage container immutability-policy` および `az storage container legal-hold` コマンド グループに含まれています。 これらに対して `-h` を実行すると、コマンドが表示されます。

### <a name="powershell"></a>PowerShell

Az.Storage プレビュー モジュールでは、不変ストレージがサポートされています。  この機能を有効にするには、次の手順を実行します。

1. `Install-Module PowerShellGet –Repository PSGallery –Force` を実行し、最新バージョンの PowerShellGet がインストールされていることを確認します。
2. Azure PowerShell の以前のインストールを削除します。
3. `Install-Module Az –Repository PSGallery –AllowClobber` を実行し、Azure PowerShell をインストールします。
4. `Install-Module Az.Storage -AllowPrerelease -Repository PSGallery -AllowClobber` を実行し、Azure PowerShell Storage モジュールのプレビュー バージョンをインストールします。

この機能の使用法については、後で「[PowerShell コードの例](#sample-powershell-code)」で説明します。

## <a name="client-libraries"></a>クライアント ライブラリ

Azure Blob Storage の不変ストレージは、次のクライアント ライブラリでサポートされます。

- [.NET クライアント ライブラリ (バージョン 7.2.0-preview 以上)](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/7.2.0-preview)
- [Node.js クライアント ライブラリ (バージョン 4.0.0 以上)](https://www.npmjs.com/package/azure-arm-storage)
- [Python クライアント ライブラリ (バージョン 2.0.0 Release Candidate 2 以上)](https://pypi.org/project/azure-mgmt-storage/2.0.0rc2/)
- [Java クライアント ライブラリ](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/storage/resource-manager/Microsoft.Storage/preview/2018-03-01-preview)

## <a name="supported-values"></a>サポートされている値

- 最小保持間隔は 1 日です。 最大保持間隔は 146,000 日 (400 年) です。
- ストレージ アカウントで、ロック済みの不変ポリシーが適用されたコンテナーの最大数は 1000 です。
- ストレージ アカウントで、訴訟ホールドが設定されたコンテナーの最大数は 1000 です。
- コンテナーで、訴訟ホールド タグの最大数は 10 です。
- 訴訟ホールド タグの最大長は英数字 23 文字です。 最小長は 3 文字です。
- コンテナーで、ロック済みの不変ポリシーの許容される保持間隔の延長の最大回数は 3 回です。
- ロック済みの不変ポリシーが適用されたコンテナーでは、コンテナーの存続期間中、最大 5 個の時間ベースのリテンション ポリシー ログと、最大 10 個の訴訟ホールド ポリシー ログが保持されます。

## <a name="faq"></a>FAQ

**WORM コンプライアンスについてのドキュメントは提供できますか?**

はい。 コンプライアンスを文書化するために、Microsoft は、記録管理と情報ガバナンスに特化した大手の独立系評価会社である Cohasset Associates を保有して、金融サービス業界に固有の要件に対して Azure の BLOB 向け不変ストレージとそのコンプライアンスを評価しました。 Cohasset では、Azure の BLOB 向け不変ストレージが、時間ベースの BLOB を WORM 状態に維持するために使用する場合、CFTC Rule 1.31(c)-(d)、FINRA Rule 4511、および 17a-4 の関連ストレージ要件を満たしていることを実証しました。 この一連のルールは金融機関のレコード保有期間に対する最も規範的なガイダンスを世界中に示すものであるため、Microsoft ではこれらのルールを対象としました。 Cohasset レポートは [Microsoft Service Trust Center](https://aka.ms/AzureWormStorage) で入手できます。

**この機能が適用されるのはブロック BLOB だけですか? それとも、ページ BLOB と追加 BLOB にも適用されますか?**

不変ストレージは任意の BLOB 型で使用できますが、主にブロック BLOB に使用することをお勧めします。 ブロック BLOB とは異なり、ページ BLOB と追加 BLOB は WORM コンテナーの外部で作成し、コンテナー内にコピーする必要があります。 これらの BLOB を WORM コンテナー内にコピーした後は、追加 BLOB への "*追加*" やページ BLOB の変更を行うことはできなくなります。

**この機能を使用するために、新しいストレージ アカウントを作成する必要がありますか?**

いいえ。既存または新しく作成した General Purpose v2 アカウントまたは BLOB ストレージ アカウントで不変ストレージを使用できます。 この機能は GPv2 および BLOB ストレージ アカウント内のブロック BLOB で使用するためのものです。 General Purpose v1 ストレージ アカウントはサポートされていませんが、General Purpose v2 に簡単にアップグレードできます。 既存の General Purpose v1 ストレージ アカウントのアップグレードについては、[ストレージ アカウントのアップグレード](../common/storage-account-upgrade.md)に関する記事を参照してください。

**訴訟ホールドと時間ベースのリテンション ポリシーを両方とも適用することはできますか?**

コンテナーには、訴訟ホールドと時間ベースのリテンション ポリシーの両方を同時に適用できます。 有効なリテンション期間が終了していても、すべての訴訟ホールドがクリアされるまで、そのコンテナー内のすべての BLOB が不変状態のままになります。 逆に、すべての訴訟ホールドがクリアされていても、有効なリテンション期間が終了するまで、BLOB は不変状態のままになります。

**訴訟ホールド ポリシーは訴訟手続き専用ですか? その他の使用のシナリオはありますか?**

いいえ。訴訟ホールドは、時間ベース以外の保持ポリシーに対して使用される一般的な用語にすぎません。 訴訟関連の手続き専用で使用する必要はありません。 訴訟ホールド ポリシーは、保持期間が不明の重要な企業の WORM データを保護するために上書きと削除を無効にする場合に役立ちます。 ミッション クリティカルな WORM ワークロードを保護するための企業のポリシーとして使用することも、カスタムのイベント トリガーで時間ベースのリテンション ポリシーの使用が必要になる前のステージング ポリシーとして使用することもできます。 

**"*ロック済み*" の時間ベースのリテンション ポリシーまたは訴訟ホールドが適用されたコンテナーを削除しようとするとどうなりますか?**

ロック済みの時間ベースのリテンション ポリシーまたは訴訟ホールドが適用された BLOB が 1 つ以上存在すると、Delete Container 操作は失敗します。 保持間隔がアクティブな BLOB が存在せず、訴訟ホールドもない場合にのみ、Delete Container 操作は成功します。 コンテナーを削除するには、事前に BLOB を削除しておく必要があります。

**"*ロック済み*" の時間ベースのリテンション ポリシーまたは訴訟ホールドが適用された WORM コンテナーを含むストレージ アカウントを削除しようとするとどうなりますか?**

訴訟ホールドまたは保持間隔がアクティブな BLOB が格納された WORM コンテナーが 1 つ以上ある場合、ストレージ アカウントの削除は失敗します。 ストレージ アカウントを削除するには、事前にすべての WORM コンテナーを削除しておく必要があります。 コンテナーの削除については、前の質問を参照してください。

**BLOB が不変状態のときに、異なる BLOB 層 (ホット、クール、コールド) の間でデータを移動できますか?**

はい。データを適合した不変状態に保ちながら、Set Blob Tier コマンドを使用して、BLOB 層の間でデータを移動できます。 不変ストレージは、ホット、クール、アーカイブの各 BLOB 層でサポートされています。

**支払いを怠ったときに、リテンション期間がまだ終了していない場合はどうなりますか?**

未払いの場合、Microsoft との契約条件に明記されているように、通常のデータ保有ポリシーが適用されます。

**機能を試してみるだけの試用期間または猶予期間は設けられていますか?**

はい。 時間ベースのリテンション ポリシーを最初に作成したとき、ポリシーは"*ロック解除*"状態です。 この状態にある間は、保持間隔に必要な変更 (延長、短縮など) を加えることができ、ポリシーを削除することもできます。 ポリシーがロックされると、保持間隔の期限が切れるまでロックされたままになります。 このロックされたポリシーにより、保持間隔の削除および変更ができなくなります。 "*ロック解除*"状態は試用の目的でのみ使用し、24 時間以内にポリシーをロックすることを強くお勧めします。 この手順により、SEC 17a-4(f) やその他の規制に準拠することができます。

**不変 BLOB ポリシーと共に論理的な削除を使用できますか?**

はい。 [Azure BLOB ストレージの論理的な削除](storage-blob-soft-delete.md)は、訴訟ホールドや時間ベースのリテンション ポリシーに関係なく、ストレージ アカウント内のすべてのコンテナーに適用されます。 不変 WORM ポリシーが適用されて承認される前に、保護を強化するために論理的な削除を有効にすることをお勧めします。 

**この機能は国内のクラウドや政府機関のクラウドで利用できますか?**

不変ストレージは、Azure パブリック、中国、および Government リージョンで利用可能です。 お客様のリージョンで不変ストレージをご利用いただけない場合は、サポートに連絡して azurestoragefeedback@microsoft.com を電子メールで送信してください。

## <a name="sample-powershell-code"></a>PowerShell コードの例

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

ご参考までに、PowerShell スクリプトの例を次に示します。 このスクリプトでは、新しいストレージ アカウントとコンテナーが作成されます。 そのあと、訴訟ホールドを設定およびクリアする方法、時間ベースのリテンション ポリシー (不変ポリシーとも呼ばれる) を作成およびロックする方法、および保持間隔を延長する方法が示されています。

Azure Storage アカウントを設定してテストします。

```powershell
$ResourceGroup = "<Enter your resource group>”
$StorageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$container2 = "<Enter another container name>”
$location = "<Enter the storage account location>"

# Log in to the Azure Resource Manager account
Login-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $ResourceGroup -Location $location

# Create your Azure storage account
New-AzStorageAccount -ResourceGroupName $ResourceGroup -StorageAccountName `
    $StorageAccount -SkuName Standard_LRS -Location $location -Kind StorageV2

# Create a new container
New-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Create Container 2 with a storage account object
$accountObject = Get-AzStorageAccount -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount
New-AzStorageContainer -StorageAccount $accountObject -Name $container2

# Get a container
Get-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Get a container with an account object
$containerObject = Get-AzStorageContainer -StorageAccount $accountObject -Name $container

# List containers
Get-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount

# Remove a container (add -Force to dismiss the prompt)
Remove-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container2

# Remove a container with an account object
Remove-AzStorageContainer -StorageAccount $accountObject -Name $container2

# Remove a container with a container object
$containerObject2 = Get-AzStorageContainer -StorageAccount $accountObject -Name $container2
Remove-AzStorageContainer -InputObject $containerObject2
```

訴訟ホールドを設定してクリアします。

```powershell
# Set a legal hold
Add-AzStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag1>,<tag2>,...

# with an account object
Add-AzStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>

# with a container object
Add-AzStorageContainerLegalHold -Container $containerObject -Tag <tag4>,<tag5>,...

# Clear a legal hold
Remove-AzStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag2>

# with an account object
Remove-AzStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>,<tag5>

# with a container object
Remove-AzStorageContainerLegalHold -Container $containerObject -Tag <tag4>
```

不変ポリシーを作成または更新します。
```powershell
# with an account name or container name
Set-AzStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container -ImmutabilityPeriod 10

# with an account object
Set-AzStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -ImmutabilityPeriod 1 -Etag $policy.Etag

# with a container object
$policy = Set-AzStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 7

# with an immutability policy object
Set-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -ImmutabilityPeriod 5
```

不変ポリシーを取得します。
```powershell
# Get an immutability policy
Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container

# with an account object
Get-AzStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container

# with a container object
Get-AzStorageContainerImmutabilityPolicy -Container $containerObject
```

不変ポリシーをロックします (プロンプトを無視するには、-Force を追加します)。
```powershell
# with an immutability policy object
$policy = Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
$policy = Lock-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -force

# with an account name or container name
$policy = Lock-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
$policy = Lock-AzStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -Etag $policy.Etag

# with a container object
$policy = Lock-AzStorageContainerImmutabilityPolicy -Container `
    $containerObject -Etag $policy.Etag -force
```

不変ポリシーを拡張します。
```powershell

# with an immutability policy object
$policy = Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container

$policy = Set-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy

# with an account name or container name
$policy = Set-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -ImmutabilityPeriod 11 -Etag $policy.Etag -ExtendPolicy

# with an account object
$policy = Set-AzStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -ImmutabilityPeriod 12 -Etag `
    $policy.Etag -ExtendPolicy

# with a container object
$policy = Set-AzStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 13 -Etag $policy.Etag -ExtendPolicy
```

不変ポリシーを削除します (プロンプトを無視するには、-Force を追加します)。
```powershell
# with an immutability policy object
$policy = Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
Remove-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy

# with an account name or container name
Remove-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
Remove-AzStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -Etag $policy.Etag

# with a container object
Remove-AzStorageContainerImmutabilityPolicy -Container $containerObject `
    -Etag $policy.Etag

```
