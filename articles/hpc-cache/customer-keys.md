---
title: カスタマー マネージド キーを使用して Azure HPC Cache 内のデータを暗号化する
description: 既定の Microsoft のマネージド暗号化キーを使用する代わりに、Azure Key Vault と Azure HPC Cache を使用して暗号化キーへのアクセスを制御する方法
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/20/2020
ms.author: v-erkel
ms.openlocfilehash: e8f1b3fffefcdf1d2ec8bd3e9b1aaea93697ca8a
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/15/2021
ms.locfileid: "103471961"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>Azure HPC Cache にカスタマー マネージド暗号化キーを使用する

Azure Key Vault を使用して、Azure HPC Cache でデータを暗号化するために使用されるキーの所有権を制御できます。 この記事では、キャッシュ データの暗号化にカスタマー マネージド キーを使用する方法について説明します。

> [!NOTE]
> Azure でキャッシュ ディスクなどに格納されているすべてのデータは、既定で Microsoft のマネージド キーを使用して保存時に暗号化されます。 この記事の手順に従うだけで、データの暗号化に使用するキーを管理することができます。

Azure HPC Cache は、キャッシュ ディスクにカスタマー キーを追加した場合でも、キャッシュされたデータを保持しているマネージド ディスクでの [VM ホスト暗号化](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)によっても保護されます。 カスタマー マネージド キーを二重暗号化のために追加することで、セキュリティ ニーズが高いお客様に追加のセキュリティ レベルが提供されます。 詳細については、「[Azure Disk Storage のサーバー側暗号化](../virtual-machines/disk-encryption.md)」を参照してください。

