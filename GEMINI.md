I have one orders table in AlloyDB with below schema.
CREATE TABLE public.orders (
	id bigserial NOT NULL,
	amount int4 NULL,
	category varchar(255) NULL,
	description varchar(255) NULL,
	payment varchar(255) NULL,
    status varchar(50) NOT NULL,  
	CONSTRAINT orders_pkey PRIMARY KEY (id)
);
I want to generate custom metrics for my orders data backlog from alloydb database based on status and publish metrics to gcp cloud monitoring using a nodejs application.
Can you write code for the same in the current project. I have already created a skelteon project and installed pg and cloud monitoring node packages.
Name of Custom Metrics should be custom.googleapis.com/orders/backlog_count

Criteria for backlog is below SQL

SELECT COUNT(*) AS backlog
FROM orders
WHERE status = 'PENDING';

I also want to track processed orders.
Name of Custom Metrics should be custom.googleapis.com/orders/processed_count

Criteria for processed orders is below SQL

SELECT COUNT(*) AS processed_count
FROM orders
WHERE status = 'PROCESSED';

Can you modify the current code to get pending and processed records in one go and generate metrics accordingly ?