---
title: Azure PowerShell のサンプル スクリプト - Web アプリをバックアップから復元する | Microsoft Docs
description: Azure PowerShell のサンプル スクリプト - Web アプリをバックアップから復元する
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.service: app-service-web
ms.workload: web
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 85cbcb4b01161e0004d38f08ae8c30fd398727ff
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087895"
---
# <a name="restore-a-web-app-from-a-backup-using-azure-powershell"></a>Azure PowerShell を使用して Web アプリをバックアップから復元する

このサンプル スクリプトでは、既存の Web アプリから以前に完了したバックアップを取得し、コンテンツを上書きすることによってアプリを復元します。 

必要に応じて、[Azure PowerShell ガイド](/powershell/azure/overview)の手順に従って Azure PowerShell をインストールし、`Connect-AzAccount` を実行して、Azure との接続を作成します。 

## <a name="sample-script"></a>サンプル スクリプト

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore/backup-restore.ps1?highlight=1-2 "Restore a web app from a backup")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

Web アプリが必要なくなったら、次のコマンドを使用して、リソース グループ、Web アプリ、およびすべての関連リソースを削除します。

```powershell
Remove-AzResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Web アプリのバックアップの一覧を取得します。 |
| [Restore-AzWebAppBackup](/powershell/module/az.websites/restore-azwebappbackup) | 以前に完了したバックアップから Web アプリを復元します。 |

## <a name="next-steps"></a>次の手順

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の Azure App Service Web Apps 用 Azure PowerShell サンプル スクリプトは、[Azure PowerShell サンプル](../samples-powershell.md)のページにあります。
