---
title: Azure Blockchain Workbench を展開する
description: Azure Blockchain Workbench を展開する方法
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/22/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 80a40cec8ebd062751e896f9b555c5ed5464d7a3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="deploy-azure-blockchain-workbench"></a>Azure Blockchain Workbench を展開する

Azure Blockchain Workbench は、Azure Marketplace のソリューション テンプレートを使って展開されます。 テンプレートを使うと、ブロックチェーン アプリケーションの作成に必要なコンポーネントを簡単に展開できます。 展開が済むと、Blockchain Workbench を使ってクライアント アプリにアクセスし、ユーザーとブロックチェーン アプリケーションを作成および管理できます。

Blockchain Workbench のコンポーネントについて詳しくは、「[Azure Blockchain Workbench アーキテクチャ](blockchain-workbench-architecture.md)」をご覧ください。

## <a name="prepare-for-deployment"></a>デプロイの準備をする

Azure Blockchain Workbench の展開にはいくつかの前提条件があります。 前提条件には、Azure AD の構成とアプリケーションの登録が含まれます。

### <a name="blockchain-workbench-api-app-registration"></a>Blockchain Workbench API アプリの登録

Blockchain Workbench の展開には、Azure AD アプリケーションの登録が必要です。 アプリを登録するには Azure Active Directory (Azure AD) テナントが必要です。 既存のテナントを使うか、新しいテナントを作成することができます。 既存の Azure AD テナントを使う場合は、Azure AD テナント内でアプリケーションを登録するための十分なアクセス許可が必要です。 アプリケーションの登録は、Workbench を展開するサブスクリプションのサブスクリプション管理者のテナントで行う必要があります。 Azure AD テナントについて詳しくは、「[Azure Active Directory テナントを取得する方法](../active-directory/develop/active-directory-howto-tenant.md)」および「[Azure Active Directory とアプリケーションの統合](../active-directory/develop/active-directory-integrating-applications.md)」をご覧ください。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 右上隅でお使いのアカウントを選び、目的の Azure AD テナントに切り替えます。 テナントは、Workbench を展開するサブスクリプションのサブスクリプション管理者のテナントでなければならず、アプリケーションを登録するための十分なアクセス許可が必要です。
3. 左側のナビゲーション ウィンドウで、**[Azure Active Directory]** サービスを選びます。 **[アプリの登録]** > **[新しいアプリケーションの登録]** の順に選びます。

    ![アプリの登録](media/blockchain-workbench-deploy/app-registration.png)

4. アプリケーションの **[名前]** と **[サインオン URL]** を指定します。 展開の間に値が変更されるため、プレースホルダーの値を使用できます。 

    ![アプリの登録を作成する](media/blockchain-workbench-deploy/app-registration-create.png)

    |Setting  | 値  |
    |---------|---------|
    |Name | `Blockchain API` |
    |アプリケーションの種類 |Web アプリ/API|
    |[サインオン URL] | `https://blockchainapi` |

5. **[作成]** を選んで、Azure AD アプリケーションを登録します。

### <a name="modify-application-manifest"></a>アプリケーション マニフェストを変更する

次に、Azure AD 内のアプリケーション ロールを使って Blockchain Workbench 管理者を指定するように、アプリケーション マニフェストを変更する必要があります。  アプリケーション マニフェストについて詳しくは、「[Azure Active Directory アプリケーション マニフェスト](../active-directory/develop/active-directory-application-manifest.md)」をご覧ください。

1. 登録したアプリケーションの詳細ウィンドウで **[マニフェスト]** を選びます。
2. GUID を生成します。 PowerShell コマンド `[guid]::NewGuid()` または`New-GUID コマンドレット`、オンライン ツールを使って、GUID を生成できます。 
3. マニフェストの **appRoles** セクションを更新します。 [マニフェストの編集] ウィンドウで、**[編集]** を選び、`"appRoles": []` を提供された JSON に置き換えます。 忘れずに、**id** フィールドの値を、生成した GUID に置き換えてください。 

    ``` json
    "appRoles": [
         {
           "allowedMemberTypes": [
             "User",
             "Application"
           ],
           "displayName": "Administrator",
           "id": "<A unique GUID>",
           "isEnabled": true,
           "description": "Blockchain Workbench administrator role allows creation of applications, user to role assignments, etc.",
           "value": "Administrator"
         }
       ],
    ```

    ![マニフェストの編集](media/blockchain-workbench-deploy/edit-manifest.png)

    > [!IMPORTANT]
    > 値 **Administrator** は、Blockchain Workbench の管理者を識別するために必要です。

