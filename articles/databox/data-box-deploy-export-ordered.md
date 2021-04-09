---
title: Azure Data Box からデータをエクスポートするチュートリアル | Microsoft Docs
description: デプロイの前提条件と、Azure Data Box からデータをエクスポートする方法について説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 12/18/2020
ms.author: alkohli
ms.openlocfilehash: 42476e2689cc503edc19e8e299a01ce922f1bf42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98789197"
---
# <a name="tutorial-create-export-order-for-azure-data-box"></a>チュートリアル:Azure Data Box のエクスポート注文を作成する

Azure Data Box は、Azure から特定の場所にデータを移動できるハイブリッド ソリューションです。 このチュートリアルでは、Azure Data Box のエクスポート注文を作成する方法について説明します。 エクスポート注文を作成する主な理由は、オンプレミスのストレージが侵害され、バックアップを復元する必要がある場合のディザスター リカバリーのためです。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> * エクスポートの前提条件
> * エクスポート用の Data Box の注文
> * エクスポート注文の追跡
> * エクスポート注文のキャンセル

## <a name="prerequisites"></a>前提条件

デバイスを注文する前に、Data Box サービスとデバイスに関する次の構成の前提条件を完了してください。

### <a name="for-service"></a>サービスでは

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

* Azure Data Box で使用できる既存のリソース グループがあることを確認します。

