---

# required metadata
title: "Quickstart for Administrators configuring for operationalization - Microsoft R Server | Microsoft Docs"
description: "Getting started for Administrators"
keywords: ""
author: "j-martens"
ms.author: "jmartens"
manager: "jhubbard"
ms.date: "6/21/2017"
ms.topic: "get-started-article"
ms.prod: "microsoft-r"

# optional metadata
#ROBOTS: ""
#audience: ""
#ms.devlang: ""
#ms.reviewer: ""
#ms.suite: ""
#ms.tgt_pltfrm: ""
ms.technology: "deployr"
#ms.custom: ""

---

# Quickstart for administrating R Server's operationalization configuration

**Applies to:  Microsoft R Server 9.x**

>To benefit from Microsoft R Server’s deployment and operationalization features, you can configure R Server after installation to act as a deployment server and host analytic web services as well as to execute code remotely. For a general introduction to R Server for operationalization, read the [About](../what-is-operationalization.md) topic.

This guide is for system administrators of the operationalization feature in R Server. If you are responsible for creating or maintaining an evaluation or a production deployment of the R Server with the operationalization feature, then this guide is for you.

As an administrator, your key responsibilities are to ensure configuration for the operationalization feature is properly provisioned and configured to meet the demands of your user community. In this context, the following policies are of central importance:

