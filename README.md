# AlloyDB Custom Metrics to Google Cloud Monitoring

This Node.js application fetches order statistics from an AlloyDB database and publishes them as custom metrics to Google Cloud Monitoring.

## Prerequisites

1.  **Node.js:** Ensure you have Node.js (version 12 or higher) installed.
2.  **Google Cloud SDK:** Have the `gcloud` CLI installed and authenticated.
    ```bash
    gcloud auth application-default login
    ```
3.  **GCP Project:** A Google Cloud project with billing enabled.
4.  **AlloyDB Instance:** An active AlloyDB for PostgreSQL instance with a database created.
5.  **AlloyDB Table:** The `orders` table must be created in your database.
    ```bash
    npm install
    ```
    Use the following SQL to create the table and add some sample data:
    ```sql
    CREATE TABLE public.orders (
        id bigserial NOT NULL,
        amount int4 NULL,
        category varchar(255) NULL,
        description varchar(255) NULL,
        payment varchar(255) NULL,
        status varchar(50) NOT NULL,  
        CONSTRAINT orders_pkey PRIMARY KEY (id)
    );

    INSERT INTO orders (status) VALUES ('PENDING'), ('PROCESSED');
    ```
6.  **IAM Permissions:** Ensure the principal (user, service account) running the script has the following IAM roles:
    *   `roles/alloydb.client`
    *   `roles/monitoring.metricWriter`
    *   `roles/monitoring.notificationChannelEditor`
    *   `roles/monitoring.alertPolicyEditor`

## Configuration

The application is configured using environment variables. Set the following variables in your shell before running the script.

```bash
# Replace with your actual configuration details
export GOOGLE_CLOUD_PROJECT="your-gcp-project-id"
export DB_USER="your-db-user"
export DB_PASS="your-db-password"
export DB_HOST="127.0.0.1" # Use 127.0.0.1 if using the AlloyDB Auth Proxy
export DB_NAME="your-db-name"
export DB_PORT="5432"
```

## Running the Metric Publishing Script

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
