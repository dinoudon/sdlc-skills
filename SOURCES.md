# SOURCES.md - Research Source of Truth

Single source of truth for all research sources used across SDLC skills.

Last updated: 2026-06-16 (v6.2.0)

---

## Industry Frameworks & Standards

| Source | URL | Used In |
|--------|-----|---------|
| DORA / State of DevOps | https://dora.dev | retrospective, deployment, cicd-pipeline |
| Google SRE Book | https://sre.google/sre-book/table-of-contents/ | observability, deployment, prd-to-production |
| OWASP Top 10 | https://owasp.org/Top10/ | adversarial-review |
| OWASP API Security Top 10 | https://owasp.org/API-Security/ | adversarial-review, api-documentation |
| OWASP SAMM | https://owaspsamm.org/model/ | adversarial-review |
| CNCF Landscape | https://landscape.cncf.io/ | architecture-design, deployment, observability |
| FinOps Foundation | https://www.finops.org/framework/ | deployment, retrospective |
| Green Software Foundation | https://greensoftware.foundation/ | cicd-pipeline, retrospective |
| SLSA Framework | https://slsa.dev/spec/v1.0/about | adversarial-review, cicd-pipeline |
| Sigstore | https://sigstore.dev/ | adversarial-review, cicd-pipeline |
| OpenAPI Specification | https://spec.openapis.org/oas/v3.1.1.html | api-documentation, architecture-design |
| AsyncAPI Specification | https://www.asyncapi.com/ | api-documentation |
| SPACE Framework | https://queue.acm.org/detail.cfm?id=3454121 | platform-engineering, retrospective |
| Team Topologies | https://teamtopologies.com/ | platform-engineering, retrospective |
| CNCF Platform White Paper | https://tag-app-delivery.cncf.io/ | platform-engineering |
| Keep a Changelog | https://keepachangelog.com/ | technical-writing |
| ADR GitHub | https://adr.github.io/ | technical-writing |

---

## Books & Publications

| Book | Author(s) | Year | Used In |
|------|-----------|------|---------|
| Accelerate | Forsgren, Humble, Kim | 2018 | retrospective, deployment, testing-qa |
| Team Topologies | Skelton & Pais | 2019 | retrospective, prd-to-production, platform-engineering |
| The Manager's Path | Camille Fournier | 2017 | prd-to-production, hiring-talent |
| An Elegant Puzzle | Will Larson | 2019 | prd-to-production, retrospective, hiring-talent |
| Staff Engineer | Will Larson | 2019 | retrospective, hiring-talent |
| Observability Engineering | Charity Majors, Liz Fong-Jones, George Miranda | 2022 | observability |
| Chaos Engineering | Casey Rosenthal, Nora Jones | 2020 | testing-qa, observability |
| Impact Mapping | Gojko Adzic | 2012 | requirements-engineering |
| Specification by Example | Gojko Adzic | 2011 | requirements-engineering |
| Continuous Discovery Habits | Teresa Torres | 2021 | prd-to-production, product-growth |
| Inspired | Marty Cagan | 2017 | prd-to-production, product-growth |
| Lean UX | Jeff Gothelf, Josh Seiden | 2013 | requirements-engineering |
| Sprint | Jake Knapp | 2016 | requirements-engineering |
| The Phoenix Project | Kim, Behr, Spafford | 2013 | retrospective |
| Playing to Win | A.G. Lafley & Roger Martin | 2013 | prd-to-production |
| Competing Against Luck | Clayton Christensen | 2016 | requirements-engineering |
| Clean Architecture | Robert C. Martin | 2017 | architecture-design |
| Designing Data-Intensive Applications | Martin Kleppmann | 2017 | architecture-design |
| High Growth Handbook | Elad Gil | 2018 | prd-to-production, finance-ops |
| Toyota Kata | Mike Rother | 2009 | retrospective |
| The Lean Startup | Eric Ries | 2011 | prd-to-production, throwaway-projects |
| Obviously Awesome | April Dunford | 2019 | gtm-strategy |
| Venture Deals | Brad Feld, Jason Mendelson | 2019 | finance-ops |
| Zero to One | Peter Thiel | 2014 | product-growth, gtm-strategy |
| Crossing the Chasm | Geoffrey Moore | 2014 | gtm-strategy |

