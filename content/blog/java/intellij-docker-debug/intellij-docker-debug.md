---
title: "IntelliJ debugging in Docker container"
date: 2024-01-19T15:52:40+05:30
---
## Scenario
So here's the deal. You have a Java application working fine in your local environemnt but whenever it get launched in a container, it crashes. So now you want to debug the app running in the container.
 
## Requirements 

- IntelliJ IDEA 2023.3.2 (Community Edition)
- Docker 

Now in the IntelliJ run configurations add remote

![Image alt](/blog/java/intellij-docker-debug/intellij-settings-debug.png)

\
Let's run the app in question with following flags:

```bash
docker run --rm -it -p 5005:5005 -e JAVA_TOOL_OPTIONS="-agentlib:jdwp=transport=dt_socket,address=*:5005,server=y,suspend=n" my-image:0.0.1
```
\
That's it! Oh and don't forget to run IntelliJ debug with the above configuration