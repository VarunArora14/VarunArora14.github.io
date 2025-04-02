---
title: "Projects" # in any language you want
layout: "single" # is necessary
summary: "Explore my personal projects on GitHub that I have built while learning new things"
showtoc: true
showReadingTime: false
---

<!-- reference - https://github.com/k10sj02/thedataplaybook/tree/main/content -->

The following are projects that I have worked on as part of my learning -

## Scalable URL Shortener

**Description:** Built and deployed url shortener application built with FastAPI along with MongoDB and Redis on Kubernetes to implement the classic system design problem.

Medium post on building the application with notes for deployment and configuration setup for local kubernetes, redis etc -

- **Part 1** - https://medium.com/@varunarora1408/url-shortener-system-design-implementation-part-1-afd97618d917
- **Part 2** - https://medium.com/@varunarora1408/url-shortener-system-design-implementation-part-2-0bd9b7a27742

**Technology/Tools:** FastAPI, Redis, MongoDB, Kubernetes, Docker, Github Actions

**Skills:** Kubernetes, API building, CI/CD, Unit Testing, Caching, Networking

{{< github-repo user="VarunArora14" repo="url-shortener-system-design" >}}

---

## Personal-Blog-Chatbot

**Description:** Built a chatbot using advanced RAG techniques that answers question based on my blog website ([here](https://varunarora14.github.io/)) along with source blog posts from where the answer came from. The chatbot is deployed on streamlit here - https://personal-blog-chatbot.streamlit.app/

Medium post on building chatbot from scratch with code - https://medium.com/@varunarora1408/building-rag-chatbot-on-my-personal-blog-ff89ac772c69

**Technology/Tools:** Python, Langchain, Google Gemini 1.5-flash, RAG

**Skills:** Data cleaning and pre-processing, Advanced RAG workflow setup, API building, CI/CD

{{< github-repo user="VarunArora14" repo="Personal-Blog-Chatbot" >}}

---

## Kubernetes Documentation Bot

**Description:** Built a documentation bot on my kubernetes notes stored in markdown format allowing me to interact with my personal notes to retrieve information without finding through them. Implemented HyDe and Query Decomposition as advanced RAG techniques and submitted it for **Google Gemini API Developer competition 2024** ([Submission Link here](https://ai.google.dev/competition/projects/varun-arora)).

**Technology/Tools:** Python, Langchain, Google Gemini 1.5-flash, RAG

**Skills:** Markdown file processing and tested different splitters and chunk sizes for optimal retrieval, Built standalone APIs for each technique, making them ready to deploy as microservices, Built frontend via streamlit and dockerized them.

{{< github-repo user="VarunArora14" repo="Kubernetes-Documentation-Bot" >}}

---

## GenAI-Cookbook

**Description:** Building a one stop repo where I implement various RAG techniques and Agents with their workflow. Multiple useful code snippets related to building GenAI applications will be part of it.

**Technology/Tools:** Python, Langchain, RAG, Agents, Hugging Face

**Skills:** Data cleaning and pre-processing, Advanced RAG workflow setup, Configurig vector databases, Advanced code snippets

{{< github-repo user="VarunArora14" repo="GenAI-Cookbook" >}}

---

## vdocs

**Tech/Tools:** A full stack collaborative document creating and management application where users sign in through google and then see the documents asssociated to them which they can edit and work on together in real time.

**Technology/Tools:** NodeJS, Flutter, Socket.io, Gcloud, MongoDB

**Skills:** Implemented socket connection using NodeJS, GCloud authentication and persistence, Flutter for UI, State management using riverpod package

{{< github-repo user="VarunArora14" repo="vdocs" >}}
