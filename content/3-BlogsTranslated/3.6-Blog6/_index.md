+++
title = " Blog 6"
type = ""
weight = 6
pre = "<b> 3.6. </b>"
+++

# **GitOps continuous delivery with ArgoCD and EKS using natural language**

_Jagdish Komakula, Aditya Ambati, and Anand Krishna Varanasi_ | 17/07/2025 | [ Amazon Elastic Kubernetes Service](https://aws.amazon.com/blogs/devops/category/compute/amazon-kubernetes-service/), [Amazon Q](https://aws.amazon.com/blogs/devops/category/amazon-q/), [Amazon Q Developer](https://aws.amazon.com/blogs/devops/category/amazon-q/amazon-q-developer/), [Developer Tools](https://aws.amazon.com/blogs/devops/category/developer-tools/), [Technical How-to ](https://aws.amazon.com/blogs/devops/category/post-types/technical-how-to/) | [Permalink](https://aws.amazon.com/blogs/devops/gitops-continuous-delivery-with-argocd-and-eks-using-natural-language/)

## Introduction

ArgoCD is a leading GitOps tool that empowers teams to manage Kubernetes deployments declaratively, using Git as the single source of truth. Its robust feature set, including automated sync, rollback support, drift detection, advanced deployment strategies, RBAC integration, and multi-cluster support, makes it a go-to solution for Kubernetes application delivery. However, as organizations scale, several pain points and operational challenges become apparent.

## Pain Points with Traditional ArgoCD Usage

- GArgoCD’s UI and CLI are designed for users with extensive technical background. Interacting with YAML manifests, understanding Kubernetes resource relationships, and troubleshooting sync errors require specialized knowledge. This limits access to GitOps workflows for less technical stakeholders and increases reliance on DevOps engineers.

- Managing ArgoCD across multiple clusters or environments (using hub-spoke, per-cluster, or grouped models) introduces significant operational complexity. Teams must handle multiple ArgoCD instances, maintain consistent configuration, and coordinate deployments, which can become a bottleneck as service footprints grow.

- ArgoCD excels at syncing and monitoring Kubernetes resources but lacks built-in mechanisms for pre-deployment (e.g., image scanning) or post-deployment (e.g., load testing) tasks. This forces teams to rely on external tools or custom scripts, fragmenting the deployment pipeline and increasing maintenance effort.

- Promoting applications across environments (Dev → Test → Prod) is not natively streamlined. Teams must manually orchestrate or script these promotions, slowing down urgent fixes and complicating the release process.

- As organizations adopt multi-cluster strategies, managing ArgoCD’s access, RBAC, and resource visibility across environments becomes cumbersome, often leading to fragmented workflows and potential security gaps.

## How ArgoCD MCP Server with Amazon Q CLI addresses these challenges:

- The integration of the ArgoCD MCP (Model Context Protocol) Server with [Amazon Q CLI](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/command-line.html) fundamentally transforms the user experience by introducing natural language interaction for GitOps operations.

- With MCP, users can manage deployments, monitor application states, and perform sync or rollback operations using plain conversational language rather than technical commands or YAML. For example, a user can simply ask, “What applications are out of sync in production?” or “Sync the api-service application,” and the system executes the appropriate ArgoCD API calls in the background.

- This democratizes access to GitOps, enabling less technical team members (such as QA, product managers, or support engineers) to safely interact with deployment workflows.

- Natural language interfaces abstract away the complexity of multi-cluster and multi-environment management. Users can query or act on resources across clusters without memorizing resource names, namespaces, or API endpoints.

- The MCP server handles authentication, session management, and robust error handling, reducing the need for manual troubleshooting and custom scripting.

- The integration provides detailed feedback, intelligent endpoint handling, and comprehensive error messages, making it easier to diagnose and resolve issues. Full static type checking and environment-based configuration further enhance reliability and maintainability.

- By leveraging Amazon Q CLI’s extensibility, users gain access to pre-built integrations and context-aware prompts, accelerating development and deployment workflows.

- The MCP server enables AI assistants and language models to automate routine tasks, recommend actions, and even debug issues, acting as a virtual DevOps engineer. This can significantly reduce manual effort and speed up incident response.

## Traditional ArgoCD vs. ArgoCD MCP Server with Amazon Q CLI

| Feature/Challenge         | Traditional ArgoCD              | With MCP Server + Amazon Q CLI         |
| ------------------------- | ------------------------------- | -------------------------------------- |
| User Interface            | Technical UI/CLI, YAML required | Natural language, conversational       |
| Access for Non-Engineers  | Limited                         | Broad, democratized                    |
| Multi-Cluster Management  | Complex, manual                 | Simplified, abstracted                 |
| Pre-Post Deployment Tasks | External tools/scripts needed   | (Still external, but easier to invoke) |
| Application Promotion     | Manual or scripted              | Natural language, easier orchestration |
| Troubleshooting           | Technical, error-prone          | Guided, AI-assisted, detailed feedback |
| Automation                | Scripting required              | AI/agent-driven, proactive             |

You can perform the following [actions](https://github.com/akuity/argocd-mcp?tab=readme-ov-file#available-tools) using natural language using Amazon Q CLI integration with ArgoCD MCP server.

- **Application Management**: List, create, update, and delete ArgoCD applications

- **Sync Operations**: Trigger sync operations and monitor their status

- **Resource Tree Visualization**: View the hierarchy of resources managed by applications

- **Health Status Monitoring**: Check the health of applications and their resources

- **Event Tracking**: View events related to applications and resources

- **Log Access**: Retrieve logs from application workloads

- **Resource Actions**: Execute actions on resources managed by applications

## Setting Up Your Environment

### Pre-requisites

Following are the pre-requisites for setting up your EKS environment to be managed by ArgoCD using Amazon Q CLI.

- An AWS account with appropriate permissions

- AWS CLI v2.13.0 or later

- Node.js v18.0.0 or later

- npm v9.0.0 or later

- Amazon Q CLI v1.0.0 or later (`npm install -g @aws/amazon-q-cli`)

- An EKS cluster (v1.27 or later) with ArgoCD v2.8 or later installed

### Connecting to your EKS cluster

1. Use AWS CLI to update your kubeconfig

`aws eks update-kubeconfig --name <cluster_name> --region <region> --role-arn <iam_role_arn>`

2. Verify ArgoCD pods are running properly in the argocd namespace

`kubectl get pods -n argocd`

3. Access the ArgoCD server UI locally using port forwarding command

`kubectl port-forward svc/blueprints-addon-argocd-server -n argocd 8080:443`

### Create AgroCD API Token

1. Access the **ArgoCD UI** at [https://localhost:8080](https://localhost:8080)
2. Log in with the admin credentials
3. Navigate to User **Settings > API Tokens**
4. Click **“Generate New”** to create a token
5. Create an Amazon Q CLI MCP configuration file at`.amazonq/mcp.json` and update the **ARGOCD_BASE_URL** and **ARGOCD_API_TOKEN** as per your environment setup.

### Integrating with Amazon Q CLI

```JSON
{
  "mcpServers": {
    "argocd-mcp-stdio": {
      "type": "stdio",
      "command": "npx",
      "args": [
         "argocd-mcp@latest",
         "stdio"
      ],
      "env": {
        "ARGOCD_BASE_URL": "<ARGOCD_BASE_URL>",
        "ARGOCD_API_TOKEN": "<ARGOCD_API_TOKEN>",
        "NODE_TLS_REJECT_UNAUTHORIZED": "0"
      }
    }
  }
}
```
Once configured, you can start using natural language commands with Amazon Q CLI to interact with your ArgoCD applications.

### Managing ArgoCD applications using natural language

Listed below are some example prompts to interact with ArgoCD applications in your EKS cluster.

**List ArgoCD application**

**Prompt**: `List all ArgoCD applications in my cluster`

![example](../../../images/Blog6/17632-image-1.png)

<p style="text-align: center;"><i>Amazon Q will use the ArgoCD MCP server to retrieve and display all applications</i></p>

**Create new ArgoCD application**

**Prompt**: `Create new argocd application using App name: game-2048   Repo: https://github.com/aws-ia/terraform-aws-eks-blueprints  Path: patterns/gitops/getting-started-argocd/k8s. Branch: main  Namespace: argocd`

![example](../../../images/Blog6/17632-image-2.png)

<p style="text-align: center;"><i>Amazon Q will create a new application from GitRepo information provided</i></p>

**Viewing deployment status**

**Câu lệnh**: `Show me the resource tree for team-carmen app`

![example](../../../images/Blog6/17632-image-8.png) 

<p style="text-align: center;"><i>Amazon Q will display the hierarchy of Kubernetes resources managed by the application</i></p>

**Synchronizing applications**

**Prompt**: `Show me the applications that’s out of sync`

![example](../../../images/Blog6/17632-image-3.png)

<p style="text-align: center;"><i>Amazon Q will display the out of sync applications</i></p>

**Prompt**: `Sync the application`

![example](../../../images/Blog6/17632-image-4.png)

<p style="text-align: center;"><i>Amazon Q syncing application</i></p>

Amazon Q will:

- Initiate a sync operation for the specified application
- Monitor the sync progress
- Report the final status of the sync operation

**Healthchecks and monitoring**

**Prompt**: `Check the health of all resources in the team-geordie application`

![example](../../../images/Blog6/17632-image-6.png)

<p style="text-align: center;"><i>Amazon Q showing health status of all the resources in an application</i></p>

Amazon Q will:

- Retrieve the health status of all resources
- Identify any unhealthy components
- Provide recommendations for addressing issues

**Prompt**: `Show me the logs for the failing pod in the team-platform application`

![example](../../../images/Blog6/17632-image-7.png)

<p style="text-align: center;"><i>Amazon Q showing logs of problematic pod</i></p>

Amazon Q will:

- Identify problematic pods
- Retrieve and display relevant logs
- Highlight potential error messages

## Conclusion

The integration of Amazon Q CLI with ArgoCD through the MCP server marks a transformative advancement in Kubernetes management, combining ArgoCD’s GitOps capabilities with Amazon Q’s natural language processing. By transforming complex Kubernetes operations into simple conversational interactions, this solution allows teams to focus on what truly matters – creating value for their business. Rather than spending time memorizing commands or navigating technical complexities, teams can now manage their cloud infrastructure through natural dialogue, making the cloud-native journey more accessible and efficient for everyone.Ready to transform your EKS and ArgoCD experience? It’s highly recommended to try out Amazon Q CLI integration with ArgoCD MCP and discover why DevOps teams are making it an essential part of their toolkit.