* データをエクスポートする Azure Storage アカウントが、[Data Box でサポートされるストレージ アカウント](data-box-system-requirements.md#supported-storage-accounts)で説明されている、サポートされているストレージ アカウントの種類の 1 つであることを確認してください。

### <a name="for-device"></a>デバイスでは

開始する前に次の点を確認します。

* データセンター ネットワークに接続されているホスト コンピューターがあること。 Azure Data Box からこのコンピューターにデータをコピーします。 ホスト コンピューターでは、「[Azure Data Box system requirements](data-box-system-requirements.md)」(Azure Data Box のシステム要件) に記載されている、サポートされるオペレーティング システムが実行されている必要があります。

* お客様のデータセンターには、高速ネットワークが必要です。 10 GbE 接続を少なくとも 1 つ利用することを強くお勧めします。 10 GbE 接続を利用できない場合は、1 GbE データ リンクを使用できますが、コピーの速度が影響を受けます。

## <a name="order-data-box-for-export"></a>エクスポート用の Data Box の注文

デバイスを注文するには、Azure portal で次の手順を実行します。

1. Microsoft Azure 資格情報を使用して、次の URL にサインインします。[https://portal.azure.com](https://portal.azure.com)

2. **[+ リソースの作成]** を選択し、*Azure Data Box* を検索します。 **[Azure Data Box]** を選択します。

   ![リソースの作成](media/data-box-deploy-export-ordered/azure-data-box-export-order-create-resource.png)

3. **［作成］** を選択します

   ![Azure Data Box リソースを作成する](media/data-box-deploy-export-ordered/azure-data-box-export-order-create-data-box-resource.png)

4. ご利用のリージョンで Azure Data Box サービスが利用可能かどうかを確認します。 次の情報を入力または選択し、 **[適用]** を選択します。

    |設定  |値  |
    |---------|---------|
    |転送の種類     | **[Azure にエクスポート]** を選択します。        |
    |サブスクリプション     | Data Box サービス用の EA、CSP、または Azure スポンサー サブスクリプションを選択します。 <br> サブスクリプションは、課金アカウントにリンクされます。       |
    |Resource group     |    既存のリソース グループを選択します。 <br> リソース グループとは、まとめて管理したり、デプロイしたりできるリソースの論理コンテナーです。         |
    |Source Azure region (ソース Azure リージョン)    |    データが現在存在する Azure リージョンを選択します。         |
    |Destination country (宛先の国)     |     デバイスを発送する国を選択します。        |

   ![Data Box の設定の選択](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-box-settings.png)

5. **[Data Box]** を選択します。 1 つの注文での使用可能な最大容量は 80 TB です。 データ サイズがこれより大きい場合は、複数の注文を作成できます。

   ![Data Box の容量の選択](media/data-box-deploy-export-ordered/azure-data-box-export-order-capacity.png)

6. **[Order]\(注文\)** で **[Basic]\(基本\)** の注文情報を指定します。 次の情報を入力または選択します。

    |設定  |値  |
    |---------|---------|
    |サブスクリプション     | サブスクリプションは、前の選択に基づいて自動的に設定されます。|
    |Resource group | 前に選択したリソース グループ。 |
    |Export order name (エクスポート注文名)     |  注文を追跡するためのフレンドリ名を指定します。 <br> 名前の長さは 3 ～ 24 文字で、文字、数字、ハイフンを使うことができます。 <br> 名前の最初と最後は、文字か数字とする必要があります。      |

    ![Export order basics (エクスポート注文の基本)](media/data-box-deploy-export-ordered/azure-data-box-export-order-basics-order-name.png)

    **[Next:Data Selection]\(次へ: データの選択\)** を選択して続行します。

7. **[Data Selection]\(データの選択\)** で、 **[Add storage account and export type]\(ストレージ アカウントとエクスポートの種類を追加\)** を選択します。

    ![ストレージ アカウントとエクスポートの種類を追加](media/data-box-deploy-export-ordered/azure-data-box-export-order-basics-add-storage.png)

8. **[Select export option]\(エクスポート オプションの選択\)** で、エクスポート オプションの詳細を指定します。 次の情報を入力または選択し、 **[追加]** を選択します。

    |設定  |値  |
    |---------|---------|
    |ストレージ アカウント     | エクスポートするデータがある Azure Storage アカウント。 |
    |Export type (エクスポートの種類)     | エクスポートするデータの種類を **[All objects]\(すべてのオブジェクト\)** および **[Use XML file]\(XML ファイルを使用\)** から指定します。<ul><li> **[All objects]\(すべてのオブジェクト\)** - **[Transfer options]\(転送オプション\)** の選択に応じて、ジョブですべてのデータをエクスポートするように指定します。</li><li> **[Use XML file]\(XML ファイルを使用\)** - ストレージ アカウントからエクスポートする BLOB またはファイルのパスとプレフィックスのセットを含む XML ファイルを指定します。 選択したストレージ アカウントのコンテナーには XML ファイルが含まれている必要があります。ファイル共有から選択することは現在サポートされていません。 ファイルは、空でない .xml ファイルである必要があります。</li></ul>        |
    |Transfer options (転送オプション)     |  **[Select all]\(すべて選択\)** 、 **[All BLOB]\(すべての BLOB\)** 、および **[All files]\(すべてのファイル\)** からデータ転送オプションを指定します。 <ul><li> **[Select All]\(すべて選択\)** - すべての BLOB と Azure Files をエクスポートすることを指定します。 BLOB のみがサポートされているストレージ アカウント (Blob ストレージ アカウント) を使用している場合は、 **[All Files]\(すべてのファイル\)** オプションは選択できません。</li><li> **[All BLOB]\(すべての BLOB\)** - ブロック BLOB とページ BLOB のみエクスポートすることを指定します。</li><li> **[All Files]\(すべてのファイル\)** - BLOB を除くすべてのファイルをエクスポートすることを指定します。 使用しているストレージ アカウントの種類 (GPv1 と GPv2、Premium Storage、または BLOB ストレージ) によって、エクスポートできるデータの種類が決まります。 詳細については、[エクスポートでサポートされるストレージ アカウント](../import-export/storage-import-export-requirements.md#supported-storage-types)に関する記事を参照してください。</li></ul>         |
    |Include verbose log (詳細ログを含める)     | 正常にエクスポートされたすべてのファイルの一覧を含む詳細ログ ファイルが必要かどうかを示します。        |

    > [!NOTE]
    >
    > **[Export type]\(エクスポートの種類\)** 設定で **[Use XML file]\(XML ファイルを使用\)** を選択した場合は、XML に有効なパスまたはプレフィックスが含まれていることを確認する必要があります。 XML ファイルを構築して指定する必要があります。  ファイルが無効な場合、または指定されたパスと一致するデータがない場合は、一部のデータまたはすべてのデータがエクスポートされずに終了します。

    XML ファイルをコンテナーに追加する方法については、「[XML ファイルを使用した注文のエクスポート](data-box-deploy-export-ordered.md#export-order-using-xml-file)」を参照してください。

   ![Select export option (エクスポート オプションの選択)](media/data-box-deploy-export-ordered/azure-data-box-export-order-export-option.png)

   XML 入力の例については、[XML 入力のサンプル](data-box-deploy-export-ordered.md#sample-xml-file)を参照してください

9. **[Data Selection]\(データの選択\)** で、設定を確認し、 **[Next:Security>]\(次へ: セキュリティ>\)** を選択して続行します。

   ![エクスポート注文、データ選択](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-selection.png)

    **[セキュリティ]** 画面では、自分の暗号化キーを使用でき、二重暗号化の使用を選択できます。

    **[セキュリティ]** 画面の設定はすべてオプションです。 設定を変更しない場合は、既定の設定が適用されます。

    ![Data Box インポート注文ウィザードの [セキュリティ] 画面](media/data-box-deploy-export-ordered/data-box-export-security-01.png)

10. 独自のカスタマー マネージド キーを使用して新しいリソースのロック解除パスキーを保護する場合は、 **[暗号化の種類]** を展開します。

    Azure Data Box のカスタマー マネージド キーの構成は省略可能です。 Data Box では、既定で Microsoft マネージド キーを使用してロック解除パスキーが保護されます。

    カスタマー マネージド キーは、デバイス上のデータの暗号化方法には影響しません。 キーは、デバイスのロック解除パスキーを暗号化するためにのみ使用されます。

    カスタマー マネージド キーを使用しない場合は、手順 16 に進んでください。

    ![[暗号化の種類] 設定を示すセキュリティ画面](./media/data-box-deploy-export-ordered/customer-managed-key-01.png)

11. キーの種類として **[カスタマー マネージド キー]** を選択します。 次に、 **[キー コンテナーとキーを選択します]** を選択します。
   
    ![セキュリティ画面。カスタマー マネージド キーの設定](./media/data-box-deploy-export-ordered/customer-managed-key-02.png)

12. **[Azure Key Vault からのキーの選択]** 画面で、サブスクリプションが自動的に設定されます。

    - **[キー コンテナー]** で、ドロップダウン リストから既存のキー コンテナーを選択できます。

      ![[Select key from Azure Key Vault]\(Azure Key Vault からのキーの選択\) 画面](./media/data-box-deploy-export-ordered/customer-managed-key-03.png)

    - **[新規作成]** を選択して、新しいキー コンテナーを作成することもできます。 **[キー コンテナーの作成]** 画面で、リソース グループとキー コンテナーの名前を入力します。 **[論理的な削除]** と **[消去保護]** が有効になっていることを確認します。 他のすべての既定値をそのまま使用し、 **[確認および作成]** を選択します。

      ![新しい Azure キー コンテナーの作成設定](./media/data-box-deploy-export-ordered/customer-managed-key-04.png)

      キー コンテナーの情報を確認し、 **[作成]** を選択します。 キー コンテナーの作成が完了するまで数分待ちます。

      ![新しい Azure キー コンテナーの確認画面](./media/data-box-deploy-export-ordered/customer-managed-key-05.png)

13. **[Azure Key Vault からのキーの選択]** 画面では、キー コンテナー内の既存のキーを選択できます。

    ![Azure Key Vault から既存のキーを選択する](./media/data-box-deploy-export-ordered/customer-managed-key-06.png)

    新しいキーを作成する場合は、 **[新規作成]** を選択します。 RSA キーを使用する必要があります。 サイズは 2,048 以上にすることができます。 新しいキーの名前を入力し、他の既定値をそのまま使用して、 **[作成]** を選択します。

      ![新しいキーの作成オプション](./media/data-box-deploy-export-ordered/customer-managed-key-07.png)

      キーがキー コンテナーに作成されると通知されます。

14. 使用するキーの **バージョン** を選択し、 **[選択]** を選択します。

      ![キー コンテナーで作成された新しいキー](./media/data-box-deploy-export-ordered/customer-managed-key-08.png)

    新しいキー バージョンを作成する場合は、 **[新規作成]** を選択します。

    ![新しいキー バージョンを作成するためのダイアログ ボックスを開く](./media/data-box-deploy-export-ordered/customer-managed-key-08-a.png)

    **[Create new key]\(新しいキーの作成\)** 画面で、新しいキー バージョンの設定を選択し、 **[作成]** を選択します。

    ![新しいキー バージョンを作成する](./media/data-box-deploy-export-ordered/customer-managed-key-08-b.png)

    **[セキュリティ]** 画面の **[暗号化の種類]** 設定に、キー コンテナーとキーが表示されます。

    ![カスタマー マネージド キーのキーとキー コンテナー](./media/data-box-deploy-export-ordered/customer-managed-key-09.png)

15. このリソースへのアクセスを管理するために使用するユーザー ID を選択します。 **[Select a user identity]\(ユーザー ID を選択する\)** を選択します。 右側のパネルで、使用するサブスクリプションとマネージド ID を選択します。 次に **[選択]** を選択します。

    ユーザー割り当てマネージド ID は、複数のリソースを管理するために使用できるスタンドアロンの Azure リソースです。 詳細については、[マネージド ID の種類](../active-directory/managed-identities-azure-resources/overview.md)に関するページを参照してください。  

    新しいマネージド ID を作成する必要がある場合は、「[Azure portal を使用してユーザー割り当てマネージド ID を作成、一覧表示、削除したり、それにロールを割り当てたりする](../../articles/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)」のガイダンスに従ってください。
    
    ![[Select a user identity]\(ユーザー ID を選択する\)](./media/data-box-deploy-export-ordered/customer-managed-key-10.png)

    ユーザー ID が **[暗号化の種類]** の設定に表示されます。

    **[暗号化の種類]** の設定は折りたたんでかまいません。

    ![選択したユーザー ID が [暗号化の種類] の設定に表示される](./media/data-box-deploy-export-ordered/customer-managed-key-11.png)

16. ソフトウェアベースの二重暗号化を有効にしたい場合は、 **[Double encryption (for high-security environments)]\(二重暗号化 (高度にセキュリティ保護された環境用)\)** を展開し、 **[Enable double encryption for the order]\(この注文に関して二重暗号化を有効にする\)** を選択します。 

    Data Box 上のデータに対し、AES-256 ビットの暗号化に加えて、ソフトウェアベースの暗号化が実行されます。

    > [!NOTE]
    > このオプションを有効にすると、注文の処理とデータのコピーにかかる時間が長くなることがあります。 注文後に、このオプションを変更することはできません。

    ![Data Box インポートの [セキュリティ] 画面 (二重暗号化)](media/data-box-deploy-export-ordered/azure-data-box-export-order-security-double-encryption.png)

    **[Next:連絡先の詳細]** を選択して続行します。

11. **[Contact Details]\(連絡先の詳細\)** で、 **[+ Add Shipping Address]\(+ 配送先住所の追加\)** を選択して発送情報を入力します。

    ![Add shipping address (配送先住所の追加)](media/data-box-deploy-export-ordered/azure-data-box-export-order-add-shipping-address.png)

12. **[Add Shipping Address]\(配送先住所の追加\)** に、お客様の氏名、会社の名前と郵送先住所、および有効な電話番号を入力します。 **[検証]** を選択します。 配送先住所でサービスが利用可能かどうかが確認されます。 指定した配送先住所でサービスを利用できる場合は、その旨が通知されます。

    ![配送先住所の検証 (Validate shipping address)](media/data-box-deploy-export-ordered/azure-data-box-export-order-validate-shipping-address.png)

    自己管理型の発送が利用可能なリージョンで注文する場合は、このオプションを選択できます。 自己管理の出荷の詳細については、[自己管理の出荷の使用](data-box-portal-customer-managed-shipping.md)に関するページをご覧ください。

13. 配送の詳細が正常に検証されたら、 **[Add shipping address]\(配送先住所の追加\)** を選択します。

14. **[Contact Details]\(連絡先の詳細\)** で、配送先住所とメール アドレスを確認します。 指定した電子メール アドレスに、注文の状態の更新に関する電子メール通知が送信されます。

    グループの管理者が辞めた場合も引き続き通知を受け取ることができるように、グループ メールを使用することをお勧めします。

    ![連絡先の詳細](media/data-box-deploy-export-ordered/azure-data-box-export-order-contact-details.png)

15. **[Next:Review + Order>]\(次へ: 確認と注文\)** を選択します。 注文の作成を続行するには、使用条件に同意する必要があります。

16. **[注文]** を選択します。 注文が作成されるまで数分かかります。

    ![注文の確定](media/data-box-deploy-export-ordered/azure-data-box-select-export-order-commit-order.png)

## <a name="export-order-using-xml-file"></a>XML ファイルを使用した注文のエクスポート

**[Use XML file]\(XML ファイルを使用\)** を選択した場合は、エクスポートする特定のコンテナーと BLOB (ページおよびブロック) を指定できます。 XML の書式設定については、[サンプル XML ファイル テーブル](#sample-xml-file)を参照してください。 次の手順は、データのエクスポートに XML ファイルを使用する方法を示しています。

1. **[Export type]\(エクスポートの種類\)** には、 **[Use XML file]\(XML ファイルを使用\)** を選択します。 これは、エクスポートする特定の BLOB と Azure ファイルを指定する XML ファイルです。 XML ファイルを追加するには、 **[Click here to select an XML file]\(ここをクリックして XML ファイルを選択\)** を選択します。

     ![エクスポート オプションの選択、XML](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-select-xml-option.png)

2. **[+ Container]\(+ コンテナー\)** を選択して、コンテナーを作成します。

    ![エクスポート オプションの選択、コンテナー](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-containers-option.png)

3. Azure portal の右側から表示される **[New Container]\(新しいコンテナー\)** タブで、コンテナーの名前を追加します。 名前は小文字にする必要があり、数字とダッシュ '-' を含めることができます。 次に、ドロップダウン リスト ボックスから **[Public access level]\(パブリック アクセス レベル\)** を選択します。 他のユーザーがデータにアクセスできないようにするため、 **[Private (non anonymous access)]\(プライベート (非匿名アクセス))\** を選択することをお勧めします。 コンテナーのアクセス レベルの詳細については、[コンテナーのアクセス許可](../storage/blobs/anonymous-read-access-configure.md#set-the-public-access-level-for-a-container)に関する記事を参照してください。

   ![エクスポート オプションの選択、新しいコンテナー設定](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-container-settings.png)

4. **［作成］** を選択します

   ![エクスポート オプションの選択、新しいコンテナーの作成。](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-create-container.png)

   コンテナーが正常に作成されると、次のメッセージが表示されます。

   ![コンテナーは正常に作成されました](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-container-success.png)

5. 作成したコンテナーを選択し、ダブルクリックします。

   ![コンテナーの詳細を表示する](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-view-container-details.png)

6. コンテナーをダブルクリックすると、コンテナーのプロパティ ビューが表示されます。 ここで、エクスポートする BLOB および、または Azure Files の一覧が含まれる XML ファイルを添付 (あるいは参照) します。 **[アップロード]** を選択します。

   ![コンテナーに BLOB をアップロードする](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-blob-to-container.png)

7. これで、XML ファイルがコンテナーに追加されました。 この XML で指定した BLOB と Azure Files のみがエクスポートされます。

   ![コンテナーに追加された XML ファイル](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-added-to-container.png)

## <a name="track-the-order"></a>注文を追跡する

注文後、Azure portal から注文の状態を追跡できます。 Data Box の注文に移動し、 **[概要]** に移動して状態を表示します。 ポータルでは、注文は、 **[注文済み]** 状態で表示されます。

デバイスの準備が完了すると、選択したストレージ アカウントからデータのコピーが開始されます。 ポータルには、 **[Data copy in progress]\(データのコピーが進行中\)** 状態の注文が表示されます。

![Data Box エクスポート注文、データ コピーが進行中](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-copy-in-progress.png)

Data Box によってソース ストレージ アカウントからデータがコピーされます。 データのコピーが完了すると Data Box がロックされ、ポータルに **[Copy Completed]\(コピー完了\)** 状態の注文が表示されます。

![Data Box エクスポート注文、データ コピー完了](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-copy-complete.png)

デバイスが使用できない場合は、通知されます。 デバイスが使用可能な場合は、Microsoft が配送用のデバイスを特定し、配送の準備をします。 デバイスの準備中に、次のアクションが実行されます。

* デバイスに関連付けられているストレージ アカウントごとに SMB 共有が作成されます。
* 各共有で、ユーザー名やパスワードなどのアクセス資格情報が生成されます。
* デバイスはロックされ、デバイスのロック解除パスワードを使用してのみアクセスできます。 パスワードを取得するには、Azure portal アカウントにログインし、 **[デバイスの詳細]** を選択する必要があります。

次に、Microsoft は、デバイスを準備して地域の運送業者を通じて発送します。 デバイスが出荷されると、お客様に追跡番号が送信されます。 ポータルには、**出荷済み** 状態の注文が表示されます。

![出荷済みの Data Box のエクスポート注文](media/data-box-deploy-export-ordered/azure-data-box-export-order-dispatched.png)

自己管理型の発送を選択した場合、データセンターからデバイスを受け取る準備が整うと、次の手順が記載されたメール通知が送信されます。 自己管理型の発送の詳細については、[自己管理型の発送](data-box-portal-customer-managed-shipping.md)に関するページを参照してください。

![引き取り準備が完了した自己管理型の発送](media/data-box-deploy-export-ordered/azure-data-box-export-order-ready-for-pickup.png)

## <a name="cancel-the-order"></a>注文をキャンセルする

この注文をキャンセルするには、Azure portal で **[概要]** に移動し、コマンド バーの **[キャンセル]** を選択します。

注文は、注文した後、注文処理が開始されるまでの任意の時点でキャンセルできます。

キャンセルされた注文を削除するには、 **[概要]** に移動し、コマンド バーの **[削除]** を選択します。

## <a name="sample-xml-file"></a>サンプル XML ファイル

次の XML は、 **[Use XML file]\(XML ファイルを使用\)** オプションを選択したときにエクスポート注文で使用される BLOB 名、BLOB プレフィックス、XML 形式の Azure Files の例を示しています。

```xml
<?xml version="1.0" encoding="utf-8"?>
   <!-- BlobList/prefix/Container list for Blob storage for export  -->
   <BlobList>
      <BlobPath>/8tbpageblob/8tbpageblob/8tbpageblob</BlobPath>
      <BlobPathPrefix>/blockblob4dot75tbdata/</BlobPathPrefix>
      <BlobPathPrefix>/1tbfilepageblob</BlobPathPrefix>
      <BlobPathPrefix>/1tbfile/</BlobPathPrefix>
      <BlobPathPrefix>/8mbfiles/</BlobPathPrefix>
      <BlobPathPrefix>/64mbfiles/</BlobPathPrefix>
   </BlobList>
   <!-- FileList/prefix/Share list for Azure File storage for export  -->
   <AzureFileList>
      <FilePathPrefix>/64mbfiles/</FilePathPrefix>
      <FilePathPrefix>/4mbfiles/prefix2/subprefix</FilePathPrefix>
      <FilePathPrefix>/1tbfile/prefix</FilePathPrefix>
   </AzureFileList>
```

XML ファイルに関して重要な点は次のとおりです。

* XML タグでは大文字と小文字が区別され、上記の例で指定したとおりに正確に一致する必要があります。
* 開始タグと終了タグは一致している必要があります。
* XML タグまたは書式指定が正しくないと、データのエクスポートに失敗する可能性があります。
* BLOB またはファイルのプレフィックスが無効である場合、データはエクスポートされません。

### <a name="examples-of-valid-blob-paths"></a>有効な BLOB パスの例

次の表は、有効な BLOB のパスの例を示しています。

   | セレクター | BLOB パス | 説明 |
   | --- | --- | --- |
   | 次で始まる |/ |ストレージ アカウントのすべての BLOB をエクスポートする |
   | 次で始まる |/$root/ |ルート コンテナー内のすべての BLOB をエクスポートする |
   | 次で始まる |/containers |任意のコンテナー内の、プレフィックス **containers** で始まるすべての BLOB をエクスポートする |
   | 次で始まる |/container-name/ |コンテナー **container-name** 内のすべての BLOB をエクスポートする |
   | 次で始まる |/container-name/prefix |プレフィックス **prefix** で始まるコンテナー **container-name** 内のすべての BLOB をエクスポートする |
   | 等しい |$root/logo.bmp |ルート コンテナー内の BLOB " **logo.bmp** " をエクスポートする |
   | 等しい |8tbpageblob/mydata.txt |コンテナー **8tbpageblob** 内の BLOB **mydata.txt** をエクスポートする |

## <a name="sample-log-files"></a>サンプル ログ ファイル

このセクションでは、エクスポート時に生成されるサンプル ログ ファイルについて説明します。 エラー ログは自動的に生成されます。 詳細ログ ファイルを生成するには、エクスポート注文を構成するときに、Azure portal で **[Include verbose log]\(詳細ログを含める\)** を選択する必要があります。
コピー ログと詳細ログの詳細については、[コピー ログ](data-box-deploy-export-copy-data.md#copy-data-from-data-box)に関する記事を参照してください。

<!-- ### Verbose log

The following log files show examples of verbose logging when you select **Include verbose log**:

```xml
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820"></File>
```

### Copy logs

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box). -->

<!-- The following xml shows an example of the copy log when the export is successful:

```xml
<CopyLog Summary="Summary">
  <Status>Succeeded</Status>
    <TotalFiles_Blobs>27</TotalFiles_Blobs>
    <FilesErrored>0</FilesErrored>
</CopyLog>
```

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box).

The following xml shows an example of the copy log when the export has errors:

```xml
<ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>4</TotalFiles_Blobs>
  <FilesErrored>3</FilesErrored>
</CopyLog>
``` -->

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Data Box に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
>
> * エクスポートの前提条件
> * エクスポート用の Data Box の注文
> * エクスポート注文の追跡
> * エクスポート注文のキャンセル

次のチュートリアルに進んで、Data Box を設定する方法を学習してください。

> [!div class="nextstepaction"]
> [Azure Data Box を設定する](./data-box-deploy-set-up.md)