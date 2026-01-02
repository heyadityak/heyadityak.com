---
title: "Creating Diagrams with Mermaid"
date: 2024-03-15
description: "Learn how to create beautiful diagrams in your blog posts using Mermaid.js"
tags: ["Mermaid", "Diagrams", "Tutorial", "Documentation"]
---

Mermaid is a powerful tool for creating diagrams and visualizations directly in Markdown. This post demonstrates the various diagram types supported.

## Flowchart

Flowcharts are great for showing processes and decision trees:

```mermaid
graph TD
    A[Start] --> B{Is it working?}
    B -->|Yes| C[Great!]
    B -->|No| D[Debug]
    D --> E[Check logs]
    E --> F[Fix the issue]
    F --> B
    C --> G[Deploy]
```

## Sequence Diagram

Perfect for showing interactions between systems or components:

```mermaid
sequenceDiagram
    participant User
    participant Frontend
    participant API
    participant Database
    
    User->>Frontend: Click submit
    Frontend->>API: POST /data
    API->>Database: INSERT query
    Database-->>API: Success
    API-->>Frontend: 200 OK
    Frontend-->>User: Show success message
```

## Class Diagram

Useful for documenting object-oriented designs:

```mermaid
classDiagram
    class User {
        +String name
        +String email
        +login()
        +logout()
    }
    class Post {
        +String title
        +String content
        +Date createdAt
        +publish()
    }
    class Comment {
        +String text
        +Date createdAt
    }
    User "1" --> "*" Post : writes
    Post "1" --> "*" Comment : has
    User "1" --> "*" Comment : writes
```

## State Diagram

Great for showing state machines and transitions:

```mermaid
stateDiagram-v2
    [*] --> Draft
    Draft --> Review : Submit
    Review --> Draft : Request changes
    Review --> Published : Approve
    Published --> Archived : Archive
    Archived --> [*]
```

## Entity Relationship Diagram

Perfect for database schema documentation:

```mermaid
erDiagram
    USER ||--o{ POST : writes
    USER ||--o{ COMMENT : writes
    POST ||--o{ COMMENT : has
    POST ||--o{ TAG : has
    
    USER {
        int id PK
        string name
        string email
    }
    POST {
        int id PK
        string title
        text content
        int user_id FK
    }
```

## Gantt Chart

Ideal for project timelines:

```mermaid
gantt
    title Project Timeline
    dateFormat  YYYY-MM-DD
    section Planning
    Requirements    :a1, 2024-01-01, 7d
    Design          :a2, after a1, 14d
    section Development
    Backend         :b1, after a2, 21d
    Frontend        :b2, after a2, 21d
    section Testing
    QA Testing      :c1, after b1, 14d
    section Deployment
    Launch          :d1, after c1, 3d
```

## Pie Chart

Simple data visualization:

```mermaid
pie title Technology Stack
    "JavaScript" : 40
    "Go" : 25
    "Python" : 20
    "Other" : 15
```

## Git Graph

Visualize Git branching strategies:

```mermaid
gitGraph
    commit
    commit
    branch feature
    checkout feature
    commit
    commit
    checkout main
    merge feature
    commit
    branch hotfix
    checkout hotfix
    commit
    checkout main
    merge hotfix
```

## Conclusion

Mermaid makes it easy to add professional diagrams to your documentation and blog posts. The diagrams are rendered client-side and automatically adapt to your site's dark/light theme!

To use Mermaid in your posts, simply wrap your diagram code in a mermaid code block:

````markdown
```mermaid
graph TD
    A --> B
```
````
