# EKS module change-review checklist

Use this checklist before applying a module upgrade or node-group change.
It separates local validation from a cloud-backed Terraform plan.

## Local checks

Read [versions.tf](versions.tf) for this revision's Terraform and provider
requirements. From the module root, with a compatible Terraform installed:

```sh
terraform fmt -check -recursive
terraform init -backend=false
terraform validate
```

Initialization downloads providers and writes local initialization files.
The backend=false option skips backend initialization; validation does not prove
that AWS permissions, quotas or the eventual cluster configuration will work.
Use a disposable checkout if you do not want local lockfile changes.

## Review a plan in the consuming configuration

The module root is not your deployed environment. Run a plan in the reviewed
root configuration that calls this module, using the intended workspace,
backend, AWS account and region. Planning can read remote state and cloud APIs.
Do not run apply merely to validate a change.

| Review area | Questions |
| --- | --- |
| Cluster endpoint | Will operators and CI retain a permitted access path? |
| Access entries and IAM | Are role changes intentional and least-privilege? |
| Node groups | Are replacements expected, and can workloads drain safely? |
| Networking | Are subnet, routing and security-group changes scoped correctly? |
| Add-ons | Are versions compatible with the target Kubernetes version? |
| Recovery | Is state backed up and is the previous configuration available? |

- Investigate every destroy or replacement, especially stateful dependencies.
- Check capacity headroom and disruption budgets before node replacement.
- Review unknown plan values instead of interpreting them as safe defaults.
- Keep state, saved plans, credentials and sensitive variable files out of Git.
- Reverting Git is not automatically a safe rollback of infrastructure or state.

Start with the relevant [example](examples/README.md); examples are not a
production architecture approval.

## Attribution

Upstream code, licenses and
contributor attribution remain unchanged.
