# First REST API – Spring Boot Product Management

A fully functional REST API built with Spring Boot for managing products. Supports full CRUD operations with H2 in-memory database, Swagger UI, and proper exception handling.

---

## Tech Stack


| Tool | Purpose |
|---|---|
| Java 17 + Spring Boot 3.x | Core framework |
| Spring Web (MVC) | REST endpoints |
| Spring Data JPA + Hibernate | Database layer |
| H2 Database | In-memory DB (dev/test) |
| Springdoc OpenAPI 2.0.0 | Swagger UI docs |
| Maven | Build tool |

---

## Project Structure


```
product/
├── api/
│   ├── request/   → ProductRequest, UpdateProductRequest  (what comes IN)
│   ├── response/  → ProductResponse                       (what goes OUT)
│   └── ProductController.java                             (handles HTTP)
├── domain/        → Product.java                          (@Entity / DB table)
├── repository/    → ProductRepository.java                (DB operations)
├── service/       → ProductService.java                   (business logic)
└── support/       → ProductMapper.java                    (object mapping)
                   → exception/ProductNotFoundException
shared/api/response/ → ErrorMessageResponse.java           (error wrapper)
```

> **Flow:** `Client → Controller → Service → Repository → H2 Database`

---

## Setup & Run


```bash
# 1. Clone the repo
git clone https://github.com/YOUR_USERNAME/First-Rest-Api-Spring.git

# 2. Open in IntelliJ → right-click project → Maven → Reload Project

# 3. Run the app
./mvnw spring-boot:run
```

App runs at: **`http://localhost:8080`**

### application.properties

```properties

spring.h2.console.enabled=true
spring.h2.console.path=/console/
spring.datasource.url=jdbc:h2:mem:testdb
logging.level.org.hibernate.SQL=DEBUG
```

---

## API Endpoints

**Base URL:** `http://localhost:8080/api/v1/products`


| Method | URL | What it does | Status |
|---|---|---|---|
| `POST` | `/` | Create product | `201 Created` |
| `GET` | `/` | Get all products | `200 OK` |
| `GET` | `/{id}` | Get product by ID | `200 OK` |
| `PUT` | `/{id}` | Update product | `200 OK` |
| `DELETE` | `/{id}` | Delete product | `204 No Content` |

---

## Use Cases & Examples


### 1. Create a Product — `POST /api/v1/products`


**Request body:**

```json
{ "name": "First product" }
```
**Response `201 Created`:**
```json
{ "id": 1, "name": "First product" }
```
<img width="1411" height="891" alt="image" src="https://github.com/user-attachments/assets/b52faa86-2077-4aeb-8bfd-378a4d675807" />


---

### 2. Get Product by ID — `GET /api/v1/products/1`

No body needed. Just hit the URL.

**Response `200 OK`:**
```json
{ "id": 1, "name": "First product" }
```
---

### 3. Get All Products — `GET /api/v1/products`

**Response `200 OK`:**
```json
[
  { "id": 1, "name": "First product" },
  { "id": 2, "name": "Second product" }
]
```
> Returns empty array `[]` if no products exist — no error thrown.

---

### 4. Update a Product — `PUT /api/v1/products/1`

**Request body:**
```json
{ "name": "Updated name" }
```
**Response `200 OK`:**
```json
{ "id": 1, "name": "Updated name" }
```
---

### 5. Delete a Product — `DELETE /api/v1/products/1`

No body needed.

**Response `204 No Content`** — empty body, means success.

---

## Exception Handling

If a product ID doesn't exist, the API returns `404` with a clear message instead of a generic `500` error.

**Example — `GET /api/v1/products/999` (doesn't exist):**
```json
{
  "message": "Product with 999 not found"
}
```

**How it works:**
1. `ProductRepository.findById(id)` returns `Optional` (null-safe)
2. `.orElseThrow(ProductExceptionSupplier.productNotFound(id))` throws `ProductNotFoundException`
3. `@ControllerAdvice` in `ProductExceptionAdvisor` catches it and returns `404 Not Found`

---

## H2 Database Console

1. Run the app → open browser → go to `http://localhost:8080/console`
2. Change **JDBC URL** to: `jdbc:h2:mem:testdb`
3. User: `sa` | Password: *(empty)* → Click **Connect**

**Useful queries:**
```sql
SELECT * FROM PRODUCTS;          -- see all records
DELETE FROM PRODUCTS WHERE ID=1; -- manually delete
```

> After Hibernate starts, it auto-creates the `PRODUCTS` table with:
> `ID (BIGINT, PRIMARY KEY)` and `NAME (VARCHAR(255))`

<img width="515" height="493" alt="image" src="https://github.com/user-attachments/assets/f3ff9a01-168c-4ee0-8755-4dd3d48cc5fe" />

<img width="1082" height="636" alt="image" src="https://github.com/user-attachments/assets/e6536b2a-d9e0-47c9-b16c-2a3a69c1ef10" />

---

## Swagger UI

Interactive API docs — test all endpoints without Postman.

```
http://localhost:8080/swagger-ui/index.html   ← full UI
http://localhost:8080/v3/api-docs             ← raw JSON spec
```
<img width="1074" height="633" alt="image" src="https://github.com/user-attachments/assets/0baf8aa6-651e-4649-97fa-129c178c5848" />
<img width="1074" height="621" alt="image" src="https://github.com/user-attachments/assets/a7a7681c-b6b4-4265-b7b4-aca2d84685fd" />

---

**pom.xml dependency:**
```xml
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
    <version>2.6.0</version>
</dependency>

## Key Annotations Explained

 Annotation : What it does ?

 #`@RestController` : Marks HTTP handler class; returns JSON automatically .
# `@RequestMapping` : Sets base URL for the controller .
# `@PostMapping` , `@GetMapping` , `@PutMapping` , `@DeleteMapping` : Maps HTTP method to a Java method .
# `@RequestBody` : Converts incoming JSON → Java object .
# `@PathVariable` : Reads `{id}` from the URL .
# `@Service` : Business logic layer bean .
# `@Repository` : Data access layer bean .
# `@Component` : Generic Spring-managed bean (used in `ProductMapper`).
# `@Entity` : Maps Java class to a DB table.
# `@Id` + `@GeneratedValue` : Auto-generated primary key.
# `@ControllerAdvice`:Global exception handler for all controllers.



