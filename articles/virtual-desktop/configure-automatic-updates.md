---
title: Microsoft Endpoint Configuration Manager を構成する - Azure
description: Windows Virtual Desktop 上の Windows 10 Enterprise マルチセッションにソフトウェア更新プログラムを展開するように、Microsoft Endpoint Configuration Manager を構成する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 06/12/2020
ms.author: helohr
ms.reviewer: v-cawood; clemr
manager: lizross
ms.openlocfilehash: bfc797e0c11a1804d27c6c97fea8cb2ba62dca7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88010125"
---
# <a name="configure-microsoft-endpoint-configuration-manager"></a>Microsoft Endpoint Configuration Manager を構成する

この記事では、Windows 10 Enterprise マルチセッションを実行している Windows Virtual Desktop ホストに更新プログラムを自動的に適用するように、Microsoft Endpoint Configuration Manager を構成する方法について説明します。

## <a name="prerequisites"></a>前提条件

この設定を構成するには、次のものが必要です。

   - 仮想マシンに Microsoft Endpoint Configuration Manager エージェントがインストールされていることを確認します。
   - Microsoft Endpoint Configuration Manager のバージョンが、少なくとも分岐レベル 1906 であることを確認します。 最良の結果を得るには、分岐レベル 1910 以上を使用してください。

## <a name="configure-the-software-update-point"></a>ソフトウェアの更新ポイントを構成する

Windows 10 Enterprise マルチセッションの更新プログラムを受信するには、Windows Server バージョン 1903 以降を Microsoft Endpoint Configuration Manager 内の製品として有効にする必要があります。 この製品設定は、Windows Server Update Service を使用して、システムに更新プログラムを展開する場合にも適用されます。

更新プログラムを受信するには、次の手順に従います。

1. Microsoft Endpoint Configuration Manager を開いて、 **[サイト]** を選択します。
2. **[サイト コンポーネントの構成]** を選択します。
3. ドロップダウンメニューの **[ソフトウェアの更新ポイント]** を選択します。
4. **[製品]** タブを選択します。
5. **[Windows Server バージョン 1903 以降]** というチェック ボックスをオンにします。
6. **[ソフトウェア ライブラリ]**  >  **[概要]**  >  **[ソフトウェアの更新]**  >  **[すべてのソフトウェア更新プログラム]** の順に選択し、 **[ソフトウェア更新プログラムを同期する]** を選択します。
7. **[プログラム ファイル]**  >  **[Microsoft Configuration Manager]**  >  **[ログ]** の wsyncmgr.log ファイルを確認して、変更が保存されていることを確認します。 更新プログラムの同期には数分かかる場合があります。

## <a name="create-a-query-based-collection"></a>クエリ ベースのコレクションの作成

Windows 10 Enterprise マルチセッション仮想マシンのコレクションを作成するには、クエリ ベースのコレクションを使用して、特定のオペレーティング システムの SKU を識別できます。

コレクションを作成するには、次の手順に従います。

1. **[資産とコンプライアンス]** を選択します。
2. **[概要]**  >  **[デバイス コレクション]** の順に移動し、 **[デバイス コレクション]** を右クリックして、ドロップダウン メニューから **[デバイス コレクションの作成]** を選択します。
3. 開いたメニューの **[全般]** タブで、 **[名前]** フィールドにコレクションを表す名前を入力します。 **[コメント]** フィールドで、コレクションの内容を説明する追加情報を指定できます。 **[限定コレクション]** には、コレクション クエリに含めるマシンを定義します。
4. **[メンバーシップの規則]** タブで、 **[規則の追加]** を選択し、 **[クエリ規則]** を選択して、クエリの規則を追加します。
5. **[クエリ規則のプロパティ]** で、規則の名前を入力し、 **[クエリ ステートメントの編集]** を選択して、規則のパラメーターを定義します。
6. **[Show Query Statement] (クエリ ステートメントの表示)** を選択します。
7. ステートメントに、次の文字列を入力します。

    ```syntax
    select
    SMS_R_SYSTEM.ResourceID,SMS_R_SYSTEM.ResourceType,SMS_R_SYSTEM.Name,SMS_R_SYSTEM.SMSUniqueIdentifier,SMS_R_SYSTEM.ResourceDomainORWorkgroup,SMS_R_SYSTEM.Client
    from SMS_R_System inner join SMS_G_System_OPERATING_SYSTEM on
    SMS_G_System_OPERATING_SYSTEM.ResourceId = SMS_R_System.ResourceId where
    SMS_G_System_OPERATING_SYSTEM.OperatingSystemSKU = 175
    ```

8. **[OK]** を選択してコレクションを作成します。
9. コレクションが正常に作成されたかどうかを確認するには、 **[資産とコンプライアンス]**  >  **[概要]**  >  **[デバイス コレクション]** の順に移動します。
