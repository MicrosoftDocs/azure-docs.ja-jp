---
title: Azure Purview に SAP ECC ソースを登録してスキャン (プレビュー) を設定する
description: この記事では、Azure Purview に SAP ECC ソースを登録し、スキャンを設定する方法について、概要を説明します。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: b72538c61d1a68bca655b0d9529f654068727f86
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101696070"
---
# <a name="register-and-scan-sap-ecc-source-preview"></a>SAP ECC ソースを登録してスキャンする (プレビュー)

この記事では、Purview に SAP ECC ソースを登録し、スキャンを設定する方法について、概要を説明します。

## <a name="supported-capabilities"></a>サポートされる機能

SAP ECC ソースでは、SAP ECC インスタンスからメタデータを抽出するための **フル スキャン** がサポートされており、データ資産間の **系列** がフェッチされます。

## <a name="prerequisites"></a>前提条件

1.  最新の[セルフホステッド統合ランタイム](https://www.microsoft.com/download/details.aspx?id=39717)を設定します。
    詳細については、「[セルフホステッド統合ランタイムを作成して共有する](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)」を参照してください。

2.  セルフホステッド統合ランタイムがインストールされている仮想マシンに [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) がインストールされていることを確認します。

3.  セルフホステッド統合ランタイム コンピューターに \"Visual C++ 再頒布可能パッケージ 2012 Update 4\" がインストールされていることを確認します。 まだインストールしていない場合は、[こちら](https://www.microsoft.com/download/details.aspx?id=30679)からダウンロードしてください。

4.  [Microsoft .NET 3.0 用の SAP コネクタ](https://support.sap.com/en/product/connectors/msnet.html) (64 ビット) を SAP の Web サイトからダウンロードし、それをセルフホステッド統合ランタイム コンピューターにインストールします。 インストール時に、 **[Optional setup steps]\(省略可能なセットアップ手順\)** ウィンドウで、 **[Install Assemblies to GAC]\(アセンブリを GAC にインストールする\)** オプションを必ず選択してください。

    :::image type="content" source="media/register-scan-sapecc-source/requirement.png" alt-text="前提条件" border="true":::

5.  コネクタにより、Java コネクタ (JCo) 3.0 API を使用して SAP からメタデータが読み取られます。 そのため、セルフホステッド統合ランタイムがインストールされている仮想マシンで Java コネクタが使用可能であることを確認します。
    環境に合った JCo ディストリビューションを使用していることを確認してください。 たとえば、Microsoft Windows コンピューターで、sapjco3.jar と sapjco3.dll ファイルが使用可能であることを確認します。

    > [!Note] 
    > このドライバーは、VM 内のすべてのアカウントからアクセスできる必要があります。 それをユーザー アカウントにインストールしないでください。

6.  [ABAP 関数のデプロイ ガイド](abap-functions-deployment-guide.md)に記載されている手順に従って、SAP サーバーにメタデータ抽出 ABAP 関数モジュールをデプロイします。 SAP サーバーに RFC 関数モジュールを作成するには、ABAP 開発者アカウントが必要です。 ユーザー アカウントには、SAP サーバーに接続し、次の RFC 関数モジュールを実行するための十分なアクセス許可が必要です。
    -   STFC_CONNECTION (接続を確認する)
    -   RFC_SYSTEM_INFO (システム情報を確認する)


## <a name="setting-up-authentication-for-a-scan"></a>スキャンの認証の設定

SAP ECC ソースでサポートされている認証は **基本認証** のみです。

## <a name="register-sap-ecc-source"></a>SAP ECC ソースを登録する

新しい SAP ECC ソースをデータ カタログに登録するには、次のようにします。

1.  ご自分の Purview アカウントに移動します。
2.  左側のナビゲーションで **[ソース]** を選択します。
3.  **[登録]** を選択します
4.  [Register sources]\(ソースの登録\) で、 **[SAP ECC]** を選択します。 **[続行]** を選択します。

    :::image type="content" source="media/register-scan-sapecc-source/register-sapecc.png" alt-text="SAPECC の登録オプション" border="true":::

**[Register sources (SAP ECC)]\(ソースの登録 (SAP ECC)\)** 画面で、次のようにします。

1.  データ ソースがカタログに一覧表示されるときの **名前** を入力します。

2.  SAP ECC ソースに接続するための **アプリケーション サーバー** 名を入力します。
    SAP アプリケーション サーバー ホストの IP アドレスにすることもできます。

3.  SAP の **システム番号** を入力します。 これは、00 から 99 の 2 桁の整数です。

4.  コレクションを選択するか、新しいものを作成します (省略可能)

5.  データ ソースの登録を終了します。

    :::image type="content" source="media/register-scan-sapecc-source/register-sapecc-2.png" alt-text="SAPECC を登録する" border="true":::

## <a name="creating-and-running-a-scan"></a>スキャンを作成し、実行する

新しいスキャンを作成して実行するには、次の操作を行います。

1.  管理センターで、 [統合ランタイム] をクリックします。 セルフホステッド統合ランタイムが設定されていることを確認してください。 設定されていない場合は、[こちら](https://docs.microsoft.com/azure/purview/manage-integration-runtimes)に記載されている手順を使用して、セルフホステッド統合ランタイムを作成します。

2.  **[ソース]** に移動します

3.  登録されている SAP ECC ソースを選択します。

4.  **[+ 新しいスキャン]** を選択します

5.  次の詳細を指定します。

    a.  **[名前]** : スキャンの名前

    b.  **[Connect via integration runtime]\(統合ランタイム経由で接続\)** : 構成済みのセルフホステッド統合ランタイムを選択します

    c.  **[資格情報]** : 対象のデータ ソースに接続するための資格情報を選択します。 次のことを確認します。

    -   資格情報を作成するときに [基本認証] を選択します。
    -   ユーザー名入力フィールドで、SAP サーバーに接続するためのユーザー ID を指定します。
    -   SAP サーバーに接続するために使用されるユーザー パスワードを、秘密鍵に格納します。

    d.  **[クライアント ID]** : SAP クライアント ID を入力します。 これは、000 から 999 の 3 桁の数値です。

    e.  **[JCo library path]\(JCo ライブラリ パス\)** : JCo ライブラリが配置されているディレクトリ パス

    f.  **[Maximum memory available]\(使用可能な最大メモリ\):** スキャン プロセスで使用される、顧客の VM で使用可能な最大メモリ (GB 単位)。 これは、スキャンする SAP ECC ソースのサイズによって異なります。

    :::image type="content" source="media/register-scan-sapecc-source/scan-sapecc.png" alt-text="SAPECC をスキャンする" border="true":::

6.  **[続行]** をクリックします。

7.  **スキャン トリガー** を選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

8.  スキャンを確認し、 **[保存および実行]** をクリックします。

## <a name="viewing-your-scans-and-scan-runs"></a>スキャンとスキャンの実行を確認する

1. 管理センターに移動します。 **[Sources and scanning]\(ソースとスキャン\)** セクションの **[データ ソース]** を選択します。

2. 目的のデータ ソースを選択します。 そのデータ ソースに対する既存のスキャンの一覧が表示されます。

3. 結果を表示するスキャンを選択します。

4. このページには、前回のすべてのスキャン実行と、各スキャン実行のメトリックと状態が表示されます。 また、そのスキャンがスケジュールされたスキャンと手動スキャンのどちらかであるか、分類が適用された資産の数、検出された資産の合計数、スキャンの開始時刻と終了時刻、スキャンの実行時間の合計も表示されます。

## <a name="manage-your-scans"></a>スキャンを管理する

スキャンを管理または削除するには、次の操作を行います。

1. 管理センターに移動します。 **[Sources and scanning]\(ソースとスキャン\)** セクションの **[データ ソース]** を選択し、目的のデータ ソースを選択します。

2. 管理するスキャンを選択します。 スキャンを編集するには、 **[編集]** を選択します。

3. スキャンを削除するには、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

- [Azure Purview データ カタログを参照する](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)