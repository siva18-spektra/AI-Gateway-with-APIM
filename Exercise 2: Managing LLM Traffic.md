# Exercise 2: Managing LLM Traffic

### Estimated Duration: 75 Minutes

## Scenario

Contoso Ltd. is experiencing increased usage of its AI-powered applications, leading to higher token consumption and cost concerns. To ensure efficient resource utilization and maintain performance, the organization needs better visibility and control over AI traffic.

In this exercise, you will implement monitoring and governance using Azure API Management (APIM) by capturing token usage metrics and applying rate-limiting policies to optimize and control LLM consumption.

## Lab Overview

In this exercise, you will manage and optimize Large Language Model (LLM) traffic using Azure API Management (APIM) and Microsoft Foundry. You will first capture and analyze token usage metrics from API requests to monitor consumption trends and identify spikes in activity. Then, you will apply token rate-limiting policies using APIM to control traffic and prevent overuse of Microsoft Foundry resources. Using Visual Studio Code and Bicep templates, you will deploy the required infrastructure, run tests with Python SDKs, and visualize token data in Application Insights. Confirm that token usage metrics are emitted correctly and that rate-limiting policies enforce consumption limits effectively.

## Lab Objectives

In this exercise, you will be performing the following tasks:

- Task 1: Capture token usage metrics from AI Gateway traffic 

- Task 2: Apply rate-limiting policies to control token consumption

## Task 1: Capture token usage metrics from AI Gateway traffic 

In this task, you will deploy APIM and Microsoft Foundry endpoints, set up models and subscriptions, and prepare the environment for testing. You will then capture, analyze, and visualize token usage metrics to monitor API consumption patterns.

1. In Visual Studio Code, expand the **labs (1)** folder and then select **token-metrics-emitting (2)**, and finally click on **token-metrics-emitting.ipynb (3)**.

     ![](./media/e2t1p1.png)

8. Scroll down to **Initialize notebook variables** and enter the following details:

   - resource_group_name: **Q2a-APIM-RG-<inject key="DeploymentID" enableCopy="false"/>**

   - resource_group_location: *Use the same region selected in* **Exercise 1 -> Task 1**.

      - `eastus2`
      - `westus` 
      - `westus2`
      - `westus3`
      - `centralus`

         >### **Note:** <span style="color:maroon;"> Use the exact same region that was selected in **Exercise 1 -> Task 1** for all subsequent exercises and tasks in this lab. Using a different region may result in deployment or configuration failures.

   - aiservices_config: **foundry4-<inject key="DeploymentID" enableCopy="false"/>-e2t1**

   - apim_name: **apim-<inject key="DeploymentID" enableCopy="false"/>**

      >### **Note:** <span style="color:maroon;"> Ensure that the correct name is entered in the respective section.

1. Run the cell **Initialize notebook variables**. Prepare the environment, define resources, models, and subscriptions so everything is ready for deployment.

   ![](./media/apim-may26-e2t1p1.png)

   >**Note:** Select the **Python 3.12.1** kernel from the top bar.
   >
   >![](./media/apim-p2t1p2-new.png)

2. Next, go to **Verify the Azure CLI and the connected Azure subscription** and click on Run. This ensures the commands will run in the correct Azure subscription and that we have permissions to create resources.

   ![](./media/ver-e2t1.png)
   
3. Run the cell **Create deployment using 🦾 Bicep**. This cell automates the creation of APIM, Microsoft Foundry endpoints, and model deployments so that the environment is consistent and reproducible.

   ![](./media/deploy-e2t1.png)

   >**Note:** If you encounter any errors, wait a few minutes and then re-execute the cell.

4. Run the cell **Get the deployment outputs**. This retrieves URLs, keys, and Application Insights names that are needed to interact with the deployed AI endpoints.

   ![](./media/outputs-e2t1.png)
   
5. Run the cell **Test the API using a direct HTTP call**. Simulate real API requests to ensure that the token metrics are emitted correctly and that the agent responds as expected.

   ![](./media/test-e2t1.png)

   ![](./media/test-e2t1(1).png)

6. Run the cell **Execute multiple runs for each subscription using the Microsoft Foundry Python SDK**. Verify that metrics are tracked consistently across multiple subscriptions and API keys using the official Microsoft Foundry SDK.

   ![](./media/execute-e2t1.png)

   ![](./media/execute-e2t1(1).png)

7. Run the cell **Analyze Application Insights custom metrics with a KQL query**, this Query and extract token usage data to monitor consumption and identify patterns.

    ![](./media/apim-may26-e2t2p2.png)

    >**Note:** If you get an error or the table doesn’t appear, wait for few minutes and execute the cell again.

8. Run the cell **Plot the custom metrics results**, this visualizes token usage over time to see trends, spikes, or anomalies.

    ![](./media/apim-may26-e2t2p3.png)

    >**Note:** The results you see may vary from the screenshot shown above.

9. Next, **See the metrics on the Azure Portal**. Validate the token consumption visually for each subscription in Application Insights for easier monitoring.
    
    ![](./media/API-gateway-image34.png)