---

## Official Documentation & Guides

### API Design & Documentation
| Source | URL |
|--------|-----|
| Google Cloud API Design Guide | https://docs.cloud.google.com/apis/design |
| Microsoft REST API Guidelines | https://github.com/microsoft/api-guidelines |
| Stripe API Design | https://docs.stripe.com/api |
| Stripe API Blog | https://stripe.dev/blog/payment-api-design |
| Redocly | https://redocly.com/ |
| Spectral | https://github.com/stoplightio/spectral |
| openapi-generator | https://openapi-generator.tech/ |
| Kiota (Microsoft) | https://github.com/microsoft/kiota |
| Fern | https://buildwithfern.com/ |
| Bump.sh | https://bump.sh/ |
| Mintlify | https://www.mintlify.com/ |
| Scalar | https://scalar.com/ |
| ReadMe | https://readme.com/ |
| Stoplight Elements | https://stoplight.io/open-source/elements |

### Cloud-Native & Infrastructure
| Source | URL |
|--------|-----|
| Kubernetes Docs | https://kubernetes.io/docs/ |
| Kubebuilder | https://book.kubebuilder.io/ |
| Operator SDK | https://sdk.operatorframework.io/ |
| Argo CD | https://argo-cd.readthedocs.io/ |
| Argo Rollouts | https://argoproj.github.io/argo-rollouts/ |
| Argo Workflows | https://argoproj.github.io/workflows/ |
| Tekton | https://tekton.dev/docs/ |
| Flux | https://fluxcd.io/docs/ |
| Istio | https://istio.io/latest/docs/ |
| Cilium | https://docs.cilium.io/ |
| Backstage | https://backstage.io/docs/ |
| Dagger | https://docs.dagger.io/ |
| Bazel | https://bazel.build/docs/ |
| Buck2 | https://buck2.build/docs/ |
| Pants | https://www.pantsbuild.org/docs/ |
| Karpenter | https://karpenter.sh/docs/ |
| vCluster | https://www.vcluster.com/docs/ |

### Observability & Monitoring
| Source | URL |
|--------|-----|
| OpenTelemetry Docs | https://opentelemetry.io/docs/ |
| OTel GenAI Semantic Conventions | https://opentelemetry.io/docs/specs/semconv/gen-ai/ |
| Prometheus Docs | https://prometheus.io/docs/ |
| Grafana Docs | https://grafana.com/docs/ |
| Grafana LGTM Stack | https://grafana.com/blog/2023/02/01/the-grafana-lgtm-stack-for-open-source-observability/ |
| Honeycomb | https://www.honeycomb.io/what-is-observability |
| Jaeger | https://www.jaegertracing.io/docs/ |
| Falco | https://falco.org/docs/ |
| Pixie | https://docs.px.dev/ |
| Cilium Hubble | https://docs.cilium.io/en/stable/observability/ |

### Testing & Quality
| Source | URL |
|--------|-----|
| Pact | https://docs.pact.io/ |
| Dredd | https://dredd.org/ |
| Schemathesis | https://schemathesis.readthedocs.io/ |
| k6 | https://k6.io/docs/ |
| Playwright | https://playwright.dev/docs/ |
| Cypress | https://docs.cypress.io/ |
| Testcontainers | https://testcontainers.com/ |
| Hypothesis | https://hypothesis.readthedocs.io/ |
| Mutation Testing (Stryker) | https://stryker-mutator.io/ |
| LitmusChaos | https://docs.litmuschaos.io/ |
| Testkube | https://docs.testkube.io/ |
| Percy | https://percy.io/ |
| Chromatic | https://www.chromatic.com/ |

