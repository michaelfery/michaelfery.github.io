---
layout: post
title:  Transfer a domain name to Azure from another registrar
date:   2017-08-03 00:17:18
# categories: blog
description: Since a while, we could order and manage custom domain names from Azure. The missing part of this service was the ability to transfer a domain name from another registrar to Azure. I mean, not only DNS, but migrate the billing and ownership to Azure. Let’s see how to do this !
img: posts/azure-banner.png
tags: ["azure", "domain"]
redirect_from: "/blog/azure-transfer-a-domain-name-to-azure-from-another-registrar"
---

Since a while, we could order and manage custom domain names from Azure. The missing part of this service was the ability to transfer a domain name from another registrar to Azure. I mean, not only DNS, but migrate the billing and ownership to Azure. Let’s see how to do this !

## [](https://github.com/CatchRetry/azure-domain-transfer#prerequisites)Prerequisites

The domain must be older than 60 days and unlocked.

## [](https://github.com/CatchRetry/azure-domain-transfer#get-the-authorization-code-from-your-registrar)Get the authorization code from your registrar

Go to yout registrar and get the transfer authorization code. 💡 Check that the domain is unlocked for transfer.

## Get Powershell scripts

You can get the source code of the Powershell script via your browser or git clone [https://github.com/CatchRetry/azure-domain-transfer](https://github.com/CatchRetry/azure-domain-transfer)

## [](https://github.com/CatchRetry/azure-domain-transfer#execute-the-transfer)Execute the transfer

*   Close all other PowerShell windows and start a new one as an administrator.
*   Navigate to the script directory and run the PS script:

```bash
cd "C:\...\azure-domain-transfer" .\DomainTransfer.ps1 
```

## [](https://github.com/CatchRetry/azure-domain-transfer#confirm-the-transfer)Confirm the transfer

When you get the confirmation email. Click the confirmation button Now you have to wait up to 5-7 days for the transfer to be complete.

## [](https://github.com/CatchRetry/azure-domain-transfer#accept-the-transfer-from-your-registrar-if-you-can)Accept the transfer from your registrar (if you can)

Some registrar let the owner of the domain cancel or confirm the transfer. It allows the operation to be completed before the 5-7 days.

## [](https://github.com/CatchRetry/azure-domain-transfer#wait-for-the-transfer-to-be-complete)Wait for the transfer to be complete...

Wait and check the resource to be fullly available in you Azure portal.