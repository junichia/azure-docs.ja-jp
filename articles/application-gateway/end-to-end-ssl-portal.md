---
title: クイック スタート - Azure Application Gateway を使用してエンド ツー エンド SSL 暗号化を構成する - Azure portal | Microsoft Docs
description: Azure portal を使用して、エンド ツー エンド SSL 暗号化により Azure Application Gateway を作成する方法について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/30/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: a37b313bd808ee0441d84ac92050b087eba7ac9d
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097171"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>ポータルで Application Gateway を使用してエンド ツー エンド SSL を構成する

この記事では、Azure portal を使用して、Application Gateway v1 SKU でエンド ツー エンド SSL 暗号化を構成する方法を示します。  

> [!NOTE]
> Application Gateway v2 SKU では、エンド ツー エンド構成を有効化するために、信頼されたルート証明書が必要です。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="before-you-begin"></a>開始する前に

アプリケーション ゲートウェイでエンド ツー エンド SSL を構成するには、ゲートウェイ用とバックエンド サーバー用の証明書が必要です。 ゲートウェイ証明書は、SSL プロトコルの仕様に従って対称キーを得る目的で使用されます。 対称キーは、ゲートウェイに送信されたトラフィックの暗号化と暗号化の解除に使用されます。 エンド ツー エンド SSL 暗号化の場合、適切なバックエンド サーバーがアプリケーション ゲートウェイで許可されている必要があります。 これを行うには、Application Gateway にバックエンド サーバーの公開証明書 (認証証明書 (v1) または信頼されたルート証明書 (v2) とも呼ばれます) をアップロードします。 証明書を追加することにより、Application Gateway は、既知のバックエンド インスタンスのみと通信するため、 エンド ツー エンド通信のセキュリティが強化されます。

詳細については、[SSL 終了とエンド ツー エンド SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview) に関するページを参照してください。

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>エンド ツー エンド SSL を使用して新しいアプリケーション ゲートウェイを作成する

エンド ツー エンド SSL 暗号化を使用して新しいアプリケーション ゲートウェイを作成するには、新しいアプリケーション ゲートウェイの作成中に、まず SSL 終了を有効化する必要があります。 これにより、クライアントとアプリケーション ゲートウェイの間の通信で SSL 暗号化が有効になります。 その後、アプリケーション ゲートウェイとバックエンド サーバーの間の通信で SSL 暗号化を有効化するために、HTTP 設定でバックエンド サーバーの証明書をホワイトリストに登録する必要があります。これにより、エンド ツー エンド SSL 暗号化が実現されます。

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>新しいアプリケーション ゲートウェイの作成中に SSL 終了を有効化する

[新しいアプリケーション ゲートウェイの作成中に SSL 終了を有効化する](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)方法については、この記事を参照してください。

### <a name="add-authenticationroot-certificate-of-back-end-servers"></a>バックエンド サーバーの認証証明書またはルート証明書を追加する

1. **[すべてのリソース]** を選択し、**myAppGateway** を選択します。

2. 左側のメニューで **[HTTP 設定]** を選択します。 アプリケーション ゲートウェイを作成したときに、既定の HTTP 設定 **appGatewayBackendHttpSettings** が Azure によって自動的に作成されました。 

3. **[appGatewayBackendHttpSettings]** を選択します。

4. **[プロトコル]** で **[HTTPS]** を選択します。 **バックエンド認証証明書または信頼されたルート証明書**のウィンドウが表示されます。 

5. **[新規作成]** を選択します。

6. 適切な**名前**を入力します。

