# Quickstart for TPC-H Benchmark Sample Data

> TPC-H is a decision support benchmark. It consists of a suite of business-oriented ad hoc queries and concurrent data modifications. The queries and the data populating the database have been chosen to have broad industry-wide relevance. This benchmark illustrates decision support systems that examine large volumes of data, execute queries with a high degree of complexity, and give answers to critical business questions.
>
> - [TPC Benchmark™ H (TPC-H)](https://www.tpc.org/tpch/)

**Step 1.** Initialize and start Spice

```bash
spice init tpch-quickstart
```

```bash
cd tpch-quickstart
spice run
```

**Step 2.** Connect the TPC-H Benchmark Sample Data

Add `datasets` configuration below to `spicepod.yaml`

```yaml
datasets:
  - from: s3://spiceai-demo-datasets/tpch/customer/
    name: customer

  - from: s3://spiceai-demo-datasets/tpch/lineitem/
    name: lineitem

  - from: s3://spiceai-demo-datasets/tpch/nation/
    name: nation

  - from: s3://spiceai-demo-datasets/tpch/orders/
    name: orders

  - from: s3://spiceai-demo-datasets/tpch/part/
    name: part

  - from: s3://spiceai-demo-datasets/tpch/partsupp/
    name: partsupp

  - from: s3://spiceai-demo-datasets/tpch/region/
    name: region
  
  - from: s3://spiceai-demo-datasets/tpch/supplier/
    name: supplier
```
The following output is shown in the Spice runtime terminal:

```bash
2024-03-31T17:47:09.589058Z  INFO runtime: Loaded dataset: region
2024-03-31T17:47:09.589846Z  INFO runtime: Loaded dataset: nation
2024-03-31T17:47:09.606925Z  INFO runtime: Loaded dataset: supplier
2024-03-31T17:47:09.662131Z  INFO runtime: Loaded dataset: partsupp
2024-03-31T17:47:09.673164Z  INFO runtime: Loaded dataset: orders
2024-03-31T17:47:09.678748Z  INFO runtime: Loaded dataset: lineitem
2024-03-31T17:47:09.717499Z  INFO runtime: Loaded dataset: customer
2024-03-31T17:47:09.757605Z  INFO runtime: Loaded dataset: part
```

**Step 3.** Run queries against the dataset using the Spice SQL REPL.

In a new terminal, start the Spice SQL REPL.

```bash
spice sql
```

Check that TPC-H tables exist:

```sql
show tables;

+---------------+--------------------+-------------+------------+
| table_catalog | table_schema       | table_name  | table_type |
+---------------+--------------------+-------------+------------+
| datafusion    | public             | lineitem    | VIEW       |
| datafusion    | public             | part        | VIEW       |
| datafusion    | public             | region      | VIEW       |
| datafusion    | public             | partsupp    | VIEW       |
| datafusion    | public             | orders      | VIEW       |
| datafusion    | public             | nation      | VIEW       |
| datafusion    | public             | customer    | VIEW       |
| datafusion    | public             | supplier    | VIEW       |
| datafusion    | information_schema | tables      | VIEW       |
| datafusion    | information_schema | views       | VIEW       |
| datafusion    | information_schema | columns     | VIEW       |
| datafusion    | information_schema | df_settings | VIEW       |
+---------------+--------------------+-------------+------------+
```

Run *Pricing Summary Report Query (Q1)*. More information about TPC-H and all the queries involved can be found in the official [TPC Benchmark H Standard Specification](https://www.tpc.org/tpc_documents_current_versions/pdf/tpc-h_v2.17.1.pdf).

```sql
select
	l_returnflag,
	l_linestatus,
	sum(l_quantity) as sum_qty,
	sum(l_extendedprice) as sum_base_price,
	sum(l_extendedprice * (1 - l_discount)) as sum_disc_price,
	sum(l_extendedprice * (1 - l_discount) * (1 + l_tax)) as sum_charge,
	avg(l_quantity) as avg_qty,
	avg(l_extendedprice) as avg_price,
	avg(l_discount) as avg_disc,
	count(*) as count_order
from
	lineitem
where
	l_shipdate <= date '1998-12-01' - interval '110' day
group by
	l_returnflag,
	l_linestatus
order by
	l_returnflag,
	l_linestatus
;

+--------------+--------------+-------------+-----------------+-------------------+---------------------+-----------+--------------+----------+-------------+
| l_returnflag | l_linestatus | sum_qty     | sum_base_price  | sum_disc_price    | sum_charge          | avg_qty   | avg_price    | avg_disc | count_order |
+--------------+--------------+-------------+-----------------+-------------------+---------------------+-----------+--------------+----------+-------------+
| A            | F            | 37734107.00 | 56586554400.73  | 53758257134.8700  | 55909065222.827692  | 25.522005 | 38273.129734 | 0.049985 | 1478493     |
| N            | F            | 991417.00   | 1487504710.38   | 1413082168.0541   | 1469649223.194375   | 25.516471 | 38284.467760 | 0.050093 | 38854       |
| N            | O            | 73416597.00 | 110112303006.41 | 104608220776.3836 | 108796375788.183317 | 25.502437 | 38249.282778 | 0.049996 | 2878807     |
| R            | F            | 37719753.00 | 56568041380.90  | 53741292684.6040  | 55889619119.831932  | 25.505793 | 38250.854626 | 0.050009 | 1478870     |
+--------------+--------------+-------------+-----------------+-------------------+---------------------+-----------+--------------+----------+-------------+

Query took: 4.178523666 seconds. 4/4 rows displayed.
```

**Step4 (Optional)** Enable [Data Acceleration](https://docs.spiceai.org/data-accelerators) for TPC-H Benchmark Sample Data

Replace `datasets` configuration in `spicepod.yaml` with

```yaml
datasets:
  - from: s3://spiceai-demo-datasets/tpch/customer/
    name: customer
    acceleration:
      enabled: true

  - from: s3://spiceai-demo-datasets/tpch/lineitem/
    name: lineitem
    acceleration:
      enabled: true

  - from: s3://spiceai-demo-datasets/tpch/nation/
    name: nation
    acceleration:
      enabled: true

  - from: s3://spiceai-demo-datasets/tpch/orders/
    name: orders
    acceleration:
      enabled: true

  - from: s3://spiceai-demo-datasets/tpch/part/
    name: part
    acceleration:
      enabled: true

  - from: s3://spiceai-demo-datasets/tpch/partsupp/
    name: partsupp
    acceleration:
      enabled: true

  - from: s3://spiceai-demo-datasets/tpch/region/
    name: region
    acceleration:
      enabled: true
  
  - from: s3://spiceai-demo-datasets/tpch/supplier/
    name: supplier
    acceleration:
      enabled: true
```

The following output is shown in the Spice runtime terminal confirming new configuration is applied.
```bash
2024-03-31T18:01:06.802013Z  INFO runtime: Unloaded dataset: customer
2024-03-31T18:01:06.802073Z  INFO runtime: Unloaded dataset: lineitem
2024-03-31T18:01:06.802641Z  INFO runtime: Unloaded dataset: nation
2024-03-31T18:01:06.803116Z  INFO runtime: Loaded dataset: customer
2024-03-31T18:01:06.803134Z  INFO runtime: Unloaded dataset: orders
2024-03-31T18:01:06.803160Z  INFO runtime: Loaded dataset: lineitem
2024-03-31T18:01:06.803203Z  INFO runtime: Unloaded dataset: part
2024-03-31T18:01:06.803276Z  INFO runtime: Loaded dataset: nation
2024-03-31T18:01:06.803333Z  INFO runtime: Unloaded dataset: partsupp
2024-03-31T18:01:06.803359Z  INFO runtime: Loaded dataset: orders
2024-03-31T18:01:06.803488Z  INFO runtime: Unloaded dataset: region
2024-03-31T18:01:06.804223Z  INFO runtime: Loaded dataset: part
2024-03-31T18:01:06.804736Z  INFO runtime: Unloaded dataset: supplier
2024-03-31T18:01:06.804974Z  INFO runtime: Loaded dataset: partsupp
2024-03-31T18:01:06.805004Z  INFO runtime: Loaded dataset: region
2024-03-31T18:01:06.805023Z  INFO runtime: Loaded dataset: supplier
```

Run *Pricing Summary Report Query* using the Spice SQL REPL. 

```sql
select
	l_returnflag,
	l_linestatus,
	sum(l_quantity) as sum_qty,
	sum(l_extendedprice) as sum_base_price,
	sum(l_extendedprice * (1 - l_discount)) as sum_disc_price,
	sum(l_extendedprice * (1 - l_discount) * (1 + l_tax)) as sum_charge,
	avg(l_quantity) as avg_qty,
	avg(l_extendedprice) as avg_price,
	avg(l_discount) as avg_disc,
	count(*) as count_order
from
	lineitem
where
	l_shipdate <= date '1998-12-01' - interval '110' day
group by
	l_returnflag,
	l_linestatus
order by
	l_returnflag,
	l_linestatus
;
+--------------+--------------+-------------+-----------------+-------------------+---------------------+-----------+--------------+----------+-------------+
| l_returnflag | l_linestatus | sum_qty     | sum_base_price  | sum_disc_price    | sum_charge          | avg_qty   | avg_price    | avg_disc | count_order |
+--------------+--------------+-------------+-----------------+-------------------+---------------------+-----------+--------------+----------+-------------+
| A            | F            | 37734107.00 | 56586554400.73  | 53758257134.8700  | 55909065222.827692  | 25.522005 | 38273.129734 | 0.049985 | 1478493     |
| N            | F            | 991417.00   | 1487504710.38   | 1413082168.0541   | 1469649223.194375   | 25.516471 | 38284.467760 | 0.050093 | 38854       |
| N            | O            | 73416597.00 | 110112303006.41 | 104608220776.3836 | 108796375788.183317 | 25.502437 | 38249.282778 | 0.049996 | 2878807     |
| R            | F            | 37719753.00 | 56568041380.90  | 53741292684.6040  | 55889619119.831932  | 25.505793 | 38250.854626 | 0.050009 | 1478870     |
+--------------+--------------+-------------+-----------------+-------------------+---------------------+-----------+--------------+----------+-------------+

Query took: 0.108190459 seconds. 4/4 rows displayed.
```

Observe query execution time decreased from **4.178523666** to **0.108190459** seconds.