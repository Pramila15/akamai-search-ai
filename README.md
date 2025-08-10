# Akamai-search-ai
Azure AI Search is a scalable search infrastructure that indexes heterogeneous content and enables retrieval through APIs, applications, and AI agents. The platform provides native integrations with Azure's AI stack (OpenAI, AI Foundry, Machine Learning) and supports extensible architectures for third-party and open-source model integration.
The service handles both traditional search workloads and modern RAG (retrieval-augmented generation) patterns for conversational AI applications. This makes it suitable for enterprise search scenarios as well as AI-powered customer experiences that require dynamic content generation through chat completion models.

Sources (read-only): SharePoint/OneDrive/Teams files, GitHub/GitLab/Bitbucket repos, Confluence/Jira, internal wikis, S3/GCS buckets, Postgres/Elastic indices, etc.

Ingestion pipeline: Azure Functions / Data Factory pull changes via REST or webhooks → normalize to a common schema → drop raw + extracted text into Azure Blob Storage/Data Lake → optional OCR/classification with Azure Document Intelligence → chunk & embed → push to Azure AI Search.

Indexing model (per chunk):
id, doc_id, source, path/url, title, body, vector, last_modified, owners[], allow_groups[], tags[], language, repo/branch
Keep owners[]/allow_groups[] for security trimming.

Identity & security: Enforce Entra ID (Azure AD) sign-in in the app. On every query, attach the caller’s user ID + group IDs and filter on allow_groups[]/owners[]. (This is what prevents “pull any file” from becoming “see restricted stuff.”)

Retrieval quality: Use hybrid (BM25 + vector) + semantic ranking. Add synonyms, repo/filetype/source facets, and date or repo boosts.

RAG: Pipe top results into Azure OpenAI for summarization/QA with strict citations and content filters.

Ops: Private endpoints for Search/Storage, Key Vault for secrets, logging in App Insights, and a dead-letter queue for bad docs.
