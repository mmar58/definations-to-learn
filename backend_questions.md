The answer depends on whether the cookie has the **`HttpOnly`** flag enabled.

Here is how it works, how the browser automatically sends the cookie, and how frontend and backend handle requests seamlessly.

---

### 1. Can the Frontend Access It?

* **If `HttpOnly` is set (`Set-Cookie: token=xyz; HttpOnly`):**
* **No.** JavaScript running on the frontend (`document.cookie`, `fetch`, `axios`) **cannot read, edit, or copy** the token value.
* **Why this is good:** It completely protects your token against **Cross-Site Scripting (XSS)** attacks. Even if a malicious third-party script gets injected into your React/Svelte app, it cannot steal your JWT token.


* **If `HttpOnly` is NOT set:**
* **Yes.** You can read it via `document.cookie` in JavaScript, but doing this defeats the primary security benefit of using cookies over `localStorage`.



---

### 2. How the Frontend Sends Requests Without Accessing the Cookie

Even though JavaScript cannot read the `HttpOnly` cookie, **the Web Browser manages it automatically behind the scenes.**

When your frontend JavaScript triggers an HTTP request (via `fetch` or `axios`), the browser automatically attaches all non-expired, matching cookies to the HTTP `Cookie` request header.

#### How to configure your frontend:

#### A. Standard `fetch` API

By default, `fetch` includes cookies for same-origin requests. For cross-origin requests (e.g., frontend on `app.com` and backend on `api.com`), set `credentials: 'include'`:

```javascript
// The browser automatically attaches the HttpOnly cookie to this request header
const response = await fetch('https://api.yourdomain.com/user/profile', {
  method: 'GET',
  credentials: 'include' // <--- Tells browser to attach cookies & accept cookies
});

```

#### B. `Axios`

Set `withCredentials: true` globally or on individual requests:

```javascript
import axios from 'axios';

// Set globally
axios.defaults.withCredentials = true;

// Now all requests send and receive HttpOnly cookies automatically
const res = await axios.get('https://api.yourdomain.com/user/profile');

```

---

### 3. How the Full Flow Works (Step-by-Step)

```
[ Frontend / Browser ]                         [ Node.js Backend ]
        |                                              |
        | --- 1. POST /api/login (credentials) ------> |
        |                                              | (Authenticates user,
        |                                              |  generates JWT)
        | <--- 2. Res 200 + Set-Cookie Header -------- |
        |        (Cookie: token=abc; HttpOnly; Secure) |
        |                                              |
(Browser stores cookie)                                |
(JS CANNOT read 'abc')                                 |
        |                                              |
        | --- 3. GET /api/user/profile --------------> |
        |        (Browser AUTO-ATTACHES Cookie header) | (Backend reads req.cookies.token,
        |                                              |  verifies JWT, sends data)
        | <--- 4. Res 200 + User Data ---------------- |

```

1. **Login:** Frontend sends user/password.
2. **Setting the Cookie:** Backend validates credentials and sets the cookie in the response header:
```http
Set-Cookie: token=eyJhbG...; HttpOnly; Secure; SameSite=Strict; Path=/

```


3. **Automatic Storage:** The browser receives this header, stores the cookie in its secure storage, and marks it as hidden from JS.
4. **Subsequent Requests:** Every time the frontend calls `fetch('/api/...')`, the browser attaches the header automatically:
```http
Cookie: token=eyJhbG...

```


5. **Backend Verification:** In Express/Fastify, you parse `req.cookies.token` using a middleware like `cookie-parser`.

---

### 4. Required Backend Setup (Express.js Example)

On your Node.js backend, set up `cookie-parser` and configure CORS properly:

```javascript
import express from 'express';
import cookieParser from 'cookie-parser';
import cors from 'cors';

const app = express();

app.use(cookieParser());

// REQUIRED: Allow credentials from frontend origin
app.use(cors({
  origin: 'https://app.yourdomain.com', // Cannot be '*' when credentials are true
  credentials: true
}));

// Login route: Setting the HttpOnly Cookie
app.post('/api/login', (req, res) => {
  const token = generateJWT(req.body.userId);

  res.cookie('token', token, {
    httpOnly: true, // Prevents frontend JS from reading the token (XSS protection)
    secure: process.env.NODE_ENV === 'production', // Requires HTTPS in production
    sameSite: 'lax', // Protects against CSRF attacks
    maxAge: 7 * 24 * 60 * 60 * 1000 // 7 days
  });

  res.json({ message: 'Logged in successfully' });
});

// Protected route: Reading the cookie automatically
app.get('/api/user/profile', (req, res) => {
  const token = req.cookies.token; // Read automatically sent cookie
  if (!token) return res.status(401).json({ error: 'Unauthorized' });

  const user = verifyToken(token);
  res.json({ user });
});

```

---

