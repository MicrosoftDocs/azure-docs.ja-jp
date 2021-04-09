---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 09/30/2020
ms.author: alkohli
ms.openlocfilehash: 80e34c117c02c2126b46297c4219effa85e9caa6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98792378"
---
- 事前に作成された共有にファイルを直接コピーしないでください。 共有の下にフォルダーを作成し、そのフォルダーにファイルをコピーする必要があります。
- *StorageAccount_BlockBlob*  と *StorageAccount_PageBlob* の下のフォルダーはコンテナーです。 コンテナーは、たとえば、*BlockBlob/container* や *PageBlob/container* として作成されます。
- *StorageAccount_AzureFiles* の直下に作成された各フォルダーは、Azure ファイル共有に変換されます。
- コピー対象のオブジェクトが、すでにクラウド内にある Azure オブジェクト (BLOB やファイルなど) と同じ名前を持つ場合、Data Box により、クラウド内のファイルが上書きされます。
- *StorageAccount_BlockBlob* 共有と *StorageAccount_PageBlob* 共有に書き込まれる各ファイルは、それぞれブロック BLOB およびページ BLOB としてアップロードされます。
- Azure Blob Storage は、ディレクトリに対応していません。 *StorageAccount_BlockBlob* フォルダーの下にフォルダーを作成する場合、 blob 名に仮想フォルダーが作成されます。 Azure Files では、実際のディレクトリ構造が維持されます。
- *StorageAccount_BlockBlob* フォルダーと *StorageAccount_PageBlob* フォルダーの下に作成される空のディレクトリ階層 (ファイルを含まない) はアップロードされません。
- Azure へのデータのアップロード時にエラーが発生した場合は、ターゲット ストレージ アカウントにエラー ログが作成されます。 このエラー ログへのパスは、アップロードが完了したときに使用可能になります。ログを確認して、是正措置を行うことができます。 アップロードしたデータを確認せずにソースからデータを削除しないでください。
- 「[Azure Data Box によるファイルの ACL、属性、タイムスタンプの保持](../articles/databox/data-box-file-acls-preservation.md)」のガイダンスを使用して Azure Files にデータをアップロードすると、ファイル メタデータと NTFS アクセス許可を保持できます。
