---
title: "Event 3"
date: 2026-07-25
weight: 3
chapter: false
pre: " <b> 4.3. </b> "
---

# Summary Report: “FCAJ - Agentic AI Build Week”

### Event Objectives

- Summarize the journey of the Agentic AI Build Week (AABW) Hackathon and share practical experience in building AI Agent applications.
- Introduce advanced Agentic AI architectures integrating AWS Bedrock, AgentCore, SageMaker, and modern LLM tools.
- Learn multi-channel AI Agent design methods and practical business problem-solving models.
- Draw lessons on time management, teamwork under heavy pressure, and Cloud/AI infrastructure cost optimization.

### Speakers / Sharing Teams

- **Team SignalScout** (Le Tan Luc, Do Hoang Hieu, Trieu Quoc Hao, Nguyen Van Duy Khiem, Nguyen Cong Minh, Nguyen Tran Minh Quan)
- **Team Plan V** (Pham Tien Thuan Phat, Huynh Hoang Long, Le Minh Nghia, Tran Dai Vi, Nguyen An) - Solution Architect Professional AI Native App Solution
- **Team 3KA** (Huynh An Khuong, Nguyen Quoc Huy, Ngo Quang Khoi, Hoang Le Thanh Duc, Dang Nguyen Phuoc Loc, Dang Truong Hung) - S.H.E.P.H.E.R.D. Project
- **Team OneTeam** (Anh Duy, Tran Dong, Doan Trung, Minh Viet, Anshul Roy) - KFC Bot Agent Solution

### Key Highlights

#### SignalScout: Strategic Enterprise Signal Detection System

- **Challenges:** Multi-service management, observability of distributed services, infrastructure costs, and early detection of corporate restructuring signals.
- **Solution & Architecture:** Integrated AWS Bedrock, AgentCore Memory, LangFuse, WAF, DynamoDB, and Amplify Hosting. Connected scattered signals into clear stories with specific evidence to support decision-making.
- **Cost Optimization:** Analyzed detailed cost problems from $17 - $130 (AWS) and provided optimized architecture models for operational budgets.

#### Plan V: AI Native App for Solution Architects

- **Real-world Problem:** Clients requested urgent AI system designs; Solution Architects had to manually extract requirements, draw diagrams, and estimate Cloud costs, taking significant time.
- **Plan V Solution:** Built an AI Native app that automatically analyzes natural language requirements (BRD/PRD), creates standard Draw.io / AWS Architecture diagrams, estimates AWS costs for ap-southeast-1 region, and generates IaC code.

#### S.H.E.P.H.E.R.D. (Team 3KA): Real-time Crowd Monitoring, Risk Detection & Dispatching

- **Solution:** Built a system to analyze user flows, crowd density, and congestion forecasting from camera live streams.
- **Technologies Used:** Combined Computer Vision (YOLO + ByteTrack), Amazon SageMaker, Amazon Bedrock AgentCore/Strands, and React Dashboard.
- **Agentic Layer:** Includes Autonomous Monitor (auto-alert on congestion) and Operator Copilot (assists operators querying in natural language).

#### KFC Bot Agent (OneTeam): Automated Multi-channel Ordering via AI

- **Design Philosophy:** "Design Once | Deploy Everywhere" - Built a single Agent platform capable of flexible integration with multiple messaging channels (Zalo, WhatsApp, Telegram, Instagram) via Channel Adapters.
- **Outstanding Efficiency:** Reduced cost to $0.006/order, response latency to 3–5 seconds, and reduced 60% of infrastructure codebase using AWS Bedrock AgentCore.

### Key Takeaways

#### Technical & AI Architecture Mindset

- **Decoupled Architecture:** Separating Ingestion Layer, Tool Layer, and Agent Core helps system scale without rewriting source code.
- **Combining Computer Vision & LLM:** Learned to integrate real-time video processing (YOLO) with Agentic AI for automated alert decisions.
- **Cost Efficiency:** Understood Bedrock token costs, memory services (AgentCore Memory), and serverless to control product budgets.

#### Practical & Hackathon Skills

- **24-Hour Hackathon Journey:** Understood the harsh reality of competitions (time pressure, no sleep, last-minute bug fixes) and overcoming initial knowledge gaps through teamwork.
- **Scope it tiny Principle:** Completing a small, smooth feature is better than pursuing a huge but buggy idea.
- **Pitching & Demo Skills:** Conveying product stories in concise 3-minute presentations targeting customer pain points.

### Applying to Work

- **Apply Agentic AI Model:** Integrate AWS Bedrock AgentCore into internal chatbot projects to increase automated task execution capabilities beyond simple text responses.
- **Optimize Application Architecture:** Use Adapter Patterns when designing multi-channel input systems.
- **Practice Cloud Cost Control:** Calculate cost estimates before deploying AWS services to production.
- **Prepare for Next Hackathons:** Prepare starter templates, assign clear team roles, and focus on building complete MVPs.

### Event Experience

The Agentic AI Build Week review and summary session provided highly practical insights and strong inspiration on bringing AI from concept to actual product. Highlights:

#### Inspiration from Battle Stories
- Impressed by the 24-hour non-stop fighting spirit of the teams. Stories of overcoming "no AI background" hurdles and achieving awards inspired me greatly.

#### Practical Product Impact
- Projects like Plan V (SA work automation) and KFC Bot Agent ($0.006/order auto-ordering) demonstrated enormous economic potential when solving real enterprise problems.

#### Key Lessons Learned
- "Showing up is already half the battle" - Courage to start and eager learning are more vital than waiting for perfection.
- The greatest value from events is not just awards, but accumulated practical skills and wonderful teammates found along the way.

#### Event Photos

![Event 3 Photo 1](/images/event3.1.jpg)

![Event 3 Photo 2](/images/event3.2.jpg)


> Overall, the event expanded my perspective on building AI Native applications, mastering Serverless/AI services on AWS, and fueled my passion for technology.
