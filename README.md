# Instructions for setting up Observability in GCP via Custom Metrics

This Node.js application fetches order statistics from an AlloyDB database and publishes them as custom metrics to Google Cloud Monitoring.

## Prerequisites

1.  **Node.js:** Ensure you have Node.js (version 12 or higher) installed.
2.  **gcloud CLI:** Have the Google Cloud SDK installed and authenticated.
    ```bash
    gcloud auth application-default login
    ```
3.  **Dependencies:** Install the required Node.js packages.
    ```bash
    npm install pg @google-cloud/monitoring
    ```
4.  **AlloyDB Table:** Make sure you have the `orders` table created in your database as per the specified schema.

## Configuration

Before running the application, you need to configure your GCP project ID and AlloyDB connection details in `index.js`:

1.  Open `index.js`.
2.  Set the `projectId` variable to your Google Cloud project ID.
3.  Update the `dbConfig` object with your AlloyDB user, private IP address, database name, and password.

## Running the Application

Once configured, you can run the application from your terminal:

```bash
node index.js
```

The script will fetch the counts for `PENDING` and `PROCESSED` orders and publish them to Cloud Monitoring under the following metric names:
- `custom.googleapis.com/orders/backlog_count`
- `custom.googleapis.com/orders/processed_count`

You can then view these metrics in the Metrics Explorer in the Google Cloud console. You might run this script periodically using a cron job or a Cloud Scheduler job to keep your metrics updated.

Assumption user has Monitoring Metrics Writer IAM role to publish metrics.
 Setup code and run node index.js
 
 gcloud beta monitoring channels list
 gcloud beta monitoring channels create   --display-name="Backlog Email Alerts"   --type=email   --channel-labels=email_address="kxxxxxxxxxh@gmail.com"
 gcloud beta monitoring channels list
 
 mkdir alert-policy
 cd alert-policy/
 Put reference of monitoring channel id.
 nano pending_orders.json
 gcloud alpha monitoring policies create --policy-from-file=pending_orders.json