-   Server [security policies](#security-policies), which include user authentication and authorization
-   Server [R package management policies](#r-package-policies)
-   Server [runtime policies](#runtime-policies), which affect availability, scalability, and throughput

Whenever your policies fail to deliver the expected runtime behavior or performance, you'll need to troubleshoot your deployment. For that we provide [diagnostic tools](configure-run-diagnostics.md) and numerous recommendations.


## Configure web & compute nodes for analytic deployment and remote execution

To benefit from Microsoft R Server’s web service deployment and remote execution features, you must first [configure R Server](../install/operationalize-r-server-one-box-config.md) after installation to act as a deployment server and host analytic web services. 

All configurations have at least a single web node and single compute node:

+ A **web node** acts as an HTTP REST endpoint with which users can interact directly to make API calls. The web node accesses data in the database, and send jobs to the compute node.

+ A **compute node** is used to execute R code as a session or service. Each compute node has its [own pool of R shells](configure-evaluate-capacity.md#r-shell-pool).

There are two types of configuration:
1. **One-box**: the simplest configuration is a single web node and compute node on a single machine as described in this [One-box configuration](../install/operationalize-r-server-one-box-config.md) article.

1. **Enterprise**: a configuration where multiple nodes are configured on multiple machines along with other enterprise features as described in this [Enterprise configuration](../install/operationalize-r-server-enterprise-config.md) article.

<a name="security"></a>

## Security policies

R Server has many features that support the creation of secure applications. Common security considerations, such as data theft or vandalism, apply regardless of the version of R Server you are using. Data integrity should also be considered as a security issue. If data is not protected, it is possible that it could become worthless if ad hoc data manipulation is permitted and the data is inadvertently or maliciously modified. In addition, there are often legal requirements that must be adhered to, such as the correct storage of confidential information. 

User access to the R Server and the operationalization services offered on its [API](concept-api.md) are entirely under your control as the server administrator. R Server's operationalization feature offers seamless integration with popular enterprise security solutions like Active Directory LDAP or Azure Active Directory. You can configure R Server to [authenticate](configure-authentication.md) using these methods to establish a trust relationship between your user community and the operationalization engine for R Server. Your users can then supply simple `username` and `password` credentials in order to verify their identity. [A token will be issued to an authenticated user.](how-to-manage-access-tokens.md)

In addition to authentication, you can add other enterprise security around Microsoft R Server such as:

+ Secured connections using [SSL/TLS 1.2](configure-https.md). For security reasons, we strongly recommend that SSL/TLS 1.2 be enabled in **all production environments.** 

+ [Cross-Origin Resource Sharing](configure-cors.md) to allow restricted resources on a web page to be requested from another domain outside the originating domain.

+ [Role-based access control](configure-roles.md) over web services in R Server.

Additionally, we recommend that you review the following [Security Considerations](configure-r-execution-security.md).

![Security](./media/configure-start-for-administrators/security.png)


## R package policies

The primary function of the operationalization feature is to support the execution of R code on behalf of client applications. One of your key objectives as an administrator is to ensure a reliable, consistent execution environment for that code.

The R code developed and deployed by data scientists within your community will frequently depend on one or more R packages. Those R packages may be hosted on [CRAN](http://cran.r-project.org/), [MRAN](http://mran.microsoft.com), [github](https://github.com/), in your own local CRAN repository or elsewhere.

Making sure that these R package dependencies are available to the code executing on R Server's operationalization feature requires active participation from you, the administrator. There are several R package management policies you can adopt for your deployment, which are detailed in this [R Package Management guide](configure-manage-r-packages.md).

## Runtime policies

The operationalization feature supports a wide range of runtime policies that affect many aspects of the server runtime environment. As an administrator, you can select the preferred policies that best reflect the needs of your user community.

### General

The external configuration file, appsettings.json defines a number of policies for the services. There is one appsettings.json file on each web node and on each compute node. This file contains a wide range of policy configuration options for that node. The location of this file depends on the R Server version, operating system, and the node. Learn more in this article: ["Editing the appsettings.json configuration file for R Server"](configure-find-admin-configuration-file.md).
 
### Asynchronous batch sizes

Your users can perform speedy real-time and batch scoring. To reduce the risk of resource exhaustion by a single user, you can set the maximum number of operations that a single caller can execute in parallel during a specific asynchronous batch job. 

This value is defined in `"MaxNumberOfThreadsPerBatchExecution"`  property in the appsettings.json on the web node. If you have multiple web nodes, we recommend you set the same values on every machine. 

### Availability

The operationalization feature consists of a number of web and compute nodes that combine to deliver the full capabilities of this R operationalization server. Each component can be configured for Active-Active High Availability (HA) in order to deliver a robust, reliable runtime environment.

You can configure R Server to use multiple Web Nodes for Active-Active backup / recovery using a load balancer.

For data storage high availability, you can leverage the high availability capabilities found in enterprise grade databases (SQL Server or PostgreSQL). Learn how to use one of those databases, [here](configure-remote-database-to-operationalize.md).

![High Availability](./media/configure-start-for-administrators/admin-HA.png)


<!--For a discussion of the available server, grid, and database HA policy options, see the [DeployR High Availability Guide](../deployr/deployr-admin-configure-high-availability.md).-->

### Scalability & throughput

In the context of a discussion on runtime policies, the topics of scalability and throughput are closely related. Some of the most common questions that arise when planning the configuration and provisioning of R Server for operationalization are:

-   How many users can I support?
-   How many web and compute nodes do I need?
-   What throughput can I expect?

The answer to these questions will ultimately depend on the configuration and size of the configuration and node resources allocated to your deployment.

To evaluate and simulate the capacity of a configuration, use the [Evaluate Capacity tool](configure-evaluate-capacity.md). You can also [adjust the pool size](configure-evaluate-capacity.md#pool) of available R shells for concurrent operations.
<!--For detailed information and recommendations on tuning the server and grid for optimal throughput, read the [DeployR Scale & Throughput Guide](../deployr/deployr-admin-scale-and-throughput.md).-->

## Troubleshooting

There is no doubt that, as an administrator, you've experienced failures with servers, networks, and systems—most probably at the very inopportune times. Likewise, your chosen runtime policies may sometime fail to deliver the runtime behavior or performance needed by your community of users.

When those failures occur in the operationalization environment, we recommend you first turn to the [diagnostic testing tool](configure-run-diagnostics.md) to attempt to identify the underlying cause of the problem.

Beyond the diagnostics tool, the [Troubleshooting](configure-run-diagnostics.md#troubleshooting) documentation offers suggestions and recommendations for common problems with known solutions.

## More resources

This section provides a quick summary of useful links for administrators working with R Server's operationalization feature.

>Use the table of contents to find all of the guides and documentation needed by the administrator.

**Key Documents**
-   [About Operationalization](../what-is-operationalization.md)
-   [Configuration](../install/operationalize-r-server-one-box-config.md)
-   [R Package Management](configure-manage-r-packages.md)
-   [Diagnostic Testing & Troubleshooting](configure-run-diagnostics.md)
-   [Capacity Evaluatation](configure-evaluate-capacity.md)
-   [Comparison between 8.x and 9.x](../whats-new-in-r-server.md)

**Other Getting Started Guides**
-   [How to integrate web services and authentication into your application](how-to-build-api-clients-from-swagger-for-app-integration.md)
-   [Data Scientists](concept-operationalize-deploy-consume.md)

**Support Channel**
-   [Microsoft R Server Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=microsoftr)