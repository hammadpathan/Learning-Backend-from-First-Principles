# 1. High Level Understanding

This is a general and simplified overview of what a backend is, how it essentially works, and why its used in comparison with the frontend

### What is a backend?

When you think of backend, the main keyword associated is “data”.

A backend is called a server because it serves something: content, data, files, JSON, and it also accepts/receives the same content.

Lets take a look at an example:

- backend-demo.sriniously.xyz/users

If you put this link into your browser and follow the devtools network you can view the request.

A get request is made which pulls data from the backend server.

“sriniously.xyz” is the domain

“backend-demo” is the subdomain

This is the overall flow:

Browser sends request 

→ DNS routes subdomain.domain to a public IP address 

→ the public ip address (hosted AWS EC2 instance) checks against its firewall

it needs to accept on Port 80 and 443 for HTTP/HTTPS connections

→ from the firewall its sent to the computer running EC2 instance where a reverse proxy routes again within the computer

This is something you put in front of servers to manage redirects/configs from a centralized place rather than changing every server.

this NGINX would forward the incoming request to the local hosted server

**This part you need to have an understanding of [localhost](http://localhost) and how deployed computing instances / hosting works**

→ once the request reaches the local host (hosted Node.js), it is processed accordingly based on url and the correct data is returned/accepted

**so overall:**

**Browser → DNS → AWS → Firewall → Instance → NGINX → localhost**

### Why do we need a backend though ?

Think about applications you interact with. Lets take instagram: you like someones post, the like count goes up, and the poster gets a notification that you liked their post.

Keep that in mind for now, lets think about your view of the app. You follow XYZ people and will only see posts from XYZ. Another person follows ABC and will only see posts from ABC. 

Notice how you need to see different things because of your specific followings. This wouldn’t be possible / very hard without a backend because the data would be the same for everyone on the frontend.

The backend handles core logic, where the frontends focus is to display based on backend logic.

You could say just connect the frontend to the database and load accordingly but the processing time would be too long. When you load the front end from your browser it follows pretty much the same flow as above but it fetches files, which are then executed by your browser that paints the visual.

Executing from your browser means your browser is running the code and using your computers processing power. To make core logic and database decisions would have serious impact on your browsers performance and also serious security flaws.

Browser runtimes are sandboxed environments meaning they are only allowed to execute code within that environment and not allowed access to anything else. 

This makes sense for security reasons. You don’t want a website accessing your local files, or calling external apis without permission.

**Top reasons we can’t write backend logic in frontend:**

- security - file access
- CORS - don’t want website accessing potentially harmful external API’s
- database - takes in many connections. connecting and disconnected thousands of times would impact performance rather than having one constant open connection via backend
- computing power - even mobiles accesses frontend. so we need to watch and minimize the load on the computer executing the frontend code so that its not laggy

### Benefits of learning backend engineering from first principles

The ability to built backend systems by breaking it down into universal components gives you a massive edge. You can switch from language to language and still understand the core system design.

1. seeing the big picture (especially in new languages) (seeing the pattern)
2. faster onboarding
3. start new projects way faster (know how to structure design without referencing docs)
4. reduce syntax fatigue (use core principles across languages and only need to translate language rather than understanding logic)
5. choosing the right tool for the right job - understanding the problems let you the exact tool you need
6. more employable - get more JOBS (think independently)