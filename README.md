# Build a stateful microservice

## Prerequisite

[Install and start the MySQL database](https://dev.mysql.com/doc/mysql-installation-excerpt/8.0/en/)

## Build

```bash
cd sales_tax_rate
cargo build --target wasm32-wasi --release

cd order_management
cargo build --target wasm32-wasi --release
```

## Run

```bash
cd sales_tax_rate
wasmedge target/wasm32-wasi/release/sales_tax_rate_lookup.wasm

cd order_management
wasmedge --env "SALES_TAX_RATE_SERVICE=http://127.0.0.1:8001/find_rate" --env "DATABASE_URL=mysql://root:pass@127.0.0.1:3306/mysql" target/wasm32-wasi/release/order_management.wasm
```

## Test

Run the following from another terminal.

```bash
$ curl http://localhost:8003/init
{"status":"true"}

$ curl http://localhost:8003/create_order -X POST -d @order.json
{
  "order_id": 123,
  "product_id": 321,
  "quantity": 2,
  "subtotal": 20.0,
  "shipping_address": "123 Main St, Anytown USA",
  "shipping_zip": "78701",
  "total": 21.65
}

$ curl http://localhost:8003/orders
[{"order_id":123,"product_id":321,"quantity":2,"subtotal":20.0,"shipping_address":"123 Main St, Anytown USA","shipping_zip":"78701","total":21.65}]
```