7. **[CER 証明書のアップロード]** ボックスを使用して、証明書ファイルを選択します。

   Standard および WAF (v1) Application Gateway の場合は、.cer 形式のバックエンド サーバー証明書の公開キーをアップロードする必要があります。

   ![addcert](./media/end-to-end-ssl-portal/addcert.png)

   Standard_v2 および WAF_v2 Application Gateway の場合は、.cer 形式のバックエンド サーバー証明書の**ルート証明書**をアップロードする必要があります。 バックエンド証明書が既知の CA によって発行されている場合は、[既知の CA 証明書を使用する] チェック ボックスをオンにすることができます。証明書をアップロードする必要はありません。

   ![addtrustedrootcert](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![rootcert](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. **[保存]** を選択します。

## <a name="enable-end-to-end-ssl-for-existing-application-gateway"></a>既存のアプリケーション ゲートウェイでエンド ツー エンド SSL を有効化する

エンド ツー エンド SSL 暗号化を使用して既存のアプリケーション ゲートウェイを構成するには、リスナーでまず SSL 終了を有効化する必要があります。 これにより、クライアントとアプリケーション ゲートウェイの間の通信で SSL 暗号化が有効になります。 その後、アプリケーション ゲートウェイとバックエンド サーバーの間の通信で SSL 暗号化を有効化するために、HTTP 設定でバックエンド サーバーの証明書をホワイトリストに登録する必要があります。これにより、エンド ツー エンド SSL 暗号化が実現されます。

SSL 終了を有効化するために、HTTPS プロトコルおよび証明書を含むリスナーを使用する必要があります。 したがって、HTTPS プロトコルおよび証明書を含む既存のリスナーを使用するか、または新しいリスナーを作成するかを選択できます。 前者を選択した場合は、次に示す「**既存のアプリケーション ゲートウェイで SSL 終了を有効にする**」の手順を無視し、「**バックエンド サーバーの認証証明書または信頼されたルート証明書を追加する**」にそのまま進むことができます。 後者を選択した場合は、次の手順を使用します。

### <a name="enable-ssl-termination-in-existing-application-gateway"></a>既存のアプリケーション ゲートウェイで SSL 終了を有効化する

1. **[すべてのリソース]** を選択し、**myAppGateway** を選択します。

2. 左側のメニューで **[リスナー]** を選択します。

3. 要件に応じて、**基本**リスナーと**マルチサイト** リスナーのどちらかを選択します。

4. **[プロトコル]** で **[HTTPS]** を選択します。 **[証明書]** ウィンドウが表示されます。

5. クライアントとアプリケーション ゲートウェイの間で SSL 終了に使用する PFX 証明書をアップロードします。

   > [!NOTE]
   > テスト目的で、自己署名証明書を使用してもかまいません。 しかし、運用環境のワークロードについては、管理が難しく、完全にセキュリティで保護されていないため、推奨されません。 [自己署名証明書の作成](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate)方法を参照してください。

6. 要件に応じて、**リスナー**に必要なその他の設定を追加します。

7. **[OK]** を選択して保存します。

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>バックエンド サーバーの認証証明書または信頼されたルート証明書を追加する

1. **[すべてのリソース]** を選択し、**myAppGateway** を選択します。

2. 左側のメニューで **[HTTP 設定]** を選択します。 既存のバックエンド HTTP 設定で証明書をホワイトリストに登録するか、または新しい HTTP 設定を作成することができます。 次の手順では、既定の HTTP 設定 **appGatewayBackendHttpSettings** の証明書をホワイトリストに追加します。

3. **[appGatewayBackendHttpSettings]** を選択します。

4. **[プロトコル]** で **[HTTPS]** を選択します。 **バックエンド認証証明書または信頼されたルート証明書**のウィンドウが表示されます。 

5. **[新規作成]** を選択します。

6. 適切な **[名前]** を入力します。

7. **[CER 証明書のアップロード]** ボックスを使用して、証明書ファイルを選択します。

   Standard および WAF (v1) Application Gateway の場合は、.cer 形式のバックエンド サーバー証明書の公開キーをアップロードする必要があります。

   ![addcert](./media/end-to-end-ssl-portal/addcert.png)

   Standard_v2 および WAF_v2 Application Gateway の場合は、.cer 形式のバックエンド サーバー証明書の**ルート証明書**をアップロードする必要があります。 バックエンド証明書が既知の CA によって発行されている場合は、[既知の CA 証明書を使用する] チェック ボックスをオンにすることができます。証明書をアップロードする必要はありません。

   ![addtrustedrootcert](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. **[保存]** を選択します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure CLI を使用してアプリケーション ゲートウェイで Web トラフィックを管理する](./tutorial-manage-web-traffic-cli.md)
