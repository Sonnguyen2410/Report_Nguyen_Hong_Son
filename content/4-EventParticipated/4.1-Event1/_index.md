---
title: "Event 1"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---

# Summary Report: “Meetup 06/06/2026”

### Event Objectives

- Share best practices in modern application design and cloud infrastructure optimization.
- Introduce Domain-Driven Design (DDD) and Event-Driven Architecture.
- Provide guidance on selecting appropriate compute/containerization services tailored to project scale and requirements.
- Introduce automated security solutions, network monitoring, and AI tools supporting the Software Development Life Cycle (SDLC).

### Speakers

- **Huynh Bao** – Junior Cloud Native Developer at Endava Vietnam / Founder & Head Lab - ITea Lab
- **Le Hoang Gia Dai** – AWS Cloud Engineer / Cyber Security Engineer (AWS G3)
- **Nguyen Quoc Bao** – Cloud & Game Developer
- **Truong Huy Phuoc** – Speaker / Technical Contributor
- **Viet Phat** – AI Specialist / Researcher (Swinburne University of Technology)
- **Tran Trung Vinh** – System Administrator at Central Retail Group

### Key Highlights

#### Drawbacks of Legacy Application Architecture & Containerization Solutions

- **Limitations of Legacy VMs & Monoliths:** Long release cycles, high system resource consumption (CPU, RAM, Storage due to separate OS instances), and difficulties in scaling smaller services.
- **Application Packaging with Docker:** Optimizes resource usage, guarantees consistency between Dev and Production environments ("Build once, run anywhere"), and enhances CI/CD pipelines as well as Microservices architectures.

#### Applying Machine Learning to Cloud Security (AWS WAF + ML NIDS)

- **Limitations of Traditional WAF:** Relies strictly on fixed rule-based filtering, making it vulnerable to novel / zero-day attacks or complex IP spoofing techniques.
- **WAF + ML NIDS Solution:** Leverages the CSE-CIC-IDS2018 dataset and Machine Learning models (such as LightGBM) to analyze network traffic behavior, detect real-time anomalies, and integrate with AWS WAF/Security Hub for proactive threat prevention.

#### Real-time & Multiplayer Connections on AWS WebSockets

- **Connection Architecture Comparison:** Evaluates trade-offs between UDP/ENet (FPS/Racing Games), HTTP Polling (Stateless/Leaderboards), and WebSockets (Turn-based/Lobby/Chat).
- **Serverless Multiplayer Model:** Utilizes API Gateway WebSocket, AWS Lambda, and DynamoDB to manage connection states, matchmaking, and bi-directional message passing between client (Godot Engine) and server at optimized costs.

#### Modernizing Data Retrieval with GraphRAG & Amazon Neptune

- **Limitations of Traditional RAG:** Poor multi-hop reasoning performance and lack of context regarding relationships between entity instances.
- **Power of GraphRAG:** Combines Knowledge Graphs with Amazon Bedrock and Amazon Neptune/Neptune Analytics to trace entity relationships, delivering more precise and contextually rich answers for LLMs.

#### Teamwork Skills & IT Career Development Roadmap

- **The Art of Effective Teamwork:** 4 golden rules including Clear Goals, Right Person in the Right Place, Open Communication & Active Listening, and Personal Accountability.
- **Journey from Helpdesk to Senior Sysadmin/DevOps:** Self-driven learning mindset (Linux, Networking, IaC, Cloud Mindset), hands-on practice through lab exercises rather than purely pursuing paper certifications.

### Key Takeaways

#### Design & Infrastructure Mindset

- **Cloud-Native Mindset:** Transitioning from manual On-Premise infrastructure to auto-scaling cloud infrastructure managed as code (Infrastructure as Code - IaC) with automated CI/CD pipelines.
- **Proactive Security Approach:** Moving beyond static defensive rules to combine behavior-based AI/ML analysis to withstand zero-day threats.

#### Technical Architecture

- **Resource Optimization with Containers:** Gaining deep understanding of Docker image mechanisms, Dockerfile layers, and practical container application in production systems.
- **Real-Time Connection Handling:** Designing asynchronous event handling systems based on API Gateway WebSocket and Serverless Lambda, minimizing stale connection issues and optimizing DynamoDB query costs.
- **Applying GraphRAG:** Selecting between Fully Managed (Bedrock + Neptune Analytics) or Custom (LlamaIndex + Neptune) implementation paths based on enterprise data requirements.

#### Soft Skills & Career Orientation

- **Operational Mindset & Problem Solving:** "Never test in production" – strictly adhering to testing workflows and establishing monitoring systems prior to incidents occurring.
- **Effective Teamwork:** Utilizing digital tools (Google Workspace, Slack, Discord, Trello) to optimize team communication efficiency.

### Applying to Work

- **Applying Docker to Projects:** Packaging development environments with Docker/Dockerfile to synchronize working environments among team members.
- **Integrating WebSocket/Serverless:** Experimenting with real-time interactive features (such as chat or notifications) using AWS API Gateway WebSocket and AWS Lambda.
- **Upgrading RAG Solutions:** Researching the integration of Knowledge Graphs (GraphRAG) into document processing and internal knowledge lookup use cases to boost accuracy.
- **Improving Personal Development Workflow:** Building a structured learning roadmap focused on Linux, Networking, and AWS Services; conducting hands-on labs to solidify SysAdmin/DevOps skills.
- **Enhancing Team Effectiveness:** Establishing clear objectives, delegating tasks aligned with member strengths, and increasing direct communication within projects.

### Event Experience

Attending the **“Meetup 06/06/2026”** workshop on June 06, 2026 was an enriching experience that provided me with a comprehensive vision of modernizing applications, databases, and career growth in the technology industry. Notable highlights include:

#### Learning from High-Caliber Speakers
- Experienced industry speakers delivered engaging presentations combining theory with live demos, making complex AWS architectural patterns easy to comprehend.
- Received genuine, practical advice on personal IT career growth from a former IT Helpdesk professional who advanced to Senior SysAdmin.

#### Practical Technical Exposure
- Observed intuitive live demos of real-time matchmaking using WebSocket & Lambda.
- Understood the mechanics of building Knowledge Graphs with Amazon Neptune for complex GenAI applications.
- Learned how to train and evaluate Machine Learning models (LightGBM) on real-world network incident datasets (CSE-CIC-IDS2018).

#### Interaction & Networking
- The event fostered an open environment where attendees could freely ask questions and engage in direct technical discussions with speakers.
- Connected with peers and fellow engineers sharing passions for Cloud, DevOps, AI, and Game Development.

#### Key Lessons Learned
- Technology evolves rapidly; mastering Core Fundamentals in networking, operating systems, and architectural thinking is the key to adaptability.
- System modernization requires a phased approach, combining modern technology (Docker, Serverless, GenAI) with standardized, secure operational practices.

#### Some Event Photos

![Event 1 Photo 1.1](/images/event1.2.JPG)

![Event 1 Photo 1.2](/images/event1.1.png)

![Event 1 Photo 1.3](/images/event1.3.png)

> Overall, the event not only provided deep technical insights but also offered strong inspiration, helping me clearly shape my future professional path and working skills.
