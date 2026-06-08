# Exercise 4: Semantic Caching for Performance Optimization 

### Estimated Duration: 60 Minutes

## Scenario

Contoso Ltd. is optimizing its AI applications to improve response time and reduce operational costs. With repeated and similar user queries, the organization requires an efficient mechanism to reuse responses and minimize unnecessary API calls.

In this exercise, you will implement semantic caching in the AI Gateway to enhance performance by serving responses for semantically similar queries, reducing latency and token consumption.

## Lab Overview 

In this exercise, you will implement semantic caching in the AI Gateway to optimize the performance of Azure OpenAI applications. You will configure caching based on semantic similarity rather than exact text matching, allowing the system to reuse responses for prompts with similar meanings. Using Visual Studio Code, you will deploy the required resources with Bicep templates, initialize environment variables, and analyze cache performance using Redis metrics and Python SDK calls. Verify that semantically similar prompts return cached results to confirm reduced latency, improved response times, and lower API token usage.

## Lab Objectives

In this exercise, you will be performing the following tasks:

- Task 1: Understand semantic caching principles

- Task 2: Configure semantic caching in the AI Gateway environment

## Task 1: Understand semantic caching principles

In this task, you will learn how semantic caching enhances AI performance by comparing the meaning of prompts using embeddings. When two prompts have similar meanings, the cached response is reused instead of sending a new request to the model. This process significantly speeds up responses and lowers API usage costs

### Introduction

Semantic caching is an advanced caching technique that improves the performance and efficiency of large language model (LLM) applications, such as those using Azure OpenAI.
Unlike traditional caching, which matches requests based on exact text or URLs, semantic caching uses meaning (semantics) to identify whether two queries are similar enough to reuse a previous response.

This approach enables faster responses, lower latency, and reduced token consumption by avoiding redundant calls to the backend Azure OpenAI service.

### How Semantic Caching Works

- When a user submits a prompt to an AI service, the system:

   - Generates a vector embedding of the input text using an embedding model (for example, text-embedding-ada-002).

   - Compares this embedding with vectors from previously cached requests.

      1. If the new prompt is semantically similar (based on a similarity score threshold) to a cached one, the cached response is returned.

      2. If not, the request is sent to the OpenAI model, and the new response is stored in the cache for future use.

- This technique ensures that responses for similar or repeated questions can be served instantly from the cache without reprocessing the entire request.

### Key Concepts

   | **Concept**                         | **Description**                               |
   | ----------------------------------- | --------------------------------------- |
   | Semantic Similarity                 | Measures how close two pieces of text are in meaning using vector embeddings.                  |
   | Embedding                           | A numerical vector representation of text that captures its semantic meaning.                  |
   | Similarity Threshold                | A configurable value (e.g., 0.9) determining how similar two inputs must be for a cache hit.   |
   | Cache Lookup                        | The process of searching for semantically similar previous prompts before calling the AI model.|
   | Cache Store                         | Saving the new prompt and its response to the cache for future reuse. |


### Benefits of Semantic Caching

- Reduces latency: Cached responses are returned almost instantly.

- Lowers token costs: Avoids unnecessary calls to Azure OpenAI models.

- Improves scalability: Decreases backend workload and bandwidth usage.

- Enhances user experience: Provides consistent and faster AI responses.


### Example Scenario

1. If one user asks:

   ```
   What is Azure Cognitive Search?
   ```

2. And another later asks:

   ```
   Can you explain what Azure Cognitive Search does?
   ```
   
Although the two prompts are worded differently, semantic caching identifies them as semantically similar and retrieves the earlier cached response, saving time and tokens.

## Task 2: Configure semantic caching in the AI Gateway environment

In this task, you will configure and test semantic caching within the AI Gateway using Visual Studio Code. You will initialize environment variables, deploy necessary resources using Bicep, and execute Python SDK calls to observe cache behavior. By analyzing Redis metrics, you will confirm performance gains through reduced response times and increased cache hits.

1. In Visual Studio Code, from the left navigation pane,  expand **lab (1)** folder and **semantic-caching (2)**, and finally click on **semantic-caching.ipynb (3)**.

    ![](./media/API-gateway-image48.png)

1. Once you’re in the **semantic-caching.ipynb** file, take a moment to review each session and read its description.

8. Scroll down to **Initialize notebook variables**. 
Enter the following details:

   - resource_group_name: **Q2a-APIM-RG-<inject key="DeploymentID" enableCopy="false"/>**

   - resource_group_location: *Use the same region selected in* **Exercise 1 -> Task 1**.

      - `eastus2`
      - `westus` 
      - `westus2`
      - `westus3`
      - `centralus`

         >### **Note:** <span style="color:maroon;"> Use the exact same region that was selected in **Exercise 1 -> Task 1** for all subsequent exercises and tasks in this lab. Using a different region may result in deployment or configuration failures.

   - aiservices_config: **foundry4-<inject key="DeploymentID" enableCopy="false"/>-e4t2**

   - apim_name: **apim-<inject key="DeploymentID" enableCopy="false"/>**

      >**Note:** Ensure that the correct name is entered in the respective section.