4.  **[保存]** をクリックして、アプリケーション マニフェストの変更を保存します。

### <a name="add-graph-api-key-to-application"></a>Graph API キーをアプリケーションに追加する

Blockchain Workbench は、ブロックチェーン アプリケーションと対話するユーザーのメイン ID 管理システムとして、Azure AD を使います。 Blockchain Workbench が Azure AD にアクセスして、名前やメール アドレスなどのユーザー情報を取得するためには、アクセス キーを追加する必要があります。 Blockchain Workbench は、Azure AD での認証にこのキーを使います。

1. 登録したアプリケーションの詳細ウィンドウで **[設定]** を選びます。
2. **[キー]** を選択します。
3. キーの**説明**を指定し、**有効期限**の値を選ぶことで、新しいキーを追加します。 

    ![キーを作成する](media/blockchain-workbench-deploy/app-key-create.png)

    |Setting  | 値  |
    |---------|---------|
    | [説明] | `Service` |
    | Expires | 有効期限を選びます |

4. **[保存]** を選択します。 
5. 後で使うので、キーの値をコピーして保存します。 展開を行うときに必要です。

    > [!IMPORTANT]
    >  展開のためにキーを保存しておかないと、新しいキーを生成する必要があります。 後でポータルからキーの値を取得することはできません。

### <a name="add-graph-api-required-permissions"></a>Graph API で必要なアクセス許可を追加する

API アプリケーションは、ディレクトリにアクセスするために、ユーザーからのアクセス許可を要求する必要があります。 API アプリケーションに必要な次のアクセス許可を設定します。

1. Blockchain API アプリの登録で、**[設定] > [必要なアクセス許可] > [API を選択します] > [Microsoft Graph]** の順に選択します。

    ![API を選択する](media/blockchain-workbench-deploy/client-app-select-api.png)

    **[選択]** をクリックします。

2. **[アプリケーションのアクセス許可]** の **[アクセスの有効化]** で、**[すべてのユーザーの完全なプロファイルの読み取り]** を選びます。

    ![アクセスを有効化](media/blockchain-workbench-deploy/client-app-read-perms.png)

    **[選択]** をクリックし、**[完了]** をクリックします。

3. **[必要なアクセス許可]** で **[アクセス許可の付与]** を選び、確認の要求に **[はい]** を選びます。

   ![アクセス許可を付与する](media/blockchain-workbench-deploy/client-app-grant-permissions.png)

   アクセス許可を付与することで、Blockchain Workbench はディレクトリのユーザーにアクセスできます。 メンバーを検索して Blockchain Workbench に追加するには、読み取りアクセス許可が必要です。

### <a name="get-application-id"></a>アプリケーション ID を取得する

展開には、アプリケーション ID とテナントの情報が必要です。 展開の間に使用できるように、情報を収集して保存します。

1. 登録したアプリケーションで、**[設定]** > **[プロパティ]** の順に選択します。
2.  **[プロパティ]** ウィンドウで、次の値をコピーし、後で展開のときに使用できるように保存しておきます。

    ![API アプリのプロパティ](media/blockchain-workbench-deploy/app-properties.png)

    | 保存する設定  | 展開での使用 |
    |------------------|-------------------|
    | アプリケーション ID | [Azure Active Directory setup]\(Azure Active Directory のセットアップ\) > [アプリケーション ID] |

### <a name="create-an-azure-ad-key-vault-application"></a>Azure AD Key Vault アプリケーションを作成する

Blockchain Workbench の展開には、Azure AD Key Vault アプリケーションの登録が必要です。