1. Open the Azure Portal in the browser and sign in with the credentials provided in the **Environment** tab.

1. Click on the **Resource groups** from the homepage.

   ![](./media/e2t1-rg(1).png)

1. Under the **Resource groups** blade, select **Q2a-APIM-RG-<inject key="DeploymentID" enableCopy="false"/>**.

   ![](./media/rg-(1).png)

1. Select the **Application Insights** resource.

   ![](./media/rg-(2).png)

1. From the left navigation pane, expand **Monitoring (1)** and then select **Metrics (2)**.

   ![](./media/e2t1-appin(1).png)

1. Enter the following values in the respective fields:

   - Metric Namespace: **openai (1)**
   - Metric: **Total Tokens (2)**
   - Aggregation: **Sum (3)**
   - Click on **Apply splitting (4)**.
   
      ![](./media/appin-e2t1.png)

   - Values: **Subscription ID (5)**

      ![](./media/appin-e2t1(1).png)

      >**Note:** If the **openai** Metric Namespace isn’t visible, select **Log-based metrics** instead.

## Task 2: Apply rate-limiting policies to control token consumption

In this task, you will deploy APIM, Microsoft Foundry endpoints, and model subscriptions, and configure token rate-limiting policies. You will test API requests, monitor token usage, and verify that the rate limiting enforces consumption limits correctly.

1. In Visual Studio Code, from the left navigation pane, select **Explorer (1)**, then expand the **lab (2)** folder and select **token-rate-limiting (3)**, and click on **token-rate-limiting.ipynb (4)**.

   ![](./media/e2t2p1.png)

8. Scroll down to **Initialize notebook variables** and enter the following details:

   - resource_group_name: **Q2a-APIM-RG-<inject key="DeploymentID" enableCopy="false"/>**

   - resource_group_location: *Use the same region selected in* **Exercise 1 -> Task 1**.

      - `eastus2`
      - `westus` 
      - `westus2`
      - `westus3`
      - `centralus`

         >### **Note:** <span style="color:maroon;"> Use the exact same region that was selected in **Exercise 1 -> Task 1** for all subsequent exercises and tasks in this lab. Using a different region may result in deployment or configuration failures.

   - aiservices_config: **foundry4-<inject key="DeploymentID" enableCopy="false"/>-e2t2**

   - apim_name: **apim-<inject key="DeploymentID" enableCopy="false"/>**

      >### **Note:** <span style="color:maroon;">Ensure that the correct name is entered in the respective section.

1. Run the cell **Initialize Notebook Variables**. This set up all lab variables, including resource group, location, AI services, models, APIM SKU, subscriptions, API paths, and utility functions. Prepares the environment for deployment.

   ![](./media/apim-may26-e2t2p1.png)

   >**Note:** Select the **Python 3.12.1** kernel from the top bar.
   >
   >![](./media/apim-p2t1p2-new.png)
   
1. Run **Verify Azure CLI and Subscription**. Check that Azure CLI is installed and connected. Retrieve and display the current user, tenant ID, and subscription ID to confirm correct access.
   
   ![](./media/ver-e2t2.png)

2. Run the cell **Create Deployment Using Bicep**. Deploy APIM, Microsoft Foundry endpoints, and model subscriptions using Bicep. Automates resource provisioning and writes parameters to params.json.

   ![](./media/deploy-e2t2.png)

   >**Note:** If you encounter any errors, wait a few minutes and then re-execute the cell.

3. Run the cell **Get Deployment Outputs**. Retrieve APIM Service ID, API Gateway URL, subscription keys, and other deployment outputs. These values are required for testing and API calls.
  
   ![](./media/outputs-e2t2.png)

4. Run the cell **Test API Using Direct HTTP Calls**. Send repeated requests to the deployed AI endpoint using Python requests. Monitor response time, status codes, and token usage to verify token limiting behavior.

   ![](./media/test-e2t2.png)

5. Run the cell **Analyze Token Rate limiting results**. Convert API run results into a DataFrame and plot tokens per request. Helps visualize rate limiting and identify when 429 errors occur.

   ![](./media/analyze-e2t2.png)

   >**Note:** Result you get may vary from the image above.

6. Run the cell **Test the API Using Microsoft Foundry Python SDK**. Send requests using the Microsoft Foundry SDK to confirm retries and proper handling of token limits. Observes consistent behavior across SDK calls.

   ![](./media/test2-e2t2.png)

## Summary

In this exercise, you learned how to monitor, analyze, and control LLM traffic using Azure API Management and Microsoft Foundry. You deployed the required infrastructure, captured token usage metrics, and visualized consumption patterns in Application Insights. By executing requests through multiple APIM subscriptions (subscription keys), you gained visibility into token consumption across different applications or consumers, enabling more effective usage tracking and governance. You also configured and validated token rate-limiting policies to ensure excessive requests were throttled and consumption limits were enforced consistently. By the end of the exercise, you verified that APIM could effectively monitor usage, govern access, optimize costs, and maintain reliable performance for Microsoft Foundry workloads at scale.

### You have successfully completed the exercise. Click on **Next >>** to proceed with the next exercise.

![](./media/nextpage-api.png)