### Security
| Source | URL |
|--------|-----|
| OWASP | https://owasp.org/ |
| NIST SP 800-207 (Zero Trust) | https://csrc.nist.gov/publications/detail/sp/800-207/final |
| NIST SP 800-218 (SSDF) | https://csrc.nist.gov/publications/detail/sp/800-218/final |
| CIS Benchmarks | https://www.cisecurity.org/benchmark |
| OPA / Rego | https://www.openpolicyagent.org/docs/ |
| Kyverno | https://kyverno.io/docs/ |
| Sentinel (HashiCorp) | https://developer.hashicorp.com/sentinel/docs |
| Falco | https://falco.org/docs/ |
| Tetragon | https://cilium.io/enterprise/ |
| Trivy | https://aquasecurity.github.io/trivy/ |
| Syft | https://github.com/anchore/syft |
| Grype | https://github.com/anchore/grype |

### Developer Experience
| Source | URL |
|--------|-----|
| SPACE Framework | https://queue.acm.org/detail.cfm?id=3454121 |
| DevEx Paper | https://queue.acm.org/detail.cfm?id=3595878 |
| DX Platform | https://getdx.com/ |
| GitHub Actions | https://docs.github.com/en/actions |
| GitLab CI | https://docs.gitlab.com/ee/ci/ |
| Turborepo | https://turbo.build/repo/docs |
| Nx | https://nx.dev/docs |
| Bazel | https://bazel.build/docs |

### Product & Requirements
| Source | URL |
|--------|-----|
| Teresa Torres / Product Talk | https://www.producttalk.org/ |
| Reforge | https://www.reforge.com/blog |
| Roman Pichler (Product Vision Board) | https://www.romanpichler.com/tools/vision-board/ |
| Strategyn (JTBD) | https://strategyn.com/jobs-to-be-done/ |
| ADR GitHub | https://adr.github.io/ |
| AsyncAPI | https://www.asyncapi.com/ |

---

## Research Papers & Academic Sources

| Paper/Article | Author(s) | Year | Used In |
|---------------|-----------|------|---------|
| The SPACE of Developer Productivity | Forsgren, Storey, Maddila, et al. | 2021 | retrospective, platform-engineering |
| DevEx: What Actually Drives Productivity | Noda, Storey, Forsgren | 2023 | retrospective, platform-engineering |
| Cognitive Load in Software Engineering | Bett, Graziotin, et al. | 2023 | retrospective |
| Hexagonal Architecture | Alistair Cockburn | 2005 | architecture-design |
| Onion Architecture | Jeffrey Palermo | 2008 | architecture-design |
| Conway's Law | Melvin Conway | 1967 | retrospective |

---

## Company Engineering Blogs

| Company | URL | Topics |
|---------|-----|--------|
| Netflix Tech Blog | https://netflixtechblog.com/ | Chaos engineering, testing culture, microservices |
| Google SRE | https://sre.google/ | SRE practices, error budgets |
| Stripe Engineering | https://stripe.com/blog/engineering | API design, developer experience, DLG |
| Shopify Engineering | https://shopify.engineering/ | Modular monolith, Packwerk |
| Spotify Engineering | https://engineering.atspotify.com/ | Backstage, squad model, testing culture |
| GitHub Engineering | https://github.blog/engineering/ | CI/CD, trunk-based dev |
| Uber Engineering | https://www.uber.com/blog/engineering/ | Microservices, observability |
| Airbnb Engineering | https://medium.com/airbnb-engineering | API design, microservices, design systems |
| Figma Engineering | https://www.figma.com/blog/category/engineering/ | Real-time collab, WebAssembly |
| Notion Engineering | https://www.notion.so/blog | Performance, offline-first |
| Datadog Engineering | https://www.datadoghq.com/blog/engineering/ | Observability, distributed systems |
| Vercel Engineering | https://vercel.com/blog | Next.js, edge computing |
| MongoDB Engineering | https://www.mongodb.com/blog | Database, developer advocacy |

---

## Growth & Product-Led Growth

