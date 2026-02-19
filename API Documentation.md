

## **1. Original API Endpoint Code**

```javascript
const productRouter = express.Router();

// Get all products with filtering and pagination
productRouter.get('/', async (req, res) => { /* ...implementation... */ });

// Get product by ID
productRouter.get('/:productId', async (req, res) => { /* ...implementation... */ });

module.exports = productRouter;
```

This is the code we’ll document.

---

## **2. Comprehensive Endpoint Documentation (Prompt 1)**

### **Product API Endpoints**

#### **Endpoint 1: Get All Products**

* **Method:** `GET`
* **Path:** `/products`
* **Description:** Retrieves a list of products. Supports filtering by category, price range, stock status, sorting, and pagination.

**Request Parameters (Query):**

| Parameter  | Type    | Description                                      | Required |
| ---------- | ------- | ------------------------------------------------ | -------- |
| `category` | string  | Filter products by category                      | No       |
| `minPrice` | number  | Minimum price for filtering                      | No       |
| `maxPrice` | number  | Maximum price for filtering                      | No       |
| `sort`     | string  | Field to sort by (default: `createdAt`)          | No       |
| `order`    | string  | Sorting order: `asc` or `desc` (default: `desc`) | No       |
| `page`     | number  | Page number for pagination (default: 1)          | No       |
| `limit`    | number  | Number of products per page (default: 20)        | No       |
| `inStock`  | boolean | Filter only products in stock (`true`)           | No       |

**Response:**

* **200 OK**

```json
{
  "products": [
    {
      "_id": "123",
      "name": "Product A",
      "category": "Electronics",
      "price": 99.99,
      "stockQuantity": 10,
      "createdAt": "2026-02-18T00:00:00.000Z"
    }
  ],
  "pagination": {
    "total": 100,
    "page": 1,
    "limit": 20,
    "pages": 5
  }
}
```

**Errors:**

* **500 Internal Server Error**: `{"error": "Server error", "message": "Failed to fetch products"}`

**Example Requests:**

```http
GET /products?category=Electronics&minPrice=50&maxPrice=200&sort=price&order=asc&page=2&limit=10
```

```http
GET /products?inStock=true
```

---

#### **Endpoint 2: Get Product by ID**

* **Method:** `GET`
* **Path:** `/products/:productId`
* **Description:** Retrieves a single product by its unique ID.

**Request Parameters:**

| Parameter   | Type   | Description                      | Required |
| ----------- | ------ | -------------------------------- | -------- |
| `productId` | string | Unique identifier of the product | Yes      |

**Response:**

* **200 OK**

```json
{
  "_id": "123",
  "name": "Product A",
  "category": "Electronics",
  "price": 99.99,
  "stockQuantity": 10,
  "createdAt": "2026-02-18T00:00:00.000Z"
}
```

**Errors:**

* **404 Not Found**: `{"error": "Not found", "message": "Product not found"}`
* **400 Bad Request**: `{"error": "Invalid ID", "message": "Invalid product ID format"}`
* **500 Internal Server Error**: `{"error": "Server error", "message": "Failed to fetch product"}`

**Example Requests:**

```http
GET /products/63f8c9b5e1234abcd5678ef
```

```http
GET /products/invalid-id
```

**Authentication Requirements:**

* No authentication required for these endpoints (can be adjusted if needed).

**Rate Limiting / Special Considerations:**

* Consider adding pagination limits for performance.
* Filtering and sorting are optional but recommended for large datasets.

---

## **3. Converted Documentation Format (Prompt 2 → OpenAPI)**

