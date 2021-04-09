---
title: リレーショナル データベースのデータベース管理を教えるためのラボを設定する | Microsoft Docs
description: リレーショナル データベースの管理について教えるためのラボを設定する方法について説明します。
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 32079d6dc31cf3fcad66976737630472129b4271
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462422"
---
# <a name="set-up-a-lab-to-teach-database-management-for-relational-databases"></a>リレーショナル データベースのデータベース管理を教えるためのラボを設定する

この記事では、基本的なデータベース管理のクラスのためのラボを Azure Lab Services で設定する方法について説明します。 データベースの概念は、大学のほとんどのコンピューター サイエンス部門で教えられる、入門コースの 1 つです。 構造化照会言語 (SQL) は、国際標準です。 SQL は、データベース内のコンテンツへの追加、アクセス、管理などの、リレーショナル データベース管理のための標準言語です。  これは、迅速な処理、実証された信頼性、使いやすさ、および柔軟性を備えています。

この記事では、MySQL Database Server と SQL Server 2019 サーバーの両方を使用して、ラボで仮想マシン テンプレートを設定する方法について説明します。  [MySQL](https://www.mysql.com/) は、無料で利用できるオープンソースのリレーショナル データベース管理システム (RDBMS) です。  [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) は、Microsoft の RDBMS の最新バージョンです。

## <a name="lab-configuration"></a>ラボの構成

このラボを設定するには、Azure サブスクリプションとラボ アカウントが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。 Azure サブスクリプションを取得したら、Azure Lab Services で新しいラボ アカウントを作成できます。 新しいラボ アカウントの作成の詳細については、[ラボ アカウントを設定するためのチュートリアル](tutorial-setup-lab-account.md)を参照してください。  既存のラボ アカウントを使用することもできます。

### <a name="lab-account-settings"></a>ラボ アカウントの設定

以下の表に記載されているラボ アカウントの設定を有効にします。 Marketplace イメージを有効にする方法の詳細については、「[ラボ作成者が利用できる Marketplace イメージを指定する](./specify-marketplace-images.md)」を参照してください。

| ラボ アカウントの設定 | Instructions |
| ------------------- | ------------ |
|Marketplace イメージ| ラボ アカウント内で使用するために、'SQL Server 2019 Standard on Windows Server 2019' イメージを有効にします。|

### <a name="lab-settings"></a>ラボの設定

クラスルーム ラボを設定するときは、次の表の設定を使用します。  クラスルーム ラボの作成方法の詳細については、[クラスルーム ラボの設定に関するチュートリアル](tutorial-setup-classroom-lab.md)を参照してください。

| ラボの設定 | 値/説明 |
| ------------ | ------------------ |
|仮想マシンのサイズ| 中。 このサイズは、リレーショナル データベース、メモリ内 Caching、および分析に最適です。|
|仮想マシン イメージ| Windows Server 2019 上の SQL Server 2019 Standard|

## <a name="template-machine-configuration"></a>テンプレート マシンの構成

Windows Server 2019 に MySQL をインストールするには、「[仮想マシンに MySQL Community Server をインストールして実行 する](/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json#install-and-run-mysql-community-server-on-the-virtual-machine)」に記載されている手順に従ってください。

SQL Server 2019 は、新しいラボの作成時に選択した仮想マシン イメージにプレインストールされています。

## <a name="cost-estimate"></a>コストの見積もり

このクラスの考えられるコスト見積もりを検討しましょう。  クラスの生徒数は 25 人とします。  予定された授業時間は 20 時間です。  また、各学生には、予定された授業時間の他に、宿題や課題を行うための時間として 10 時間が割り当てられます。  ここで選択した仮想マシンのサイズは、42 ラボ ユニットの "中" です。

このクラスで考えられるコスト見積もりの例を次に示します。

25 人の学生 \* (スケジュールされた 20 時間 + クォータの 10 時間) \* 0.42 USD/時間 = 315.00 USD

価格について詳しくは、「[Azure Lab Services の価格](https://azure.microsoft.com/pricing/details/lab-services/)」をご覧ください。

## <a name="conclusion"></a>まとめ

この記事では、MySQL と SQL Server の両方を使用した基本的なデータベース管理の概念のためのラボの作成に必要な手順について説明しました。 他のデータベース クラスにも同様のセットアップを使用できます。

## <a name="next-steps"></a>次のステップ

次の手順は、すべてのラボの設定で共通です。

- [テンプレートの作成と管理](how-to-create-manage-template.md)
- [ユーザーの追加](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [クォータを設定する](how-to-configure-student-usage.md#set-quotas-for-users)
- [スケジュールを設定する](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [登録リンクを学生に電子メールで送る](how-to-configure-student-usage.md#send-invitations-to-users)