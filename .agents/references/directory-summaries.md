# Directory Summaries

The subdirectories in this directory contain GitHub repository clones from several organizations. Each direct subdirectory resembles either a GitHub organization or a GitHub user.

## Personal Accounts

- **`dhiller/`** — The primary developer's own GitHub account. Contains personal projects (dotfiles, presentations, demos), forks of upstream repos (kubevirt, ginkgo, project-infra), and original tools such as `prow-mcp-server`, `check-owners`, `inactive-org-members`, `meta-article-search-cli`, and various experiment/test repos. This is the most active directory.
- **`skyconqueror/`** — A second personal GitHub account. Contains `plsync` (playlist sync), `event-qr-code-sender`, a GitHub Pages site, and a Docker images repo.

## KubeVirt / OpenShift Virtualization Ecosystem

- **`kubevirt.io/`** — The upstream KubeVirt organization. Contains the core `kubevirt` repo alongside related projects: `containerized-data-importer` (CDI), `common-instancetypes`, `common-templates`, `hostpath-provisioner`, `ssp-operator`, `hyperconverged-cluster-operator`, `project-infra` (CI infra), `ci-health`, `qe-tools`, and more. Primary area of contribution.
- **`kubevirt-ui/`** — KubeVirt UI organization. Contains `kubevirt-plugin`, the OpenShift console plugin for managing virtual machines.
- **`openshift-cnv/`** — OpenShift Virtualization (CNV) CI. Contains `cnv-ci`, the CI pipeline for the OpenShift Virtualization product.
- **`lyarwood/`** — Fork/clone of `healthcheck` from a KubeVirt contributor.

## Kubernetes / OpenShift Infrastructure

- **`kubernetes/`** — Core Kubernetes organization repos: `kubernetes`, `client-go`, `ingress-nginx`, `kube-state-metrics`, `community`, `org`, `repo-infra`, `test-infra`.
- **`kubernetes-sigs/`** — Kubernetes SIGs repos: `cluster-api-provider-kubevirt` (CAPK), `prow` (CI system).
- **`openshift/`** — OpenShift tooling: `ci-tools`, `ci-search`, `oc` (CLI), `release` (release tooling and configuration).
- **`cncf/`** — CNCF organization. Contains `devstats` (contributor statistics dashboards).
- **`kserve/`** — KServe organization. Contains `kserve`, a Kubernetes-native model serving framework.

## Storage / Monitoring

- **`containers/`** — OCI/container tooling. Contains `skopeo` (inspect, copy, and sign container images).
- **`rook/`** — Rook storage orchestrator for Kubernetes. Contains the `rook` repo.
- **`prometheus-operator/`** — Prometheus Operator organization. Contains `kube-prometheus` (end-to-end Prometheus stack for Kubernetes).

## MCP Servers

- **`quay/`** — Quay.io container registry organization. Contains `quay-mcp-server` (MCP server for Quay).
- **`redhat-community-ai-tools/`** — Red Hat community AI tooling. Contains `prow-mcp-server` (MCP server for interacting with Prow CI).
- **`redhat-data-and-ai/`** — Red Hat data & AI tooling. Contains `template-mcp-server` (a template/scaffolding for new MCP servers).

## Go Libraries and CLI Tools

- **`onsi/`** — Onsi Fakhouri's repos. Contains `ginkgo`, the BDD testing framework for Go.
- **`invopop/`** — Invopop organization. Contains `jsonschema`, a Go library for generating JSON Schema from structs.
- **`johnkerl/`** — John Kerl's repos. Contains `miller` (mlr), a data-processing tool (like awk/sed for CSV/JSON/TSV).
- **`akalenyu/`** — Contains `junit-differ`, a tool to diff JUnit XML test result files.
- **`mikah13/`** — Contains `ggs` (Go Git Stats), a CLI for Git repository statistics.

## Shell / Desktop Tooling

- **`0xacx/`** — Contains `chatGPT-shell-cli`, a shell client for the ChatGPT/OpenAI API.
- **`zsh-users/`** — Zsh community. Contains `zsh-syntax-highlighting`.
- **`paperwm/`** — Contains `PaperWM`, a scrollable tiling window manager extension for GNOME Shell.
- **`bsneed/`** — Contains `claude-desktop-fedora`, packaging of Claude Desktop for Fedora Linux.
- **`OddCN/`** — Contains `go-builder-generator-idea-plugin`, an IntelliJ IDEA plugin for generating Go builder patterns.
- **`swagger2markup/`** — Contains `swagger2markup`, a library/CLI to convert Swagger/OpenAPI specs to AsciiDoc or Markdown.

## Demo / Workshop Projects

- **`whatstheweatherlike/`** — A demo project with two repos: `weather-service` and `services` (a multi-service architecture demo).
- **`pllst.xyz/`** — Personal website/blog source (`site` repo).
