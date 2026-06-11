# SOURCES.md - Research Source of Truth

Single source of truth for all research sources used across SDLC skills.

Last updated: 2026-06-11

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

---

## Books & Publications

| Book | Author(s) | Year | Used In |
|------|-----------|------|---------|
| Accelerate | Forsgren, Humble, Kim | 2018 | retrospective, deployment, testing-qa |
| Team Topologies | Skelton & Pais | 2019 | retrospective, prd-to-production |
| The Manager's Path | Camille Fournier | 2017 | prd-to-production |
| An Elegant Puzzle | Will Larson | 2019 | prd-to-production, retrospective |
| Staff Engineer | Will Larson | 2019 | retrospective |
| Observability Engineering | Charity Majors, Liz Fong-Jones, George Miranda | 2022 | observability |
| Chaos Engineering | Casey Rosenthal, Nora Jones | 2020 | testing-qa, observability |
| Impact Mapping | Gojko Adzic | 2012 | requirements-engineering |
| Specification by Example | Gojko Adzic | 2011 | requirements-engineering |
| Continuous Discovery Habits | Teresa Torres | 2021 | prd-to-production |
| Inspired | Marty Cagan | 2017 | prd-to-production |
| Lean UX | Jeff Gothelf, Josh Seiden | 2013 | requirements-engineering |
| Sprint | Jake Knapp | 2016 | requirements-engineering |
| The Phoenix Project | Kim, Behr, Spafford | 2013 | retrospective |
| Playing to Win | A.G. Lafley & Roger Martin | 2013 | prd-to-production |
| Competing Against Luck | Clayton Christensen | 2016 | requirements-engineering |
| Clean Architecture | Robert C. Martin | 2017 | architecture-design |
| Designing Data-Intensive Applications | Martin Kleppmann | 2017 | architecture-design |
| High Growth Handbook | Elad Gil | 2018 | prd-to-production |
| Toyota Kata | Mike Rother | 2009 | retrospective |
| The Lean Startup | Eric Ries | 2011 | prd-to-production |

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
| The SPACE of Developer Productivity | Forsgren, Storey, Maddila, et al. | 2021 | retrospective |
| DevEx: What Actually Drives Productivity | Noda, Storey, Forsgren | 2023 | retrospective |
| Cognitive Load in Software Engineering | Bett, Graziotin, et al. | 2023 | retrospective |
| Hexagonal Architecture | Alistair Cockburn | 2005 | architecture-design |
| Onion Architecture | Jeffrey Palermo | 2008 | architecture-design |
| Conway's Law | Melvin Conway | 1967 | retrospective |

---

## Company Engineering Blogs

| Company | URL | Topics |
|---------|-----|--------|
| Netflix Tech Blog | https://netflixtechblog.com/ | Chaos engineering, testing culture |
| Google SRE | https://sre.google/ | SRE practices, error budgets |
| Stripe Engineering | https://stripe.com/blog/engineering | API design, developer experience |
| Shopify Engineering | https://shopify.engineering/ | Modular monolith, Packwerk |
| Spotify Engineering | https://engineering.atspotify.com/ | Backstage, testing culture |
| GitHub Engineering | https://github.blog/engineering/ | CI/CD, trunk-based dev |
| Uber Engineering | https://www.uber.com/blog/engineering/ | Microservices, observability |
| Airbnb Engineering | https://medium.com/airbnb-engineering | API design, microservices |

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

## How to Use This File

1. **Verifying claims**: When a skill states a fact, check the source here
2. **Deep diving**: Use URLs to read original documentation
3. **Updating skills**: When sources release new versions, update skills accordingly
4. **Adding new sources**: Add to appropriate section when researching new topics

---

## Maintenance Schedule

- **Quarterly**: Check for major version updates to key sources
- **On release**: Update when DORA, OWASP, or CNCF publish new reports
- **On demand**: When user requests deep research on specific topic
