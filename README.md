# Pratica-gRPC_microservices-proto

Definições `.proto` e stubs gerados em Go para os microsserviços **Order** e **Payment**.

## Estrutura

```
Pratica-gRPC_microservices-proto/
├── order/
│   └── order.proto
├── payment/
│   └── payment.proto
└── golang/
    ├── order/    # .pb.go gerados
    └── payment/
```

## Requisitos

- **protoc**
- Plugins Go:
  ```bash
  go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
  go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest
  ```

## go_package (ajuste para o seu namespace)

```proto
// order/order.proto
option go_package = "github.com/Jogos101/Pratica-gRPC_microservices-proto/golang/order;order";

// payment/payment.proto
option go_package = "github.com/Jogos101/Pratica-gRPC_microservices-proto/golang/payment;payment";
```

## Geração dos stubs

Dentro de `Pratica-gRPC_microservices-proto`:

```bash
# (opcional) normalizar CRLF -> LF caso edite no Windows
find . -name "*.proto" -type f -exec dos2unix {} + 2>/dev/null || true

# ORDER
protoc -I .   --go_out=./golang --go_opt=paths=source_relative   --go-grpc_out=./golang --go-grpc_opt=paths=source_relative   order/order.proto

# PAYMENT
protoc -I .   --go_out=./golang --go_opt=paths=source_relative   --go-grpc_out=./golang --go-grpc_opt=paths=source_relative   payment/payment.proto
```

## Uso nos serviços

Nos `go.mod` de `order` e `payment` (no repo de serviços):

```go
require (
  github.com/Jogos101/Pratica-gRPC_microservices-proto/golang/order v0.0.0-00010101000000-000000000000
  github.com/Jogos101/Pratica-gRPC_microservices-proto/golang/payment v0.0.0-00010101000000-000000000000
)

replace github.com/Jogos101/Pratica-gRPC_microservices-proto/golang/order => ../../Pratica-gRPC_microservices-proto/golang/order
replace github.com/Jogos101/Pratica-gRPC_microservices-proto/golang/payment => ../../Pratica-gRPC_microservices-proto/golang/payment
```

## Teste rápido (após subir os serviços)

```bash
grpcurl -plaintext -d '{
  "customer_id": 123,
  "order_items": [{"product_code":"P1","quantity":2,"unit_price":10.5}],
  "total_price": 21.0
}' localhost:3000 Order/Create
```