1. **Run** the cell **Initialize notebook variables**. This step sets up environment variables for the deployment, including the resource group name, region, AI Foundry configuration, OpenAI model details, Redis cache SKU, and API Management parameters. These variables ensure consistent and automated deployments across regions and subscriptions.

    ![](./media/apim-may26-e4t2p1.png)

1. Next, run **Verify the Azure CLI and the connected Azure subscription**. This cell checks that the Azure CLI is properly installed and signed in to your Azure subscription. It retrieves details like the current user, tenant ID, and subscription ID to confirm the correct environment before proceeding with resource creation.

   ![](./media/ver-e4t1.png)

1. Run **Create deployment using 🦾 Bicep**. Here, the Bicep template automatically deploys all required resources, including APIM, Azure OpenAI models, and Managed Redis Cache into the resource group. The parameters are dynamically generated and written to a JSON file before executing the deployment command.

    ![](./media/deploy-e4t1.png)

    >**Note:** The deployment usually takes about 15-30 minutes. You can proceed to Exercise 5 while it runs, and then return to finish this exercise once the deployment is complete.

    >**Note:** <span style="color:maroon;"> If the deployment does not succeed, from the left navigation pane,  expand **lab (1)** folder and then **semantic-caching (2)**, right click on **params.json (3)** and **delete (4)** it.  
   >
    >![](./media/E4T4P4.png)
   >
   > <span style="color:maroon;"> Then go back to the cell **Initialize notebook variables** and add **`wus`** after the following values:
   >
   >- resource_group_name:    **Q2a-APIM-RG-<inject key="DeploymentID" enableCopy="false"/> (1)**
   >
   >- aiservices_config: **foundry4-<inject key="DeploymentID" enableCopy="false"/> (2)**
   >
   >- apim_name: **apim-<inject key="DeploymentID" enableCopy="false"/> (3)**
   >    
   >- and enter the location as **"westus", "westus2" or "centralus" (4)** and then re-run the cells.
   >
   >     ![](./media/E4T4P41.png)

1. Run **Get the deployment outputs**. After deployment, the script retrieves and displays key outputs such as the APIM Gateway URL, subscription keys, and Redis connection information. These details are required for connecting to the API endpoint and managing cache operations in the next steps.
    
    ![](./media/outputs-e4t1.png)

1. Run **Make multiple calls using the Azure OpenAI Python SDK**. This section sends multiple requests with semantically similar prompts (e.g., different ways of asking about coffee making) to test caching behavior. The first call is slower since it reaches the backend model, while subsequent ones are faster as they are served from the cache.

   ![](./media/make-e4t1.png)

   ![](./media/make-e4t1(1).png)

1. Run **Analyze Semantic Caching performance**. Response times from the multiple API calls are plotted on a bar graph to visualize caching efficiency. You’ll notice reduced latency after the first call, showing that semantic caching successfully retrieves results from the cache instead of querying the backend repeatedly

   ![](./media/analyze-e4t1.png)

1. Run **Show Redis Cache information**. This part connects to the Redis cache and displays key metrics like cache hits, misses, and evictions. A higher number of cache hits indicates effective reuse of responses, confirming that semantic caching is optimizing API performance.

    ![](./media/redis-e4t1.png)

    ![](./media/redis-e4t1(1).png)

> **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
> - If you receive a success message, you can proceed to the next task.
> - If not, carefully read the error message and retry the step, following the instructions in the lab guide. 
> - If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com. We are available 24/7 to help you out.

<validation step="ed8cba73-817c-4ca0-8de9-f237b92cd785" />

## Summary 

In this exercise, you explored how semantic caching improves the efficiency and cost-effectiveness of Azure OpenAI-based solutions by reusing results for prompts with similar meanings. You learned how embeddings allow AI systems to detect semantic similarity between queries, enabling cached responses to be served instantly rather than reprocessed by the model. By deploying semantic caching within the AI Gateway, you observed measurable improvements in response latency and cache hit rates, confirming that repeated or related queries can be served from cache rather than through new model invocations. You also reviewed Redis cache statistics to understand how cache hits, misses, and evictions reflect real-world performance optimization.
Through this hands-on implementation, you gained practical insights into how semantic caching supports faster, more scalable, and more cost-efficient AI applications, a key optimization strategy for production-grade Azure OpenAI deployments.

### You have successfully completed the exercise. Click on Next >> to proceed with the next exercise.

![](./media/nextpage-api.png)