1. Azure portal の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** サービスを選びます。 **[アプリの登録]** > **[新しいアプリケーションの登録]** の順に選びます。
2. アプリケーションの **[名前]** と **[サインオン URL]** を指定します。 後で展開の間に値が変更される可能性があるため、プレースホルダーの値を使用できます。

    ![Key Vault アプリの登録を作成する](media/blockchain-workbench-deploy/key-vault-app-create.png)

    | Setting  | 値  |
    |---------|---------|
    | Name | `Blockchain Key Vault app` |
    | アプリケーションの種類 | Web アプリ/API |
    | [サインオン URL] | `https://keyvaultclient |

5. **[作成]** を選んで、Azure AD Key Vault アプリケーションを登録します。

### <a name="get-tenant-domain-name"></a>テナントのドメイン名を取得する

アプリケーションを登録する Active Directory テナントのドメイン名を収集して保存します。 

左側のナビゲーション ウィンドウで、**[Azure Active Directory]** サービスを選びます。 **[カスタム ドメイン名]** を選択します。 ドメイン名をコピーして保存します。

![ドメイン名](media/blockchain-workbench-deploy/domain-name.png)

## <a name="deploy-blockchain-workbench"></a>Blockchain Workbench を展開する

前提条件の手順が完了すると、Blockchain Workbench を展開できる状態になります。 次のセクションでは、フレームワークを展開する方法の概要を説明します。

1.  [Azure Portal](https://portal.azure.com) にサインインします。
2.  右上隅でお使いのアカウントを選び、Azure Blockchain Workbench を展開する Azure AD テナントに切り替えます。
3.  左側のウィンドウで、**[リソースの作成]** を選びます。 **[Search the Marketplace]** 検索バーで、`Azure Blockchain Workbench` を検索します。 

    ![Marketplace の検索バー](media/blockchain-workbench-deploy/marketplace-search-bar.png)

4.  **Azure Blockchain Workbench** を選びます。

    ![Marketplace の検索結果](media/blockchain-workbench-deploy/marketplace-search-results.png)

4.  **[作成]** を選択します。
5.  基本的な設定を行います。

    ![Azure Blockchain Workbench を作成する](media/blockchain-workbench-deploy/blockchain-workbench-settings-basic.png)

    | Setting | [説明]  |
    |---------|--------------|
    | Resource prefix (リソース プレフィックス) | この展開の短い一意識別子です。 この値は、リソースの名前付けのベースとして使われます。 |
    | VM ユーザー名 | このユーザー名は、すべての仮想マシン (VM) の管理者として使われます。 |
    | 認証の種類 | VM への接続にパスワードまたはキーを使うかどうかを選びます。 |
    | パスワード | このパスワードは、VM に接続するために使われます。 |
    | SSH | **ssh-rsa** で始まる単一行形式の RSA 公開キー、または複数行の PEM 形式を使います。 SSH キーは、Linux と OS X では `ssh-keygen` を使って、Windows では PuTTYGen を使って、生成できます。 SSH キーについて詳しくは、「[Azure 上の Windows で SSH キーを使用する方法](../virtual-machines/linux/ssh-from-windows.md)」をご覧ください。 |
    | Database password (データベース パスワード) / Confirm database password (データベース パスワードの確認) | 展開の一部として作成されるデータベースにアクセスするために使うパスワードを指定します。 |
    | [サブスクリプション] | 展開に使う Azure サブスクリプションを指定します。 |
    | リソース グループ | **[新規作成]** を選び、一意のリソース グループ名を指定して、新しいリソース グループを作成します。 |
    | 場所 | フレームワークを展開するリージョンを指定します。 |

6.  **[OK]** を選んで、基本設定の構成セクションを完了します。

7.  **[Azure Active Directory setup]\(Azure Active Directory のセットアップ\)** を設定します。

    ![Azure AD のセットアップ](media/blockchain-workbench-deploy/blockchain-workbench-settings-aad.png)

    | Setting | [説明]  |
    |---------|--------------|
    | ドメイン名 | 前提条件の「[テナントのドメイン名を取得する](#get-tenant-domain-name)」セクションで収集した Azure AD テナントを使います。 |
    | アプリケーション ID | 前提条件の「[アプリケーション ID を取得する](#get-application-id)」セクションで収集した Blockchain クライアント アプリの登録のアプリケーション ID を使います。 |
    | アプリケーション キー | 前提条件の「[Graph API キーをアプリケーションに追加する](#add-graph-api-key-to-application)」セクションで収集した Blockchain クライアント アプリの登録のアプリケーション キーを使います。 |


8.  **[OK]** をクリックして、Azure AD パラメーター構成セクションを完了します。

9.  **[Network size and performance]\(ネットワークのサイズとパフォーマンス\)** の設定を行います。

    ![ネットワークとパフォーマンスの設定](media/blockchain-workbench-deploy/blockchain-workbench-settings-network.png)

    | Setting | [説明]  |
    |---------|--------------|
    | Number of blockchain nodes (Blockchain ノードの数) | ネットワークに展開する Ethereum PoA バリデーター ノードの数を選びます。 |
    | Storage performance (ストレージのパフォーマンス) | Blockchain ネットワークの望ましい VM ストレージ パフォーマンスを選びます。 |
    | 仮想マシンのサイズ | Blockchain ネットワークの望ましい VM サイズを選びます。 |

10. **[OK]** をクリックして、ネットワーク サイズとパフォーマンスのセクションを完了します。

11. **[Azure Monitor]** の設定を行います。

    ![Azure Monitor](media/blockchain-workbench-deploy/blockchain-workbench-settings-oms.png)

    | Setting | [説明]  |
    |---------|--------------|
    | 監視 | Blockchain ネットワークの監視に Azure Monitor を使うかどうかを選びます。 |
    | Connect to existing OMS instance (既存の OMS インスタンスに接続する) | 既存の Operations Management Suite インスタンスを使うか、新しいインスタンスを作成するかを選びます。 

12. **[OK]** をクリックして、Azure Monitor のセクションを完了します。

13. サマリーで、パラメーターが正しいことを確認します。

    ![まとめ](media/blockchain-workbench-deploy/blockchain-workbench-summary.png)

14. **[作成]** を選び、条項に同意して、Azure Blockchain Workbench を展開します。

展開には、最大 90 分かかる場合があります。 Azure portal を使って進行状況を監視できます。 展開された成果物の状態を確認するには、新しく作成したリソース グループで **[デプロイ] > [概要]** を選びます。

## <a name="blockchain-workbench-web-url"></a>Blockchain Workbench の Web URL

Blockchain Workbench の展開が完了すると、新しいリソース グループには Blockchain Workbench のリソースが格納されています。 Blockchain Workbench サービスには、Web URL を使ってアクセスします。 次の手順では、展開済みのフレームワークの Web URL を取得する方法を示します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のナビゲーション ウィンドウで、**[リソース グループ]** を選びます。
3. Blockchain Workbench の展開時に指定したリソース グループ名を選びます。
4. **[種類]** 列見出しをクリックして、種類のアルファベット順に一覧を並べ替えます。
5. **[App Service]** の種類で 2 つのリソースがあります。 "-api" サフィックスが "*付いていない*" リソースの種類 **[App Service]** を選びます。

    ![App Service のリスト](media/blockchain-workbench-deploy/resource-group-list.png)

6.  App Service の **[基本]** セクションで、**[URL]** の値をコピーします。これは、展開した Blockchain Workbench に対する Web URL を表します。

    ![App Service の基礎](media/blockchain-workbench-deploy/app-service.png)

## <a name="configuring-the-reply-url"></a>応答 URL の構成

Azure Blockchain Workbench を展開した後は、次に、Azure Active Directory (Azure AD) クライアント アプリケーションが展開した Blockchain Workbench Web URL の正しい**応答 URL** に登録されていることを確認します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. Azure AD クライアント アプリケーションを登録したテナントにいることを確認します。
3. 左側のナビゲーション ウィンドウで、**[Azure Active Directory]** サービスを選びます。 **[アプリの登録]** を選択します。
4. 前提条件セクションで登録した Azure AD クライアント アプリケーションを選びます。
5. **[設定] > [応答 URL]** を選びます。
6. 「**Blockchain Workbench の Web URL**」セクションで取得した Azure Blockchain Workbench の展開のメイン Web URL を指定します。 応答 URL には、プレフィックス `https://` が付加されます。  たとえば、`https://myblockchain2-7v75.azurewebsites.net` のように指定します。

    ![応答 URL](media/blockchain-workbench-deploy/configure-reply-url.png)

7. **[保存]** を選んで、クライアントの登録を更新します。

## <a name="next-steps"></a>次の手順

これで、Azure Blockchain Workbench が展開されました。次に、[Azure Blockchain Workbench でユーザーを管理](blockchain-workbench-manage-users.md)します。