| Source | URL | Used In |
|--------|-----|---------|
| Reforge | https://www.reforge.com/blog | product-growth |
| Lenny Rachitsky Newsletter | https://www.lennysnewsletter.com/ | product-growth |
| OpenView Partners PLG | https://openviewpartners.com/blog/ | product-growth |
| a16z Growth | https://a16z.com/growth/ | product-growth |
| Bessemer Cloud Atlas | https://www.bvp.com/atlas | product-growth, finance-ops |
| Kyle Poyar, OpenView | https://openviewpartners.com/blog/ | product-growth |
| Dave McClure Pirate Metrics | https://500.co/ | product-growth |
| SaaStr | https://www.saastr.com/ | product-growth, finance-ops |
| Tomasz Tunguz | https://tomtunguz.com/ | product-growth, finance-ops |
| Christoph Janz SaaS Metrics | https://christophjanz.blogspot.com/ | product-growth, finance-ops |

---

## Developer Relations

| Source | URL | Used In |
|--------|-----|---------|
| Developer Relations | https://developerrelations.com/ | developer-relations |
| DevRel Collective | https://devrelcollective.fun/ | developer-relations |
| DevRelCon | https://devrelcon.dev/ | developer-relations |
| Stripe Developer Experience | https://stripe.com/blog/engineering | developer-relations |
| Twilio Developer Marketing | https://www.twilio.com/en-us/blog | developer-relations |
| MongoDB Community | https://www.mongodb.com/community | developer-relations |

---

## Platform Engineering

| Source | URL | Used In |
|--------|-----|---------|
| Backstage (Spotify) | https://backstage.io/docs/ | platform-engineering |
| Port.io State of IDP | https://www.port.io/state-of-internal-developer-portals | platform-engineering |
| Humanitec Platform Orchestrator | https://humanitec.com/ | platform-engineering |
| Gartner Platform Engineering | https://www.gartner.com/en/articles/what-is-platform-engineering | platform-engineering |
| Team Topologies | https://teamtopologies.com/ | platform-engineering |
| Sleuth (DORA metrics) | https://www.sleuth.io/ | platform-engineering |
| LinearB (engineering metrics) | https://linearb.io/ | platform-engineering |
| Pluralsight Flow | https://www.pluralsight.com/product/flow | platform-engineering |
| Jellyfish (eng management) | https://www.getjellyfish.com/ | platform-engineering |

---

## Prototyping & Validation Tools

| Source | URL | Used In |
|--------|-----|---------|
| Carrd (landing pages) | https://carrd.co/ | throwaway-projects |
| Webflow | https://webflow.com/ | throwaway-projects |
| Framer | https://www.framer.com/ | throwaway-projects |
| Supabase | https://supabase.com/ | throwaway-projects |
| PlanetScale | https://planetscale.com/ | throwaway-projects |
| Neon (serverless Postgres) | https://neon.tech/ | throwaway-projects |
| Railway | https://railway.app/ | throwaway-projects |
| Render | https://render.com/ | throwaway-projects |
| Plausible Analytics | https://plausible.io/ | throwaway-projects |
| PostHog | https://posthog.com/ | throwaway-projects |

---

## Technical Writing

| Source | URL | Used In |
|--------|-----|---------|
| Divio Documentation System | https://documentation.divio.com/ | technical-writing |
| Google Developer Style Guide | https://developers.google.com/style | technical-writing |
| Write the Docs | https://www.writethedocs.org/ | technical-writing |
| MkDocs Material | https://squidfunk.github.io/mkdocs-material/ | technical-writing |
| Docusaurus | https://docusaurus.io/ | technical-writing |
| Vale (prose linter) | https://vale.sh/ | technical-writing |
| Mermaid diagrams | https://mermaid.js.org/ | technical-writing |
| Hugo | https://gohugo.io/ | technical-writing |
| VitePress | https://vitepress.dev/ | technical-writing |
| Starlight | https://starlight.astro.build/ | technical-writing |
| GitBook | https://www.gitbook.com/ | technical-writing |
| Mintlify | https://www.mintlify.com/ | technical-writing |

