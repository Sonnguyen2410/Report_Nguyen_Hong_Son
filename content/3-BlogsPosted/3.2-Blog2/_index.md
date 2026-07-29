---
title: "Blog 2"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---
# Why Our Team Didn't Use Amazon Bedrock Knowledge Bases Despite Building a RAG Chatbot

Hello everyone,

While working on a document Q&A chatbot project (RAG), our team used Amazon Bedrock to call Claude to answer questions based on documents uploaded by users. When researching how to build RAG on AWS, I saw that almost every documentation mentioned Knowledge Bases for Amazon Bedrock.

This is understandable. Knowledge Bases practically does the entire RAG pipeline for you:
* reading documents
* chunking
* creating embeddings
* storing vectors
* retrieval

It sounds very appealing. Initially, I also thought: "So why do we need to build it ourselves?" But after reading the AWS Blog and comparing it with our team's architecture, we decided not to use Knowledge Bases and instead build our own pipeline with FAISS.

## What does Knowledge Bases do?

AWS describes Knowledge Bases as a Fully Managed RAG service.

You just need to specify where the documents are stored (e.g., Amazon S3), and Bedrock will automatically:
* read documents
* chunk content
* create embeddings
* store in a vector database
* retrieve context
* send context to the Foundation Model

In other words, many steps that developers usually have to write themselves are automated by AWS.

## Initially, I intended to use it right away

When I first read the documentation, I thought this was almost a perfect choice.

* No need for FAISS.
* No need to write an ingest pipeline.
* No need to manage a vector database.

Everything is available.

## But when looking back at the project...

This is what made our team change our decision. Our chatbot doesn't just upload PDFs. Users also upload:
* scanned PDFs
* DOCX
* documents with tables
* documents with images

Some files need to be OCR'd first, some files need to be processed separately, some files need to be chunked by heading, and some files need to be chunked by section. If we used Knowledge Bases, the ingest part would be managed by AWS. Meanwhile, our team wanted to control the entire pipeline.

## This is why our team chose FAISS

FAISS forces our team to do more work. But in return, our team can proactively:
* self-OCR with Textract when needed
* decide on the chunking method
* add metadata per user
* handle multi-tenant
* update vectors when users delete documents

These are all things our team needs in the project.

## What I learned

After reading the AWS Blog, I realized something quite interesting. Knowledge Bases is not a "better version" of FAISS. It's just a different way to build RAG.

If you want to deploy quickly, Knowledge Bases is almost an ideal choice. But if you want to control the entire ingest and retrieval pipeline, building it yourself still has many advantages. In my opinion, there is no absolutely correct choice. The important thing is which architecture is suitable for the project's requirements.

## Conclusion

Initially, I thought: AWS already has Knowledge Bases, so we probably don't need to build RAG ourselves anymore. After looking into it more carefully, I realized that Knowledge Bases solves the problem of quick deployment very well. Meanwhile, projects that need deep customization in document processing, chunking, metadata, or retrieval still have reasons to build a separate pipeline. For our team's chatbot, FAISS isn't because it's "better" than Knowledge Bases, but simply because it's more suitable for how the system is designed.

---

🔗 **REFERENCE BLOG LINK**
AWS News Blog – Knowledge Bases now delivers fully managed RAG experience in Amazon Bedrock:
[https://aws.amazon.com/blogs/aws/knowledge-bases-now-delivers-fully-managed-rag-experience-in-amazon-bedrock/](https://aws.amazon.com/blogs/aws/knowledge-bases-now-delivers-fully-managed-rag-experience-in-amazon-bedrock/)

🔗 **Original Facebook Post:** [AWS Study Group Post](https://www.facebook.com/groups/awsstudygroupfcj/posts/2226903721407921?notif_id=1785325801641354&notif_t=tagged_with_story&ref=notif)