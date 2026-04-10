---
title: Supported Models & Vendors
description: Which AI models and providers Factory supports, including BYOK and local inference.
---

Factory's model architecture is designed around vendor agnosticism and enterprise control. Rather than betting on a single provider, the platform supports a broad matrix of models, providers, and deployment patterns — with hierarchical governance to ensure organizational compliance.

## Built-In Models

Factory provides first-party access to models from the major providers:

| Provider | Model Families |
|----------|---------------|
| **Anthropic** | Claude Opus, Sonnet, Haiku (3.x and 4.x families) |
| **OpenAI** | GPT-5, Codex, and related models |
| **Google** | Gemini family |

These are available out of the box with Factory-managed API access — no user configuration required.

## BYOK (Bring Your Own Key)

For teams that need to use their own API keys — whether for cost tracking, compliance, or access to specific model versions — Factory supports BYOK across a wide range of providers:

- **OpenAI**
- **Anthropic**
- **Google Gemini**
- **Groq**
- **DeepInfra**
- **Fireworks AI**
- **OpenRouter**
- **Hugging Face**
- **Baseten**

BYOK keys are configured per-user or per-organization and can be scoped to specific projects or use cases.

## Local Inference

For air-gapped environments, latency-sensitive workloads, or cost optimization:

- **Ollama** — local model serving with a simple interface
- **vLLM** — high-performance local inference engine
- **Any OpenAI-compatible endpoint** — custom or self-hosted inference servers

Local models are configured as custom endpoints and participate in the same model selection and governance framework as cloud models.

## Cloud AI Platforms

Enterprise cloud deployments with IAM-based authentication:

| Platform | Auth Pattern |
|----------|-------------|
| **AWS Bedrock** | IAM roles, cross-account access |
| **GCP Vertex AI** | Service account authentication |
| **Azure OpenAI** | Azure AD / managed identity |

These integrations allow organizations to route all LLM traffic through their existing cloud infrastructure, maintaining audit trails and cost attribution.

## Hierarchical Model Governance

Factory enforces a three-tier model governance hierarchy:

### Organization Level
Admins define the **allowed model set** for the entire organization. Models can be explicitly banned — once banned at the org level, they cannot be re-enabled at any lower level. This is the compliance boundary.

### Project Level
Project owners can add **additional models** beyond the org-level set for specific repositories or workspaces. However, they cannot re-enable models that the org has banned. This allows teams to experiment with new models while respecting organizational constraints.

### User Level
Individual users choose from the models allowed by both org and project policies. Personal BYOK keys further expand options within the allowed set.

## LLM Gateways

For organizations that require all LLM traffic to pass through a centralized gateway (for logging, rate limiting, cost control, or compliance):

- **Gateway base URLs** are configured in `settings.json` or via environment variables (`ANTHROPIC_BASE_URL`, `OPENAI_BASE_URL`)
- **Org-level gateway policy** can require all traffic to route through specific gateway endpoints
- Compatible with common gateway solutions (LiteLLM, Portkey, custom proxies)

This ensures that even BYOK and local inference traffic can be monitored and governed centrally.

## Founder Takeaway

Factory's model architecture reflects a critical insight for vertical AI builders: model lock-in is a business risk, not a technical convenience. By abstracting the model layer behind a governance-aware routing system, Factory can swap providers as the market shifts without disrupting customers. Build your vertical AI system the same way — treat models as interchangeable compute, invest in the orchestration and context layers that create durable differentiation.

## Sources
- [Factory Docs — Models, LLM Gateways, and Integrations](https://docs.factory.ai/enterprise/models-llm-gateways-and-integrations)
- [Factory Docs — BYOK Overview](https://docs.factory.ai/cli/byok/overview)