---

## Go-to-Market & Sales

| Source | URL | Used In |
|--------|-----|---------|
| April Dunford, Obviously Awesome | https://www.aprildunford.com/ | gtm-strategy |
| Winning by Design | https://winningbydesign.com/ | gtm-strategy |
| Pavilion (revenue leaders) | https://joinpavilion.com/ | gtm-strategy |
| First Round Review | https://review.firstround.com/ | gtm-strategy, hiring-talent |
| Y Combinator Startup School | https://www.startupschool.org/ | gtm-strategy |
| Product Hunt | https://www.producthunt.com/ | gtm-strategy |
| AWS Marketplace | https://aws.amazon.com/marketplace/ | gtm-strategy |
| GCP Marketplace | https://cloud.google.com/marketplace | gtm-strategy |
| Azure Marketplace | https://azuremarketplace.microsoft.com/ | gtm-strategy |

---

## Hiring & Talent

| Source | URL | Used In |
|--------|-----|---------|
| Will Larson, Staff Engineer | https://staffeng.com/ | hiring-talent |
| Will Larson, Elegant Puzzle | https://lethain.com/elegant-puzzle/ | hiring-talent |
| Camille Fournier, Manager's Path | https://www.themanagerspath.org/ | hiring-talent |
| Levels.fyi | https://www.levels.fyi/ | hiring-talent |
| Key Values | https://www.keyvalues.com/ | hiring-talent |
| Holloway Equity Guide | https://www.holloway.com/g/equity-compensation | hiring-talent, finance-ops |
| Pragmatic Engineer | https://blog.pragmaticengineer.com/ | hiring-talent |
| Hacker News Who's Hiring | https://news.ycombinator.com/ | hiring-talent |

---

## Finance & Operations

| Source | URL | Used In |
|--------|-----|---------|
| Bessemer Cloud Atlas | https://www.bvp.com/atlas | finance-ops |
| OpenView SaaS Benchmarks | https://openviewpartners.com/blog/ | finance-ops |
| a16z Startup School | https://a16z.com/startup-school/ | finance-ops |
| Carta (cap table) | https://carta.com/ | finance-ops |
| Holloway Equity Guide | https://www.holloway.com/g/equity-compensation | finance-ops |
| SaaStr | https://www.saastr.com/ | finance-ops |
| Tomasz Tunguz | https://tomtunguz.com/ | finance-ops |
| YC Fundraising Guide | https://www.ycombinator.com/library | finance-ops |
| Brad Feld, Venture Deals | https://www.feld.com/archives/2019/06/venture-deals-4th-edition.html | finance-ops |
| Crunchbase | https://www.crunchbase.com/ | finance-ops |
| PitchBook | https://pitchbook.com/ | finance-ops |

---

## Legal & Compliance

| Source | URL | Used In |
|--------|-----|---------|
| GDPR Official Text | https://gdpr.eu/ | legal-compliance |
| CCPA Official | https://oag.ca.gov/privacy/ccpa | legal-compliance |
| SOC 2 (AICPA) | https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/trustservicescriteria | legal-compliance |
| ISO 27001 | https://www.iso.org/isoiec-27001-information-security.html | legal-compliance |
| Choose a License | https://choosealicense.com/ | legal-compliance |
| TLDRLegal | https://tldrlegal.com/ | legal-compliance |
| Vanta (SOC 2 automation) | https://www.vanta.com/ | legal-compliance |
| Drata (compliance automation) | https://www.drata.com/ | legal-compliance |
| Secureframe | https://secureframe.com/ | legal-compliance |
| Cooley GO (startup legal) | https://www.cooleygo.com/ | legal-compliance |
| Gunderson Dettmer | https://www.gunder.com/ | legal-compliance |
| Wilson Sonsini | https://www.wsgr.com/ | legal-compliance |

---

## Tool-Specific Documentation

