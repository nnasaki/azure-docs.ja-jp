---
title: 独自に開発したアプリケーションにアクセス許可を付与する方法 | Microsoft Docs
description: Azure AD ポータルまたは URL パラメーターを使用して、独自に開発したアプリケーションにアクセス許可を付与する方法
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: 10cf04fca8379f41587b1ea680c5b52a26193b53
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2018
ms.locfileid: "44723899"
---
# <a name="how-to-grant-permissions-to-a-custom-developed-application"></a>独自に開発したアプリケーションにアクセス許可を付与する方法

事前にアプリに同意を付与したい場合、またはアプリに対する同意がないことを示すエラーが発生した場合は、以下の手順を試してみてください。

## <a name="how-to-perform-admin-consent-for-your-application"></a>アプリケーションに対して管理者の同意を実行する方法

この方法を使用すると、組織内のすべてのユーザーに関してアプリケーションに同意が付与されます。

1. **グローバル管理者**として **[アプリの登録]** ブレードに移動し、アプリを選択します。

2. **[必要なアクセス許可]** を選択し、最後にブレードの上部にある **[アクセス許可の付与]** ボタンをクリックします。

または、アプリ構成を使用して *login.microsoftonline.com* に対する要求を作成し、そこに *&prompt=admin\_consent* を追加する方法があります。 管理者の資格情報でサインインしたら、すべてのユーザーに関してアプリに同意が付与されます。

## <a name="how-to-force-user-consent-for-your-application"></a>アプリケーションでユーザーの同意を強制する方法

* *&prompt=consent* を認証要求に追加します。認証のたびにエンド ユーザーの同意が必要になります。

## <a name="next-steps"></a>次の手順

[同意と Azure AD へのアプリの統合](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

[Azure AD v2.0 集中型アプリの同意とアクセス許可](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