### Summary Checklist for HttpOnly Cookies

| Feature | Behavior |
| --- | --- |
| **Can JS read `document.cookie`?** | ❌ No (Block XSS token theft) |
| **Does browser send token in requests?** | ✅ Yes, automatically |
| **Frontend requirement** | Set `credentials: 'include'` (`fetch`) or `withCredentials: true` (`axios`) |
| **Backend CORS requirement** | `origin` must be explicitly named (cannot be `*`), and `credentials: true` enabled |

The main difference is that a **B-Tree index** refers to the **underlying data structure** used to store the index, whereas a **Compound (or Composite) index** refers to the **number of columns/fields** included in the index.

They are not mutually exclusive—in fact, in relational databases like PostgreSQL and MySQL (InnoDB), **most compound indexes are implemented using B-Trees**.

---

### Key Comparisons

| Characteristic | B-Tree Index | Compound (Composite) Index |
| --- | --- | --- |
| **Category** | **Data Structure / Implementation Type** | **Schema Configuration Type** |
| **Definition** | A self-balancing search tree structure that keeps sorted data and allows searches, sequential access, insertions, and deletions in logarithmic time ($O(\log N)$). | An index created across **two or more columns** simultaneously in a single index structure (e.g., `(tenant_id, status, created_at)`). |
| **Column Count** | Can be **single-column** or **multi-column**. | Strictly **multi-column** ($\ge 2$ columns). |
| **Primary Purpose** | Provides fast range queries (`<`, `>`, `BETWEEN`), exact equality (`=`), and sorting (`ORDER BY`). | Optimizes multi-condition queries (`WHERE col1 = x AND col2 = y`) and multi-column sorting without reading multiple indexes. |
| **Crucial Rule** | Uses balance operations to keep search depth shallow ($O(\log N)$). | Governed by the **Leftmost Prefix Rule** (the order of columns in the index matters significantly). |

---

### 1. B-Tree Index (The Underlying Mechanism)

A **B-Tree** (Balanced Tree) is the default index type in databases like PostgreSQL, MySQL, SQLite, and Oracle.

#### How It Works:

Instead of scanning every row in a table ($O(N)$), the database organizes indexed values into a tree hierarchy. Each node in the tree contains sorted key values and pointers to child nodes or actual table rows (data pages).

```
                  [ 50 ]
                /        \
         [ 20 | 35 ]      [ 70 | 85 ]
        /     |    \     /     |    \
     (10)   (25)  (40) (60)   (80)  (90)

```

* **Best For:** Equality checks (`WHERE id = 5`), range queries (`WHERE age >= 25 AND age <= 40`), prefix matching (`LIKE 'John%'`), and sorted retrieves (`ORDER BY created_at`).
* **Can be Single or Multi-Column:** You can create a single-column B-Tree (`CREATE INDEX idx_user ON users(email);`) or a multi-column B-Tree (`CREATE INDEX idx_user_status ON users(tenant_id, status);`).

---

### 2. Compound Index (Multi-Column Layout)

A **Compound Index** (also called a **Composite Index**) is an index built on multiple columns in a specific order.

```sql
-- Creating a Compound B-Tree Index
CREATE INDEX idx_leads_company_status 
ON leads (company_id, status, created_at DESC);

```

#### How It Works:

The database sorts the entries first by the **first column** (`company_id`). If multiple rows share the same `company_id`, it sorts those by the **second column** (`status`). If those are identical, it sorts by the **third column** (`created_at`).

```
(company_id=10, status='NEW',       created_at=2026-10-01)
(company_id=10, status='QUALIFIED', created_at=2026-09-30)
(company_id=10, status='QUALIFIED', created_at=2026-09-15)
(company_id=12, status='NEW',       created_at=2026-10-02)

```

#### The Leftmost Prefix Rule

A compound index `(A, B, C)` can only be used by the query optimizer if your query filters by the **leftmost columns in sequence**:

* ✅ `WHERE A = 1` *(Uses index)*
* ✅ `WHERE A = 1 AND B = 2` *(Uses index)*
* ✅ `WHERE A = 1 AND B = 2 AND C = 3` *(Uses index)*
* ❌ `WHERE B = 2 AND C = 3` *(Cannot use index efficiently because `A` is missing)*
* ❌ `WHERE C = 3` *(Cannot use index)*

---

### Summary Analogy

Think of a **Telephone Directory (Yellow Pages)**:

* **B-Tree** is the *method* used to arrange the physical book into alphabetical sections and sub-sections for fast flipping.
* **Compound Index** is the *sorting order* chosen for each entry: **`(Last_Name, First_Name)`**.
* You can quickly look up `"Smith, John"` or all `"Smith"`s (Leftmost prefix).
* You cannot easily look up everyone named `"John"` without scanning the whole book because the list is sorted by `Last_Name` first.