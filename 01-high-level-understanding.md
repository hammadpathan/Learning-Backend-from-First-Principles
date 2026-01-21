# High-Level Understanding

## What is a Backend?

When thinking about backends, the main keyword is **data**. A backend is a server because it serves something: content, data, files, JSON—and it also accepts/receives the same content.

### Example Request Flow

Consider the URL `backend-demo.sriniously.xyz/users`:

```
Browser → DNS → AWS → Firewall → Instance → NGINX → localhost
```

**Detailed breakdown:**

1. **Browser sends request** to the domain
2. **DNS routes** the subdomain (`backend-demo`) and domain (`sriniously.xyz`) to a public IP address
3. **AWS/Firewall checks** if the instance accepts connections on Port 80 (HTTP) and 443 (HTTPS)
4. **Reverse Proxy (NGINX)** routes the request from the firewall to the local server within the instance
   - A reverse proxy sits in front of servers to manage redirects and configs from a centralized place
5. **Local server** (e.g., Node.js on localhost) processes the request based on the URL and returns the appropriate data

> **Key prerequisite:** You need to understand localhost and how deployed computing instances/hosting works

## Why Do We Need a Backend?

Consider Instagram: when you like a post, the like count goes up and the poster gets notified. Here's why we can't do this without a backend:

**Personalization & Data Isolation:**
- You follow person XYZ and see only their posts
- Another user follows ABC and sees only their posts
- Everyone needs to see different content based on their relationships—this requires **backend logic**

**Problems with Frontend-Only Logic:**

1. **Processing power** – Running complex logic on the browser uses the device's resources, making it laggy. Especially problematic for mobile devices.
2. **Security** – Browser runtimes are sandboxed. They cannot access local files or make arbitrary external API calls without permission.
3. **CORS** – We don't want websites accessing potentially harmful external APIs on behalf of users.
4. **Database connections** – Databases can only handle a limited number of connections. Connecting and disconnecting thousands of times would tank performance. A backend maintains constant connections efficiently.

**Bottom line:** The backend handles core logic and business decisions. The frontend displays data based on backend logic.

## Benefits of Learning Backend Engineering from First Principles

Understanding universal backend components gives you a massive advantage:

1. **See the big picture** – Understand patterns that work across languages and frameworks
2. **Faster onboarding** – Jump into new projects and languages quickly
3. **Faster project startup** – Structure and design without constantly referencing docs
4. **Reduced syntax fatigue** – Apply core principles across languages; only translate syntax
5. **Better tool selection** – Understand problems deeply enough to choose the right tool for the job
6. **More employable** – Think independently and solve problems systematically
