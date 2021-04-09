---
title: Azure AD Connect データベースを SQL Server Express から SQL Server に移動する | Microsoft Docs
description: このドキュメントでは、Azure AD Connect データベースを、ローカル SQL Server Express サーバーからリモート SQL Server に移動する方法について説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94710e99fa7d04d757f2ad5fd7b2d3f6e01371d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91306344"
---
# <a name="move-azure-ad-connect-database-from-sql-server-express-to-sql-server"></a>Azure AD Connect データベースを SQL Server Express から SQL Server に移動する 

このドキュメントでは、Azure AD Connect データベースを、ローカル SQL Server Express サーバーからリモート SQL Server に移動する方法について説明します。  この作業は、次の手順に従って実行できます。

## <a name="about-this-scenario"></a>このシナリオについて
このシナリオに関する簡単な情報を次に示します。  このシナリオでは、Azure AD Connect バージョン (1.1.819.0) が 1 つの Windows Server 2016 ドメイン コントローラーにインストールされています。  データベースに使用されているのは、組み込みの SQL Server 2012 Express Edition です。  データベースは、SQL Server 2017 サーバーに移動されます。

![シナリオのアーキテクチャ](media/how-to-connect-install-move-db/move1.png)

## <a name="move-the-azure-ad-connect-database"></a>Azure AD Connect データベースを移動する
次の手順を使用して、Azure AD Connect データベースをリモート SQL Server に移動します。

1. Azure AD Connect サーバーで **[サービス]** に移動し、**Microsoft Azure AD Sync** サービスを停止します。
2. **%ProgramFiles%\Microsoft Azure AD Sync\Data** フォルダーを見つけて、**ADSync.mdf** ファイルと **ADSync_log.ldf** ファイルをリモート SQL Server にコピーします。
3. Azure AD Connect サーバーで **Microsoft Azure AD Sync** サービスを再起動します。
4. [コントロール パネル] -- [プログラム] -- [プログラムと機能] に移動して、Azure AD Connect をアンインストールします。  Microsoft Azure AD Connect を選択し、上部の [アンインストール] をクリックします。
5. リモート SQL サーバーで、SQL Server Management Studio を開きます。
6. [データベース] を右クリックし、[Attach]\(アタッチ\) を選択します。
7. **[Attach Databases]\(データベースのアタッチ\)** 画面で、 **[追加]** をクリックし、ADSync.mdf ファイルに移動します。  **[OK]** をクリックします。
   ![データベースのアタッチ](media/how-to-connect-install-move-db/move2.png)

8. データベースがアタッチされたら、Azure AD Connect サーバーに戻り、Azure AD Connect をインストールします。
9. MSI のインストールが完了すると、Azure AD Connect ウィザードが簡易モードの設定で開始されます。 [終了] アイコンをクラスター リソースして画面を閉じます。
   ![左側のメニューの [Express 設定] が強調表示された [Azure AD Connect へようこそ] ページを示すスクリーンショット。](./media/how-to-connect-install-move-db/db1.png)
10. 新しいコマンド プロンプトまたは PowerShell セッションを開始します。 フォルダー \<drive>\program files\Microsoft Azure AD Connect に移動します。 コマンド .\AzureADConnect.exe /useexistingdatabase を実行して、"既存のデータベースを使用する" 設定モードで Azure AD Connect ウィザードを開始します。
    ![PowerShell](./media/how-to-connect-install-move-db/db2.png)
11. [Azure AD Connect へようこそ] 画面が表示されます。 ライセンス条項とプライバシーに関する声明に同意したら、 **[続行]** をクリックします。
    ![[Azure AD Connect へようこそ] ページを示すスクリーンショット](./media/how-to-connect-install-move-db/db3.png)
12. **[必須コンポーネントのインストール]** 画面で **[既存の SQL Server を使用する]** オプションをオンにします。 ADSync データベースをホストしている SQL サーバーの名前を指定します。 ADSync データベースのホストに使用されている SQL エンジン インスタンスが SQL サーバーで既定のインスタンスではない場合、SQL エンジン インスタンス名を指定する必要があります。 さらに、SQL の参照が有効ではない場合、SQL エンジン インスタンスのポート番号も指定する必要があります。 次に例を示します。         
    ![[必須コンポーネントのインストール] ページを示すスクリーンショット。](./media/how-to-connect-install-move-db/db4.png)           

13. **[Azure AD に接続]** 画面で、Azure AD ディレクトリのグローバル管理者の資格情報を指定する必要があります。 既定の onmicrosoft.com ドメインでアカウントを使用することをお勧めします。 このアカウントは、Azure AD のサービス アカウントを作成するためにのみ使用され、ウィザードが完了した後は使用されません。
    ![のインスタンスに接続するときには、](./media/how-to-connect-install-move-db/db5.png)
 
14. **[ディレクトリの接続]** 画面では、ディレクトリ同期に構成されている既存の AD フォレストは、赤色の×アイコンで表示されます。 オンプレミスの AD フォレストの変更を同期するには、AD DS アカウントが必要です。 Azure AD Connect ウィザードでは、ADSync データベースに格納されている AD DS アカウントの資格情報を取得できません。これは、資格情報が暗号化され、復号には前の Azure AD Connect サーバーが必要なためです。 **[資格情報の変更]** をクリックして、AD フォレストの AD DS アカウントを指定します。
    ![Directories](./media/how-to-connect-install-move-db/db6.png)
 

15. ポップアップ ダイアログで、(i) エンタープライズ管理者の資格情報を指定して Azure AD Connect に AD DS アカウントの作成を任せるか、(ii) AD DS アカウントを自分で作成してその資格情報を Azure AD Connect に提供することができます。 オプションを選択し、必要な資格情報を指定したら、 **[OK]** をクリックしてポップアップ ダイアログを閉じます。
    ![[新しい AD アカウントを作成] が選択されている [AD フォレスト アカウント] ポップアップ ダイアログのスクリーンショット。](./media/how-to-connect-install-move-db/db7.png)
 

16. 資格情報を入力すると、赤色の×アイコンは緑色のチェック アイコンで置き換えられます。 **[次へ]** をクリックします。
    ![アカウントの資格情報を入力した後の [ディレクトリの接続] ページを示すスクリーンショット。](./media/how-to-connect-install-move-db/db8.png)
 

17. **[構成の準備完了]** 画面で、 **[インストール]** をクリックします。
    ![ようこそ](./media/how-to-connect-install-move-db/db9.png)
 

18. インストールが完了すると、Azure AD Connect サーバーがステージング モードで自動的に有効になります。 ステージング モードを無効にする前に、予期しない変更に備えてサーバー構成と保留中のエクスポートを確認することをお勧めします。 

## <a name="next-steps"></a>次のステップ

- 「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。
- [既存の ADSync データベースを使用して Azure AD Connect をインストールする](how-to-connect-install-existing-database.md)
- [SQL によって委任された管理者のアクセス許可を使用して Azure AD Connect をインストールする](how-to-connect-install-sql-delegation.md)

