---
title: Azure でデプロイされた SSIS パッケージを使用してファイルを開いて保存する
description: ローカル ファイル システムを使う SSIS パッケージを Azure の SSIS にリフト アンド シフトするときに、オンプレミスおよび Azure のファイルを開いて保存する方法について説明します
ms.date: 06/27/2018
ms.topic: conceptual
ms.prod: sql
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: jburchel
ms.openlocfilehash: 9775266c986b3be547d8b78b8c74765c3b72a92e
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786857"
---
# <a name="open-and-save-files-on-premises-and-in-azure-with-ssis-packages-deployed-in-azure"></a>Azure でデプロイされた SSIS パッケージを使用して、オンプレミスおよび Azure のファイルを開いて保存する

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

この記事では、ローカル ファイル システムを使う SSIS パッケージを Azure の SSIS にリフト アンド シフトするときに、オンプレミスおよび Azure のファイルを開いて保存する方法について説明します。

## <a name="save-temporary-files"></a>一時ファイルを保存する

単一のパッケージ実行の間に一時ファイルを格納して処理する必要がある場合、パッケージは Azure-SSIS Integration Runtime ノードの現在の作業ディレクトリ (`.`) または一時フォルダー (`%TEMP%`) を使うことができます。

## <a name="use-on-premises-file-shares"></a>オンプレミスのファイル共有を使う

ローカル ファイル システムを使うパッケージを Azure の SSIS にリフト アンド シフトするときに、引き続き **オンプレミスのファイル共有** を使うには、次のようにします。

1. ローカル ファイル システムからオンプレミスのファイル共有にファイルを転送します。

2. オンプレミスのファイル共有を Azure Virtual Network に参加させます。

3. Azure-SSIS IR を同じ仮想ネットワークに参加させます。 詳細情報については、「[Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる](./join-azure-ssis-integration-runtime-virtual-network.md)」を参照してください。

4. Windows 認証を使うアクセス資格情報を設定することにより、同じ仮想ネットワーク内のオンプレミスのファイル共有に Azure SSIS IR を接続します。 詳細については、「[Windows 認証でデータとファイル共有に接続する](ssis-azure-connect-with-windows-auth.md)」を参照してください。

5. パッケージ内のローカル ファイル パスを、オンプレミスのファイル共有を指す UNC パスに更新します。 たとえば、`C:\abc.txt` を `\\<on-prem-server-name>\<share-name>\abc.txt` に更新します。

## <a name="use-azure-file-shares"></a>Azure Files 共有を使用する

ローカル ファイル システムを使うパッケージを Azure の SSIS にリフト アンド シフトするときに、**Azure Files** を使うには、次のようにします。

1. ローカル ファイル システムから Azure Files にファイルを転送します。 詳しくは、「[Azure Files](https://azure.microsoft.com/services/storage/files/)」をご覧ください。

2. Windows 認証を使うアクセス資格情報を設定することにより、Azure Files に Azure SSIS IR を接続します。 詳細については、「[Windows 認証でデータとファイル共有に接続する](ssis-azure-connect-with-windows-auth.md)」を参照してください。

3. パッケージ内のローカル ファイル パスを、Azure Files を指す UNC パスに更新します。 たとえば、`C:\abc.txt` を `\\<storage-account-name>.file.core.windows.net\<share-name>\abc.txt` に更新します。

## <a name="next-steps"></a>次のステップ

- パッケージをデプロイします。 詳しくは、[SSMS を使用した Azure への SSIS プロジェクトのデプロイ](/sql/integration-services/ssis-quickstart-deploy-ssms)に関する記事をご覧ください。
- パッケージを実行します。 詳しくは、[SSMS を使用した Azure での SSIS パッケージの実行](/sql/integration-services/ssis-quickstart-run-ssms)に関する記事をご覧ください。
- パッケージをスケジュールします。 詳細については、「[Azure で SSIS パッケージのスケジュールを設定する](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)」を参照してください。