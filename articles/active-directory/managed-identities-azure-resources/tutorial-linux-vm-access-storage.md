---
title: チュートリアル`:`マネージド ID を使用して Azure Storage にアクセスする - Linux - Azure AD
description: Linux VM のシステム割り当てマネージド ID を使用して Azure Storage にアクセスするプロセスについて説明するチュートリアルです。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/23/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4c7612188043be070ead92c88838b567b22787d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98131272"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-storage"></a>チュートリアル: Linux VM のシステム割り当てマネージド ID を使用して Azure Storage にアクセスする 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

このチュートリアルでは、Linux 仮想マシン (VM) のシステム割り当てマネージド ID を使用して Azure Storage にアクセスする方法について説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * ストレージ アカウントの作成
> * ストレージ アカウントに BLOB コンテナーを作成する
> * Linux VM のマネージド ID に Azure Storage コンテナーへのアクセス権を付与します
> * アクセス トークン取得し、それを使用して Azure Storage を呼び出す

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

このチュートリアルの CLI スクリプトの例を実行するには、次の 2 つの方法があります。

- Azure Portal から、または各コード ブロックの右上隅にある **[使ってみる]** ボタンを使用して、[Azure Cloud Shell](~/articles/cloud-shell/overview.md) を使用します。
- ローカル CLI コンソールを使用する場合は、[CLI 2.0 の最新バージョン (2.0.23 以降) をインストール](/cli/azure/install-azure-cli)します。

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成 

このセクションでは、ストレージ アカウントを作成します。 

1. Azure Portal の左上隅にある **[+ リソースの作成]** ボタンをクリックします。
2. **[ストレージ]** 、 **[ストレージ アカウント - Blob、File、Table、Queue]** の順にクリックします。
3. **[名前]** で、ストレージ アカウントの名前を入力します。  
4. **[デプロイ モデル]** と **[アカウントの種類]** がそれぞれ **[Resource manager]** と **[ストレージ (汎用 v1)]** に設定されている必要があります。 
5. **[サブスクリプション]** と **[リソース グループ]** が、前の手順で VM を作成したときに指定したものと一致していることを確認します。
6. **Create** をクリックしてください。

    ![新しいストレージ アカウントを作成する](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>BLOB コンテナーを作成し、ファイルをストレージ アカウントにアップロードする

ファイルには Blob Storage が必要であるため、ファイルを格納する BLOB コンテナーを作成する必要があります。 次に、新しいストレージ アカウントで、BLOB コンテナーにファイルをアップロードします。

1. 新たに作成したストレージ アカウントに戻ります。
2. **[Blob service]** の **[コンテナー]** をクリックします。
3. ページの上部にある **[+ コンテナー]** をクリックします。
4. **[新しいコンテナー]** で、コンテナーの名前を入力し、 **[パブリック アクセス レベル]** で既定値を保持します。

    ![ストレージ コンテナーの作成](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. 任意のエディターを使用して、ローカル コンピューターに *hello world.txt* という名前のファイルを作成します。  ファイルを開き、"Hello world! :)" というテキストを (引用符なしで) 追加し、保存します。 

6. 新しく作成したコンテナーにファイルをアップロードします。コンテナー名をクリックしてから **[アップロード]** をクリックします。
7. **[BLOB のアップロード]** ウィンドウの **[ファイル]** で、フォルダー アイコンをクリックし、ローカル コンピューターの **hello_world.txt** を参照してファイルを選択して、 **[アップロード]** をクリックします。

    ![テキスト ファイルをアップロードする](./media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-vm-access-to-an-azure-storage-container"></a>VM に Azure Storage コンテナーへのアクセスを許可する 

VM のマネージド ID を使用して、Azure Storage Blob のデータを取得できます。

>[!NOTE]
> ストレージのレビューにアクセス許可を付与するために使用できるさまざまなロールの詳細については、「[Azure Active Directory を使用して BLOB とキューへのアクセスを承認する](../../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights)」を参照してください。

1. 新たに作成したストレージ アカウントに戻ります。  
2. 左側のパネルの **[アクセス制御 (IAM)]** リンクをクリックします。  
3. ページの上部にある **[+ ロール割り当ての追加]** をクリックして、VM 用に新しいロールの割り当てを追加します。
4. **[ロール]** で、ドロップダウンから **[ストレージ BLOB データ閲覧者]** を選択します。 
5. 次のドロップダウンで、 **[アクセスの割り当て先]** として **[仮想マシン]** を選択します。  
6. 次に、適切なサブスクリプションが **[サブスクリプション]** ドロップダウンにリストされていることを確認してから、 **[リソース グループ]** を **[すべてのリソース グループ]** に設定します。  
7. **[選択]** で、VM を選択し、 **[保存]** をクリックします。

    ![アクセス許可の割り当て](./media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="get-an-access-token-and-use-it-to-call-azure-storage"></a>アクセス トークン取得し、それを使用して Azure Storage を呼び出す

Azure Storage は Azure AD 認証をネイティブにサポートするため、マネージド ID を使用して取得したアクセス トークンを直接受け入れることができます。 これは Azure Storage の Azure AD との統合の一部であり、接続文字列に資格情報を提供することとは異なります。

次の手順を完了するには、前に作成した VM から行う必要があり、それに接続するには SSH クライアントが必要です。 Windows を使用している場合は、[Windows Subsystem for Linux](/windows/wsl/about) で SSH クライアントを使用することができます。 SSH クライアント キーの構成について支援が必要な場合は、「[Azure 上の Windows で SSH キーを使用する方法](~/articles/virtual-machines/linux/ssh-from-windows.md)」または「[Azure に Linux VM 用の SSH 公開キーと秘密キーのペアを作成して使用する方法](~/articles/virtual-machines/linux/mac-create-ssh-keys.md)」をご覧ください。

1. Azure portal で **[Virtual Machines]** にナビゲートして Linux 仮想マシンに移動し、 **[概要]** ページの **[接続]** をクリックします。 VM に接続する文字列をコピーします。
2. 任意の SSH クライアントを使用して、VM に **接続** します。 
3. ターミナル ウィンドウで、CURL を使用して、ローカルのマネージド ID エンドポイントに対して Azure Storage のアクセス トークンを取得するよう要求します。
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fstorage.azure.com%2F' -H Metadata:true
    ```
4. このアクセス トークンを使用して Azure Storage にアクセスします。たとえば、コンテナーに事前にアップロードされたサンプル ファイルの内容を読み取るなどです。 `<STORAGE ACCOUNT>`、`<CONTAINER NAME>`、`<FILE NAME>` の値を、以前に指定した値で置き換えます。`<ACCESS TOKEN>` は、前の手順で返されたトークンに置き換えます。

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME> -H "x-ms-version: 2017-11-09" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   応答には、次のようなファイルの内容が含まれています。

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Linux VM のシステム割り当てマネージド ID を使用して Azure Storage にアクセスする方法を説明しました。  Azure Storage の詳細については、以下を参照してください。

> [!div class="nextstepaction"]
> [Azure ストレージ](../../storage/common/storage-introduction.md)
