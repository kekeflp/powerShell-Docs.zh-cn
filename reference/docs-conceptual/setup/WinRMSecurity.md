---
ms.date: 2017-06-05
keywords: powershell,cmdlet
title: WinRMSecurity
ms.openlocfilehash: a6adf61517708661e31a7387df5141f3c4f2c020
ms.sourcegitcommit: 598b7835046577841aea2211d613bb8513271a8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2017
---
# <a name="powershell-remoting-security-considerations"></a><span data-ttu-id="a169e-103">PowerShell 远程处理安全注意事项</span><span class="sxs-lookup"><span data-stu-id="a169e-103">PowerShell Remoting Security Considerations</span></span>

<span data-ttu-id="a169e-104">PowerShell 远程处理是管理 Windows 系统的推荐方式。</span><span class="sxs-lookup"><span data-stu-id="a169e-104">PowerShell Remoting is the recommended way to manage Windows systems.</span></span> <span data-ttu-id="a169e-105">在 Windows Server 2012 R2 中，默认启用 PowerShell 远程处理。</span><span class="sxs-lookup"><span data-stu-id="a169e-105">PowerShell Remoting is enabled by default in Windows Server 2012 R2.</span></span> <span data-ttu-id="a169e-106">本文档将介绍与使用 PowerShell 远程处理相关的安全问题、建议和最佳做法。</span><span class="sxs-lookup"><span data-stu-id="a169e-106">This document covers security concerns, recommendations, and best practices when using PowerShell Remoting.</span></span>

## <a name="what-is-powershell-remoting"></a><span data-ttu-id="a169e-107">PowerShell 远程处理是什么？</span><span class="sxs-lookup"><span data-stu-id="a169e-107">What is PowerShell Remoting?</span></span>

