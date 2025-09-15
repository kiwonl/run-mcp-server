# Currency MCP Server 💵

A simple MCP server that provides exchange rate information using the Frankfurter API.

## Deploying the MCP Server on Cloud Run

1.  **Enable APIs (Cloud Run, Artifact Registry, Cloud Build)**

    ```bash
    gcloud services enable \
      run.googleapis.com \
      artifactregistry.googleapis.com \
      cloudbuild.googleapis.com
    ```

2.  **Deploy to Cloud Run**

    ```bash
    gcloud run deploy currency-mcp-server \
        --no-allow-unauthenticated \
        --region=us-central1 \
        --source=.
    ```

## Testing the MCP Server using the Gemini CLI in Cloud Shell

1.  **Add Cloud Run Invoker permission to the Cloud Shell User**

    ```bash
    gcloud projects add-iam-policy-binding $GOOGLE_CLOUD_PROJECT \
        --member=user:$(gcloud config get-value account) \
        --role='roles/run.invoker'
    ```

2.  **Declare environment variables**

    ```bash
    # Get the service URL from the deployed Cloud Run service
    export MCP_SERVER_URL=$(gcloud run services describe currency-mcp-server --region=us-central1 --format="value(status.url)")

    # Get an identity token for authentication
    export ID_TOKEN=$(gcloud auth print-identity-token)
    ```

3.  **Configure the MCP server in the Gemini CLI**

    ```bash
    cat <<EOF > ~/.gemini/settings.json
    {
      "selectedAuthType": "cloud-shell", // only cloudshell
      "mcpServers": {
        "exchange-mcp-server": {
          "httpUrl": "${MCP_SERVER_URL}/mcp/",
          "headers": {
            "Authorization": "Bearer ${ID_TOKEN}"
          }
        }
      }
    }
    EOF
    ```

4.  **Test in the Gemini CLI**

    ![alt text](./images/01.png)
    ![alt text](./images/02.png)

5.  **References**

    - [Currency Agent Codelab](https://codelabs.developers.google.com/codelabs/currency-agent#0)
    - [How to deploy a secure MCP server on Cloud Run Codelab](https://codelabs.developers.google.com/codelabs/cloud-run/how-to-deploy-a-secure-mcp-server-on-cloud-run#0)