```yaml
openapi: 3.0.3
info:
  title: Product API
  version: 1.0.0
  description: API for retrieving products with filtering, sorting, and pagination

paths:
  /products:
    get:
      summary: Get all products
      description: Retrieves a list of products with optional filtering, sorting, and pagination.
      parameters:
        - name: category
          in: query
          schema:
            type: string
        - name: minPrice
          in: query
          schema:
            type: number
        - name: maxPrice
          in: query
          schema:
            type: number
        - name: sort
          in: query
          schema:
            type: string
            default: createdAt
        - name: order
          in: query
          schema:
            type: string
            enum: [asc, desc]
            default: desc
        - name: page
          in: query
          schema:
            type: integer
            default: 1
        - name: limit
          in: query
          schema:
            type: integer
            default: 20
        - name: inStock
          in: query
          schema:
            type: boolean
      responses:
        '200':
          description: Successful response
          content:
            application/json:
              schema:
                type: object
                properties:
                  products:
                    type: array
                    items:
                      $ref: '#/components/schemas/Product'
                  pagination:
                    type: object
                    properties:
                      total:
                        type: integer
                      page:
                        type: integer
                      limit:
                        type: integer
                      pages:
                        type: integer
        '500':
          description: Server error
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'

  /products/{productId}:
    get:
      summary: Get product by ID
      parameters:
        - name: productId
          in: path
          required: true
          schema:
            type: string
      responses:
        '200':
          description: Product found
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Product'
        '400':
          description: Invalid product ID
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'
        '404':
          description: Product not found
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'
        '500':
          description: Server error
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'

components:
  schemas:
    Product:
      type: object
      properties:
        _id:
          type: string
        name:
          type: string
        category:
          type: string
        price:
          type: number
        stockQuantity:
          type: integer
        createdAt:
          type: string
          format: date-time
    Error:
      type: object
      properties:
        error:
          type: string
        message:
          type: string
```

---

## **4. Developer-Friendly Usage Guide (Prompt 3)**

### **Product API Usage Guide**

**Audience:** Beginner to intermediate JavaScript developers

#### **1. Authentication**

* No API key or token required.
* If secured, add `Authorization: Bearer <token>` in request headers.

#### **2. Making Requests**

**Get all products:**

```javascript
import axios from 'axios';

async function fetchProducts() {
  try {
    const response = await axios.get('https://api.example.com/products', {
      params: {
        category: 'Electronics',
        minPrice: 50,
        maxPrice: 200,
        sort: 'price',
        order: 'asc',
        page: 1,
        limit: 10,
        inStock: true
      }
    });
    console.log(response.data);
  } catch (error) {
    console.error(error.response?.data || error.message);
  }
}

fetchProducts();
```

**Get product by ID:**

```javascript
async function fetchProductById(id) {
  try {
    const response = await axios.get(`https://api.example.com/products/${id}`);
    console.log(response.data);
  } catch (error) {
    console.error(error.response?.data || error.message);
  }
}

fetchProductById('63f8c9b5e1234abcd5678ef');
```

#### **3. Handling Responses**

* **200 OK:** Product(s) returned. Check `response.data.products` for lists or the object for single product.
* **400 Bad Request:** Invalid product ID format.
* **404 Not Found:** Product does not exist.
* **500 Internal Server Error:** Something went wrong on the server. Retry or log error.

#### **4. Common Errors**

* Incorrect query parameters (e.g., non-numeric `minPrice`) → 400 Bad Request
* Invalid product ID → 400
* Non-existent product ID → 404

#### **5. Best Practices**

* Use pagination (`page` and `limit`) for large datasets.
* Filter results to improve performance.
* Handle errors gracefully in your client code.

---

## **5. Key Takeaways**

**Challenging Parts:**

* Explaining filtering, pagination, and optional query parameters clearly.
* Mapping JS/Mongoose error handling to proper HTTP status codes.

**Prompt Adjustments for Better Results:**

* Adding explicit request/response examples.
* Including default values for query params.
* Asking for OpenAPI conversion to get machine-readable documentation.

**Most Effective Format:**

* **OpenAPI** is best for integration with tools like Postman, Swagger UI, and automated client generation. Markdown is good for simple human-readable guides.

**Incorporation into Workflow:**

1. Write endpoint code → generate documentation via Prompt 1.
2. Convert to OpenAPI → integrate with API tooling.
3. Create developer guide → share with team for onboarding.
4. Keep documentation up to date with code changes.


