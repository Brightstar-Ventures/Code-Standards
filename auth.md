# Authentication and Authorization

This document is for Brightstar Developers integrating authentication and authorization in their feature work using **Supabase**.

## Table of Contents

- [Authorization](#authorization)
  - [Feature-specific rules](#1-feature-specific-rules)
  - [Administrative Permission Rules](#2-administrative-permission-rules)
- [Common Development Concerns](#common-development-concerns)
  - [Backend Routes Requiring Authentication](#backend-routes-requiring-authentication)
  - [Testing Protected Routes via Supabase](#testing-authenticated-routes-via-supabase)
  - [Frontend Features Requiring Authentication](#frontend-features-requiring-authentication)
  - [Frontend Features Requiring Authorization](#frontend-features-requiring-authorization)

---

## Authorization

Access control in our applications is enforced through **Supabase's Role-Based Access Control (RBAC) and Row-Level Security (RLS) policies**, ensuring secure and precise permission management aligned with organizational requirements.

## 1. Feature-specific Rules

When a feature involves user-specific access, **Row-Level Security (RLS) policies** define which users can perform actions on specific rows within a table.

Developers must create **RLS policies** in Supabase to enforce feature-specific access control. For example, to allow a user to update only their own workshop:

```sql
CREATE POLICY "Users can update their own workshops"
ON workshops
FOR UPDATE
USING (user_id = auth.uid());
```

## 2. Administrative Permission Rules

Administrative roles (e.g., `admin`, `moderator`) are assigned to users via Supabase Auth and Role-Based Access Control (RBAC). Permissions are granted via PostgreSQL roles and RLS policies.

### Example: Granting admin users access to modify all workshops
```sql
CREATE POLICY "Admins can update all workshops"
ON workshops
FOR UPDATE
USING (EXISTS (SELECT 1 FROM user_roles WHERE user_roles.user_id = auth.uid() AND user_roles.role = 'admin'));
```

### Checking Admin Permissions in Supabase Auth (Frontend)
```javascript
const { data: user } = await supabase.auth.getUser();
const { data: roles } = await supabase.from('user_roles').select('role').eq('user_id', user.id);

const isAdmin = roles.some(r => r.role === 'admin');
```

---

## Common Development Concerns

### Backend Routes Requiring Authentication

To require authentication for API routes, validate the **Supabase JWT**:

```javascript
import { createClient } from '@supabase/supabase-js';

const supabase = createClient(SUPABASE_URL, SUPABASE_ANON_KEY);

app.get('/protected-route', async (req, res) => {
    const { user } = await supabase.auth.getUser(req.headers.authorization);
    if (!user) {
        return res.status(401).json({ error: 'Unauthorized' });
    }
    res.json({ message: 'Authenticated request' });
});
```

### Testing Protected Routes via Supabase

To access protected API routes in **Supabase Studio**, manually set the **Bearer Token**:

1. **Log in** to the Supabase frontend.
2. Open **Developer Tools > Application > Local Storage**.
3. Copy the `sb-access-token` value.
4. In API requests (e.g., Postman), set the **Authorization header**:
   ```sh
   Authorization: Bearer <your-token>
   ```

---

## Frontend Features Requiring Authentication

To check if a user is signed in:

```javascript
const { data: user } = await supabase.auth.getUser();

if (user) {
  console.log('User is signed in', user);
} else {
  console.log('No user signed in');
}
```

For **real-time authentication state tracking**, subscribe to `onAuthStateChange`:

```javascript
supabase.auth.onAuthStateChange((event, session) => {
    console.log('Auth event:', event);
    console.log('User session:', session);
});
```

---

## Frontend Features Requiring Authorization

For **admin-only UI features**, check the user’s role:

```javascript
const { data: roles } = await supabase.from('user_roles').select('role').eq('user_id', user.id);

const isAdmin = roles.some(r => r.role === 'admin');

if (isAdmin) {
  console.log('User has admin access');
}
```

For a list of existing roles and permissions, refer to the **Roles & Permissions documentation**.

---

### **Testing Authenticated Routes via Supabase API**

To use authentication-protected routes via the **Supabase API** (e.g., `/rest/v1` or `/graphql/v1`), you need to authenticate yourself by adding your signed-in **Bearer Token**.

#### **Finding Your Supabase API Token**
1. **Log in** to your application via the front-end.
2. Open **Developer Tools** in your browser.
3. Navigate to **Application > Storage > Local Storage > your-app-domain**.
4. Copy the **full value** associated with the `supabase.auth.token` key.

#### **Using Your Token in API Requests**
To authenticate requests in **Postman or OpenAPI**, add the token to the **Authorization header**:

```sh
Authorization: Bearer <your-token>
```

In the **Supabase API documentation** or **Swagger UI**, look for the **"Authorize"** button and paste in your token.

---

### **Protecting Backend Service Methods**

Backend API routes must verify **Supabase authentication and authorization** to prevent unauthorized access. Supabase uses **Row-Level Security (RLS)** and **JWT validation** for this.

#### Example: Protecting API Endpoints in Supabase Edge Functions

```javascript
import { createClient } from '@supabase/supabase-js';

const supabase = createClient(SUPABASE_URL, SUPABASE_ANON_KEY);

export default async function handler(req, res) {
    const { user } = await supabase.auth.getUser(req.headers.authorization);

    if (!user) {
        return res.status(401).json({ error: 'Unauthorized' });
    }

    // Business logic for authorized users
    res.json({ message: 'Authenticated request' });
}
```

---

## Frontend Features Requiring a Registered User

To check whether a user is signed in on the frontend, use the Supabase Auth SDK:

```javascript
const { data: user } = await supabase.auth.getUser();

if (user) {
    console.log('User is signed in', user);
} else {
    console.log('No user signed in');
}
```

### **For real-time authentication state tracking, subscribe to onAuthStateChange**
```javascript
supabase.auth.onAuthStateChange((event, session) => {
    console.log('Auth event:', event);
    console.log('User session:', session);
});
```

---

## Frontend Features Requiring Authorization

For **feature-specific authorization**, Supabase’s **Row-Level Security (RLS) policies** define access at the **database level**.

### Example: Restricting Users to Editing Their Own Data

```sql
CREATE POLICY "Users can update their own profile"
ON profiles
FOR UPDATE
USING (id = auth.uid());
```

For **admin-only actions**, check user roles in the frontend:

```javascript
const { data: roles } = await supabase.from('user_roles').select('role').eq('user_id', user.id);

const isAdmin = roles.some(r => r.role === 'admin');

if (isAdmin) {
    console.log('User has admin access');
}
```