<!-- This feature is available only in some of the Azure regions where Azure HPC Cache is available. Refer to the [Region availability](hpc-cache-overview.md#region-availability) list for details. -->

Azure HPC Cache にカスタマー マネージド キーの暗号化を有効にするには、次の 3 つの手順を行います。

1. キーを格納する Azure Key Vault を設定します。
1. Azure HPC Cache を作成するときに、カスタマー マネージド キーの暗号化を選択し、使用するキー コンテナーとキーを指定します。
1. キャッシュが作成されたら、それに対してキー コンテナーへのアクセスを承認します。

暗号化は、新しく作成したキャッシュから認証してからでないと完全には設定されません (手順 3)。 これは、キー コンテナーにキャッシュの ID を渡して、認証されたユーザーにする必要があるためです。 キャッシュが作成されるまで ID は存在しないため、キャッシュを作成する前にこれを行うことはできません。

キャッシュを作成したら、カスタマー マネージド キーと Microsoft のマネージド キーを切り換えることはできません。 ただし、キャッシュでカスタマー マネージド キーを使用している場合は、必要に応じて暗号化キー、キーのバージョン、キー コンテナーを[変更](#update-key-settings)することができます。

## <a name="understand-key-vault-and-key-requirements"></a>キー コンテナーとキーの要件について

Azure HPC Cache を使用するには、キー コンテナーとキーがこれらの要件を満たしている必要があります。

キー コンテナーのプロパティ:

* **サブスクリプション** - キャッシュに使用されているのと同じサブスクリプションを使用します。
* **リージョン** - キー コンテナーは、Azure HPC Cache と同じリージョンにある必要があります。
* **価格レベル** - Azure HPC Cache で使用するには、Standard レベルで十分です。
* **論理的な削除** - キー コンテナーで論理的な削除がまだ構成されていない場合は、Azure HPC Cache によって有効になります。
* **消去保護** - 消去保護は有効にする必要があります。
* **アクセス ポリシー** - 既定の設定で十分です。
* **ネットワーク接続** - 選択したエンドポイントの設定に関係なく、Azure HPC Cache がキー コンテナーにアクセスできる必要があります。

キーのプロパティ:

* **キーの種類** - RSA
* **RSA キー サイズ** - 2048
* **有効** - はい

キー コンテナーのアクセス許可:

* Azure HPC Cache を作成するユーザーは、[Key Vault の共同作成者ロール](../role-based-access-control/built-in-roles.md#key-vault-contributor)と同等のアクセス許可を持っている必要があります。 Azure Key Vault を設定および管理するには、同じアクセス許可が必要です。

  詳細については、「[キー コンテナーへのアクセスをセキュリティで保護する](../key-vault/general/secure-your-key-vault.md)」を参照してください。

## <a name="1-set-up-azure-key-vault"></a>1.Azure Key Vault を設定する

キー コンテナーとキーの設定は、キャッシュを作成する前に行うことも、キャッシュ作成の一環として行うこともできます。 これらのリソースが[上記で](#understand-key-vault-and-key-requirements)説明した要件を満たしていることを確認します。

キャッシュの作成時に、キャッシュの暗号化に使用するコンテナー、キー、およびキーのバージョンを指定する必要があります。

詳細については、[Azure Key Vault のドキュメント](../key-vault/general/overview.md)を参照してください。

> [!NOTE]
> Azure Key Vault は、Azure HPC Cache と同じサブスクリプションを使用し、同じリージョンに存在する必要があります。 選択するリージョンで、[カスタマー マネージド キー機能がサポートされている](hpc-cache-overview.md#region-availability)ことを確認します。

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2.カスタマー マネージド キーを有効にしてキャッシュを作成する

Azure HPC Cache を作成する際には、暗号化キーのソースを指定する必要があります。 「[Azure HPC Cache を作成する](hpc-cache-create.md)」の手順に従って、 **[ディスク暗号化キー]** ページでキー コンテナーとキーを指定します。 キャッシュの作成時に新しいキー コンテナーとキーを作成できます。

> [!TIP]
> **[ディスク暗号化キー]** ページが表示されない場合は、キャッシュがサポートされているリージョンのいずれかにあることを確認します。

キャッシュを作成するユーザーは、[Key Vault の共同作成者ロール](../role-based-access-control/built-in-roles.md#key-vault-contributor)以上の権限を持っている必要があります。

1. ボタンをクリックして、マネージド キーをプライベートに有効にします。 この設定を変更すると、キー コンテナーの設定が表示されます。

1. **[キー コンテナーの選択]** をクリックしてキーの選択ページを開きます。 このキャッシュのディスクのデータを暗号化するためのキー コンテナーとキーを選択するか作成します。

   Azure Key Vault がリストに表示されない場合は、次の要件を確認してください。

   * キャッシュはキー コンテナーと同じサブスクリプションにありますか?
   * キャッシュはキー コンテナーと同じリージョンにありますか?
   * Azure portal とキー コンテナーの間にネットワーク接続がありますか?

1. コンテナーを選択したら、使用可能なオプションから個々のキーを選択するか、新しいキーを作成します。 キーは、2,048 ビット RSA キーである必要があります。

1. 選択したキーのバージョンを指定します。 バージョン管理の詳細については、[Azure Key Vault のドキュメント](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning)を参照してください。

残りの仕様に進み、「[Azure HPC Cache を作成する](hpc-cache-create.md)」の説明に従ってキャッシュを作成します。

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3.キャッシュから Azure Key Vault の暗号化を承認する
<!-- header is linked from create article, update if changed -->

数分後に、Azure portal に新しい Azure HPC Cache が表示されます。 **[概要]** ページにアクセスして、Azure Key Vault へのアクセスを承認し、カスタマー マネージド キーの暗号化を有効にします

> [!TIP]
> キャッシュは、"デプロイが進行中です" のメッセージがクリアされる前に、リソースの一覧に表示される場合があります。 1 - 2 分経過したら、成功の通知を待たずに、ご自分のリソース一覧を確認してください。

この 2 段階のプロセスが必要な理由は、Azure HPC Cache インスタンスでは承認のために Azure Key Vault に渡す ID が必要だからです。 キャッシュ ID は、最初の作成手順が完了するまで存在しません。

> [!NOTE]
> キャッシュを作成してから 90 分以内に暗号化を承認する必要があります。 この手順を完了しないと、キャッシュがタイムアウトになって失敗します。 失敗したキャッシュは、修正できないため、再作成する必要があります。

キャッシュには、状態 **[Waiting for key]\(キーの待機中\)** が表示されます。 ページの上部にある **[暗号化を有効にする]** ボタンをクリックして、キャッシュに指定したキー コンテナーへのアクセスを承認します。

![[暗号化を有効にする] ボタン (一番上の行) と [状態]: [キーの待機中] が強調表示されている、ポータルのキャッシュの概要ページのスクリーンショット](media/waiting-for-key.png)

暗号化キーの使用をキャッシュに承認するには、 **[暗号化を有効にする]** をクリックして、 **[はい]** ボタンをクリックします。 また、この操作により、キー コンテナーで論理的な削除と消去保護が有効になります (まだ有効になっていない場合)。

![上部に、ユーザーに [はい] をクリックして暗号化を有効にするように求めるバナー メッセージが表示されたポータルのキャッシュの概要ページのスクリーンショット](media/enable-keyvault.png)

キャッシュによりキー コンテナーへのアクセスが要求されると、キャッシュ データを格納するディスクを作成および暗号化できるようになります。

暗号化を承認すると、Azure HPC Cache では、暗号化されたディスクと関連するインフラストラクチャを作成するためにのセットアップにさらに数分かかります。

## <a name="update-key-settings"></a>キー設定を更新する

キャッシュのキー コンテナー、キー、またはキーのバージョンは、Azure portal から変更できます。 キャッシュの **[暗号化]** 設定リンクをクリックして、 **[カスタマー キーの設定]** ページを開きます

カスタマー マネージド キーとシステム マネージド キーの間でキャッシュを変更することはできません。

![Azure portal の [キャッシュ] ページで [設定] > [暗号化] をクリックすると表示される、[カスタマー キーの設定] ページのスクリーンショット](media/change-key-click.png)

**[キーの変更]** リンクをクリックし、 **[Change the key vault, key, or version]\(キー コンテナー、キー、またはバージョンの変更\)** をクリックしてキー セレクターを開きます。

![キー コンテナー、キー、バージョンを選択する 3 つのドロップダウン セレクターがある [Azure Key Vault からのキーの選択] ページのスクリーンショット](media/select-new-key.png)

このキャッシュと同じサブスクリプションと同じリージョンのキー コンテナーがリストに表示されます。

新しい暗号化キーの値を選択したら、 **[選択]** をクリックします。 新しい値を含む確認ページが表示されます。 **[保存]** をクリックして、選択を確定します。

![左上に [保存] ボタンがある確認ページのスクリーンショット](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>Azure のカスタマー マネージド キーについて確認する

これらの記事では、Azure Key Vault とカスタマー マネージド キーを使用して Azure でデータを暗号化する方法が詳しく説明されています。

* [ストレージ暗号化の概要](../storage/common/storage-service-encryption.md)
* [カスタマー マネージド キーを使用したディスク暗号化](../virtual-machines/disk-encryption.md#customer-managed-keys) - Azure Key Vault をマネージド ディスクと共に使用した Azure HPC Cache と類似のシナリオのドキュメント

## <a name="next-steps"></a>次のステップ

Azure HPC Cache を作成し、Key Vault ベースの暗号化を承認したら、キャッシュにデータ ソースへのアクセス権を付与して設定を続行します。

* [ストレージ ターゲットを追加する](hpc-cache-add-storage.md)