<span data-ttu-id="a169e-108">PowerShell 远程处理使用 [Windows 远程管理 (WinRM)](https://msdn.microsoft.com/en-us/library/windows/desktop/aa384426.aspx)（这是 [Web Services for Management (WS-Management)](http://www.dmtf.org/sites/default/files/standards/documents/DSP0226_1.2.0.pdf) 协议的 Microsoft 实现），允许用户在远程计算机上运行 PowerShell 命令。</span><span class="sxs-lookup"><span data-stu-id="a169e-108">PowerShell Remoting uses [Windows Remote Management (WinRM)](https://msdn.microsoft.com/en-us/library/windows/desktop/aa384426.aspx), which is the Microsoft implementation of the [Web Services for Management (WS-Management)](http://www.dmtf.org/sites/default/files/standards/documents/DSP0226_1.2.0.pdf) protocol, to allow users to run PowerShell commands on remote computers.</span></span> <span data-ttu-id="a169e-109">你可以在[运行远程命令](https://technet.microsoft.com/en-us/library/dd819505.aspx)处找到有关使用 PowerShell 远程处理的详细信息。</span><span class="sxs-lookup"><span data-stu-id="a169e-109">You can find more information about using PowerShell Remoting at [Running Remote Commands](https://technet.microsoft.com/en-us/library/dd819505.aspx).</span></span>

<span data-ttu-id="a169e-110">PowerShell 远程处理不同于使用 cmdlet 的 **ComputerName** 参数在远程计算机上运行它，后者使用远程过程调用 (RPC) 作为其基础协议。</span><span class="sxs-lookup"><span data-stu-id="a169e-110">PowerShell Remoting is not the same as using the **ComputerName** parameter of a cmdlet to run it on a remote computer, which uses Remote Procedure Call (RPC) as its underlying protocol.</span></span>

##  <a name="powershell-remoting-default-settings"></a><span data-ttu-id="a169e-111">PowerShell 远程处理默认设置</span><span class="sxs-lookup"><span data-stu-id="a169e-111">PowerShell Remoting default settings</span></span>

<span data-ttu-id="a169e-112">PowerShell 远程处理（和 WinRM）侦听以下端口：</span><span class="sxs-lookup"><span data-stu-id="a169e-112">PowerShell Remoting (and WinRM) listen on the following ports:</span></span>

- <span data-ttu-id="a169e-113">HTTP：5985</span><span class="sxs-lookup"><span data-stu-id="a169e-113">HTTP: 5985</span></span>
- <span data-ttu-id="a169e-114">HTTPS：5986</span><span class="sxs-lookup"><span data-stu-id="a169e-114">HTTPS: 5986</span></span>

<span data-ttu-id="a169e-115">默认情况下，PowerShell 远程处理仅允许来自管理员组成员的连接。</span><span class="sxs-lookup"><span data-stu-id="a169e-115">By default, PowerShell Remoting only allows connections from members of the Administrators group.</span></span> <span data-ttu-id="a169e-116">由于会话是在用户的上下文内启动的，因此所有应用于各个用户和组的操作系统访问控制会在他们通过 PowerShell 远程处理进行连接时继续应用于他们。</span><span class="sxs-lookup"><span data-stu-id="a169e-116">Sessions are launched under the user's context, so all operating system access controls applied to individual users and groups continue to apply to them while connected over PowerShell Remoting.</span></span>

<span data-ttu-id="a169e-117">在专用网络上，默认的 PowerShell 远程处理 Windows 防火墙规则接受所有连接。</span><span class="sxs-lookup"><span data-stu-id="a169e-117">On private networks, the default Windows Firewall rule for PowerShell Remoting accepts all connections.</span></span> <span data-ttu-id="a169e-118">在公用网络上，默认 Windows 防火墙规则仅允许来自同一子网内的 PowerShell 远程处理连接。</span><span class="sxs-lookup"><span data-stu-id="a169e-118">On public networks, the default Windows Firewall rule allows PowerShell Remoting connections only from within the same subnet.</span></span> <span data-ttu-id="a169e-119">必须明确地更改该规则，以将 PowerShell 远程处理打开到公用网络上的所有连接。</span><span class="sxs-lookup"><span data-stu-id="a169e-119">You have to explicitly change that rule to open PowerShell Remoting to all connections on a public network.</span></span>

><span data-ttu-id="a169e-120">**警告：**公用网络的防火墙规则旨在保护计算机免于潜在的恶意外部连接尝试。</span><span class="sxs-lookup"><span data-stu-id="a169e-120">**Warning:** The firewall rule for public networks is meant to protect the computer from potentially malicious external connection attempts.</span></span> <span data-ttu-id="a169e-121">请谨慎删除此规则。</span><span class="sxs-lookup"><span data-stu-id="a169e-121">Use caution when removing this rule.</span></span>

## <a name="process-isolation"></a><span data-ttu-id="a169e-122">进程隔离</span><span class="sxs-lookup"><span data-stu-id="a169e-122">Process isolation</span></span>

<span data-ttu-id="a169e-123">PowerShell 远程处理使用 [Windows 远程管理 (WinRM)](https://msdn.microsoft.com/en-us/library/windows/desktop/aa384426)来实现计算机之间的通信。</span><span class="sxs-lookup"><span data-stu-id="a169e-123">PowerShell Remoting uses [Windows Remote Management (WinRM)](https://msdn.microsoft.com/en-us/library/windows/desktop/aa384426) for communication between computers.</span></span> <span data-ttu-id="a169e-124">WinRM 作为网络服务帐户下的服务运行，并生成以用户帐户运行的隔离进程以托管 PowerShell 实例。</span><span class="sxs-lookup"><span data-stu-id="a169e-124">WinRM runs as a service under the Network Service account, and spawns isolated processes running as user accounts to host PowerShell instances.</span></span> <span data-ttu-id="a169e-125">以一个用户身份运行的 PowerShell 实例无权访问以其他用户身份运行 PowerShell 实例的进程。</span><span class="sxs-lookup"><span data-stu-id="a169e-125">An instance of PowerShell running as one user has no access to a process running an instance of PowerShell as another user.</span></span>

## <a name="event-logs-generated-by-powershell-remoting"></a><span data-ttu-id="a169e-126">PowerShell 远程处理生成的事件日志</span><span class="sxs-lookup"><span data-stu-id="a169e-126">Event logs generated by PowerShell Remoting</span></span>

<span data-ttu-id="a169e-127">FireEye 提供了良好的事件日志和其他由 PowerShell 远程处理会话生成的安全证据的摘要。该摘要在</span><span class="sxs-lookup"><span data-stu-id="a169e-127">FireEye has provided a good summary of the event logs and other security evidence generated by PowerShell Remoting sessions, available at</span></span>  
<span data-ttu-id="a169e-128">[调查 PowerShell 攻击](https://www.fireeye.com/content/dam/fireeye-www/global/en/solutions/pdfs/wp-lazanciyan-investigating-powershell-attacks.pdf)中可用。</span><span class="sxs-lookup"><span data-stu-id="a169e-128">[Investigating PowerShell Attacks](https://www.fireeye.com/content/dam/fireeye-www/global/en/solutions/pdfs/wp-lazanciyan-investigating-powershell-attacks.pdf).</span></span>

## <a name="encryption-and-transport-protocols"></a><span data-ttu-id="a169e-129">加密和传输协议</span><span class="sxs-lookup"><span data-stu-id="a169e-129">Encryption and transport protocols</span></span>

<span data-ttu-id="a169e-130">从以下两个角度来考虑 PowerShell 远程处理连接的安全性会有所帮助：初始身份验证和正在进行的通信。</span><span class="sxs-lookup"><span data-stu-id="a169e-130">It is helpful to consider the security of a PowerShell Remoting connection from two perspectives: initial authentication, and ongoing communication.</span></span> 

<span data-ttu-id="a169e-131">无论使用哪种传输协议（HTTP 或 HTTPS），在使用每会话 AES-256 对称密钥完成初始身份验证后，PowerShell 远程处理将始终加密所有的通信。</span><span class="sxs-lookup"><span data-stu-id="a169e-131">Regardless of the transport protocol used (HTTP or HTTPS), PowerShell Remoting always encrypts all communication after initial authentication with a per-session AES-256 symmetric key.</span></span>
    
### <a name="initial-authentication"></a><span data-ttu-id="a169e-132">初始身份验证</span><span class="sxs-lookup"><span data-stu-id="a169e-132">Initial authentication</span></span>

<span data-ttu-id="a169e-133">身份验证确认客户端到服务器（理想情况下，从服务器到客户端）的身份。</span><span class="sxs-lookup"><span data-stu-id="a169e-133">Authentication confirms the identity of the client to the server - and ideally - the server to the client.</span></span>
    
<span data-ttu-id="a169e-134">当客户端连接到使用其计算机名称（即 server01 或 server01.contoso.com）的域服务器时，默认身份验证协议为 [Kerberos](https://msdn.microsoft.com/en-us/library/windows/desktop/aa378747.aspx)。</span><span class="sxs-lookup"><span data-stu-id="a169e-134">When a client connects to a domain server using its computer name (i.e.: server01, or server01.contoso.com), the default authentication protocol is [Kerberos](https://msdn.microsoft.com/en-us/library/windows/desktop/aa378747.aspx).</span></span>
<span data-ttu-id="a169e-135">Kerberos 保证用户标识和服务器标识，而不发送任何种类的可重用的凭据。</span><span class="sxs-lookup"><span data-stu-id="a169e-135">Kerberos guarantees both the user identity and server identity without sending any sort of reusable credential.</span></span>

<span data-ttu-id="a169e-136">当客户端使用其 IP 地址连接到域服务器，或连接到工作组服务器时，不能进行 Kerberos 身份验证。</span><span class="sxs-lookup"><span data-stu-id="a169e-136">When a client connects to a domain server using its IP address, or connects to a workgroup server, Kerberos authentication is not possible.</span></span> <span data-ttu-id="a169e-137">在这种情况下，PowerShell 远程处理依赖的是 [NTLM 身份验证协议](https://msdn.microsoft.com/en-us/library/windows/desktop/aa378749.aspx)。</span><span class="sxs-lookup"><span data-stu-id="a169e-137">In that case, PowerShell Remoting relies on the [NTLM authentication protocol](https://msdn.microsoft.com/en-us/library/windows/desktop/aa378749.aspx).</span></span> <span data-ttu-id="a169e-138">NTLM 身份验证协议可在不发送任何种类的可代理凭据的情况下确保用户标识。</span><span class="sxs-lookup"><span data-stu-id="a169e-138">The NTLM authentication protocol guarantees the user identity without sending any sort of delegable credential.</span></span> <span data-ttu-id="a169e-139">为了证明用户标识，NTLM 协议要求客户端和服务器通过用户的密码计算会话密钥，而不自行交换密码。</span><span class="sxs-lookup"><span data-stu-id="a169e-139">To prove user identity, the NTLM protocol requires that both the client and server compute a session key from the user's password without ever exchanging the password itself.</span></span> <span data-ttu-id="a169e-140">由于服务器通常不知道用户的密码，因此它会与域控制器进行通信，域控制器不仅知道用户的密码，还为服务器计算会话密钥。</span><span class="sxs-lookup"><span data-stu-id="a169e-140">The server typically does not know the user's password, so it communicates with the domain controller, which does know the user's password and calculates the session key for the server.</span></span> 
      
<span data-ttu-id="a169e-141">但 NTLM 协议不能保证服务器标识。</span><span class="sxs-lookup"><span data-stu-id="a169e-141">The NTLM protocol does not, however, guarantee server identity.</span></span> <span data-ttu-id="a169e-142">如同所有使用 NTLM 进行身份验证的协议一样，如果攻击者有权访问已加入域的计算机的帐户，则可以调用域控制器来计算 NTLM 会话密钥，从而模拟服务器。</span><span class="sxs-lookup"><span data-stu-id="a169e-142">As with all protocols that use NTLM for authentication, an attacker with access to a domain-joined computer's machine account could invoke the domain controller to compute an NTLM session-key and thereby impersonate the server.</span></span>

<span data-ttu-id="a169e-143">默认禁用基于 NTLM 的身份验证，但是可以通过配置目标服务器上的 SSL 或配置客户端上的 WinRM TrustedHosts 设置而允许使用。</span><span class="sxs-lookup"><span data-stu-id="a169e-143">NTLM-based authentication is disabled by default, but may be permitted by either configuring SSL on the target server, or by configuring the WinRM TrustedHosts setting on the client.</span></span>
    
#### <a name="using-ssl-certificates-to-validate-server-identity-during-ntlm-based-connections"></a><span data-ttu-id="a169e-144">使用 SSL 证书以在基于 NTLM 的连接过程中验证服务器标识</span><span class="sxs-lookup"><span data-stu-id="a169e-144">Using SSL certificates to validate server identity during NTLM-based connections</span></span>

<span data-ttu-id="a169e-145">由于 NTLM 身份验证协议无法确保目标服务器的标识（除非它已知道你的密码），因此你可以将目标服务器配置为使用适用于 PowerShell 远程处理的 SSL。</span><span class="sxs-lookup"><span data-stu-id="a169e-145">Since the NTLM authentication protocol cannot ensure the identity of the target server (only that it already knows your password), you can configure target servers to use SSL for PowerShell Remoting.</span></span> <span data-ttu-id="a169e-146">将 SSL 证书分配给目标服务器（如果证书是由客户端也信任的证书颁发机构颁发的话）可启用基于 NTLM 的身份验证，从而确保用户标识和服务器标识。</span><span class="sxs-lookup"><span data-stu-id="a169e-146">Assigning a SSL certificate to the target server (if issued by a Certificate Authority that the client also trusts) enables NTLM-based authentication that guarantees both the user identity and server identity.</span></span>
    
#### <a name="ignoring-ntlm-based-server-identity-errors"></a><span data-ttu-id="a169e-147">忽略基于 NTLM 的服务器标识错误</span><span class="sxs-lookup"><span data-stu-id="a169e-147">Ignoring NTLM-based server identity errors</span></span>
      
<span data-ttu-id="a169e-148">如果无法将 SSL 证书部署到服务器以进行 NTLM 连接，你可以将此服务器添加到 WinRM **TrustedHosts** 列表中，从而取消生成的标识错误。</span><span class="sxs-lookup"><span data-stu-id="a169e-148">If deploying a SSL certificate to a server for NTLM connections is infeasible, you may suppress the resulting identity errors by adding the server to the WinRM **TrustedHosts** list.</span></span> <span data-ttu-id="a169e-149">请注意，将服务器名称添加到 TrustedHosts 列表不得被视为任何形式的主机本身可信度声明，因为 NTLM 身份验证协议无法确保你实际要连接到的主机就是你想要连接到的主机。</span><span class="sxs-lookup"><span data-stu-id="a169e-149">Please note that adding a server name to the TrustedHosts list should not be considered as any form of statement of the trustworthiness of the hosts themselves - as the NTLM authentication protocol cannot guarantee that you are in fact connecting to the host you are intending to connect to.</span></span>
<span data-ttu-id="a169e-150">相反，应将 TrustedHosts 设置视为你想为其取消因无法验证服务器标识而生成的错误的主机列表。</span><span class="sxs-lookup"><span data-stu-id="a169e-150">Instead, you should consider the TrustedHosts setting to be the list of hosts for which you wish to suppress the error generated by being unable to verify the server's identity.</span></span>
    
    
### <a name="ongoing-communication"></a><span data-ttu-id="a169e-151">正在进行的通信</span><span class="sxs-lookup"><span data-stu-id="a169e-151">Ongoing Communication</span></span>

<span data-ttu-id="a169e-152">初始身份验证完成后，[PowerShell 远程处理协议](https://msdn.microsoft.com/en-us/library/dd357801.aspx)会使用每会话 AES-256 对称密钥对所有正在进行的通信进行加密。</span><span class="sxs-lookup"><span data-stu-id="a169e-152">Once initial authentication is complete, the [PowerShell Remoting Protocol](https://msdn.microsoft.com/en-us/library/dd357801.aspx) encrypts all ongoing communication with a per-session AES-256 symmetric key.</span></span>  


## <a name="making-the-second-hop"></a><span data-ttu-id="a169e-153">形成第二个跃点</span><span class="sxs-lookup"><span data-stu-id="a169e-153">Making the second hop</span></span>

<span data-ttu-id="a169e-154">默认情况下，PowerShell 远程处理使用 Kerberos（如果可用）或者 NTLM 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="a169e-154">By default, PowerShell Remoting uses Kerberos (if available) or NTLM for authentication.</span></span> <span data-ttu-id="a169e-155">这两种协议对远程计算机进行身份验证而无需向其发送凭据。</span><span class="sxs-lookup"><span data-stu-id="a169e-155">Both of these protocols authenticate to the remote machine without sending credentials to it.</span></span>
<span data-ttu-id="a169e-156">这是进行身份验证最安全的方式，但由于远程计算机没有用户的凭据，因此它不能以该用户的名义访问其他计算机和服务。</span><span class="sxs-lookup"><span data-stu-id="a169e-156">This is the most secure way to authenticate, but because the remote machine does not have the user's credentials, it cannot access other computers and services on the user's behalf.</span></span> <span data-ttu-id="a169e-157">这被称为“第二个跃点问题”。</span><span class="sxs-lookup"><span data-stu-id="a169e-157">This is known as the "second hop problem".</span></span>

<span data-ttu-id="a169e-158">有几种方法可以避免此问题。</span><span class="sxs-lookup"><span data-stu-id="a169e-158">There are several ways to avoid this problem.</span></span> <span data-ttu-id="a169e-159">若要深入了解这些方法以及每种方法的优缺点，请参阅[在 PowerShell 远程处理中形成第二个跃点](PS-remoting-second-hop.md)。</span><span class="sxs-lookup"><span data-stu-id="a169e-159">For descriptions of these methods, and the pros and cons of each, see [Making the second hop in PowerShell Remoting](PS-remoting-second-hop.md).</span></span>