| Tool | URL | Category |
|------|-----|----------|
| Docker | https://docs.docker.com/ | Containers |
| Terraform | https://developer.hashicorp.com/terraform/docs | IaC |
| Pulumi | https://www.pulumi.com/docs/ | IaC |
| Ansible | https://docs.ansible.com/ | Configuration |
| Helm | https://helm.sh/docs/ | K8s packages |
| Kustomize | https://kustomize.io/ | K8s config |
| GitHub CLI (gh) | https://cli.github.com/manual/ | Git workflow |
| AWS CLI | https://docs.aws.amazon.com/cli/ | Cloud |
| gcloud CLI | https://cloud.google.com/sdk/gcloud | Cloud |
| Azure CLI | https://learn.microsoft.com/en-us/cli/azure/ | Cloud |

---

## Database Design

| Source | URL | Used In |
|--------|-----|---------|
| PostgreSQL Docs | https://www.postgresql.org/docs/ | database-design |
| Use The Index Luke | https://use-the-index-luke.com/ | database-design |
| Flyway Docs | https://docs.flyway.io/ | database-design |
| Alembic Docs | https://alembic.sqlalchemy.org/ | database-design |
| Atlas Docs | https://atlasgo.io/ | database-design |
| Prisma Migrate | https://www.prisma.io/docs/orm/prisma-migrate | database-design |
| Vitess Docs | https://vitess.io/docs/ | database-design |
| PgBouncer Config | https://www.pgbouncer.org/config.html | database-design |
| HikariCP | https://github.com/brettwooldridge/HikariCP | database-design |
| Debezium Docs | https://debezium.io/documentation/ | database-design |
| MongoDB Data Modeling | https://www.mongodb.com/docs/manual/core/data-model-design/ | database-design |
| Redis Docs | https://redis.io/docs/ | database-design |
| Neo4j Docs | https://neo4j.com/docs/ | database-design |
| TimescaleDB Docs | https://docs.timescale.com/ | database-design |
| Microservices.io Data Patterns | https://microservices.io/patterns/data/ | database-design |

---

## ML Engineering

| Source | URL | Used In |
|--------|-----|---------|
| Google MLOps Whitepaper | https://cloud.google.com/architecture/mlops-continuous-delivery-and-automation-pipelines-in-machine-learning | ml-engineering |
| Hidden Technical Debt in ML (NeurIPS 2015) | https://papers.nips.cc/paper/2015/hash/86df7dcfd896f0254d54811f70a25e3e-Abstract.html | ml-engineering |
| MLflow Docs | https://mlflow.org/docs/ | ml-engineering |
| Kubeflow Docs | https://www.kubeflow.org/docs/ | ml-engineering |
| Feast Docs | https://docs.feast.dev/ | ml-engineering |
| Tecton Docs | https://www.tecton.ai/docs/ | ml-engineering |
| Hopsworks Docs | https://docs.hopsworks.ai/ | ml-engineering |
| TorchServe | https://pytorch.org/serve/ | ml-engineering |
| TF Serving | https://www.tensorflow.org/tfx/guide/serving | ml-engineering |
| Triton Inference Server | https://docs.nvidia.com/deeplearning/triton-inference-server/ | ml-engineering |
| BentoML Docs | https://docs.bentoml.com/ | ml-engineering |
| Seldon Core | https://docs.seldon.io/projects/seldon-core/ | ml-engineering |
| Evidently AI | https://docs.evidentlyai.com/ | ml-engineering |
| NannyML | https://nannyml.readthedocs.io/ | ml-engineering |
| Alibi Detect | https://docs.seldon.io/projects/alibi-detect/ | ml-engineering |
| whylogs | https://docs.whylabs.ai/ | ml-engineering |
| W&B Docs | https://docs.wandb.ai/ | ml-engineering |
| Neptune Docs | https://docs.neptune.ai/ | ml-engineering |
| DVC Docs | https://dvc.org/doc | ml-engineering |
| HuggingFace Hub | https://huggingface.co/docs/hub/ | ml-engineering |
| LangChain Docs | https://docs.langchain.com/ | ml-engineering |
| LlamaIndex Docs | https://docs.llamaindex.ai/ | ml-engineering |
| Ragas Docs | https://docs.ragas.io/ | ml-engineering |
| HuggingFace TRL | https://huggingface.co/docs/trl/ | ml-engineering |
| Airflow Docs | https://airflow.apache.org/docs/ | ml-engineering |
| Prefect Docs | https://docs.prefect.io/ | ml-engineering |
| Dagster Docs | https://docs.dagster.io/ | ml-engineering |

---

## Incident Management

| Source | URL | Used In |
|--------|-----|---------|
| Google SRE Workbook - Incident Response | https://sre.google/workbook/incident-response/ | incident-management |
| Google SRE Workbook - Postmortem Culture | https://sre.google/workbook/postmortem-culture/ | incident-management |
| PagerDuty Incident Response | https://response.pagerduty.com/ | incident-management |
| PagerDuty Severity Levels | https://response.pagerduty.com/before/severity_levels/ | incident-management |
| Atlassian Incident Management | https://www.atlassian.com/incident-management | incident-management |
| incident.io Blog | https://incident.io/blog | incident-management |
| Rootly | https://rootly.com/ | incident-management |
| Gremlin Chaos Engineering | https://www.gremlin.com/ | incident-management |
| AWS Fault Injection Simulator | https://aws.amazon.com/fis/ | incident-management |
| LitmusChaos | https://litmuschaos.io/ | incident-management |
| Firehydrant | https://firehydrant.com/ | incident-management |
| Opsgenie | https://www.atlassian.com/software/opsgenie | incident-management |
| Squadcast | https://www.squadcast.com/ | incident-management |

---

## Unicorn Company Case Studies

| Company | Key Pattern | URL | Used In |
|---------|------------|-----|---------|
| Stripe | Developer-led growth, API-first, 7 lines of code | https://stripe.com/blog/engineering | product-growth, developer-relations, prd-to-production |
| Slack | Viral team adoption, freemium, integrations | https://slack.com/blog | product-growth, prd-to-production |
| Notion | PLG, community templates, power-user advocacy | https://www.notion.so/blog | product-growth, prd-to-production |
| Figma | Collaboration as growth, browser-based, sharing loops | https://www.figma.com/blog/ | product-growth, prd-to-production |
| Zoom | Frictionless experience, one-click join | https://zoom.us/blog | product-growth |
| Spotify | Squad model, Backstage, engineering culture | https://engineering.atspotify.com/ | platform-engineering, retrospective |
| Netflix | Chaos engineering, microservices, culture deck | https://netflixtechblog.com/ | retrospective, testing-qa |
| Airbnb | SOA migration, design systems | https://medium.com/airbnb-engineering | architecture-design |
| Google | SRE, error budgets, two-pizza teams | https://sre.google/ | observability, retrospective |
| Amazon | Two-pizza teams, leadership principles | https://www.amazon.jobs/ | retrospective |
| Twilio | Developer-first marketing, TwilioQuest | https://www.twilio.com/en-us/blog | developer-relations |
| MongoDB | Community-powered growth, university | https://www.mongodb.com/community | developer-relations |
| Datadog | Hybrid PLG+SLG, multi-product | https://www.datadoghq.com/blog/ | gtm-strategy |

---

## gstack (Gary Tan / YC)

| Source | URL | Used In |
|--------|-----|---------|
| gstack GitHub | https://github.com/garrytan/gstack | prd-to-production, retrospective |
| gstack Hacker News | https://news.ycombinator.com/item?id=47418576 | prd-to-production |
| Y Combinator Library | https://www.ycombinator.com/library | gtm-strategy, finance-ops |
| YC Startup School | https://www.startupschool.org/ | gtm-strategy, throwaway-projects |

---

## Maintenance Schedule

- **Quarterly**: Check for major version updates to key sources
- **On release**: Update when DORA, OWASP, or CNCF publish new reports
- **On demand**: When user requests deep research on specific topic
- **After unicorn case studies**: Update when major tech companies publish engineering blog posts
